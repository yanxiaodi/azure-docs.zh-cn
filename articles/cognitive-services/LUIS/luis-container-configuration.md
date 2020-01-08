---
title: Docker 容器设置-LUIS
titleSuffix: Azure Cognitive Services
description: 使用 `docker run` 命令参数配置 LUIS 容器运行时环境。 LUIS 有几个必需的设置以及一些可选设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 9760475886ecb0f20d9f0f3981eab8246643da21
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101982"
---
# <a name="configure-language-understanding-docker-containers"></a>配置语言理解 Docker 容器 

**语言理解** (LUIS) 容器运行时环境使用 `docker run` 命令参数进行配置。 LUIS 有几个必需的设置以及一些可选设置。 多个[示例](#example-docker-run-commands)命令均可用。 特定于容器的设置是输入[装载设置](#mount-settings)和账单设置。 

## <a name="configuration-settings"></a>配置设置

此容器具有以下配置设置：

|必填|设置|用途|
|--|--|--|
|是|[ApiKey](#apikey-setting)|用于跟踪账单信息。|
|否|[ApplicationInsights](#applicationinsights-setting)|允许向容器添加 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遥测支持。|
|是|[Billing](#billing-setting)|指定 Azure 上服务资源的终结点 URI。|
|是|[Eula](#eula-setting)| 表示已接受容器的许可条款。|
|否|[Fluentd](#fluentd-settings)|将日志和（可选）指标数据写入 Fluentd 服务器。|
|否|[Http 代理](#http-proxy-credentials-settings)|配置 HTTP 代理以发出出站请求。|
|否|[Logging](#logging-settings)|为容器提供 ASP.NET Core 日志记录支持。 |
|是|[Mounts](#mount-settings)|从主计算机读取数据并将其写入到容器，以及从容器读回数据并将其写回到主计算机。|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)、[`Billing`](#billing-setting) 和 [`Eula`](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](luis-container-howto.md#billing)。

## <a name="apikey-setting"></a>ApiKey 设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，且此值必须是为 [`Billing`](#billing-setting) 配置设置指定的“认知服务”资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：**认知服务**“资源管理”部分的“密钥”下
* LUIS 门户：“密钥和终结点”设置页。 

请勿使用初学者密钥或创作密钥。 

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>账单设置

`Billing` 设置指定 Azure 上用于计量容器帐单信息的“认知服务”资源的终结点 URI。 必须为这个配置设置指定值，且此值必须是 Azure 上“认知服务”资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

可以在以下位置找到此设置：

* Azure 门户：**认知服务**概述，标记为 `Endpoint`
* LUIS 门户：“密钥和终结点设置”页面，作为终结点 URI 的一部分。

请记住在 URL 中包括 `luis/v2.0` 路由，如下表所示：


|必填| 姓名 | 数据类型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 账单终结点 URI<br><br>例如：<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 代理凭据设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 选项来指定输入装载或输出装载。 

LUIS 容器不使用输入或输出装载来存储训练或服务数据。 

主机确切语法的安装位置因主机操作系统不同而异。 此外，由于 docker 服务帐户使用的权限与主机安装位置权限之间的冲突，可能无法访问[主计算机](luis-container-howto.md#the-host-computer)的装载位置。 

下表描述了支持的设置。

|必填| 姓名 | 数据类型 | 描述 |
|-------|------|-----------|-------------|
|是| `Input` | String | 输入装入点的目标。 默认值为 `/input`。 这是 LUIS 包文件的位置。 <br><br>例如：<br>`--mount type=bind,src=c:\input,target=/input`|
|否| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括 LUIS 查询日志和容器日志。 <br><br>例如：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 运行命令示例

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](luis-container-howto.md#stop-the-container)它。

* 这些示例使用 `C:` 驱动器外的目录来避免 Windows 上的任何权限冲突。 如果需要使用特定目录作为输入目录，则需要授予 docker 服务权限。 
* 除非非常熟悉 docker 容器，否则不要更改参数顺序。
* 如果使用的是不同的操作系统，请使用正确的控制台/终端、用于装载的文件夹语法和系统的行继续符。 这些示例假定 Windows 控制台使用行继续符 `^`。 由于容器是 Linux 操作系统，因此目标装载使用 Linux 样式的文件夹语法。

请记住在 URL 中包括 `luis/v2.0` 路由，如下表所示。

将 {_argument_name_} 替换为为你自己的值：

| 占位符 | ReplTest1 | 格式或示例 |
|-------------|-------|---|
| **{API_KEY}** | `LUIS` Azure`LUIS`密钥页上的资源的终结点键。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Azure `LUIS`“概览”页面上提供了账单终结点值。| 有关显式示例，请参阅[收集所需的参数](luis-container-howto.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。 有关详细信息，请参阅[计费](luis-container-howto.md#billing)。
> ApiKey 值是 LUIS 门户中“密钥和终结点”页面中的“密钥”，也可以在 Azure `Cognitive Services`资源密钥页上找到。 

### <a name="basic-example"></a>基本示例

以下示例具有运行容器的最少参数：

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights 示例

以下示例设置 ApplicationInsights 参数，以便在容器运行时将遥测数据发送到 Application Insights：

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>日志记录示例 

以下命令设置日志记录级别 `Logging:Console:LogLevel`，将日志记录级别配置为 [`Information`](https://msdn.microsoft.com)。 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](luis-container-howto.md)
* 若要解决与 LUIS 功能相关的问题，请参阅[故障排除](troubleshooting.md)。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
