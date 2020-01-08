---
title: Microsoft 基因组学：常见问题-常见问题 |Microsoft Docs
titleSuffix: Azure
description: 有关 Microsoft 基因组学的用户常见问题解答。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: d36a2c6379a95cc67a55c2cc266ced94b4a0179a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672236"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft 基因组学：常见问题

本文列出了用户可能会遇到的与 Microsoft 基因组学相关的几大疑问。 有关 Microsoft 基因组学服务的详细信息，请参阅[什么是 Microsoft 基因组学？](overview-what-is-genomics.md)。 有关故障排除的详细信息，请参阅我们的[故障排除指南](troubleshooting-guide-genomics.md)。 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>如何在 Microsoft 基因组学运行 GATK4 工作流？
在 Microsoft 基因组学服务的 config.txt 文件中，指定到 process_name `gatk4`。 请注意，您将会按常规费率计费计费。


## <a name="what-is-the-sla-for-microsoft-genomics"></a>什么是 Microsoft 基因组学的 SLA？
我们保证 Microsoft 基因组学服务 99.9% 的时间均可用于接收工作流 API 请求。 有关详细信息，请参阅 [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)。

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Microsoft 基因组学的使用情况如何体现在我的帐单上？
Microsoft 基因组学将按每个工作流处理的千兆碱基数计费。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/genomics/)。


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>在哪里可以找到所有可用 `msgen` 客户端命令和参数列表？
通过运行 `msgen help` 可获得可用命令和参数的完整列表。 如果未提供进一步的参数，它会显示可用帮助部分的列表，每个 `submit`、`list`、`cancel` 和 `status` 各有一个列表。 若要获取有关特定命令的帮助，请键入 `msgen help command`；例如，`msgen help submit` 会列出所有提交选项。

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>`msgen` 客户端最常用的命令有哪些？
最常用的命令是 `msgen` 客户端的参数，包括： 

 |**命令**          |  **字段说明** |
 |:--------------------|:-------------         |
 |`list`               |返回已提交的作业列表。 有关参数，请参阅 `msgen help list`。  |
 |`submit`             |向服务提交工作流请求。 有关参数，请参阅 `msgen help submit`。|
 |`status`             |返回由 `--workflow-id` 指定的工作流状态。 另请参阅 `msgen help status`。 |
 |`cancel`             |发送请求来取消由 `--workflow-id` 指定的工作流进程。 另请参阅 `msgen help cancel`。 |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>在何处获取 `--api-url-base` 的值？
请转到 Azure 门户并打开基因组学帐户页。 在“管理”标题下方，选择“访问密钥”   。 可在此处找到 API URL 和访问密钥。

## <a name="where-do-i-get-the-value-for---access-key"></a>在何处获取 `--access-key` 的值？
请转到 Azure 门户并打开基因组学帐户页。 在“管理”标题下方，选择“访问密钥”   。 可在此处找到 API URL 和访问密钥。

## <a name="why-do-i-need-two-access-keys"></a>为什么需要两个访问密钥？
如果想更新（重新生成）密钥而不中断对服务的使用，则需要两个访问密钥。 例如，若要更新第一个密钥，则应该让所有新工作流使用第二个密钥。 等待使用第一个密钥的所有工作流完成，然后再更新第一个密钥。

## <a name="do-you-save-my-storage-account-keys"></a>是否保存了我的存储帐户密钥？
存储帐户密钥用于创建 Microsoft 基因组学服务的短期访问令牌，读取输入文件并写入输出文件。 默认令牌持续时间为 48 小时。 可以通过提交命令的 `-sas/--sas-duration` 选项更改令牌持续时间；该值以小时为单位。

## <a name="what-genome-references-can-i-use"></a>可以使用哪些基因组引用？

支持以下这些引用：

 |引用              | `-pa/--process-args` 的值 |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38（无 alt 分析） | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>如何将命令行参数格式化为配置文件？ 

msgen 可识别采用以下格式的配置文件：
* 所有选项都以键值对的形式提供，值与密钥之间用冒号隔开。
  忽略空格。
* 忽略以 `#` 开头的行。
* 可通过去除任何长格式命令行参数单词之间的前导短划线并将短划线替换为下划线，将其转换为密钥。 此处是一些转换示例：

  |命令行参数            | 配置文件行 |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | api_url_base: https://url     |
  |`-k/--access-key KEY`            | access_key:KEY               |      
  |`-pa/--process-args R=B37m1`     | process_args:R-b37m1         |  

## <a name="next-steps"></a>后续步骤

使用以下资源进行 Microsoft 基因组学入门：
- 通过 Microsoft 基因组学服务开始运行第一个工作流。 [通过 Microsoft 基因组学服务运行工作流](quickstart-run-genomics-workflow-portal.md)
- 提交自己的数据并通过以下 Microsoft 基因组学服务进行处理：[配对 FASTQ](quickstart-input-pair-FASTQ.md) | [BAM ](quickstart-input-BAM.md) | [多个 FASTQ 或 BAM](quickstart-input-multiple.md) 

