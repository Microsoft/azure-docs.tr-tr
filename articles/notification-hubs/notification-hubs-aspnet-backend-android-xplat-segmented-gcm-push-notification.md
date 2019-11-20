---
title: Azure Notification Hubs ve Google Cloud Messaging kullanarak belirli Android cihazlara bildirim gönderin | Microsoft Docs
description: Azure Notification Hubs ve Google Cloud Messaging kullanarak belirli Android cihazlara anında iletme bildirimleri göndermek için Notification Hubs kullanma hakkında bilgi edinin.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
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
ms.openlocfilehash: aa2aa7b77bf174f4a2ca89408ee037b398a32e4d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387439"
---
# <a name="tutorial-push-notifications-to-specific-android-devices-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Öğretici: Azure Notification Hubs ve Google Cloud Messaging (kullanım dışı) kullanarak belirli Android cihazlara anında Iletme bildirimleri gönderin

> [!WARNING]
> 10 Nisan 2018 itibariyle, Google Google Cloud Messaging (GCM) kullanım dışıdır. GCM sunucusu ve istemci API 'Leri kullanım dışıdır ve 29 Mayıs 2019 ' den hemen sonra kaldırılacaktır. Daha fazla bilgi için bkz. [GCM ve FCM hakkında sık sorulan sorular](https://developers.google.com/cloud-messaging/faq).

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Genel Bakış

Bu öğreticide, bir Android uygulamasında son dakika haber bildirimleri yayınlamak için Azure Notification Hubs'ın nasıl kullanılacağı gösterilir. Bu öğreticiyi tamamladığınızda, ilginizi çeken son dakika haberi kategorilerine kaydolabilecek ve yalnızca bu kategoriler için anında iletme bildirimleri alabileceksiniz. Bu senaryo, daha önce ilgisini belirtmiş kullanıcı gruplarına bildirim gönderilmesi gereken RSS okuyucu, müzik hayranlarına yönelik uygulamalar vb. birçok uygulama için ortak düzendir.

Yayın senaryoları, bildirim hub’ında bir kayıt oluştururken bir veya daha fazla *etiket* dahil edilerek etkinleştirilir. Bir etikete bildirimler gönderildiğinde, etikete kaydolan tüm cihazlar bildirimi alır. Etiketler basitçe birer dize olduğundan, önceden hazırlanması gerekmez. Etiketler hakkında daha fazla bilgi için bkz. [Notification Hubs Yönlendirme ve Etiket İfadeleri](notification-hubs-tags-segment-push-message.md).

Bu öğreticide, aşağıdaki eylemleri gerçekleştireceksiniz:

> [!div class="checklist"]
> * Mobil uygulamaya kategori seçimi ekleme.
> * Etiketlerle bildirimler için kaydedilir.
> * Etiketli bildirimler gönderme.
> * Uygulamayı test edin

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici, [öğretici: Azure Notification Hubs ve Google Cloud Messaging kullanarak Android cihazlarına anında iletme bildirimleri aracılığıyla][get-started]oluşturduğunuz uygulamayı oluşturur. Bu öğreticiye başlamadan önce [öğreticiyi izleyin: Azure Notification Hubs ve Google Cloud Messaging kullanarak Android cihazlarına anında iletme bildirimleri gönderin][get-started].

## <a name="add-category-selection-to-the-app"></a>Uygulamaya kategori seçimi ekleme

İlk adım, mevcut ana etkinliğinize kullanıcının kaydolunacak kategorileri seçmesini sağlayan UI öğeleri eklemektir. Bir kullanıcı tarafından seçilen kategoriler cihazda depolanır. Uygulama başlatıldığında, etiketler olarak seçilen kategorilerle bildirim hub’ınızda bir cihaz kaydı oluşturulur.

1. @No__t-0 ' ı açın ve içeriği aşağıdakiler ile değiştirin:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. @No__t-0 dosyasını açın ve aşağıdaki satırları ekleyin:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    @No__t 0 grafik düzeniniz aşağıdaki görüntüde şöyle görünmelidir:

    ![][A1]
3. @No__t-1 sınıfınız ile aynı pakette `Notifications` sınıfı oluşturun.

    ```java
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName,
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM,
                            categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

    }
    ```

    Bu sınıf, bu cihazın alması gereken haber kategorilerini depolamak için yerel depolamayı kullanır. Ayrıca, bu kategoriler için kaydolma yöntemlerini içerir.
4. @No__t-0 sınıfında, `NotificationHub` ve `GoogleCloudMessaging` için özel alanlarınızı kaldırın ve `Notifications` için bir alan ekleyin:

    ```java
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. Ardından, `onCreate` yönteminde `hub` alanının ve `registerWithNotificationHubs` yönteminin başlatılmasını kaldırın. Ardından, `Notifications` sınıfının bir örneğini başlatmak için aşağıdaki satırları ekleyin.

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Hub adı ve bağlantı dizesinin NotificationSettings sınıfında düzgün şekilde ayarlandığını onaylayın.

    > [!NOTE]
    > Bir istemci uygulaması ile dağıtılmış kimlik bilgileri genellikle güvenli olmadığından yalnızca istemci uygulamanızla dinleme erişimi için anahtarı dağıtmanız gerekir. Dinleme erişimi, uygulamanızın bildirimlere kaydolmasını sağlar, ancak mevcut kayıtlar değiştirilemez ve bildirimler gönderilemez. Tam erişim anahtarı, güvenli bir arka uç hizmetinde bildirimler göndermek ve mevcut kayıtları değiştirmek için kullanılır.

6. Ardından, aşağıdaki içeri aktarma işlemlerini ekleyin:

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
7. Abone ol düğmesine tıklama olayını işlemek için aşağıdaki `subscribe` yöntemini ekleyin:

    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```

    Bu yöntem, kategorilerin bir listesini oluşturur ve listeyi yerel depolama alanında depolamak ve karşılık gelen etiketleri Bildirim Hub 'ınızla kaydettirmek için `Notifications` sınıfını kullanır. Kategoriler değiştirildiğinde kayıt yeni kategorilerle yeniden oluşturulur.

Uygulamalarınız artık bir kategori kümesini cihazdaki yerel depolama alanında depolayabilir ve kullanıcının kategori seçimini her değiştirmesinde bildirim hub’ına kaydolabilir.

## <a name="register-for-notifications"></a>Bildirimlere kaydolma

Bu adımlar, yerel depolama alanında depolanan kategorileri kullanarak başlatma sırasında bildirim hub’ına kaydolur.

> [!NOTE]
> Google Cloud Messaging (GCM) tarafından atanan registrationId değeri her zaman değişebileceğinden, bildirim hatalarını önlemek için sık sık bildirimlere kaydolmanız gerekir. Bu örnek, uygulama her başlatıldığında bildirimlere kaydolur. Sık sık çalıştırılan uygulamalar için, önceki kayıttan bu yana bir günden az zaman geçtiyse bant genişliğini korumak için günde birkaç kere kaydı atlayabilirsiniz.

1. @No__t-1 sınıfındaki `onCreate` yönteminin sonuna aşağıdaki kodu ekleyin:

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    Bu kod, uygulama her başlatıldığında uygulamanın yerel depolama alanından kategorileri aldığından ve bu kategorilere kayıt isteğinde bulunduğundan emin olur.
2. Ardından `MainActivity` sınıfının `onStart()` yöntemini aşağıdaki gibi güncelleştirin:

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```

    Bu kod, önceden kaydedilmiş kategorilerin durumuna göre ana etkinliği güncelleştirir.

Uygulama artık tamamlanmıştır ve kullanıcının kategori seçimini her değiştirmesinde bildirim hub’ına kaydolmak için kullanılan cihazın yerel depolama alanında bir kategori kümesini depolayabilir. Daha sonra bu uygulamaya kategori bildirimleri gönderebilen bir arka uç tanımlayın.

## <a name="send-tagged-notifications"></a>Etiketli bildirimler gönderme

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Uygulamayı test edin

1. Android Studio'da, Android cihazınız veya öykünücünüz üzerinde uygulamayı çalıştırın. Uygulama kullanıcı arabirimi, abone olunacak kategorileri seçmenize olanak sağlayan iki durumlu düğmeler sağlar.
2. Bir veya daha fazla kategori iki durumlu düğmesini etkinleştirin ve **Abone ol**’a tıklayın. Uygulama, seçilen kategorileri etiketlere dönüştürür ve bildirim hub’ından seçilen etiketler için yeni bir cihaz kaydı ister. Kayıtlı kategoriler döndürülür ve bir bildirimde görüntülenir.

    ![Kategorilere abone olma](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. Her kategori için bildirim gönderen .NET konsol uygulamasını açın. Seçili kategorilere ait bildirimler, bildirim olarak görünür.

    ![Teknoloji haberi bildirimleri](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kategorilere kaydolmuş belirli Android cihazlara yayın bildirimleri gönderdiniz. Belirli kullanıcılara nasıl anında iletme bildirimleri gönderileceğini öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Belirli kullanıcılara anında iletme bildirimleri gönderme](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
