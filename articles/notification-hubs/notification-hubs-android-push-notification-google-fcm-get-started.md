---
title: Azure Notification Hubs ve Firebase Cloud Messaging ile Android 'e anında iletme bildirimleri gönderme | Microsoft Docs
description: Bu öğreticide, Android cihazlarına anında iletme bildirimleri göndermek için Azure Notification Hubs ve Google Firebase Cloud Messaging’in nasıl kullanılacağını öğrenirsiniz.
services: notification-hubs
documentationcenter: android
keywords: anında iletme bildirimleri,anında iletme bildirimi,android anında iletme bildirimi,fcm,firebase cloud messaging
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: 1af109405118754daa08adf848f1da3f04534455
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387499"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-google-firebase-cloud-messaging"></a>Öğretici: Google Firebase Cloud Messaging kullanarak Android cihazlarına anında iletme bildirimleri gönderme

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Bu öğreticide, bir Android uygulamasına bildirim göndermek için Azure Notification Hubs ve Firebase Cloud Messaging (FCM) kullanımı gösterilmektedir. Bu öğreticide Firebase Cloud Messaging (FCM) kullanarak anında iletme bildirimleri alan bir Android uygulaması oluşturacaksınız.

Bu öğreticinin tamamlanan kodu [GitHub ' dan](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp)indirilebilir.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Android Studio projesi oluşturma.
> * Firebase Cloud Messaging’i destekleyen bir Firebase projesi oluşturma.
> * Bir hub oluşturun.
> * Uygulamanızı hub 'a bağlayın.
> * Uygulamayı test etme.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için etkin bir Azure hesabınızın olması gerekir. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/free/). 

Ayrıca aşağıdaki öğeler de gereklidir: 

* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) en son sürümü
* Firebase Cloud Messaging için Android 2,3 veya üzeri
* Firebase Cloud Messaging için Google Repository düzeltme 27 veya üzeri
* Firebase Cloud Messaging için 9.0.2 veya üzeri Google Play Hizmetleri

Bu öğreticiyi tamamlamak, Android uygulamalarına yönelik diğer tüm Notification Hubs öğreticileri gerçekleştirmek için bir önkoşuldur.

## <a name="create-an-android-studio-project"></a>Android Studio Projesi oluşturma

1. Android Studio başlatın.
2. **Dosya**' yı seçin, **Yeni**üzerine gelin ve ardından **Yeni proje**' yi seçin. 
2. **Projenizi seçin** sayfasında **boş etkinlik**' i seçin ve ardından **İleri**' yi seçin. 
3. **Projenizi yapılandırın** sayfasında, aşağıdaki adımları uygulayın: 
    1. Uygulama için bir ad girin.
    2. Proje dosyalarının kaydedileceği bir konum belirtin. 
    3. **Son**’u seçin. 

        ![Projenizi yapılandırma)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>FCM’yi destekleyen bir Firebase projesi oluşturma

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Hub yapılandırma

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Hub için Firebase Cloud Messaging ayarlarını yapılandırma

