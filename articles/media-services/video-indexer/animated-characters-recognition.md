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
ms.date: 09/05/2019
ms.author: juliako
ms.openlocfilehash: 584d3fa787fbd44ad47d21c51ea67f301c04436d
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300328"
---
# <a name="animated-character-detection-preview"></a>Animasyonlu karakter algılama (Önizleme)

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

|İşlevi|Deneme Sürümü|Ücretli|
|---|---|---|
|Özel Görüntü İşleme hesabı|Video Indexer tarafından arka planda yönetilir. |Özel Görüntü İşleme hesabınız Video Indexer bağlı.|
|Animasyon modeli sayısı|Biriyle|Hesap başına en fazla 100 model (Özel Görüntü İşleme sınırlaması).|
|Modeli eğitme|Video Indexer yeni karakterler için mevcut karakterlerin ek örnekleri için model TRAIN.|Hesap sahibi, değişiklik yapmaya hazırlarsa modeli geçirir.|
|Özel Görüntü İşleme Gelişmiş Seçenekler|Özel Görüntü İşleme portalına erişim yok.|Modelleri Özel Görüntü İşleme portalında kendiniz yapabilirsiniz.|

## <a name="use-the-animated-character-detection-with-portal"></a>Portal ile animasyonlu karakter algılamayı kullanma 

Bu bölümde, animasyonlu karakter algılama modelini kullanmaya başlamak için gerçekleştirmeniz gereken adımlar açıklanmaktadır.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Özel Görüntü İşleme hesabınızı bağlama (yalnızca ücretli hesaplar)

