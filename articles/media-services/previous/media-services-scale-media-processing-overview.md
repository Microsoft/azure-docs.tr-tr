---
title: Medyaya ayrılan birimlere genel bakış | Microsoft Docs
description: Bu makalede, Azure Media Services ile medya Işlemeyi ölçeklendirmeye yönelik bir genel bakış sunulmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012726"
---
# <a name="media-reserved-units"></a>Medya ayrılmış birimleri

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services medya ayrılmış birimlerini (MRU) yöneterek Medya işlemeyi ölçeklendirmenizi sağlar. MRU, kodlama medyası için gereken ek bilgi işlem kapasitesi sağlar. MRUs sayısı, medya görevlerinizin işlenme hızını ve bir hesapta aynı anda kaç tane medya görevi işleneceğini saptar. Örneğin, hesabınızda beş MRU varsa ve işlenecek görevler varsa, beş medya görevi aynı anda çalıştırılabilir. Kalan görevler yukarı sıraya alınır ve çalışan bir görev tamamlandığında sırasıyla işlenmek üzere alınabilir. Sağladığınız her MRU bir kapasite rezervasyonuna neden olur, ancak size adanmış kaynak sağlamaz. Son derece yüksek talepler sırasında tüm MRUs işlemleri hemen işlemeye başlayamayabilir.

## <a name="choosing-between-different-reserved-unit-types"></a>Farklı ayrılmış birim türleri arasında seçim yapma

Aşağıdaki tablo, farklı kodlama hızları arasından seçim yaparken bir karar vermenize yardımcı olur.  Kullanılan MRU 'a bağlı olarak 7 dakikalık, 1080p videosunu kodlama süresini gösterir.

|RU türü|Senaryo|7 dakikalık 1080p videosu için örnek sonuçlar |
|---|---|---|
| **S1**|Tek bit hızlı kodlama. <br/>SD veya altta bulunan dosyalar zamana duyarlı, düşük maliyetli değildir.|"H264 Single bit hızı SD 16X9" kullanılarak tek bit hızlı SD çözüm MP4 dosyasına kodlama 7 dakika sürer.|
| **S2**|Tek bit hızı ve çoklu bit hızı kodlaması.<br/>SD ve HD kodlaması için normal kullanım.|"H264 Single bit hızı 720p" önayar ile kodlama 6 dakikadan fazla sürer.<br/><br/>"H264 çoklu bit hızı 720p" ön ayarıyla kodlama 12 dakika sürer.|
| **S3**|Tek bit hızı ve çoklu bit hızı kodlaması.<br/>Tam HD ve 4K çözünürlük videoları. Zamana duyarlı, kodlamayı daha hızlı bir şekilde kapatma.|"H264 Single bit hızı 1080p" ön ayarı ile kodlama işlemi yaklaşık 3 dakika sürer.<br/><br/>"H264 çoklu bit hızı 1080p" ön ayarı ile kodlama yaklaşık 8 dakika sürer.|

> [!NOTE]
> Hesabınız için MRU ' ı sağlamadıysanız medya görevleriniz S1 MRU 'su performansı ile işlenir ve görevler sırayla oluşturulur. Bir işlem kapasitesi ayrılmadığından, bir görev bitmeden ve bir sonraki sıradaki bir sonraki sefer, sistemdeki kaynakların kullanılabilirliğine bağlıdır.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

* Media Services v3 veya Video Indexer tarafından tetiklenen ses analizi ve video analizi işleri için on S3 birimli hesabın sağlanması kesinlikle önerilir. 10 ' dan fazla S3 MRU 'a ihtiyacınız varsa [Azure Portal](https://portal.azure.com/)kullanarak bir destek bileti açın.
* MRUs olmayan kodlama görevleri için, görevlerinizin sıraya alınmış durumda harcayacağı zamana üst sınır yoktur ve aynı anda yalnızca bir görev çalışır.

## <a name="billing"></a>Faturalandırma

Medya ayrılmış birimlerinin hesabınızda sağlanacağı dakika sayısına göre ücretlendirilirsiniz. Bu, hesabınızda çalışan herhangi bir Iş olup olmadığından bağımsız olarak gerçekleşir. Ayrıntılı bir açıklama için [Media Services fiyatlandırma](https://azure.microsoft.com/pricing/details/media-services/) sayfasının SSS bölümüne bakın.

## <a name="quotas-and-limitations"></a>Kotalar ve sınırlamalar

Kotalar ve sınırlamalar ve bir destek biletini açma hakkında bilgi için bkz. [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md).

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki teknolojilerden biriyle Medya işlemeyi ölçeklendirmeyi deneyin:

[.Net](media-services-dotnet-encoding-units.md) 
 [Portal](media-services-portal-scale-media-processing.md) 
 [Rest](/rest/api/media/operations/encodingreservedunittype) 
 [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples) 
 [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)