---
title: Baidu kullanarak Azure Notification Hubs ile çalışmaya başlama | Microsoft Belgeleri
description: Bu öğreticide, Baidu kullanarak Android cihazlarına anında iletme bildirimleri göndermek için Azure Notification Hubs'ın nasıl kullanılacağını öğrenirsiniz.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 06/19/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 06/19/2019
ms.openlocfilehash: de02c1add1d6ea3a98d30407b7661e71a28458fe
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211916"
---
# <a name="deprecated-get-started-with-notification-hubs-using-baidu"></a>Kullanım dışı Baidu kullanarak Azure Notification Hubs ile çalışmaya başlama

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!IMPORTANT]
> Bu öğretici kullanım dışıdır. 

Baidu bulut anında iletme, mobil cihazlara anında iletme bildirimleri göndermede kullanabileceğiniz bir Çin bulut hizmetidir.

Google Play ve FCM (Firebase Cloud Messaging) Çin'de mevcut değildir ve farklı uygulama mağazaları ile anında iletim hizmetlerinin kullanılması gerekir. Baidu bunlardan biridir ve şu anda Notification Hubs tarafından kullanılmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici için aşağıdakiler gereklidir:

* [Android sitesinden](https://go.microsoft.com/fwlink/?LinkId=389797) indirebileceğiniz Android SDK'sı (Android Studio kullanacağınız varsayılır)
* [Baidu Anında İletme Android SDK’sı]

> [!NOTE]
> Bu öğreticiyi tamamlamak için etkin bir Azure hesabınızın olması gerekir. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).

## <a name="create-a-baidu-account"></a>Bir Baidu hesabı oluşturma

Baidu kullanmak için bir Baidu hesabınızın olması gerekir. Zaten varsa [Baidu portalında] oturum açın ve sonraki adıma atlayın. Aksi halde, bir Baidu hesabının nasıl oluşturulacağı hakkında aşağıdaki yönergelere bakın.  

1. [Baidu portalında] gidin ve **登录** (**Oturum Aç**) bağlantısına tıklayın. Hesap kayıt işlemini başlatmak için **立即注册** (**Şimdi Kaydol**) seçeneğine tıklayın.

    ![Baidu Kaydı](./media/notification-hubs-baidu-get-started/BaiduRegistration.png)

2. Gerekli ayrıntıları girin (telefon/e-posta adresi, parola ve doğrulama kodu) ve 注册 (**Kaydol**) seçeneğine tıklayın.

    ![Baidu Kayıt Girişi](./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png)

3. Girdiğiniz e-posta adresine, Baidu hesabınızı etkinleştirmek için bir bağlantıya sahip bir e-posta gönderilir.

    ![Baidu Kayıt Onayı](./media/notification-hubs-baidu-get-started/BaiduConfirmation.png)

4. E-posta hesabınızda oturum açın, Baidu etkinleştirme e-postasını açın ve Baidu hesabınızı etkinleştirmek için etkinleştirme bağlantısına tıklayın.

    ![Baidu Etkinleştirme e-postası](./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png)

Baidu hesabınızı etkinleştirdikten sonra, [Baidu portalında] oturum açın.

## <a name="create-a-baidu-cloud-push-project"></a>Bir Baidu bulut anında iletme projesi oluşturma

Bir Baidu bulut anında iletme projesi oluşturduğunuzda, uygulama kimliğinizi, API anahtarınızı ve gizli anahtarınızı alırsınız.

1. [Baidu portalında] oturum açtıktan sonra **更多 >>** (**diğer**) seçeneğine tıklayın.

    ![Kayıt - Devamı](./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png)

2. **站长与开发者服务** (**Web Uzmanı ve Geliştirici Hizmetleri**) bölümünde aşağı kaydırın ve **百度云推送** (**Baidu Bulut Anında İletme** ) seçeneğine tıklayın.

    ![Baidu Açık Bulut Platformu](./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png)

3. Sonraki sayfada sağ üst köşedeki **登录** (**Oturum Aç**) seçeneğine tıklayın.

    ![Baidu Oturum Açma](./media/notification-hubs-baidu-get-started/BaiduLogin.png)

4. Ardından bu sayfada **创建应用** (**Uygulama Oluştur**) seçeneğine tıklayın.

    ![Baidu Uygulama Oluşturma](./media/notification-hubs-baidu-get-started/BaiduCreateApplication.png)

