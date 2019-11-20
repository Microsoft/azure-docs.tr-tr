---
title: Media Services dönüşümler ve Işler
titleSuffix: Azure Media Services
description: Azure Media Services ' de videolarınızı işlemeye yönelik kuralları tanımlamaya yönelik dönüşümler oluşturmayı öğrenin.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571230"
---
# <a name="transforms-and-jobs-in-media-services"></a>Media Services dönüşümler ve Işler

Bu konu, [dönüşümler](https://docs.microsoft.com/rest/api/media/transforms) ve [işlerle](https://docs.microsoft.com/rest/api/media/jobs) ilgili ayrıntıları verir ve bu varlıklar arasındaki ilişkiyi açıklar.

## <a name="overview"></a>Genel Bakış

### <a name="transformsjobs-workflow"></a>Dönüşümler/Işler iş akışı

Aşağıdaki diyagramda dönüşümler/işler iş akışı gösterilmektedir:

![Azure Media Services dönüşümler ve işler iş akışı](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Tipik iş akışı

1. Bir dönüşüm oluşturun.
2. Bu dönüşüm altında Işleri gönder.
3. Dönüşümleri listeleyin.
4. Daha sonra kullanmak üzere planlama yapmadıysanız, bir dönüşümü silin.

#### <a name="example"></a>Örnek

Tüm videolarınızın ilk çerçevesini küçük resim görüntüsü olarak ayıklamak istediğinizi varsayalım: gerçekleştireceğiniz adımlar şunlardır:

1. Tarifi veya videolarınızı işleme kuralını tanımlayın: "videonun küçük resim olarak ilk çerçevesini kullanın".
2. Her video için, hizmete şunu söylersiniz:
    1. Videonun nerede bulunacağı.
    2. Çıktı küçük resminin yazılacağı yer.

Bir **dönüşüm** , tarifi bir kez oluşturmanıza yardımcı olur (1. adım) ve bu tarifi kullanarak işleri gönderebilirsiniz (2. adım).

> [!NOTE]
> Tarih saat türünün **dönüştürme** ve **iş** özellikleri her zaman UTC biçimindedir.

## <a name="transforms"></a>Dönüştürmeler

Videoları kodlamaya veya çözümlemeye yönelik ortak görevleri yapılandırmak için **dönüşümler** kullanın. Her **dönüşüm** , video veya ses dosyalarınızı işlemeye yönelik bir tarif veya görev iş akışını açıklar. Tek bir dönüşüm, birden fazla kural uygulayabilir. Örneğin, bir dönüşüm, her videonun verilen bit hızında bir MP4 dosyasında kodlandığını ve videonun ilk çerçevesinden bir küçük resim görüntüsünün oluşturulmasını belirtebilir. Dönüşümizin dahil etmek istediğiniz her kural için bir TransformOutput girişi ekleyin. Giriş medya dosyalarının nasıl işleneceğini dönüştürmek için önceden ayarları kullanın.

### <a name="viewing-schema"></a>Şemayı görüntüleme

Media Services v3 'de, Önayarlar, API 'nin kendisinde kesin olarak belirlenmiş varlıklardır. Bu nesneler için "şema" tanımını [Open API belirtiminde (veya Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)bulabilirsiniz. [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.net sdk](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)veya diğer Media Services v3 SDK başvuru belgelerindeki önceden ayarlanmış tanımları ( **standardencoderönayar**gibi) da görüntüleyebilirsiniz.

### <a name="creating-transforms"></a>Dönüşümler oluşturma

REST, CLı veya yayımlanmış SDK 'Lardan herhangi birini kullanarak dönüşümler oluşturabilirsiniz. Media Services v3 API 'SI Azure Resource Manager tarafından dağıtılır, bu nedenle Media Services hesabınızda dönüşümler oluşturup dağıtmak için Kaynak Yöneticisi şablonlarını da kullanabilirsiniz. Rol tabanlı erişim denetimi, dönüşümlere erişimi kilitlemek için kullanılabilir.

### <a name="updating-transforms"></a>Dönüşümler güncelleştiriliyor

[Dönüşümünü](https://docs.microsoft.com/rest/api/media/transforms)güncelleştirmeniz gerekiyorsa **güncelleştirme** işlemini kullanın. Açıklamada değişiklik yapmak veya temel alınan Transformoutin öncelikleri için tasarlanmıştır. Tüm devam eden işler tamamlandığında bu tür güncelleştirmelerin yapılması önerilir. Tarifi yeniden yazmayı düşünüyorsanız, yeni bir dönüşüm oluşturmanız gerekir.

### <a name="transform-object-diagram"></a>Nesne diyagramını Dönüştür

Aşağıdaki diyagramda, türetme ilişkileri dahil olmak üzere **Transform** nesnesi ve başvurduğu nesneler gösterilmektedir. Gri oklar, Işin başvurduğu bir tür gösterir ve yeşil oklar sınıf türetme ilişkilerini gösterir.

Tam boyutunu görüntülemek için görüntüyü seçin.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>İşler

Bir **iş** , belirli bir giriş videosunun veya ses içeriğinin **dönüşümünü** uygulamak için Media Services yapılan gerçek istedir. Dönüşüm oluşturulduktan sonra, Media Services API 'Leri veya yayımlanmış SDK 'Lardan herhangi birini kullanarak işleri gönderebilirsiniz. **İş** , giriş videonun konumu ve çıktının konumu gibi bilgileri belirtir. Giriş videolarınızın konumunu kullanarak şunları yapabilirsiniz: HTTPS URL 'Leri, SAS URL 'Leri veya [varlıklar](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>HTTPS 'den iş girişi

İçeriğiniz bir URL aracılığıyla zaten erişilebilir olduğunda ve kaynak dosyayı Azure 'da depolamanız gerekmiyorsa (örneğin, S3 'ten içeri aktarma), [https 'den iş girişini](job-input-from-http-how-to.md) kullanın. Bu yöntem, Azure Blob depolama alanında içeriğiniz varsa ancak dosyanın bir varlık içinde olması gerekmiyorsa de uygundur. Şu anda, bu yöntem yalnızca giriş için tek bir dosyayı destekler.

### <a name="asset-as-job-input"></a>Iş girişi olarak varlık

Giriş içeriği zaten bir varlık içinde veya içerik yerel dosyada depolanıyorsa [iş girişi olarak varlığı](job-input-from-local-file-how-to.md) kullanın. Ayrıca, giriş varlığını akış veya indirme için yayımlamayı planlıyorsanız (örneğin, MP4 indirmek için yayımlamak istediğinizi, ancak metin veya yüz algılama için de konuşma yapmak istediğinizi varsayalım) iyi bir seçenektir. Bu yöntem çok dosya varlıklarını destekler (örneğin, yerel olarak kodlanmış MBR akış kümeleri).

### <a name="checking-job-progress"></a>Iş ilerleme durumu denetleniyor

İşlerin ilerleme durumu ve durumu, Event Grid ile olayları izleyerek elde edilebilir. Daha fazla bilgi için bkz. [EventGrid kullanarak olayları izleme](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Işler güncelleştiriliyor

[İş varlığındaki güncelleştirme](https://docs.microsoft.com/rest/api/media/jobs) işlemi, iş gönderildikten sonra *açıklamayı* ve *Öncelik* özelliklerini değiştirmek için kullanılabilir. *Priority* özelliğinde yapılan bir değişiklik, ancak iş hala sıraya alınmış durumdaysa geçerlidir. İş işleme başlamışsa veya tamamlandıysa, önceliğin değiştirilmesi etkisizdir.

### <a name="job-object-diagram"></a>İş nesnesi diyagramı

Aşağıdaki diyagramda, **iş** nesnesi ve başvurduğu nesneler, türetme ilişkileri dahil gösterilmektedir.

Resmi tam boyutlu görüntülemek için tıklayın.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Medya ayrılmış birimlerini yapılandırma

Media Services v3 veya Video Indexer tarafından tetiklenen ses analizi ve video analizi Işleri için, hesabınızı 10 S3 medya ayrılmış birimi (MRU) ile sağlamanız önemle önerilir. 10 ' dan fazla S3 MRU 'a ihtiyacınız varsa [Azure Portal](https://portal.azure.com/)kullanarak bir destek bileti açın.

Ayrıntılar için bkz. [CLI ile medya Işlemeyi ölçeklendirme](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="see-also"></a>Ayrıca bkz.

* [Hata kodları](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Media Services varlıkların filtrelenmesi, sıralanması, sayfalama](entities-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

- Geliştirmeye başlamadan önce, [Media Services v3 API 'leri Ile geliştirmeyi](media-services-apis-overview.md) Inceleyin (API 'lere erişme hakkında bilgi, adlandırma kuralları vb.)
- Şu öğreticilere göz atın:

    - [Öğretici: URL 'yi temel alarak uzak bir dosyayı kodlayın ve videoyu akışa sunun](stream-files-tutorial-with-rest.md)
    - [Öğretici: videoları karşıya yükleme, kodlama ve akışla](stream-files-tutorial-with-api.md)
    - [Öğretici: Media Services v3 ile videoları analiz etme](analyze-videos-tutorial-with-api.md)
