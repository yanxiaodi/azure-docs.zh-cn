---
title: 滚动更新 Azure Service Fabric 群集证书 |Microsoft Docs
description: 了解如何滚动使用由证书公用名标识的 Service Fabric 群集证书。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: atsenthi
ms.openlocfilehash: d6ead6aaa5d4c0e864126bf63d4cc0e9339464f2
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773352"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>手动滚动更新 Service Fabric 群集证书
当 Service Fabric 群集证书接近到期时，需要更新该证书。  如果群集已[设置为基于公用名称使用证书](service-fabric-cluster-change-cert-thumbprint-to-cn.md)（而不是指纹），证书滚动更新很简单。  从证书颁发机构获取具有新到期日期的新证书。  自签名证书不支持用于生产 Service Fabric 群集，也不支持包括在执行 Azure 门户群集创建工作流期间生成的证书。 新证书必须具有与旧证书相同的公用名称。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

当主机上安装了多个验证证书时，Service Fabric 群集将自动使用声明的证书，并进一步延长到将来的到期日期。 最佳做法是使用资源管理器模板预配 Azure 资源。 对于非生产环境，可以使用以下脚本将新证书上传到密钥保管库，然后将证书安装在虚拟机规模集上： 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> 计算虚拟机规模集机密不支持对两个不同的机密使用相同的资源 ID，因为每个机密都是带有版本的唯一资源。 

## <a name="next-steps"></a>后续步骤

* 了解[群集安全性](service-fabric-cluster-security.md)。
* [更新和管理群集证书](service-fabric-cluster-security-update-certs-azure.md)