1. Sol bölmede, ayarlar altında **Google (GCM/FCM)** **öğesini** seçin. 
2. Daha önce kaydettiğiniz FCM projesinin **sunucu anahtarını** girin. 
3. Araç çubuğunda **Kaydet**' i seçin. 

    ![Azure Notification Hub 'ı-Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Azure portal,, hub 'ın başarıyla güncelleştirildiği uyarılarda bir ileti görüntüler. **Kaydet** düğmesi devre dışı bırakıldı. 

Hub 'ınız şimdi Firebase Cloud Messaging ile çalışacak şekilde yapılandırılmıştır. Ayrıca, bir cihaza bildirim göndermek ve bildirim almak için bir uygulamayı kaydetmek için gerekli olan bağlantı dizelerine sahip olursunuz.

## <a id="connecting-app"></a>Uygulamanızı bildirim hub'ına bağlama

### <a name="add-google-play-services-to-the-project"></a>Projeye Google Play hizmetlerini ekleme

1. Android Studio menüsünde **Araçlar** ' ı seçin ve ardından **SDK yöneticisi**' ni seçin. 
2. Projenizde kullanılan Android SDK hedef sürümünü seçin. Ardından **Paket ayrıntılarını göster**' i seçin. 

    ![Android SDK Yöneticisi-hedef sürümü seçin](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Zaten yüklenmemişse **Google API 'leri**seçin.

    ![Android SDK Manager-Google API 'Leri seçildi](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. **SDK Tools** sekmesine geçin. Google Play Hizmetleri zaten yüklemediyseniz, aşağıdaki görüntüde gösterildiği gibi **Google Play hizmetleri** seçin. Ardından, yüklemek için **Uygula** ' yı seçin. SDK yolunun sonraki bir adım için olduğunu unutmayın.

    ![Android SDK Manager-Google Play Hizmetleri seçildi](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. **Değişikliği Onayla** iletişim kutusunu görürseniz **Tamam**' ı seçin. Bileşen yükleyicisi istenen bileşenleri yüklüyor. Bileşenler yüklendikten sonra **son** ' u seçin.
4. **Yeni projeler Için ayarlar** iletişim kutusunu kapatmak için **Tamam ' ı** seçin.  
1. AndroidManifest. xml dosyasını açın ve ardından aşağıdaki etiketi *uygulama* etiketine ekleyin.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs kitaplıklarını ekleme

1. Uygulamanın Build. Gradle dosyasında, bağımlılıklar bölümüne aşağıdaki satırları ekleyin.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. Bağımlılıklar bölümünden sonra aşağıdaki depoyu ekleyin.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Google Firebase desteği ekleme

1. Uygulamanın Build. Gradle dosyasında, zaten mevcut değilse, **Bağımlılıklar** bölümüne aşağıdaki satırları ekleyin. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Henüz orada değilse, dosyanın sonuna aşağıdaki eklentiyi ekleyin. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Araç çubuğunda **Şimdi Eşitle** ' yi seçin.

### <a name="update-the-androidmanifestxml-file"></a>AndroidManifest. xml dosyasını güncelleştirme

1. FCM kayıt belirtecinizi aldıktan sonra [Azure Notification Hubs kaydetmek](notification-hubs-push-notification-registration-management.md)için bunu kullanırsınız. Bu kaydı arka planda, `RegistrationIntentService` adlı `IntentService` kullanarak destekleyebilirsiniz. Bu hizmet, FCM kayıt belirtecinizi de yeniler. Ayrıca, `FirebaseMessagingService` ' in bir alt sınıfı olarak `FirebaseService` adlı bir sınıf oluşturursunuz ve bildirimleri almak ve işlemek için `onMessageReceived` yöntemini geçersiz kılarsınız. 

    Aşağıdaki hizmet tanımını AndroidManifest.xml dosyasında `<application>` etiketinin içine ekleyin.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. @No__t-0 etiketinin altına aşağıdaki gerekli FCM ile ilgili izinleri ekleyin.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Kod Ekle

1. Proje Görünümü'nde **app** > **src** > **main** > **java**'yı genişletin. **Java**altındaki paket klasörünüze sağ tıklayın, **Yeni**' yi seçin ve ardından **Java sınıfı**' nı seçin. Ad için **Notificationsettings** yazın ve ardından **Tamam**' ı seçin.

    Aşağıdaki kodda `NotificationSettings` sınıfı için bu üç yer tutucuyu güncelleştirdiğinizden emin olun:

   * **HubListenConnectionString**: Hub'ınız için **DefaultListenAccessSignature** bağlantı dizesi. [Azure portalda]hub 'ınızdaki **erişim ilkeleri** ' ne tıklayarak bu bağlantı dizesini kopyalayabilirsiniz.
   * **Hubname**: [Azure portalda]hub sayfasında görünen hub 'ınızın adını kullanın.

     `NotificationSettings` kodu:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Devam etmeden önce hub 'ınızın adını ve **Defaultlistensharedaccesssignature** **adını** girin. 

2. `RegistrationIntentService` adlı projenize başka bir yeni sınıf ekleyin. Bu sınıf `IntentService` arabirimini uygular. Ayrıca [, FCM belirtecini yenilemeyi](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) ve [Bildirim Hub 'ına kaydetmeyi](notification-hubs-push-notification-registration-management.md)de gerçekleştirir.

    Bu sınıf için aşağıdaki kod kullanın.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. @No__t-0 sınıfında, sınıf bildiriminin üzerine aşağıdaki `import` deyimlerini ekleyin.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Aşağıdaki üyeleri sınıfının üst kısmına ekleyin. [Google Play Hizmetleri'nin kullanılabilirliğini Google tarafından önerildiği şekilde denetlemek](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk) için bu alanları kullanırsınız.

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. @No__t-0 sınıfında Google Play Hizmetleri kullanılabilirliğini denetlemek için aşağıdaki yöntemi ekleyin.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

6. @No__t-0 sınıfında, FCM kayıt belirtecinizi almak ve merkezinizle kaydettirmek için `IntentService` ' i çağırmadan önce Google Play Hizmetleri denetleyen aşağıdaki kodu ekleyin:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

7. @No__t-1 sınıfının `OnCreate` yönteminde, etkinlik oluşturulduğunda kayıt işlemini başlatmak için aşağıdaki kodu ekleyin:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Uygulama durumunu doğrulamak ve uygulamanızda durumu raporlamak için bu ek yöntemleri `MainActivity` ' a ekleyin:

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

9. `ToastNotify` yöntemi, uygulamada kalıcı olarak durumu ve bildirimleri raporlamak için *"Hello World"* `TextView` denetimini kullanır. **Kay** > **düzeninde** > **activity_main. xml** DÜZENINDE, bu denetim için aşağıdaki kimliği ekleyin.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Azure Notification Hubs - Test Gönderimi](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Ardından, AndroidManifest. xml ' de tanımladığınız alıcı için bir alt sınıf eklersiniz. `FirebaseService` adlı projenize başka bir yeni sınıf ekleyin.

11. `FirebaseService.java`'in üst kısmına şu içeri aktarma deyimlerini ekleyin:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. @No__t-0 sınıfı için aşağıdaki kodu ekleyin, bunu `FirebaseMessagingService` ' in bir alt sınıfı yapar.

    Bu kod `onMessageReceived` yöntemini geçersiz kılar ve alınan bildirimleri raporlar. Ayrıca, `sendNotification()` yöntemini kullanarak, anında iletme bildirimini Android bildirim Yöneticisi 'ne gönderir. Uygulama çalışmadığı zaman ve bir bildirim alındığında `sendNotification()` yöntemini çağırın.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. Android Studio kodunuzda herhangi bir hata olmadığından emin olmak için, menü çubuğunda, **oluştur** > **projeyi yeniden derle** ' yi seçin. @No__t-0 simgesiyle ilgili bir hata alırsanız, AndroidManifest. xml dosyasından aşağıdaki ifadeyi kaldırın: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Uygulamayı çalıştırmak için bir sanal cihazınıza sahip olduğunuzdan emin olun. Yoksa, aşağıdaki gibi bir tane ekleyin:
    1. ![Cihaz Yöneticisi 'ni aç](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Sanal cihaz oluştur](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Seçili cihazınızda uygulamayı çalıştırın ve hub 'a başarıyla kaydedildiğinizi doğrulayın.

    > [!NOTE]
    > Kayıt, örnek KIMLIĞI hizmetinin `onTokenRefresh()` yöntemi çağrılana kadar ilk başlatma sırasında başarısız olabilir. Yenileme işlemi bildirim hub'ına başarılı bir kayıt başlatmalıdır.

    ![Cihaz kaydı başarılı](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Bildirim hub’ından bildirim göndermeyi test edin

Aşağıdaki adımları uygulayarak [Azure portalda] anında iletme bildirimleri gönderebilirsiniz:

1. Azure portal, hub 'ınız için Bildirim Hub 'ı sayfasında, **sorun giderme** bölümünde **Test gönder** ' i seçin.
3. **Platformlar** için **Android**’i seçin.
4. **Gönder**’i seçin.  Mobil uygulamayı üzerinde çalıştırmadığınız için henüz Android cihazında bir bildirim görmezsiniz. Mobil uygulamayı çalıştırdıktan sonra bildirim iletisini görmek için **Gönder** düğmesini tekrar seçin.
5. En alttaki listede işlemin sonucuna bakın.

    ![Azure Notification Hubs - Test Gönderimi](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Uyarı iletisini cihazınızda görürsünüz. 

    ![Cihazdaki bildirim iletisi](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Öykünücü üzerinde mobil uygulamayı çalıştırma
Bir öykünücü içinde anında iletme bildirimlerini test etmeden önce, öykünücü görüntünüzün uygulamanız için seçtiğiniz Google API düzeyini desteklediğinden emin olun. Görüntünüz yerel Google API 'Lerini desteklemiyorsa, **@ no__t-1NOT @ no__t-2** dışı özel durum hizmetini alabilirsiniz.

Ayrıca, Google hesabınızı, **ayarlar** > **hesapları**altında çalışan öykünücüse eklediğinizden emin olun. Aksi halde, FCM ile kaydolma girişimleri, **kimlik doğrulaması @ no__t-1BAŞARıSıZ** özel durumu ile sonuçlanabilir.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, hizmet ile kaydedilmiş tüm Android cihazlara bildirim yayımlamak için Firebase bulut mesajlaşma 'yı kullandınız. Belirli cihazlara nasıl anında iletme bildirimleri gönderileceğini öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Öğretici: belirli Android cihazlara anında Iletme bildirimleri gönderme](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure portalda]: https://portal.azure.com
