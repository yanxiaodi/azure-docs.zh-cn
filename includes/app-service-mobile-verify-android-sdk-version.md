---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173465"
---
由于 Android SDK 的持续开发，安装在 Android Studio 中的 Android SDK 版本可能与代码中的版本不匹配。 本教程中引用的 Android SDK 是版本 26，是撰写本教程时的最新版本。 随着 SDK 的新版本问世，版本号也会随之增加，建议使用最新版本。

版本不匹配的两种表现是：

- 生成或重新生成项目时，可能会收到类似 `Gradle sync failed: Failed to find target with hash string 'android-XX'` 的 Gradle 错误消息。
- 应该基于 `import` 语句解析的代码中的标准 Android 对象可能产生错误消息。

如果出现上述任何一种情况，说明在 Android Studio 中安装的 Android SDK 版本与已下载项目的 SDK 目标可能不匹配。 若要验证版本，请进行以下更改：

1. 在 Android Studio 中，单击“工具” > “Android” > “SDK Manager”。    如果尚未安装最新版本的 SDK 平台，请单击安装。 记下版本号。

2. 在“项目资源管理器”  选项卡的“Gradle 脚本”  下，打开文件 build.gradle (Module: app)  。 确保 compileSdkVersion  和 targetSdkVersion  设置为安装的最新 SDK 版本。 `build.gradle` 可能如下所示：

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
