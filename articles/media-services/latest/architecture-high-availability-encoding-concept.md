---
title: Isteğe bağlı Media Services video ile yüksek kullanılabilirlik
description: Bu makalede, VOD uygulaması için yüksek kullanılabilirliği kolaylaştırmak amacıyla kullanabileceğiniz Azure hizmetlerine genel bakış sunulmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c334888f5b85b0d2211225282680d5f791b50793
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277887"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Media Services ve Isteğe bağlı video (VOD) ile yüksek kullanılabilirlik

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>VOD için yüksek kullanılabilirlik

Azure mimarisi belgelerinde [Geodes](/azure/architecture/patterns/geodes) adlı yüksek kullanılabilirliğe sahip bir tasarım deseninin olması vardır. Ölçeklenebilirlik ve dayanıklılık sağlamak için yinelenen kaynakların farklı coğrafi bölgelere nasıl dağıtıldığını açıklar.  Yedeklilik, sistem durumu izleme, yük dengeleme ve veri yedekleme ve kurtarma gibi birçok yüksek kullanılabilirliğe sahip tasarım konularını kapsayacak şekilde bu tür bir mimari oluşturmak için Azure hizmetlerini kullanabilirsiniz.  Bu tür bir mimaride, çözümde kullanılan her bir hizmetle ilgili ayrıntıların yanı sıra, VOD uygulamanız için yüksek kullanılabilirlik mimarisi oluşturmak üzere bireysel hizmetlerin nasıl kullanılabileceği aşağıda açıklanmaktadır.

### <a name="sample"></a>Örnek

