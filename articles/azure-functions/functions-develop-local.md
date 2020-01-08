---
title: 在本地开发并运行 Azure Functions | Microsoft Docs
description: 了解如何在本地计算机上对 Azure 函数进行编码和测试，然后在 Azure Functions 中运行。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 7c2e727ecb080d1db212e8b45a2c48bac81a3949
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709316"
---
# <a name="code-and-test-azure-functions-locally"></a>在本地对 Azure Functions 进行编码和测试

尽管你可以在 [Azure 门户]中开发和测试 Azure Functions，但许多开发人员更偏爱本地开发体验。 在 Functions 中，可以轻松使用你偏好的代码编辑器和开发工具在本地计算机上开发和测试函数。 本地函数可以连接到实时 Azure 服务，你可以在本地计算机上使用完整的 Functions 运行时调试函数。

## <a name="local-development-environments"></a>本地开发环境

在本地计算机开发函数的方式取决于[语言](supported-languages.md)和工具偏好。 下表中的环境支持本地开发：

|环境                              |语言         |描述|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# （类库）](functions-dotnet-class-library.md), [C# 脚本 (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | [适用于 VS Code 的 Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)在 VS Code 中添加了 Functions 支持。 需要 Core Tools。 使用 2.x 版 Core Tools 时，支持 Linux、MacOS 和 Windows 上的开发。 若要了解详细信息，请参阅[使用 Visual Studio Code 创建第一个函数](functions-create-first-function-vs-code.md)。 |
| [命令提示符或终端](functions-run-local.md) | [C#（类库）](functions-dotnet-class-library.md), [C# 脚本 (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Azure Functions Core Tools] 提供核心运行时和模板用于创建函数，以实现本地开发。 版本 2.x 支持 Linux、MacOS 和 Windows 上的开发。 所有环境依赖于 Core Tools 提供本地 Functions 运行时。 |
| [Visual Studio 2019](functions-develop-vs.md) | [C#（类库）](functions-dotnet-class-library.md) | Azure Functions 工具包含在 [Visual Studio 2019](https://www.visualstudio.com/vs/) 和更高版本的 **Azure 开发**工作负荷中。 可以编译类库中的函数，并将 .dll 文件发布到 Azure。 包含用于本地测试的 Core Tools。 有关详细信息，请参阅[使用 Visual Studio 开发 Azure Functions](functions-develop-vs.md)。 |
| [Maven](functions-create-first-java-maven.md)（不同的） | [Java](functions-reference-java.md) | 与 Core Tools 集成以实现 Java 函数的开发。 版本 2.x 支持 Linux、MacOS 和 Windows 上的开发。 有关详细信息，请参阅[使用 Java 和 Maven 创建第一个函数](functions-create-first-java-maven.md)。 还支持使用 [Eclipse](functions-create-maven-eclipse.md) 和 [IntelliJ IDEA](functions-create-maven-intellij.md) 进行开发 |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

其中每个本地开发环境允许创建函数应用项目，并使用预定义的 Functions 模板创建新函数。 每个环境使用 Core Tools，使你能够在自己的计算机上针对实际的 Functions 运行时测试和调试函数，就像对其他任何应用执行此操作一样。 你还可以将函数应用项目从这些环境中的任何一种发布到 Azure。  

## <a name="next-steps"></a>后续步骤

+ 若要详细了解如何使用 Visual Studio 2019 在本地开发编译的 C# 函数，请参阅[使用 Visual Studio 开发 Azure Functions](functions-develop-vs.md)。
+ 若要详细了解如何在 Mac、Linux 或 Windows 计算机上使用 VS Code 来对函数进行本地开发，请参阅[从 VS Code 部署 Azure Functions](/azure/javascript/tutorial-vscode-serverless-node-01)。
+ 若要详细了解如何通过命令提示符或终端开发函数，请参阅[使用 Azure Functions Core Tools](functions-run-local.md)。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 门户]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
