---
title: HDInsight 中的 Azure Active Directory 企业安全性套餐
description: 了解如何使用 Azure Active Directory 域服务设置和配置 HDInsight 企业安全性套餐群集。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: aa18c4a078edf579e8d9c4c09df99100dfcea148
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918306"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>在 HDInsight 中企业安全性套餐配置与 Azure Active Directory 域服务

企业安全性套餐 (ESP) 群集在 Azure HDInsight 群集上提供多用户访问权限。 具有 ESP 的 HDInsight 群集连接到域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。

本文介绍如何使用 Azure Active Directory 域服务 (Azure AD-DS) 配置具有 ESP 的 HDInsight 群集。

> [!NOTE]  
> ESP 在 HDInsight 3.6 和4.0 中已正式发布，适用于群集类型：Apache Spark、交互式、Hadoop 和 HBase。 适用于 Apache Kafka 群集类型的 ESP 仅限预览，并且仅支持最大努力。 在 ESP GA 日期（2018年10月1日）之前创建的 ESP 群集不受支持。

## <a name="enable-azure-ad-ds"></a>启用 Azure AD-DS

> [!NOTE]  
> 只有租户管理员有权启用 Azure AD-DS。 如果群集存储是 Azure Data Lake Storage （ADLS） Gen1 或 Gen2，则必须仅针对将需要使用基本 Kerberos 身份验证来访问群集的用户禁用多重身份验证（MFA）。 你可以使用 "[受信任的 ip](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) " 或 "[条件性访问](../../active-directory/conditional-access/overview.md)"，仅在访问 HDInsight 群集 VNET IP 范围时才为特定用户禁用 MFA。 如果使用条件访问，请确保在 HDInsight VNET 中启用 AD 服务终结点。
>
> 如果群集存储是 Azure Blob 存储 (WASB)，请不要禁用 MFA。

要想能够创建具有 ESP 的 HDInsight 群集，必须先启用 Azure AD-DS。 有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance.md)。 

默认情况下，启用 Azure AD-DS 后，所有用户和对象就开始从 Azure Active Directory (AAD) 同步到 Azure AD-DS。 同步操作的时长取决于 Azure AD 中对象的数目。 如果对象数以十万记，则同步可能需要数天。 

与 HDInsight 配合使用时，用于 Azure AD-DS 的域名必须等于或小于39个字符。

你可以选择只同步需要访问 HDInsight 群集的组。 这种仅同步特定组的选项称为“范围有限的同步”。 有关说明，请参阅 [Configure Scoped Synchronization from Azure AD to your managed domain](../../active-directory-domain-services/scoped-synchronization.md)（配置从 Azure AD 到托管域的范围有限的同步）。

启用安全 LDAP 时，请将域名置于使用者名称，并将使用者可选名称置于证书中。 例如，如果域名为 *contoso100.onmicrosoft.com*，请确保证书所有者名称和使用者可选名称中存在完全匹配的名称。 有关详细信息，请参阅[为 Azure AD-DS 托管域配置安全 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md)。 下面是创建自签名证书的示例，其中的使用者名称和 DnsName（使用者可选名称）都包含域名 (*contoso100.onmicrosoft.com*)：

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>查看 Azure AD-DS 运行状况
在“管理”类别下选择“运行状况”，查看 Azure Active Directory 域服务的运行状况。 确保 Azure AD-DS 的状态为绿色（正在运行），且同步已完成。

