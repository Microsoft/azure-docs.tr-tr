---
title: Azure Notification Hubs 'da bırakılan bildirimleri tanılama
description: Azure Notification Hubs 'de bırakılan bildirimler ile ilgili yaygın sorunları tanılamayı öğrenin.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 3aaa99caca461d4b8e339cf4c1f7847adef4027a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076858"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Azure Notification Hubs 'da bırakılan bildirimleri tanılama

Azure Notification Hubs hakkında sık sorulan sorular, bir uygulamadan gelen bildirimlerin istemci cihazlarda görünmemesinde nasıl sorun giderilir. Müşteriler, bildirimlerin nerede ve neden bırakılacağını ve sorunun nasıl düzeltileceğini öğrenmek ister. Bu makalede, bildirimlerin cihaz tarafından nasıl bırakıldığına veya alınamadığına ilişkin bildirimler tanımlanmaktadır. Ayrıca, kök nedenin nasıl belirleneceğini de açıklar.

Öncelikle Notification Hubs bir cihaza nasıl bildirim ilettiği hakkında anlaşılması önemlidir.

![Notification Hubs mimarisi][0]

Tipik bir gönderme bildirim akışında ileti, *uygulama arka ucundan* Notification Hubs 'e gönderilir. Notification Hubs tüm kayıtları işler. Hedefleri belirlemede yapılandırılmış Etiketler ve etiket ifadelerini dikkate alır. Hedefler, anında iletme bildirimi alması gereken kayıtlardır. Bu kayıtlar, desteklenen platformlarımızdan herhangi birine yayılabilir: Android, Baidu (Çin 'de Android cihazları), Fire OS (Amazon) iOS, Windows ve Windows Phone.

Hedefler oluşturulduğunda, cihaz platformu için *anında iletme bildirimi hizmetine* bildirim gönderir Notification Hubs. Örnek olarak iOS ve macOS için Apple Anında Iletilen bildirim hizmeti (APNs) ve Android cihazlar için Firebase Cloud Messaging (FCM) sayılabilir. Notification Hubs birden çok kayıt halinde bildirim ayrılır. **Bildirim Hub 'ını Yapılandır**altında Azure Portal ayarladığınız kimlik bilgilerine göre ilgili anında iletme bildirimi hizmeti ile kimlik doğrular. Anında iletme bildirimi hizmeti, bildirimleri ilgili *istemci cihazlarına*iletir.

Bildirim tesliminin son bacağı, platformun anında iletme bildirimi hizmeti ve cihaz arasındadır. Bildirim teslimi, anında iletme bildirimi işlemindeki (istemci, uygulama arka ucu, Notification Hubs ve platformun anında iletme bildirimi hizmeti) dört aşamanın herhangi birinde başarısız olabilir. Notification Hubs mimarisi hakkında daha fazla bilgi için bkz. [Notification Hubs genel bakış].

İlk test/hazırlama aşamasında bildirimleri teslim etme hatası oluşabilir. Bu aşamada bırakılan bildirimler bir yapılandırma sorununa işaret edebilir. Üretimde bildirim teslim etmek için bir hata oluşursa, bildirimlerin bazıları veya tümü bırakılmış olabilir. Bu durumda daha derin bir uygulama veya mesajlaşma deseninin sorunu gösterilir.

Sonraki bölümde, bildirimlerin, yaygın olarak sık görülen ve nadir olarak bırakılan senaryolara bakar.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs yanlış yapılandırma ##

İlgili anında iletme bildirimi hizmetine bildirim göndermek için Notification Hubs uygulamanızın bağlamında kimliğini doğrulamalıdır. Hedef platformun bildirim hizmeti (Microsoft, Apple, Google, vb.) ile bir geliştirici hesabı oluşturmanız gerekir. Daha sonra, hedef PNS ile çalışmak için kullandığınız bir belirteç veya anahtar aldığınız işletim sistemine uygulamanızı kaydetmeniz gerekir.

