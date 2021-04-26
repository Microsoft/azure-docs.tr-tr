---
title: Ölçekli Video Indexer kullanırken göz önünde bulundurmanız gerekenler-Azure
titleSuffix: Azure Media Services
description: Bu konuda, ölçek Video Indexer kullanılırken dikkate alınması gereken noktalar açıklanmaktadır.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: f941d81df670f017d24a7c5011c55fcc4f082605
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531579"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Video Indexer ölçekli kullanırken göz önünde bulundurmanız gerekenler

Videoları indekslemek için Azure Media Services video Dizin Oluşturucu kullanılırken ve videoların Arşivi büyüirken ölçeklendirmeyi değerlendirin. 

Bu makalede aşağıdaki gibi sorular yanıtlanmaktadır:

* Dikkate almanız gereken teknolojik kısıtlamalar var mı?
* Bunu yapmanın akıllı ve verimli bir yolu var mı?
* İşlemde daha fazla para harcamasını engelleyebilir miyim?

Bu makalede, Video Indexer ölçeğinde nasıl kullanılacağına ilişkin altı en iyi yöntem sunulmaktadır.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Videoları karşıya yüklerken byte dizisi üzerinde bir URL kullanmayı düşünün

Video Indexer, URL 'den veya doğrudan dosyayı bir bayt dizisi olarak göndererek, ikincisi bazı kısıtlamalarla birlikte gelen videoları karşıya yükleme seçeneği sunar. Daha fazla bilgi için bkz. [hususlar ve sınırlamaları yükleme](upload-index-videos.md#uploading-considerations-and-limitations)

İlk olarak, dosya boyutu sınırlamaları vardır. Bayt dizisi dosyasının boyutu, URL kullanılırken 30 GB karşıya yükleme boyutu sınırlaması ile karşılaştırıldığında 2 GB ile sınırlıdır.

İkinci olarak, performansınızı etkileyebilecek bazı sorunları göz önünde bulundurun ve bu nedenle ölçeklendirebilirsiniz:

* Çoklu parça kullanarak dosya gönderme, ağınıza yüksek bağımlılık anlamına gelir, 
* hizmet güvenilirliği, 
* bilirlik 
* yükleme hızı, 
* World Wide Web 'de bir yerde kayıp paketler.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Video Indexer ölçekteki kullanımı ilk kez dikkate alınmaz":::

URL 'YI kullanarak videoları karşıya yüklediğinizde, yalnızca bir medya dosyasının konumunun yolunu sağlamanız ve geri kalanı Video Indexer gerekir ( `videoUrl` [karşıya yükleme videosu](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API 'sindeki alana bakın).

> [!TIP]
> `videoUrl`Karşıya yükleme VIDEOSU API 'sinin isteğe bağlı parametresini kullanın.