![Azure Active Directory 域服务运行状况](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>创建并授权托管标识

**用户分配的托管标识**用于简化和保护域服务操作。 为托管标识分配 HDInsight 域服务参与者角色后，它可以读取、创建、修改和删除域服务操作。 HDInsight 企业安全性套餐需要某些域服务操作（例如创建 Ou 和服务主体）。 可以在任何订阅中创建托管标识。 有关一般托管标识的详细信息，请参阅[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 有关 Azure HDInsight 中托管标识的工作方式的详细信息，请参阅[Azure hdinsight 中的托管标识](../hdinsight-managed-identities.md)。

若要设置 ESP 群集，请创建用户分配的托管标识（如果还没有）。 有关说明，请参阅[使用 Azure 门户创建、列出和删除用户分配的托管标识以及为其分配角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 接下来，为托管标识分配 Azure AD-DS 访问控制中的“HDInsight 域服务参与者”角色（需要 AAD-DS 管理员权限来执行此角色分配）。

![Azure Active Directory 域服务访问控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

分配“HDInsight 域服务参与者”角色可确保此标识具有适当的（代理）访问权限，可以在 AAD-DS 域中执行创建 OU、删除 OU 等域服务操作。

一旦创建托管标识并提供正确的角色，AAD-DS 管理员就可以设置可使用此托管标识的用户。 若要设置托管标识用户，管理员应选择门户中的托管标识，然后单击“概述”下的“访问控制 (IAM)”。 然后，在右侧，为要创建 HDInsight ESP 群集的用户或组指定“托管标识操作员”角色。 例如，AAD DS 管理员可以将此角色分配给**sjmsi**托管标识的**MarketingTeam**组，如下图所示。 这可以确保组织中的适当人员有权使用此托管标识来创建 ESP 群集。

![HDInsight 托管标识操作者角色分配](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>网络注意事项

> [!NOTE]  
> Azure AD-必须将 DS 部署到基于 Azure 资源管理器的 vNET 中。 Azure AD-DS 不支持经典虚拟网络。 有关更多详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance.md#create-and-configure-the-virtual-network)。

启用 Azure AD-DS 后，本地域名服务 (DNS) 服务器将在 AD 虚拟机 (VM) 上运行。 配置 Azure AD-DS 虚拟网络 (VNET) 来使用这些自定义 DNS 服务器。 若要找到正确的 IP 地址，请选择“管理”类别下的“属性”，然后查看“虚拟网络上的 IP 地址”下列出的 IP 地址。

![找到本地 DNS 服务器的 IP 地址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

选择“设置”类别下的“DNS 服务器”，更改 Azure AD-DS VNET 中的 DNS 服务器配置以使用这些自定义 IP。 然后单击“自定义”旁边的单选按钮，在下面的文本框中输入第一个 IP 地址，然后单击“保存”。 使用相同步骤添加其他 IP 地址。

![更新 VNET DNS 配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

将 Azure AD-DS 实例和 HDInsight 群集放在同一 Azure 虚拟网络中会更方便。 如果打算使用不同的 VNET，必须使这些虚拟网络对等，以便域控制器对 HDI VM 可见。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。 

VNET 对等后，配置 HDInsight VNET 以使用自定义 DNS 服务器并输入 Azure AD-DS 专用 IP 作为 DNS 服务器地址。 当两个 VNET 都使用相同的 DNS 服务器，自定义域名将解析为正确的 IP 并可从 HDInsight 进行访问。 例如，如果你的域名`contoso.com`在此步骤之后， `ping contoso.com`应解析为正确的 Azure AD DS IP。

![为对等 VNET 配置自定义 DNS 服务器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

如果在 HDInsight 子网中使用网络安全组 (NSG) 规则，应允许入站和出站流量[所需的 IP](../hdinsight-management-ip-addresses.md)。 

若要测试网络连接设置是否正确，将 windows VM 加入到 HDInsight VNET/子网并对域名执行 ping 操作（它应解析为 IP），然后运行 ldp.exe 以访问 Azure AD-DS 域。 然后将此 windows VM 加入到域以确认客户端和服务器之间所有所需的 RPC 调用均已成功。 此外可以使用 nslookup 来确认对存储帐户或任何可能使用的外部数据库（例如，外部 Hive 元存储或 Ranger DB）的网络访问。
如果 AAD-DS 由 NSG 提供保护，应确保所有[所需的端口](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)均在 AAD-DS 子网网络安全组规则的允许列表中。 如果此 windows VM 的域加入操作成功，则可以继续执行下一步以创建 ESP 群集。

## <a name="create-a-hdinsight-cluster-with-esp"></a>创建具有 ESP 的 HDInsight 群集

正确设置前面的步骤后，下一步是创建启用了 ESP 的 HDInsight 群集。 创建 HDInsight 群集后，可以在“自定义”选项卡中启用企业安全性套餐。如果想要使用 Azure 资源管理器模板进行部署，使用门户体验一次，并下载最后“摘要”页上的预填写模板，供将来重复使用。

> [!NOTE]  
> ESP 群集名称的前六个字符在环境中必须是唯一的。 例如，如果在不同 VNET 中有多个 ESP 群集，则应选择一个命名约定，该命名约定确保群集名称上的前六个字符是唯一的。

![Azure HDInsight 企业安全性套餐域验证](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

启用 ESP 后，将自动检测与 Azure AD-DS 相关的常见错误配置并对其进行验证。 修复这些错误后，你可以继续下一步： 

![Azure HDInsight 企业安全性套餐域验证失败](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

创建具有 ESP 的 HDInsight 群集时，必须提供以下参数：

- **群集管理员用户**：从同步的 Azure AD-DS 中选择群集的管理员。 此域帐户必须已同步并在 Azure AD-DS 中可用。

- **群集访问组**：要同步其用户且有权访问群集的安全组应该在 Azure AD-DS 中可用。 例如，HiveUsers 组。 有关详细信息，请参阅[在 Azure Active Directory 中创建组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

- **LDAPS URL**：例如 `ldaps://contoso.com:636`。

以下屏幕截图显示了 Azure 门户中的成功配置：

![Azure HDInsight ESP Active Directory 域服务配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

创建新群集时，已创建的托管标识可以从用户分配的托管标识下拉列表中选择。

![Azure HDInsight ESP Active Directory 域服务托管标识](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).

## <a name="next-steps"></a>后续步骤

* 有关如何配置 Hive 策略和运行 Hive 查询的信息，请参阅[为具有 ESP 的 HDInsight 群集配置 Apache Hive 策略](apache-domain-joined-run-hive.md)。
* 有关如何使用 SSH 连接到具有 ESP 的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Apache Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。
