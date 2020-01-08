---
title: 为开发测试实验室虚拟机创建自定义项目 | Microsoft Docs
description: 了解如何创作自己的项目以用于 Azure 开发测试实验室。
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 69b83590fb9b25c68d231b732b985ba633bb6884
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399203"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>为开发测试实验室虚拟机创建自定义项目

请观看以下视频，了解本文所述步骤：

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>概述
预配 VM 后，可使用项目来部署和设置应用程序。 项目由项目定义文件和存储在 Git 存储库的文件夹中的其他脚本文件组成。 项目定义文件包含 JSON 和可用于指定要在 VM 上安装的内容的表达式。 例如，可以定义项目名称、要运行的命令，以及运行命令时可用的参数。 可以按名称引用项目定义文件中的其他脚本文件。

## <a name="artifact-definition-file-format"></a>项目定义文件格式
以下示例显示组成定义文件基本结构的各个部分：

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| 元素名称 | 必需？ | 描述 |
| --- | --- | --- |
| $schema |否 |JSON 架构文件的位置。 JSON 架构文件可帮助测试定义文件的有效性。 |
| title |是 |实验室中显示的项目的名称。 |
| description |是 |实验室中显示的项目的说明。 |
| iconUri |否 |实验室中显示的图表的 URI。 |
| targetOsType |是 |在其中安装项目的 VM 的操作系统。 支持的选项为：Windows 和 Linux。 |
| parameters |否 |在计算机上运行项目安装命令时提供的值。 可帮助对项目进行自定义。 |
| runCommand |是 |在 VM 上执行的项目安装命令。 |

### <a name="artifact-parameters"></a>项目参数
在定义文件的参数部分中，指定用户在安装项目时可以输入的值。 可以在项目安装命令中引用这些值。

若要定义参数，请使用以下结构：

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| 元素名称 | 必需？ | 描述 |
| --- | --- | --- |
| type |是 |参数值的类型。 请参阅以下允许类型列表。 |
| displayName |是 |在实验室中显示给用户的参数的名称。 |
| description |是 |实验室中显示的参数的说明。 |

允许的类型是：

* 字符串（任何有效的 JSON 字符串）
* int（任何有效的 JSON 整数）
* bool（任何有效的 JSON 布尔值）
* 数组（任何有效的 JSON 数组）

## <a name="secrets-as-secure-strings"></a>作为安全字符串的机密
声明为安全字符串的机密。 下面是用于声明中的一个安全字符串参数语法`parameters`一部分**artifactfile.json**文件：

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

为项目安装命令，运行 PowerShell 脚本会使用 Convertto-securestring 命令创建的安全字符串。 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

有关完整示例 artifactfile.json 和 artifact.ps1 （PowerShell 脚本），请参阅[GitHub 上的此示例](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)。

另一个要注意的重要一点是不记录到控制台输出捕获的用户调试的机密。 

## <a name="artifact-expressions-and-functions"></a>项目表达式和函数
可以使用表达式和函数构建项目安装命令。
表达式用方括号（[和]）括起来，并在安装项目时求值。 表达式可以出现在 JSON 字符串值的任何位置。 表达式始终返回另一个 JSON 值。 如果需要使用以方括号 ([) 开头的文本字符串，则必须使用两个方括号 ([[)。
通常情况下，使用具有函数的表达式来构造值。 与在 JavaScript 中一样，函数调用的格式为 **functionName(arg1, arg2, arg3)** 。

以下列表显示常见函数：

* **parameters(parameterName)** ：返回运行项目命令时提供的参数值。
* **concat(arg1, arg2, arg3,….. )** ：合并多个字符串值。 此函数可结合使用各种类型的参数。

以下示例显示如何使用表达式和函数构造值：

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>创建自定义项目

1. 安装 JSON 编辑器。 需要一个 JSON 编辑器来处理项目定义文件。 建议使用 [Visual Studio Code](https://code.visualstudio.com/)，它适用于 Windows、Linux 和 OS X。
2. 获取示例 artifactfile.json 定义文件。 查看开发测试实验室团队在我们的 [GitHub 存储库](https://github.com/Azure/azure-devtestlab)中创建的项目。 我们已创建资源丰富的项目库，可帮助你创建自己的项目。 下载项目定义文件并对其进行更改以创建自己的项目。
3. 利用 IntelliSense。 使用 IntelliSense 查看可用于构建项目定义文件的有效元素。 此外还可以查看元素值的不同选项。 例如，编辑“targetOsType”元素时，IntelliSense 显示 Windows 或 Linux 两种选择。
4. 将项目存储在[开发测试实验室的公共 Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)或[自己的 Git 存储库](devtest-lab-add-artifact-repo.md)中。 在公共存储库中，可以查看他人共享的项目，可以直接使用或自定义它们来满足自己的需求。
   
   1. 为每个项目创建单独的目录。 目录名称应与项目名称相同。
   2. 将项目定义文件 (artifactfile.json) 存储在创建的目录中。
   3. 存储从项目安装命令引用的脚本。
      
      下面是项目文件夹的外观示例：
      
      ![项目文件夹示例](./media/devtest-lab-artifact-author/git-repo.png)
5. 如果使用自己的存储库来存储项目，请按照以下文章中的说明将存储库添加到实验室：[为项目和模板添加 Git 存储库](devtest-lab-add-artifact-repo.md)。

## <a name="related-articles"></a>相关文章
* [如何诊断开发测试实验室中的项目失败](devtest-lab-troubleshoot-artifact-failure.md)
* [使用开发测试实验室中的资源管理器模板将 VM 加入到现有 Active Directory 域中](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>后续步骤
* 了解如何 [将 Git 项目存储库添加到实验室](devtest-lab-add-artifact-repo.md)。
