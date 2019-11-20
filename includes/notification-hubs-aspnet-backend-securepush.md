---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 3086d15ba541aa7f08f983dac4bc363f43248a9e
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74062938"
---
## <a name="webapi-project"></a>WebAPI projesi
1. Visual Studio 'da, **kullanıcıları bilgilendir** öğreticisinde oluşturduğunuz **apparka uç** projesini açın.
2. Notifications.cs ' de, tüm **bildirim** sınıfını aşağıdaki kodla değiştirin. Yer tutucuları, Bildirim Hub 'ınız ve hub adı için bağlantı dizeniz (tam erişim ile) değiştirdiğinizden emin olun. Bu değerleri [Azure Portal](https://portal.azure.com)elde edebilirsiniz. Bu modül artık gönderilecek farklı güvenli bildirimleri temsil eder. Tüm bir uygulamada, bildirimler bir veritabanında depolanır; kolaylık olması için, bu durumda bunları bellekte depolarız.
   
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
                var notification = new Notification
                {
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

1. NotificationsController.cs ' de, **Notificationscontroller** sınıf tanımının içindeki kodu aşağıdaki kodla değiştirin. Bu bileşen, cihazın bildirimi güvenli bir şekilde alması için bir yol uygular ve ayrıca cihazlarınıza güvenli bir gönderim tetiklemesi için bir yol (Bu öğreticinin amaçları doğrultusunda) sağlar. Bildirimi Bildirim Hub 'ına gönderirken yalnızca bildirimin KIMLIĞIYLE (gerçek ileti olmadan) ham Bildirim gönderdiğimiz unutulmamalıdır:
   
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


`Post` yönteminin şimdi bir bildirim göndermediğini unutmayın. Yalnızca bildirim KIMLIĞINI içeren ve hassas içerik olmayan ham bir bildirim gönderir. Ayrıca, hata oluşmasına yol açacak şekilde, Bildirim Hub 'ında kimlik bilgilerinizin yapılandırılmadığı platformlar için gönderme işlemine yorum yaptığınızdan emin olun.

1. Şimdi bu uygulamayı tüm cihazlardan erişilebilir hale getirmek için bir Azure Web sitesine yeniden dağıtacağız. **AppBackend** projesine sağ tıklayıp **Yayımla**’yı seçin.
2. Yayımlama hedefi olarak Azure Web sitesini seçin. Azure hesabınızla oturum açın ve var olan veya yeni bir Web sitesini seçin ve **bağlantı** SEKMESINDE **hedef URL** özelliğini bir yere göz önüne alın. Bu öğreticide daha sonra *arka uç uç* noktanız olarak bu URL 'ye başvuracağız. **Yayımla**’ta tıklayın.

