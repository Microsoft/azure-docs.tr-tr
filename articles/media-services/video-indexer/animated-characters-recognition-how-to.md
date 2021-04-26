---
title: Video Indexer ile animasyonlu karakter algılama
titleSuffix: Azure Media Services
description: Bu, Video Indexer ile animasyonlu karakter algılamayı nasıl kullanacağınızı gösterir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: e80699a043d4c18a1bc7ba75ce58c6972fae0fad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531548"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Portal ve API ile animasyonlu karakter algılamayı (Önizleme) kullanma 

Azure Media Services Video Indexer, animasyonlu içeriklerde karakterlerin algılanmasını, gruplandırılmasını ve tanınmasını destekler, bu işlevsellik Azure portal ve API aracılığıyla kullanılabilir. [Bu genel bakış](animated-characters-recognition.md) konusunu gözden geçirin.

Bu makalede, Azure portal ve Video Indexer API 'SI ile animasyonlu karakter algılamayı nasıl kullanabileceğiniz gösterilmektedir.

## <a name="use-the-animated-character-detection-with-portal"></a>Portal ile animasyonlu karakter algılamayı kullanma 

Deneme hesaplarında Özel Görüntü İşleme tümleştirme Video Indexer tarafından yönetilir, animasyonlu karakterler modelini oluşturmaya ve kullanmaya başlayabilirsiniz. Deneme hesabı kullanılıyorsa, aşağıdaki ("Özel Görüntü İşleme hesabınızı bağlama") bölümünü atlayabilirsiniz.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Özel Görüntü İşleme hesabınızı bağlama (yalnızca ücretli hesaplar)

