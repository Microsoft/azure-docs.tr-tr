---
title: Video Indexer-Azure ile konuşulan dili otomatik olarak tanımla
titleSuffix: Azure Media Services
description: Bu makalede, bir videodaki konuşulan dili otomatik olarak tanımlamak için Video Indexer dil tanımlama modelinin nasıl kullanıldığı açıklanmaktadır.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: ce3e488a6387f9a823d7c1b514b52af24944776b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839002"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Dil tanımlama modeliyle konuşulan dili otomatik olarak tanımla

Video Indexer, otomatik dil kimliğini (kapak) destekler, bu, sesli okunan dil içeriğini otomatik olarak tanımlama ve baskın tanımlanmış dilde kullanılacak medya dosyasını gönderme işlemidir. Şu anda KAPAĞı Ingilizce, Ispanyolca, Fransızca, Almanca, Italyanca, Çince (Basitleştirilmiş), Japonca, Rusça ve Portekizce (Brezilya) desteklemektedir. 

## <a name="choosing-auto-language-identification-on-indexing"></a>Dizin oluşturma sırasında otomatik dil tanımlamayı seçme

API 'yi kullanarak bir videoyu dizinleme veya [yeniden dizinlerken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) , `sourceLanguage` parametresindeki `auto detect` seçeneğini belirleyin.

Portalı kullanırken [video Indexer](https://www.videoindexer.ai/) giriş sayfasında **Hesap videolarınıza** gidin ve yeniden dizinlemek istediğiniz videonun adının üzerine gelin. Sağ alt köşede yeniden dizin düğmesine tıklayın. **Yeniden dizin videosu** iletişim kutusunda, **video kaynak dili** açılır listesinden *Otomatik Algıla* ' yı seçin.

![Otomatik Algıla](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Model çıkışı

Bu dilin güvenilirliği `> 0.6`, videoyu en olası dile göre Video Indexer. Dil güvenle tanımlanamıyorsa, konuşulan dilin Ingilizce olduğunu varsayar. 

Model baskın dil, `sourceLanguage` özniteliği (kök/videolar/Öngörüler) olarak Öngörüler JSON 'da bulunabilir. Karşılık gelen güven puanı `sourceLanguageConfidence` özniteliği altında da kullanılabilir.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Kılavuzlar ve sınırlamalar

* Desteklenen diller arasında Ingilizce, Ispanyolca, Fransızca, Almanca, Italyanca, Çince (Basitleştirilmiş), Japonca, Rusça ve Portekizce (Brezilya).
* Ses yukarıda desteklenen listeden farklı diller içeriyorsa sonuç beklenmedik olur.
* Video Indexer yüksek güvenilirliğe sahip dili tanımlayamıyor (`>0.6`), geri dönüş dili Ingilizce olur.
* Karma diller ses içeren dosya için geçerli destek yoktur. Ses karışık diller içeriyorsa, sonuç beklenmedik olur. 
* Düşük kaliteli ses, model sonuçlarını etkileyebilir.
* Model, sese en az bir dakikalık konuşma gerektirir.
* Model, Spontaneous bir konuşmayı tanımak için tasarlanmıştır (ses komutları, Malama, vb.).

## <a name="next-steps"></a>Sonraki adımlar

* [Genel Bakış](video-indexer-overview.md)
* [Çoklu dil içeriğini otomatik olarak tanımla ve yeniden tanımla](multi-language-identification-transcription.md)