Media Services ve Isteğe bağlı video (VOD) ile yüksek kullanılabilirlik hakkında bilgi sahibi olmak için kullanabileceğiniz bir örnek vardır. Ayrıca, bir VOD senaryosu için hizmetlerin nasıl kullanıldığı hakkında daha fazla ayrıntıya gider.  Örnek, üretimde kullanılmak üzere geçerli biçimde tasarlanmamıştır.  Örnek kodu ve Benioku dosyasını dikkatle gözden geçirin, özellikle de [hata modlarında](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) bölüm, bir üretim uygulamasıyla tümleştirmeye başlamadan önce.  Isteğe bağlı video (VOD) için yüksek kullanılabilirliğe sahip bir üretim uygulamasının, Content Delivery Network (CDN) stratejisini de dikkatle incelemesi gerekir.  [GitHub 'daki koda](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)göz atın.

## <a name="overview-of-services"></a>Hizmetlere genel bakış

Bu örnek mimaride kullanılan hizmetler şunlardır:

| Simge | Ad | Açıklama |
| :--: | ---- | ----------- |
|![Bu, Media Services hesabı simgedir.](./media/architecture-high-availability-encoding-concept/azure-media-services.svg)| Media Services hesabı | **Açıklama:**<br>Media Services hesap, Azure 'daki medya içeriğini yönetmek, şifrelemek, kodlamak, çözümlemek ve akışa alma için başlangıç noktasıdır. Bir Azure depolama hesabı kaynağıyla ilişkilendirilir. Hesabın ve ilişkili tüm depolamanın aynı Azure aboneliğinde olması gerekir.<br><br>**VOD kullanımı:**<br>Bunlar, video ve ses varlıklarınızı kodlamak ve sunmak için kullandığınız hizmetlerdir.  Yüksek kullanılabilirlik için, her biri farklı bir bölgede en az iki Media Services hesap ayarlarsınız. [Azure Media Services hakkında daha fazla bilgi edinin](media-services-overview.md). |
|![Bu, depolama hesabı simgedir.](./media/architecture-high-availability-encoding-concept/storage-account.svg)| Depolama hesabı | **Açıklama:**<br>Azure depolama hesabı, tüm Azure depolama veri nesnelerinizi içerir: Bloblar, dosyalar, kuyruklar, tablolar ve diskler. Veriler, dünyanın her yerinden HTTP veya HTTPS üzerinden erişilebilir.<br><br>Her bölgedeki her bir Media Services hesabının aynı bölgedeki bir depolama hesabı vardır.<br><br>**VOD kullanımı:**<br>VOD işleme ve akış için giriş ve çıkış verilerini saklayabilirsiniz. [Azure depolama hakkında daha fazla bilgi edinin](../../storage/common/storage-introduction.md). |
|![Bu, Azure depolama kuyruğu simgedir.](./media/architecture-high-availability-encoding-concept/storage-account-queue.svg)| Azure Depolama Kuyruğu | **Açıklama:**<br>Azure Kuyruk depolama, HTTP veya HTTPS kullanan kimlik doğrulaması yapılmış çağrılar aracılığıyla dünyanın her yerinden erişilebilen çok sayıda iletinin depolanması için bir hizmettir.<br><br>**VOD kullanımı:**<br>Kuyruklar, farklı modüller arasında etkinlikleri koordine etmek üzere ileti göndermek ve almak için kullanılabilir. Örnek, bir Azure depolama kuyruğu kullanır, ancak Azure, gereksinimlerinize daha uygun olabilecek Service Bus ve Service Fabric güvenilir kuyruklar gibi diğer sıra türlerini sağlar. [Azure kuyruğu hakkında daha fazla bilgi edinin](../../storage/queues/storage-queues-introduction.md). |
|![Bu Azure Cosmos DB simgedir.](./media/architecture-high-availability-encoding-concept/azure-cosmos-db.svg)| Azure Cosmos DB  | **Açıklama:**<br>Azure Cosmos DB, Microsoft 'un Dünya çapında dağıtılmış, çok modelli bir veritabanı hizmetidir ve tüm dünyada birçok Azure bölgesinde işlem ve depolamayı bağımsız olarak ölçeklendirin.<br><br>**VOD kullanımı:**<br>Tablolar, iş çıkışı durum kayıtlarını depolamak ve her bir Media Services örneğinin sistem durumunu izlemek için kullanılabilir. Ayrıca Media Services API 'sine yapılan her çağrının durumunu izleyebilir/kaydedebilirsiniz. [Azure Cosmos DB hakkında daha fazla bilgi edinin](../../cosmos-db/introduction.md).  |
|![Bu, yönetilen kimlik simgedir.](./media/architecture-high-availability-encoding-concept/managed-identity.svg)| Yönetilen Kimlik | **Açıklama:**<br>Yönetilen kimlik, Azure AD 'de otomatik olarak yönetilen bir kimlik sağlayan bir Azure AD özelliğidir. Kimlik bilgilerini kodda depolamadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanılabilir.<br><br>**VOD kullanımı:**<br>Azure Işlevleri, Key Vault bağlanmak için Media Services örneklerine kimlik doğrulaması yapmak üzere yönetilen kimlik kullanabilir. [Yönetilen kimlik hakkında daha fazla bilgi edinin](../../active-directory/managed-identities-azure-resources/overview.md). |
|![Bu Key Vault simgedir.](./media/architecture-high-availability-encoding-concept/key-vault.svg)| Key Vault | **Açıklama:**<br>Azure Key Vault belirteçleri, parolaları, sertifikaları, API anahtarlarını ve diğer gizli dizileri güvenli bir şekilde depolamak ve bu erişimi sıkı şekilde denetlemek için kullanılabilir. Ayrıca, anahtar yönetim çözümü olarak da kullanılabilir. Azure Key Vault, verilerinizi şifrelemek için kullanılan şifreleme anahtarlarını oluşturmayı ve denetlemeyi kolaylaştırır. Azure ve iç bağlı kaynaklarla kullanılmak üzere ortak ve özel Aktarım Katmanı Güvenlik/Güvenli Yuva Katmanı (TLS/SSL) sertifikalarını kolayca sağlayabilir, yönetebilir ve dağıtabilir. Gizli dizileri ve anahtarları yazılım veya FIPS 140-2 düzey 2 tarafından doğrulanan HSM 'ler tarafından korunabilir.<br><br>**VOD kullanımı:**<br>Key Vault, uygulamanız için hizmet sorumlusu için erişim ilkeleri ayarlamak üzere kullanılabilir.  Bağlantı dizesini depolama hesaplarına depolamak için kullanılabilir. Bağlantı dizelerini depolama hesaplarına ve Cosmos DB 'ye depolamak için Key Vault kullanırız. Ayrıca, genel küme yapılandırmasını depolamak için Key Vault de kullanabilirsiniz. Her medya hizmeti örneği için abonelik KIMLIĞINI, kaynak grubu adını ve hesap adını saklayabilirsiniz. Daha ayrıntılı bilgi için bkz. nasıl kullanıldığı. [Key Vault hakkında daha fazla bilgi edinin](../../key-vault/general/overview.md). |
|![Bu, Azure Işlevleri simgedir.](./media/architecture-high-availability-encoding-concept/function-app.svg)| Azure İşlevleri | **Açıklama:**<br>Azure Işlevleri ile uygulama altyapısı hakkında endişelenmeden küçük kod parçalarını çalıştırın ("işlevler" olarak adlandırılır). [Azure işlevleri hakkında daha fazla bilgi edinin](../../azure-functions/functions-overview.md).<br><br>**VOD kullanımı:**<br>Azure Işlevleri, VOD uygulamanızın modüllerini barındırmak için kullanılabilir.  Bir VOD uygulaması için modüller şunları içerebilir:<br><br>**İş zamanlama modülü**<br>İş zamanlama modülü, yeni işlerin bir Media Services kümesine gönderilmesi (farklı bölgelerdeki iki veya daha fazla örnek) için olur. Her bir Media Services örneğinin sistem durumunu izler ve sonraki sağlıklı örneğe yeni bir iş gönderir.<br><br>**İş durumu modülü**<br>İş durumu modülü Azure Event Grid hizmetinden gelen iş çıkış durumu olaylarını dinliyor. Modüller geri kalanı tarafından Media Services API 'Lerin çağrı sayısını en aza indirmek için olayları olay deposuna depolar.<br><br>**Örnek sistem durumu modülü**<br>Bu modül, gönderilen işleri izler ve her bir Media Services örneğinin sistem durumunu tespit edecektir. Tamamlanmış işleri, başarısız işleri ve hiçbir şekilde tamamlanmamış işleri izler.<br><br>**Sağlama modülü**<br>Bu modül, işlenen varlıkları temin edecektir. Varlık verilerini tüm Media Services örneklerine kopyalar ve bazı Media Services örnekleri kullanılabilir durumda olmasa bile varlıkların akışını sağlamak için Azure ön kapı hizmeti 'ni ayarlar. Ayrıca akış Konumlandırıcı 'yı da ayarlar.<br><br>**İş doğrulama modülü**<br>Bu modül gönderilen her işi izler, başarısız işleri yeniden gönderin ve iş başarıyla tamamlandıktan sonra iş verilerini temizleme işlemini gerçekleştirir.  |
|![Bu App Service simgedir.](./media/architecture-high-availability-encoding-concept/application-service.svg)| App Service (ve planı)  | **Açıklama:**<br>Azure App Service Web uygulamalarını, REST API 'Leri ve mobil arka uçları barındırmak için HTTP tabanlı bir hizmettir. .NET, .NET Core, Java, Ruby, Node.js, PHP veya Python 'u destekler. Uygulamalar hem Windows hem de Linux tabanlı ortamlarda çalışır ve ölçeklendirebilir.<br><br>**VOD kullanımı:**<br>Her modül bir App Service tarafından barındırılır. [App Service hakkında daha fazla bilgi edinin](../../app-service/overview.md). |
|![Bu, Azure ön kapısının simgedir.](./media/architecture-high-availability-encoding-concept/azure-front-door.svg)| Azure Front Door | **Açıklama:**<br>Azure ön kapısı, yüksek kullanılabilirlik için en iyi performansı ve hızlı genel yük devretmeyi iyileştirerek Web trafiğinin genel yönlendirilmesini tanımlamak, yönetmek ve izlemek için kullanılır.<br><br>**VOD kullanımı:**<br>Azure ön kapısı, trafiği akış uç noktalarına yönlendirmek için kullanılabilir. [Azure ön kapısı hakkında daha fazla bilgi edinin](../../frontdoor/front-door-overview.md).  |
|![Bu Azure Event Grid simgedir.](./media/architecture-high-availability-encoding-concept/event-grid-subscription.svg)| Azure Event Grid | **Açıklama:**<br>Olay tabanlı mimariler için oluşturulan Event Grid, depolama Blobları ve kaynak grupları gibi Azure hizmetlerinden gelen olaylar için yerleşik desteğe sahiptir. Ayrıca özel konu olayları için destek içerir. Filtreler, belirli olayları farklı uç noktalara yönlendirmek için, birden çok bitiş noktasına çok noktaya yayın yapmak ve olayların güvenilir bir şekilde teslim edildiğinden emin olmak için kullanılabilir. Her bölgedeki birden çok hata etki alanında ve kullanılabilirlik alanları arasında yerel olarak yayılarak kullanılabilirliği en üst düzeye çıkarır.<br><br>**VOD kullanımı:**<br>Event Grid, tüm uygulama olaylarını izlemek ve iş durumu kalıcı hale getirmek için bunları depolamak için kullanılabilir. [Azure Event Grid hakkında daha fazla bilgi edinin](../../event-grid/overview.md). |
|![Bu Application Insights simgedir.](./media/architecture-high-availability-encoding-concept/application-insights.svg)| Application Insights | **Açıklama:** <br>Azure İzleyici'nin bir özelliği olan Application Insights, geliştiricilere ve DevOps uzmanlarına yönelik genişletilebilir bir Uygulama Performans Yönetimi (APM) hizmetidir. Canlı uygulamaları izlemek için kullanılır. Performans sorunlarını algılar ve sorunları tanılamak ve kullanıcıların bir uygulamayla neler yaptığını anlamak için analiz araçları içerir. Performansı ve kullanılabilirliği sürekli geliştirmenize yardımcı olmak amacıyla tasarlanmıştır.<br><br>**VOD kullanımı:**<br>Tüm Günlükler Application Insights gönderilebilir. Başarılı bir şekilde oluşturulan iş iletilerini arayarak her işi hangi örneğin işlediğini görmek mümkündür. Benzersiz tanımlayıcı ve örnek adı bilgileri de dahil olmak üzere tüm gönderilen iş meta verilerini içerebilir. [Application Insights hakkında daha fazla bilgi edinin](../../azure-monitor/app/app-insights-overview.md). |
## <a name="architecture"></a>Mimari

Bu üst düzey diyagramda, yüksek kullanılabilirlik ve Medya Hizmetleri ile çalışmaya başlamanızı sağlamak için sunulan örneğin mimarisi gösterilmektedir.

[![İsteğe bağlı video (VOD) yüksek düzey mimari diyagramı ](media/architecture-high-availability-encoding-concept/high-availability-architecture.svg)](./media/architecture-high-availability-encoding-concept/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>En iyi uygulamalar

### <a name="regions"></a>Bölgeler

* İki (veya daha fazla) Azure Media Services hesabı [oluşturun](./account-create-how-to.md) . İki hesabın farklı bölgelerde olması gerekir. Daha fazla bilgi için [Azure Media Services hizmetinin dağıtıldığı bölgeler](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)bölümüne bakın.
* Medyanızı, işi göndermeyi planladığınız aynı bölgeye yükleyin. Kodlama başlatma hakkında daha fazla bilgi için bkz. bir [https URL 'sinden iş girişi oluşturma](./job-input-from-http-how-to.md) veya [yerel bir dosyadan iş girişi oluşturma](./job-input-from-local-file-how-to.md).
* [İşi](./transform-jobs-concept.md) başka bir bölgeye yeniden göndermeniz gerekirse, `JobInputHttp` `Copy-Blob` verileri kaynak varlık kapsayıcısından alternatif bölgedeki bir varlık kapsayıcısına kopyalamak için kullanabilirsiniz.

### <a name="monitoring"></a>İzleme

* `JobStateChange`Her hesaptaki ileti için Azure Event Grid aracılığıyla abone olun.
    * Azure portal veya CLı aracılığıyla [olaylara kaydolun](./reacting-to-media-services-events.md) (Ayrıca, bunu Event Grid Yönetimi SDK 'sı ile de yapabilirsiniz)
    * [Microsoft. Azure. EventGrid SDK 'sını](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) kullanın (yerel olarak Media Services olaylarını destekler).
    * Azure Işlevleri aracılığıyla Event Grid olaylarını da kullanabilirsiniz.

    Daha fazla bilgi için:

    * Azure Event Grid iletilerinin bazı nedenlerle gecikildiği durumlarda geri dönüş ekleme dahil Azure Event Grid bir işi nasıl izleyeceğinizi gösteren [Ses analizi örneğine](./transform-jobs-concept.md) bakın.
    * [Media Services olaylar için Azure Event Grid şemalarına](./media-services-event-schemas.md)göz atın.

* Bir [iş](./transform-jobs-concept.md)oluşturduğunuzda:
    * Şu anda kullanılan hesapların listesinden rastgele bir hesap seçin (Bu liste normalde her iki hesabı da içerir, ancak sorunlar algılanırsa yalnızca bir hesap içerebilir). Liste boşsa, bir işlecin bir uyarı oluşturup Araştırabilmesi için bir uyarı yükseltin.
    * Her bir Inflight işini ve kullanılan bölge/hesabı izlemek için bir kayıt oluşturun.
* `JobStateChange`İşleyiciniz bir işin zamanlanan duruma ulaştığı bir bildirim aldığında, zamanlanan duruma ve kullanılan bölgeye/hesaba giren zamanı kaydedin.
* `JobStateChange`İşleyiciniz bir işin işleme durumuna ulaştığı bir bildirim aldığında, iş için kaydı işleme olarak işaretleyin ve işlem durumuna giren süreyi kaydedin.
* `JobStateChange`İşleyiciniz bir işin son duruma ulaştığı bir bildirim aldığında (tamamlandı/hatalı/Iptal edildi), iş için kaydı uygun şekilde işaretleyin.
* İşlerin kayıtlarınıza düzenli olarak baktığı ayrı bir işleme sahiptir
    * Zamanlanan durumda, belirli bir bölge için makul bir süre içinde işleme durumuna kadar gelişmiş olmayan işleriniz varsa, o bölgeyi şu anda kullanılan hesapların listesinden kaldırın. İş gereksinimlerinize bağlı olarak, bu işleri hemen iptal edip diğer bölgeye yeniden gönderebilirsiniz. Ya da bir sonraki duruma geçmek için birkaç zaman daha verebilirsiniz.
    * Hesap listesinden bir bölge kaldırılmışsa, listeye geri eklemeden önce onu kurtarma için izleyin. Bölgesel sistem durumu, bölgedeki mevcut işler aracılığıyla (iptal edilmediğinde ve yeniden gönderiyorlarsa), hesabı bir süre sonra listeye geri ekleyerek ve Azure Media Services etkileyebilecek kesintiler hakkında Azure iletişimini izleyen operatörler tarafından izlenebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Kod örneklerine](/samples/browse/?products=azure-media-services) göz atın