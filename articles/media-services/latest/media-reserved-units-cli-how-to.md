---
title: Azure medya ayrılmış birimi - ölçeklendirmek için CLI kullanma | Microsoft Docs
description: Bu konuda, ölçeklendirme medya Azure Media Services ile işleme için CLI kullanma gösterilmektedir.
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
ms.custom: seodec18
ms.openlocfilehash: 13fa733417558ab8be9ff1e5a9f1e484fb40f445
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102940"
---
# <a name="scaling-media-processing"></a>Medya işlemeyi ölçeklendirme

Azure Media Services, medya ayrılmış birimi (MRU) yöneterek, hesabınızdaki medya işlemeyi ölçeklendirme sağlar. MRUs, medya işleme görevlerinizin işlenme hızını saptar. Aşağıdaki ayrılmış birim türleri arasından seçim yapabilirsiniz: **S1**, **S2**veya **S3**. Örneğin, aynı kodlama işi **S2** ayrılmış birim türünü kullandığınızda **S1** türüne göre daha hızlı çalışır. 

Ayrılmış birim türünü belirtmenin yanı sıra, hesabınızı ayrılmış birimlerle sağlamayı belirtebilirsiniz. Sağlanan ayrılmış birim sayısı, verili bir hesapta eşzamanlı olarak işlenebilecek medya görevlerinin sayısını belirler. Örneğin, hesabınızda ayrılmış beş birim varsa, işlenmek üzere görevler olduğu sürece beş medya görevi eşzamanlı olarak çalışır. Kalan görevler sırada beklecektir ve çalışan bir görev tamamlandığında sırasıyla işleme için alınır. Bir hesabın sağlanan ayrılmış birimi yoksa, görevler sırayla oluşturulur. Bu durumda, bir görev bitmeden ve sonraki bir sıradaki bir sonraki bekleme süresi sistemdeki kaynakların kullanılabilirliğine bağlıdır.

## <a name="choosing-between-different-reserved-unit-types"></a>Farklı ayrılmış birim türleri arasında seçim yapma

Aşağıdaki tablo, farklı kodlama hızları arasından seçim yaparken bir karar vermenize yardımcı olur. Ayrıca, kendi testlerinizi gerçekleştirmek üzere indirebileceğiniz [bir videoda](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) birkaç kıyaslama durumu sağlar:

|RU türü|Senaryo|[7 dakikalık 1080p videosu](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) için örnek sonuçlar|
|---|---|---|
| **S1**|Tek bit hızlı kodlama. <br/>SD veya altta bulunan dosyalar zamana duyarlı, düşük maliyetli değildir.|"H264 Single bit hızı SD 16X9" kullanılarak tek bit hızlı SD çözüm MP4 dosyasına kodlama 7 dakika sürer.|
| **S2**|Tek bit hızı ve çoklu bit hızı kodlaması.<br/>SD ve HD kodlaması için normal kullanım.|"H264 Single bit hızı 720p" önayar ile kodlama 6 dakikadan fazla sürer.<br/><br/>"H264 çoklu bit hızı 720p" ön ayarıyla kodlama 12 dakika sürer.|
| **S3**|Tek bit hızı ve çoklu bit hızı kodlaması.<br/>Tam HD ve 4K çözünürlük videoları. Zamana duyarlı, kodlamayı daha hızlı bir şekilde kapatma.|"H264 Single bit hızı 1080p" ön ayarı ile kodlama işlemi yaklaşık 3 dakika sürer.<br/><br/>"H264 çoklu bit hızı 1080p" ön ayarı ile kodlama yaklaşık 8 dakika sürer.|

## <a name="considerations"></a>Dikkat edilmesi gerekenler

* Media Services v3 veya Video Indexer tarafından tetiklenen ses analizi ve video analizi işleri için S3 birim türü kesinlikle önerilir.
* Paylaşılan havuz kullanılıyorsa, diğer bir deyişle, ayrılmış birimler olmadan, kodlama görevleriniz S1 RUs ile aynı performansa sahiptir. Ancak, görevlerinizin sıraya alınmış durumda harcayacağı zamana ve belirli bir zamanda yalnızca bir görevin çalıştığı zamana bir üst sınır yoktur.

Makalenin geri kalanında, MRU 'yi ölçeklendirmek için [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) 'nin nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
> Ses analizi ve Video analizi işleri, Media Services v3 tarafından tetiklenen veya Video Indexer için 10 S3 MRU hesabınızla sağlama önemle tavsiye edilir. 10'dan fazla S3 MRU gerekiyorsa, kullanarak bir destek bileti açın [Azure portalında](https://portal.azure.com/).
>
> Şu anda, diğer v3 kaynaklarını yönetmek için Azure portal kullanamazsınız. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) veya desteklenen [SDK'lardan](media-services-apis-overview.md#sdks) birini kullanın.

## <a name="prerequisites"></a>Önkoşullar 

[Bir Media Services hesabı oluşturma](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Ölçek medya ayrılmış birimleri CLI ile

`mru` komutunu çalıştırın.

Aşağıdaki [az ams hesabınızı mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) komut, medya ayrılmış birimi "amsaccount" hesabını kullanarak kümeleri **sayısı** ve **türü** parametreleri.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Faturalandırma

Medya ayrılmış birimlerinin hesabınızda sağlanacağı dakika sayısına göre ücretlendirilirsiniz. Bu, hesabınızda çalışan herhangi bir Iş olup olmadığından bağımsız olarak gerçekleşir. Ayrıntılı bir açıklaması için SSS bölümüne bakın. [Media Services fiyatlandırma](https://azure.microsoft.com/pricing/details/media-services/) sayfası.   

## <a name="next-step"></a>Sonraki adım

[Videoları analiz etme](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Ayrıca bkz.

* [Kotalar ve sınırlamalar](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
