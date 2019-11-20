---
title: Sürüm notlarını Media Services | Microsoft Docs
description: Media Services sürüm notları
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: f76a2eb098e2d96f617d19de089e56c7e8a497fd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162774"
---
# <a name="azure-media-services-release-notes"></a>Azure Media Services sürüm notları

Bu sürüm notları, önceki sürümlerden ve bilinen sorunlardan değişiklikleri özetler Azure Media Services.

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Sizi etkileyen sorunları gidermeye odaklanabilmeniz için müşterilerimizden haberdar olmak istiyoruz. Bir sorunu bildirmek veya soru sormak için [Azure Media Services MSDN Forumu]bir gönderi gönderin. 

## <a name="a-idissuesknown-issues"></a>Bilinen sorunları <a id="issues"/>
### <a name="a-idgeneral_issuesmedia-services-general-issues"></a><a id="general_issues"/>Media Services genel sorunlar

| Sorun | Açıklama |
| --- | --- |
| REST API birkaç ortak HTTP üst bilgisi sağlanmaz. |REST API kullanarak Media Services uygulamalar geliştirirseniz, bazı ortak HTTP üst bilgi alanlarının (ISTEMCI-Istek KIMLIĞI, Istek KIMLIĞI ve RETURN-CLIENT-REQUEST-ID dahil) desteklenmediğini fark edersiniz. Üst bilgiler gelecekteki bir güncelleştirmeye eklenecektir. |
| Yüzde kodlamalı izin verilmez. |Media Services, akış içeriği için URL 'Ler oluştururken IAssetFile.Name özelliğinin değerini kullanır (örneğin, `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Bu nedenle, yüzde kodlamalı izin verilmez. Ad özelliğinin değeri, Şu sayıda [kodlamaya ayrılan karakterlerden](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)herhangi birini içeremez:! * ' ();: @ & = + $,/?% # [] ". Ayrıca, dosya adı uzantısı için yalnızca bir "." olabilir. |
| Azure Storage SDK sürüm 3. x ' in parçası olan Listbloblar yöntemi başarısız olur. |Media Services [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) sürümünü temel alan SAS URL 'leri oluşturur. Blob kapsayıcısında Blobları listelemek için depolama SDK 'sını kullanmak istiyorsanız, depolama SDK 'Sı sürüm 2. x ' in parçası olan [Cloudblobcontainer. listblobu](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) yöntemini kullanın. |
| Media Services daraltma mekanizması, hizmette aşırı istek yapan uygulamalar için kaynak kullanımını kısıtlar. Hizmet, "hizmet kullanılamıyor" 503 HTTP durum kodunu döndürebilir. |Daha fazla bilgi için [Media Services hata kodlarında](media-services-encoding-error-codes.md)503 http durum kodunun açıklamasına bakın. |
| Varlıkları sorguladığınızda, genel REST sürüm 2 ' nin sorgu sonuçlarını 1.000 sonuçlara göre kısıtladığından, bir kerede 1.000 varlıklardan oluşan bir sınır döndürülür. |[Bu .net örneğinde](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) ve [Bu REST API örnekte](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)açıklandığı gibi atla ve al (.net)/top (REST) kullanın. |
| Bazı istemciler Kesintisiz Akış bildiriminde bir yineleme Etiketi sorunu üzerinden gelebilir. |Daha fazla bilgi için [Bu bölüme](media-services-deliver-content-overview.md#known-issues)bakın. |
| Media Services .NET SDK nesneleri serileştirilemiyor ve sonuç olarak Redsıs için Azure Cache ile birlikte çalışmaz. |Redin için Azure önbelleğine eklemek üzere SDK AssetCollection nesnesini serileştirmenize çalışırsanız, bir özel durum oluşturulur. |
|REST API, bir varlık veya hesap düzeyi filtresi almaya çalışırken "filtreye REST API 'nin bu sürümü tarafından erişilemeyeceğini" belirten bir hata iletisiyle yanıt verir.|Filtre, filtreyi almayı denemek için kullanılandan daha yeni bir API sürümüyle oluşturulmuş veya değiştirilmiş. Bu durum, müşteri tarafından kullanılan kod veya araçlar tarafından iki API sürümü kullanılıyorsa meydana gelebilir.  Buradaki en iyi çözüm, kodu veya araçları, daha yeni veya iki API sürümünü kullanacak şekilde yükseltmek için kullanılır.|

## <a name="a-idrest_version_historyrest-api-version-history"></a><a id="rest_version_history"/>REST API sürümü geçmişi
Media Services REST API sürümü geçmişi hakkında daha fazla bilgi için [Azure Media Services REST API'si Başvurusu]bakın.

## <a name="september-2019"></a>Eylül 2019

### <a name="deprecation-of-media-processors"></a>Medya işlemcilerinin kullanımdan kaldırılması

*Azure Media Indexer* ve *Azure Media Indexer 2 Preview*kullanım dışı olarak duyuruluyoruz. [Azure Media Indexer](media-services-index-content.md) medya işlemcisi, 1 Ekim 2020 ' de kullanımdan kaldırılacaktır. [Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md) medya Işlemcileri 1 Ocak 2020 tarihinde kullanımdan kaldırılacaktır. [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) bu eski medya işlemcilerinin yerini alır.

Daha fazla bilgi için [Azure Media Indexer ve Azure Media Indexer 2 ' den Azure Media Services video Indexer geçiş](migrate-indexer-v1-v2.md)konusuna bakın.

## <a name="august-2019"></a>Ağustos 2019

### <a name="deprecation-of-media-processors"></a>Medya işlemcilerinin kullanımdan kaldırılması

30 Kasım 2019 ' de devre dışı bırakılmakta olan *Windows Azure Medya Kodlayıcısı* (WAME) ve *Azure Media Encoder* (AME) medya işlemcilerinin kullanımdan kaldırılması duyuruluyor.

Ayrıntılar için bkz. [WAME 'i Media Encoder Standard geçirin](https://go.microsoft.com/fwlink/?LinkId=2101334) ve [adı Media Encoder Standard geçirin](https://go.microsoft.com/fwlink/?LinkId=2101335).

## <a name="march-2019"></a>Mart 2019

Azure Media Services ortam hiper atlama önizleme özelliği kullanım dışıdır.

## <a name="december-2018"></a>Aralık 2018

Azure Media Services ortam hiper atlama önizleme özelliği yakında kullanımdan kaldırılacak. 19 Aralık 2018 ' den itibaren, Media Services medya Hyperlapi üzerinde değişiklik veya iyileştirmeler olmayacaktır. 29 Mart 2019 ' de kullanımdan kaldırılır ve artık kullanılamaz.

## <a name="october-2018"></a>Ekim 2018

### <a name="cmaf-support"></a>CMAF desteği

Apple HLS (iOS 11 +) için CMAF ve ' CBCS ' şifreleme desteği ve CMAF 'yi destekleyen MPEG-DASH çalarlar.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT küçük resim Sprites

Artık v2 API 'lerimizi kullanarak Web VTT küçük resim yükleri oluşturmak için Media Services kullanabilirsiniz. Daha fazla bilgi için bkz. [küçük resim Sprite oluşturma](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Temmuz 2018

En son hizmet sürümü sayesinde, bir Iş başarısız olduğunda hizmet tarafından döndürülen hata iletilerinde küçük biçimlendirme değişiklikleri vardır ve bu, iki veya daha fazla satıra nasıl bölündüğü ile ilgili olarak yapılır.

## <a name="may-2018"></a>Mayıs 2018 

12 Mayıs 2018 ' den itibaren Canlı Kanallar artık RTP/MPEG-2 aktarım akışı alma protokolünü desteklemezler. Lütfen RTP/MPEG-2 ' den RTMP veya parçalanmış MP4 (Kesintisiz Akış) alma protokolleriyle geçiş yapın.

## <a name="october-2017-release"></a>Ekim 2017 sürümü
> [!IMPORTANT] 
> Media Services Azure Access Control Service kimlik doğrulama anahtarları desteğini kullanımdan kaldırır. 22 Haziran 2018 ' de, Access Control Service anahtarlarını kullanarak kod aracılığıyla Media Services arka ucu ile kimlik doğrulaması yapamayacaktır. Kodunuzu [Azure AD tabanlı kimlik doğrulaması](media-services-use-aad-auth-to-access-ams-api.md)başına Azure Active Directory (Azure AD) kullanacak şekilde güncelleştirmeniz gerekir. Azure portal bu değişiklik hakkındaki uyarıları izleyin.

### <a name="updates-for-october-2017"></a>2017 Ekim güncelleştirmeleri
#### <a name="sdks"></a>SDK'ler
* .NET SDK, Azure AD kimlik doğrulamasını destekleyecek şekilde güncelleştirildi. Azure AD 'ye daha hızlı geçiş yapmak için, Access Control Service kimlik doğrulaması desteği Nuget.org üzerindeki en son .NET SDK 'sinden kaldırılmıştır. 
* JAVA SDK 'Sı, Azure AD kimlik doğrulamasını destekleyecek şekilde güncelleştirildi. Java SDK 'sına Azure AD kimlik doğrulaması desteği eklenmiştir. Java SDK 'sını Media Services ile kullanma hakkında daha fazla bilgi için bkz [. Java istemci SDK 'sını kullanmaya başlama Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Dosya tabanlı kodlama
* Artık içeriğinizi H. 265 yüksek verimlilik video kodlama (HEVC) video codec bileşenine kodlamak için Premium Encoder 'ı kullanabilirsiniz. H. 265 gibi diğer codec bileşenleri üzerinde H. seçerseniz fiyatlandırma etkisi yoktur. HEVC patent lisansları hakkında daha fazla bilgi için bkz. [çevrimiçi hizmet koşulları](https://azure.microsoft.com/support/legal/).
* İOS11 veya GoPro Hero 6 kullanılarak yakalanan video gibi H. 265 (HEVC) video codec bileşeniyle kodlanmış kaynak video için artık bu videoları kodlamak için Premium Encoder veya standart kodlayıcı kullanabilirsiniz. Patent lisansları hakkında daha fazla bilgi için bkz. [çevrimiçi hizmet koşulları](https://azure.microsoft.com/support/legal/).
* Birden çok dil sesi parçası içeren içerik için, dil değerlerinin karşılık gelen dosya biçimi belirtimine göre doğru şekilde etiketlenmesi gerekir (örneğin, ISO MP4). Daha sonra, akışı içeriğini kodlamak için standart kodlayıcıyı kullanabilirsiniz. Elde edilen akış bulucu, kullanılabilir ses dillerini listeler.
* Standart kodlayıcı artık yalnızca iki yeni ses sistem ön ayarlarını desteklemektedir, "AAC ses" ve "AAC Iyi kaliteli ses". Her ikisi de, sırasıyla 128 kbps ve 192 Kbps hızında bit hızında, stereo gelişmiş ses kodlaması (AAC) çıkışı üretir.
* Premium kodlayıcı artık, giriş olarak QuickTime/MOV dosya biçimlerini desteklemektedir. Video codec bileşeni, [Bu GitHub makalesinde listelenen Apple ProRes türlerinden](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats)biri olmalıdır. Ses, AAC veya Pulse Code modülasyonu (PCM) olmalıdır. Premium kodlayıcı, örnek olarak QuickTime/MOV dosyalarında Sarmalanan DVC/DVCPro videosunu desteklemez. Standart kodlayıcı bu video codec bileşenlerini destekler.
* Kodlayıcılarda aşağıdaki hata düzeltmeleri yapılmıştır:

    * Artık, bir giriş varlığı kullanarak işleri gönderebilirsiniz. Bu işler bittikten sonra, varlığı değiştirebilir (örneğin, varlık içindeki dosyaları ekleyebilir, silebilir veya yeniden adlandırabilir) ve ek işler gönderebilirsiniz.
    * Standart kodlayıcı tarafından üretilen JPEG küçük resimlerinin kalitesi geliştirilmiştir.
    * Standart kodlayıcı çok kısa süreli videolarda giriş meta verilerini ve küçük resim oluşturmayı daha iyi işler.
    * Standart kodlayıcıda kullanılan H. ve kod çözücü geliştirmeleri, bazı nadir yapıtları ortadan kaldırır. 

#### <a name="media-analytics"></a>Media Analytics
Azure Media Redactor genel kullanılabilirliği: Bu medya işlemcisi, seçilen kişilerin yüzlerini bulanıklaştırma ve genel güvenlik ve haber medya senaryolarında kullanılmak üzere ideal bir seçenektir. 

Bu yeni işlemciye genel bakış için, [Bu blog gönderisine](https://azure.microsoft.com/blog/azure-media-redactor/)bakın. Belgeler ve ayarlar hakkında daha fazla bilgi için, [Azure Media Analytics ile yüzeyleri redaksiyonu](media-services-face-redaction.md)bölümüne bakın.



## <a name="june-2017-release"></a>Haziran 2017 sürümü

Media Services artık [Azure AD tabanlı kimlik doğrulamasını](media-services-use-aad-auth-to-access-ams-api.md)desteklemektedir.

> [!IMPORTANT]
> Şu anda Media Services Access Control Service kimlik doğrulama modelini destekler. Access Control Service yetkilendirmesi 1 Haziran 2018 ' de kullanım dışı olacaktır. Azure AD kimlik doğrulaması modeline mümkün olan en kısa sürede geçiş yapmanız önerilir.

## <a name="march-2017-release"></a>Mart 2017 sürümü

Artık bir kodlama görevi oluştururken "uyarlamalı akış" önceden ayarlanmış dizesini belirterek [bit hızı bir el](media-services-autogen-bitrate-ladder-with-mes.md) ile hızlı bir şekilde oluşturmak Için standart kodlayıcıyı kullanabilirsiniz. Media Services ile akış için bir video kodlamak üzere "uyarlamalı akış" ön ayarını kullanın. Belirli senaryonuz için bir kodlama ön ayarını özelleştirmek üzere, [Bu önayarlarla](media-services-mes-presets-overview.md)başlayabilirsiniz.

Artık, [fMP4 öbekleri üreten bir kodlama görevi oluşturmak](media-services-generate-fmp4-chunks.md)için Media Encoder Standard veya Media Encoder Premium Workflow kullanabilirsiniz. 

## <a name="february-2017-release"></a>Şubat 2017 sürümü

1 Nisan 2017 ' den itibaren, hesabınızda 90 günden eski bir iş kaydı, ilişkili görev kayıtlarıyla birlikte otomatik olarak silinir. Kayıtların toplam sayısı maksimum kotanın altında olsa bile silme işlemi gerçekleşir. İş/görev bilgilerini arşivlemek için, [Media Services .NET SDK ile varlıkları ve ilgili varlıkları yönetme](media-services-dotnet-manage-entities.md)bölümünde açıklanan kodu kullanabilirsiniz.

## <a name="january-2017-release"></a>Ocak 2017 sürümü

Media Services, akış uç noktası, daha fazla dağıtım için doğrudan bir istemci oynatıcı uygulamasına veya bir içerik teslim ağına (CDN) içerik teslim edebilen bir akış hizmetini temsil eder. Media Services Ayrıca sorunsuz Azure Content Delivery Network tümleştirmesi de sağlar. Bir StreamingEndpoint hizmetinden giden akış canlı bir akış, isteğe bağlı bir video veya Media Services hesabınızda varlığınızın aşamalı bir şekilde indirilmesi olabilir. Her Media Services hesabı bir varsayılan akış uç noktası içerir. Hesap altında ek akış uç noktaları oluşturulabilir. 

İki akış uç noktası sürümü vardır, 1,0 ve 2,0. 10 Ocak 2017 tarihinden itibaren, yeni oluşturulan Media Services hesapların sürümü 2,0 varsayılan akış uç noktasını içerir. Bu hesaba eklediğiniz ek akış uç noktaları da sürüm 2,0 ' dir. Bu değişiklik mevcut hesapları etkilemez. Mevcut akış uç noktaları 1,0 sürümüdür ve sürüm 2,0 ' ye yükseltilebilir. Bu değişiklikle ilgili davranış, faturalandırma ve özellik değişiklikleri vardır. Daha fazla bilgi için bkz. [Akış uç noktalarına genel bakış](media-services-streaming-endpoints-overview.md).

2,15 sürümünden başlayarak, akış uç noktası varlığına aşağıdaki özellikleri ekledi Media Services:

* CdnProvider 
* Cdnprofıle
* FreeTrialEndTime 
* Streammingendpointversion 

Bu özellikler hakkında daha fazla bilgi için bkz. [Streamingendpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Aralık 2016 sürümü

 Artık Media Services, Hizmetleri için telemetri/ölçüm verilerine erişmek üzere kullanabilirsiniz. Canlı Kanal, akış uç noktası ve arşiv varlıkları için telemetri verilerini toplamak üzere Media Services geçerli sürümünü kullanabilirsiniz. Daha fazla bilgi için bkz. [telemetri Media Services](media-services-telemetry-overview.md).

## <a name="a-idjuly_changes16july-2016-release"></a>2016 Temmuz <a id="july_changes16"/>sürümü
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Bildirim dosyası güncelleştirmeleri (*. ISM) tarafından oluşturulan görevler
Bir kodlama görevi Media Encoder Standard veya Medya Kodlayıcısı Premium 'a gönderildiğinde, kodlama görevi çıkış varlığı içinde bir [akış bildirim dosyası](media-services-deliver-content-overview.md) (*. ISM) oluşturur. En son hizmet sürümü ile bu akış bildirimi dosyasının sözdizimi güncelleştirildi.

> [!NOTE]
> Akış bildirimi (. ISM) dosyasının sözdizimi iç kullanım için ayrılmıştır. Gelecek sürümlerde değiştirilebilir. Bu dosyanın içeriğini değiştirmeyin veya değiştirmeyin.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Yeni bir istemci bildirimi (*. ISMC) bir kodlama görevi bir veya daha fazla MP4 dosyasında çıkış yaparken çıkış varlığı dosyasında oluşturulur.
En son hizmet sürümünden itibaren, bir veya daha fazla MP4 dosyası üreten bir kodlama görevinin tamamlanmasından sonra, çıkış varlığı bir akış istemci bildirimi (*. ismc) dosyası da içerir. . İsmc dosyası dinamik akışın performansını artırmaya yardımcı olur. 

> [!NOTE]
> İstemci bildirimi (. ismc) dosyasının sözdizimi iç kullanım için ayrılmıştır. Gelecek sürümlerde değiştirilebilir. Bu dosyanın içeriğini değiştirmeyin veya değiştirmeyin.
> 
> 

Daha fazla bilgi için [Bu bloga](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/)bakın.

### <a name="known-issues"></a>Bilinen sorunlar
Bazı istemciler Kesintisiz Akış bildiriminde bir yineleme Etiketi sorunu üzerinden gelebilir. Daha fazla bilgi için [Bu bölüme](media-services-deliver-content-overview.md#known-issues)bakın.

## <a id="apr_changes16"></a>Nisan 2016 sürümü
### <a name="media-analytics"></a>Media Analytics
 Güçlü video zekası için Media Analytics Media Services. Daha fazla bilgi için bkz. [Media Services Analytics 'e genel bakış](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (Önizleme)
Artık, Apple FairPlay ile HTTP Canlı Akışı (HLS) içeriğinizi dinamik olarak şifrelemek için Media Services kullanabilirsiniz. Ayrıca, istemcilere FairPlay lisansları sunmak için Media Services lisans teslim hizmetini de kullanabilirsiniz. Daha fazla bilgi için bkz. Apple FairPlay ile korunan HLS içeriğinizi akışa almak için Azure Media Services kullanma. "

## <a id="feb_changes16"></a>Şubat 2016 sürümü
.NET için Media Services SDK 'nın en son sürümü (3.5.3), Google Widevine ile ilgili hata düzeltmesini içerir. Widevine ile şifrelenen birden çok varlık için AssetDeliveryPolicy yeniden kullanılması imkansız oldu. Bu hata düzeltmesinin bir parçası olarak, aşağıdaki özellik SDK 'ya eklenmiştir: Widevınebaselicensetanışytionurl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Ocak 2016 sürümü
Kodlayıcı adlarıyla karışıklığı azaltmak için kodlama ayrılmış birimleri yeniden adlandırıldı.

Temel, standart ve Premium kodlama ayrılmış birimleri sırasıyla S1, S2 ve S3 ayrılmış birimleri olarak yeniden adlandırıldı. Günümüzde temel Encoding 'e ayrılmış birimler kullanan müşteriler, Azure portal (ve faturanızda) etiket olarak S1 ' i görür. Standart ve Premium kullanan müşteriler sırasıyla S2 ve S3 etiketlerine bakın. 

## <a id="dec_changes_15"></a>Aralık 2015 sürümü

### <a name="media-encoder-deprecation-announcement"></a>Medya Kodlayıcısı kullanımdan kaldırma duyurusu

 Medya Kodlayıcısı, Media Encoder Standard sürümünden yaklaşık 12 ay sonra kullanımdan kaldırılacaktır.

### <a name="azure-sdk-for-php"></a>PHP için Azure SDK
Azure SDK ekibi, Media Services için güncelleştirmeler ve yeni özellikler içeren [php Için Azure SDK](https://github.com/Azure/azure-sdk-for-php) paketinin yeni bir sürümünü yayımladı. Özellikle, PHP için Media Services SDK artık en son [içerik koruma](media-services-content-protection-overview.md) özelliklerini desteklemektedir. Bu özellikler, belirteç kısıtlamalarına sahip ve olmayan AES ve DRM (PlayReady ve Widevine) ile dinamik şifrelemedir. Ayrıca [kodlama birimlerini](media-services-dotnet-encoding-units.md)ölçeklendirmeyi destekler.

Daha fazla bilgi için bkz.

* Aşağıdaki [kod örnekleri](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) hızlı bir şekilde başlamanıza yardımcı olur:
  * **vodworkflow_aes. php**: Bu php dosyası, aes-128 dinamik şifrelemesini ve anahtar teslim hizmetini nasıl kullanacağınızı gösterir. Bu, [AES-128 dinamik şifrelemesini ve anahtar teslim hizmetini kullanma](media-services-protect-with-aes128.md)bölümünde açıklanan .net örneğine dayalıdır.
  * **vodworkflow_aes. php**: Bu php dosyası, PlayReady dinamik şifrelemesini ve lisans teslimi hizmetini nasıl kullanacağınızı gösterir. Bu, [PlayReady ve/veya Widevine dinamik ortak şifrelemeyi kullanma](media-services-protect-with-playready-widevine.md)bölümünde açıklanan .net örneğine dayalıdır.
  * **scale_encoding_units. php**: Bu php dosyası, kodlama ayrılmış birimlerinin nasıl ölçeklenmesi gerektiğini gösterir.

## <a id="nov_changes_15"></a>Kasım 2015 sürümü
 Media Services artık bulutta Widevine lisans teslim hizmetini sunmaktadır. Daha fazla bilgi için [Bu bloga](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)bakın. Ayrıca, [Bu öğreticiye](media-services-protect-with-playready-widevine.md) ve [GitHub deposuna](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)bakın. 

Media Services tarafından sunulan Widevine lisans Teslim Hizmetleri önizleme aşamasındadır. Daha fazla bilgi için [Bu bloga](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)bakın.

## <a id="oct_changes_15"></a>Ekim 2015 sürümü
Media Services artık şu veri merkezlerinde canlı: Brezilya Güney, Hindistan Batı, Hindistan Güney ve Hindistan Orta. Artık [Media Service hesapları oluşturmak](media-services-portal-create-account.md) ve [Media Services belgeler Web sayfasında](https://azure.microsoft.com/documentation/services/media-services/)açıklanan çeşitli görevleri gerçekleştirmek için Azure Portal kullanabilirsiniz. Live Encoding bu veri merkezlerinde etkin değildir. Ayrıca, bu veri merkezlerinde Encoding 'e ayrılan birimlerin her türlü türü kullanılabilir değildir.

* Brezilya Güney: yalnızca standart ve temel kodlamaya ayrılan birimler kullanılabilir.
* Hindistan Batı, Hindistan Güney ve Hindistan Orta: yalnızca temel kodlamaya ayrılan birimler kullanılabilir.

## <a id="september_changes_15"></a>Eylül 2015 sürümü
Media Services artık Widevine modüler DRM teknolojisine sahip hem isteğe bağlı hem de canlı akışlara sahip videoyu koruma olanağı sunar. Widevine lisansları sunabilmenize yardımcı olması için aşağıdaki teslim hizmetleri iş ortaklarını kullanabilirsiniz:
* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Daha fazla bilgi için [Bu bloga](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)bakın.
  
AssetDeliveryConfiguration’ı Widevine kullanacak şekilde yapılandırmak için [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (3.5.1 sürümünden başlayarak) veya REST API'yi kullanabilirsiniz. 
* Media Services Apple ProRes videoları için destek eklendi. Artık Apple ProRes veya diğer codec bileşenleri kullanan QuickTime kaynak videoları dosyalarınızı karşıya yükleyebilirsiniz. Daha fazla bilgi için [Bu bloga](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)bakın.
* Artık Media Encoder Standard, alt kırpma ve Canlı Arşiv ayıklama yapmak için kullanabilirsiniz. Daha fazla bilgi için [Bu bloga](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)bakın.
* Aşağıdaki filtreleme güncelleştirmeleri yapıldı: 
  
  * Artık Apple HLS biçimini yalnızca bir ses filtresi ile kullanabilirsiniz. Bu güncelleştirmeyi, URL 'de (yalnızca ses = false) belirterek yalnızca bir ses parçasını kaldırmak için kullanabilirsiniz.
  * Varlıklarınız için filtre tanımladığınızda, artık tek bir URL 'de birden çok (en fazla üç) filtre birleştirebilirsiniz.
    
    Daha fazla bilgi için [Bu bloga](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)bakın.
* Media Services artık HLS sürüm 4 ' te ı-Frame 'leri destekliyor. G-kare desteği, ileri sarma ve geri sarma işlemlerini iyileştirir. Varsayılan olarak, tüm HLS sürüm 4 çıkışları ı-Frame şarkı listesini (EXT-X-I-FRAME-STREAM-INF) içerir.
Daha fazla bilgi için [Bu bloga](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)bakın.

## <a id="august_changes_15"></a>Ağustos 2015 sürümü
* Java sürüm 0.8.0 sürümü ve yeni örnekler için Media Services SDK artık kullanılabilir. Daha fazla bilgi için bkz.
    
* Azure Media Player çoklu ses akışı desteğiyle güncelleştirildi. Daha fazla bilgi için [Bu blog gönderisine](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)bakın.

## <a id="july_changes_15"></a>Temmuz 2015 sürümü
* Media Encoder Standard genel kullanılabilirliği duyuruldu. Daha fazla bilgi için [Bu blog gönderisine](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)bakın.
  
    Media Encoder Standard, [Bu bölümde](https://go.microsoft.com/fwlink/?LinkId=618336)açıklandığı gibi önayarları kullanır. 4K kodlama için bir ön ayar kullandığınızda, Premium ayrılmış birim türünü alın. Daha fazla bilgi için bkz. [Ölçek kodlaması](media-services-scale-media-processing-overview.md).
* Canlı gerçek zamanlı açıklamalı alt yazılar Media Services ve Media Player birlikte kullanıldı. Daha fazla bilgi için [Bu blog gönderisine](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)bakın.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK güncelleştirmeleri
Media Services .NET SDK artık sürüm 3.4.0.0. Aşağıdaki güncelleştirmeler yapılmıştır: 

* Canlı Arşiv için destek uygulandı. Canlı Arşiv içeren bir varlığı indiremiyoruz.
* Dinamik filtreler için destek uygulandı.
* İşlevler, kullanıcıların bir varlığı silerken bir depolama kapsayıcısını tutabilmeleri için uygulandı.
* Kanalların yeniden deneme ilkeleriyle ilgili hata düzeltmeleri yapıldı.
* Media Encoder Premium Workflow etkinleştirildi.

## <a id="june_changes_15"></a>Haziran 2015 sürümü
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK güncelleştirmeleri
Media Services .NET SDK artık sürüm 3.3.0.0. Aşağıdaki güncelleştirmeler yapılmıştır: 

* OpenID Connect bulma belirtimi için destek eklendi.
* Kimlik sağlayıcısı tarafında anahtar geçişi işlemek için destek eklendi.

Bir OpenID Connect bulgu belgesi (Azure AD, Google ve Salesforce) sunan bir kimlik sağlayıcısı kullanırsanız, OpenID Connect bulma belirtiminin içinden JSON Web belirteçleri (JWTs) doğrulaması için İmzalama anahtarları almak üzere Media Services söyleyebilirsiniz. 

Daha fazla bilgi için bkz. [Media Services 'DA JWT kimlik doğrulamasıyla çalışmak Için OpenID Connect Discovery spec 'TEN JSON Web anahtarlarını kullanma](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Mayıs 2015 sürümü
Aşağıdaki yeni özellikler duyurulmuştur:

* [Media Services ile canlı kodlama önizlemesi](media-services-manage-live-encoder-enabled-channels.md)
* [Dinamik bildirim](media-services-dynamic-manifest-overview.md)

## <a id="april_changes_15"></a>Nisan 2015 sürümü
### <a name="general-media-services-updates"></a>Genel Media Services güncelleştirmeleri
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) duyuruldu.
* Media Services REST 2,10 ile başlayarak, birincil ve ikincil alma URL 'Leriyle gerçek zamanlı mesajlaşma protokolünü (RTMP) almak üzere yapılandırılmış Kanallar oluşturulur. Daha fazla bilgi için bkz. [Channel ınest Configurations](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer güncelleştirildi.
* Ispanyolca Dil desteği eklendi.
* XML biçimi için yeni bir yapılandırma eklendi.

Daha fazla bilgi için [Bu bloga](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)bakın.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK güncelleştirmeleri
Media Services .NET SDK artık sürüm 3.2.0.0. Aşağıdaki güncelleştirmeler yapılmıştır:

* Son değişiklik: TokenRestrictionTemplate. Issuer ve TokenRestrictionTemplate. Audience bir dize türünde olacak şekilde değiştirildi.
* Özel yeniden deneme ilkeleri oluşturmayla ilgili güncelleştirmeler yapıldı.
* Dosya yükleme ve indirme ile ilgili hata düzeltmeleri yapıldı.
* MediaServicesCredentials sınıfı artık kimlik doğrulaması için birincil ve ikincil erişim denetimi uç noktalarını kabul eder.

## <a id="march_changes_15"></a>Mart 2015 sürümü
### <a name="general-media-services-updates"></a>Genel Media Services güncelleştirmeleri
* Media Services artık Content Delivery Network tümleştirme sağlar. Tümleştirmeyi desteklemek için, CdnEnabled özelliği StreamingEndpoint öğesine eklenmiştir. CdnEnabled, 2,9 sürümünden başlayarak REST API 'Leri ile kullanılabilir. Daha fazla bilgi için bkz. [Streamingendpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled, Version 3.1.0.2 ile başlayan .NET SDK ile birlikte kullanılabilir. Daha fazla bilgi için bkz. [Streamingendpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Media Encoder Premium Workflow duyuruldu. Daha fazla bilgi için bkz. [Azure Media Services Premium Encoding 'e giriş](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Şubat 2015 sürümü
### <a name="general-media-services-updates"></a>Genel Media Services güncelleştirmeleri
Media Services REST API artık 2,9 sürümüdür. Bu sürümden itibaren, akış uç noktalarıyla Content Delivery Network tümleştirmeyi etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [Streamingendpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Ocak 2015 sürümü
### <a name="general-media-services-updates"></a>Genel Media Services güncelleştirmeleri
Dinamik şifreleme ile içerik korumasının genel kullanılabilirliği duyurulmuştur. Daha fazla bilgi için, bkz. [DRM teknolojisinin genel kullanılabilirliği ile akış güvenliğini geliştirir Media Services](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK güncelleştirmeleri
Media Services .NET SDK artık sürüm 3.1.0.1.

Bu sürüm, varsayılan Microsoft. WindowsAzure. MediaServices. Client. Contentkeyauthorleştirme. TokenRestrictionTemplate oluşturucusunu eski olarak işaretledi. Yeni Oluşturucu, TokenType öğesini bir bağımsız değişken olarak alır.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Aralık 2014 sürümü
### <a name="general-media-services-updates"></a>Genel Media Services güncelleştirmeleri
* Media Indexer bazı güncelleştirmeler ve yeni özellikler eklendi. Daha fazla bilgi için bkz. [Azure Media Indexer Version 1.1.6.7 sürüm notları](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Kodlama ayrılmış birimlerini güncelleştirmek için kullanabileceğiniz yeni bir REST API eklenmiştir. Daha fazla bilgi için bkz. [EncodingReservedUnitType WITH Rest](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Anahtar teslim hizmeti için CORS desteği eklendi.
* Yetkilendirme İlkesi seçeneklerini sorgulamak için performans geliştirmeleri yapılmıştır.
* Çin veri merkezinde, [anahtar teslim URL 'si](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) artık müşteri (diğer veri merkezlerinde olduğu gibi) için de kullanılır.
* HLS otomatik hedef süresi eklendi. Canlı akış yaparken, HLS her zaman dinamik olarak paketlenmiştir. Varsayılan olarak Media Services, ana kare aralığına (keyFrameInterval) göre HLS segmenti paketleme oranını (FragmentsPerSegment) otomatik olarak hesaplar. Bu yöntem, canlı kodlayıcıdan alınan bir resim grubu (GOP) olarak da adlandırılır. Daha fazla bilgi için bkz. [Media Services canlı akış Ile çalışma](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK güncelleştirmeleri
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) artık sürüm 3.1.0.0. Aşağıdaki güncelleştirmeler yapılmıştır:

* .NET SDK bağımlılığı .NET 4,5 çerçevesine yükseltildi.
* Encoding 'e ayrılan birimleri güncelleştirmek için kullanabileceğiniz yeni bir API eklendi. Daha fazla bilgi için bkz. [ayrılmış birim türünü güncelleştirme ve .NET kullanarak kodlamaya ayrılan birimleri artırma](media-services-dotnet-encoding-units.md).
* Belirteç kimlik doğrulaması için JWT desteği eklendi. Daha fazla bilgi için, [Media Services ve dinamik şifrelemede JWT belirteci kimlik doğrulaması](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)konusuna bakın.
* PlayReady lisans şablonunda BeginDate ve ExpirationDate için göreli uzaklıklar eklendi.

## <a id="november_changes_14"></a>Kasım 2014 sürümü
* Artık, bir SSL bağlantısı üzerinden canlı Kesintisiz Akış (fMP4) içeriğini almak için Media Services kullanabilirsiniz. SSL üzerinden almak için alma URL 'sini HTTPS olarak güncelleştirdiğinizden emin olun. Şu anda, Media Services özel etki alanlarıyla SSL 'yi desteklemez. Canlı akış hakkında daha fazla bilgi için bkz. [Azure Media Services canlı akış Ile çalışma](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Şu anda, bir SSL bağlantısı üzerinden RTMP canlı akışını alamazsınız.
* Yalnızca içeriğinizi teslim ettiğiniz akış uç noktası 10 Eylül 2014 ' den sonra oluşturulduysa SSL üzerinden akış yapabilirsiniz. Akış URL 'larınız 10 Eylül 2014 ' den sonra oluşturulan akış uç noktalarına dayıyorsa, URL "streaming.mediaservices.windows.net" (yeni biçim) içerir. "Origin.mediaservices.windows.net" (eski biçim) içeren akış URL 'Leri SSL 'yi desteklemez. URL 'niz eski biçimindeyse ve SSL üzerinden akış yapmak istiyorsanız [Yeni bir akış uç noktası oluşturun](media-services-portal-manage-streaming-endpoints.md). İçeriğinizi SSL üzerinden akışa almak için yeni akış uç noktasına göre URL 'Leri kullanın.

### <a id="oct_sdk"></a>Media Services .NET SDK
.NET uzantıları için Media Services SDK artık sürüm 2.0.0.3.

.NET için Media Services SDK artık sürüm 3.0.0.8. Aşağıdaki güncelleştirmeler yapılmıştır:

* Yeniden düzenleme yeniden deneme ilke sınıflarında uygulandı.
* HTTP istek üst bilgilerine bir Kullanıcı Aracısı dizesi eklendi.
* NuGet geri yükleme derleme adımı eklendi.
* Senaryo testleri depodan x509 sertifikası kullanacak şekilde düzeltildi.
* Kanal ve akış son güncelleştirmesi için doğrulama ayarları eklendi.

### <a name="new-github-repository-to-host-media-services-samples"></a>Media Services örnekleri barındırmak için yeni GitHub deposu
Örnekler [Media Services örnekleri GitHub deposundadır](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Eylül 2014 sürümü
Media Services REST meta verileri artık 2,7 sürümüdür. En son REST güncelleştirmeleri hakkında daha fazla bilgi için [Media Services REST API başvurusuna](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)bakın.

.NET için Media Services SDK artık sürüm 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Son değişiklikler
* Kaynak, [Streammingendpoint]olarak yeniden adlandırıldı.
* MP4 dosyalarını kodlamak ve sonra yayımlamak için Azure portal kullandığınızda varsayılan davranışta değişiklik yapılmıştır.

### <a id="sept_14_GA_changes"></a>Genel kullanılabilirlik sürümünün parçası olan yeni özellikler/senaryolar
* Media Indexer medya işlemcisi sunuldu. Daha fazla bilgi için bkz. [Media Indexer Ile Dizin medya dosyaları](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Özel etki alanı (ana bilgisayar) adları eklemek için [Streammingendpoint] varlığını kullanabilirsiniz.
  
    Media Services akış uç noktası adı olarak özel bir etki alanı adı kullanmak için, akış uç noktanıza özel ana bilgisayar adları ekleyin. Özel ana bilgisayar adları eklemek için Media Services REST API 'Lerini veya .NET SDK 'sını kullanın.
  
    Aşağıdaki noktalar geçerlidir:
  
  * Özel etki alanı adının sahipliğinin olması gerekir.
  * Etki alanı adının sahipliğinin Media Services tarafından doğrulanması gerekir. Etki alanını doğrulamak için, DNS mediaservices-DNS-Zone ' ı doğrulamak üzere Mediaservicesaccountıd üst etki alanını eşleyen bir CName oluşturun.
  * Özel ana bilgisayar adını (örneğin, sports.contoso.com) Media Services StreamingEndpoint ana bilgisayar adı (örneğin, amstest.streaming.mediaservices.windows.net) ile eşleyen başka bir CName oluşturmanız gerekir.

    Daha fazla bilgi için, [Streamingendpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) makalesindeki customana bilgisayar adı özelliğine bakın.

### <a id="sept_14_preview_changes"></a>Genel Önizleme sürümünün parçası olan yeni özellikler/senaryolar
* Canlı akış önizlemesi. Daha fazla bilgi için bkz. [Media Services canlı akış Ile çalışma](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Anahtar teslim hizmeti. Daha fazla bilgi için bkz. [AES-128 dinamik şifrelemesini ve anahtar teslim hizmetini kullanma](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* AES dinamik şifreleme. Daha fazla bilgi için bkz. [AES-128 dinamik şifrelemesini ve anahtar teslim hizmetini kullanma](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* PlayReady lisans teslim hizmeti. 
* PlayReady dinamik şifrelemesi. 
* PlayReady lisans şablonu Media Services. Daha fazla bilgi için bkz. [Media Services PlayReady lisans şablonuna genel bakış].
* Akış depolama-şifrelenmiş varlıklar. Daha fazla bilgi için bkz. [akış depolama-şifrelenmiş içerik](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Ağustos 2014 sürümü
Bir varlığı kodlarken, kodlama işi tamamlandığında bir çıkış varlığı üretilir. Bu sürüm ne kadar Media Services Kodlayıcısı çıkış varlıkları hakkında meta veriler üretti. Bu sürümden itibaren kodlayıcı, giriş varlıkları hakkında meta veriler de üretir. Daha fazla bilgi için bkz. [giriş meta verileri] ve [Çıkış meta verileri].

## <a id="july_changes_14"></a>Temmuz 2014 sürümü
Azure Media Services Paketleyici ve Şifreleyici için aşağıdaki hata düzeltmeleri yapılmıştır:

* Canlı bir arşiv varlığı HLS 'ye aktarıldığında yalnızca ses yeniden oynatılır: Bu sorun düzeltildi ve artık ses ve video oynayabilir.
* Bir varlık HLS ve AES 128-bit zarf şifrelemesi olarak paketlendiğinde, paketlenmiş akışlar Android cihazlarda kayıttan çalmaz: Bu hata düzeltildi ve paketlenmiş akış, HLS 'yi destekleyen Android cihazlarda yeniden oynuyor.

## <a id="may_changes_14"></a>Mayıs 2014 sürümü
### <a id="may_14_changes"></a>Genel Media Services güncelleştirmeleri
Artık, HLS sürüm 3 için [dinamik paketleme] kullanabilirsiniz. HLS sürüm 3 ' ü göndermek için, Kaynak Bulucu yoluna şu biçimi ekleyin: *. ISM/manifest (format = M3U8-AAPL-v3). Daha fazla bilgi için [Bu foruma](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3)bakın.

Dinamik paketleme artık, PlayReady ile statik olarak şifrelenen Kesintisiz Akış tabanlı PlayReady ile şifrelenen HLS (sürüm 3 ve sürüm 4) gönderimi de desteklemektedir. PlayReady ile Kesintisiz Akış şifreleme hakkında daha fazla bilgi için bkz. [PlayReady ile kesintisiz akış koruma](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK güncelleştirmeleri
Media Services .NET SDK artık sürüm 3.0.0.5. Aşağıdaki güncelleştirmeler yapılmıştır:

* Medya varlıklarını karşıya yükleyip indirerek hız ve esnekliği daha iyi bir seçenektir.
* Yeniden deneme mantığındaki geliştirmeler ve geçici özel durum işleme: 
  
  * Geçici hata algılama ve yeniden deneme mantığı, sorgu, değişiklikleri kaydetme ve dosyaları karşıya yükleme veya indirme sırasında oluşan özel durumlar için geliştirilmiştir. 
  * Web özel durumları aldığınızda (örneğin, bir Access Control Service belirteç isteği), artık önemli hatalar daha hızlı başarısız olur.

Daha fazla bilgi için bkz. [.NET için MEDIA SERVICES SDK 'Da yeniden deneme mantığı].

## <a id="jan_feb_changes_14"></a>Ocak/Şubat 2014 yayınları
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 ve 3.0.0.3
3\.0.0.1 ve 3.0.0.2 içindeki değişiklikler şunları içerir:

* OrderBy deyimleriyle LINQ sorgularının kullanımıyla ilgili sorunlar düzeltildi.
* [GitHub] 'daki test çözümleri, birim tabanlı testlere ve senaryo tabanlı testlere bölündü.

Değişiklikler hakkında daha fazla bilgi için bkz. [.NET SDK 3.0.0.1 ve 3.0.0.2 yayınları Media Services](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

3\.0.0.3 sürümünde aşağıdaki değişiklikler yapılmıştır:

* Azure depolama bağımlılıkları, 3.0.3.0 sürümünü kullanacak şekilde yükseltildi.
* 3,0 için geriye dönük uyumluluk sorunu düzeltildi. *.* değiştirilebilir.

## <a id="december_changes_13"></a>Aralık 2013 sürümü
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3\.0. x. x yayınları, 2,4. x. x yayımları ile geriye dönük olarak uyumlu değildir.
> 
> 

Media Services SDK 'sının en son sürümü artık 3.0.0.0. NuGet 'den en son paketi indirebilir veya [GitHub]'dan bitleri edinebilirsiniz.

Media Services SDK sürümü 3.0.0.0 ile başlayarak [Azure AD Access Control Service](https://msdn.microsoft.com/library/hh147631.aspx) belirteçlerini yeniden kullanabilirsiniz. Daha fazla bilgi için bkz. [.NET için MEDIA SERVICES SDK ile Media Services bağlanma](https://msdn.microsoft.com/library/azure/jj129571.aspx)içindeki "belirteçleri yeniden Access Control Service kullanma" bölümüne bakın.

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK uzantıları 2.0.0.0
 Media Services .NET SDK uzantıları, kodunuzun basitleşmesini sağlayan ve Media Services geliştirmeyi kolaylaştıran bir genişletme yöntemleri ve yardımcı işlevleridir. [Media Services .NET SDK uzantılarından](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev)en son bitleri edinebilirsiniz.

## <a id="november_changes_13"></a>Kasım 2013 sürümü
### <a name="nov_13_donnet_changes"></a>.NET SDK değişikliklerini Media Services
Bu sürümden itibaren, .NET için Media Services SDK, Media Services REST API katmanında çağrılar yapıldığında oluşabilecek geçici hata hatalarını idare edebilir.

## <a id="august_changes_13"></a>Ağustos 2013 sürümü
### <a name="aug_13_powershell_changes"></a>Azure SDK araçları 'nda bulunan PowerShell cmdlet 'leri Media Services
Aşağıdaki Media Services PowerShell cmdlet 'leri artık [Azure SDK araçlarına](https://github.com/Azure/azure-sdk-tools)eklenmiştir:

* Get-Azudüzeltmelere Aservices 

    Örneğin, `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Örneğin, `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Örneğin, `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Örneğin, `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a id="june_changes_13"></a>Haziran 2013 sürümü
### <a name="june_13_general_changes"></a>Media Services değişiklikler
Bu bölümde bahsedilen aşağıdaki değişiklikler Haziran 2013 Media Services yayınlarına dahil edilen güncelleştirmelerdir:

* Birden çok depolama hesabını bir medya hizmeti hesabına bağlama özelliği. 
    * StorageAccount
    * Varlık. StorageAccountName ve varlık. StorageAccount
* Işi güncelleştirme özelliği. öncelik. 
* Bildirimle ilgili varlıklar ve Özellikler: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * İş
* Varlık. Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>.NET SDK değişikliklerini Media Services
Aşağıdaki değişiklikler Haziran 2013 Media Services SDK yayınlarına eklenmiştir. En son Media Services SDK, GitHub ' da kullanılabilir.

* Sürüm 2.3.0.0 ile başlayarak, Media Services SDK birden çok depolama hesabının bir Media Services hesabına bağlanmasını destekler. Aşağıdaki API 'Ler bu özelliği destekler:
  
    * Itoesgeaccount türü
    * Microsoft. WindowsAzure. MediaServices. Client. CloudMediaContext. StorageAccounts özelliği
    * StorageAccount özelliği
    * StorageAccountName özelliği
  
      Daha fazla bilgi için bkz. [birden çok depolama hesabı genelinde Media Services varlıkları yönetme](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Bildirimle ilgili API 'Ler. Sürüm 2.2.0.0 'den başlayarak, Azure kuyruk depolama bildirimlerini dinleyebilirsiniz. Daha fazla bilgi için bkz. [tanıtıcı Media Services iş bildirimleri](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft. WindowsAzure. MediaServices. Client. ıjob. Jobnotificationabonelikler özelliği
    * Microsoft. WindowsAzure. MediaServices. Client. ınocertificate Ationendpoint türü
    * Microsoft. WindowsAzure. MediaServices. Client. ıjobnotificationsubscription türü
    * Microsoft. WindowsAzure. MediaServices. Client. NotificationEndPointCollection türü
    * Microsoft. WindowsAzure. MediaServices. Client. NotificationEndPointType türü
* Depolama istemcisi SDK 2,0 bağımlılığı (Microsoft. WindowsAzure. StorageClient. dll)
* OData 5,5 bağımlılığı (Microsoft. Data. OData. dll)

## <a id="december_changes_12"></a>Aralık 2012 sürümü
### <a name="dec_12_dotnet_changes"></a>.NET SDK değişikliklerini Media Services
* IntelliSense: birçok tür için eksik IntelliSense belgeleri eklenmiştir.
* Microsoft. Yöntemler. TransientFaultHandling. Core: SDK 'nın hala bu derlemenin eski bir sürümüne bağımlılığı olduğu bir sorun düzeltildi. SDK artık bu derlemenin 5.1.1209.1 sürümüne başvurmuş.

Kasım 2012 SDK 'sında bulunan sorunlara yönelik düzeltmeler:

* Ivarlık. Konumlandırıcı. Count: Bu sayı artık, tüm Konumlandırıcıları silindikten sonra yeni ıvarlık arabirimlerinde doğru şekilde raporlanır.
* Iassetfile. ContentFileSize: Bu değer artık ıassetfile. upload (FilePath) tarafından karşıya yüklendikten sonra düzgün şekilde ayarlanır.
* Iassetfile. ContentFileSize: Bu özellik artık bir varlık dosyası oluşturduğunuzda ayarlanabilir. Daha önce salt okunurdur.
* Iassetfile. upload (FilePath): Bu zaman uyumlu yükleme yönteminin, varlığa birden çok dosya yüklendiğinde aşağıdaki hatayı aldığı bir sorun düzeltildi. Hata "sunucu, isteğin kimliğini doğrulayamadı. Yetkilendirme üstbilgisinin değerinin imza dahil doğru biçimlendirildiğinden emin olun. "
* Iassetfile. UploadAsync: dosyaları beş dosyaya aynı anda karşıya yüklemeyi sınırlı olan bir sorun düzeltildi.
* Iassetfile. UploadProgressChanged: Bu olay artık SDK tarafından sağlanıyor.
* Iassetfile. DownloadAsync (dize, BlobTransferClient, IBU, CancellationToken): Bu yöntem aşırı yüklemesi artık sağlanıyor.
* Iassetfile. DownloadAsync: dosyaların eşzamanlı olarak beş dosyaya indirilmesini sınırlı olan bir sorun düzeltildi.
* Iassetfile. Delete (): bir sorun düzeltildiğinde, ıassetdosyası için hiçbir dosya karşıya yüklenemediğinde Delete çağrısı bir özel durum oluşturabilir.
* İşler: bir iş şablonu kullanarak bir "PlayReady koruma görevi" ile "bir MP4 to sorunsuz akışlar görevi" zincirleme bir sorun düzeltildi.
* EncryptionUtils. GetCertificateFromStore (): Bu yöntem, sertifika yapılandırma sorunlarına göre sertifikayı bulmayla ilgili bir hata nedeniyle artık null başvuru özel durumu oluşturmaz.

## <a id="november_changes_12"></a>Kasım 2012 sürümü
Bu bölümde bahsedilen değişiklikler Kasım 2012 (sürüm 2.0.0.0) SDK 'sında yer alan güncelleştirmelerdir. Bu değişiklikler, Haziran 2012 Preview SDK sürümü için yazılan herhangi bir kodun değiştirilmesi veya yeniden yazılması gerekebilir.

* Varlıklar
  
    * Ivarlık. Create (assetName), *tek* varlık oluşturma işlevidir. Ivarlık. Create, artık dosya çağrısının bir parçası olarak dosyaları karşıya yüklememe. Karşıya yüklemek için ıassetfile kullanın.
    * Ivarlık. Publish yöntemi ve AssetState. Yayımla sabit listesi değeri, hizmetler SDK 'sından kaldırılmıştır. Bu değere dayanan kodların yeniden yazılması gerekir.
* Bilgis
  
    * Bu sınıf, ıassetfile tarafından kaldırıldı ve değiştirildi.
  
* Iassetfiles
  
    * Iassetdosyası, FileInfo yerini alır ve farklı bir davranışa sahiptir. Bunu kullanmak için, Media Services SDK veya depolama SDK 'sını kullanarak, ıassetfiles nesnesinin ardından bir dosya karşıya yüklemesi ile örneğini oluşturun. Aşağıdaki ıassetdosyası. karşıya yükleme aşırı yüklemeleri kullanılabilir:
  
        * Iassetfile. upload (filePath): Bu zaman uyumlu yöntem iş parçacığını engeller ve yalnızca tek bir dosyayı karşıya yüklediğinizde önerilir.
        * Iassetfile. UploadAsync (filePath, blobTransferClient, Locator, cancellationToken): Bu zaman uyumsuz yöntem, tercih edilen karşıya yükleme mekanizmasıdır. 
    
            Bilinen hata: iptal belirtecini kullanırsanız karşıya yükleme iptal edilir. Görevler birçok iptal durumuna sahip olabilir. Özel durumları doğru bir şekilde yakalayıp işlemeniz gerekir.
* Bulucuları
  
    * Kaynağa özgü sürümler kaldırılmıştır. SAS 'a özgü bağlam. Konumlandırıcı. CreateSasLocator (varlık, accessPolicy) kullanım dışı olarak işaretlenir veya genel kullanıma sunulacaktır. Güncelleştirilmiş davranış için "yeni işlevsellik" altındaki "Konumlandırıcı" bölümüne bakın.

## <a id="june_changes_12"></a>Haziran 2012 Preview sürümü
Aşağıdaki işlev SDK 'nın Kasım sürümünde yenidir:

* Varlıkları silme
  
    * Ivarlık, ıassetfile, ıIAccessPolicy ve ıditentkey nesneleri artık nesne düzeyinde silinir, yani koleksiyonda silme gerektirmek yerine ıobject. Delete (), yani cloudMediaContext. ObjCollection. Delete (Objınstance).
* Bulucuları
  
    * Konumlandırıcı artık CreateLocator yöntemi kullanılarak oluşturulmalıdır. Oluşturmak istediğiniz konum belirleyici türü için bir bağımsız değişken olarak, LocatorType. SAS veya LocatorType. OnDemandOrigin Enum değerlerini kullanmaları gerekir.
    * İçerik için kullanılabilir URI 'Leri edinmeyi kolaylaştırmak için, konum belirleyicilerinin yeni özellikler eklenmiştir. Konumlandırıcı 'nın bu şekilde yeniden tasarlanması, gelecekteki üçüncü taraf genişletilebilirliği için daha fazla esneklik sağlar ve medya istemci uygulamaları için kullanım kolaylığını artırır.
* Zaman uyumsuz yöntem desteği
  
    * Tüm yöntemlere zaman uyumsuz destek eklenmiştir.

## <a name="provide-feedback"></a>Geri bildirim sağlayın
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN Forumu]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API'si Başvurusu]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Giriş meta verileri]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Çıkış meta verileri]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[Streammingendpoint]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Media Services PlayReady lisans şablonuna genel bakış]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dinamik paketleme]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[.NET için Media Services SDK 'da yeniden deneme mantığı]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

