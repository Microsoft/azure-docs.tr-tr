---
title: Video Indexer ile çoklu dil içeriğini otomatik olarak belirleyip yeniden yapın
titleSuffix: Azure Media Services
description: Bu konuda, Video Indexer ile çoklu dil içeriğini otomatik olarak belirleme ve yeniden oluşturma işlemlerinin nasıl yapılacağı gösterilmiştir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968734"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Çoklu dil içeriğini otomatik olarak tanımla ve yeniden tanımla (Önizleme)

Video Indexer, çok dilli içerikte otomatik dil tanımlamayı ve dökümünü destekler. Bu işlem, bir araya getirilebilmesi için bir medya dosyasının her segmentini göndermek ve geri yüklemek üzere bir birleştirilmiş döküm ile birleştirmek için, ses durumundan farklı kesimlerde konuşulan dili otomatik olarak tanımlamayı içerir. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Portal ile dizin oluşturma sırasında çok dilli kimlik seçme

Videonuzu karşıya yüklerken ve dizin oluştururken **Çoklu dil algılama** seçeneğini belirleyebilirsiniz. Alternatif olarak, videonuzu yeniden dizinlerken **Çoklu dil algılamayı** seçebilirsiniz. Aşağıdaki adımlar, yeniden dizin oluşturmayı açıklamaktadır:

1. [Video Indexer](https://vi.microsoft.com/) web sitesine gidip oturum açın.
1. **Kitaplık** sayfasına gidin ve yeniden adlandırmak istediğiniz videonun adının üzerine gelin. 
1. Sağ alt köşede, **videoyu yeniden indeksle** düğmesine tıklayın. 
1. **Yeniden dizin videosu** iletişim kutusunda, **video kaynağı dili** açılan kutusundan **çok dilli algılama** ' yı seçin.

    * Bir videonun çoklu dil olarak dizini oluşturulduğunda, Öngörüler sayfasında bu seçenek bulunur ve Kullanıcı, hangi segmentin "konuşulan dilde" hangi kesime dahil edileceğini görüntülemesine olanak tanır.
    * Tüm dillere çeviri, çok dilli döküm betiğinden tamamen kullanılabilir.
    * Diğer tüm Öngörüler algılanan ana dilde görünür. bu dil, ses içinde en çok görülen dildir.
    * Player 'da kapalı açıklamalı altyazı, çok dilde de mevcuttur.

![Portal deneyimi](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>API ile dizin oluşturma sırasında çok dilli kimlik seçme

API kullanarak bir videoyu dizinleme veya [yeniden dizinleme](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) yaparken, `sourceLanguage` parametresindeki `multi-language detection` seçeneğini belirleyin.

### <a name="model-output"></a>Model çıkışı

Model, videoda algılanan dillerin tümünü tek bir listede alacaktır

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Ayrıca, döküm bölümündeki her bir örnek, oluşturulduğu dili içerir

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Kılavuzlar ve sınırlamalar

* Desteklenen dillerin kümesi: Ingilizce, Fransızca, Almanca, Ispanyolca.
* Desteklenen üç dile kadar çok dilli içerik desteği.
* Ses yukarıda desteklenen listeden farklı diller içeriyorsa sonuç beklenmedik olur.
* Her dil için 15 saniyelik en az kesim uzunluğu.
* Dil algılama boşluğu, ortalama olarak 3 saniyedir.
* Konuşmanın sürekli olması beklenmektedir. Diller arasındaki sık yapılan değişimler modeller performansını etkileyebilir.
* Yerel olmayan konuşmacılara yönelik konuşma, model performansını etkileyebilir (örneğin, hoparlörler yerel dil düzeyini kullanırken ve başka bir dile geçiş yaparken).
* Model, Spontaneous ses Acoustics (ses komutları, Malama, vb.) ile bir konuşma konuşmayı tanımak için tasarlanmıştır.
* Proje oluşturma ve düzenlemesi Şu anda çok dilli videolar için kullanılamaz.
* Özel dil modelleri, çoklu dil algılama kullanılırken kullanılamaz.
* Anahtar sözcük ekleme desteklenmiyor.
* Kapalı açıklamalı altyazı dosyalarını dışa aktarırken dil göstergesi görünmez.
* Güncelleştirme dökümü API 'SI birden çok dil dosyasını desteklemiyor.

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer’a genel bakış](video-indexer-overview.md)