Video Indexer ücretli bir hesabınız varsa, önce bir Özel Görüntü İşleme hesabı bağlamanız gerekir. Zaten bir Özel Görüntü İşleme hesabınız yoksa lütfen bir tane oluşturun. Daha fazla bilgi için bkz. [özel görüntü işleme](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Her iki hesabın de aynı bölgede olması gerekir. Özel Görüntü İşleme tümleştirme Şu anda Japonya bölgesinde desteklenmiyor.

Özel Görüntü İşleme hesabına erişimi olan ücretli hesaplar, modelleri ve etiketli görüntüleri burada görebilir.  [Özel görüntü işleme sınıflandırıcınızı iyileştirme](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)hakkında daha fazla bilgi edinin. 

Model eğitiminin, Özel Görüntü İşleme Web sitesi aracılığıyla değil, yalnızca Video Indexer ile yapılması gerektiğini unutmayın. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Özel Görüntü İşleme bir hesabı API ile bağlama 

Özel Görüntü İşleme hesabınızı Video Indexer bağlamak veya şu anda Video Indexer 'e bağlı olan Özel Görüntü İşleme hesabı değiştirmek için aşağıdaki adımları izleyin:

1. [Www.customvision.ai](https://www.customvision.ai) ve oturum açma bilgilerine gidin.
1. Eğitim ve tahmin kaynakları için anahtarları kopyalayın:

    > [!NOTE]
    > Tek bir eğitim ve diğeri tahmin için Özel Görüntü İşleme iki ayrı kaynağa sahip olmanız için ihtiyacınız olan tüm anahtarları sağlamak için.
1. Diğer bilgileri girin:

    * Uç Nokta 
    * Tahmin kaynak KIMLIĞI
1. [Video Indexer](https://vi.microsoft.com/)gidin ve oturum açın.
1. Sayfanın sağ üst köşesindeki soru işaretine tıklayın ve **API başvurusu**' nu seçin.
1. **Ürünler** sekmesi ' ne tıklayarak API Management abone olduğunuzdan emin olun. Bir API bağlantısı varsa, bir sonraki adıma devam edebilir, aksi takdirde abone olabilirsiniz. 
1. Geliştirici portalında, **tüm API başvurusuna** tıklayın ve **işlemlere** gidin.  
1. **Özel görüntü işleme hesabı Bağlan ' ı (Önizleme)** seçin ve **dene**' ye tıklayın.
1. Gerekli alanların yanı sıra erişim belirtecini de girin ve **Gönder**' e tıklayın. 

    Video Indexer erişim belirtecini alma hakkında daha fazla bilgi için [Geliştirici portalına](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)gidin ve [ilgili belgelere](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)bakın.  
1. Çağrı 200 Tamam yanıtı döndürtikten sonra hesabınız bağlanır.
1. Bağlantınızı doğrulamak için [video Indexer](https://vi.microsoft.com/)) portalına gidin:
1. Sağ üst köşedeki **içerik modeli özelleştirmesi** düğmesine tıklayın.
1. **Animasyonlu karakterler** sekmesine gidin.
1. Özel Görüntü İşleme modelleri Yönet ' e tıkladığınızda, yeni bağladığınız Özel Görüntü İşleme hesabına aktarılacaktır.

> [!NOTE]
> Şu anda yalnızca Video Indexer aracılığıyla oluşturulan modeller desteklenir. Özel Görüntü İşleme üzerinden oluşturulan modeller kullanılamayacak. Ayrıca, Özel Görüntü İşleme aracılığıyla yapılan değişiklikler istenmeden sonuçlara neden olabileceğinden, Video Indexer aracılığıyla oluşturulan modelleri yalnızca Video Indexer platformu aracılığıyla düzenlemek en iyi uygulamadır.

### <a name="create-an-animated-characters-model"></a>Animasyonlu karakterler modeli oluşturma

1. [Video Indexer](https://vi.microsoft.com/) web sitesine gidip oturum açın.
1. Hesabınızdaki bir modeli özelleştirmek için sayfanın solundaki **içerik modeli özelleştirmesi** düğmesini seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Video Indexer içerik modelini özelleştirme":::
1. Model özelleştirme bölümündeki **animasyonlu karakterler** sekmesine gidin.
1. **Model Ekle**' ye tıklayın.
1. Modelleyin ve adı kaydetmek için ENTER 'a tıklayın.

> [!NOTE]
> En iyi uygulama, her bir animasyon serisi için bir özel görüntü işleme modelidir. 

### <a name="index-a-video-with-an-animated-model"></a>Animasyonlu modelle video dizini oluşturma

İlk eğitim için en az iki videoyu karşıya yükleyin. İyi bir tanıma modeli beklemeden önce her birinin tercihen 15 dakikadan daha uzun olması gerekir. Daha kısa bir bölüm varsa, eğitimden önce en az 30 dakikalık video içeriğini karşıya yüklemeniz önerilir. Bu, farklı sahneler ve arka planlardan aynı karaktere ait olan grupları birleştirmenizi sağlar ve bu nedenle, Dizin oluşturduğunuz aşağıdaki bölümleri belirten karakteri algılamama olasılığını artırırsınız. Birden çok videoda (bölüm) bir modeli eğitme, bunları aynı animasyon modeliyle dizinlemek için gerekir. 

1. **Karşıya yükle** düğmesine tıklayın.
1. Karşıya yüklenecek bir video seçin (bir dosyadan veya URL 'den).
1. **Gelişmiş Seçenekler**' e tıklayın.
1. **Kişiler/animasyonlu karakterler** altında **animasyon modeller**' ı seçin.
1. Tek bir modeliniz varsa, otomatik olarak seçilir ve birden çok modelleriniz varsa, açılan menüden ilgili olanı seçebilirsiniz.
1. Karşıya yükle ' ye tıklayın.
1. Videonun dizini oluşturulduktan sonra, **Öngörüler** bölmesindeki **animasyonlu karakterler** bölümünde Algılanan karakterleri görürsünüz.

Modeli etiketleyerek ve eğitimi yapmadan önce tüm animasyonlu karakterler "bilinmeyen #X" olarak adlandırılır. Modeli eğdikten sonra da tanınacaktır.

### <a name="customize-the-animated-characters-models"></a>Animasyonlu karakterler modellerini özelleştirme

1. Video Indexer karakterleri adlandırın.

    1. Modelin karakter grubu oluşturulduktan sonra, bu grupları Özel Görüntü İşleme gözden geçirmeniz önerilir. 
    1. Videonuzdaki bir animasyonlu karakteri etiketlemek için **Öngörüler**   sekmesine gidin ve ****   pencerenin sağ üst köşesindeki Düzenle düğmesine tıklayın. 
    1.  **Öngörüler**   bölmesinde, algılanan animasyonlu karakterlerden herhangi birine tıklayın ve adlarını "bilinmeyen #X" olarak geçici bir ada (veya daha önce karaktere atanmış olan ada) değiştirin. 
    1. Yeni adı yazdıktan sonra, yeni adın yanındaki onay simgesine tıklayın. Bu, yeni adı modelde Video Indexer kaydeder. 
1. Yalnızca ücretli hesaplar: Özel Görüntü İşleme grupları gözden geçirin 

    > [!NOTE]
    > Özel Görüntü İşleme hesabına erişimi olan ücretli hesaplar, modelleri ve etiketli görüntüleri burada görebilir.  [Özel görüntü işleme sınıflandırıcınızı iyileştirme](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)hakkında daha fazla bilgi edinin. Modelin eğitiminin, Özel Görüntü İşleme Web sitesi aracılığıyla değil, yalnızca Video Indexer (Bu topıd 'de açıklandığı gibi) ile yapılması gerektiğine dikkat edin. 

    1. Video Indexer **özel modeller** sayfasına gidin ve **animasyonlu karakterler** sekmesini seçin. 
    1. Özel Görüntü İşleme içinde yönetmek için üzerinde çalıştığınız modelin Düzenle düğmesine tıklayın. 
    1. Her karakter grubunu gözden geçirin: 

        * Grupta ilişkisiz görüntüler varsa, bunları Özel Görüntü İşleme Web sitesinde silmeniz önerilir. 
        * Farklı bir karaktere ait olan görüntüler varsa, resme tıklayarak, sağ etiketi ekleyerek ve yanlış etiketi silerek bu belirli görüntülerde etiketi değiştirin. 
        * Grup doğru değilse, genellikle karakter olmayan görüntüleri veya birden çok karakterden oluşan resimleri içeriyorsa, Özel Görüntü İşleme Web sitesinde veya Video Indexer öngörülerinde silebilirsiniz. 
        * Gruplama algoritması bazen karakterlerinizi farklı gruplara bölecektir. Bu nedenle, aynı karaktere ait olan tüm grupların aynı ada (Video Indexer içgörüler) sahip olması önerilir, bu da bu grupların tümünün Özel Görüntü İşleme Web sitesinde açık olarak görünmesine neden olur. 
    1. Grup iyileştirildikten sonra, etiketlediğiniz ilk adın gruptaki karakteri yansıttığından emin olun. 
1. Modeli eğitme 

    1. İstediğiniz tüm adları düzenledikten sonra modeli eğmeniz gerekir. 
    1. Bir karakter modele eğitilirken, bu modelle birlikte dizine alınmış bir sonraki video tanınacaktır. 
    1. Özelleştirme sayfasını açın ve **animasyon karakterleri**   sekmesine tıklayın ve ardından modelinizi eğitebilmeniz için **eğitme** düğmesine tıklayın. Video ile bağlantıyı korumak için 
    
Dizin Oluşturucu ve model, Özel Görüntü İşleme Web sitesinde modeli eğmeyin (ücretli hesapların Özel Görüntü İşleme Web sitesine erişimi vardır), yalnızca Video Indexer. Eğitim olduktan sonra, bu modelle Dizinlenecek veya yeniden Dizinlenecek tüm videolar eğitilen karakterleri tanır. 

## <a name="delete-an-animated-character-and-the-model"></a>Bir animasyonlu karakteri ve modeli silme

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
    Zaten bir Özel Görüntü İşleme hesabınız yoksa lütfen bir tane oluşturun. Daha fazla bilgi için bkz. [özel görüntü işleme](../../cognitive-services/custom-vision-service/overview.md).

    [API kullanarak özel görüntü işleme hesabınızı bağlayın](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account).
1. Bir animasyonlu karakter modeli oluşturun.

    [Animasyon modeli oluşturma](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model) API 'sini kullanın.
1. Videoyu dizine koyun veya yeniden dizine koyun.

    [Yeniden dizin oluşturma](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API 'sini kullanın. 
1. Animasyonlu karakterler modellerini özelleştirin.

    [Eğitme animasyon modeli](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model) API 'sini kullanın.

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

* Şu anda, "animasyon kimliği" özelliği East-Asia bölgede desteklenmez.
* Videonun kalitesi zayıflarsa videoda küçük veya uzak görünen karakterler düzgün şekilde tanımlanmayabilir.
* Öneri, her bir animasyon karakter kümesi için (örneğin, bir animasyon serisi) bir model kullanmaktır.

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer’a genel bakış](video-indexer-overview.md)