Video Indexer ücretli bir hesabınız varsa, önce bir Özel Görüntü İşleme hesabı bağlamanız gerekir. Zaten bir Özel Görüntü İşleme hesabınız yoksa lütfen bir tane oluşturun. Daha fazla bilgi için bkz. [özel görüntü işleme](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

### <a name="create-an-animated-characters-model"></a>Animasyonlu karakterler modeli oluşturma

1. [Video Indexer](https://vi.microsoft.com/) web sitesine gidip oturum açın.
1. Sayfanın sağ üst köşesindeki içerik modeli özelleştirmesi düğmesine tıklayın.

    ![İçerik modeli özelleştirme](./media/animated-characters-recognition/content-model-customization.png)
1. Model özelleştirme bölümündeki **animasyonlu karakterler** sekmesine gidin.
1. **Model Ekle**' ye tıklayın.
1. Modelleyin ve adı kaydetmek için ENTER 'a tıklayın.

> [!NOTE]
> En iyi uygulama, her bir animasyon serisi için bir özel görüntü işleme modelidir. 

### <a name="index-a-video-with-an-animated-model"></a>Animasyonlu modelle video dizini oluşturma

1. Üstteki menüden **karşıya yükle** düğmesine tıklayın.
1. Karşıya yüklenecek bir video seçin (bir dosyadan veya URL 'den).
1. **Gelişmiş Seçenekler**' e tıklayın.
1. **Kişiler/animasyonlu karakterler** altında **animasyon modeller**' ı seçin.
1. Tek bir modeliniz varsa, otomatik olarak seçilir ve birden çok modelleriniz varsa, açılan menüden ilgili olanı seçebilirsiniz.
1. Karşıya yükle ' ye tıklayın.
1. Videonun dizini oluşturulduktan sonra, **Öngörüler** bölmesindeki **animasyonlu karakterler** bölümünde Algılanan karakterleri görürsünüz.

> [!NOTE] 
> Modeli etiketleyerek ve eğitimi yapmadan önce tüm animasyonlu karakterler "bilinmeyen #X" olarak adlandırılır. Modeli eğdikten sonra da tanınacaktır.

### <a name="customize-the-animated-characters-models"></a>Animasyonlu karakterler modellerini özelleştirme

1. Modeli etiketleyin ve eğitme.

    1. Algılanan karakteri, adını düzenleyerek etiketleyin. Bir karakter modele eğitilirken, bu modelle birlikte dizine alınmış bir sonraki video tanınacaktır. 
    1. Videonuzdaki bir animasyonlu karakteri etiketlemek için **Öngörüler** sekmesine gidin ve pencerenin sağ üst köşesindeki **Düzenle** düğmesine tıklayın.
    1. **Öngörüler** bölmesinde, algılanan animasyonlu karakterlerden herhangi birine tıklayın ve adlarını "bilinmeyen #X" (veya daha önce karaktere atanmış olan ad) olarak değiştirin.
    1. Yeni adı yazdıktan sonra, yeni adın yanındaki onay simgesine tıklayın. Bu, yeni adı modelde Video Indexer kaydeder.
    1. İstediğiniz tüm adları düzenledikten sonra modeli eğmeniz gerekir.

        Özelleştirme sayfasını açın ve **animasyon karakterleri** sekmesine tıklayın ve ardından modelinizi eğitebilmeniz Için **eğitme** düğmesine tıklayın.
         
        Ücretli bir hesabınız varsa, **Müşteri vizyonlarındaki modelleri Yönet** bağlantısını (aşağıda gösterildiği gibi) tıklayabilirsiniz. Daha sonra **özel görüntü işleme**modelin sayfasına iletilecektir.
 
        ![İçerik modeli özelleştirme](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Eğitim olduktan sonra, bu modelle Dizinlenecek veya yeniden Dizinlenecek tüm videolar eğitilen karakterleri tanır. 
    Özel Görüntü İşleme hesabına erişimi olan ücretli hesaplar, modelleri ve etiketli görüntüleri burada görebilir. [Özel görüntü işleme sınıflandırıcınızı iyileştirme](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)hakkında daha fazla bilgi edinin.

1. Animasyonlu bir karakteri silin.

    1. Video öngörülerinizi animasyonlu bir karakter silmek için **Öngörüler** sekmesine gidin ve pencerenin sağ üst köşesindeki **Düzenle** düğmesine tıklayın.
    1. Animasyonlu karakteri seçin ve ardından ad altındaki **Sil** düğmesine tıklayın.

    > [!NOTE]
    > Bu, bu videodan öngörüyü siler, ancak modeli etkilemez.

1. Bir modeli silin.

    1. Üstteki menüdeki **içerik modeli özelleştirmesi** düğmesine tıklayın ve **animasyonlu karakterler** sekmesine gidin.
    1. Silmek istediğiniz modelin sağ tarafındaki üç nokta simgesine ve ardından Sil düğmesine tıklayın.
    
    * Ücretli hesap: modelin bağlantısı Video Indexer kesilecek ve yeniden bağlanamayacaksınız.
    * Deneme hesabı: model gümrük Vision 'dan da silinecek. 
    
        > [!NOTE]
        > Deneme hesabında yalnızca bir modelleyebilirsiniz. Sildikten sonra, diğer modelleri eğitebilirsiniz.

## <a name="use-the-animated-character-detection-with-api"></a>API ile animasyonlu karakter algılamayı kullanma 

1. Özel Görüntü İşleme bir hesabı bağlayın.

    Video Indexer ücretli bir hesabınız varsa, önce bir Özel Görüntü İşleme hesabı bağlamanız gerekir. <br/>
    Zaten bir Özel Görüntü İşleme hesabınız yoksa lütfen bir tane oluşturun. Daha fazla bilgi için bkz. [özel görüntü işleme](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [API kullanarak özel görüntü işleme hesabınızı bağlayın](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Bir animasyonlu karakter modeli oluşturun.

    [Animasyon modeli oluşturma](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API 'sini kullanın.
1. Videoyu dizine koyun veya yeniden dizine koyun.

    [Yeniden dizin oluşturma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) API 'sini kullanın. 
1. Animasyonlu karakterler modellerini özelleştirin.

    [Eğitme animasyon modeli](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API 'sini kullanın.

### <a name="view-the-output"></a>Çıktıyı görüntüleme

Oluşturulan JSON dosyasındaki animasyonlu karakterleri görüntüleyin.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Sınırlamalar

* Şu anda, "animasyon kimliği" özelliği Doğu Asya bölgesinde desteklenmez.
* Videonun kalitesi zayıflarsa videoda küçük veya uzak görünen karakterler düzgün şekilde tanımlanmayabilir.
* Öneri, her bir animasyon karakter kümesi için (örneğin, bir animasyon serisi) bir model kullanmaktır.

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer’a genel bakış](video-indexer-overview.md)
