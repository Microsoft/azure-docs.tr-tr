---
title: Video Indexer videolar halinde tam dakika arama
titleSuffix: Azure Media Services
description: Video Indexer kullanarak videolarda tam dakika arama hakkında bilgi edinin.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030579"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Video Indexer videolar halinde tam dakika arama

Bu konu başlığı altında, videolarda tam olarak arama yapmak için Video Indexer Web sitesinin nasıl kullanılacağı gösterilmektedir.

1. [Video Indexer](https://www.videoindexer.ai/) Web sitesine gidin ve oturum açın.
1. Arama anahtar sözcüklerini belirtin ve arama, hesabınızın kitaplığındaki tüm videolar arasında gerçekleştirilir. 

    **Filtreler**' i seçerek aramanızı filtreleyebilirsiniz. Aşağıdaki örnekte, yalnızca ekrandaki metin (OCR) olarak görünen "Microsoft" araması yaptık.

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filtre, yalnızca metin":::
1. Sonucu görmek için **Ara** ' ya basın.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Video arama sonucu":::

    Sonuçlardan birini seçerseniz, Player sizi videoda tam olarak bir araya getirir.
1. Videoda **oynat** ' a tıklayarak veya orijinal arama sonuçlarınızdan birini seçerek videonun özetlenen öngörülerini görüntüleyin ve arayın. 

    **Öngörüleri** görüntüleyebilir, arayabilir, düzenleyebilirsiniz. Öngörülerden birini seçtiğinizde, Player sizi videoda tam olarak bir araya getirir.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Videonun öngörülerini görüntüleyin, arayın ve düzenleyin":::

    Videoyu Video Indexer pencere öğeleri aracılığıyla katıştırırsanız, uygulamanızda Player/Öngörüler görünümü ve eşitleme elde edebilirsiniz. Daha fazla bilgi için bkz. [uygulamanıza video Indexer pencere öğeleri ekleme](video-indexer-embed-widgets.md).
1. **Zaman çizelgesi** sekmesine tıklayarak dökümü görüntüleyebilir, arayabilir ve düzenleyebilirsiniz. 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Videonun dökümünü görüntüleyin, arayın ve düzenleyin":::

    Metni düzenlemek için sağ üst köşeden **Düzenle** ' yi seçin ve metni gerektiği gibi değiştirin. 

    Ayrıca, sağ üst köşedeki uygun seçeneği belirleyerek dökümü çevirebilir ve indirebilirsiniz. 

## <a name="embed-download-create-projects"></a>Ekleme, indirme, proje oluşturma

Videonuzu eklemek **</>Ekle** ' ye tıklayarak videonuzu ekleyebilirsiniz. Ayrıntılar için bkz. [uygulamanıza görsel pencere öğeleri ekleme](video-indexer-embed-widgets.md).

Videonun altında **İndir** ' i tıklatarak, video, döküm hakkında kaynak videosunu, dökümü indirebilirsiniz.

Belirli satırlardan ve kısa bir süre **içinde, düzenleyicide aç**' a tıklayarak bir klip oluşturabilirsiniz. Ardından videoyu düzenleyip projeyi kaydederek. Ayrıntılar için bkz. [videolarınızın derin öngörülerini kullanma](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Videonun projelerini ekleyin, indirin, oluşturun":::

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer REST API'si ile içerik işleme](video-indexer-use-apis.md)
