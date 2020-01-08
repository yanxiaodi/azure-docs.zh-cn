---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b150cad22528234286fa7939bf7055e8312ed361
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229214"
---
## <a name="webapi-project"></a>WebAPI 项目
1. 在 Visual Studio 中，打开在**通知用户**教程中创建的 **AppBackend** 项目。
2. 在 Notifications.cs 中，将整个 Notifications 类替换为以下代码  。 请确保将占位符替换为通知中心的连接字符串（具有完全访问权限）和中心名称。 可以从 [Azure 门户](https://portal.azure.com)获取这些值。 现在，该模块将表示要发送的其他安全通知。 在完整的实现中，通知将存储在数据库中；为简单起见，在此示例中我们将它们存储在内存中。
   
        public class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
            }

            public Notification CreateNotification(string payload)
            {
                var notification = new Notification() {
                Id = notifications.Count,
                Payload = payload,
                Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Notification ReadNotification(int id)
            {
                return notifications.ElementAt(id);
            }
        }

1. 在 NotificationsController.cs 中，将 NotificationsController 类定义中的代码替换为以下代码  。 该组件为设备实现了一种安全检索通知的方法，还提供了一种方法来触发到设备的安全推送（用于本教程的教学目的）。 请注意，在向通知中心发送通知时，我们将只发送一个包含通知 ID（且没有实际的消息内容）的原始通知：
   
       public NotificationsController()
       {
           Notifications.Instance.CreateNotification("This is a secure notification!");
       }
   
       // GET api/notifications/id
       public Notification Get(int id)
       {
           return Notifications.Instance.ReadNotification(id);
       }
   
       public async Task<HttpResponseMessage> Post()
       {
           var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
           var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
           // windows
           var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                           new Dictionary<string, string> {
                               {"X-WNS-Type", "wns/raw"}
                           });
           await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);
   
           // apns
           await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);
   
           // gcm
           await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }


请注意，`Post` 方法现在不发送 toast 通知。 它将发送只包含通知 ID 且没有任何敏感内容的原始通知。 另外，请确保注释在通知中心上未配置其凭据的平台的发送操作，因为它们会导致错误。

1. 现在，我们将此应用重新部署到 Azure 网站，以便可以从所有设备对其进行访问。 右键单击 **AppBackend** 项目，并选择“发布”  。
2. 选择 Azure 网站作为发布目标。 使用 Azure 帐户登录，选择现有网站或新网站，并记下“连接”  选项卡中的“目标 URL”  属性。在本教程后面的部分中，会将此 URL 称为“后端终结点”  。 单击“发布”  。

