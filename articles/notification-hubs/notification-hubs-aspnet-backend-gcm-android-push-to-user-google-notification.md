---
title: Azure Notification Hubs kullanarak belirli Android uygulamalarına bildirim gönderme | Microsoft Docs
description: Azure Notification Hubs kullanarak belirli kullanıcılara anında iletme bildirimleri göndermeyi öğrenin.
documentationcenter: android
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ae0e17a8-9d2b-496e-afd2-baa151370c25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c5c9ec26c9387cd9ae129002697210c2b342ab9b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385897"
---
# <a name="tutorial-push-notification-to-specific-android-application-users-by-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Öğretici: Azure Notification Hubs ve Google Cloud Messaging (kullanım dışı) kullanarak belirli Android uygulama kullanıcılarına anında Iletme bildirimi

> [!WARNING]
> 10 Nisan 2018 itibariyle, Google Google Cloud Messaging (GCM) kullanım dışıdır. GCM sunucusu ve istemci API 'Leri kullanım dışıdır ve 29 Mayıs 2019 ' den hemen sonra kaldırılacaktır. Daha fazla bilgi için bkz. [GCM ve FCM hakkında sık sorulan sorular](https://developers.google.com/cloud-messaging/faq).

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Bu öğreticide, belirli bir cihazdaki belirli bir uygulama kullanıcısına anında iletme bildirimleri göndermek için Azure Bildirim Hub'larını nasıl kullanacağınız gösterilir. [Uygulama arka ucunuzdan kaydolma](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) başlıklı yönerge makalesinde gösterildiği gibi, istemcilerin kimliğini doğrulamak ve bildirimler oluşturmak için ASP.NET WebAPI arka ucu kullanılır. Bu öğretici, [Öğretici: Azure Notification Hubs ve Google Cloud Messaging kullanarak Android cihazlara anında iletme bildirimleri gönderme](notification-hubs-android-push-notification-google-gcm-get-started.md) öğreticisinde oluşturduğunuz bildirim hub’ını temel alır.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Kullanıcıların kimliğini doğrulayan arka uç Web API projesi oluşturma.  
> * Android uygulamasını güncelleştirme.
> * Uygulamayı test edin

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi uygulamadan önce [Öğretici: Azure Notification Hubs ve Google Cloud Messaging kullanarak Android cihazlara anında iletme bildirimleri gönderme](notification-hubs-android-push-notification-google-gcm-get-started.md) öğreticisini tamamlayın.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Android Projesi oluşturma

Sonraki adım, [Öğretici: Azure Notification Hubs ve Google Cloud Messaging kullanarak Android cihazlara anında iletme bildirimleri gönderme](notification-hubs-android-push-notification-google-gcm-get-started.md) öğreticisinde oluşturulan Android uygulamasının güncelleştirilmesidir.

1. @No__t-0 dosyanızı açın, aşağıdaki içerik tanımlarını değiştirin:

    Kullanıcı olarak oturum açmak için yeni EditText denetimleri ekler. Ayrıca gönderdiğiniz bildirimlerin parçası olacak kullanıcı adı etiketi için bir alan da eklenir:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <EditText
        android:id="@+id/usernameText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/usernameHint"
        android:layout_above="@+id/passwordText"
        android:layout_alignParentEnd="true" />
    <EditText
        android:id="@+id/passwordText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/passwordHint"
        android:inputType="textPassword"
        android:layout_above="@+id/buttonLogin"
        android:layout_alignParentEnd="true" />
    <Button
        android:id="@+id/buttonLogin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/loginButton"
        android:onClick="login"
        android:layout_above="@+id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="24dp" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="WNS on"
        android:textOff="WNS off"
        android:id="@+id/toggleButtonWNS"
        android:layout_toLeftOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="GCM on"
        android:textOff="GCM off"
        android:id="@+id/toggleButtonGCM"
        android:checked="true"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="APNS on"
        android:textOff="APNS off"
        android:id="@+id/toggleButtonAPNS"
        android:layout_toRightOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessageTag"
        android:layout_below="@id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_tag_hint" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_below="@+id/editTextNotificationMessageTag"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_hint" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:onClick="sendNotificationButtonOnClick"
        android:layout_below="@+id/editTextNotificationMessage"
        android:layout_centerHorizontal="true" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:id="@+id/text_hello"
    />  
    </RelativeLayout>
    ```
2. @No__t-0 dosyanızı açın ve `send_button` tanımını `send_button` dizesini yeniden tanımlayarak aşağıdaki satırlarla değiştirin ve diğer denetimler için dizeler ekleyin:

    ```xml
    <string name="usernameHint">Username</string>
    <string name="passwordHint">Password</string>
    <string name="loginButton">1. Sign in</string>
    <string name="send_button">2. Send Notification</string>
    <string name="notification_message_hint">Notification message</string>
    <string name="notification_message_tag_hint">Recipient username</string>
    ```

    @No__t 0 grafik düzeniniz artık aşağıdaki görüntüye benzer şekilde görünmelidir:

    ![][A1]
3. @No__t-1 sınıfınız ile aynı pakette `RegisterClient` adlı yeni bir sınıf oluşturun. Yeni sınıf dosyası için aşağıdaki kodu kullanın.

    ```java
    import java.io.IOException;
    import java.io.UnsupportedEncodingException;
    import java.util.Set;

    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.methods.HttpPut;
    import org.apache.http.client.methods.HttpUriRequest;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONException;
    import org.json.JSONObject;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.util.Log;

    public class RegisterClient {
        private static final String PREFS_NAME = "ANHSettings";
        private static final String REGID_SETTING_NAME = "ANHRegistrationId";
        private String Backend_Endpoint;
        SharedPreferences settings;
        protected HttpClient httpClient;
        private String authorizationHeader;

        public RegisterClient(Context context, String backendEndpoint) {
            super();
            this.settings = context.getSharedPreferences(PREFS_NAME, 0);
            httpClient =  new DefaultHttpClient();
            Backend_Endpoint = backendEndpoint + "/api/register";
        }

        public String getAuthorizationHeader() {
            return authorizationHeader;
        }

        public void setAuthorizationHeader(String authorizationHeader) {
            this.authorizationHeader = authorizationHeader;
        }

        public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
            String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

            JSONObject deviceInfo = new JSONObject();
            deviceInfo.put("Platform", "gcm");
            deviceInfo.put("Handle", handle);
            deviceInfo.put("Tags", new JSONArray(tags));

            int statusCode = upsertRegistration(registrationId, deviceInfo);

            if (statusCode == HttpStatus.SC_OK) {
                return;
            } else if (statusCode == HttpStatus.SC_GONE){
                settings.edit().remove(REGID_SETTING_NAME).commit();
                registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                statusCode = upsertRegistration(registrationId, deviceInfo);
                if (statusCode != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            } else {
                Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                throw new RuntimeException("Error upserting registration");
            }
        }

        private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                throws UnsupportedEncodingException, IOException,
                ClientProtocolException {
            HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
            request.setEntity(new StringEntity(deviceInfo.toString()));
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            request.addHeader("Content-Type", "application/json");
            HttpResponse response = httpClient.execute(request);
            int statusCode = response.getStatusLine().getStatusCode();
            return statusCode;
        }

        private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
            if (settings.contains(REGID_SETTING_NAME))
                return settings.getString(REGID_SETTING_NAME, null);

            HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            HttpResponse response = httpClient.execute(request);
            if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                throw new RuntimeException("Error creating Notification Hubs registrationId");
            }
            String registrationId = EntityUtils.toString(response.getEntity());
            registrationId = registrationId.substring(1, registrationId.length()-1);

            settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

            return registrationId;
        }
    }
    ```

    Bu bileşen, anında iletme bildirimlerine kaydolmak için uygulama arka ucuyla iletişim kurmada gereken REST çağrılarını uygular. [Uygulama arka ucunuzdan kaydetme](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) bölümünde açıklandığı gibi Bildirim Hub’ı tarafından oluşturulan *registrationId*’leri de yerel olarak depolar. **Oturum aç** düğmesine tıkladığınızda yerel depolamada depolanan bir yetkilendirme belirteci kullanır.
4. Sınıfınızda, `NotificationHub` için özel alanınızı kaldırın veya açıklama satırı yapın, ardından `RegisterClient` sınıfı için bir alan ve ASP.NET arka ucunuzun uç noktası için bir dize ekleyin. `<Enter Your Backend Endpoint>` değerini, önceden aldığınız gerçek arka ucun uç noktasıyla değiştirdiğinizden emin olun. Örneğin, `http://mybackend.azurewebsites.net`.

    ```java
    //private NotificationHub hub;
    private RegisterClient registerClient;
    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

5. `MainActivity` sınıfınızda `onCreate` yönteminde, `registerWithNotificationHubs` yöntemine yapılan çağrının ve `hub` alanının başlatmasını kaldırın veya açıklama satırı yapın. `RegisterClient` sınıfının bir örneğini başlatmak için kod ekleyin. Yöntem aşağıdaki satırları içermelidir:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);

        //hub = new NotificationHub(HubName, HubListenConnectionString, this);
        //registerWithNotificationHubs();

        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

        setContentView(R.layout.activity_main);
    }
    ```
