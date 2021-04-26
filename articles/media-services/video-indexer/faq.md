---
title: Video Indexer hakkında sık sorulan sorular-Azure
titleSuffix: Azure Media Services
description: Bu makale Azure Media Services Video Indexer hakkında sık sorulan soruların yanıtlarını verir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/12/2020
ms.author: juliako
ms.openlocfilehash: 0b7f0427708eece75b5d418b68dd1c7a2e90853f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108702"
---
# <a name="video-indexer-frequently-asked-questions"></a>Video Indexer sık sorulan sorular

Bu makale Video Indexer hakkında sık sorulan soruları yanıtlar.

## <a name="general-questions"></a>Genel sorular

### <a name="what-is-video-indexer"></a>Video Indexer nedir?

Video Indexer, Microsoft Azure Media Services bir parçası olan yapay zeka hizmetidir. Video Indexer, bir videodan kolayca derin Öngörüler ayıklamanızı sağlayan birden çok makine öğrenimi modeli düzenlemesi sağlar. Gelişmiş ve doğru Öngörüler sağlamak için Video Indexer videonun birden çok kanalını kullanmasını sağlar: ses, konuşma ve görsel. Video Indexer içgörüler, içerik bulunabilirliği ve erişilebilirliği geliştirmek, yeni para, fırsatları oluşturmak veya öngörüleri kullanan yeni deneyimler oluşturmak gibi birçok şekilde kullanılabilir. Video Indexer, hesabınızdaki modellerin test edilmesi, yapılandırılması ve özelleştirilmesi için Web tabanlı bir arabirim sağlar. Geliştiriciler, Video Indexer üretim sistemine tümleştirmede REST tabanlı bir API kullanabilir. 

### <a name="what-can-i-do-with-video-indexer"></a>Video Indexer ile ne yapabilirim?

Video Indexer medya dosyalarında gerçekleştirebileceği işlemlerden bazıları şunlardır:

* Konuşmayı tanımlama ve ayıklama ve hoparlörleri tanımlama.
* Videoda ekran metnini tanımlama ve ayıklama.
* Bir video dosyasındaki nesneler algılanıyor.
* Ses İzlemelerden (örneğin, Microsoft) markalarını ve bir videodaki ekran metnini tanımla.
* Ünlüler veritabanından yüzleri ve Kullanıcı tanımlı yüzlerin bir veritabanını algılayarak ve tanıyor.
* Ses ve video içeriklerde ele alınmayan ancak bahsedilen konular ayıklanıyor.
* Ses izağından kapalı açıklamalı alt yazılar veya alt yazılar oluşturma.

