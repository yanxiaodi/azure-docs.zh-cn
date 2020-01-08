---
title: 将证书导入到 Azure Service Fabric 上运行的容器| Microsoft Docs
description: 了解如何将证书文件导入到 Service Fabric 容器服务。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 80ac20fd2dc7bfe3fea6a58a6df94e3f7b99a700
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599218"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>将证书文件导入到 Service Fabric 上运行的容器

可以通过指定证书保护容器服务。 Service Fabric 提供一种机制，供容器内服务访问在 Windows 或 Linux 群集（5.7 或更高版本）的节点中安装的证书。 必须在群集的所有节点上将证书安装到 LocalMachine 下的证书存储中。 与证书对应的私钥必须可用、可访问，在 Windows 上还必须可导出。 `ContainerHostPolicies` 标记下的应用程序清单中提供了证书信息，如以下代码片段所示：

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

对于 Windows 群集，当启动应用程序时，运行时会将所引用的每个证书及其对应的私钥导出到一个 PFX 文件中，该文件由随机生成的密码提供保护。 可以在容器内使用以下环境变量来分别访问此 PFX 和密码文件： 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

对于 Linux 群集，证书 (PEM) 是从 X509StoreName 指定的存储中复制到容器中。 对应的 linux 环境变量为：

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

或者，如果已有所需形式的证书且想在容器内访问它，可在应用包内创建数据包，并在应用程序清单中指定以下内容：

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

容器服务或进程负责将证书文件导入到容器中。 要导入证书，可以在容器进程内使用 `setupentrypoint.sh` 脚本或执行自定义代码。 下面是用于导入 PFX 文件的 C# 示例代码：

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
此 PFX 证书可以用于对应用程序或服务或与其他服务的安全通信进行身份验证。 默认情况下，文件仅可列入 SYSTEM 的 ACL。 根据服务需要，可将其列入其他帐户的 ACL。

有关后续步骤，请阅读以下文章：

* [将 Windows 容器部署到 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)
* [将 Docker 容器部署到 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)
