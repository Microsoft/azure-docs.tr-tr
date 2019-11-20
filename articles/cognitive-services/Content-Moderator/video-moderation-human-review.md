---
title: İnsan incelemesi ile video denetleme-Content Moderator
titleSuffix: Azure Cognitive Services
description: Uygun olmayan içeriğe sahip olan makine yardımlı video denetleme ve insan gözden geçirme araçlarını kullanın
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: pafarley
ms.openlocfilehash: a4e7b079367a4b4dec1d2b3d6c0afde1d8276766
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754165"
---
# <a name="video-moderation-with-human-review"></a>İnsan incelemesinin bulunduğu video denetlemesi

İşletmeniz için en iyi sonuçları elde etmek için Content Moderator makine yardımlı [video denetlemesi](video-moderation-api.md) ve [insan gözden geçirme aracı](Review-Tool-User-Guide/human-in-the-loop.md) 'nı, Yetişkin (açık) ve kcy (müstesel) içeriği için orta ve döküm dosyaları için kullanın.

## <a name="video-trained-classifier-preview"></a>Video eğitilen sınıflandırıcı (Önizleme)

Görüntü eğitilen modeller veya video eğitilen modeller ile makine destekli video sınıflandırması elde edilir. Görüntü eğitilen video sınıflandırıcılarından farklı olarak, Microsoft 'un yetişkinlere yönelik video sınıflandırıcıları videolar ile eğitilir. Bu yöntem, daha iyi eşleşme kalitesine neden olur.

## <a name="shot-detection"></a>Görüntü algılama

Sınıflandırma ayrıntılarının çıktıları sırasında, ek video zekası videoları çözümlemede daha fazla esneklik sağlar. Microsoft 'un video denetleme hizmeti, yalnızca çerçevelerin çıktısını almak yerine, görüntü düzeyinde bilgiler de sağlar. Artık videolarınızı, görüntü düzeyinde ve çerçeve düzeyinde analiz etme seçeneğiniz vardır.

## <a name="key-frame-detection"></a>Anahtar çerçeve algılama

Video denetleme hizmeti, düzenli aralıklarla çerçeve almak yerine yalnızca tamamlanmış olabilecek (iyi) çerçeveleri tanımlar ve verir. Özelliği, çerçeve düzeyinde yetişkinlere yönelik ve hızlı analizler için verimli çerçeve oluşturulmasına olanak sağlar.

Aşağıdaki ayıklama, olası görüntüler, anahtar çerçeveler ve yetişkin ve kcy puanlarını içeren kısmi bir yanıt gösterir:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>İnsan incelemeleri için görselleştirme

Daha fazla sayıda durumda, işletmeler video, çerçeve ve makine tarafından atanan etiketleri işlemek için bir insan incelemesi çözümüne ihtiyaç duyar. Videoları ve kareleri inceleyen insan BT yöneticileri, öngörülerin tamamen bir görünümünü alır, etiketleri değiştirebilir ve kararlarını gönderir.

![video inceleme aracı varsayılan görünümü](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Video düzeyinde inceleme için oynatıcı görünümü

Video düzeyinde ikili kararlar, olası yetişkinlere ve kcy çerçevelerini gösteren bir video oynatıcı görünümüyle mümkün hale getirilir. İnsan gözden geçirenler, sahneleri incelemek için çeşitli hız seçenekleriyle videoda gezinenler. Bunlar, etiketleri değiştirerek kararlarını doğrulamalar.

![video İnceleme araç oynatıcı görünümü](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Ayrıntılı incelemeler için çerçeveler görünümü

Çerçeve tabanlı bir görünümle çerçeve başına analiz için ayrıntılı bir video incelemesi yapılabilir. İnsan gözden geçirenler bir veya daha fazla kare inceleyerek, kararları almak için bir veya daha fazla kare ve geçiş etiketi seçer İsteğe bağlı bir sonraki adım, rahatsız edici çerçevelerin veya içeriğin bir kopyası olur.

![video İnceleme araç çerçeveleri görünümü](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Transkripti denetleme

Videolar genellikle, rahatsız edici bir konuşma için gereken bir sestir. Konuşmayı metne dönüştürmek ve Content Moderator İnceleme API 'sini kullanarak İnceleme aracında metin denetlemesi için dökümü göndermek üzere Azure Media Indexer hizmetini kullanın.

![video inceleme aracı dökümü görünümü](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Video denetleme hızlı](video-moderation-api.md)başlangıcı ile çalışmaya başlayın.
- Aracılı çıkışınızdan insan gözden geçirenler için [video İncelemeleri](video-reviews-quickstart-dotnet.md) oluşturmayı öğrenin.
- Video incelemelerinizi [video dökümü](video-transcript-reviews-quickstart-dotnet.md) ekleme hakkında inceleme.
- [Tüm video denetleme çözümünü](video-transcript-moderation-review-tutorial-dotnet.md)geliştirme hakkında ayrıntılı öğreticiye göz atın.