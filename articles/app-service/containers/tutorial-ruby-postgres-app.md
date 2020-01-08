---
title: 在 Linux 上将 Ruby (Rails) 与 Postgres 配合使用 - Azure 应用服务 | Microsoft Docs
description: 了解如何创建一个可在 Azure 中运行的 Ruby 应用，并将其连接到 PostgreSQL 数据库。 本教程中使用 Rails。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 3ec19b1c564c09406ab1f29c38aef6332d80f8f1
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544682"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>在基于 Linux 上的 Azure 应用服务中生成 Ruby 和 Postgres 应用

[Linux 应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本教程介绍如何创建 Ruby 应用，并将其连接到 PostgreSQL 数据库。 完成本教程后，Linux 上的应用服务将会运行一个 [Ruby on Rails](https://rubyonrails.org/) 应用。

![Azure 应用服务中运行的 Ruby on Rails 应用](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 中创建 PostgreSQL 数据库
> * 将 Ruby on Rails 应用连接到 PostgreSQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

* [安装 Git](https://git-scm.com/)
* [安装 Ruby 2.3](https://www.ruby-lang.org/en/documentation/installation/)
* [安装 Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [安装并运行 PostgreSQL](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>准备本地 Postgres

此步骤在本地 Postgres 服务器中创建一个数据库，以便在本教程中使用。

### <a name="connect-to-local-postgres-server"></a>连接到本地 Postgres 服务器

打开终端窗口，并运行 `psql` 以连接到本地 Postgres 服务器。

```bash
sudo -u postgres psql
```

如果连接成功，则表示 Postgres 数据库已在运行。 否则，应确保按照[下载 - PostgreSQL Core 发行版](https://www.postgresql.org/download/)中的步骤启用本地 Postgres 数据库。

键入 `\q` 退出 Postgres 客户端。 

使用登录的 Linux 用户名，创建一个可以通过运行以下命令来创建数据库的 Postgres 用户。

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>在本地创建 Ruby on Rails 应用
此步骤创建一个 Ruby on Rails 示例应用程序、配置其数据库连接，并在本地运行该应用程序。 

### <a name="clone-the-sample"></a>克隆示例

在终端窗口中，通过 `cd` 转到工作目录。

运行下列命令以克隆示例存储库。

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

通过 `cd` 转到克隆目录。 安装所需程序包。

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>在本地运行示例

运行 [Rails 迁移](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations)以创建应用程序所需的表。 若要查看迁移中创建了哪些表，请查看 Git 存储库中的 _db/migrate_ 目录。

```bash
rake db:create
rake db:migrate
```

运行应用程序。

```bash
rails server
```

在浏览器中导航至 `http://localhost:3000` 。 在页面中添加一些任务。

![Ruby on Rails 已成功连接到 Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

若要停止 Rails 服务器，请在终端中键入 `Ctrl + C`。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>在 Azure 中创建 Postgres

此步骤在 [Azure Database for PostgreSQL](/azure/postgresql/) 中创建一个 Postgres 数据库。 稍后需要将 Ruby on Rails 应用程序配置为连接到此数据库。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>创建一个 Postgres 服务器

使用 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 命令创建 PostgreSQL 服务器。

在 Cloud Shell 中运行以下命令，使用唯一的服务器名称来替换 *\<postgres-server-name>* 占位符。 服务器名称在 Azure 中的所有服务器中必须是唯一的。 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres-server-name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

创建用于 PostgreSQL 的 Azure 数据库服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres-server-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres-server-name>",
  "location": "westeurope",
  "name": "<postgres-server-name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>配置服务器防火墙

在 Cloud Shell 中，使用 [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create) 命令创建 Postgres 服务器的防火墙规则，以便建立客户端连接。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。 使用唯一的服务器名称来替换 *\<postgres-server-name>* 占位符。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres-server-name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!TIP] 
> 你甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)。
>

### <a name="connect-to-production-postgres-server-locally"></a>在本地连接到生产 Postgres 服务器

在 Cloud Shell 中，连接到 Azure 中的 Postgres 服务器。 使用前面为 _&lt;postgres-server-name>_ 占位符指定的值。

```bash
psql -U adminuser@<postgres-server-name> -h <postgres-server-name>.postgres.database.azure.com postgres
```

当提示输入密码时，请使用在创建数据库服务器时指定的 _My5up3r$tr0ngPa$w0rd!_。

### <a name="create-a-production-database"></a>创建生产数据库

在 `postgres` 提示中创建数据库。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>创建具有权限的用户

创建一个名为 _railsappuser_ 的数据库用户并向其授予 `sampledb` 数据库中的所有特权。

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017';
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

键入 `\q` 退出服务器连接。

## <a name="connect-app-to-azure-postgres"></a>将应用连接到 Azure Postgres

此步骤将 Ruby on Rails 应用程序连接到在 Azure Database for PostgreSQL 中创建的 Postgres 数据库。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>配置数据库连接

在存储库中，打开 _config/database.yml_。 在该文件的底部，将生产变量替换为以下代码。 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

保存更改。

### <a name="test-the-application-locally"></a>在本地测试应用程序

回到本地终端，设置以下环境变量：

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres-server-name>
export DB_PASSWORD=MyPostgresAzure2017
```

使用刚刚配置的生产值运行 Rails 数据库迁移，在 Azure Database for PostgreSQL 中的 Postgres 数据库内创建表。

```bash
rake db:migrate RAILS_ENV=production
```

在生产环境中运行时，Rails 应用程序需要预编译的资产。 使用以下命令生成所需的资产：

```bash
rake assets:precompile
```

Rails 生产环境还使用机密来管理安全性。 生成机密密钥

```bash
rails secret
```

将机密密钥保存到 Rails 生产环境使用的相应变量。 为方便起见，可对两个变量使用相同的密钥。

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

启用 Rails 生产环境，以提供 JavaScript 和 CSS 文件。

```bash
export RAILS_SERVE_STATIC_FILES=true
```

在生产环境中运行示例应用程序。

```bash
rails server -e production
```

导航到 `http://localhost:3000`。 如果页面可加载且未出错，则表示 Ruby on Rails 应用程序正在连接到 Azure 中的 Postgres 数据库。

在页面中添加一些任务。

![Ruby on Rails 已成功连接到 Azure Database for PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

若要停止 Rails 服务器，请在终端中键入 `Ctrl + C`。

### <a name="commit-your-changes"></a>提交更改

运行以下的 Git 命令，提交更改：

```bash
git add .
git commit -m "database.yml updates"
```

应用已可用于部署。

## <a name="deploy-to-azure"></a>“部署到 Azure”

此步骤将已连接 Postgres 的 Rails 应用程序部署到 Azure 应用服务。

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>配置数据库设置

在应用服务的 Cloud Shell 中，使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令将环境变量设置为应用设置。

以下 Cloud Shell 命令配置应用设置 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME` 和 `DB_PASSWORD`。 替换占位符 _&lt;appname>_ 和 _&lt;postgres-server-name>_。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres-server-name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>配置 Rails 环境变量

在本地终端中，为 Azure 中的 Rails 生产环境[生成新的机密](configure-language-ruby.md#set-secret_key_base-manually)。

```bash
rails secret
```

配置 Rails 生产环境所需的变量。

在以下 Cloud Shell 命令中，将两个 _&lt;output-of-rails-secret>_ 占位符替换在本地终端中生成的新机密密钥。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` 告知默认 Ruby 容器要在每次进行 Git 部署时预编译资产。 有关详细信息，请参阅[预编译资产](configure-language-ruby.md#precompile-assets)和[提供静态资产](configure-language-ruby.md#serve-static-assets)。

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

在本地终端中，将 Azure 远程功能添加到本地 Git 存储库。

```bash
git remote add azure <paste-copied-url-here>
```

推送到 Azure 远程功能以部署 Ruby on Rails 应用程序。 系统会提示输入前面在创建部署用户期间提供的密码。

```bash
git push azure master
```

在部署期间，Azure 应用服务会向 Git 告知其进度。

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-app"></a>浏览到 Azure 应用

浏览到 `http://<app-name>.azurewebsites.net` 并在列表中添加一些任务。

![Azure 应用服务中运行的 Ruby on Rails 应用](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

恭喜，你已在 Azure 应用服务中运行了一个数据驱动的 Ruby 应用。

## <a name="update-model-locally-and-redeploy"></a>在本地更新模型和重新部署

在此步骤中，对 `task` 数据模型和 webapp 进行简单更改，然后将更新发布到 Azure。

对于任务方案，需要修改应用程序，以便能够将任务标记为已完成。

### <a name="add-a-column"></a>添加列

在终端中，导航到 Git 存储库中的根路径。

生成新的迁移，用于将名为 `Done` 的布尔列添加到 `Tasks` 表：

```bash
rails generate migration AddDoneToTasks Done:boolean
```

此命令在 _db/migrate_ 目录中生成新的迁移文件。


在终端中运行 Rails 数据库迁移，以便在本地数据库中进行更改。

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>更新应用程序逻辑

打开 *app/controllers/tasks_controller.rb* 文件。 在该文件的末尾找到以下行：

```rb
params.require(:task).permit(:Description)
```

修改此行，以包含新的 `Done` 参数。

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>更新视图

打开 *app/views/tasks/_form.html.erb* 文件，即“编辑”窗体。

找到 `<%=f.error_span(:Description) %>` 行，并紧接在其下方插入以下代码：

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

打开 *app/views/tasks/show.html.erb* 文件，即单记录“视图”页。 

找到 `<dd><%= @task.Description %></dd>` 行，并紧接在其下方插入以下代码：

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

打开 *app/views/tasks/index.html.erb* 文件，即所有记录的“索引”页。

找到 `<th><%= model_class.human_attribute_name(:Description) %></th>` 行，并紧接在其下方插入以下代码：

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

在同一文件中找到 `<td><%= task.Description %></td>` 行，并紧接在其下方插入以下代码：

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>在本地测试更改

在本地终端中运行 Rails 服务器。

```bash
rails server
```

若要查看任务状态的变化，请导航到 `http://localhost:3000` 并添加或编辑项。

![将复选框添加到任务](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

若要停止 Rails 服务器，请在终端中键入 `Ctrl + C`。

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

在终端中，针对生产环境运行 Rails 数据库迁移，在 Azure 数据库中进行更改。

```bash
rake db:migrate RAILS_ENV=production
```

提交在 Git 中进行的所有更改，然后将代码更改推送到 Azure。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push` 完成后，请导航至 Azure 应用，测试新功能。

![发布到 Azure 的模型和数据库更改](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

如果添加任何任务，则它们保留在数据库中。 更新数据架构不会改变现有数据。

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>管理 Azure 应用

转到 [Azure 门户](https://portal.azure.com)管理已创建的应用。

在左侧菜单中单击“应用程序服务”，然后单击 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/tutorial-php-mysql-app/access-portal.png)

这里我们可以看到应用的“概述”页。 在此处可以执行基本的管理任务，例如停止、启动、重启、浏览和删除。

左侧菜单提供用于配置应用的页面。

![Azure 门户中的应用服务页](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 中创建一个 Postgres 数据库
> * 将 Ruby on Rails 应用连接到 Postgres
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

继续学习下一篇教程，了解如何将自定义 DNS 名称映射到应用。

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](../app-service-web-tutorial-custom-domain.md)

或者，查看其他资源：

> [!div class="nextstepaction"]
> [配置 Ruby 应用](configure-language-ruby.md)