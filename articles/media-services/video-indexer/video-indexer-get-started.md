---
title: Video Indexer kaydolun ve ilk videonuzu karşıya yükleyin-Azure
titleSuffix: Azure Media Services
description: Video Indexer portalını kullanarak kaydolmak ve ilk videonuzu karşıya yüklemek hakkında bilgi edinin.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: 76862800e88b41a70449cff4a4ffad072c755272
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108049"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Hızlı başlangıç: nasıl kaydolun ve ilk videonuzu karşıya yükleyebilirsiniz

Bu başlangıç hızlı başlangıcı, Video Indexer Web sitesinde oturum açmayı ve ilk videonuzu karşıya yüklemeyi gösterir.

Video Indexer hesabınızı oluştururken ücretsiz bir deneme hesabı (belirli sayıda ücretsiz dizin oluşturma dakikası elde edersiniz) veya ücretli bir seçenek (kota sınırlaması olmaz) arasından seçim yapabilirsiniz. Ücretsiz deneme kullanıldığında Video Indexer, web sitesi kullanıcılarına 600 dakikaya kadar ve API kullanıcılarına ise 2400 dakikaya kadar ücretsiz dizin oluşturma olanağı sunar. Ücretli seçenek kullanıldığında [Azure aboneliğinize ve bir Azure Media Services hesabına bağlı](connect-to-azure.md) bir Video Indexer hesabı oluşturulur. Dizin oluşturma için ödeme yaparsınız. daha fazla bilgi için bkz. [Media Services fiyatlandırması](https://azure.microsoft.com/pricing/details/media-services/). 

## <a name="sign-up-for-video-indexer"></a>Video Indexer’a kaydolma

Video Indexer ile geliştirmeye başlamak için [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip kaydolun.

Video Indexer kullanmaya başladıktan sonra, tüm depolanmış verileriniz ve karşıya yüklenen içerikler, Microsoft tarafından yönetilen bir anahtarla geri kalanında şifrelenir.

> [!NOTE]
> [Planlı video Indexer Web sitesi kimlik doğrulama değişikliklerini](release-notes.md#planned-video-indexer-website-authenticatication-changes)gözden geçirin.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Video Indexer web sitesini kullanarak karşıya video yükleme

### <a name="supported-file-formats-for-video-indexer"></a>Video Indexer için desteklenen dosya biçimleri

Video Indexer ile kullanabileceğiniz dosya biçimlerinin bir listesi için bkz. [Giriş kapsayıcı/dosya biçimleri](../latest/encode-media-encoder-standard-formats-reference.md) makalesi.

### <a name="upload-a-video"></a>Karşıya video yükleme

1. [Video Indexer](https://www.videoindexer.ai/) web sitesinde oturum açın.
1. Karşıya video yüklemek için **Karşıya Yükle** düğme veya bağlantısına basın.

    > [!NOTE]
    > Videonun adı 80 karakterden büyük olmamalıdır.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Karşıya yükle":::
1. Videonuz karşıya yüklendikten sonra Video Indexer videoyu dizinlemeye ve analiz etmeye başlar. İlerleme durumunu görürsünüz. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Karşıya yükleme ilerleme durumu":::
1. Video Indexer çözümlendikten sonra, videonuza bağlantı içeren bir e-posta alacaksınız ve videonuzda nelerin bulunmuştur ilgili kısa bir açıklama alırsınız. Örneğin: insanlar, konuşulan ve yazılı sözcükler, konular ve adlandırılmış varlıklar.
1. Daha sonra videonuzu kitaplık listesinde bulabilir ve farklı işlemler yapabilirsiniz. Örneğin: arama, yeniden dizin, düzenleme.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Karşıya yükleme karşıya yüklendi":::

## <a name="supported-browsers"></a>Desteklenen tarayıcılar

Daha fazla bilgi için bkz. [Desteklenen tarayıcılar](video-indexer-overview.md#supported-browsers).

## <a name="see-also"></a>Ayrıca bkz.

Daha fazla ayrıntı için bkz. [videoları karşıya yükleme ve Dizin](upload-index-videos.md) .

Bir videoyu karşıya yükleyip dizinledikten sonra, videonun öngörülerini görmek için [video Indexer Web sitesini](video-indexer-view-edit.md) veya [video Indexer geliştirici portalını](video-indexer-use-apis.md) kullanmaya başlayabilirsiniz. 

[API 'Leri kullanmaya başlama](video-indexer-use-apis.md)

## <a name="next-steps"></a>Sonraki adımlar

Ayrıntılı giriş için lütfen [giriş laboratuvarımızı](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)ziyaret edin. 

Atölyenin sonunda, video ve ses içeriğinden ayıklanabilen bilgi türünü iyi anlayabilirsiniz, içerik zekası ile ilgili fırsatları belirlemek için daha hazırlanacaktır, Azure 'da video AI 'yi aralıklı olarak kullanabilirsiniz ve Video Indexer çeşitli senaryolar tanıtımına sahip olacaksınız.

