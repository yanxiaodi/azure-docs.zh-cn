---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173637"
---
1. 在 Android Studio 中打开项目。

2. 在 Android Studio 的“项目资源管理器”中，打开 `ToDoActivity.java` 文件，并添加以下 import 语句  ：

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. 将以下方法添加到 **ToDoActivity** 类：

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    此代码会创建一个用于处理 Google 身份验证过程的方法。 出现的对话框中会显示已经过身份验证的用户 ID。 只能在身份验证成功后继续操作。

    > [!NOTE]
    > 如果使用的标识提供者不是 Google，更改的值传递给**登录名**方法为以下值之一：_MicrosoftAccount_， _Facebook_， _Twitter_，或_windowsazureactivedirectory_。

4. 在 **onCreate** 方法中，在实例化 `MobileServiceClient` 对象的代码后面添加以下代码行。

    ```java
    authenticate();
    ```

    此调用启动身份验证过程。

5. 将 **onCreate** 方法中 `authenticate();` 后面的剩余代码移到新的 **createTable** 方法：

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. 若要确保重定向正常工作，请将下面的 `RedirectUrlActivity` 代码片段添加到 `AndroidManifest.xml`：

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. 将 `redirectUriScheme` 添加到 Android 应用程序的 `build.gradle`。

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. 将 `com.android.support:customtabs:23.0.1` 添加到 `build.gradle` 中的依赖项：

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. 然后，从“运行”  菜单中单击“运行应用”  ，以启动应用，并使用所选的标识提供者登录。

> [!WARNING]
> 所述的 URL 方案区分大小写。 请确保出现的所有 `{url_scheme_of_you_app}` 使用相同的大小写。

成功登录后，应用应可以正常运行，并且你应能查询后端服务并对数据进行更新。
