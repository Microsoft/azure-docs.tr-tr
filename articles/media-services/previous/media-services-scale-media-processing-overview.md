---
title: Medya Işlemeye genel bakış ölçeklendirme | Microsoft Docs
description: Bu konu, Azure Media Services ile medya Işlemeyi ölçeklendirmeye genel bakış.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 780d3ab5047bff321d0c554880ba2995bcf25524
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102904"
---
# <a name="scaling-media-processing-overview"></a>Ölçek medya Işlemeye genel bakış 
Bu sayfa, medya işlemenin nasıl ve neden ölçeklendirilmesine ilişkin bir genel bakış sunar. 

## <a name="overview"></a>Genel Bakış
Media Services hesabı bir Ayrılmış Birim Türüyle ilişkilendirilir ve bu da medya işleme görevlerinizin ne hızda işleneceğini belirler. Aşağıdaki ayrılmış birim türleri arasından seçim yapabilirsiniz: **S1**, **S2**veya **S3**. Örneğin, aynı kodlama işi **S2** ayrılmış birim türünü kullandığınızda **S1** türüne göre daha hızlı çalışır. Daha fazla bilgi için bkz. [ayrılmış birim türleri](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Ayrılmış birim türünü belirtmenin yanı sıra, hesabınızı ayrılmış birimlerle sağlamayı belirtebilirsiniz. Sağlanan ayrılmış birim sayısı, verili bir hesapta eşzamanlı olarak işlenebilecek medya görevlerinin sayısını belirler. Örneğin, hesabınızda ayrılmış beş birim varsa, işlenmek üzere görevler olduğu sürece beş medya görevi eşzamanlı olarak çalışır. Kalan görevler sırada beklecektir ve çalışan bir görev tamamlandığında sırasıyla işleme için alınır. Bir hesabın sağlanan ayrılmış birimi yoksa, görevler sırayla oluşturulur. Bu durumda, bir görev bitmeden ve sonraki bir sıradaki bir sonraki bekleme süresi sistemdeki kaynakların kullanılabilirliğine bağlıdır.

## <a name="choosing-between-different-reserved-unit-types"></a>Farklı ayrılmış birim türleri arasında seçim yapma
Aşağıdaki tablo, farklı kodlama hızları arasından seçim yaparken bir karar vermenize yardımcı olur. Ayrıca, kendi testlerinizi gerçekleştirmek üzere indirebileceğiniz [bir videoda](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) birkaç kıyaslama durumu sağlar:

|RU türü|Senaryo|[7 dakikalık 1080p videosu](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) için örnek sonuçlar|
|---|---|---|
| **S1**|Tek bit hızlı kodlama. <br/>SD veya altta bulunan dosyalar zamana duyarlı, düşük maliyetli değildir.|"H264 Single bit hızı SD 16X9" kullanılarak tek bit hızlı SD çözüm MP4 dosyasına kodlama 7 dakika sürer.|
| **S2**|Tek bit hızı ve çoklu bit hızı kodlaması.<br/>SD ve HD kodlaması için normal kullanım.|"H264 Single bit hızı 720p" önayar ile kodlama 6 dakikadan fazla sürer.<br/><br/>"H264 çoklu bit hızı 720p" ön ayarıyla kodlama 12 dakika sürer.|
| **S3**|Tek bit hızı ve çoklu bit hızı kodlaması.<br/>Tam HD ve 4K çözünürlük videoları. Zamana duyarlı, kodlamayı daha hızlı bir şekilde kapatma.|"H264 Single bit hızı 1080p" ön ayarı ile kodlama işlemi yaklaşık 3 dakika sürer.<br/><br/>"H264 çoklu bit hızı 1080p" ön ayarı ile kodlama yaklaşık 8 dakika sürer.|

## <a name="considerations"></a>Dikkat edilmesi gerekenler
> [!IMPORTANT]
> Bu bölümde açıklanan konuları gözden geçirin.  
> 
> 

* Media Services v3 veya Video Indexer tarafından tetiklenen ses analizi ve video analizi işleri için S3 birim türü kesinlikle önerilir.
* Paylaşılan havuz kullanılıyorsa, diğer bir deyişle, ayrılmış birimler olmadan, kodlama görevleriniz S1 RUs ile aynı performansa sahiptir. Ancak, görevlerinizin sıraya alınmış durumda harcayacağı zamana ve belirli bir zamanda yalnızca bir görevin çalıştığı zamana bir üst sınır yoktur.

## <a name="billing"></a>Faturalandırma

Medya ayrılmış birimlerinin hesabınızda sağlanacağı dakika sayısına göre ücretlendirilirsiniz. Bu, hesabınızda çalışan herhangi bir Iş olup olmadığından bağımsız olarak gerçekleşir. Ayrıntılı bir açıklaması için SSS bölümüne bakın. [Media Services fiyatlandırma](https://azure.microsoft.com/pricing/details/media-services/) sayfası.   

## <a name="quotas-and-limitations"></a>Kotalar ve sınırlamalar
Kotalar ve sınırlamalar ve bir destek biletini açma hakkında bilgi için bkz. [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Sonraki adım
Şu teknolojilerden biriyle ölçeklendirme medyası işleme görevine ulaşın: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Java SDK'ın en son sürümünü almak ve Java ile geliştirmeye başlamak için bkz. [Media Services için Java istemcisi SDK’sı ile çalışmaya başlama](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Media Services için en yeni PHP SDK'sını indirmek üzere, [Packagist deposunda](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) Microsoft/WindowAzure paketinin 0.5.7 sürümünü arayın.  

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