6. `MainActivity` sınıfınızda `registerWithNotificationHubs` yönteminin tamamını silin veya açıklama satırı yapın. Bu öğreticide kullanılmayacaktır.
7. Aşağıdaki `import` deyimlerini `MainActivity.java` dosyanıza ekleyin.

    ```java
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;

    import android.widget.Button;
    import android.widget.ToggleButton;
    import java.io.UnsupportedEncodingException;
    import android.content.Context;
    import java.util.HashSet;
    import android.widget.Toast;
    import org.apache.http.client.ClientProtocolException;
    import java.io.IOException;
    import org.apache.http.HttpStatus;

    import android.os.AsyncTask;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;

    import android.app.AlertDialog;
    import android.content.DialogInterface;
    ```
8. onStart yöntemindeki kodu, aşağıdaki kodla değiştirin:

    ```java
    super.onStart();
    Button sendPush = (Button) findViewById(R.id.sendbutton);
    sendPush.setEnabled(false);
    ```
9. Ardından, **oturum açma** düğmesine tıklayıp olay ve anında iletme bildirimleri göndermek için aşağıdaki yöntemleri ekleyin.

    ```java
    public void login(View view) throws UnsupportedEncodingException {
        this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

        final Context context = this;
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    String regid = gcm.register(NotificationSettings.SenderId);
                    registerClient.register(regid, new HashSet<String>());
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to register", e.getMessage());
                    return e;
                }
                return null;
            }

            protected void onPostExecute(Object result) {
                Button sendPush = (Button) findViewById(R.id.sendbutton);
                sendPush.setEnabled(true);
                Toast.makeText(context, "Logged in and registered.",
                        Toast.LENGTH_LONG).show();
            }
        }.execute(null, null, null);
    }

    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
        return basicAuthHeader;
    }

    /**
        * This method calls the ASP.NET WebAPI backend to send the notification message
        * to the platform notification service based on the pns parameter.
        *
        * @param pns     The platform notification service to send the notification message to. Must
        *                be one of the following ("wns", "gcm", "apns").
        * @param userTag The tag for the user who will receive the notification message. This string
        *                must not contain spaces or special characters.
        * @param message The notification message string. This string must include the double quotes
        *                to be used as JSON content.
        */
    public void sendPush(final String pns, final String userTag, final String message)
            throws ClientProtocolException, IOException {
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    String uri = BACKEND_ENDPOINT + "/api/notifications";
                    uri += "?pns=" + pns;
                    uri += "&to_tag=" + userTag;

                    HttpPost request = new HttpPost(uri);
                    request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                    request.setEntity(new StringEntity(message));
                    request.addHeader("Content-Type", "application/json");

                    HttpResponse response = new DefaultHttpClient().execute(request);

                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        DialogNotify("MainActivity - Error sending " + pns + " notification",
                            response.getStatusLine().toString());
                        throw new RuntimeException("Error sending notification");
                    }
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                    return e;
                }

                return null;
            }
        }.execute(null, null, null);
    }
    ```

    **Oturum aç** düğmesi için `login` işleyicisi, giriş Kullanıcı adı ve parolası (kimlik doğrulama şemanızın kullandığı belirteci temsil eder) kullanarak temel bir kimlik doğrulama belirteci oluşturur, ardından arka ucunu kayıt için çağırmak üzere `RegisterClient` kullanır.

    `sendPush` yöntemi, kullanıcı etiketine dayalı olarak kullanıcıya güvenli bir bildirim tetiklemek için arka ucu çağırır. `sendPush` tarafından hedeflenen platform bildirim hizmeti, geçirilen `pns` dizesine bağlıdır.

