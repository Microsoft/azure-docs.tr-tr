---
title: Proje URL Önizlemesi nedir?
titlesuffix: Azure Cognitive Services
description: Project URL Önizlemesi'ne giriş.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 022fe002057f157d4d9fe6e6cad03fc79810888c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706920"
---
# <a name="what-is-project-url-preview"></a>Proje URL Önizlemesi nedir?
URL Önizlemesi uç noktası bir URL sorgu parametresini alır ve önizlemede görüntülenmek üzere hedef kaynak, kısa açıklama ve bağlantı içeren bir JSON yanıtı döndürür. Yanıt aynı zamanda URL'de yetişkinlere yönelik, korsan veya farklı yasa dışı içerik olup olmadığını belirten [isFamilyFriendly](url-preview-reference.md#query-parameters) bayrağını da içerir. 

URL önizleme sonuçlarını almak için bir GET isteği gönderin ve geçerli belirtece sahip *Ocp-Apim-Subscription-Key* üst bilgisini dahil edin:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
Yanıt: 
```
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

```
## <a name="scenarios"></a>Senaryolar 

URL Önizlemesi API'si, Web kaynakları için kısa açıklamaları destekler. Geliştiriciler bunu kullanarak zengin bir önizleme deneyimi sunabilir.  Kullanıcılar web sayfaları, haberler, bloglar ve forumlar gibi sayfaları paylaşabilir veya yer işareti ekleyebilir. Bu API içerik moderasyonu için de kullanılabilir.    

Uygulamalar URL Önizlemesini kullanarak önizlemesi yapılacak URL'ye atanmış olan bir sorgu ile uç noktasına Web isteği gönderebilir.  JSON yanıtı şu önizleme bilgilerini içerir: kaynağın adı, açıklaması, *familyFriendly* bayrağı ve temsili görüntüyle tam çevrimiçi kaynağa erişim sağlayan bağlantılar. 

## <a name="terms-of-use"></a>Kullanım koşulları
URL Önizlemesi Projesinden gelen verileri yalnızca sosyal medya, sohbet botu veya benzer durumlarda kullanıcı tarafından başlatılan URL paylaşma işlemlerinde kod parçacığı önizlemesi ve kaynak sitesine bağlantılı küçük resimler görüntülemek için kullanın. URL Önizlemesi Projesinden gelen verileri kopyalamayın, depolamayın veya önbelleğe almayın. Web sitesinden veya içerik sahiplerinden gelen önizlemeleri devre dışı bırakma isteklerini yerine getirin.

URL Önizleme API'sinden alınan veriler sizin tarafınızdan veya sizin adınıza hareket eden üçüncü şahıslar tarafından Microsoft harici bir hizmeti veya özelliği test etmek, geliştirmek, eğitmek, dağıtmak veya kullanıma sunmak üzere kullanılamaz, saklanamaz, depolanamaz, önbelleğe alınamaz, paylaşılamaz veya dağıtılamaz. 

## <a name="throttling-requests"></a>İstekleri azaltma

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Sonraki adımlar
- [C# hızlı başlangıcı](csharp.md)
- [Java hızlı başlangıcı](java-quickstart.md)
- [JavaScript hızlı başlangıcı](javascript.md)
- [Node hızlı başlangıcı](node-quickstart.md)
- [Python hızlı başlangıcı](python-quickstart.md)
