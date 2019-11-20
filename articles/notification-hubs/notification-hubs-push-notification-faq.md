---
title: 'Azure Notification Hubs: sık sorulan sorular (SSS) | Microsoft Docs'
description: Notification Hubs çözümleri tasarlama/uygulama hakkında SSS
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
editor: jwargo
keywords: anında iletme bildirimi, anında iletme bildirimleri, iOS anında iletme bildirimleri, Android anında iletme bildirimleri, iOS push, Android gönderim
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: c84a4472789430524cbf5ff3f1ae24ea10d342b9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74066870"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Azure Notification Hubs ile anında iletme bildirimleri: sık sorulan sorular

## <a name="general"></a>Genel

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Notification Hubs kaynak yapısı nedir?

Azure Notification Hubs iki kaynak düzeyine sahiptir: hub 'lar ve ad alanları. Hub, bir uygulamanın platformlar arası anında iletme bilgilerini tutabilecek tek bir itme kaynağıdır. Ad alanı, bir bölgedeki hub 'ların koleksiyonudur. Önerilen eşleme bir uygulamayla bir ad alanıyla eşleşir. Bir ad alanı içinde, üretim uygulamanızla birlikte çalışarak test uygulamanız ile birlikte çalışarak test hub 'ı ve benzeri bir üretim hub 'ına sahip olabilirsiniz.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Notification Hubs için fiyat modeli nedir?

En son fiyatlandırma ayrıntıları [Notification Hubs fiyatlandırması] sayfasında bulunabilir. Notification Hubs ad alanı düzeyinde faturalandırılır. (Bir ad alanının tanımı için, "Notification Hubs kaynak yapısı nedir?" başlığına bakın. Notification Hubs üç katman sunar:

* **Ücretsiz**: Bu katman, anında iletme özelliklerini keşfetmek için iyi bir başlangıç noktasıdır. Üretim uygulamaları için önerilmez. Hizmet düzeyi sözleşmesi (SLA) garantisi olmadan, her ay ad alanı başına 500 cihaz ve 1.000.000 gönderim edinirsiniz.
* **Temel**: Bu katman (veya Standart katman), daha küçük üretim uygulamaları için önerilir. Bir temel olarak ayda ad alanı başına 200.000 cihaz ve 10.000.000 gönderim alırsınız.
* **Standart**: Bu katman, orta ve büyük üretim uygulamaları için önerilir. Bir temel olarak ayda ad alanı başına 10.000.000 cihaz ve 10.000.000 gönderim alırsınız. Zengin telemetri (belirtilen gönderim durumu hakkında ek veriler) içerir.

Standart katman özellikleri:

* **Zengin telemetri**: herhangi bir anında iletme isteğini izlemek platform bildirim sistemi ve hata ayıklama Için geri bildirimde bulunmak Için Ileti telemetri başına Notification Hubs kullanabilirsiniz.
* **Çoklu kiracı**: ad alanı düzeyinde platform bildirim sistemi kimlik bilgileriyle çalışabilirsiniz. Bu seçenek, kiracıları aynı ad alanı içinde kolayca hub 'lara bölmenizin olanaklı olmasına olanak sağlar.
* **Zamanlanmış gönderim**: her zaman gönderilmek üzere bildirimler zamanlayabilirsiniz.
* **Toplu işlemler**: kayıtları [Kayıt verme/Içeri aktarma] aktarma belgesinde açıklandığı gibi dışarı aktarma/içeri aktarma işlevlerini sunar.

### <a name="what-is-the-notification-hubs-sla"></a>Notification Hubs SLA nedir?

Temel ve Standart Notification Hubs katmanlarında, düzgün şekilde yapılandırılmış uygulamalar anında iletme bildirimleri gönderebilir veya en az yüzde 99,9 ' luk bir kayıt yönetimi işlemi gerçekleştirebilir. SLA hakkında daha fazla bilgi edinmek için [NOTIFICATION HUBS SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) sayfasına gidin.

> [!NOTE]
> Anında iletme bildirimleri Apple Anında Iletilen bildirim hizmeti (APNs) ve Google 'ın Firebase Cloud Messaging (FCM) gibi üçüncü taraf platform bildirim sistemlerine bağlı olduğundan, bu iletilerin teslim edilmesi için SLA garantisi yoktur. Notification Hubs, toplu işlemleri platform bildirim sistemlerine (SLA garantisi) gönderdikten sonra, bu, gönderimleri teslim etmek için platform bildirim sistemlerinin sorumluluğundadır (SLA garantisi yoktur).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hub veya ad alanını farklı bir katmana yükseltmek veya alçaltma Nasıl yaparım? mı?

**[Azure portal]**  > **Notification Hubs ad alanları** veya **Notification Hubs**gidin. Güncelleştirmek istediğiniz kaynağı seçin ve **fiyatlandırma katmanına**gidin. Aşağıdaki gereksinimleri dikkate alın:

* Güncelleştirilmiş fiyatlandırma katmanı, üzerinde çalıştığınız ad alanındaki *Tüm* hub 'lara uygulanır.
* Cihazınızın sayısı, eski sürümü düşürüyoruz katman sınırını aşarsa, ' ı indirgemeden önce cihazları silmeniz gerekir.

## <a name="design-and-development"></a>Tasarım ve geliştirme

### <a name="which-server-side-platforms-do-you-support"></a>Hangi sunucu tarafı platformları destekliyoruz?

Sunucu SDK 'Ları .NET, Java, Node. js, PHP ve Python için kullanılabilir. Notification Hubs API 'Ler REST arabirimlerini temel alır. bu sayede, farklı platformlar kullanıyorsanız veya ek bağımlılık istemiyorsanız doğrudan REST API 'lerle çalışabilirsiniz. Daha fazla bilgi için [REST API 'Leri Notification Hubs] sayfasına gidin.

