---
title: 在 gMSA 帐户下运行 Azure Service Fabric 服务 |Microsoft Docs
description: 了解如何在 Service Fabric Windows 独立群集上以 gMSA 身份运行服务。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d00eceffebb222196191a389058c0feb496e169a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307646"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>以组托管服务帐户身份运行服务
在 Windows Server 独立群集上，可以使用 RunAs 策略以组托管服务帐户 (gMSA) 的身份来运行服务。  默认情况下，Service Fabric 应用程序在运行 Fabric.exe 程序的帐户之下运行。 即使在共享托管环境中以不同帐户身份运行应用程序，也可确保运行的应用程序彼此更安全。 注意：这是域中的本地 Active Directory，不是 Azure Active Directory (Azure AD)。 使用 gMSA 时，没有密码或加密的密码存储在应用程序清单中。  还可以采用 [Active Directory 用户或组](service-fabric-run-service-as-ad-user-or-group.md)身份运行服务。

以下示例演示如何创建一个名为 *svc-Test$* 的 gMSA 帐户；如何将该托管服务帐户部署到群集节点；以及如何配置用户主体。

先决条件：
- 域需要 KDS 根密钥。
- 域中必须至少有一个 Windows Server 2012 （或 R2） DC。

1. 让 Active Directory 域管理员使用 `New-ADServiceAccount` commandlet 创建组托管服务帐户，并确保 `PrincipalsAllowedToRetrieveManagedPassword` 包括所有 service fabric 群集节点。 `AccountName`、`DnsHostName` 和 `ServicePrincipalName` 必须是唯一的。

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. 在每个 Service Fabric 群集节点（例如，`SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`）上，安装并测试 gMSA。
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. 配置用户主体，并配置 RunAsPolicy 以引用用户。
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> 如果将 RunAs 策略应用到服务，且服务清单使用 HTTP 协议声明终结点资源，则必须指定 SecurityAccessPolicy。  有关详细信息，请参阅[为 HTTP 和 HTTPS 终结点分配安全访问策略](service-fabric-assign-policy-to-endpoint.md)。 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
有关后续步骤，请阅读以下文章：
* [了解应用程序模型](service-fabric-application-model.md)
* [在服务清单中指定资源](service-fabric-service-manifest-resources.md)
* [部署应用程序](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