Azure portal platform kimlik bilgilerini eklemeniz gerekir. Cihaza bir bildirim yoksa, ilk adım Notification Hubs doğru kimlik bilgilerinin yapılandırıldığından emin olunması sağlamaktır. Kimlik bilgilerinin, platforma özgü bir geliştirici hesabı altında oluşturulan uygulamayla eşleşmesi gerekir.

Bu işlemi tamamlamaya yönelik adım adım yönergeler için bkz. [Azure Notification Hubs kullanmaya başlama].

Denetlenecek bazı yaygın yapılandırma hataları aşağıda verilmiştir:

### <a name="notification-hub-name-location"></a>Bildirim Hub 'ı ad konumu

Bildirim Hub 'ınızın adının (yazım hataları olmadan) bu konumların her birinde aynı olduğundan emin olun:
   * İstemciden kaydettiğiniz yer
   * Arka uçtan bildirim gönderirsiniz
   * Anında iletme bildirimi hizmeti kimlik bilgilerini yapılandırdığınız yer

İstemcide ve uygulama arka ucunun doğru paylaşılan erişim imzası yapılandırma dizelerini kullandığınızdan emin olun. Genellikle, istemcide **Defaultlistensharedaccesssignature** ve uygulama arka ucunun **defaultfullsharedaccesssignature** kullanmanız gerekir. Bu, Notification Hubs bildirim gönderme izinleri verir.

### <a name="apn-configuration"></a>APN yapılandırması ###

İki farklı hub korumanız gerekir: biri üretim için ve başka bir test için. Korumalı bir ortamda kullandığınız sertifikayı, üretimde kullanacağınız sertifikadan/hub 'a göre ayrı bir hub 'a yüklemeniz gerekir. Aynı hub 'a farklı türde sertifikalar yüklemeye çalışmayın. Bu bildirim hatalara neden olur.

Aynı hub 'a yanlışlıkla farklı türde sertifikalar yüklerseniz, hub 'ı silmeniz ve yeni bir hub ile baştan başlamanız gerekir. Bir nedenden dolayı hub 'ı sileminiz varsa, en azından tüm mevcut kayıtları hub 'dan silmeniz gerekir.

### <a name="fcm-configuration"></a>FCM yapılandırması ###

1. Firebase 'den aldığınız *sunucu anahtarının* Azure Portal kaydettiğiniz sunucu anahtarıyla eşleştiğinden emin olun.

   ![Firebase sunucu anahtarı][3]

2. İstemcide **Proje kimliğini** yapılandırdığınızdan emin olun. Firebase panosundan **proje kimliği** için değer elde edebilirsiniz.

   ![Firebase proje KIMLIĞI][1]

## <a name="application-issues"></a>Uygulama sorunları ##

### <a name="tags-and-tag-expressions"></a>Etiketler ve etiket ifadeleri ###

Hedef kitlenizi segmentlere ayırmak için Etiketler veya etiket ifadeleri kullanırsanız, bildirim gönderdiğinizde hedef bulunamadı. Bu hata, gönderme çağrdaki belirtilen etiketlere veya etiket ifadelerine dayanır.

Bir bildirim gönderdiğinizde etiketlerin eşleştiğinden emin olmak için kayıtlarınızı gözden geçirin. Daha sonra, bildirim alındığını yalnızca bu kayıtları içeren istemcilerden doğrulayın.

Örneğin, Notification Hubs ile tüm kayıtlarınızın "siyatik" etiketini kullanmasını varsayalım. Daha sonra "spor" etiketiyle bir bildirim gönderirseniz, bildirim hiçbir cihaza gönderilmez. Karmaşık bir durum, "A etiketi" *veya* "Tag b" kullanarak kaydettiğiniz, ancak "bir & & etiketini b" hedeflediniz "etiketini içerebilir. Makalenin ilerleyen bölümlerinde bulunan kendi kendine tanılama ipuçları bölümü, kayıtlarınızı ve bunların etiketlerini nasıl gözden geçiistediğinizi gösterir.

