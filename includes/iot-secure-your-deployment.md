---
title: include 文件
description: include 文件
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e5acb8e0f8805da7f14bbce58b4bfd2acdc24f23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173606"
---
# <a name="secure-your-internet-of-things-iot-deployment"></a>保护物联网 (IoT) 部署

本文提供保护基于 Azure IoT 的物联网 (IoT) 基础结构的进一步详细信息。 它链接到配置和部署每个组件的实现级别详细信息。 还提供多种竞争方式间的比较和选择。

保护 Azure IoT 部署可分为以下三个安全区域：

* **设备安全**：在现实中部署 IoT 设备时，保护 IoT 设备安全。

* **连接安全**：确保 IoT 设备和 IoT 中心之间传输的所有数据的机密性和防篡改性。

* **云安全**：数据移动或存储在云中时，提供一种数据保护方式。

![三个安全区域](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>安全的设备预配和身份验证

IoT 解决方案加速器通过以下两种方法保护 IoT 设备：

* 为每个设备提供唯一标识密钥（安全令牌），设备可使用该密钥与 IoT 中心通信。

* 使用设备内置 [X.509 证书](https://www.itu.int/rec/T-REC-X.509-201210-S)和私钥作为一种向 IoT 中心验证设备的方式。 此身份验证方式可确保任何时候都无法在设备外部获知设备上的私钥，从而提供更高级别的安全性。

安全令牌方式通过将对称密钥与每个呼叫关联，为每个设备向 IoT 中心作出的呼叫提供身份验证。 基于 X.509 的身份验证允许物理层 IoT 设备的身份验证作为 TLS 连接建立的一部分。 基于安全令牌的方式可在没有 X.509 身份验证的情况下使用，但这种模式的安全性较低。 这两种方式的选择主要取决于设备验证需要达到的安全程度和设备上安全储存的可用性（以安全存储私钥）。

## <a name="iot-hub-security-tokens"></a>IoT 中心安全令牌

IoT 中心使用安全令牌对设备和服务进行身份验证，以避免在网络上发送密钥。 并且安全令牌的有效期和范围有限。 Azure IoT SDK 无需任何特殊配置即可自动生成令牌。 但在某些情况下，需要用户生成并直接使用安全令牌。 这些方案包括 MQTT、AMQP 或 HTTP 应用层协议的直接使用，以及令牌服务模式的实现。

可在以下文章中找到有关安全令牌结构及其用法的详细信息：

* [安全令牌结构](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [将 SAS 令牌用作设备](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

每个 IoT 中心都有一个[标识注册表](../articles/iot-hub/iot-hub-devguide-identity-registry.md)，可用于在服务中创建各设备的资源（例如包含即时云到设备消息的队列），以及允许访问面向设备的终结点。 IoT 中心标识注册表针对解决方案为设备标识和安全密钥提供安全存储。 可将单个或一组设备标识添加到允许列表或方块列表，以便完全控制设备访问。 以下文章提供有关标识注册表的结构和受支持操作的详细信息。

[IoT 中心支持 MQTT、AMQP 和 HTTP 等协议](../articles//iot-hub/iot-hub-devguide-security.md)。 每个协议使用 IoT 设备到 IoT 中心的安全令牌的方式不同：

* AMQP：基于 SASL PLAIN 和 AMQP 声明的安全性（若是 IoT 中心级别令牌，则为 `{policyName}@sas.root.{iothubName}`；若是设备范围令牌，则为 `{deviceId}`）。

* MQTT：CONNECT 数据包使用 `{deviceId}` 作为 `{ClientId}`、“用户名”  字段中的 `{IoThubhostname}/{deviceId}` 以及“密码”  字段中的 SAS 令牌。

* HTTP：有效令牌位于授权请求标头中。

IoT 中心标识注册表可用于配置每个设备的安全凭据和访问控制。 但是，如果 IoT 解决方案已大幅投资于[自定义设备标识注册表和/或身份验证方案](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)，则可通过创建令牌服务，将该解决方案集成到具有 IoT 中心的现有基础结构中。

### <a name="x509-certificate-based-device-authentication"></a>基于 X.509 证书的设备身份验证

使用[基于设备的 X.509 证书](../articles/iot-hub/iot-hub-devguide-security.md)及其关联的私钥和公钥允许在物理层进行其他身份验证。 私钥安全存储在设备中，无法在设备外发现。 X.509 证书包含有关设备的信息（例如设备 ID）以及其他组织详细信息。 使用公钥生成证书签名。

高级设备预配流：

* 将标识符关联到物理设备 - 设备制造或调试过程中将设备标识和/或 X.509 证书关联到设备。

* 在 IoT 中心创建对应的标识条目 - IoT 中心标识注册表中的设备标识和关联的设备信息。

* 将 X.509 证书指纹安全存储在 IoT 中心标识注册表中。

### <a name="root-certificate-on-device"></a>设备上的根证书

与 IoT 中心建立安全的 TLS 连接时，IoT 设备使用作为设备 SDK 的一部分的根证书验证 IoT 中心。 对于 C 客户端 SDK，该证书位于存储库根下的“\\c\\certs”文件夹中。 虽然这些根证书长期有效，但仍可能过期或被撤销。 如果无法更新设备上的证书，该设备随后可能无法连接到 IoT 中心（或任何其他云服务）。 IoT 设备部署后提供更新根证书的方法可有效减轻此风险。

## <a name="securing-the-connection"></a>保护连接安全

使用传输层安全性 (TLS) 标准来保护 IoT 设备和 IoT 中心之间的 Internet 连接安全。 Azure IoT 支持 [TLS 1.2](https://tools.ietf.org/html/rfc5246)、TLS 1.1 和 TLS 1.0（按此顺序）。 对 TLS 1.0 的支持仅为向后兼容性提供。 如果可能，请使用 TLS 1.2，因为它可提供最大安全性。

## <a name="securing-the-cloud"></a>保护云的安全

Azure IoT 中心允许为每个安全密钥定义[访问控制策略](../articles/iot-hub/iot-hub-devguide-security.md)。 它使用以下一组权限向每个 IoT 中心的终结点授予访问权限。 权限可根据功能限制对 IoT 中心的访问。

* **RegistryRead**。 授予对标识注册表的读取访问权限。 有关详细信息，请参阅[标识注册表](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。

* **RegistryReadWrite**。 授予对标识注册表的读取和写入访问权限。 有关详细信息，请参阅[标识注册表](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。

* **ServiceConnect**。 授予对面向云服务的通信和监视终结点的访问权限。 例如，它授权后端云服务接收设备到云的消息、发送云到设备的消息，以及检索对应的传送确认。

* **DeviceConnect**。 授予对面向设备的终结点的访问权限。 例如，它授予发送设备到云的消息和接收云到设备的消息的权限。 此权限由设备使用。

有两种方法可以使用[安全令牌](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)来获取 IoT 中心的 **DeviceConnect** 权限：使用设备标识密钥，或者使用共享访问密钥。 此外，必须注意的是，可从设备访问的所有功能都故意显示在前缀为 `/devices/{deviceId}` 的终结点上。

[服务组件只能使用共享访问策略生成安全令牌](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components)，授予适当权限。

Azure IoT 中心和其他可能是解决方案的一部分的服务允许使用 Azure Active Directory 管理用户。

Azure IoT 中心引入的数据可供多种服务（例如 Azure 流分析、Azure Blob 存储等）使用。 这些服务允许管理访问权限。 了解有关这些服务和可用选项的详细信息：

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)：适用于半结构化数据的可缩放且已完全编制索引的数据库服务，可管理预配的设备的元数据，例如，属性、配置和安全属性。 Azure Cosmos DB 提供高性能和高吞吐量处理、与架构无关的数据索引，以及丰富的 SQL 查询接口。

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/):通过云中处理的实时流可以快速开发和部署低成本分析解决方案，以便从设备、传感器、基础结构和应用程序实时获取深入了解。 来自这种完全托管服务的数据可缩放为任何数量，同时保持高吞吐量、低延迟和复原能力。

* [在 azure 应用服务](https://azure.microsoft.com/services/app-service/):一个云平台，用以构建能够连接到任何地方（在云中或本地）的数据的强大 Web 和移动应用。 构建具有吸引力的 iOS、Android 和 Windows 移动应用。 与软件即服务 (SaaS) 和企业应用程序相集成，这些应用程序一经使用便可直接连接到数十种基于云的服务和企业应用程序。 使用偏好的语言和 IDE（.NET、Node.js、PHP、Python 或 Java）进行编码，比以往更快地构建 Web 应用和 API。

* [逻辑应用](https://azure.microsoft.com/services/app-service/logic/):Azure 应用服务的逻辑应用功能可帮助用户将 IoT 解决方案集成到现有业务线系统并自动执行工作流程。 逻辑应用可让开发人员设计从触发过程开始，并运行一系列步骤的工作流 — 使用功能强大的连接器来与业务过程集成的规则和操作。 Logic Apps 提供与 SaaS、基于云和本地应用程序的广泛生态系统的实时连接。

* [Azure Blob 存储](https://azure.microsoft.com/services/storage/):可靠且符合经济效益的云存储，适用于设备要发送到云的数据。

## <a name="conclusion"></a>结束语

本文概述了使用 Azure IoT 来设计和部署 IoT 基础结构的实现级别的详细信息。 将每个组件配置为安全状态是保护 IoT 总体基础结构安全的关键。 Azure IoT 中可用的设计选择提供了一定程度的灵活性和选择性；但是，每个选择都可能具有安全隐患。 推荐通过风险/成本评估对这些选择进行评估。