### <a name="which-client-platforms-do-you-support"></a>Hangi istemci platformlarını destekliyoruz?

Anında iletme bildirimleri [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (Baidu aracılığıyla)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) ve Android ve [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari)) için desteklenir. Daha fazla bilgi için [Notification Hubs Başlarken öğreticileri] sayfasına gidin.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>SMS mesajı, e-posta veya Web bildirimlerini destekliyor musunuz?

Notification Hubs, mobil uygulamaları çalıştıran cihazlara bildirim gönderir. E-posta veya kısa mesaj işlevleri sağlamaz. Notification Hubs Ayrıca, kutudan çıkan bir tarayıcı içi anında iletme bildirimi teslim hizmeti sağlamaz. Müşteriler, desteklenen sunucu tarafı platformlarının üzerinde SignalR kullanarak bu özelliği uygulayabilir. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Notification Hubs aracılığıyla anında iletme bildirimleri gönderdiğimde, kaç cihaz destekleyebilirim?

Desteklenen cihaz sayısıyla ilgili ayrıntılı bilgi için [Notification Hubs fiyatlandırması] sayfasına bakın.

10.000.000 ' den fazla kayıtlı cihaz için desteğe ihtiyacınız varsa, cihazlarınızı birden çok hub arasında bölümleyebilirsiniz.

### <a name="how-many-push-notifications-can-i-send-out"></a>Kaç anında iletme bildirimi gönderebilirim?

Azure Notification Hubs, seçilen katmana bağlı olarak, sistem üzerinden akan bildirim sayısına göre otomatik olarak ölçeklendirilir.

> [!NOTE]
> Genel kullanım maliyeti, gönderilen anında iletme bildirimlerinin sayısına göre artabilir. [Notification Hubs fiyatlandırması] sayfasında özetlenen katman limitlerinin farkında olduğunuzdan emin olun.

Müşterilerimiz, günlük milyonlarca anında iletme bildirimi göndermek için Notification Hubs kullanır. Azure Notification Hubs kullandığınız sürece anında iletme bildirimlerinizin ulaşmasını ölçeklendirmek için özel bir şey yapmanız gerekmez.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Cihaza erişmek için gönderilen anında iletme bildirimlerinin ne kadar sürer?

Bir normal kullanım senaryosunda, gelen yükün tutarlı olduğu ve hatta Azure Notification Hubs, en az *1.000.000 anında iletme bildiriminin bir dakika göndereceğini*de işleyebilir. Bu hız, etiket sayısına, gelen gönderme yapısına ve diğer dış faktörlere bağlı olarak farklılık gösterebilir.

Tahmini teslimat süresi boyunca hizmet, hedefleri her platform için hesaplar ve iletileri, kaydedilen etiketlere veya etiket ifadelerine göre anında Iletme bildirimi hizmetine (PNS) yönlendirir. Cihaza bildirim göndermek için PNS 'nin sorumluluğundadır.