Daha fazla bilgi ve Video Indexer özellikler için bkz. [genel bakış](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Video Indexer kullanmaya başlamak Nasıl yaparım??

Video Indexer, Web tabanlı arabirimde 600 dakika ve API aracılığıyla 2.400 dakika boyunca size sağlayan ücretsiz bir deneme teklifi içerir. [Video Indexer Web tabanlı arabirimde oturum](https://www.videoindexer.ai/) açabilir ve herhangi bir Web kimliği kullanarak ve bir Azure aboneliği ayarlamanıza gerek kalmadan kendiniz deneyin. Video Indexer kullanma hakkında daha iyi fikir edinmek için [Bu kolay giriş Laboratuvarı](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) izleyin.

Videoları ve ses uçlarını ölçeklendirerek, Video Indexer ücretli bir Microsoft Azure aboneliğine bağlayabilirsiniz. [Fiyatlandırma sayfasında fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) hakkında daha fazla bilgi edinebilirsiniz.

Kullanmaya başlarken hakkında daha fazla bilgi edinmek için bkz [..](video-indexer-get-started.md)

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Video Indexer kullanmak için kodlama becerileri almam gerekiyor mu?

Bir **kodlama gerekmeden** hesabınızı değerlendirmek, yapılandırmak ve yönetmek için video Indexer Web tabanlı arabirimi kullanabilirsiniz.  Daha karmaşık uygulamalar geliştirmeye hazırsanız, Azure Logic Apps veya Azure Işlevleri [gibi sunucusuz teknolojileri kullanarak](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) kendi uygulamalarınıza, Web sitelerinize veya özel iş akışlarıyla video Indexer bütünleştirmek IÇIN [video Indexer API](https://api-portal.videoindexer.ai/) 'sini kullanabilirsiniz.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Video Indexer kullanmak için makine öğrenimi becerileri gerekir mi?

Hayır, Video Indexer birden çok makine öğrenimi modelinin tek bir işlem hattına tümleştirilmesini sağlar. Video Indexer aracılığıyla bir video veya ses dosyasının dizinlenmesini, herhangi bir makine öğrenimi özelliği veya müşterinin bölümünde gerekli algoritmalarla ilgili bilgi sahibi olmadan, paylaşılan bir zaman çizelgesinde ayıklanan tüm Öngörüler kümesini alır.

### <a name="what-media-formats-does-video-indexer-support"></a>Video Indexer hangi medya biçimlerini destekler?

Video Indexer en yaygın medya biçimlerini destekler. Daha fazla ayrıntı için [Azure Media Encoder standart biçimler](../latest/encode-media-encoder-standard-formats-reference.md) listesine bakın.

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>Nasıl yaparım? medya dosyasını Video Indexer karşıya yükleyin ve sınırlamalar nelerdir?

Video Indexer Web tabanlı portalda karşıya dosya yükleme iletişim kutusunu kullanarak veya kaynak dosyayı doğrudan barındıran bir URL 'yi işaret ederek bir medya dosyası yükleyebilirsiniz (bkz. [örnek](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). İFrame veya ekleme kodu kullanarak medya içeriğini barındıran herhangi bir URL çalışmaz (bkz. [örnek](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). 

Daha fazla bilgi için lütfen bu [nasıl yapılır Kılavuzu](./upload-index-videos.md)'na bakın.

#### <a name="limitations"></a>Sınırlamalar

* Videonun adı 80 karakterden uzun olamaz.
* Bayt dizisi kullanarak bir video yüklerseniz, video boyutu 2 GB ile sınırlıdır (URL kullanılırken, 1 GB). 

Kapsamlı liste için bkz. [konuları ve sınırlamaları karşıya yükleme](upload-index-videos.md#uploading-considerations-and-limitations).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Medyadan Öngörüler çıkarmak için ne kadar sürer Video Indexer?

Her ikisi de Video Indexer API ve Video Indexer Web tabanlı arabirim kullanarak bir video veya ses dosyasının dizin oluşturma süresi, dosya uzunluğu ve kalitesi, dosyada bulunan Öngörüler sayısı, kullanılabilir [ayrılmış birim](../previous/media-services-scale-media-processing-overview.md) sayısı ve [akış uç noktasının](../previous/media-services-streaming-endpoints-overview.md) etkin olup olmadığı gibi birden çok parametreye bağlıdır. Daha iyi bir fikir edinmek için kendi içeriklerinize birkaç test dosyası çalıştırmanızı ve ortalama bir fikir almanızı öneririz.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Video Indexer ile işlemleri otomatikleştirmek için özelleştirilmiş iş akışları oluşturabilir miyim?

Evet, Video Indexer Logic Apps, Flow ve [Azure işlevleri](https://azure.microsoft.com/services/functions/)gibi sunucusuz teknolojilerle tümleştirilebilir. Daha fazla ayrıntı için [mantıksal uygulama](https://azure.microsoft.com/services/logic-apps/) ve [akış](https://flow.microsoft.com/en-us/) bağlayıcıları hakkında daha fazla bilgiyi [buradan](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)video Indexer bulabilirsiniz. [Video Indexer örnekleri](https://github.com/Azure-Samples/media-services-video-indexer) deposunda iş ortakları tarafından gerçekleştirilen bazı Otomasyon projelerini görebilirsiniz.

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Video Indexer hangi Azure bölgelerinde kullanılabilir?

[Bölgeler](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) sayfasında hangi Azure bölgelerinin video Indexer kullanılabilir olduğunu görebilirsiniz.

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Video Indexer modellerini belirli kullanım alanım için özelleştirebilir miyim? 

Evet. Video Indexer, ihtiyaçlarınıza daha iyi uyum sağlamak için kullanılabilir modellerin bazılarını özelleştirebilirsiniz. 

Örneğin, kişi modelimiz, ünlüme tanımanın hazır 1.000.000 yüzlerini destekler, ancak bu veritabanında olmayan diğer yüzeyleri tanımak için de eğitebilirsiniz. 

Ayrıntılar için bkz. [kişi](customize-person-model-overview.md), [markaların](customize-brands-model-overview.md)ve [dil](customize-language-model-overview.md) modellerinin özelleştirilmesi hakkındaki makaleler. 

###  <a name="can-i-edit-the-videos-in-my-library"></a>Kütüphanedeki videoları düzenleyebilir miyim?

Evet. **Projeler** sekmesine ulaşmak için Player 'daki ekran görüntüsü ' nden veya **Düzenleyici 'de aç** düğmesinden **video Düzenle** düğmesine basın. Yeni bir proje oluşturabilir ve kitaplığınızdan daha fazla video ekleyerek bunları birlikte düzenleyebilirsiniz, işiniz bittiğinde videonuzu işleyebilir ve indirebilirsiniz. 

Yeni videolarınız hakkında öngörüler elde etmek istiyorsanız, Video Indexer ile dizin oluşturup kendi içgörüleri ile kitaplığınızda görüntülenir.

### <a name="can-i-index-multiple-audio-streams-or-channels"></a>Birden çok ses akışını veya kanalını dizine ekleyebilir miyim?

Birden çok ses akışı varsa, Video Indexer birinciden karşılaşacaktır ve yalnızca bu akışı işleyebilir. Her ses akışı Video Indexer işlemde, farklı kanalları alır (varsa) ve bunları mono olarak birlikte işler. Akışlar/kanallar işleme için, dizin oluşturmadan önce dosya üzerinde ffmpeg komutlarını kullanabilirsiniz.

### <a name="what-is-the-sla-for-video-indexer"></a>Video Indexer SLA nedir?

Azure Media Service 'in SLA 'sı Video Indexer kapsamakta ve [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) sayfasında bulunabilir. SLA yalnızca Video Indexer ücretli hesaplara uygulanır ve ücretsiz deneme için geçerli değildir.

## <a name="privacy-questions"></a>Gizlilik soruları

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Video ve ses dosyaları Video Indexer tarafından dizinlenebilir mi?

Evet, Video Indexer Web sitesini veya API 'yi kullanarak Video Indexer dosyayı silmediğiniz takdirde, video ve ses dosyalarınız saklanır. Ücretsiz deneme için, dizininizdeki video ve ses dosyaları Azure bölgesinde Doğu ABD depolanır. Aksi halde, video ve ses dosyalarınız Azure aboneliğinizin depolama hesabında depolanır.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Video Indexer portalında depolanan dosyalarımı silebilir miyim?

Evet, her zaman video ve ses dosyalarınızı, Video Indexer tarafından ayıklanan meta verileri ve öngörüleri de silebilirsiniz. Video Indexer bir dosyayı sildikten sonra dosya ve meta verileri ve öngörüleri Video Indexer kalıcı olarak kaldırılır. Ancak, Azure depolama 'da kendi yedekleme çözümünüzü uyguladıysanız, dosya Azure depolama alanında kalır.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Video Indexer hesabımın Kullanıcı erişimini denetleyebilir miyim?

Evet, yalnızca hesap yöneticileri kişileri kendi hesaplarına davet edebilir ve davet edebilir ve kimin ve salt okuma erişimine sahip olduğunu atayabilir.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Video Indexer ve/veya tarafından depolanan video ve ses dosyalarına kim ve ayıklanan meta veriler ve Öngörüler erişebilir?

Gizlilik ayarı olarak genel olan videonuzya da ses içeriğinize, video veya ses içeriğiniz ve öngörüleri bağlantısı olan herkes erişebilir. Gizlilik ayarı olarak özel olan videonuzya da ses içeriğinize yalnızca video veya ses içeriğinin hesabına davet edilen kullanıcılar erişebilir. İçeriğinizin gizlilik ayarı, Video Indexer çıkaran meta veriler ve Öngörüler için de geçerlidir. Video veya ses dosyanızı karşıya yüklerken gizlilik ayarını atarsınız. Ayrıca, dizin oluşturma işleminden sonra gizlilik ayarını değiştirebilirsiniz.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Microsoft 'un, Video Indexer ve/veya tarafından depolanan video veya ses dosyaları ve çıkarılan meta veriler ve içgörüler tarafından hangi erişimi vardır?

[Azure Online Services koşulları](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) 'na (OST) tamamen sahip olursunuz ve Microsoft, Içeriğinize ve Microsoft gizlilik bildirimine göre içeriğinizi yalnızca video Indexer ayıklayarak, meta veriler ve öngörülere erişir.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Video Indexer hesabında oluşturduğum özel modeller diğer hesaplar için kullanılabilir mi?

 Hayır, hesabınızda oluşturduğunuz özel modeller başka bir hesap için kullanılamaz. Video Indexer Şu anda hesabınızda özel [marka](customize-brands-model-overview.md), [dil](customize-language-model-overview.md)ve [kişi](customize-person-model-overview.md) modelleri oluşturmanıza izin veriyor. Bu modeller yalnızca modelleri oluşturduğunuz hesapta kullanılabilir.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Video Indexer tarafından dizin oluşturulan ve Video Indexer kullandığım Azure bölgesinde tutulan içerik var mı?

Evet, Azure aboneliğinizde birden çok Azure bölgesi kullanan bir el ile yapılandırmanız yoksa, içerik ve öngörüleri Azure bölgesi içinde tutulur. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Video Indexer için Gizlilik ilkesi nedir?

Video Indexer, [Microsoft gizlilik bildirimi](https://privacy.microsoft.com/privacystatement)tarafından ele alınmıştır. Gizlilik bildirimi Microsoft işlemleri, Microsoft 'un bunu nasıl işleyeceği ve Microsoft 'un bunu hangi amaçlarla işledikleri konusunda açıklar. Gizlilik hakkında daha fazla bilgi edinmek için [Microsoft Güven Merkezi](https://www.microsoft.com/trustcenter)' ni ziyaret edin.

### <a name="what-certifications-does-video-indexer-have"></a>Video Indexer hangi sertifikaları vardır?

Video Indexer Şu anda SOC sertifikasıdır. Video Indexer sertifikasını gözden geçirmek için lütfen [Microsoft Güven Merkezi](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure)' ne bakın.

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Özel ve genel videolar arasındaki fark nedir? 

Videolar Video Indexer karşıya yüklendiğinde, iki gizlilik ayarlarından birini seçebilirsiniz: özel ve genel. Anonim ve tanımlanmamış kullanıcılar dahil herkes için ortak videolara erişilebilir. Özel olanlar yalnızca hesap üyeleriyle kısıtlıdır. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Bir videoyu herkese açık olarak yüklemeye çalıştım ve uygunsuz veya rahatsız edici içerik için işaretlendi, ne anlama geliyor? 

Bir videoyu Video Indexer karşıya yüklerken, hiçbir uygun olmayan içeriğe sahip olmadığından emin olmak için algoritmalar ve modeller tarafından otomatik içerik analizi yapılır. Açık içerik içeren bir videonun şüpheli olduğu bulunursa, bunu ortak olarak ayarlamak mümkün olmayacaktır. Ancak, hesap üyeleri buna özel bir video olarak erişebilir (görüntüleyin, öngörüleri ve ayıklanan yapıtları indirebilir ve hesap üyeleri tarafından kullanılabilen diğer işlemleri gerçekleştirebilir).   

Videoyu genel erişim için ayarlamak için şunlardan birini yapabilirsiniz: 

* Kendi arabirim katmanınızı (uygulama veya Web sitesi gibi) oluşturun ve Video Indexer hizmetiyle etkileşim kurmak için kullanın. Bu şekilde, bu video portalımızda özel kalır ve kullanıcılarınız arabiriminiz aracılığıyla etkileşime girebilirler. Örneğin, öngörüleri elde edebilir veya kendi arabiriminizdeki videonun görüntülenmesine izin verebilirsiniz. 
* İçeriğin açık olmadığı varsayılarak kısıtlamanın kaldırılmasına neden olacak içerik için bir insan incelemesi isteyin. 

    Bu seçenek, Video Indexer Web sitesinin doğrudan kullanıcılarınız tarafından arabirim katmanı olarak kullanılması ve genel (kimliği doğrulanmamış) görüntülenmesi durumunda araştırılabilir. 

## <a name="api-questions"></a>API soruları

### <a name="what-apis-does-video-indexer-offer"></a>Hangi API 'Ler Video Indexer sunmaktadır?

Video Indexer API 'Leri, dizin oluşturma, meta veri ayıklama, varlık yönetimi, çeviri, ekleme, modellerin özelleştirilmesi ve daha fazlasını sağlar. Video Indexer API 'sini kullanma hakkında daha ayrıntılı bilgi edinmek için [video Indexer geliştirici portalına](https://api-portal.videoindexer.ai/)bakın.

### <a name="what-client-sdks-does-video-indexer-offer"></a>Hangi istemci SDK 'larının Video Indexer teklifi vardır?

Şu anda sunulan hiçbir istemci SDK 'Sı yok. Video Indexer ekibi SDK 'larda çalışıyor ve yakında teslim edilecek planlar.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Nasıl yaparım? Video Indexer API 'SI ile çalışmaya başlama

[Öğreticiyi izleyin: video Indexer API 'si ile çalışmaya başlayın](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Video Indexer API 'si ve Azure Media Service v3 API 'SI arasındaki fark nedir?

Şu anda Video Indexer API ve Azure Media Service v3 API 'SI tarafından sunulan özelliklerde bazı örtüşmeler vardır. Her [iki hizmeti de](compare-video-indexer-with-media-services-presets.md)nasıl karşılaştırılacağı hakkında daha fazla bilgi edinebilirsiniz.

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>API erişim belirteci nedir ve neden ihtiyacım var?

Video Indexer API 'si bir yetkilendirme API 'SI ve bir Operations API 'si içerir. Yetkilendirmeler API 'SI size erişim belirteci veren çağrılar içerir. İşlemler API'sine yapılan her çağrı, çağrının yetkilendirme kapsamına uyan bir erişim belirteci ile ilişkilendirilmelidir.

Video Indexer API 'Lerini güvenlik amacıyla kullanmak için erişim belirteçleri gerekir. Bu, herhangi bir çağrının sizin veya hesabınıza erişim izinleri olan kişilerin gelmesini sağlar. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Hesap erişim belirteci, Kullanıcı erişim belirteci ve video erişim belirteci arasındaki fark nedir?

* Hesap düzeyi: Hesap düzeyindeki erişim belirteçleri, hesap veya video düzeyinde işlem gerçekleştirmenize olanak sağlar. Örneğin, bir videoyu karşıya yükleyin, tüm videoları listeleyin, video öngörüleri alın.
* Kullanıcı düzeyi-Kullanıcı düzeyi erişim belirteçleri, Kullanıcı düzeyinde işlem gerçekleştirmenize olanak tanır. Örnek: ilişkili hesaplar alma.
* Video düzeyi: Video düzeyindeki erişim belirteçleri, belirli bir video üzerinde işlem gerçekleştirmenize olanak sağlar. Örnek: video içgörüsü alma, açıklamalı alt yazı indirme, pencere öğesi alma vb.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Ne sıklıkla yeni bir erişim belirteci almam gerekir? Erişim belirteçlerinin süre sonu ne zaman dolacak?

Erişim belirteçlerinin her saat sonu dolduğunda, her saat için yeni bir erişim belirteci oluşturmanız gerekir. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Geliştirici portalı Video Indexer oturum açma seçenekleri nelerdir?

[Oturum açma bilgileri](release-notes.md#october-2020)hakkında bir sürüm notuna bakın.

Bir kimlik sağlayıcısı kullanarak e-posta hesabınızı kaydettikten sonra, bu e-posta hesabını başka bir kimlik sağlayıcısıyla kullanamazsınız.

## <a name="billing-questions"></a>Faturalama soruları

### <a name="how-much-does-video-indexer-cost"></a>Video Indexer maliyeti ne kadar sürer?

Video Indexer, Dizin oluşturduğunuz içerik girişi süresine bağlı olarak basit bir Kullandıkça Öde fiyatlandırma modeli kullanır. Kodlama, akış, depolama, ağ kullanımı ve medya ayrılmış birimleri için ek ücretler uygulanabilir. Daha fazla bilgi için [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) sayfasına bakın.

### <a name="when-am-i-billed-for-using-video-indexer"></a>Video Indexer kullanmak için ne zaman faturalandırılırım?

Dizine eklenecek bir video gönderilirken, Kullanıcı dizin oluşturmayı video analizi, ses analizi veya her ikisi olacak şekilde tanımlar. Bu, hangi SKU 'Ların ücretlendirileceğini saptacaktır. İşlem sırasında kritik düzey hatası varsa, yanıt olarak bir hata kodu döndürülür. Böyle bir durumda faturalandırma gerçekleşmez.  Kritik bir hata, kodunuzdaki bir hata ya da hizmetin sahip olduğu bir iç bağımlılıkta kritik bir hata nedeniyle oluşabilir. Yanlış tanımlama veya öngörü ayıklama gibi hatalar kritik olarak değerlendirilmez ve bir yanıt döndürülür. Geçerli bir (hata olmayan kod) yanıtının döndürüldüğü her durumda faturalandırma oluşur.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Video Indexer ücretsiz bir deneme sunuyor mu?

Evet, Video Indexer tam hizmet ve API işlevselliği sağlayan ücretsiz bir deneme sunar. Web tabanlı arabirim kullanıcıları ve API kullanıcıları için 2.400 dakika 600 dakikalık videoların bir kotası vardır. 

## <a name="next-steps"></a>Sonraki adımlar

* [Genel Bakış](video-indexer-overview.md)
* [Stack Overflow](https://stackoverflow.com/search?q=video-indexer)
