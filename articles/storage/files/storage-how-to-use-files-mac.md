---
title: 在 macOS 中通过 SMB 装载 Azure 文件共享 | Microsoft Docs
description: 了解如何在 macOS 中通过 SMB 装载 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bd696ce5a314b0c849256311d0629b917036ea2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699548"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>在 macOS 中通过 SMB 装载 Azure 文件共享
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 Azure 文件共享可以通过 macOS El Capitan 10.11+ 使用标准的 SMB 3 协议进行装载。 本文介绍如何使用两种不同的方法通过 Finder UI 和 Terminal 在 macOS 中装载 Azure 文件共享。

> [!Note]  
> 通过 SMB 装载 Azure 文件共享之前，建议禁用 SMB 数据包签名。 如果不这样做，则从 macOS 访问 Azure 文件共享时，可能会导致性能不佳。 SMB 连接会加密，因此这不会影响连接的安全性。 在 Terminal 中，以下命令会禁用 SMB 数据包签名，详见这篇[有关如何禁用 SMB 数据包签名的 Apple 支持文章](https://support.apple.com/HT205926)：  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>在 macOS 中装载 Azure 文件共享的先决条件
* **存储帐户名称**：需提供存储帐户的名称才能装载 Azure 文件共享。

* **存储帐户密钥**：需提供主要（或辅助）存储密钥才能装载 Azure 文件共享。 目前不支持使用 SAS 密钥进行装载。

* **确保端口 445 处于打开状态**：SMB 通过 TCP 端口 445 通信。 在客户端计算机 (Mac) 上检查，确保防火墙未阻止 TCP 端口 445。

## <a name="mount-an-azure-file-share-via-finder"></a>通过 Finder 装载 Azure 文件共享
1. **打开 Finder**：默认情况下，Finder 在 macOS 中处于打开状态，但你可以通过单击 Dock 中的“macOS 笑脸图标”来确保它是当前选定的应用程序：  
    ![macOS 笑脸图标](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **在“前往”菜单中选择“连接到服务器”** ：使用先决条件中的 UNC 路径，将开头的双反斜杠 (`\\`) 转换为 `smb://`，将所有其他的反斜杠 (`\`) 转换为斜杠 (`/`)。 链接应如下所示：![“连接到服务器”对话框](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **当系统提示你输入用户名和密码时，使用存储帐户名称和存储帐户密钥**：单击“连接到服务器”对话框中的“连接”时，系统会提示你输入用户名和密码（系统会使用你的 macOS 用户名自动填充此用户名字段）。 可以选择将存储帐户名称/存储帐户密钥置于 macOS 密钥链中。

4. **根据需要使用 Azure 文件共享**：使用共享名称和存储帐户密钥替换用户名和密码以后，系统就会装载共享。 可以像通常情况下使用本地文件夹/文件共享一样使用此功能，例如，可以将文件拖放到文件共享中：

    ![已装载 Azure 文件共享的快照](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>通过 Terminal 装载 Azure 文件共享
1. 将 `<storage-account-name>` 替换为存储帐户的名称。 在系统提示时提供存储帐户密钥作为密码。 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **根据需要使用 Azure 文件共享**：Azure 文件共享将装载到上一命令指定的装载点。  

    ![已装载 Azure 文件共享的快照](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息。

* [Apple 支持文章 - 如何使用 Mac 上的“文件共享”进行连接](https://support.apple.com/HT204445)
* [常见问题](../storage-files-faq.md)
* [在 Windows 上进行故障排除](storage-troubleshoot-windows-file-connection-problems.md)      
* [在 Linux 上进行故障排除](storage-troubleshoot-linux-file-connection-problems.md)    
