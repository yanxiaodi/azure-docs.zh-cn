---
title: 教程 - 在 Azure 中的 Linux 虚拟机上部署 LEMP | Microsoft Docs
description: 本教程介绍如何在 Azure 中的 Linux 虚拟机上安装 LEMP 堆栈
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 15d88e082f9ab0838f4a560d89801edd9d46d682
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703546"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>教程：在 Azure 中的 Linux 虚拟机上安装 LEMP Web 服务器

本文逐步讲解如何在 Azure 中的 Ubuntu VM 上部署 NGINX Web 服务器、MySQL 和 PHP（LEMP 堆栈）。 LEMP 堆栈可以替代常用的 [LAMP 堆栈](tutorial-lamp-stack.md)，可安装在 Azure 中。 若要了解 LEMP 服务器的运作情况，可以选择性地安装并配置 WordPress 站点。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Ubuntu VM（LEMP 堆栈中的“L”）
> * 为 Web 流量打开端口 80
> * 安装 NGINX、MySQL 和 PHP
> * 验证安装和配置
> * 在 LEMP 服务器上安装 WordPress

此设置用于快速测试或概念证明。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>安装 NGINX、MySQL 和 PHP

运行以下命令更新 Ubuntu 包源并安装 NGINX、MySQL 和 PHP。 

```bash
sudo apt update && sudo apt install nginx && sudo apt install mysql-server php-mysql php-fpm
```

系统会提示安装包和其他依赖项。 此股从会安装最低要求的 PHP 扩展，这些扩展是通过 MySQL 使用 PHP 所必需的。  

## <a name="verify-installation-and-configuration"></a>验证安装和配置


### <a name="verify-nginx"></a>验证 NGINX

使用以下命令检查 NGINX 版本：
```bash
nginx -v
```

安装 NGINX 并向 VM 打开端口 80 以后，即可通过 Internet 访问 Web 服务器。 若要查看 NGINX 欢迎页，请打开 Web 浏览器并输入 VM 的公共 IP 地址。 使用通过 SSH 连接到 VM 时所用的公共 IP 地址：

![NGINX 默认页][3]


### <a name="verify-and-secure-mysql"></a>验证并保护 MySQL

使用以下命令检查 MySQL 版本（请注意大写的 `V` 参数）：

```bash
mysql -V
```

若要帮助保护 MySQL 安装，包括设置 root 密码，请运行 `mysql_secure_installation` 脚本。 

```bash
sudo mysql_secure_installation
```

还可以选择设置“验证密码”插件（推荐）。 然后，为 MySQL root 用户设置一个密码，并为您的环境配置剩余的安全设置。 建议对所有问题都回答“Y”（是）。

如果想要试用 MySQL 功能（创建 MySQL 数据库、添加用户或更改配置设置），请登录到 MySQL。 此步骤非本教程必需步骤。 


```bash
sudo mysql -u root -p
```

完成后，键入 `\q` 退出 mysql 提示符。

### <a name="verify-php"></a>验证 PHP

使用以下命令检查 PHP 版本：

```bash
php -v
```

配置 NGINX 以使用 PHP FastCGI Process Manager (PHP-FPM)。 运行以下命令备份原始 NGINX 服务器块配置文件，并在所选的编辑器中编辑原始文件：

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

在编辑器中，将 `/etc/nginx/sites-available/default` 的内容替换为以下内容。 请参阅注释了解设置说明。 用你的 VM 的公共 IP 地址替换 *yourPublicIPAddress*，确认 `fastcgi_pass` 中的 PHP 版本，并保留其余设置。 然后保存文件。

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
}
```

检查 NGINX 配置中的语法错误：

```bash
sudo nginx -t
```

如果语法正确，请使用以下命令重启 NGINX：

```bash
sudo service nginx restart
```

如果想要进一步测试，请创建一个可在浏览器中查看的快速 PHP 信息页。 以下命令创建 PHP 信息页：

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



现在，可以检查创建的 PHP 信息页。 打开浏览器并转到 `http://yourPublicIPAddress/info.php`。 替换 VM 的公共 IP 地址。 应如下图所示。

![PHP 信息页][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>后续步骤

本教程在 Azure 中部署了一台 LEMP 服务器。 你已了解如何：

> [!div class="checklist"]
> * 创建 Ubuntu VM
> * 为 Web 流量打开端口 80
> * 安装 NGINX、MySQL 和 PHP
> * 验证安装和配置
> * 在 LEMP 堆栈上安装 WordPress

转到下一教程，了解如何使用 SSL 证书保护 Web 服务器。

> [!div class="nextstepaction"]
> [使用 SSL 保护 Web 服务器](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
