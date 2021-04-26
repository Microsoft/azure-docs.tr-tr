---
title: Video Indexer kavramları-Azure
titleSuffix: Azure Media Services Video Indexer
description: Bu makalede, Azure Media Services Video Indexer terimleri ve kavramları hakkında kısa bir genel bakış sunulmaktadır.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98634239"
---
# <a name="video-indexer-concepts"></a>Video Indexer kavramlar

Bu makalede, Azure Media Services Video Indexer terimleri ve kavramları hakkında kısa bir genel bakış sunulmaktadır.

## <a name="audiovideocombined-insights"></a>Ses/video/Birleşik Öngörüler

Videolarınızı Video Indexer yüklediğinizde, farklı AI modellerini çalıştırarak hem görselleri hem de sesi analiz edersiniz. Video Indexer, video, AI modelleri tarafından ayıklanan Öngörüler ile analiz edilir. Daha fazla bilgi için bkz. [genel bakış](video-indexer-overview.md).

## <a name="confidence-scores"></a>Güven puanları

Güvenirlik puanı, bir öngörüdeki güveni belirtir. 0,0 ile 1,0 arasında bir sayıdır. Puan arttıkça, yanıtın güveni daha yüksektir. Örneğin, 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>İçerik denetleme

Kullanıcılarınızın uygunsuz içerikten güvende kalmasını sağlamak ve yayımladığınız içeriğin kuruluşunuzun değerleriyle eşleştiğini doğrulamak için metin ve görsel içerik denetleme modellerini kullanın. Belirli videoları otomatik olarak bloke edebilir veya içeriğinizi içerik hakkında uyarır. Daha fazla bilgi için bkz. [Öngörüler: görsel ve metin içeriği denetleme](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Bloklar   

Bloklar, verileri daha kolay hale getirmek için tasarlanmıştır. Örneğin, blok ne zaman konuşmacının değiştiğine ya da uzun duraklamalar olduğuna bağlı olarak çözümlenebilir.  

## <a name="project-and-editor"></a>Proje ve düzenleyici

[Video Indexer](https://www.videoindexer.ai/) Web sitesi, videonuzun derin öngörülerinizi kullanarak şunları yapmanızı sağlar: doğru medya içeriğini bulur, ilgilendiğiniz bölümleri bulabilir ve sonuçları kullanarak tamamen yeni bir proje oluşturabilirsiniz. Oluşturulduktan sonra proje, Video Indexer oluşturulabilir ve indirilebilir ve kendi Düzenle uygulamalarınızda veya aşağı akış akışlarınızda kullanılabilir.

Bu özelliği yararlı bulabileceğiniz bazı senaryolar şunlardır: 

* Tanıtım için film vurguları oluşturma.
* Haber yayınlarına daha eski video klipleri kullanma.
* Sosyal medya için daha kısa içerik oluşturma.

Daha fazla bilgi için bkz. [proje oluşturmak için düzenleyiciyi kullanma](use-editor-create-project.md).

## <a name="keyframes"></a>Gezen

Video Indexer her bir görüntüsünü en iyi temsil eden çerçeveleri seçer. Ana kareler, Aesthetic Characteristics özelliklerine (örneğin, kontrast ve stablete) göre videonun tamamının seçtiği temsili çerçevelerdir. Daha fazla bilgi için bkz. [sahneler, görüntüleri ve ana kareler](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>zaman aralığı ve ayarlanan zaman aralığı karşılaştırması   

TimeRange, orijinal videodaki zaman aralığıdır. Ayarlantedtimerange, geçerli çalma listesine göre zaman aralığıdır. Farklı videoların farklı satırlarından bir çalma listesi oluşturbileceğinizden, 1 saatlik bir video alabilir ve yalnızca 1 satır kullanabilirsiniz, örneğin, 10:00-10:15. Bu durumda, 1 satır içeren bir çalma listenize sahip olursunuz, burada zaman aralığı 10:00-10:15, ancak Ayarlantedtimerange 00:00-00:15 ' dir. 

## <a name="widgets"></a>Pencere öğeleri

Video Indexer, uygulamalarınıza Pencere öğelerinin gömülmesini destekler. Daha fazla bilgi için bkz. [uygulamalarınıza video Indexer pencere öğeleri ekleme](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Özetlenen Öngörüler  

Özetlenen Öngörüler, verilerin toplu bir görünümünü içerir: yüzler, konular, lamalar. Örneğin, binlerce zaman aralığının her birini ve hangi yüzlerin nerede olduğunu kontrol etmek yerine, özetlenen Öngörüler tüm yüzleri ve her biri için, göründüğü zaman aralıklarını ve gösterilen sürenin yüzdesini içerir.  

## <a name="next-steps"></a>Sonraki adımlar

- [genel bakış](video-indexer-overview.md)
- [İçgörüler](video-indexer-output-json-v2.md)
