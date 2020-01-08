---
title: Azure Service Fabric 的网络模式 | Microsoft Docs
description: 介绍 Service Fabric 的常见网络模式以及如何使用 Azure 网络功能创建群集。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: atsenthi
ms.openlocfilehash: 90b2a1954d60f1e86ab61afb264483177f4aca3b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073936"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric 网络模式
可将 Azure Service Fabric 群集与其他 Azure 网络功能集成。 本文说明如何创建使用以下功能的群集：

- [现有虚拟网络或子网](#existingvnet)
- [静态公共 IP 地址](#staticpublicip)
- [仅限内部的负载均衡器](#internallb)
- [内部和外部负载均衡器](#internalexternallb)

Service Fabric 在标准的虚拟机规模集中运行。 可在虚拟机规模集中使用的任何功能同样可在 Service Fabric 群集中使用。 虚拟机规模集与 Service Fabric 的 Azure 资源管理器模板的网络部分是相同的。 部署到现有虚拟网络后，可以轻松地整合其他网络功能，例如 Azure ExpressRoute、Azure VPN 网关、网络安全组和虚拟网络对等互连。

与其他网络功能相比，Service Fabric 的独特之处体现在一个方面。 [Azure 门户](https://portal.azure.com)在内部使用 Service Fabric 资源提供程序连接到群集，以获取有关节点和应用程序的信息。 Service Fabric 资源提供程序需要对管理终结点上的 HTTP 网关端口（默认为 19080）具有可公开访问的入站访问权限。 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 使用该管理终结点来管理群集。 Service Fabric 资源提供程序还使用此端口来查询有关群集的信息，以便在 Azure 门户中显示。 

如果无法通过 Service Fabric 资源提供程序访问端口 19080，门户中会显示一条类似于“找不到节点”的消息，并且节点和应用程序列表显示为空。 如果想要在 Azure 门户中查看群集，负载均衡器必须公开一个公共 IP 地址，并且网络安全组必须允许端口 19080 上的传入流量。 如果设置不满足这些要求，Azure 门户不会显示群集的状态。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>模板

所有 Service Fabric 模板都位于 [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking) 中。 使用以下 PowerShell 命令应可按原样部署模板。 若要部署现有的 Azure 虚拟网络模板或静态公共 IP 模板，请先阅读本文的[初始设置](#initialsetup)部分。

<a id="initialsetup"></a>
## <a name="initial-setup"></a>初始设置

### <a name="existing-virtual-network"></a>现有虚拟网络

在以下示例中，我们从 **ExistingRG** 资源组中名为 ExistingRG-vnet 的现有虚拟网络着手。 子网命名为 default。 这些默认资源是在使用 Azure 门户创建标准虚拟机 (VM) 时创建的。 可以只创建虚拟网络和子网而不创建 VM，但是，将群集添加到现有虚拟网络的主要目的是提供与其他 VM 之间的网络连接。 创建 VM 可以很好地示范现有虚拟网络的典型用法。 如果 Service Fabric 群集仅使用不带公共 IP 地址的内部负载均衡器，则可以将 VM 及其公共 IP 用作安全的*转接盒*。

### <a name="static-public-ip-address"></a>静态公共 IP 地址

静态公共 IP 地址通常是一个专用资源，与其所分配的 VM 分开管理。 它在专用网络资源组中（而不是在 Service Fabric 群集资源组本身中）预配。 使用 Azure 门户或 PowerShell 在同一个 ExistingRG 资源组中创建名为 staticIP1 的静态公共 IP 地址：

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric 模板

本文中的示例使用 Service Fabric template.json。 在创建群集之前，可以使用标准门户向导下载该模板。 也可以使用[示例模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)之一，例如[保护五节点 Service Fabric 群集](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)。

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>现有虚拟网络或子网

1. 将子网参数更改为现有子网的名称，然后添加两个新参数以引用现有的虚拟网络：

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. 注释掉 `Microsoft.Compute/virtualMachineScaleSets` 的 `nicPrefixOverride` 属性，因为你使用的是现有子网，并且已在步骤 1 中禁用了此变量。

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. 将 `vnetID` 变量更改为指向现有虚拟网络：

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. 从资源中删除 `Microsoft.Network/virtualNetworks`，使 Azure 不会创建新的虚拟网络：

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. 从 `Microsoft.Compute/virtualMachineScaleSets` 的 `dependsOn` 属性中注释掉虚拟网络，避免非得要创建新的虚拟网络：

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. 部署模板：

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    部署后，虚拟网络应包含新的规模集 VM。 虚拟机规模集节点类型应显示现有虚拟网络和子网。 还可以使用远程桌面协议 (RDP) 访问虚拟网络中已有的 VM，并对新规模集 VM 执行 ping 操作：

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

请参阅[并非特定于 Service Fabric 的另一个示例](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)。


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>静态公共 IP 地址

1. 添加现有静态 IP 资源组名称、名称和完全限定的域名 (FQDN) 的参数：

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. 删除 `dnsName` 参数。 （静态 IP 已有名称。）

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. 添加一个变量来引用现有的静态 IP 地址：

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. 从资源中删除 `Microsoft.Network/publicIPAddresses`，使 Azure 不会创建新的 IP 地址：

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. 从 `Microsoft.Network/loadBalancers` 的 `dependsOn` 属性中注释掉 IP 地址，避免非得要创建新的 IP 地址：

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. 在 `Microsoft.Network/loadBalancers` 资源中，将 `frontendIPConfigurations` 的 `publicIPAddress` 元素更改为引用现有的静态 IP 地址而不是新建的 IP 地址：

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. 在 `Microsoft.ServiceFabric/clusters` 资源中，将 `managementEndpoint` 更改为静态 IP 地址的 DNS FQDN。 如果使用安全群集，请确保将 *http://* 更改为 *https://* 。 （请注意，此步骤仅适用于 Service Fabric 群集。 如果使用虚拟机规模集，请跳过此步骤。）

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. 部署模板：

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

部署后，可以看到负载均衡器已绑定到其他资源组中的公共静态 IP 地址。 Service Fabric 客户端连接终结点和 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 终结点指向静态 IP 地址的 DNS FQDN。

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>仅限内部的负载均衡器

本方案用仅限内部的负载均衡器替代默认 Service Fabric 模板中的外部负载均衡器。 有关 Azure 门户和 Service Fabric 资源提供程序的含义，请参阅前面的内容。

1. 删除 `dnsName` 参数。 （不需要此参数。）

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. （可选）如果使用静态分配方法，可添加静态 IP 地址参数。 如果使用动态分配方法，则无需执行此步骤。

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. 从资源中删除 `Microsoft.Network/publicIPAddresses`，使 Azure 不会创建新的 IP 地址：

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. 删除 `Microsoft.Network/loadBalancers` 的 IP 地址 `dependsOn` 属性，避免非得要创建新的 IP 地址。 添加虚拟网络 `dependsOn` 属性，因为负载均衡器现在依赖于虚拟网络中的子网：

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. 将负载均衡器的 `frontendIPConfigurations` 设置从使用 `publicIPAddress` 更改为使用子网和 `privateIPAddress`。 `privateIPAddress` 使用预定义的静态内部 IP 地址。 要使用动态 IP 地址，请删除 `privateIPAddress` 元素，然后将 `privateIPAllocationMethod` 更改为 **Dynamic**。

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. 在 `Microsoft.ServiceFabric/clusters` 资源中，将 `managementEndpoint` 更改为指向内部负载均衡器地址。 如果使用安全群集，请确保将 *http://* 更改为 *https://* 。 （请注意，此步骤仅适用于 Service Fabric 群集。 如果使用虚拟机规模集，请跳过此步骤。）

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. 部署模板：

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

部署后，负载均衡器将使用专用静态 IP 地址 10.0.0.250。 如果同一虚拟网络中还有其他计算机，可以转到内部 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 终结点。 可以看到，该终结点已连接到负载均衡器后面的某个节点。

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>内部和外部负载均衡器

本方案从现有的单节点类型外部负载均衡器着手，添加一个相同节点类型的内部负载均衡器。 附加到后端地址池的后端端口只能分配给单个负载均衡器。 选择哪个负载均衡器使用应用程序端口，哪个负载均衡器使用管理终结点（端口 19000 和 19080）。 如果将管理终结点放在内部负载均衡器上，请记住前文所述的 Service Fabric 资源提供程序限制。 本示例将管理终结点保留在外部负载均衡器上。 还需要添加一个端口号为 80 的应用程序端口，并将其放在内部负载均衡器上。

在双节点类型的群集中，一个节点类型位于外部负载均衡器上。 另一个节点类型位于内部负载均衡器上。 要使用双节点类型的群集，请在门户创建的双节点类型模板（附带两个负载均衡器）中，将第二个负载均衡器切换为内部负载均衡器。 有关详细信息，请参阅[仅限内部的负载均衡器](#internallb)部分。

1. 添加静态内部负载均衡器 IP 地址参数。 （有关使用动态 IP 地址的说明，请参阅本文的前面部分。）

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. 添加应用程序端口 80 参数。

3. 若要添加现有网络变量的内部版本，请复制并粘贴这些变量，并在名称中添加“-Int”：

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. 如果从使用应用程序端口 80、由门户生成的模板开始，则默认门户模板会在外部负载均衡器上添加 AppPort1（端口 80）。 在此情况下，请将 AppPort1 从外部负载均衡器 `loadBalancingRules` 和探测中删除，以便将其添加到内部负载均衡器中：

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. 添加第二个 `Microsoft.Network/loadBalancers` 资源。 该资源与[仅限内部的负载均衡器](#internallb)部分中创建的内部负载均衡器类似，不过它使用的是“-Int”负载均衡器变量，并且仅实现应用程序端口 80。 这样做还会删除 `inboundNatPools`，以便将 RDP 终结点保留在公共负载均衡器上。 如果要将 RDP 放在内部负载均衡器上，请将 `inboundNatPools` 从外部负载均衡器移到此内部负载均衡器：

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. 在 `Microsoft.Compute/virtualMachineScaleSets` 资源的 `networkProfile` 中，添加内部后端地址池：

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. 部署模板：

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

部署后，可在资源组中看到两个负载均衡器。 如果浏览这两个负载均衡器，可以看到公共 IP 地址和分配给公共 IP 地址的管理终结点（端口 19000 和 19080）。 此外，还会看到静态内部 IP 地址和分配给内部负载均衡器的应用程序终结点（端口 80）。 这两个负载均衡器使用同一个虚拟机规模集后端池。

## <a name="notes-for-production-workloads"></a>生产工作负荷的说明

上述 GitHub 模板旨在使用 Azure 标准负载均衡器 (SLB) 的默认 SKU, 即基本 SKU。 此 SLB 没有 SLA, 因此对于生产工作负荷, 应使用标准 SKU。 有关详细信息, 请参阅[Azure 标准负载均衡器概述](/azure/load-balancer/load-balancer-standard-overview)。 任何使用适用于 SLB 的标准 SKU 的 Service Fabric 群集都需要确保每个节点类型都有一个允许端口443上的出站流量的规则。 这是完成群集安装所必需的, 并且任何没有此类规则的部署都将失败。 在上面的 "仅内部" 负载均衡器示例中, 必须使用允许端口443的出站流量的规则将额外的外部负载平衡器添加到模板。

## <a name="next-steps"></a>后续步骤
[创建群集](service-fabric-cluster-creation-via-arm.md)

部署后，可在资源组中看到两个负载均衡器。 如果浏览这两个负载均衡器，可以看到公共 IP 地址和分配给公共 IP 地址的管理终结点（端口 19000 和 19080）。 此外，还会看到静态内部 IP 地址和分配给内部负载均衡器的应用程序终结点（端口 80）。 这两个负载均衡器使用同一个虚拟机规模集后端池。

