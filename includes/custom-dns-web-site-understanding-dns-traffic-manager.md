---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: f42a97cdd74d360bc047ef561cbe626d526f9e4a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173529"
---
域名系统 (DNS) 用于在 Internet 上查找内容。 例如，在浏览器中输入一个地址或单击 Web 页面上的某个链接时，它使用 DNS 将域名转换为 IP 地址。 IP 地址有点像街道地址，但其用户友好性并不是很好。 例如，记住 **contoso.com** 这样的 DNS 名称比较容易，而记住 192.168.1.88 或 2001:0:4137:1f67:24a2:3888:9cce:fea3 这样的 IP 地址则要困难得多。

DNS 系统基于*记录*。 记录将特定的*名称*（例如 **contoso.com**）与一个 IP 地址或其他的 DNS 名称相关联。 当某个应用程序（例如 Web 浏览器）在 DNS 中查找某个名称，它将找到该记录，并将它所指向的内容用作地址。 如果它所指向的值是 IP 地址，则浏览器会使用该值。 如果它指向另一个 DNS 名称，则应用程序必须再次执行解析。 最终，所有名称解析都以 IP 地址的形式结束。

创建 Azure 网站时，DNS 名称会自动分配到站点。 此名称采用 **&lt;yoursitename&gt;.azurewebsites.net** 格式。 将网站添加为 Azure 流量管理器终结点后，可以通过 **&lt;yourtrafficmanagerprofile&gt;.trafficmanager.net** 域访问该网站。

> [!NOTE]
> 将网站配置为流量管理器终结点后，创建 DNS 记录时需使用 **.trafficmanager.net** 地址。
> 
> 只能为 Traffic Manager 使用 CNAME 记录
> 
> 

此外还有多种类型的记录，每种类型都有其自己的功能和限制，但是对于配置为流量管理器终结点的网站，我们只关心一种，即 *CNAME* 记录。

### <a name="cname-or-alias-record"></a>CNAME 记录，或称别名记录
CNAME 记录映射*特定*DNS 名称，例如**mail.contoso.com**或**www\.contoso.com**，到另一个 （规范） 域名。 对于使用流量管理器的 Azure 网站，规范域名是流量管理器配置文件的 **&lt;myapp>.trafficmanager.net** 域名。 创建映射后，CNAME 将为 **&lt;myapp>.trafficmanager.net** 域名创建一个别名。 CNAME 条目会自动解析为 **&lt;myapp>.trafficmanager.net** 域名的 IP 地址，因此，如果网站的 IP 地址发生更改，不需要采取任何操作。

一旦流量到达 Traffic Manager，后者随后会使用它为流量配置的负载均衡方法，将该流量路由到网站。 这对网站访问者完全透明。 他们将只在浏览器中看到自定义域。

> [!NOTE]
> 某些域注册机构仅允许你将使用 CNAME 记录，如映射子域**www\.contoso.com**，并不根名称，例如**contoso.com**。 有关 CNAME 记录的详细信息，请参阅由注册机构提供的文档、<a href="https://en.wikipedia.org/wiki/CNAME_record">CNAME 记录上的 Wikipedia 条目</a>或 <a href="https://tools.ietf.org/html/rfc1035">IETF 域名 - 实现和规范</a>文档。