URL kullanarak videoları karşıya yükleme hakkında bir örnek görmek için [Bu örneğe](upload-index-videos.md#code-sample)göz atın. Ya da, verilerinizi [SAS URL 'sini](../../storage/common/storage-sas-overview.md)kullanarak video Indexer gönderebileceğiniz bir depolama hesabına alabilmeniz için hızlı ve güvenilir bir yol Için [AzCopy](../../storage/common/storage-use-azcopy-v10.md) kullanabilirsiniz.

## <a name="increase-media-reserved-units-if-needed"></a>Gerekirse medya ayrılmış birimlerini artır

Genellikle Video Indexer kullanmaya başladığınızda kavram kanıtı aşamasında, çok fazla bilgi işlem gücü gerekmez. Dizinlemeniz gereken daha büyük bir video arşivi olmaya başladığınızda ve işlemin kullanım örneğine uyan bir hızda olmasını istiyorsanız, Video Indexer kullanımınızı ölçeklendirmeniz gerekir. Bu nedenle, geçerli bilgi işlem gücü miktarı yeterince yoksa kullandığınız işlem kaynakları sayısını artırmayı düşünmelisiniz.

Azure Media Services, bilgi işlem gücü ve paralelleştirme düzeyini artırmak istediğinizde, medya [ayrılmış birimlerine](../latest/concept-media-reserved-units.md)(ru) dikkat etmeniz gerekir. Ru, medya işleme görevleriniz için parametreleri tespit eden işlem birimleridir. Ru sayısı, her hesapta eşzamanlı olarak işlenebilen medya görevlerinin sayısını etkiler ve bu tür işleme hızını belirler ve bir videonun dizinlemesi karmaşıksa, bir video birden fazla RU gerektirebilir. Ru 'niz meşgul olduğunda, başka bir kaynak kullanılabilir olana kadar yeni görevler sırada tutulur.

Verimli bir şekilde çalışmak ve sürenin boşta kalmasını engellemek için Video Indexer, daha az işlem gerektiğinde ABD aşağı açılan bir otomatik ölçek sistemi sağlar ve aceleniz saatleriniz (tüm ru 'larınızı tam olarak kullanabilmek için). Hesap ayarlarında [Otomatik ölçeklendirmeyi açıp](manage-account-connected-to-azure.md#autoscale-reserved-units) veya [Update-ücretli hesap-Azure-MEDIA-Services API 'sini](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)kullanarak bu işlevselliği etkinleştirebilirsiniz.

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Video Indexer ölçekli kullanımı için ikinci dikkat":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="AMS ayrılmış birimleri":::

Dizin oluşturma süresini ve düşük aktarım hızını en aza indirmek için S3 türünde 10 ru ile başlayacağız. Daha sonra daha fazla içerik veya daha fazla eşzamanlılık desteklemek üzere ölçeği artırdıysanız ve bunu yapmak için daha fazla kaynak gerekiyorsa, daha fazla RUs tahsisi istemek için destek sistemi (yalnızca ücretli hesaplar) [kullanarak bizimle iletişime geçin](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="respect-throttling"></a>Kısıtlama

Video Indexer, diziyle dizin oluşturma ile başa çıkmak için oluşturulmuştur ve bundan en iyi şekilde yararlanmak istediğinizde, sistemin yeteneklerini de bilmelisiniz ve tümleştirmenizi uygun şekilde tasarlayın. Bir video toplu işi için karşıya yükleme isteği göndermek istemezsiniz ancak bazı filmlerin karşıya yüklenemediği ve bir HTTP 429 yanıt kodu (çok fazla istek) aldığınızı fark edersiniz. Bu, dakikada çok sayıda [film sınırından](upload-index-videos.md#uploading-considerations-and-limitations)daha fazla istek gönderdiğiniz için meydana gelebilir. Video Indexer `retry-after` , http yanıtında bir üst bilgi ekler, üst bilgi bir sonraki yeniden denemeye ne zaman deneneceğini belirtir. Bir sonraki isteğinizi denemeden önce bunu dikkate aldığınızdan emin olun.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Tümleştirmenizi iyi tasarlama, azaltmayı azaltma":::

## <a name="use-callback-url"></a>Geri çağırma URL 'SI kullan

İsteğin durumunu karşıya yükleme isteğini gönderdiğiniz saniyeden sürekli olarak yoklamak yerine, bir [geri arama URL 'si](upload-index-videos.md#callbackurl)ekleyebilir ve video Indexer sizi güncelleştirmesini bekleyebilirsiniz. Karşıya yükleme isteğiniz üzerinde herhangi bir durum değişikliği olduğu anda, belirttiğiniz URL 'ye bir GÖNDERI bildirimi alırsınız.

[Karşıya yükleme VIDEOSU API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)'sinin parametrelerinden biri olarak bir geri çağırma URL 'si ekleyebilirsiniz. [GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/)deposunda kod örneklerine göz atın. 

Geri arama URL 'SI için, HTTP tarafından tetiklenebilecek ve aşağıdaki akışı uygulayan sunucusuz olay odaklı bir platform olan Azure Işlevleri de kullanabilirsiniz.

### <a name="callback-url-definition"></a>Geri çağırma URL tanımı

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Sizin için doğru dizin oluşturma parametrelerini kullanın

Video Indexer kullanımı ile ilgili kararlar almak için, gereksinimlerinize göre doğru parametrelerle en iyi şekilde yararlanın. Farklı parametreler tanımlayarak kullanım sürecinizi düşünün ve videolarınızın dizin oluşturma işlemini daha hızlı hale getirebilirsiniz.

Videonuzu karşıya yüklemeden ve dizin oluşturmadan önce bu kısa [belgeleri](upload-index-videos.md)okuyun, seçeneklerinizin ne olduğunu daha iyi bir fikir edinmek Için [ındexingönayar](upload-index-videos.md#indexingpreset) ve [streamingönayar](upload-index-videos.md#streamingpreset) ' nı kontrol edin.

Örneğin, videoyu izlemeyi planlamıyorsanız, video öngörülerini yalnızca ses içgörüler varsa akışa alın.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>En yüksek çözünürlükte değil, en iyi çözünürlükte Dizin

Videolarınızın dizinini oluşturmak için hangi video kalitesine ihtiyacınız olduğunu sordunuz musunuz? 

Çoğu durumda, dizin oluşturma performansı, HD (720P) videoları ve 4K videoları arasında neredeyse fark vermez. Sonuç olarak, aynı güvenilirlikle neredeyse aynı öngörüleri elde edersiniz. Karşıya yüklediğiniz filmin kalitesi arttıkça dosya boyutu daha yüksektir ve bu, videoyu karşıya yüklemek için gereken daha yüksek bilgi işlem gücü ve zamanına yol açar.

Örneğin, yüz algılama özelliği için daha yüksek bir çözünürlük, çok küçük ancak bağlamsal olarak önemli yüzlerine sahip olan senaryoya yardımcı olabilir. Ancak, bu, çalışma zamanında ikinci dereceden artış ve hatalı pozitif sonuç artışı riskini arttıracaktır.

Bu nedenle, kullanım durumu için doğru sonuçları almanızı ve öncelikle yerel olarak test etmenizi doğrulamanızı öneririz. Aynı videoyu 720P ve 4K 'da karşıya yükleyin ve aldığınız öngörüleri karşılaştırın.

## <a name="next-steps"></a>Sonraki adımlar

[API tarafından üretilen Azure Video Indexer çıkışını inceleyin](video-indexer-output-json-v2.md)