---
title: 混合标识设计 - 内容管理要求 Azure | Microsoft Docs
description: 让你深入了解如何确定业务的内容管理要求。 通常，当用户拥有自己的设备时，他们还可能具有多个凭据，会根据他们使用的应用程序交替使用这些凭据。 请务必区分使用个人凭据创建的内容与使用公司凭据创建的内容。 标识解决方案应能与云服务进行交互，从而向最终用户提供无缝体验的同时，确保用户的隐私以及增强针对数据泄漏的防护。
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64918438"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>确定混合标识解决方案的内容管理要求
了解业务的内容管理要求可能会直接影响你决定使用哪一个混合标识解决方案。 随着设备数量的激增以及用户会自带设备 ([BYOD](https://aka.ms/byodcg))，公司必须确保自身数据的安全，同时也要使用户的隐私完好无损。 通常，当用户拥有自己的设备时，他们还可能具有多个凭据，会根据他们使用的应用程序交替使用这些凭据。 请务必区分使用个人凭据创建的内容与使用公司凭据创建的内容。 标识解决方案应能与云服务进行交互，从而向最终用户提供无缝体验的同时，确保用户的隐私以及增强针对数据泄漏的防护。 

不同的技术控制会利用标识解决方案提供内容管理，如下图中所示：

![安全控制](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**将利用标识管理系统的安全控制**

通常，内容管理要求会在以下几个方面利用标识管理系统：

* 隐私：识别拥有资源的用户，并应用适当控制保持完整性。
* 数据分类：确定用户或组的身份以及对象的访问级别（根据其分类）。 
* 数据泄漏保护：负责保护数据免遭泄漏的安全控制将需要与标识系统进行交互，来验证用户的身份。 这对于审核跟踪目的同样重要。

> [!NOTE]
> 有关数据分类的最佳实践和准则的详细信息，请阅读[云数据分类准备就绪](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)。
> 
> 

规划混合标识解决方案时，确保根据组织的要求回答以下问题：

* 贵公司是否部署了实施数据保护的安全控制？
  * 如果是，则这些安全控制是否能够与要采用的混合标识解决方案集成？
* 贵公司是否使用数据分类？
  * 如果是，则当前解决方案是否能够与要采用的混合标识解决方案集成？
* 贵公司当前是否具有针对数据泄漏的任何解决方案？ 
  * 如果是，则当前解决方案是否能够与要采用的混合标识解决方案集成？
* 贵公司是否需要审核对资源的访问？
  * 如果是，是什么类型的资源？
  * 如果是，何种级别的信息是必需的？
  * 如果是，审核日志必须位于何处？ 在本地，还是在云中？
* 贵公司是否需要加密任何包含敏感数据（身份证号、信用卡号等）的电子邮件？
* 贵公司是否需要加密与外部业务合作伙伴共享的所有文档/内容？
* 贵公司是否需要对某些类型的电子邮件强制实施公司策略（不回复所有人、不转发）？

> [!NOTE]
> 务必记下每个答案并了解答案背后的依据。 [定义数据保护策略](plan-hybrid-identity-design-considerations-data-protection-strategy.md)会检查可用选项以及每个选项的优点/缺点。  回答了这些问题之后，就会挑选出最适合业务需求的选项。
> 
> 

## <a name="next-steps"></a>后续步骤
[确定访问控制要求](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](plan-hybrid-identity-design-considerations-overview.md)

