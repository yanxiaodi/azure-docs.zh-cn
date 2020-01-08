---
title: 配置 Azure Service Fabric 容器服务的网络模式 | Microsoft Docs
description: 了解如何设置 Azure Service Fabric 支持的不同网络模式。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: d749e1355e69ad93c8c211474043f88127ec76f0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599388"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric 容器网络模式

容器服务的 Azure Service Fabric 群集默认使用 nat 网络模式。 使用 nat 模式时，如果多个容器服务在相同端口上进行侦听，则会发生部署错误。 为了支持在相同端口上进行多个容器服务侦听，Service Fabric 提供开放网络模式（版本 5.7 和更高版本）。 在开放模式下，每个容器服务都具有动态分配的内部 IP 地址，支持在相同端口上同时侦听多个服务。  

如果在服务清单中有一个带静态终结点的容器服务，则可以使用开放模式创建和删除新服务，这不会造成部署错误。 相同的 docker-compose.yml 文件还可结合使用静态端口映射，创建多个服务。

容器服务重启或移动到群集中的另一个节点时，IP 地址会发生更改。 为此，不建议使用动态分配的 IP 地址来发现容器服务。 应仅使用 Service Fabric 命名服务或 DNS 服务来发现服务。 

>[!WARNING]
>Azure 允许每个虚拟网络总共 65,356 个 IP。 因此，在一个虚拟网络中，节点数和容器服务实例数的总和（使用开放模式）不能超过 65,356 个 IP。 对于高密度方案，建议使用 nat 网络模式。 此外，其他依赖项（例如负载均衡器）也有要考虑的其他[限制](https://docs.microsoft.com/azure/azure-subscription-service-limits)。 当前，已测试了每个节点最多 50 个 IP 并且已证明了稳定性。 
>

## <a name="set-up-open-networking-mode"></a>设置开放网络模式

1. 设置 Azure 资源管理器模板。 在群集资源的 fabricSettings 部分中，启用 DNS 服务和 IP 提供程序： 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. 设置虚拟机规模集资源的网络配置文件部分。 这允许在群集的每个节点上配置多个 IP 地址。 下例为 Windows/Linux Service Fabric 群集的每个节点设置了五个 IP 地址。 在每个节点的端口上都可以有五个服务实例侦听。 若要可从 Azure 负载均衡器访问这五个 IP，请按如下所示在 Azure 负载均衡器后端地址池中注册这五个 IP。  还需要在变量部分中将变量添加到模板的顶部。

    将此部分添加到变量：

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    将此部分添加到虚拟机规模集资源：

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. 仅对于 Windows 群集，请使用以下值设置 Azure 网络安全组 (NSG) 规则，以便为虚拟网络打开端口 UDP/53：

   |设置 |ReplTest1 | |
   | --- | --- | --- |
   |Priority |2000 | |
   |名称 |Custom_Dns  | |
   |Source |VirtualNetwork | |
   |目标 | VirtualNetwork | |
   |服务 | DNS (UDP/53) | |
   |Action | Allow  | |
   | | |

4. 在应用程序清单中为每个服务指定网络模式 `<NetworkConfig NetworkType="Open">`。 开放网络模式使服务获得专用 IP 地址。 如果未指定模式，服务默认使用 nat 模式。 在以下清单示例中，`NodeContainerServicePackage1` 和 `NodeContainerServicePackage2` 服务均可在相同端口上进行侦听（这两个服务都在 `Endpoint1` 上进行侦听）。 如果指定了开放网络模式，便无法指定 `PortBinding` 配置。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    可在一个应用程序中为 Windows 群集跨服务混合与匹配不同网络模式。 一些服务使用开放模式，而其他服务使用 nat 模式。 如果某个服务配置为使用 nat 模式，则该服务进行侦听的端口必须唯一。

    >[!NOTE]
    >Linux 群集上不支持混合不同服务的网络模式。 
    >

5. 选择“打开”模式时，服务清单中的**终结点**定义应显式指向终结点所对应的代码包，即使服务包中只有一个代码包，也是如此。 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. 对于 Windows，VM 重新启动将导致重新创建开放网络。 这是为了缓解网络堆栈中的底层问题。 默认行为是重新创建网络。 如果需要关闭此行为，则可以使用以下配置，然后进行配置升级。

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>后续步骤
* [了解 Service Fabric 应用程序模型](service-fabric-application-model.md)
* [详细了解 Service Fabric 服务清单资源](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [将 Windows 容器部署到 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)
* [将 Docker 容器部署到 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)
