---
title: Azure Notification Hubs nedir?
description: Azure Notification Hubs ile anında iletme bildirimi özellikleri eklemeyi öğrenin.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: ba5a329d12735fbddc86ff2e3725a1e7de6d9d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546474"
---
# <a name="what-is-azure-notification-hubs"></a>Azure Notification Hubs nedir?

Azure Notification Hubs, herhangi bir arka uçtan (bulutta veya şirket içi) herhangi bir platforma (iOS, Android, Windows vb.) bildirim göndermenizi sağlayan kullanımı kolay ve ölçeği genişletilmiş bir gönderim altyapısı sağlar. Notification Hubs hem kuruluş hem de tüketici senaryoları için sorunsuzca çalışır. Birkaç senaryo örneği aşağıda verilmiştir:

- Düşük gecikme ile milyonlarca kişiye son dakika haber bildirimleri gönderme.
- İlgili kullanıcı segmentlerine konum temelli kuponlar gönderme.
- Medya/spor/finans/oyun uygulamaları için kullanıcılara veya gruplara etkinliklerle ilgili bildirimler gönderme.
- Müşterilerle etkileşimde bulunmak ve pazarlama yapmak için uygulamalara promosyon içeriği gönderme.
- Yeni iletiler ve iş öğeleri gibi kurumsal olayları kullanıcılara bildirin.
- Çok faktörlü kimlik doğrulaması için kod gönderme.

## <a name="what-are-push-notifications"></a>Anında iletme bildirimleri nedir?

Anında iletme bildirimleri, mobil uygulama kullanıcılarının, genellikle bir mobil cihazdaki açılan veya iletişim kutusunda yer alan belirli bilgileri bilgilendirmesi durumunda, uygulamadan kullanıcıya iletişimin bir biçimidir. Kullanıcılar genellikle iletiyi görüntülemeyi veya kapatmak için seçer; Eski ' nin seçilmesi, bildirime gönderilen mobil uygulamayı açar. Bazı bildirimler, uygulamanın işlemesi ve ne yapacağınıza karar vermek için arka planda sessiz olarak dağıtılır.

Anında iletme bildirimleri, tüketici uygulamalarında uygulama etkileşiminin ve kullanımının artırılması, kurumsal uygulamalarda ise güncel iş bilgilerinin iletilmesi açısından çok önemlidir. Bu, mobil cihazlar için enerji açısından verimli olduğundan, bildirim göndericileri için esnek ve ilgili uygulamalar etkin olmadığında kullanılabilir olan en iyi uygulama iletişimidir.

> [!NOTE]
> Azure Notification Hubs, Internet Protokolü (VOıP) anında iletme bildirimlerini resmi olarak desteklemez; Ancak, [Bu makalede APNs VoIP bildirimlerini Azure Notification Hubs aracılığıyla nasıl kullanabileceğiniz açıklanır](voip-apns.md) .

Birkaç popüler platformda anında iletme bildirimleri hakkında daha fazla bilgi için aşağıdaki konulara bakın:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](/previous-versions/windows/apps/hh779725(v=win.10))

## <a name="how-do-push-notifications-work"></a>Anında iletme bildirimleri nasıl çalışır?

Anında iletme bildirimleri, *Platform Bildirim Sistemleri* (PNS) adlı platforma özgü altyapılar aracılığıyla teslim edilir. Bir cihaza, sağlanmış bir tanıtıcı ile bir ileti teslim etmek için temel gönderme işlevleri sunar ve ortak arabirimine sahip olmaz. Bir uygulamanın Android, iOS ve Windows sürümlerindeki tüm müşterilere bildirim göndermek için, geliştiricinin Apple Anında İletilen Bildirim Servisi (APNS), Firebase Cloud Messaging (FCM) ve Windows Notification hizmeti (WNS) ile ayrı olarak çalışması gerekir.

Yüksek bir düzeyde gönderme işlemi şu şekilde çalışır:

1. Uygulama bir bildirim almak istiyor, bu nedenle uygulamanın çalıştığı hedef platform için PNS ile iletişim kurar ve benzersiz ve geçici bir gönderim tanıtıcısı ister. Tanıtıcı türü sisteme bağlıdır (örneğin, WNS, APNS belirteçleri kullandığında URI 'Leri kullanır).
2. İstemci uygulaması bu tanıtıcıyı uygulama arka ucunda veya sağlayıcıda depolar.
3. Bir anında iletme bildirimi göndermek için, uygulama arka ucu belirli bir istemci uygulamasını hedeflemek üzere tanıtıcıyı kullanarak PNS ile iletişim kurar.
4. PNS, tanıtıcı tarafından belirtilen cihaza bildirimi iletir.

![Anında iletme bildirimi iş akışı](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Anında iletme bildirimlerinin zorlukları

PNS’ler güçlüdür. Bununla birlikte, segmentlere ayrılmış kullanıcılara anında iletme bildirimleri yayımlamak gibi genel anında iletme bildirimi senaryolarını uygulamak için bile uygulama geliştiricisine çok iş bırakır.

Anında iletme bildirimleri göndermek, uygulamanın ana iş mantığı ile ilgisi olmayan karmaşık bir altyapı gerektirir. Bazı altyapı güçlükleri şunlardır:

- **Platform bağımlılığı**
  - Arka uç, farklı platformlardaki cihazlara bildirim göndermek için karmaşık ve donanımdan korumaya yönelik platforma bağımlı mantık gerektirir, ancak PNSes birleştirilmiş değildir.
- **Ölçeklendirme**
  - Her uygulama başlatıldığında cihaz belirteçlerinin her PNS Kılavuzu için yenilenmesi gerekir. Arka uç, belirteçlerin güncel tutulması için büyük miktarda trafik ve veritabanı erişimi ile ilgilidir. Cihaz sayısı yüzlerce, binlerce veya milyonlarca büyüdüğünde, bu altyapıyı oluşturma ve sürdürme maliyeti çok büyük olur.
  - Çoğu PNS, birden fazla cihaza yayın yapmayı desteklemez. Bir milyon cihaza basit bir yayın yapılması, PNS’lere yönelik bir milyon çağrı ile sonuçlanır. Bu miktarda trafiğin en düşük gecikme ile ölçeklendirilmesi sıradan bir işlem değildir.
- **Yönlendirme**
  - PNSes cihazları cihazlara göndermek için bir yol sağlar, ancak çoğu uygulama bildirimi kullanıcılara veya ilgi alanı gruplarına yöneliktir. Arka uç, cihazları ilgili gruplar, kullanıcılar, özellikler vb. ile ilişkilendirmek için bir kayıt defteri saklanması gerekir. Bu ek yük, bir uygulamanın pazarlamak ve bakım maliyetleri için zaman kazandırır.

## <a name="why-use-azure-notification-hubs"></a>Azure Notification Hubs neden kullanılır?

Notification Hubs, uygulama arka ucunuzdaki kendi kendinize anında iletme bildirimleri gönderme ile ilişkili tüm karmaşıklıkları ortadan kaldırır. Çok platformlu, ölçeği genişletilmiş anında iletme bildirimi altyapısı, gönderme ile ilgili kodlama işlemlerini azaltır ve arka ucunuzu basitleştirir. Notification Hubs sayesinde cihazlar yalnızca PNS tanıtıcılarını bir hub’a kaydetmekten sorumludur. Arka uç ise aşağıdaki şekilde gösterildiği gibi kullanıcılara veya ilgili alanı gruplarına iletiler gönderir:

![Bildirim Hub'ı diyagramı](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs, kullanıma hazırlamış itme motorunuz aşağıdaki avantajları sağlar:

- **Platformlar arası**
  - Tüm büyük gönderim platformları için destek.
  - Platforma özel bir iş olmaksızın platforma özgü ya da platforma bağımlı biçimlerle tüm platformlara gönderebilen bir ortak arabirimdir.
  - Cihaz tanıtıcısı yönetimi tek yerden yürütülür.
- **Arka uçlar arası**
  - Bulut veya şirket içi.
  - .NET, Node.js, Java, Python vb.
- **Zengin teslim düzeni kümesi**
  - Bir veya daha fazla platforma yayın: tek bir API çağrısıyla platformlar arasında milyonlarca cihaza anında yayın yapabilirsiniz.
  - Cihaza gönderme: Bildirimlerle tek cihazları hedefleyebilirsiniz.
  - Kullanıcıya gönder: Etiketler ve şablonlar, bir kullanıcı için tüm platformlar arası cihazlara ulaşmanıza yardımcı olur.
  - Dinamik etiketlerle kesime gönder: Etiketler özelliği, cihazları segmentlere ayırmak ve gereksinimlerinize göre bunlara gönderim yapmanıza yardımcı olur (örneğin, etkin ve Seattle 'da yeni olmayan bir kullanıcı değil). Yayımla-abone ol ile sınırlı olmak yerine, her yerde ve dilediğiniz zaman cihaz etiketlerini güncelleştirebilirsiniz.
  - Yerelleştirilmiş gönderim: şablonlar özelliği, arka uç kodunu etkilemeden yerelleştirme elde etmeye yardımcı olur.
  - Sessiz gönderme: Cihazlara sessiz bildirimler göndererek ve belirli çekme veya eylemleri tamamlamak üzere cihazları tetikleyerek gönderme-çekme düzenini etkinleştirebilirsiniz.
  - Zamanlanmış gönderim: her zaman gönderilmek üzere bildirimler zamanlayabilirsiniz.
  - Doğrudan gönderme: Notification Hubs hizmetine cihaz kaydetme işlemini atlayabilir ve doğrudan bir cihaz tanıtıcıları listesine toplu gönderim yapabilirsiniz.
  - Kişiselleştirilmiş gönderim: cihaz gönderme değişkenleri, özelleştirilmiş anahtar-değer çiftleri ile cihaza özel kişiselleştirilmiş anında iletme bildirimleri göndermenize yardımcı olur.
- **Zengin telemetri**
  - Genel gönderim, cihaz, hata ve işlem telemetrisi hem Azure portal hem de programlama yoluyla kullanılabilir.
  - İleti başına telemetri, ilk istek çağrınızdan gelen her gönderimi, Notification Hubs hizmetine, gönderimleri başarıyla gönderen bir şekilde izler.
  - Platform Bildirim Sistemi geri bildirim, hata ayıklamaya yardımcı olmak için PNSes 'teki tüm geri bildirimleri iletişim kurar
- **Ölçeklenebilirlik**
  - Ya da cihaz parçalama olmadan milyonlarca cihaza hızlı iletiler gönderin.
- **Güvenlik**
  - Paylaşılan Erişim Gizli Dizisi (SAS) veya şirket dışı kimlik doğrulaması.

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: Mobil uygulamalara anında iletme bildirimleri gönderme](notification-hubs-android-push-notification-google-fcm-get-started.md) bölümünü takip ederek bildirim hub’ı oluşturmaya ve kullanmaya başlayın.

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: ./notification-hubs-push-notification-fixer.md
[Android]: ./notification-hubs-android-push-notification-google-gcm-get-started.md
[Windows Universal]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Windows Phone]: ./notification-hubs-windows-mobile-push-notifications-mpns.md
[Kindle]: ./notification-hubs-android-push-notification-google-fcm-get-started.md
[Xamarin.iOS]: ./xamarin-notification-hubs-ios-push-notification-apns-get-started.md
[Xamarin.Android]: ./xamarin-notification-hubs-push-notifications-android-gcm.md
[Microsoft.WindowsAzure.Messaging.NotificationHub]: /previous-versions/azure/reference/dn339221(v=azure.100)
[Microsoft.ServiceBus.Notifications]: /previous-versions/azure/
[App Service Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