PNS, bildirim teslim etmek için herhangi bir SLA garantisi vermez. Ancak, çoğu anında iletme bildirimi, hedef cihazlara, birkaç dakika içinde (genellikle 10 dakika içinde), Notification Hubs gönderilen süreden dağıtılır. Birkaç bildirim daha zaman alabilir.

> [!NOTE]
> Azure Notification Hubs, %30 dakika içinde PNS 'ye teslim edilmemiş anında iletme bildirimlerini bırakmaya yönelik bir ilkeye sahiptir. Bu gecikme birkaç nedenden kaynaklanabilir, ancak en yaygın olarak PNS uygulamanızı azaltıyordur.

### <a name="is-there-any-latency-guarantee"></a>Gecikme garantisi var mı?

Anında iletme bildirimlerinin doğası nedeniyle (bunlar harici, platforma özgü bir PNS tarafından teslim edilir), gecikme garantisi yoktur. Genellikle, anında iletme bildirimlerinin çoğu birkaç dakika içinde dağıtılır.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Ad alanları ve Bildirim Hub 'ları ile bir çözüm tasarlarken göz önünde bulundurmanız gerekenler nelerdir?

#### <a name="mobile-appenvironment"></a>Mobil uygulama/ortam

* Her ortam için mobil uygulama başına bir Bildirim Hub 'ı kullanın.
* Çok kiracılı bir senaryoda, her kiracının ayrı bir hub 'ı olmalıdır.
* Üretim ve test ortamları için aynı Bildirim Hub 'ını hiçbir şekilde paylaşmayın. Bu uygulama, bildirim gönderilirken soruna neden olabilir. (Apple, her biri ayrı kimlik bilgileri ile korumalı alan ve üretim anında Iletme noktaları sunar.)
* Varsayılan olarak, Visual Studio 'da Azure portal veya Azure tümleşik bileşeni aracılığıyla kayıtlı cihazlarınıza test bildirimleri gönderebilirsiniz. Eşik, kayıt havuzundan rastgele olarak seçilen 10 cihaza ayarlanır.

> [!NOTE]
> Hub 'ınız başlangıçta bir Apple korumalı alan sertifikasıyla yapılandırıldıysa ve sonra bir Apple üretim sertifikası kullanmak üzere yeniden yapılandırıldıysa, özgün cihaz belirteçleri geçersizdir. Geçersiz belirteçler, gönderim başarısız olmasına neden olur. Üretim ve test ortamlarınızı ayırın ve farklı ortamlar için farklı hub 'lar kullanın.

#### <a name="pns-credentials"></a>PNS kimlik bilgileri

Bir mobil uygulama, platformun geliştirici portalına (örneğin, Apple veya Google) kaydedildiğinde, bir uygulama tanımlayıcısı ve güvenlik belirteçleri gönderilir. Uygulama arka ucu bu belirteçleri platformun PNS 'leri için sağlar, böylece anında iletme bildirimleri cihazlara gönderilebilir. Güvenlik belirteçleri, sertifikalar biçiminde (örneğin, Apple iOS veya Windows Phone) veya güvenlik anahtarlarına (örneğin, Google Android veya Windows) olabilir. Bildirim Hub 'larında yapılandırılması gerekir. Yapılandırma genellikle Bildirim Hub 'ı düzeyinde yapılır, ancak aynı zamanda çok kiracılı bir senaryoda ad alanı düzeyinde de yapılabilir.

#### <a name="namespaces"></a>Ad Alanları

Ad alanları, dağıtım gruplandırması için kullanılabilir. Aynı uygulamadaki tüm kiracılar için tüm Bildirim Hub 'larını çok kiracılı bir senaryoda göstermek için de kullanılabilir.

#### <a name="geo-distribution"></a>Coğrafi dağıtım

Çekme bildirimi senaryolarında coğrafi dağıtım her zaman önemli değildir. Cihazlara anında iletme bildirimleri sağlayan çeşitli PNSes (örneğin, APNs veya FCM) eşit olarak dağıtılmaz.

Küresel olarak kullanılan bir uygulamanız varsa, dünyanın dört bir yanındaki farklı Azure bölgelerindeki Notification Hubs hizmetini kullanarak farklı ad alanlarında hub 'lar oluşturabilirsiniz.

