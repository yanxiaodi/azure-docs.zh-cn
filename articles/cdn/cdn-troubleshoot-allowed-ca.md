---
title: 获准对 Azure CDN 启用自定义 HTTPS 的证书颁发机构 | Microsoft Docs
description: 若要使用自己的证书对自定义域启用 HTTPS，必须使用获准的证书颁发机构 (CA) 来创建证书。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 754941163ddce9512870f0b76a96207472e5b2aa
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593363"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>获准对 Azure CDN 启用自定义 HTTPS 的证书颁发机构

对于“Microsoft 提供的 Azure CDN 标准版”  终结点上的 Azure 内容分发网络 (CDN) 自定义域，如果[使用自己的证书启用 HTTPS 功能](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)，必须使用获准的证书颁发机构 (CA) 来创建 SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

> [!NOTE]
> 使用自己的证书启用自定义 HTTPS 仅适用于“Microsoft 提供的 Azure CDN 标准版”  配置文件。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

