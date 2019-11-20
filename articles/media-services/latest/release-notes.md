---
title: Azure Media Services v3 sürüm notları | Microsoft Docs
description: En son gelişmelerden haberdar olmak için bu makalede, Azure Media Services v3 hakkında en son güncelleştirmeler sunulmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/07/2019
ms.author: juliako
ms.openlocfilehash: 34d68974366eb17704b4ea1291bd1d29a0eea95a
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168626"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 sürüm notları

En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

* En son yayınlar
* Bilinen sorunlar
* Hata düzeltmeleri
* Kullanım dışı işlev

## <a name="known-issues"></a>Bilinen sorunlar

> [!NOTE]
> Şu anda, v3 kaynaklarını yönetmek için Azure portalını kullanamıyorsunuz. [REST API](https://aka.ms/ams-v3-rest-sdk), CLI veya desteklenen SDK 'lardan birini kullanın.

Daha fazla bilgi için bkz. [Media Services V2 'den v3 'e geçiş Için geçiş kılavuzu](migrate-from-v2-to-v3.md#known-issues).

## <a name="september-2019"></a>Eylül 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Canlı olayların canlı doğrusal kodlaması

Media Services v3, canlı etkinliklerin canlı doğrusal kodlamasının 24 saat x 365 gün önizlemesini duyuruyor.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Medya işlemcilerinin kullanımdan kaldırılması

*Azure Media Indexer* ve *Azure Media Indexer 2 Preview*kullanım dışı olarak duyuruluyoruz. [Azure Media Indexer](../previous/media-services-index-content.md) medya işlemcisi, 1 Ekim 2020 ' de kullanımdan kaldırılacaktır. [Azure Media Indexer 2 Preview](../previous/media-services-process-content-with-indexer2.md) medya Işlemcileri 1 Ocak 2020 tarihinde kullanımdan kaldırılacaktır. [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) bu eski medya işlemcilerinin yerini alır.

Daha fazla bilgi için [Azure Media Indexer ve Azure Media Indexer 2 ' den Azure Media Services video Indexer geçiş](../previous/migrate-indexer-v1-v2.md)konusuna bakın.

## <a name="august-2019"></a>Ağustos 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Güney Afrika bölgesel çifti Media Services için açık 

Media Services artık Güney Afrika Kuzey ve Güney Afrika Batı bölgelerinde kullanılabilir.

Daha fazla bilgi için bkz. [Media Services v3 'nin bulunduğu bulutlar ve bölgeler](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Medya işlemcilerinin kullanımdan kaldırılması

30 Kasım 2019 ' de devre dışı bırakılmakta olan *Windows Azure Medya Kodlayıcısı* (WAME) ve *Azure Media Encoder* (AME) medya işlemcilerinin kullanımdan kaldırılması duyuruluyor.

Ayrıntılar için bkz. [WAME 'i Media Encoder Standard geçirin](https://go.microsoft.com/fwlink/?LinkId=2101334) ve [adı Media Encoder Standard geçirin](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>2019 Temmuz

### <a name="content-protection"></a>Content Protection

Belirteç kısıtlamasıyla korunan içerik akışı yaparken, son kullanıcılar anahtar teslim isteğinin bir parçası olarak gönderilen bir belirteç elde etmeniz gerekir. *Belirteç yeniden yürütme engellemesi* özelliği, Media Services müşterilerin aynı belirtecin bir anahtar veya lisans istemek için kaç kez kullanılabileceği konusunda bir sınır ayarlamasına olanak tanır. Daha fazla bilgi için bkz. [belirteç yeniden yürütme engellemesi](content-protection-overview.md#token-replay-prevention).

Bu özellik şu anda ABD Orta ve ABD Orta Batı kullanılabilir.

## <a name="june-2019"></a>Haziran 2019

### <a name="video-subclipping"></a>Video alt kırpması

Artık bir [işi](https://docs.microsoft.com/rest/api/media/jobs)kullanarak kodlarken videoyu kırpabilir veya alt kırpabilirsiniz. 

Bu işlev, [Builtınstandardencoderönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ön ayarları veya [Standardencoderönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) önayarları kullanılarak oluşturulan [dönüşümlerle](https://docs.microsoft.com/rest/api/media/transforms) birlikte kullanılır. 

Örneklere bakın:

* [.NET ile videoyu alt kırpın](subclip-video-dotnet-howto.md)
* [REST ile videoyu alt kırpın](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Mayıs 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Izleyici Media Services tanılama günlükleri ve ölçümleri desteği

Azure Izleyici 'yi artık Media Services tarafından kullanılabilen telemetri verilerini görüntülemek için kullanabilirsiniz.

* Media Services anahtar teslim uç noktası tarafından gönderilen istekleri izlemek için Azure Izleyici tanılama günlüklerini kullanın. 
* Media Services [akış uç noktaları](streaming-endpoint-concept.md)tarafından yayılan ölçümleri izleyin.   

Ayrıntılar için bkz. [Media Services ölçümleri ve tanılama günlüklerini izleme](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Dinamik paketlemeyle çoklu ses izleme desteği 

Birden çok codec ve dilde birden çok ses izi bulunan varlıklar akışı yaparken, [dinamik paketleme](dynamic-packaging-overview.md) artık HLS çıkışı (sürüm 4 veya üzeri) için birden çok ses parçasını desteklemektedir.

### <a name="korea-regional-pair-is-open-for-media-services"></a>Kore bölgesel çifti Media Services için açık 

Media Services artık Kore Orta ve Kore Güney bölgelerinde kullanılabilir. 

Daha fazla bilgi için bkz. [Media Services v3 'nin bulunduğu bulutlar ve bölgeler](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Performans iyileştirmeleri

Media Services performans iyileştirmeleri içeren güncelleştirmeler eklendi.

* İşleme için desteklenen en büyük dosya boyutu güncelleştirildi. Bkz., [Kotalar ve sınırlamalar](limits-quotas-constraints.md).
* [Kodlama hızı iyileştirmeleri](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>2019 Nisan

### <a name="new-presets"></a>Yeni Önayarlar

* [Facedetectorönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) yerleşik çözümleyici önayarlarına eklendi.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) , yerleşik kodlayıcı önayarlarına eklendi. Daha fazla bilgi için bkz. [içeriğe duyarlı kodlama](cae-experimental.md). 

## <a name="march-2019"></a>Mart 2019

Dinamik paketleme artık Dolby Atmos 'ı destekliyor. Daha fazla bilgi için bkz. [dinamik paketleme tarafından desteklenen ses codec bileşenleri](dynamic-packaging-overview.md#audio-codecs).

Artık akış bulucusu için uygulanacak varlık veya hesap filtrelerinin bir listesini belirtebilirsiniz. Daha fazla bilgi için bkz. [akış bulucu ile filtreleri ilişkilendirme](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Şubat 2019

Media Services v3 artık Azure Ulusal bulutlarında desteklenmektedir. Tüm özellikler henüz tüm bulutlarda kullanılamaz. Ayrıntılar için bkz. [Azure Media Services v3 'nin bulunduğu bulutlar ve bölgeler](azure-clouds-regions.md).

Media Services için Azure Event Grid şemalarına [Microsoft. Media. Joi Putprogress](media-services-event-schemas.md#monitoring-job-output-progress) olayı eklendi.

## <a name="january-2019"></a>Ocak 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard ve MPı dosyaları 

MP4 dosyaları üretmek için Media Encoder Standard ile kodlarken, yeni bir. MPI dosyası oluşturulur ve çıkış varlığına eklenir. Bu MPı dosyası, [dinamik paketleme](dynamic-packaging-overview.md) ve akış senaryoları için performansı iyileştirmeye yöneliktir.

MPı dosyasını değiştirmemelisiniz veya kaldırmamalıdır ya da bu tür bir dosyanın var olan (veya olmayan) hizmetinize herhangi bir bağımlılığı almalısınız.

## <a name="december-2018"></a>Aralık 2018

V3 API 'sinin GA sürümündeki güncelleştirmeler şunlardır:
       
* **Varlık filtreleri** ve **hesap filtreleri**için **presentationtimerange** özellikleri artık ' gerekli ' değildir. 
* **İşler** ve **dönüşümler** için $top ve $Skip sorgu seçenekleri kaldırılmıştır ve $OrderBy eklenmiştir. Yeni sıralama işlevinin eklenmesinin bir parçası olarak, $top ve $skip seçeneklerinin, uygulanmamasına rağmen daha önce daha önce açığa çıkmıştı.
* Numaralandırma genişletilebilirliği yeniden etkinleştirildi. Bu özellik SDK 'nın önizleme sürümlerinde etkinleştirilmiştir ve GA sürümünde yanlışlıkla devre dışı bırakıldı.
* Önceden tanımlanmış iki akış ilkesi yeniden adlandırıldı. **Securestreaming** artık **Multidrmcencstreaming**. **SecureStreamingWithFairPlay** şimdi **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>2018 Kasım

CLı 2,0 modülü artık [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50 için kullanılabilir.

### <a name="new-commands"></a>Yeni komutlar

- [az AMS hesabı](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az AMS Account-Filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az AMS varlık](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az AMS varlık-filtre](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az AMS Content-Key-Policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az AMS Job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az AMS canlı-olay](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az AMS Live-Output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az AMS streaming-Endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az AMS streaming-Locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az AMS Account MRU](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) -medya ayrılmış birimlerini yönetmenizi sağlar. Daha fazla bilgi için bkz. [Ölçek medya ayrılmış birimleri](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Yeni özellikler ve son değişiklikler

#### <a name="asset-commands"></a>Varlık komutları

- ```--storage-account``` ve ```--container``` bağımsız değişkenleri eklendi.
- @No__t-0 komutunda, sona erme saati (şimdi + 23h) ve izinler (okuma) için varsayılan değerler eklendi.

#### <a name="job-commands"></a>İş komutları

- ```--correlation-data``` ve ```--label``` bağımsız değişkenleri eklendi
- ```--output-asset-names``` ```--output-assets``` olarak yeniden adlandırıldı. Şimdi, ' assetName = Label ' biçiminde varlıkların boşlukla ayrılmış bir listesini kabul eder. Etiketi olmayan bir varlık şu şekilde gönderilebilir: ' assetName = '.

#### <a name="streaming-locator-commands"></a>Akış Bulucu komutları

- ```az ams streaming locator``` taban komutu ```az ams streaming-locator``` ile değiştirilmiştir.
- ```--streaming-locator-id``` ve ```--alternative-media-id support``` bağımsız değişkenleri eklendi.
- ```--content-keys argument``` bağımsız değişkeni güncelleştirildi.
- ```--content-policy-name``` ```--content-key-policy-name``` olarak yeniden adlandırıldı.

#### <a name="streaming-policy-commands"></a>Akış Ilkesi komutları

- ```az ams streaming policy``` taban komutu ```az ams streaming-policy``` ile değiştirilmiştir.
- @No__t-0 ' da şifreleme parametreleri desteği eklendi.

#### <a name="transform-commands"></a>Dönüşüm komutları

- ```--preset-names``` bağımsız değişkeni ```--preset``` ile değiştirilmiştir. Artık tek seferde yalnızca 1 çıkış/ön ayar ayarlayabilirsiniz (daha fazlasını eklemek için ```az ams transform output add```). Ayrıca, yolu özel JSON 'unuza geçirerek özel Standardencoderönayar ayarlayabilirsiniz.
- ```az ams transform output remove```, kaldırılacak çıkış dizini geçirerek gerçekleştirilebilir.
- ```az ams transform create``` ve ```az ams transform output add``` komutlarında @no__t 0 bağımsız değişken eklendi.

## <a name="october-2018---ga"></a>Ekim 2018-GA

Bu bölümde Azure Media Services (AMS) Ekim güncelleştirmeleri açıklanmaktadır.

### <a name="rest-v3-ga-release"></a>REST v3 GA sürümü

[Rest v3 GA sürümü](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) , canlı, hesap/varlık düzeyi bildirim FILTRELERI ve DRM desteği için daha fazla API içerir.

#### <a name="azure-resource-management"></a>Azure Kaynak Yönetimi 

Azure Kaynak yönetimi desteği, birleştirilmiş yönetim ve işlemler API 'SI (artık tek bir yerde) sunar.

Bu sürümden itibaren, canlı olaylar oluşturmak için Kaynak Yöneticisi şablonları kullanabilirsiniz.

#### <a name="improvement-of-asset-operations"></a>Varlık işlemlerinin geliştirilmesi 

Aşağıdaki geliştirmeler sunulmuştur:

- HTTP (s) URL 'Leri veya Azure Blob Storage SAS URL 'Lerinden alma.
- Varlıklar için kendi kapsayıcı adlarından istediğinizi belirtin. 
- Azure Işlevleri ile özel iş akışları oluşturmak için daha kolay çıkış desteği.

#### <a name="new-transform-object"></a>Yeni dönüştürme nesnesi

Yeni **dönüşüm** nesnesi kodlama modelini basitleştirir. Yeni nesne, kodlama Kaynak Yöneticisi şablonları ve hazır ayarları oluşturmayı ve paylaşmayı kolaylaştırır. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory kimlik doğrulaması ve RBAC

Azure AD kimlik doğrulaması ve rol tabanlı Access Control (RBAC), Azure AD 'de role veya kullanıcılara göre güvenli dönüşümler, canlı olaylar, Içerik anahtar Ilkeleri veya varlıklar sağlar.

#### <a name="client-sdks"></a>İstemci SDK'ları  

Media Services v3 sürümünde desteklenen diller: .NET Core, Java, Node. js, Ruby, TypeScript, Python, go.

#### <a name="live-encoding-updates"></a>Canlı kodlama güncelleştirmeleri

Aşağıdaki canlı kodlama güncelleştirmeleri sunulmuştur:

- Canlı için yeni düşük gecikme modu (10 saniyelik uçtan uca).
- İyileştirilmiş RTMP desteği (artan kararlılık ve daha fazla kaynak Kodlayıcısı desteği).
- RTMPS güvenli alma.

    Canlı bir olay oluşturduğunuzda, artık 4 alma URL 'Si alırsınız. 4 içe alınan URL 'Ler neredeyse aynıdır, aynı akış belirtecine (AppID) sahiptir, yalnızca bağlantı noktası numarası bölümü farklıdır. URL 'Lerden ikisi, RTMPS için birincil ve yedeğdir. 
- 24 saat kodlamayı kodlama desteği. 
- SCTE35 aracılığıyla RTMP 'de geliştirilmiş ad sinyali desteği.

#### <a name="improved-event-grid-support"></a>İyileştirilmiş Event Grid desteği

Aşağıdaki Event Grid destek geliştirmelerini görebilirsiniz:

- Logic Apps ve Azure Işlevleriyle daha kolay geliştirme için Azure Event Grid tümleştirme. 
- Kodlama, Canlı Kanallar ve daha fazlası için olaylar için abone olun.

### <a name="cmaf-support"></a>CMAF desteği

Apple HLS (iOS 11 +) için CMAF ve ' CBCS ' şifreleme desteği ve CMAF 'yi destekleyen MPEG-DASH çalarlar.

### <a name="video-indexer"></a>Video Dizinleyici

Video Indexer GA yayını Ağustos ayında duyuruldu. Şu anda desteklenen özellikler hakkında daha fazla bilgi için bkz. [video Indexer nedir](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Değişiklik planları

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Tüm özelliklerle (canlı, Içerik anahtarı Ilkeleri, hesap/varlık filtreleri, akış Ilkeleri dahil) işlemler içeren Azure CLı 2,0 modülü yakında kullanıma sunulacak. 

### <a name="known-issues"></a>Bilinen sorunlar

Yalnızca varlık veya AccountFilters için Önizleme API 'sini kullanan müşteriler aşağıdaki sorundan etkilenir.

Media Services v3 CLı veya API 'Leri ile 09/28 ve 10/12 arasında varlıklar veya hesap filtreleri oluşturduysanız, tüm varlık ve AccountFilters değerlerini kaldırmalı ve sürüm çakışması nedeniyle bunları yeniden oluşturmanız gerekir. 

## <a name="may-2018---preview"></a>Mayıs 2018-Önizleme

### <a name="net-sdk"></a>.NET SDK

.NET SDK 'da aşağıdaki özellikler mevcuttur:

* Medya içeriğini kodlamak veya çözümlemek için **dönüşümler** ve **işler** . Örnekler için bkz. [akış dosyaları](stream-files-tutorial-with-api.md) ve [Çözümleme](analyze-videos-tutorial-with-api.md).
* Son Kullanıcı cihazlarındaki yayımlama ve akış içeriği için **akış Konumlandırıcı**
* İçerik teslim edilirken anahtar teslimi ve içerik korumayı (DRM) yapılandırmak için **akış ilkeleri** ve **içerik anahtarı ilkeleri** .
* Canlı akış içeriğini alma ve arşivlemeyi yapılandırmak için **canlı olaylar** ve **canlı çıktılar** .
* Azure depolama 'da medya içeriğini depolamak ve yayımlamak için **varlıklar** . 
* Canlı ve isteğe bağlı medya içeriği için dinamik paketleme, şifreleme ve akışı yapılandırmak ve ölçeklendirmek için **akış uç noktaları** .

### <a name="known-issues"></a>Bilinen sorunlar

* Bir iş gönderilirken, HTTPS URL 'Leri, SAS URL 'Leri veya Azure Blob depolama alanında bulunan dosyalara yollar kullanarak kaynak videonuzu almak istediğinizi belirtebilirsiniz. AMS v3 şu anda HTTPS URL'leri üzerinden yığın halinde aktarım kodlamasını desteklememektedir.

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

- [Genel Bakış](media-services-overview.md)
- [Media Services V2 sürüm notları](../previous/media-services-release-notes.md)