> [!NOTE]
> Özellikle kayıtlar için yönetim maliyetinizi arttığı için bu düzenlemeyi önermiyoruz. Yalnızca açık bir gereksinim varsa yapılmalıdır.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Uygulama arka ucundan veya doğrudan istemci cihazlarından kayıtlar yapmam gerekir mi?

Kayıt oluşturmadan önce istemcilerin kimliğini doğrulamanız gerektiğinde uygulama arka ucunun kayıtları yararlı olur. Uygulama mantığını temel alan uygulama arka ucu tarafından oluşturulması veya değiştirilmesi gereken Etiketler varsa de bunlar da faydalıdır. Daha fazla bilgi için [arka uç Kayıt Kılavuzu] ve [arka uç Kayıt Kılavuzu 2] sayfalarına gidin.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Anında iletme bildirimi teslim güvenlik modeli nedir?

Azure Notification Hubs, [paylaşılan erişim imzası](../storage/common/storage-dotnet-shared-access-signature-part-1.md)tabanlı bir güvenlik modeli kullanır. Paylaşılan erişim imza belirteçlerini kök ad alanı düzeyinde veya ayrıntılı Bildirim Hub 'ı düzeyinde kullanabilirsiniz. Paylaşılan erişim imzası belirteçleri, farklı yetkilendirme kurallarını izlemek üzere ayarlanabilir, örneğin, ileti izinleri göndermek veya bildirim izinlerini dinlemek için. Daha fazla bilgi için bkz. [Notification Hubs güvenlik modeli] belgesi.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Anında iletme bildirimlerinde hassas yükü nasıl işleymem gerekir?

Tüm bildirimler, platformun PNS 'leri tarafından hedef cihazlara dağıtılır. Azure Notification Hubs bir bildirim gönderildiğinde, işlenir ve ilgili PNS 'ye geçirilir.

Gönderenin Azure Notification Hubs 'den PNS 'ye kadar olan tüm bağlantılar HTTPS kullanır.

> [!NOTE]
> Azure Notification Hubs iletilerin yükünü günlüğe eklemez.

Gizli yükleri göndermek için güvenli bir gönderme deseninin kullanılması önerilir. Gönderen, hassas yük olmadan cihaza ileti tanımlayıcısı ile bir ping bildirimi sunar. Cihazdaki uygulama yükü aldığında, uygulama ileti ayrıntılarını getirmek için doğrudan güvenli bir API çağırır. Bu düzenin nasıl uygulanacağı hakkında bir kılavuz için, [Notification Hubs güvenli anında iletme öğreticisi] sayfasına gidin.

## <a name="operations"></a>İşlemler

### <a name="what-support-is-provided-for-disaster-recovery"></a>Olağanüstü durum kurtarma için hangi destek sağlanır?

Son derece (Notification Hubs adı, bağlantı dizesi ve diğer kritik bilgiler) için meta veri olağanüstü durum kurtarma kapsamı sağlıyoruz. Bir olağanüstü durum kurtarma senaryosu tetiklendiğinde, kayıt verileri kaybolan Notification Hubs altyapısının *tek kesimdir* . Bu verileri yeni hub-kurtarmayla yeniden doldurmak için bir çözüm uygulamalısınız:

1. Farklı bir veri merkezinde ikincil bildirimler hub 'ı oluşturun. Yönetim olanaklarınızı etkileyebilecek bir olağanüstü durum kurtarma olayından sizi en başından korumak için baştan başlayarak oluşturmanız önerilir. Olağanüstü durum kurtarma olayı sırasında da bir tane oluşturabilirsiniz.

2. İkincil Bildirim Hub 'ını birincil Bildirim Hub 'ınızdaki kayıtlarla doldurun. Kayıtları her iki hub 'da tutmaya ve kayıtlar ' da olduğu gibi eşitlenmiş halde tutmaya çalışmamız önerilmez. Bu uygulama, kayıt sayısının PNS tarafında sona erme eğilimi nedeniyle iyi çalışmaz. Notification Hubs, zaman aşımına uğradı veya geçersiz kayıtlar hakkında PNS geri bildirimleri aldığından onları temizler.  

Uygulama arka uçları için iki önerimiz var:

* Sonunda belirli bir kayıt kümesini tutan bir uygulama arka ucu kullanın. Daha sonra ikincil Bildirim Hub 'ına bir toplu ekleme işlemi gerçekleştirebilir.
* Birincil Bildirim Hub 'ından yedek olarak kayıtların düzenli bir dökümünü alan bir uygulama arka ucu kullanın. Daha sonra ikincil Bildirim Hub 'ına bir toplu ekleme işlemi gerçekleştirebilir.

> [!NOTE]
> Standart katmanda kullanılabilen kayıtları dışarı aktarma/Içeri aktarma işlevselliği [Kayıt verme/Içeri aktarma] aktarma belgesinde açıklanmaktadır.

Arka ucunuz yoksa, uygulama hedef cihazlarda başladığında, ikincil Bildirim Hub 'ında yeni bir kayıt gerçekleştirir. Son olarak, ikincil Bildirim Hub 'ının tüm etkin cihazları kaydedilir.

Açık olmayan uygulamalar içeren cihazlarda bildirim olmadığında bir zaman aralığı olacaktır.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Tüm verilerim şifrelenmiş biçimde depolanıyor mu?

Azure Notification Hubs, bekleyen tüm müşteri verilerini kayıt etiketleri hariç olacak şekilde şifreler. Bu nedenle, etiketleri kullanarak kişisel veya gizli verileri depolamamalısınız.

### <a name="is-there-audit-log-capability"></a>Denetim günlüğü özelliği var mı?

Evet. Tüm Notification Hubs yönetim işlemleri, [Azure Portal]kullanıma sunulan Azure etkinlik günlüğünü güncelleştirir. Azure etkinlik günlüğü, aboneliklerinizde bulunan kaynaklarda gerçekleştirilen işlemlere yönelik öngörüler sunar. Etkinlik günlüğünü kullanarak, aboneliğinizdeki kaynaklar için ne kadar, kim ve ne zaman bir yazma işlemi yapıldığını (PUT, POST, SILME) belirleyebilirsiniz. Ayrıca, işlemleri ve diğer ilgili özellikler durumunu anlayabilirsiniz. Ancak. Etkinlik günlüğü okuma (GET) işlemini içermez.

## <a name="monitoring-and-troubleshooting"></a>İzleme ve sorun giderme

### <a name="what-troubleshooting-capabilities-are-available"></a>Hangi sorun giderme özellikleri kullanılabilir?

Azure Notification Hubs, özellikle bırakılan bildirimlerin en yaygın senaryosunda sorun gidermeye yönelik çeşitli özellikler sağlar. Ayrıntılar için [Notification Hubs sorunlarını giderme] teknik incelemesine bakın.

### <a name="what-telemetry-features-are-available"></a>Hangi telemetri özellikleri kullanılabilir?

Azure Notification Hubs, [Azure Portal]Telemetri verilerinin görüntülenmesine izin verebilir. Ölçümlerin ayrıntıları [Notification Hubs ölçümleri] sayfasında bulunabilir.

Ölçümlere de programlı bir şekilde erişebilirsiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

- [.Net Ile Azure izleyici ölçümlerini alın](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Bu örnek, Kullanıcı adını ve parolayı kullanır. Bir sertifika kullanmak için, [Bu örnekte](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs)gösterildiği gibi bir sertifika sağlamak üzere FromServicePrincipal metodunu aşırı yükleme. 
- [Bir kaynak için ölçümleri ve etkinlik günlüklerini alma](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Azure Izleme REST API izlenecek yol](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> Başarılı bildirimler, anında iletme bildirimlerinin dış PNS 'ye teslim edildiğini (örneğin, iOS ve macOS için APNs ve Android cihazlar için FCM) sağlar. Hedef cihazlara bildirimleri teslim etmek için PNS 'nin sorumluluğundadır. Genellikle, PNS, teslim ölçümlerini üçüncü taraflara sunmaz.  

[Azure Portal]: https://portal.azure.com
[Notification Hubs fiyatlandırması]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[REST API 'Leri Notification Hubs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Notification Hubs Başlarken öğreticileri]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Arka uç Kayıt Kılavuzu]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Arka uç Kayıt Kılavuzu 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Notification Hubs güvenlik modeli]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Notification Hubs güvenli anında Iletme öğreticisi]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Notification Hubs sorunlarını giderme]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs ölçümleri]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Kayıt verme/Içeri aktarma]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