### <a name="template-issues"></a>Şablon sorunları ###

Şablonlar kullanıyorsanız, [Şablonlar]açıklanan yönergeleri izlediğinizden emin olun.

### <a name="invalid-registrations"></a>Geçersiz kayıtlar ###

Bildirim Hub 'ı doğru şekilde yapılandırıldıysa ve Etiketler ya da etiket ifadeleri doğru şekilde kullanıldıysa, geçerli hedefler bulunur. Bildirimlerin bu hedeflere gönderilmesi gerekir. Notification Hubs, paralel olarak birkaç işlem toplu işlemini devre dışı bırakır. Her Batch, iletileri bir kayıt kümesine gönderir.

> [!NOTE]
> Notification Hubs toplu işleri paralel olarak işlediğinden, bildirimlerin teslim edileceği sıra garanti edilmez.

Notification Hubs bir "en çok bir kez" ileti teslim modeli için iyileştirilmiştir. Bir cihaza birden çok bildirim teslim edilmeden, bu sayede yinelenenleri kaldırma girişiminde bulunuldu. Kayıtlar, anında iletme bildirimi hizmetine gönderilmeden önce cihaz tanımlayıcısı başına yalnızca bir iletinin gönderilmesini sağlamak üzere denetlenir.

Her toplu işlem anında iletme bildirimi hizmetine gönderilir ve bu da kayıtları kabul eder ve doğrular. Bu işlem sırasında, anında iletme bildirimi hizmeti bir toplu işteki bir veya daha fazla kayıt ile bir hata tespit ediyor olabilir. Anında iletme bildirimi hizmeti daha sonra Notification Hubs bir hata döndürür ve işlem durduruluyor. Anında iletme bildirimi hizmeti bu toplu işi tamamen bırakır. Bu, özellikle TCP akış protokolü kullanan APNs ile doğrudur.

Bu durumda, hatalı kayıt veritabanından kaldırılır. Daha sonra, bu toplu işteki cihazların geri kalanı için bildirim teslimi yeniden deneniyoruz.

Bir kayıtla ilgili başarısız teslimat girişimi hakkında daha fazla bilgi almak için, Ileti telemetri başına Notification Hubs REST API 'Leri kullanabilirsiniz [: bildirim iletisi telemetrisi](https://msdn.microsoft.com/library/azure/mt608135.aspx) ve [PNS geri bildirimleri](https://msdn.microsoft.com/library/azure/mt705560.aspx)alın. Örnek kod için [rest gönder örneğine](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)bakın.

## <a name="push-notification-service-issues"></a>Anında iletme bildirimi hizmeti sorunları

Anında iletme bildirimi hizmeti bildirimi aldıktan sonra cihaza bildirim gönderir. Bu noktada, Notification Hubs cihaza bildirim teslimi üzerinde bir denetim yoktur.

Platform bildirim hizmetleri sağlam olduğundan, bildirimler birkaç saniye içinde cihazlara ulaşılmayı eğilimlidir. Anında iletme bildirimi hizmeti azaltıyorsa Notification Hubs bir üstel geri dönüş stratejisi uygular. Anında iletme bildirimi hizmeti 30 dakika boyunca ulaşılamaz durumda kalırsa, süresi dolacak ve iletileri kalıcı olarak bırakan bir ilke vardır.

Bir anında iletme bildirimi hizmeti bir bildirim sunmaya çalışır, ancak cihaz çevrimdışı olduğunda bildirim anında bildirim hizmeti tarafından depolanır. Yalnızca sınırlı bir süre için depolanır. Cihaz kullanılabilir hale geldiğinde bildirim cihaza gönderilir.