10. `MainActivity` sınıfına aşağıdaki `DialogNotify` yöntemini ekleyin.

    ```java
    protected void DialogNotify(String title, String message)
    {
        AlertDialog alertDialog = new AlertDialog.Builder(MainActivity.this).create();
        alertDialog.setTitle(title);
        alertDialog.setMessage(message);
        alertDialog.setButton(AlertDialog.BUTTON_NEUTRAL, "OK",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog, int which) {
                        dialog.dismiss();
                    }
                });
        alertDialog.show();
    }
    ```
11. `MainActivity` sınıfınızda, aşağıdaki adımları uygulayarak kullanıcının seçtiği platform bildirim hizmetleriyle `sendPush` yöntemini çağırmak için `sendNotificationButtonOnClick` yöntemini güncelleştirin.

    ```java
    /**
    * Send Notification button click handler. This method sends the push notification
    * message to each platform selected.
    *
    * @param v The view
    */
    public void sendNotificationButtonOnClick(View v)
            throws ClientProtocolException, IOException {

        String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                .getText().toString();
        String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                .getText().toString();

        // JSON String
        nhMessage = "\"" + nhMessage + "\"";

        if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
        {
            sendPush("wns", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
        {
            sendPush("gcm", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
        {
            sendPush("apns", nhMessageTag, nhMessage);
        }
    }
    ```
