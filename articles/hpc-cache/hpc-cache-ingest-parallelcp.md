---
title: Azure HPC 缓存预览数据引入-并行复制脚本
description: 如何使用并行复制脚本在 Azure HPC 缓存中将数据移动到 Blob 存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: 852b4e692a4316c7701c8c179039104bee561949
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180953"
---
# <a name="azure-hpc-cache-preview-data-ingest---parallel-copy-script-method"></a>Azure HPC 缓存（预览）数据引入-并行复制脚本方法

本文提供了有关创建``parallelcp``脚本并使用它将数据移动到 Blob 存储容器以便与 Azure HPC 缓存一起使用的说明。

若要详细了解如何将数据移到 Azure HPC 缓存的 Blob 存储，请参阅[将数据移到 AZURE Hpc 缓存的 Azure blob 存储](hpc-cache-ingest.md)。

## <a name="create-the-parallelcp-script"></a>创建 parallelcp 脚本

以下脚本将添加可执行文件 `parallelcp`。 （此脚本适用于 Ubuntu；如果使用其他分发版，则必须单独安装 ``parallel``。）

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>并行复制示例

此示例使用并行复制脚本在 Azure HPC ``glibc``缓存中使用源文件进行编译。

源文件缓存在 Azure HPC 缓存装载点，对象文件存储在本地硬盘上。

此示例将并行复制脚本与选项``-j``一起使用， ``make``以获得并行化。

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
