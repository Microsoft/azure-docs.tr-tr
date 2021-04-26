---
title: Video Indexer ile animasyonlu karakter algılama
titleSuffix: Azure Media Services
description: Bu konu, Video Indexer ile animasyonlu karakter algılamayı nasıl kullanacağınızı gösterir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854759"
---
# <a name="animated-character-detection-preview"></a>Animasyonlu karakter algılama (önizleme)

Azure Media Services Video Indexer, bilişsel [Hizmetler özel vizyonu](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)ile tümleştirme yoluyla animasyonlu içerikte karakterlerin algılanmasını, gruplanmasını ve tanınmasını destekler. Bu işlev hem Portal hem de API aracılığıyla kullanılabilir.

Belirli bir animasyon modeliyle animasyonlu bir videoyu karşıya yükledikten sonra Video Indexer ana kareleri ayıklar, bu çerçevelerdeki animasyonlu karakterleri algılar, benzer bir karakter gruplandırır ve en iyi örneği seçer. Daha sonra, gruplandırılmış karakterleri, üzerinde eğitilen modellere göre tanımlayan Özel Görüntü İşleme olarak gönderir. 

Modelinize eğitime başlamadan önce, karakterler ad gereksiz olarak algılanır. Ad ekler ve modeli eğiten Video Indexer karakterleri tanıyacak ve bunlara göre isimlendirilecektir.

## <a name="flow-diagram"></a>Akış diyagramı

Aşağıdaki diyagramda, animasyonlu karakter algılama işleminin akışı gösterilmektedir.

![Akış diyagramı](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Hesaplar

Video Indexer hesabınızın türüne bağlı olarak, farklı özellik kümeleri kullanılabilir. Hesabınızı Azure 'a bağlama hakkında daha fazla bilgi için bkz. [Azure 'a bağlı video Indexer hesabı oluşturma](connect-to-azure.md).

* Deneme hesabı: Video Indexer model oluşturmak ve Video Indexer hesabınıza bağlamak için bir iç Özel Görüntü İşleme hesabı kullanır. 
* Ücretli hesap: Özel Görüntü İşleme hesabınızı Video Indexer hesabınıza bağlayın (henüz bir hesabınız yoksa önce bir hesap oluşturmanız gerekir).

### <a name="trial-vs-paid"></a>Deneme ve ücretli

|İşlev|Deneme|Ücretli|
|---|---|---|
|Özel Görüntü İşleme hesabı|Video Indexer tarafından arka planda yönetilir. |Özel Görüntü İşleme hesabınız Video Indexer bağlı.|
|Animasyon modeli sayısı|Bir|Hesap başına en fazla 100 model (Özel Görüntü İşleme sınırlaması).|
|Modeli eğitme|Video Indexer yeni karakterler için mevcut karakterlerin ek örnekleri için model TRAIN.|Hesap sahibi, değişiklik yapmaya hazırlarsa modeli geçirir.|
|Özel Görüntü İşleme Gelişmiş Seçenekler|Özel Görüntü İşleme portalına erişim yok.|Modelleri Özel Görüntü İşleme portalında kendiniz yapabilirsiniz.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Portal ve API ile animasyonlu karakter algılamayı kullanma

Ayrıntılar için bkz. [Portal ve API ile animasyonlu karakter algılamayı kullanma](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Sınırlamalar

* Şu anda, "animasyon kimliği" özelliği East-Asia bölgede desteklenmez.
* Videonun kalitesi zayıflarsa videoda küçük veya uzak görünen karakterler düzgün şekilde tanımlanmayabilir.
* Öneri, her bir animasyon karakter kümesi için (örneğin, bir animasyon serisi) bir model kullanmaktır.

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer’a genel bakış](video-indexer-overview.md)