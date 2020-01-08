---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485037"
---
1. 启动 Android Studio，然后在“欢迎”窗口中选择“启动新的 Android Studio 项目”  。

    ![Android Studio 的“欢迎”窗口的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. 此时会出现“选择项目”  向导，在活动选择框中选择“手机和平板电脑”  以及“空活动”  。 选择“**下一步**”。

   ![“选择项目”向导的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. 在“配置项目”屏幕中，输入“Quickstart”作为名称，输入“samples.speech.cognitiveservices.microsoft.com”作为包名称，并选择项目目录      。 对于**最低 API 级别**，选取“API 23:  Android 6.0 (Marshmallow)”，将所有其他复选框保留未选中，然后选择“完成”  。

   ![“配置项目”向导的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio 需要一定的时间来准备你的新 Android 项目。 接下来对项目进行配置，以便了解 Speech SDK 并使用 Java 8。

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

认知服务语音 SDK 的当前版本是 `1.6.0`。

将适用于 Android 的语音 SDK 打包为 [AAR（Android 库）](https://developer.android.com/studio/projects/android-library)，其内附必要的库以及所需的 Android 权限。
它托管在位于 https:\//csspeechstorage.blob.core.windows.net/maven/ 处的 Maven 存储库中。

设置项目以使用语音 SDK。 打开“项目结构”窗口，方法是从 Android Studio 菜单栏中选择“文件” > “项目结构”。   在“项目结构”窗口中进行以下更改：

1. 在窗口左侧的列表中，选择“项目”。  编辑“默认库存储库”设置，方法是附加用单引号引起来的逗号和 Maven 存储库 URL。  “https:\//csspeechstorage.blob.core.windows.net/maven/”

   ![“项目结构”窗口的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. 在同一屏幕的左侧选择“应用”。  然后在窗口顶部选择“依赖项”  选项卡。 选择绿色加号 (+)，然后从下拉菜单中选择“库依赖项”。 

   ![“项目结构”窗口的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. 在出现的窗口中，输入适用于 Android 的语音 SDK 的名称和版本 (`com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`)。 然后选择“确定”。 
   现应将语音 SDK 添加到依赖项列表中，如下所示：

   ![“项目结构”窗口的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. 选择“属性”  选项卡。“源兼容性”和“目标兼容性”都选择 1.8    。

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. 选择“确定”，关闭“项目结构”窗口并应用对项目所做的更改  。
