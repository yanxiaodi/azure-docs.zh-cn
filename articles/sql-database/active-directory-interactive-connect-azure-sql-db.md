---
title: ActiveDirectoryInteractive 连接到 SQL | Microsoft Docs
description: 使用 SqlAuthenticationMethod.ActiveDirectoryInteractive 模式连接到 Azure SQL 数据库的 C# 代码示例（附带说明）。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
ms.openlocfilehash: 4f36dcc9953134ac5dd24d3d762ac0dde9949ab7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561373"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>通过 Azure 多重身份验证连接到 Azure SQL 数据库

本文提供连接到C# Azure SQL 数据库的程序。 此程序使用支持[Azure 多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)的交互式模式身份验证。

有关对 SQL 工具的多重身份验证支持的详细信息, 请参阅[SQL Server Data Tools 中的 Azure Active Directory 支持 (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory)。

## <a name="multi-factor-authentication-for-azure-sql-database"></a>适用于 Azure SQL 数据库的多重身份验证

从 .NET Framework 版本4.7.2 开始, 枚举[`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod)具有新值:。 `ActiveDirectoryInteractive` 在客户端C#程序中, 枚举值指示系统使用支持多重身份验证的 Azure Active Directory (Azure AD) 交互模式来连接到 Azure SQL 数据库。 然后，运行该程序的用户将看到以下对话框：

* 一个显示 Azure AD 用户名并要求输入用户密码的对话框。

   如果用户的域与 Azure AD 联合, 则不会显示此对话框, 因为无需密码。

   如果 Azure AD 策略对用户施加多重身份验证, 则会显示接下来的两个对话框。

* 用户第一次执行多重身份验证时, 系统会显示一个对话框, 要求提供用于发送短信的移动电话号码。 每条短信提供了用户必须在下一对话框中输入的验证码。

* 要求提供多重身份验证验证码 (系统已将系统发送到移动电话) 的对话框。

有关如何配置 Azure AD 以要求多重身份验证的信息, 请参阅[云中的 Azure 多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)入门。

有关这些对话框的屏幕截图, 请参阅[为 SQL Server Management Studio 和 Azure AD 配置多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。

> [!TIP]
> 可以通过[.NET API 浏览器工具页](https://docs.microsoft.com/dotnet/api/)搜索 .NET Framework api。
>
> 你还可以直接搜索可选的["字词 =&lt;搜索&gt;值参数](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)"。

## <a name="configure-your-c-application-in-the-azure-portal"></a>在 Azure 门户中配置 C# 应用程序

在开始之前，[Azure SQL 数据库服务器](sql-database-get-started-portal.md)应已创建且可用。

### <a name="register-your-app-and-set-permissions"></a>注册应用并设置权限

若要使用 Azure AD 身份验证，必须将 C# 程序注册为 Azure AD 应用程序。 只有 Azure AD 管理员或者分配有 Azure AD“应用程序开发人员”角色的用户才能注册应用。 有关分配角色的详细信息, 请参阅向[具有 Azure Active Directory 的用户分配管理员和非管理员角色](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

完成应用注册后, 将生成并显示**应用程序 ID**。 程序中必须包含此 ID 才能建立连接。

注册应用程序并为其设置所需的权限：

1. 在 Azure 门户中，依次选择 Azure Active Directory > “应用注册” > “新应用程序注册”。

    ![应用注册](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    创建应用注册后, 将生成并显示**应用程序 ID**值。

    ![显示的应用 ID](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2.  > 选择 "**所需**权限""添加" > 。 > 

    ![已注册应用的权限设置](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. 选择 "**所需权限** > " "**添加** > " "**选择 API** > " "**Azure SQL 数据库**"。

    ![为 Azure SQL 数据库添加对 API 的访问](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. 选择 " **API 访问** > " "**选择权限** > **委托权限**"。

    ![为 Azure SQL 数据库委托对 API 的权限](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>为 SQL 数据库服务器设置 Azure AD 管理员

要使C#程序运行, Azure sql server 管理员需要为 SQL 数据库服务器指定 Azure AD 管理员。 

在 " **SQL Server** " 页上, 选择 " **Active Directory 管理员** > **集管理员**"。

有关 Azure SQL 数据库 Azure AD 管理员和用户的详细信息, 请参阅[配置和管理 Azure Active Directory 身份验证和 SQL 数据库](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)中的屏幕截图。

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>将非管理员用户添加到特定的数据库（可选）

SQL 数据库服务器的 Azure AD 管理员可以运行 C# 示例程序。 数据库中的 Azure AD 用户可以运行该程序。 已在数据库中并对该数据库拥有 `ALTER ANY USER` 权限的 Azure AD SQL 管理员或 Azure AD 用户可以添加用户。

可以使用 SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 命令将用户添加到数据库。 例如 `CREATE USER [<username>] FROM EXTERNAL PROVIDER`。

有关详细信息，请参阅[将 Azure Active Directory 身份验证与 SQL 数据库、托管实例或 SQL 数据仓库结合使用](sql-database-aad-authentication.md)。

## <a name="new-authentication-enum-value"></a>新的身份验证枚举值

该 C# 示例依赖于 [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) 命名空间。 对于多重身份验证, 特别感兴趣的是枚举`SqlAuthenticationMethod`, 它具有以下值:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   将此值与 Azure AD 的用户名结合使用, 以实现多重身份验证。 此值是本文的重点。 它通过显示用户密码的对话框来生成交互式体验, 然后在此用户上施加多重身份验证验证的情况下进行多重身份验证验证。 此值自 .NET Framework 版本 4.7.2 起提供。

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  对联合帐户使用此值。 对于联合帐户，Windows 域已知用户名。 此身份验证方法不支持多重身份验证。

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  使用此值进行需要 Azure AD 用户名和密码的身份验证。 Azure SQL 数据库执行身份验证。 此方法不支持多重身份验证。

## <a name="set-c-parameter-values-from-the-azure-portal"></a>在 Azure 门户中设置 C# 参数值

要使 C# 程序成功运行，需将适当的值分配到静态字段。 此处显示了包含示例值的字段。 还显示了 Azure 门户位置, 你可以在其中获取所需的值。

| 静态字段名称 | 示例值 | Azure 门户中的位置 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | “SQL 服务器” > “按名称筛选” |
| AzureAD_UserID | "用户名\@abc.onmicrosoft.com" | “Azure Active Directory” > “用户” > “新建来宾用户” |
| Initial_DatabaseName | "myDatabase" | “SQL 服务器” > “SQL 数据库” |
| ClientApplicationID | “a94f9c62-97fe-4d19-b06d-111111111111” | “Azure Active Directory” > “应用注册” > “按名称搜索” > “应用程序 ID” |
| RedirectUri | new Uri("https://mywebserver.com/") |  > **按名称** **Azure Active Directory 应用注册** *搜索 [应用注册*] > 设置 RedirectURIs >  >  > <br /><br />对于本文, 任何有效的值都适用于 RedirectUri, 因为此处没有使用它。 |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>验证 SQL Server Management Studio

在运行C#程序之前, 最好检查 SQL SERVER MANAGEMENT STUDIO (SSMS) 中的设置和配置是否正确。 然后，可将任何 C# 程序错误范围缩小到源代码级别。

### <a name="verify-sql-database-firewall-ip-addresses"></a>验证 SQL 数据库防火墙 IP 地址

从同一建筑物中你要运行 C# 程序的同一台计算机运行 SSMS。 对于此测试, 任何**身份验证**模式都是正常的。 如果有任何迹象表明数据库服务器防火墙不接受 IP 地址，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-firewall-configure.md)获得帮助。

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>验证 Azure Active Directory 多重身份验证

再次运行 SSMS，这一次将“身份验证”设为“具有 MFA 支持的 Active Directory - 通用”。 此选项需要 SSMS 17.5 或更高版本。

有关详细信息, 请参阅[为 SSMS 和 Azure AD 配置多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。

> [!NOTE]
> 如果你是数据库中的来宾用户, 还需要为数据库提供 Azure AD 域名:选择**选项** > **AD 域名或租户 ID**。 若要在 Azure 门户中查找域名，请选择“Azure Active Directory” > “自定义域名”。 在 C# 示例程序中，不必要提供域名。

## <a name="c-code-example"></a>C# 代码示例

示例 C# 程序依赖于 [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL 程序集。

若要安装此包，请在 Visual Studio 中选择“项目” > “管理 NuGet 包”。 搜索并安装 **Microsoft.IdentityModel.Clients.ActiveDirectory**。

这是源代码的C#示例。

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

这是C#测试输出的示例。

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