Her uygulama yalnızca bir son bildirimi depolar. Bir cihaz çevrimdışıyken birden çok bildirim gönderiliyorsa, her yeni bildirim son birinin atılmasına neden olur. Yalnızca en yeni bildirimin tutulması, APNs 'de *birleştirme* ve FCM 'de *daraltma* olarak adlandırılır. (FCM daraltma anahtarını kullanır.) Cihaz uzun bir süre çevrimdışı kaldığında, cihaz için depolanan bildirimler atılır. Daha fazla bilgi için bkz. [APNs genel bakış] ve [FCM iletileri hakkında].

Notification Hubs, genel SendNotification API 'sini kullanarak bir HTTP üst bilgisi aracılığıyla birleştirme anahtarını geçirebilirsiniz. Örneğin, .NET SDK için `SendNotificationAsync`kullanırsınız. SendNotification API 'SI, karşılık gelen anında iletme bildirimi hizmetine gönderilen HTTP üstbilgilerini de alır.

## <a name="self-diagnosis-tips"></a>Kendi kendine tanılama ipuçları

Notification Hubs ' de bırakılan bildirimlerin kök nedenini tanılamaya yönelik yollar şunlardır.

### <a name="verify-credentials"></a>Kimlik bilgilerini doğrulama ###

#### <a name="push-notification-service-developer-portal"></a>Anında iletme bildirimi hizmeti geliştirici portalı ####

