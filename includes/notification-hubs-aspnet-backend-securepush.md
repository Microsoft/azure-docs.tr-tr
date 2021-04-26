---
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/14/2020
ms.author: sethm
ms.openlocfilehash: fb3c95b74128f1da7b29a290e17fefe21987dd76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019437"
---
## <a name="webapi-project"></a>WebAPI projesi

1. Visual Studio 'da, **kullanıcıları bilgilendir** öğreticisinde oluşturduğunuz **apparka uç** projesini açın.
2. Notifications. cs ' de, tüm **bildirim** sınıfını aşağıdaki kodla değiştirin. Yer tutucuları, Bildirim Hub 'ınız ve hub adı için bağlantı dizeniz (tam erişim ile) değiştirdiğinizden emin olun. Bu değerleri [Azure Portal](https://portal.azure.com)elde edebilirsiniz. Bu modül artık gönderilecek farklı güvenli bildirimleri temsil eder. Tüm bir uygulamada, bildirimler bir veritabanında depolanır; kolaylık olması için, bu durumda bunları bellekte depolarız.

   ```csharp
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
    ```

3. NotificationsController. cs dosyasında, **notificationscontroller** sınıf tanımının içindeki kodu aşağıdaki kodla değiştirin. Bu bileşen, cihazın bildirimi güvenli bir şekilde alması için bir yol uygular ve ayrıca cihazlarınıza güvenli bir gönderim tetiklemesi için bir yol (Bu öğreticinin amaçları doğrultusunda) sağlar. Bildirimi Bildirim Hub 'ına gönderirken yalnızca bildirimin KIMLIĞIYLE (gerçek ileti olmadan) ham Bildirim gönderdiğimiz unutulmamalıdır:

   ```csharp
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
    ```

`Post`Yöntemin şimdi bir bildirim göndermediğini unutmayın. Yalnızca bildirim KIMLIĞINI içeren ve hassas içerik olmayan ham bir bildirim gönderir. Ayrıca, hata oluşmasına yol açacak şekilde, Bildirim Hub 'ında kimlik bilgilerinizin yapılandırılmadığı platformlar için gönderme işlemine yorum yaptığınızdan emin olun.

1. Şimdi bu uygulamayı tüm cihazlardan erişilebilir hale getirmek için bir Azure Web sitesine yeniden dağıtacağız. **AppBackend** projesine sağ tıklayıp **Yayımla**’yı seçin.
2. Yayımlama hedefi olarak Azure Web sitesini seçin. Azure hesabınızla oturum açın ve var olan veya yeni bir Web sitesini seçin ve **bağlantı** SEKMESINDE **hedef URL** özelliğini bir yere göz önüne alın. Bu öğreticide daha sonra *arka uç uç* noktanız olarak bu URL 'ye başvuracağız. **Yayımla**’ya tıklayın.
