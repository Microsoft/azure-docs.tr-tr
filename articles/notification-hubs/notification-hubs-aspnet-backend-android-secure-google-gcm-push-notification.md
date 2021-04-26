---
title: Azure Notification Hubs güvenli anında iletme bildirimleri gönderin
description: Azure 'dan bir Android uygulamasına güvenli anında iletme bildirimleri göndermeyi öğrenin. Java ve C# dilinde yazılan kod örnekleri.
documentationcenter: android
keywords: anında iletme bildirimi, anında iletme bildirimleri, anında iletme iletileri, Android anında iletme bildirimleri
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f2d5d618fabbe7400ce825f984ace1622a524f05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004021"
---
# <a name="send-secure-push-notifications-with-azure-notification-hubs"></a>Azure Notification Hubs güvenli anında iletme bildirimleri gönderin

> [!div class="op_single_selector"]
> * [Windows Evrensel](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Genel Bakış

> [!IMPORTANT]
> Bu öğreticiyi tamamlamak için etkin bir Azure hesabınızın olması gerekir. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Microsoft Azure anında iletme bildirimi desteği, mobil platformlar için hem tüketici hem de kurumsal uygulamalarda anında iletme bildirimlerinin uygulanmasını büyük ölçüde kolaylaştıran, kullanımı kolay, çok platformlu, ölçeği genişletilmiş bir anında iletme ileti altyapısına erişmenizi sağlar.

Yasal düzenlemeler veya güvenlik kısıtlamaları nedeniyle, bazen bir uygulama, bildirime standart anında iletme bildirimi altyapısı üzerinden aktarılamayan bir şey eklemek isteyebilir. Bu öğreticide, istemci Android cihazı ile uygulama arka ucu arasında güvenli, kimliği doğrulanmış bir bağlantıyla gizli bilgiler göndererek aynı deneyimin nasıl elde edileceğini açıklar.

Yüksek düzeyde, akış şu şekildedir:

- Uygulama arka ucu:
  * Güvenli yükü arka uç veritabanında depolar.
  * Bu bildirimin KIMLIĞINI Android cihazına gönderir (güvenli bilgi gönderilmez).
- Bildirim alırken cihazdaki uygulama:
  * Android cihaz, güvenli yük isteyen arka uca iletişim kurar.
  * Uygulama, yükü cihazda bir bildirim olarak gösterebilir.

Önceki akışta (ve bu öğreticide), Kullanıcı oturum açtıktan sonra cihazın yerel depolamada bir kimlik doğrulama belirteci depoladığını varsayması önemlidir. Bu yaklaşım, cihazın bu belirteci kullanarak bildirimin güvenli yükünü alabilmesi için sorunsuz bir deneyim sağlar. Uygulamanız, kimlik doğrulama belirteçlerini cihazda depolamaz veya bu belirteçlerin kullanım tarihi dolmuşsa, anında iletme bildirimi alındıktan sonra cihaz uygulaması, kullanıcıdan uygulamayı başlatması istenmeden ilgili genel bir bildirim görüntülemelidir. Uygulama daha sonra kullanıcının kimliğini doğrular ve bildirim yükünü gösterir.

Bu öğreticide, güvenli anında iletme bildirimlerinin nasıl gönderileceği gösterilmektedir. [Kullanıcılara bildirme](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) öğreticisini oluşturur, bu nedenle öncelikle söz konusu öğreticideki adımları tamamlamalısınız.

> [!NOTE]
> Bu öğreticide, Bildirim Hub 'ınızı [Notification Hubs (Android) ile çalışmaya başlama](notification-hubs-android-push-notification-google-gcm-get-started.md)bölümünde açıklandığı gibi oluşturduğunuzu ve yapılandırdığınızı varsaymaktadır.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Android projesini değiştirme

Yalnızca bir anında iletme bildiriminin KIMLIĞINI göndermek için uygulamanızın arka ucuna göre değişiklik yapmış olduğunuza göre, Android uygulamanızı bu bildirimi işleyecek şekilde değiştirmeniz ve görüntülenecek güvenli iletiyi almak için arka uca geri çağrı yapmanız gerekir.
Bu hedefe ulaşmak için, Android uygulamanızın anında iletme bildirimleri aldığında arka UCUNUZDAN kimliğini nasıl doğrulayacağını öğrenmeniz gerekir.

Şimdi, kimlik doğrulama üst bilgisi değerini uygulamanızın paylaşılan tercihlerine kaydetmek için oturum açma akışını değiştirin. Benzer mekanizmalar, uygulamanın kullanıcı kimlik bilgileri gerektirmeden kullanması gereken herhangi bir kimlik doğrulama belirtecini (örneğin, OAuth belirteçleri) depolamak için kullanılabilir.

1. Android uygulama projenizde, sınıfının en üstüne aşağıdaki sabitleri ekleyin `MainActivity` :

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```

2. Hala `MainActivity` sınıfında, `getAuthorizationHeader()` yöntemi aşağıdaki kodu içerecek şekilde güncelleştirin:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```

3. Aşağıdaki `import` deyimlerini dosyanın üst kısmına ekleyin `MainActivity` :

    ```java
    import android.content.SharedPreferences;
    ```

Şimdi, bildirim alındığında çağrılan işleyiciyi değiştirin.

1. `MyHandler`Sınıfında `OnReceive()` yöntemi şunu içerecek şekilde değiştirin:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```

2. Ardından, `retrieveNotification()` `{back-end endpoint}` geri tutucuyu dağıtım sırasında elde edilen arka uç bitiş noktasıyla değiştirerek yöntemini ekleyin:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Bu yöntem, paylaşılan tercihlerinde depolanan kimlik bilgilerini kullanarak bildirim içeriğini almak için uygulamanızın arka uca çağrı yapın ve normal bir bildirim olarak görüntüler. Bildirim, uygulama kullanıcısına tam olarak diğer anında iletme bildirimleri gibi bakar.

Eksik kimlik doğrulama üst bilgisi özelliğinin veya arka uç tarafından ret durumlarının işlenmesi tercih edilir. Bu durumların belirli işleme, genellikle hedef Kullanıcı deneyiminize bağlıdır. Bir seçenek, kullanıcının gerçek bildirimi almak için kimlik doğrulaması yapması için bir genel istem ile bir bildirim görüntülemektir.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı çalıştırmak için aşağıdaki eylemleri gerçekleştirin:

1. **Apparka uç** 'nin Azure 'da dağıtıldığından emin olun. Visual Studio kullanıyorsanız, **Apparka uç** Web API uygulamasını çalıştırın. Bir ASP.NET Web sayfası görüntülenir.
2. Çakışan Küreler, uygulamayı fiziksel bir Android cihazda veya Öykünücüde çalıştırın.
3. Android uygulama kullanıcı arabiriminde, bir Kullanıcı adı ve parola girin. Bunlar herhangi bir dize olabilir, ancak aynı değer olmalıdır.
4. Android uygulama kullanıcı arabiriminde **oturum aç**' a tıklayın. Sonra **gönderme gönder**' e tıklayın.
