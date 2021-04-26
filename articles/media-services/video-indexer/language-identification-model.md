---
title: Konuşulan dilleri otomatik olarak tanımlamak için Video Indexer kullanın-Azure
titleSuffix: Azure Media Services
description: Bu makalede, bir videodaki konuşulan dili otomatik olarak tanımlamak için Video Indexer dil tanımlama modelinin nasıl kullanıldığı açıklanmaktadır.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 40f2e146956919e154f59d90b56a1b03379abbb2
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600646"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Dil tanımlama modeliyle konuşulan dili otomatik olarak belirleme

Video Indexer, otomatik dil kimliğini (kapak) destekler, bu, sesli okunan dil içeriğini otomatik olarak tanımlama ve baskın tanımlanmış dilde kullanılacak medya dosyasını gönderme işlemidir. 

Şu anda KAPAĞı şunları destekler: Ingilizce, Ispanyolca, Fransızca, Almanca, Italyanca, Mandarin Çince, Japonca, Rusça ve Portekizce (Brezilya). 

Aşağıdaki [kılavuz ve sınırlamalar](#guidelines-and-limitations) bölümünü gözden geçirdiğinizden emin olun.

## <a name="choosing-auto-language-identification-on-indexing"></a>Dizin oluşturma sırasında otomatik dil tanımlamayı seçme

API 'yi kullanarak bir videoyu dizinleme veya [yeniden dizinlerken](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) , `auto detect` parametresindeki seçeneği belirleyin `sourceLanguage` .

Portalı kullanırken [video Indexer](https://www.videoindexer.ai/) giriş sayfasında **Hesap videolarınıza** gidin ve yeniden dizinlemek istediğiniz videonun adının üzerine gelin. Sağ alt köşede yeniden dizin düğmesine tıklayın. **Yeniden dizin videosu** iletişim kutusunda, **video kaynak dili** açılır listesinden *Otomatik Algıla* ' yı seçin.

![Otomatik Algıla](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Model çıkışı

Video Indexer söz konusu dilin güvenilirliği ise, videoyu en olası dile göre `> 0.6` . Dil güvenle tanımlanamıyorsa, konuşulan dilin Ingilizce olduğunu varsayar. 

Model baskın dil, Öngörüler JSON 'da öznitelik olarak kullanılabilir `sourceLanguage` (kök/videolar/Öngörüler altında). Karşılık gelen bir güven puanı özniteliği altında da kullanılabilir `sourceLanguageConfidence` .

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

## <a name="guidelines-and-limitations"></a>Yönergeler ve sınırlamalar

* Otomatik dil tanımlama (kapak) aşağıdaki dilleri destekler: 

    İngilizce, Ispanyolca, Fransızca, Almanca, Italyanca, Mandarin, Japonca, Rusça ve Portekizce (Brezilya).
* Video Indexer, Arapça (modern standart ve Levantine), Hintçe ve Korece 'yi desteklese de bu diller KAPAPLANDA desteklenmez.
* Ses yukarıda desteklenen listeden farklı diller içeriyorsa sonuç beklenmedik olur.
* Video Indexer, yüksek oranda güvenilirlikli () dili tanımlayamıyor `>0.6` , geri dönüş dili İngilizce olur.
* Karma diller ses içeren dosya için geçerli destek yoktur. Ses karışık diller içeriyorsa, sonuç beklenmedik olur. 
* Düşük kaliteli ses, model sonuçlarını etkileyebilir.
* Model, sese en az bir dakikalık konuşma gerektirir.
* Model, Spontaneous bir konuşmayı tanımak için tasarlanmıştır (ses komutları, Malama, vb.).

## <a name="next-steps"></a>Sonraki adımlar

* [Genel Bakış](video-indexer-overview.md)
* [Çok dilli içeriği otomatik olarak tanımlama ve transkripsiyonunu alma](multi-language-identification-transcription.md)
