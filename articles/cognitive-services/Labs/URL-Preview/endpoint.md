---
title: Proje URL 'SI önizleme uç noktası
titlesuffix: Azure Cognitive Services
description: URL önizleme uç noktasının Özeti.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 3ef5ebd4ec88deac8c49430f36956d3711c8c535
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706946"
---
# <a name="project-url-preview-endpoint"></a>Proje URL 'SI önizleme uç noktası

URL önizleme API 'SI bir uç nokta içerir.

## <a name="endpoint"></a>Uç Nokta
URL önizlemesi almak için aşağıdaki uç noktaya bir istek gönderin. Diğer belirtimlerin üst bilgilerini ve URL parametrelerini kullanın.

AL:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Sorgu parametreleri
|Ad|Value|Type|Gerekli|  
|----------|-----------|----------|--------------|  
|q|Önizlenecek URL|Dize |Evet|
|Güvenli Arama|Hatalı yetişkin içeriği veya korsan içerik 400 hata koduyla engelleniyor ve *isFamilyFriendly* bayrağı döndürülmüyor. <p>Yasal yetişkin içeriği için aşağıdaki davranış vardır. Durum kodu 200 döndürür ve *isFamilyFriendly* bayrağı false olarak ayarlanır.<ul><li>Güvenli Arama = katı: Başlık, açıklama, URL ve görüntü döndürülmeyecektir.</li><li>Güvenli Arama = orta; Açıklayıcı görüntüyü değil başlık, URL ve açıklama alın.</li><li>Güvenli Arama = kapalı; Tüm Yanıt nesneleri/öğeleri – başlık, URL, açıklama ve görüntü al.</li></ul> |Dize|Gerekli değildir. </br> Varsayılan olarak safeSearch = Strict olur.| 

## <a name="response-object"></a>Yanıt nesnesi

Yanıt, aşağıdaki örnekte gösterildiği gibi öznitelikleri olan http üst bilgilerini ve Web sayfası nesnesini içerir: `name`, `url`, `description` `isFamilyFriendly`, ve `primaryImageOfPage`.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Sonraki adımlar
- [C# hızlı başlangıcı](csharp.md)
- [Java hızlı başlangıcı](java-quickstart.md)
- [JavaScript hızlı başlangıcı](javascript.md)
- [Node hızlı başlangıcı](node-quickstart.md)
- [Python hızlı başlangıcı](python-quickstart.md)
