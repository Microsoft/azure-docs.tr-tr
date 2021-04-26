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
ms.openlocfilehash: 319bd408943c560622dc3208a6701417b8ca010c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532911"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Çok dilli içeriği otomatik olarak tanımlama ve transkripsiyonunu alma

Video Indexer, çok dilli içerikte otomatik dil tanımlamayı ve dökümünü destekler. Bu işlem, bir araya getirilebilmesi için bir medya dosyasının her segmentini göndermek ve geri yüklemek üzere bir birleştirilmiş döküm ile birleştirmek için, ses durumundan farklı kesimlerde konuşulan dili otomatik olarak tanımlamayı içerir. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Portal ile dizin oluşturma sırasında çok dilli kimlik seçme

Videonuzu karşıya yüklerken ve dizin oluştururken **Çoklu dil algılama** seçeneğini belirleyebilirsiniz. Alternatif olarak, videonuzu yeniden dizinlerken **Çoklu dil algılamayı**  seçebilirsiniz. Aşağıdaki adımlar, yeniden dizin oluşturmayı açıklamaktadır:

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

API 'yi kullanarak bir videoyu dizinleme veya [yeniden dizinleme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) yaparken, `multi-language detection` parametresindeki seçeneği belirleyin `sourceLanguage` .

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

## <a name="guidelines-and-limitations"></a>Yönergeler ve sınırlamalar

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
