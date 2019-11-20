---
title: Visual Studio App Center ve Azure Notification Hubs mobil uygulamalarınızda anında iletme bildirimlerinin önemi
description: Mobil uygulama kullanıcılarınızla çalışabilmenizi sağlayan App Center gibi hizmetler hakkında bilgi edinin.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795919"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Anında iletme bildirimleri göndererek uygulama kullanıcılarınıza katılın 

Tüketici veya kurumsal uygulamalar oluşturuyor olmanız, kullanıcıların uygulamanızı etkin bir şekilde kullanmasını ister. Son haberleri, oyun güncelleştirmelerini, heyecan verici teklifleri, ürün güncelleştirmesini veya kullanıcılarınıza ilişkin herhangi bir bilgiyi paylaşmak istediğinizde genellikle saatler vardır. Kullanıcılarınıza yönelik katılımı artırmak ve bunları uygulamanıza geri almak için, kullanıcılarınız ile gerçek zamanlı olarak iletişim kurmanızı sağlamak için bir yol gerekir.

Anında iletme bildirimleri, uygulama kullanıcılarınız ile iletişim kurmanın hızlı ve verimli bir yolunu sağlar. Kullanıcılarınıza doğru zamanda ulaşabilirsiniz ve bu kullanıcılara, genellikle bir mobil cihazdaki açılan veya iletişim kutusunda, ne yapacaklarından bağımsız olarak, istenen bilgileri bildirebilirler.

## <a name="value-of-push-notifications"></a>Anında iletme bildirimlerinin değeri
Anında iletme bildirimleri hem son kullanıcılara hem de işletmeye değer sağlar.

İşletmeler şunları yapabilir:
- Kullanıcıları, doğru zamanda desteklenen platforma göndererek **, kendi kullanıcılarıyla doğrudan Iletişim kurun** .
- Kullanıcılarınıza gerçek zamanlı güncelleştirmeler ve anımsatıcılar göndererek **kullanıcı etkileşimini** güçlendirin, bu kullanıcılara düzenli olarak uygulama teşvik.
- **Kullanıcıları koruyun** ve uygulamayı indirmiş ancak tekrar etkin hale gelmesi için kullanmayan etkin olmayan kullanıcılarla yeniden devreye geçin.
- Son Kullanıcı davranışını çözümleyerek, son kullanıcıları bölümleyerek ve mobil anında iletme bildirimlerine göre kampanyaları kullanarak **dönüştürme oranlarını artırın** .
- Kullanıcılara anında iletme iletileri ve zamanında güncelleştirmeler göndererek **ürün ve Hizmetleri yükseltin** .
- Kullanıcılara kişiselleştirilmiş anında iletme iletileri göndererek **kullanıcıları hedefleyin** .

Uygulama kullanıcıları için anında iletme bildirimleri:
- Gerçek zamanlı olarak **değer ve bilgi sağlayın** .
- **Kullanıcılara uygulamayı kullanmasını hatırlatın** .

Mobil uygulamalarınızda anında iletme bildirimlerini etkinleştirmek için aşağıdaki hizmetleri kullanın.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Push](/appcenter/push/) hizmeti, gönderim bildirim hizmetleri (PNS) kullanarak cihazlara bildirim gönderme işlemini yönetmek zorunda kalmadan, bunları IOS, Android ve Windows kullanıcılarına hedeflenmiş iletiler göndererek kullanıcılarınıza yönlendirmenize olanak tanır. Azure Notification Hubs üzerine inşa ederek, bu hizmet güçlü bir pano sunarak bildirimleri el ile iletme ile ilişkili karmaşıklıkları ortadan kaldırır.

**Temel Özellikler**
- Çeşitli platformlar arasında **mobil cihazlara anında iletme bildirimleri gönderin** .
- Bir uygulamaya veri göndermek, kullanıcıya bir ileti göstermek veya uygulama tarafından bir eylem tetiklemek için bildirimleri kullanın.
- Bildirim hedefleri: 
    - **Tüm kayıtlı cihazlara**yayın iletileri gönderin.
    - Cihaz bilgilerine ve özel özelliklere göre oluşturulan **hedef kitlelere** bildirimler gönderin.
    - **Belirli kullanıcılara**bildirimler gönderin.
    - **Belirli cihazlara**bildirim gönderin.
- Anında iletme, cihaz, hata üzerinde **zengin telemetri** App Center Portal 'da bulunabilir.
- **Platform desteği** -IOS, Android, MacOS, Xamarin, doğal yerel, Unity, Cordova.

**Başvur**
- [App Center kaydolun](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center Push ile çalışmaya başlama](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) , herhangi bir platforma ve herhangi bir arka uçtan (bulutta veya şirket içi) bildirim göndermenizi sağlayan kullanımı kolay ve ölçeği genişletilmiş bir gönderim altyapısı sağlar.

**Temel Özellikler**
- Bulutta veya şirket içinde herhangi bir arka uçtan herhangi bir platforma **anında iletme bildirimleri gönderin** .
- Tek bir API çağrısıyla milyonlarca mobil cihaza **Hızlı Yayın** gönderme.
- Anında iletme bildirimlerini müşteri, dil ve konuma göre uyarlayın.
- **Müşteri segmentlerini**dinamik olarak tanımlayın ve bilgilendirin.
- Milyonlarca mobil cihaza **anında ölçeklendirin** .
- **Platform desteği** -IOS, Android, Windows, Ille, Baidu.
        
**Başvur**
- [Azure portalda](https://portal.azure.com) 
- [Azure Notification Hubs kullanmaya başlama](/azure/notification-hubs/)   
- [Hızlı başlangıçlar](/azure/notification-hubs/create-notification-hub-portal)
- [Örnekler](/azure/notification-hubs/samples)
