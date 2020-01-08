---
title: 部署自定义映像、多容器或内置映像 - Azure 应用服务 | Microsoft Docs
description: 对于 Linux 上的应用服务，如何在自定义 Docker 容器部署、多容器和内置应用程序框架之间做出选择
keywords: azure 应用服务, web 应用, linux, oss
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ae28b185aa44ca22d59204826036435a10c64e91
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066794"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>自定义映像、多容器还是内置平台映像？

[Linux 上 的应用服务](app-service-linux-intro.md)提供了三种不同的方法来获取发布到 Web 的应用程序：

- **自定义映像部署**：将应用 Docker 化为 Docker 映像，映像中包含可运行包中的所有文件和依赖项。
- **多容器部署**：使用 Docker Compose 配置文件跨多个容器 "docker 化" 应用。
- **使用内置平台映像的应用部署**：我们的内置平台映像包含常见 Web 应用运行时和依赖项，如 Node 和 PHP。 使用任意一种 [Azure App Service 部署方法](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)将应用部署到 Web 应用的存储，然后使用内置平台映像运行应用。

## <a name="which-method-is-right-for-your-app"></a>你的应用适合哪种方法？ 

要考虑的主要因素包括：

- **开发工作流中 Docker 的可用性**：自定义映像开发需要具备 Docker 开发工作流的基本知识。 将自定义映像部署到 Web 应用需要将自定义映像发布到存储库主机，如 Docker Hub。 如果熟悉 Docker 并且可以将 Docker 任务添加到生成工作流，或者如果已将应用作为 Docker 映像发布，则自定义映像无疑就是最佳选择。
- **多层体系结构**：通过使用多容器部署多个容器（例如一个 Web 应用程序层和一个 API 层）来将各项功能分离开来。 
- **应用程序性能**：使用诸如 Redis 的缓存层提高多容器应用的性能。 选择多容器可实现此目的。
- **唯一运行时要求**：内置平台映像旨在满足大多数 Web 应用的需求，但在可定制性方面存在限制。 应用可能具有唯一依赖项或其他内置映像无法满足的运行时需求。
- **生成要求**：利用[持续部署](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，可以直接通过源代码启动应用并在 Azure 上运行。 无需外部生成或发布过程。 但是，[Kudu](https://github.com/projectkudu/kudu/wiki) 部署引擎中的生成工具在可定制性和可用性方面存在限制。 随着应用的依赖项或对自定义生成逻辑的要求的增加，Kudu 的功能可能无法满足应用。
- **磁盘读/写要求**：所有 Web 应用都分配有用于 Web 内容的存储卷。 此卷由 Azure 存储提供支持，装载到应用文件系统中的 `/home`。 与容器文件系统中的文件不同，内容卷中的文件可通过应用的所有扩展实例进行访问，并且应用重启后修改将继续保留。 但是，内容卷的磁盘延迟比本地容器文件系统的延迟更高且更多变，并且平台升级、非计划停机和网络连接错误都可能影响访问。 自定义映像部署将文件放在映像文件系统而非内容卷中，因此可能更适用于需要频繁以只读方式访问内容文件的应用。
- **生成资源使用情况**：从源部署应用时，Kudu 运行的部署脚本与正在运行的应用使用相同的应用服务计划计算和存储资源。 大型应用部署可能会比预期占用更多资源或时间。 具体而言，许多部署工作流会在应用内容卷上生成大量磁盘活动，但应用内容卷尚未针对此类活动进行优化。 自定义映像可用一个包将所有应用文件和依赖项传输到 Azure，无需其他文件传输或部署操作。
- **对快速迭代的需求**：将应用 Docker 化需要其他生成步骤。 为使更改生效，必须在每次更新时将新映像推送到存储库。 这些更新随后将提取到 Azure 环境中。 如果其中一个内置容器满足应用需求，从源进行部署可能会加快开发工作流的速度。

## <a name="next-steps"></a>后续步骤

自定义容器:
* [运行自定义容器](quickstart-docker-go.md)

多容器:
* [创建多容器应用](quickstart-multi-container.md)

以下文章介绍了在 Linux 上使用内置平台映像的应用服务入门:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)