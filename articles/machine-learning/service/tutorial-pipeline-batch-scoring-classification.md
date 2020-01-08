---
title: 教程：用于批量评分的 Azure ML 管道
titleSuffix: Azure Machine Learning
description: 生成用于在图像分类模型中运行批量评分的 ML 管道。 机器学习管道可以优化工作流以提高其速度、可移植性和可重用性，使你能够将工作重心放在专业技术和机器学习，而不是在基础结构和自动化上。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 15a11ba74262ec5a354f0cb3fe22c09167c8d5a6
ms.sourcegitcommit: d15b23e23328ce7502dd3d2846b49fd2d6d8209c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005396"
---
# <a name="use-azure-machine-learning-pipelines-for-batch-scoring"></a>使用 Azure 机器学习管道进行批量评分

在本教程中，你将使用 Azure 机器学习管道运行一个批量评分作业。 本示例使用预先训练的 [Inception-V3](https://arxiv.org/abs/1512.00567) 卷积神经网络 Tensorflow 模型来对不带标签的图像进行分类。 生成并发布管道后，你将配置一个 REST 终结点，以便可以从任何平台上的任何 HTTP 库触发该管道。

机器学习管道可以优化工作流以提高其速度、可移植性和可重用性，使你能够将工作重心放在专业技术和机器学习，而不是在基础结构和自动化上。 [详细了解 ML 管道](concept-ml-pipelines.md)。

在本教程中，你将学习如何执行以下任务：

> [!div class="checklist"]
> * 配置工作区并下载示例数据
> * 创建用于提取和输出数据的数据对象
> * 下载、准备模型并将其注册到工作区
> * 预配计算目标并创建评分脚本
> * 生成、运行并发布管道
> * 为管道启用 REST 终结点

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>先决条件

* 如果你没有 Azure 机器学习工作区或笔记本虚拟机，请完成[设置教程的第 1 部分](tutorial-1st-experiment-sdk-setup.md)。
* 完成设置教程后，使用同一笔记本服务器打开 **tutorials/tutorial-pipeline-batch-scoring-classification.ipynb** 笔记本。

如果你想要在自己的[本地环境](how-to-configure-environment.md#local)中运行此教程，也可以在 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 上找到它。 运行 `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` 以获取所需的包。

## <a name="configure-workspace-and-create-datastore"></a>配置工作区并创建数据存储

从现有的 Azure 机器学习工作区创建工作区对象。 
+ [工作区](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)是可接受 Azure 订阅和资源信息的类。 它还可创建云资源来监视和跟踪模型运行。 

+ `Workspace.from_config()` 读取文件 **config.json** 并将身份验证详细信息加载到名为 `ws` 的对象。 在本教程中，`ws` 在代码的其余部分使用。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>为示例图像创建数据存储

从帐户 `pipelinedata` 中的公共 Blob 容器 `sampledata` 获取 ImageNet 评估公共数据示例。 调用 `register_azure_blob_container()` 可使数据可用于名为 `images_datastore` 的工作区。 然后，将工作区的默认数据存储指定为输出数据存储，用于在管道中为输出评分。

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>创建数据对象

生成管道时，将使用 `DataReference` 对象从工作区数据存储读取数据，并使用 `PipelineData` 对象在管道步骤之间传输中间数据。

> [!Important]
> 此批量评分示例只使用一个管道步骤，但在包含多个步骤的用例中，典型的流包括：
>
> 1. 使用 `DataReference` 对象作为**输入**来提取原始数据，执行一些转换，然后**输出** `PipelineData` 对象。
>
> 2. 使用上一步骤的 `PipelineData` **输出对象**作为*输入对象*，并在后续步骤中重复此操作。

在此场景中，你将创建与输入图像和分类标签（y-test 值）的数据存储目录相对应的 `DataReference` 对象。 此外，将为批量评分输出数据创建一个 `PipelineData` 对象。

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>下载并注册模型

下载预先训练的 Tensorflow 模型用于管道中的批量评分。 首先创建一个用于存储模型的本地目录，然后下载并提取该目录。

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

现在，可将模型注册到工作区，以便能够在管道流程中轻松检索该模型。 在 `register()` 静态函数中，`model_name` 参数是用于在整个 SDK 中查找模型的键。

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-remote-compute-target"></a>创建并附加远程计算目标

由于 ML 管道无法在本地运行，因此你需要在云资源中运行这些管道。 我们将其称为远程计算目标，它们是可重用的虚拟计算环境，可在其中运行试验和 ML 工作流。 运行以下代码创建支持 GPU 的 [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) 目标，并将其附加到工作区。 有关计算目标的详细信息，请参阅[概念文章](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target)。


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>编写评分脚本

若要执行评分，请创建批量评分脚本 `batch_scoring.py`，并将其写入当前目录。 该脚本将提取输入图像，应用分类模型，然后将预测结果输出到结果文件中。

脚本 `batch_scoring.py` 采用以下参数，这些参数是从稍后在本教程中创建的管道步骤传递的：

- `--model_name`：所用模型的名称
- `--label_dir`：保存 `labels.txt` 文件的目录 
- `--dataset_path`：包含输入图像的目录
- `--output_dir`：脚本将对数据运行模型，并将 `results-label.txt` 输出到此目录
- `--batch_size`：运行模型时使用的批大小

管道基础结构使用 `ArgumentParser` 类将参数传入管道步骤。 例如，在以下代码中，为第一个参数 `--model_name` 指定了属性标识符 `model_name`。 在 `main()` 函数中，使用 `Model.get_model_path(args.model_name)` 访问此属性。


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)

    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                  num_classes=classes_num,
                                                  is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> 本教程中的管道只有一个步骤，它会将输出写入某个文件；对于多步骤管道，你也可以使用 `ArgumentParser` 来定义要将输出数据写入到的目录，以便将其输入到后续步骤。 有关使用 `ArgumentParser` 设计模式在多个管道步骤之间传递数据的示例，请参阅 [笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)。

## <a name="build-and-run-the-pipeline"></a>生成并运行管道

在运行管道之前，需要创建一个对象，用于定义脚本 `batch_scoring.py` 所需的 Python 环境和依赖项。 所需的主要依赖项是 Tensorflow，但你还需要通过 SDK 为后台进程安装 `azureml-defaults`。 使用依赖项创建 `RunConfiguration` 对象，并指定 Docker 和 Docker-GPU 支持。

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>参数化管道

定义管道的自定义参数以控制批大小。 通过 REST 终结点发布并公开管道后，也会公开配置的任何参数，并可以在使用 HTTP 请求重新运行管道时，在 JSON 有效负载中指定这些参数。

创建 `PipelineParameter` 对象来启用此行为，并定义名称和默认值。

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>创建管道步骤

管道步骤是一个对象，用于封装运行管道所需的任何内容，其中包括：

* 环境和依赖项设置
* 要在其上运行管道的计算资源
* 输入和输出数据，以及任何自定义参数
* 对执行步骤期间要运行的脚本或 SDK 逻辑的引用

有多个类继承自父类 [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)，以帮助使用特定的框架和堆栈生成步骤。 在本示例中，你将使用 [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) 类来定义使用自定义 Python 脚本的步骤逻辑。 请注意，如果脚本的某个自变量是步骤的输入或步骤的输出，则必须分别在 `arguments` 数组**以及** `input` 或 `output` 参数中定义该自变量。  

如果存在多个步骤，`outputs` 数组中的某个对象引用可用作后续管道步骤的**输入**。

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

有关不同步骤类型的所有类的列表，请参阅[步骤包](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

### <a name="run-the-pipeline"></a>运行管道

现在请运行该管道。 首先，使用工作区引用和创建的管道步骤创建一个 `Pipeline` 对象。 `steps` 参数是步骤数组，在本例中，批量评分只使用一个步骤。 若要生成包含多个步骤的管道，请将步骤按顺序放入此数组。

接下来，使用 `Experiment.submit()` 函数提交管道以供执行。 还可以指定自定义参数 `param_batch_size`。 `wait_for_completion` 函数将在管道生成过程中输出日志，这样你就可以查看当前进度。

> [!IMPORTANT]
> 首次管道运行需要大约 **15 分钟**，因为此过程需要下载所有依赖项、创建 Docker 映像，并预配/创建 Python 环境。 再次运行所花费的时间会大幅减少，因为这些资源已重复使用。 但是，总运行时间取决于脚本的工作负荷，以及每个管道步骤中运行的进程数。

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>下载并查看输出

运行以下代码下载通过 `batch_scoring.py` 脚本创建的输出文件，然后浏览评分结果。

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>文件名</th>
      <th>预测</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102.JPEG</td>
      <td>Rhodesian ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103.JPEG</td>
      <td>tripod</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104.JPEG</td>
      <td>typewriter keyboard</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105.JPEG</td>
      <td>silky terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106.JPEG</td>
      <td>Windsor tie</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107.JPEG</td>
      <td>harvestman</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108.JPEG</td>
      <td>violin</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109.JPEG</td>
      <td>loudspeaker</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110.JPEG</td>
      <td>apron</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111.JPEG</td>
      <td>American lobster</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-rest-endpoint"></a>从 REST 终结点发布和运行

运行以下代码，将管道发布到工作区。 在门户上的工作区中，可以看到管道的元数据，包括运行历史记录和持续时间。 还可以从门户手动运行管道。

此外，发布管道会启用一个 REST 终结点，以便从任何平台上的任何 HTTP 库重新运行该管道。

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

若要从 REST 终结点运行管道，首先需要获取 OAuth2 Bearer-type 身份验证标头。 为方便演示，本示例使用了交互式身份验证，但对于需要自动化身份验证或无头身份验证的大多数生产方案，请使用[此笔记本中所述](https://aka.ms/pl-restep-auth)的服务主体身份验证。

服务主体身份验证涉及到在 **Azure Active Directory** 中创建**应用注册**，生成客户端机密，然后为服务主体授予对机器学习工作区的**角色访问权限**。 然后，使用 [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) 类来管理身份验证流。 

`InteractiveLoginAuthentication` 和 `ServicePrincipalAuthentication` 均继承自 `AbstractAuthentication`，在这两种情况下，请以相同的方式使用 `get_authentication_header()` 函数来提取标头。

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

从已发布的管道对象的 `endpoint` 属性获取 REST URL。 也可以在门户上的工作区中找到该 REST URL。 对终结点生成 HTTP POST 请求，并指定身份验证标头。 此外，请使用试验名称和批大小参数添加一个 JSON 有效负载对象。 特此提醒，`param_batch_size` 将传递到 `batch_scoring.py` 脚本，因为已在步骤配置中将其定义为 `PipelineParameter` 对象。

发出触发运行的请求。 访问响应字典中的 `Id` 密钥以获取运行 ID 的值。

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

使用运行 ID 监视新运行的状态。 这又要花费 10-15 分钟来完成运行，结果类似于上次管道运行，因此，如果不需要查看其他管道运行，可以跳过完整输出的监视。

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>清理资源

如果你打算运行其他 Azure 机器学习教程，请不要结束本部分。

### <a name="stop-the-notebook-vm"></a>停止笔记本 VM

如果你使用了云笔记本服务器，请停止未使用的 VM，以降低成本。

1. 在工作区中，选择“笔记本 VM”。 
1. 从列表中选择 VM。
1. 选择“停止”  。
1. 准备好再次使用服务器时，选择“启动”  。

### <a name="delete-everything"></a>删除所有内容

如果不打算使用已创建的资源，请删除它们，以免产生任何费用。

1. 在 Azure 门户中，选择最左侧的“资源组”  。
1. 从列表中选择已创建的资源组。
1. 选择“删除资源组”  。
1. 输入资源组名称。 然后选择“删除”  。

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本机器学习管道教程中，你已完成以下任务：

> [!div class="checklist"]
> * 使用环境依赖项生成了一个要在远程 GPU 计算资源上运行的管道
> * 使用预先训练的 Tensorflow 模型创建了一个用于运行批量预测的评分脚本
> * 发布了管道，并使其从 REST 终结点运行

有关使用机器学习 SDK 生成管道的其他示例，请参阅[笔记本存储库](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)。
