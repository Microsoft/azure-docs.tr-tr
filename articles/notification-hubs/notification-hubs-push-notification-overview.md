---
title: Azure Notification Hubs nedir?
description: Azure Notification Hubs ile anında iletme bildirimi özellikleri eklemeyi öğrenin.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 04/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: f92c9ac6942bfad8df57bb3887d1ba5b7c7190af
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213114"
---
# <a name="what-is-azure-notification-hubs"></a>Azure Notification Hubs nedir?

Azure Notification Hubs, herhangi bir arka uçtan (bulut ya da şirket içi) herhangi bir platforma (iOS, Android, Windows, Kindle, Baidu vb.) bildirim göndermenize olanak tanıyan, kullanımı kolay ve ölçeği artırılmış bir gönderme altyapısı sağlar. Notification Hubs hem kuruluş hem de tüketici senaryoları için sorunsuzca çalışır. Birkaç senaryo örneği aşağıda verilmiştir:

- Düşük gecikme ile milyonlarca kişiye son dakika haber bildirimleri gönderme.
- İlgili kullanıcı segmentlerine konum temelli kuponlar gönderme.
- Medya/spor/finans/oyun uygulamaları için kullanıcılara veya gruplara etkinliklerle ilgili bildirimler gönderme.
- Müşterilerle etkileşimde bulunmak ve pazarlama yapmak için uygulamalara promosyon içeriği gönderme.
- Yeni iletiler ve iş öğeleri gibi kurumsal olaylar hakkında kullanıcılara bildirim gönderme.
- Çok faktörlü kimlik doğrulaması için kod gönderme.

## <a name="what-are-push-notifications"></a>Anında iletme bildirimleri nedir?

Anında iletme bildirimleri, mobil uygulama kullanıcılarının, genellikle bir mobil cihazdaki açılan veya iletişim kutusunda bulunan belirli bilgilere göre bilgilendirmesi durumunda, uygulamadan kullanıcıya iletişimin bir biçimidir. Kullanıcılar genellikle iletiyi görüntülemeyi veya kapatmak için seçer; Eski ' nin seçilmesi, bildirime gönderilen mobil uygulamayı açar. Bazı bildirimler, uygulamanın arka planda işlem yapması ve ne yapacağınıza karar vermesini sağlamak için arka planda sessiz bir şekilde dağıtılır.

Anında iletme bildirimleri, tüketici uygulamalarında uygulama etkileşiminin ve kullanımının artırılması, kurumsal uygulamalarda ise güncel iş bilgilerinin iletilmesi açısından çok önemlidir. Bu, mobil cihazlar için enerji açısından verimli olduğundan, bildirim göndericileri için esnek ve ilgili uygulamalar etkin olmadığında kullanılabilir olan en iyi uygulama iletişimidir.

Birkaç popüler platformda anında iletme bildirimleri hakkında daha fazla bilgi için aşağıdaki konulara bakın:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Anında iletme bildirimleri nasıl çalışır?

Anında iletme bildirimleri, *Platform Bildirim Sistemleri* (PNS) adlı platforma özgü altyapılar aracılığıyla teslim edilir. Bunlar, sağlanan tanıtıcıyla bir cihaza ileti teslim etmek için temel gönderme işlevleri sunarlar ve ortak bir arabirimleri yoktur. Bir uygulamanın Android, iOS ve Windows sürümlerindeki tüm müşterilere bildirim göndermek için, geliştiricinin Apple Anında İletilen Bildirim Servisi (APNS), Firebase Cloud Messaging (FCM) ve Windows Notification hizmeti (WNS) ile birlikte çalışması gerekir.

Yüksek bir düzeyde gönderme işlemi şu şekilde çalışır:

1. Uygulama, bildirim almak istiyor. bu nedenle, uygulamanın çalıştığı hedef platform için PNS ile iletişim kurar ve benzersiz ve geçici bir anında iletme tanıtıcısı ister. Tanıtıcı türü sisteme bağlıdır (örneğin, WNS, APNS belirteçleri kullandığında URI 'Leri kullanır).
2. İstemci uygulaması bu tanıtıcıyı uygulama arka ucunda veya sağlayıcıda depolar.
3. Bir anında iletme bildirimi göndermek için, uygulama arka ucu belirli bir istemci uygulamasını hedeflemek üzere tanıtıcıyı kullanarak PNS ile iletişim kurar.
4. PNS, tanıtıcı tarafından belirtilen cihaza bildirimi iletir.

![Anında iletme bildirimi iş akışı](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Anında iletme bildirimlerinin zorlukları

PNS’ler güçlüdür. Bununla birlikte, segmentlere ayrılmış kullanıcılara anında iletme bildirimleri yayımlamak gibi genel anında iletme bildirimi senaryolarını uygulamak için bile uygulama geliştiricisine çok iş bırakır.

Anında iletme bildirimlerinin gönderilmesi için, uygulamanın ana iş mantığıyla ilgili olmayan karmaşık bir altyapı gerekir. Altyapıyla ilgili zorluklardan bazıları şunlardır:

- **Platform bağımlılığı**
  - Arka uç, çeşitli platformlarda cihazlara bildirim göndermek için karmaşık ve donanımdan korumaya yönelik platforma bağımlı mantık gerektirir.
- **Ölçeklendirme**
  - PNS yönergelerine göre, uygulama her başlatıldığında cihaz belirteçlerinin yenilenmesi gerekir. Arka uç, belirteçlerin güncel tutulması için büyük miktarda trafik ve veritabanı erişimi ile ilgilidir. Cihaz sayısı yüzlerce, binlerce veya milyonlarca büyüdüğünde, bu altyapıyı oluşturma ve sürdürme maliyeti çok büyük olur.
  - Çoğu PNS, birden fazla cihaza yayın yapmayı desteklemez. Bir milyon cihaza basit bir yayın yapılması, PNS’lere yönelik bir milyon çağrı ile sonuçlanır. Bu miktarda trafiğin en düşük gecikme ile ölçeklendirilmesi sıradan bir işlem değildir.
- **Yönlendirme**
  - PNS’ler cihazlara iletileri göndermek için bir yol sağlasa da, çoğu uygulama bildirimi kullanıcılara veya ilgi alanı gruplarına yöneliktir. Cihazları ilgi alanı grupları, kullanıcılar, özellikler vb. ile ilişkilendirmek için arka ucun bir kayıt defteri tutması gerekir. Bu ek yük, bir uygulamanın pazara sunum süresine ve bakım maliyetlerine eklenir.

## <a name="why-use-azure-notification-hubs"></a>Azure Notification Hubs neden kullanılır?

Notification Hubs, uygulama arka ucunuzdaki bildirimleri kendi kendinize dağıtmaya ilişkin tüm karmaşıklıkları ortadan kaldırır. Çok platformlu, ölçeği genişletilmiş anında iletme bildirimi altyapısı, gönderme ile ilgili kodlama işlemlerini azaltır ve arka ucunuzu basitleştirir. Notification Hubs sayesinde cihazlar yalnızca PNS tanıtıcılarını bir hub’a kaydetmekten sorumludur. Arka uç ise aşağıdaki şekilde gösterildiği gibi kullanıcılara veya ilgili alanı gruplarına iletiler gönderir:

![Bildirim Hub'ı diyagramı](./media/notification-hubs-overview/notification-hub-diagram.png)

Bildirim hub'ları, aşağıdaki avantajlara sahip olan kullanıma hazır gönderme altyapınızdır:

- **Platformlar arası**
  - iOS, Android, Windows ve Kindle ile Baidu dahil olmak üzere başlıca tüm gönderme platformlarını destekler.
  - Platforma özel bir iş olmaksızın platforma özgü ya da platforma bağımlı biçimlerle tüm platformlara gönderebilen bir ortak arabirimdir.
  - Cihaz tanıtıcısı yönetimi tek yerden yürütülür.
- **Arka uçlar arası**
  - Bulut veya şirket içi
  - .NET, Node. js, Java, Python, vb.
- **Zengin teslim düzeni kümesi**
  - Bir veya birden çok platforma yayın: Tek bir API çağrısıyla platformlar arasında milyonlarca cihaza anında yayın yapabilirsiniz.
  - Cihaza gönder: Bildirimleri ayrı cihazlara hedefleyebilirsiniz.
  - Kullanıcıya gönder: Etiketler ve şablonlar özellikleri bir kullanıcının tüm platformlar arası cihazlarına ulaşmanıza yardımcı olur.
  - Dinamik etiketlerle kesime gönder: Etiketler özelliği, cihazları segmentlere ayırmak ve gereksinimlerinize göre bunlara gönderim yapmanıza yardımcı olur (örneğin, etkin ve Seattle 'da yeni olmayan bir kullanıcı değil). Pub-sub ile kısıtlanmadan cihaz etiketlerini dilediğiniz yerde ve dilediğiniz zaman güncelleştirebilirsiniz.
  - Yerelleştirilmiş gönderme: Şablonlar özelliği, arka uç kodunu etkilemeden yerelleştirme elde etmenize yardımcı olur.
  - Sessiz gönderim: Cihazlara sessiz bildirimler göndererek ve bunları belirli bir çekme veya eylemleri tamamlamaya yönelik olarak tetikleyerek, gönderim düzenine izin verebilirsiniz.
  - Zamanlanmış gönderim: Her zaman bildirim gönderilmesini zamanlayabilirsiniz.
  - Doğrudan gönderim: Notification Hubs hizmetiyle cihazları kaydetmeyi atlayabilir ve cihaz tanıtıcıları listesine doğrudan toplu gönderim yapabilirsiniz.
  - Kişiselleştirilmiş gönderim: Cihaz gönderme değişkenleri, özelleştirilmiş anahtar-değer çiftleri ile cihaza özel kişiselleştirilmiş anında iletme bildirimleri göndermenize yardımcı olur.
- **Zengin telemetri**
  - Azure portalında ve program aracılığıyla genel gönderme, cihaz, hata ve işlem telemetrileri kullanılabilir.
  - İleti Başına Telemetri, yaptığınız ilk istek çağrısından Notification Hubs hizmetine kadar her bir gönderme işlemini takip eder ve gönderme işlemleri başarıyla toplu hale getirir.
  - Platform Bildirim Sistemi Geri Bildirimi, hata ayıklamaya yardımcı olmak üzere Platform Bildirim Sistemi’nden alınan tüm geri bildirimleri iletir.
- **Ölçeklenebilirlik**
  - Yeniden tasarlama veya cihaz parçalaması yapmadan milyonlarca cihaza hızlı iletiler gönderin.
- **Güvenlik**
  - Paylaşılan Erişim Gizli Dizisi (SAS) veya şirket dışı kimlik doğrulaması.

## <a name="next-steps"></a>Sonraki adımlar

[Öğreticiyi izleyerek bir Bildirim Hub 'ı oluşturma ve kullanma ile çalışmaya başlayın: Mobil uygulamalara](notification-hubs-android-push-notification-google-fcm-get-started.md)anında iletme bildirimleri gönderin.

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: https://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: https://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: https://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: https://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