İlgili anında iletme bildirimi hizmeti Geliştirici portalındaki (APNs, FCM, Windows Notification hizmeti vb.) kimlik bilgilerini doğrulayın. Daha fazla bilgi için bkz. [öğretici: Azure Notification Hubs kullanarak Evrensel Windows platformu uygulamalara bildirim gönderme](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Azure portalında ####

Anında iletme bildirimi hizmeti Geliştirici portalından edindiğiniz kimlik bilgilerini gözden geçirmek ve eşleştirmek için Azure portal **erişim ilkeleri** sekmesine gidin.

![Azure portal erişim Ilkeleri][4]

### <a name="verify-registrations"></a>Kayıtları doğrula

#### <a name="visual-studio"></a>Visual Studio ####

Visual Studio 'da, Notification Hubs dahil olmak üzere birden çok Azure hizmetini görüntülemek ve yönetmek için Sunucu Gezgini aracılığıyla Azure 'a bağlanabilirsiniz. Bu kısayol öncelikle geliştirme/test ortamınız için yararlıdır.

![Visual Studio Sunucu Gezgini][9]

Merkezinizdeki tüm kayıtları görüntüleyebilir ve yönetebilirsiniz. Kayıtlar platform, yerel veya şablon kaydı, etiket, anında bildirim hizmeti tanımlayıcısı, kayıt KIMLIĞI ve sona erme tarihine göre kategorilere ayrılmıştır. Ayrıca, bu sayfadaki bir kaydı düzenleyebilirsiniz. Bu, özellikle etiketleri düzenlemede yararlıdır.

**Sunucu Gezgini**Bildirim Hub 'ına sağ tıklayın ve **Tanıla**' yı seçin. 

![Visual Studio Sunucu Gezgini: Tanılama Menüsü](./media/notification-hubs-diagnosing/diagnose-menu.png)

Aşağıdaki sayfayı görürsünüz:

![Visual Studio: Tanılama sayfası](./media/notification-hubs-diagnosing/diagnose-page.png)

**Cihaz kayıtları** sayfasına geçin:

![Visual Studio: cihaz kayıtları](./media/notification-hubs-diagnosing/VSRegistrations.png)

Sınama **gönderme** sayfasını, test bildirim iletisi göndermek için kullanabilirsiniz:

![Visual Studio: test gönderme](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Kayıtları yalnızca geliştirme/test sırasında ve sınırlı sayıda kayıt ile düzenlemek için Visual Studio 'Yu kullanın. Kayıtlarınızı toplu olarak düzenlemeniz gerekiyorsa, [nasıl yapılır: kayıtları toplu olarak dışarı ve değiştirme](https://msdn.microsoft.com/library/dn790624.aspx)bölümünde açıklanan dışarı aktarma ve içeri aktarma kaydetme işlevlerini kullanmayı düşünün.

#### <a name="service-bus-explorer"></a>Service Bus Explorer ####

Birçok müşteri, Bildirim Hub 'larını görüntülemek ve yönetmek için [Service Bus Gezginini](https://github.com/paolosalvatori/ServiceBusExplorer) kullanır. Service Bus Explorer açık kaynaklı bir projem. 

### <a name="verify-message-notifications"></a>İleti bildirimlerini doğrulama

#### <a name="azure-portal"></a>Azure portalında ####

Bir hizmet arka ucu ve çalışır duruma girmeden istemcilerinize test bildirimi göndermek için, **destek + sorun giderme**altında, **Test gönder**' i seçin.

![Azure 'da gönderme işlevselliğini test etme][7]

#### <a name="visual-studio"></a>Visual Studio ####

Ayrıca, Visual Studio 'dan test bildirimleri de gönderebilirsiniz.

![Visual Studio 'da test gönderme işlevselliği][10]

Visual Studio Sunucu Gezgini ile Notification Hubs kullanma hakkında daha fazla bilgi için şu makalelere bakın:

* [Bildirim Hub 'ları için cihaz kayıtlarını görüntüleme](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Derin bakış: Visual Studio 2013 güncelleştirme 2 RC ve Azure SDK 2,3]
* [Visual Studio 2013 güncelleştirme 3 ve Azure SDK 2,4 sürümü duyurusu]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Hata ayıklama başarısız bildirimleri ve gözden geçirme bildirimi sonucu

#### <a name="enabletestsend-property"></a>EnableTestSend özelliği ####

Notification Hubs aracılığıyla bir bildirim gönderdiğinizde, bildirim başlangıçta sıraya alınır. Notification Hubs doğru hedefleri belirler ve bildirimi anında iletme bildirimi hizmetine gönderir. REST API veya istemci SDK 'larından herhangi birini kullanıyorsanız, gönderme çağrımız geri dönüşü yalnızca iletinin Notification Hubs ile sıraya alındığı anlamına gelir. Notification Hubs sonunda anında iletme bildirimi hizmetine bildirim gönderildiğinde ne olduğunu gösteren bir fikir vermez.

Bildirim istemci cihazına ulaşmazsa, Notification Hubs bunu anında iletme bildirimi hizmetine sunmaya çalıştığında bir hata oluşmuş olabilir. Örneğin, yük boyutu anında iletme bildirimi hizmeti tarafından izin verilen üst sınırı aşabilir veya Notification Hubs ' de yapılandırılan kimlik bilgileri geçersiz olabilir.

Anında iletme bildirimi hizmeti hatalarıyla ilgili bilgi almak için [Enabletestsend] özelliğini kullanabilirsiniz. Portalda veya Visual Studio istemcisinden test iletileri gönderdiğinizde bu özellik otomatik olarak etkinleştirilir. Ayrıntılı hata ayıklama bilgilerini ve API 'Leri kullanarak da bu özelliği kullanabilirsiniz. Şu anda bunu .NET SDK 'da kullanabilirsiniz. Bu, sonunda tüm istemci SDK 'larına eklenecektir.

REST çağrısıyla `EnableTestSend` özelliğini kullanmak için, Send çağrınızın sonuna *Test* adlı bir sorgu dizesi parametresi ekleyin. Örneğin:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK örneği ####

Aşağıda, yerel bir açılır pencere (bildirim) bildirimi göndermek için .NET SDK kullanmaya bir örnek verilmiştir:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Yürütmenin sonunda, `result.State` yalnızca `Enqueued`durumlar olur. Sonuçlar, anında iletme bildiriğinize ne olduğunu gösteren bir öngörü sağlamaz.

Sonra, `EnableTestSend` Boolean özelliğini kullanabilirsiniz. Bildirim gönderildiğinde oluşan anında iletme bildirimi hizmeti hataları hakkında ayrıntılı bir durum almak için `NotificationHubClient` başlatırken `EnableTestSend` özelliğini kullanın. Gönderme çağrısı, bildirimi anında iletme bildirimi hizmetine teslim etmek için Notification Hubs ihtiyaç duyduğundan ek süre dönmesi daha fazla zaman alır.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Örnek çıktı ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Bu ileti Notification Hubs ' de yapılandırılan kimlik bilgilerinin geçersiz olduğunu veya hub 'daki kayıtlarla ilgili bir sorun olduğunu gösterir. Bu kaydı silin ve iletiyi göndermeden önce istemcinin kaydı yeniden oluşturmasını sağlayın.

> [!NOTE]
> `EnableTestSend` özelliğinin kullanılması yoğun bir şekilde kısıtlanıyor. Bu seçeneği yalnızca bir geliştirme/test ortamında ve sınırlı sayıda kayıt kümesiyle kullanın. Hata ayıklama bildirimleri yalnızca 10 cihaza gönderilir. Ayrıca, dakikada 10, hata ayıklama gönderme için bir sınır de vardır.

### <a name="review-telemetry"></a>Telemetriyi İnceleme ###

#### <a name="azure-portal"></a>Azure portalında ####

Portalda, Bildirim Hub 'ınızdaki tüm etkinliklere hızlı bir genel bakış edinebilirsiniz.

1. **Genel bakış** sekmesinde, platforma göre kayıtların, bildirimlerin ve hataların toplanmış bir görünümünü görebilirsiniz.

   ![Notification Hubs Genel Bakış Panosu][5]

2. **İzleyici** sekmesinde, daha derin bir görünüm için platforma özgü birçok farklı ölçüm ekleyebilirsiniz. Notification Hubs bildirimi anında iletme bildirimi hizmetine göndermeye çalıştığında döndürülen hatalara de bakabilirsiniz.

   ![Etkinlik günlüğünü Azure portal][6]

3. **Gelen iletileri**, **kayıt Işlemlerini**ve **başarılı bildirimleri**inceleyerek başlayın. Ardından, anında iletme bildirimi hizmetine özgü hataları gözden geçirmek için platform başına sekmesine gidin.

4. Bildirim Hub 'ınız için kimlik doğrulama ayarları yanlışsa, ileti **PNS kimlik doğrulama hatası** görüntülenir. Anında iletme bildirimi hizmeti kimlik bilgilerini denetlemek iyi bir göstergesidir.

#### <a name="programmatic-access"></a>Programlı erişim ####

Programlı erişim hakkında daha fazla bilgi için bkz. [programlı erişim](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Kayıt ve telemetri erişimini API 'Ler aracılığıyla dışarı aktarma ve içeri aktarma gibi telemetri ile ilgili birçok özellik yalnızca standart hizmet katmanında kullanılabilir. Bu özellikleri ücretsiz veya temel hizmet katmanından kullanmaya çalışırsanız, SDK kullanıyorsanız bir özel durum iletisi alırsınız. Doğrudan REST API 'Lerinden özellikleri kullanırsanız, HTTP 403 (yasak) hatası alırsınız.
>
> Telemetri ile ilgili özellikleri kullanmak için, önce standart hizmet katmanını kullanmakta olduğunuz Azure portal emin olun.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs genel bakış]: notification-hubs-push-notification-overview.md
[Azure Notification Hubs kullanmaya başlama]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Şablonlar]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs genel bakış]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[FCM iletileri hakkında]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Derin bakış: Visual Studio 2013 güncelleştirme 2 RC ve Azure SDK 2,3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Visual Studio 2013 güncelleştirme 3 ve Azure SDK 2,4 sürümü duyurusu]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