5. Sonraki sayfada 创建新应用 (**Yeni Uygulama Oluştur**) seçeneğine tıklayın.

    ![Baidu Yeni Uygulama Oluştur](./media/notification-hubs-baidu-get-started/BaiduCreateNewApplication.png)

6. Bir uygulama adı girin ve 创建 (**Oluştur**) seçeneğine tıklayın.

    ![](./media/notification-hubs-baidu-get-started/BaiduCreateApplicationDoCreate.png)

7. Bir Baidu bulut anında iletme projesi başarılı bir şekilde oluşturulduktan sonra, **Uygulama Kimliği**, **API Anahtarı** ve **Gizli Anahtar** değerlerini içeren bir sayfa görürsünüz. Daha sonra kullanacağımız API anahtarı ve gizli anahtarı not edin.

    ![Baidu Anında İletme Gizli Anahtarları](./media/notification-hubs-baidu-get-started/BaiduGetSecrets.png)

8. Sol bölmedeki 创建通知 (**Bildirim Oluştur**) seçeneğine tıklayarak projeyi anında iletme bildirimleri için yapılandırın.

    ![](./media/notification-hubs-baidu-get-started/BaiduCreateNotification.png)

## <a name="configure-a-new-notification-hub"></a>Yeni bir bildirim hub'ı yapılandırma

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. Bildirim hub'ınızda **Bildirim Hizmetleri**'ni ve ardından **Baidu (Android China)** seçeneğini belirleyin.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs - Baidu](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

&emsp;&emsp;7. Baidu bildirim ayarları bölümüne inin. Baidu bulut anında iletme projesinde Baidu konsolundan aldığınız API anahtarını ve gizli anahtarını girin. Daha sonra Kaydet'e tıklayın.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs - Baidu Gizli Anahtarları](./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png)

Bildirim hub'ınız Baidu ile birlikte çalışacak şekilde yapılandırıldı. Ayrıca uygulamanızı anında iletme bildirimi göndermek ve almak üzere kaydetmek için kullanabileceğiniz **bağlantı dizelerine** de sahipsiniz.

Erişim bağlantı bilgileri penceresindeki `DefaultListenSharedAccessSignature` ve `DefaultFullSharedAccessSignature` bilgilerini not edin.

## <a name="connect-your-app-to-the-notification-hub"></a>Uygulamanızı bildirim hub'ına bağlama

1. Android Studio'da yeni bir Android projesi oluşturun (Dosya > Yeni > Yeni Proje).

    ![Azure Notification Hubs - Baidu Yeni Proje](./media/notification-hubs-baidu-get-started/AndroidNewProject.png)

2. Bir uygulama adı girin ve gereken minimum SDK sürümünün API 16 olarak ayarlandığından emin olun: Android 4,1. **Ayrıca lütfen paketinizin adının (应用包名) Baidu Bulut Anında İletme Portalı’nda aynı olduğundan emin olun**

    ![Azure Notification Hubs-Baidu min SDK1](./media/notification-hubs-baidu-get-started/AndroidMinSDK.png) ![Azure Notification Hubs-Baidu min SDK2](./media/notification-hubs-baidu-get-started/AndroidMinSDK2.png)

3. İleri'ye tıklayın ve Etkinlik Oluştur penceresi görünene kadar sihirbazı izlemeye devam edin. Boş Etkinlik değerinin seçildiğinden emin olun ve son olarak yeni bir Android Uygulaması oluşturmak için Son'u seçin.

    ![Azure Notification Hubs - Baidu Etkinliği Ekleme](./media/notification-hubs-baidu-get-started/AndroidAddActivity.png)

4. Proje Derleme Hedefi'nin doğru ayarlandığından emin olun.

5. Ardından Azure Notification Hubs kitaplıklarını ekleyin. Uygulamanın `Build.Gradle` dosyasında bağımlılıklar bölümüne aşağıdaki satırları ekleyin.

    ```javascript
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

    Bağımlılıklar bölümünden sonra aşağıdaki depoyu ekleyin.

    ```javascript
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

    Liste çakışmasını önlemek için, aşağıdaki kodu projenin `Manifest.xml` dosyasına ekleyin:

    ```xml
    <manifest package="YOUR.PACKAGE.NAME"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android">
    ```

    ve `<application/>` etiketine:

    ```xml
    <application
        tools:replace="android:allowBackup,icon,theme,label">
    ```

6. [Baidu Anında İletme Android SDK’sı](https://push.baidu.com/doc/android/api) indirin ve açın. `pushservice-x.y.z jar` dosyasını libs klasörüne kopyalayın. Ardından `.so` dosyalarını Android uygulamanızın `src/main/jniLibs` (yeni bir klasör oluşturun) klasörlerine kopyalayın.

    ![Azure Notification Hubs - Baidu SDK Kitaplıkları](./media/notification-hubs-baidu-get-started/BaiduSDKLib.png)

7. Projenin `libs` klasöründe, `pushervice-x.y.z.jar` dosyaya sağ tıklayın; bu kitaplığı projeye dahil etmek için **kitaplık olarak ekle** ' yi seçin.

    ![Azure Notification Hubs - Baidu Kitaplık Olarak Ekleme](./media/notification-hubs-baidu-get-started/BaiduAddAsALib.jpg)

8. Android projesinin `AndroidManifest.xml` dosyasını açın ve Baidu SDK 'sının gerektirdiği izinleri ekleyin. **`YOURPACKAGENAME` yerine paketinizin adını yazın**.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
    <uses-permission android:name="android.permission.EXPAND_STATUS_BAR" />
    !! <uses-permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME" />
    !!<permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME"android:protectionLevel="normal" />

    ```

9. `.MainActivity` etkinlik öğesinden sonra uygulama öğesinin içine aşağıdaki yapılandırmayı ekleyip *yourprojectname*'i değiştirin (ör. `com.example.BaiduTest`):

    ```xml
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaViewActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaViewActivity" />
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaListActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaListActivity"
        android:launchMode="singleTask" />

    <!-- Push application definition message -->
    <receiver android:name=".MyPushMessageReceiver">
        <intent-filter>

            <!-- receive push message-->
            <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
            <!-- receive bind,unbind,fetch,delete.. message-->
            <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
            <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.PushServiceReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
            <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            <action android:name="com.baidu.android.pushservice.action.media.CLICK" />
            <action android:name="android.intent.action.MEDIA_MOUNTED" />
            <action android:name="android.intent.action.USER_PRESENT" />
            <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
            <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.RegistrationReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.METHOD" />
            <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
        </intent-filter>
        <intent-filter>
            <action android:name="android.intent.action.PACKAGE_REMOVED" />

            <data android:scheme="package" />
        </intent-filter>
    </receiver>

    <service
        android:name="com.baidu.android.pushservice.PushService"
        android:exported="true"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
        </intent-filter>
    </service>

    <service
        android:name="com.baidu.android.pushservice.CommandService"
        android:exported="true" />

    <!-- Adapt the ContentProvider declaration required for the Android N system, and the write permissions include the application package name-->
    <provider
        android:name="com.baidu.android.pushservice.PushInfoProvider"
        android:authorities="com.baidu.push.example.bdpush"
        android:exported="true"
        android:protectionLevel="signature"
        android:writePermission="baidu.push.permission.WRITE_PUSHINFOPROVIDER. yourprojectname  " />

    <!-- API Key of the Baidu application -->
    <meta-data
        android:name="api_key"
        !!   android:value="api_key" />
    </application>
    ```

10. Projeye `ConfigurationSettings.java` adında yeni bir sınıf ekleyin.

    ```java
    public class ConfigurationSettings {
        public static String API_KEY = "...";
        public static String NotificationHubName = "...";
        public static String NotificationHubConnectionString = "...";
    }
    ```

    `API_KEY` dizesinin değerini Baidu Bulut Projesi API_KEY değeriyle değiştirin.

    `NotificationHubName` dizesinin değerini [Azure portalındaki] bildirim hub'ı ile, `NotificationHubConnectionString` değerini de [Azure portalındaki] `DefaultListenSharedAccessSignature` değeriyle değiştirin.

11. MainActivity.java'yı açın ve aşağıdakini onCreate yöntemine ekleyin:

    ```java
    PushManager.startWork(this, PushConstants.LOGIN_TYPE_API_KEY,  API_KEY );
    ```

12. `MyPushMessageReceiver.java` adlı yeni bir sınıf ekleyin ve bu sınıfa aşağıdaki kodu ekleyin. Bu, Baidu anında iletme sunucusundan alınan anında iletme bildirimlerini işleyen sınıftır.

    ```java
    package your.package.name;

    import android.content.Context;
    import android.content.Intent;
    import android.os.AsyncTask;
    import android.text.TextUtils;
    import android.util.Log;

    import com.baidu.android.pushservice.PushMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import org.json.JSONException;
    import org.json.JSONObject;

    import java.util.List;

    public class MyPushMessageReceiver extends PushMessageReceiver {

        public static final String TAG = MyPushMessageReceiver.class
                .getSimpleName();
        public static NotificationHub hub = null;
        public static String mChannelId, mUserId;

        @Override
        public void onBind(Context context, int errorCode, String appid,
                        String userId, String channelId, String requestId) {
            String responseString = "onBind errorCode=" + errorCode + " appid="
                    + appid + " userId=" + userId + " channelId=" + channelId
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Binding successful
                Log.d(TAG, " Binding successful");
            }
            try {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName,
                            ConfigurationSettings.NotificationHubConnectionString,
                            context);
                    Log.i(TAG, "Notification hub initialized");
                }
            } catch (Exception e) {
                Log.e(TAG, e.getMessage());
            }
            mChannelId = channelId;
            mUserId = userId;

            registerWithNotificationHubs();
        }
        private void registerWithNotificationHubs() {

            new AsyncTask<Void, Void, Void>() {
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        hub.registerBaidu(mUserId, mChannelId);
                        Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                    } catch (Exception e) {
                        Log.e(TAG, e.getMessage());
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

        @Override
        public void onMessage(Context context, String message,
                            String customContentString) {
            String messageString = " onMessage=\"" + message
                    + "\" customContentString=" + customContentString;
            Log.d(TAG, messageString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }

        }

        @Override
        public void onNotificationArrived(Context context, String title, String description, String customContentString) {
            String notifyString = " Notice Arrives onNotificationArrived  title=\"" + title
                    + "\" description=\"" + description + "\" customContent="
                    + customContentString;
            Log.d(TAG, notifyString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }

        @Override
        public void onNotificationClicked(Context context, String title, String description, String customContentString) {
            String notifyString = " onNotificationClicked title=\"" + title + "\" description=\""
                    + description + "\" customContent=" + customContentString;
            Log.d(TAG, notifyString);
            Intent intent = new Intent(context.getApplicationContext(),MainActivity.class);
            intent.putExtra("title",title);
            intent.putExtra("description",description);
            intent.putExtra("isFromNotify",true);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.getApplicationContext().startActivity(intent);

        }

        @Override
        public void onSetTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onSetTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onDelTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onDelTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onListTags(Context context, int errorCode, List<String> tags,
                            String requestId) {
            String responseString = "onListTags errorCode=" + errorCode + " tags="
                    + tags;
            Log.d(TAG, responseString);

        }

        @Override
        public void onUnbind(Context context, int errorCode, String requestId) {
            String responseString = "onUnbind errorCode=" + errorCode
                    + " requestId = " + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Unbinding is successful
                Log.d(TAG, " Unbinding is successful ");
            }
        }
    }
    ```

## <a name="send-notifications-to-your-app"></a>Uygulamanıza bildirimler gönderme

Aşağıdaki ekranlarda gösterildiği [Azure portalındaki] bildirim hub'ı yapılandırma ekranındaki **Gönder** düğmesini kullanarak uygulamanızda bildirim almayı hızlıca test edebilirsiniz:

![](./media/notification-hubs-baidu-get-started/BaiduTestSendButton.png)
![](./media/notification-hubs-baidu-get-started/BaiduTestSend.png)

Anında iletme bildirimleri normalde, uyumlu bir kitaplık kullanılarak Mobile Services veya ASP.NET gibi bir arka uç hizmetinde gönderilir. Arka ucunuz için uygun bir kitaplık yoksa bildirim iletilerini göndermek için doğrudan REST API'sini kullanabilirsiniz.

Bu öğreticide kolaylık sağlamak için .NET SDK ile bildirim göndermenin gösterilmesi amacıyla konsol uygulaması kullanılmaktadır. Ancak bir ASP.NET arka ucundan bildirim göndermek için sonraki adım olarak [Kullanıcılara anında iletme bildirimleri göndermek için Notification Hubs'ı kullanma](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) öğreticisini öneririz. 

Bildirim göndermeye yönelik farklı yaklaşımlar aşağıda listelenmiştir:

* **Rest arabirimi**:  [Rest arabirimini](https://msdn.microsoft.com/library/windowsazure/dn223264.aspx)kullanarak herhangi bir arka uç platformunda bildirimi destekleyebilirsiniz.
* **Microsoft Azure Notification Hubs .NET SDK**: Visual Studio için NuGet Paket Yöneticisi ' nde [Install-Package Microsoft. Azure. Notificationhub](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)' ı çalıştırın.
* **Node.js**: [Node. js ' den Notification Hubs kullanma](notification-hubs-nodejs-push-notification-tutorial.md).
* **Mobile Apps**: Notification Hubs ile tümleştirilmiş Azure App Service Mobile Apps arka ucundan nasıl bildirim gönderileceğini gösteren bir örnek için bkz. [mobil uygulamanıza anında iletme bildirimleri ekleme](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).
* **Java/php**: REST API 'leri kullanarak bildirimlerin nasıl gönderileceği hakkında bir örnek için, bkz. "Java/php 'den Notification Hubs kullanma" ([Java](notification-hubs-java-push-notification-tutorial.md) | [php](notification-hubs-php-push-notification-tutorial.md)).

## <a name="optional-send-notifications-from-a-net-console-app"></a>(İsteğe bağlı) Bir .NET konsol uygulamasından bildirim gönderme

Bu bölümde, bir .NET konsol uygulaması kullanarak bildirim göndermeyi göstereceğiz.

1. Yeni bir Visual C# konsol uygulaması oluşturun:

    ![](./media/notification-hubs-baidu-get-started/ConsoleProject.png)

2. Paket Yöneticisi Konsolu penceresinde, **Varsayılan projeyi** yeni konsol uygulaması projeniz olarak ayarlayın ve ardından konsol penceresinde aşağıdaki komutu yürütün:

    ```shell
    Install-Package Microsoft.Azure.NotificationHubs
    ```

    Bu yönerge, [Microsoft.Azure.Notification Hubs NuGet paketini](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) kullanarak Azure Notification Hubs SDK'sına bir başvuru ekler.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. `Program.cs` dosyasını açın ve aşağıdaki deyimi ekleyin:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

4. `Program` sınıfınıza aşağıdaki yöntemi ekleyin ve `DefaultFullSharedAccessSignatureSASConnectionString` ile `NotificationHubName` değerlerini sahip olduğunuz değerlerle değiştirin.

    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
        string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
        var result = await hub.SendBaiduNativeNotificationAsync(message);
    }
    ```

5. Aşağıdaki satırları `Main` yönteminize ekleyin:

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

## <a name="test-your-app"></a>Uygulamanızı test etme

Bu uygulamayı gerçek bir telefonla test etmek için telefonu bir USB kablosu kullanarak bilgisayarınıza bağlamanız yeterlidir. Bu işlem uygulamanızı iliştirilmiş telefona yükler.

Bu uygulamayı öykünücüyle test etmek için Android Studio üst araç çubuğunda **Çalıştır**'a tıklayın ve ardından uygulamanızı seçin: öykünücü başlatılır, uygulama yüklenir ve çalıştırılır.

Uygulama, Baidu Anında iletme bildirimi hizmetinden `userId` ve `channelId` alır ve bildirim hub'ı ile kaydeder.

Bir test bildirimi göndermek için [Azure portalındaki] hata ayıklama sekmesini kullanabilirsiniz. Visual Studio için .NET konsol uygulamasını oluşturduysanız uygulamayı çalıştırmak için Visual Studio'da F5 tuşuna basmanız yeterlidir. Uygulama, cihazınızın veya öykünücünüzün bildirim alanının üst kısmında görünecek bir bildirim gönderir.

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Anında İletme Android SDK’sı]: https://push.baidu.com/sdk/push_client_sdk_for_android
[Azure portalındaki]: https://portal.azure.com/
[Baidu portalında]: https://www.baidu.com/
