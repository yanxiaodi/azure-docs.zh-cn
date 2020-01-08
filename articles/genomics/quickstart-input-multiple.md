---
title: 使用多个输入-Microsoft 基因组学提交工作流
titleSuffix: Azure
description: 本文演示如何在输入的文件为多个 FASTQ 或 BAM 文件来自同一示例提交到 Microsoft 基因组学服务工作流。 已安装 msgen 客户端并成功运行了通过服务的示例数据。
services: genomics
ms.service: genomics
author: grhuynh
manager: cgronlund
ms.author: grhuynh
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 399b1ed735ce1b7a3fca1d27155863f6bfa18776
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60780872"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>使用来自同一示例的多个输入提交工作流

本文演示如何在输入的文件为多个 FASTQ 或 BAM 文件提交到 Microsoft 基因组学服务工作流**来自同一示例**。 例如，如果在顺序器的多个通道中运行了**同一示例**，顺序器可能会为每个通道输出一组 FASTQ 文件。 可以不在比对与变体识别之前将这些 FASTQ 文件串联在一起，而是直接将所有这些输入提交到 `msgen` 客户端。 来自 `msgen` 客户端的输出将是**一组**文件，包括 .bam、.bai、.vcf 文件。 

但请记住，**不能**在同一提交中混合 FASTQ 和 BAM 文件。 另外，**不能**从多个个体提交多个 FASTQ 或 BAM 文件。 

本文假定你已安装和运行 `msgen` 客户端，并且熟悉如何使用 Azure 存储。 如果您已成功提交使用提供的示例数据的工作流，你现可继续执行本文。 


## <a name="multiple-bam-files"></a>多个 BAM 文件

### <a name="upload-your-input-files-to-azure-storage"></a>将输入文件上传到 Azure 存储
假设你有多个用作输入的 BAM 文件，即 *reads.bam*、*additional_reads.bam* 和 *yet_more_reads.bam*，并且已将其上传到 Azure 中的存储帐户 *myaccount*。 你有 API URL 和访问密钥。 你希望将输出置于 **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** 中。


### <a name="submit-your-job-to-the-msgen-client"></a>将作业提交到 `msgen` 客户端 

可以提交多个 BAM 文件，方法是将其所有名称传递到 --input-blob-name-1 参数。 请注意，所有文件都应该来自同一示例，但其顺序不重要。 以下部分详述了通过命令行进行的示例提交，适用于 Windows、Unix 和配置文件。 为清楚起见，添加了换行符：


对于 Windows：

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


对于 Unix

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


如果愿意使用配置文件，可以参考以下示例，其中包含：

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

提交 `config.txt` 文件时可以使用此调用：`msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>多个配对的 FASTQ 文件

### <a name="upload-your-input-files-to-azure-storage"></a>将输入文件上传到 Azure 存储
假设有多个用作输入的配对的 FASTQ 文件，即 *reads_1.fq.gz* 和 *reads_2.fq.gz*、*additional_reads_1.fq.gz* 和 *additional_reads_2.fq.gz*，以及 *yet_more_reads_1.fq.gz* 和 *yet_more_reads_2.fq.gz*。 你已将它们上传到 Azure 中的存储帐户 *myaccount*，并且你有 API URL 和访问密钥。 你希望将输出置于 **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** 中。


### <a name="submit-your-job-to-the-msgen-client"></a>将作业提交到 `msgen` 客户端 

配对的 FASTQ 文件不仅需要来自同一示例，还需要一起处理。  将它们作为参数传递到 --input-blob-name-1 和 --input-blob-name-2 时，文件名的顺序很重要。 

以下部分详述了通过命令行进行的示例提交，适用于 Windows、Unix 和配置文件。 为清楚起见，添加了换行符：


对于 Windows：

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


对于 Unix：

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


如果愿意使用配置文件，可以参考以下示例，其中包含：

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

提交 `config.txt` 文件时可以使用此调用：`msgen submit -f config.txt`

## <a name="next-steps"></a>后续步骤
本文介绍了如何将多个 BAM 文件或配对的 FASTQ 文件上传到 Azure 存储中，以及如何通过 `msgen` Python 客户端将工作流提交到 Microsoft 基因组学服务。 若要详细了解工作流提交以及其他可以与 Microsoft 基因组学服务配合使用的命令，请参阅[常见问题解答](frequently-asked-questions-genomics.md)。 