12. @No__t-0 dosyasında, `buildTypes` bölümünden sonraki `android` bölümüne aşağıdaki satırı ekleyin.

    ```java
    useLibrary 'org.apache.http.legacy'
    ```
13. Projeyi derleyin.

## <a name="test-the-app"></a>Uygulamayı test edin

1. Android Studio kullanarak bir cihazda veya öykünücüde uygulamayı çalıştırın.
2. Android uygulamasında bir kullanıcı adı ve parola girin. Her ikisi de aynı dize değerine sahip olmalı ve boşluk veya özel karakterler içermemelidir.
3. Android uygulamasında **oturum aç**' a tıklayın. **Oturumun açıldığını ve kaydın yapıldığını** bildiren bir bildirim iletisi görüntülenmesini bekleyin. **Bildirim Gönder** düğmesini etkinleştirir.

    ![][A2]
4. Uygulamayı çalıştırdığınız ve bir kullanıcı kaydettiğiniz tüm platformları etkinleştirmek için iki durumlu düğmelere tıklayın.
5. Bildirim iletisini alan kullanıcının adını girin. Bu kullanıcı, hedef cihazlarda bildirimlere kaydolmalıdır.
6. Kullanıcının anında iletme bildirimi olarak alması için bir ileti girin.
7. **Bildirim Gönder**’e tıklayın.  Eşleşen kullanıcı adı etiketini içeren bir kaydı olan her cihaz, anında iletme bildirimini alır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kayıtlarıyla ilişkili etiketleri olan belirli kullanıcılara anında iletme bildirimleri göndermeyi öğrendiniz. Konum tabanlı anında iletme bildirimleri göndermeyi öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Konum temelli anında iletme bildirimleri gönderme](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png
