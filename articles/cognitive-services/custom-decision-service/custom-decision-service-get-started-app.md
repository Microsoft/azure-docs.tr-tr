---
title: Uygulamadan API çağırma-Özel Karar Alma Hizmeti
titlesuffix: Azure Cognitive Services
description: Özel Karar Alma Hizmeti API 'Lerini bir smartphone uygulamasından çağırma.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 08fbc1716d402c83bc2c33be82cba143c1737a55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707248"
---
# <a name="call-api-from-an-app"></a>Uygulamadan API çağrısı yapma

Bir smartphone uygulamasından Azure Özel Karar Alma Hizmeti API 'Lerine çağrılar yapın. Bu makalede, bazı temel seçeneklerle nasıl başlamak açıklanmaktadır.

Önce [uygulamanızı](custom-decision-service-get-started-register.md)kaydettiğinizden emin olun.

Akıllı telefonlardan Özel Karar Alma Hizmeti için oluşturduğunuz iki API çağrısı vardır: içeriğinizin sıralanmış bir listesini almak için derecelendirme API 'sine yapılan bir çağrı ve bir ödül bildirmek için bir Reward API çağrısı. Örnek çağrılar aşağıda [verilmiştir.](https://en.wikipedia.org/wiki/CURL)

Daha fazla bilgi için bkz. başvuru [API 'si](custom-decision-service-api-reference.md).

Derecelendirme API 'SI çağrısıyla başlayın. Eylem kümesi kimliği `<request.json>`olan dosyayı oluşturun. Bu KIMLIK, portalda girdiğiniz karşılık gelen RSS veya Atom akışın adıdır:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Birçok eylem kümesi aşağıdaki şekilde belirtilebilir:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Bu JSON dosyası daha sonra derecelendirme isteğinin bir parçası olarak gönderilir:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Burada, `<appId>` uygulamanızın portalda kayıtlı adı vardır. Uygulamanızda işleyebilmeniz için sıralı bir içerik öğeleri kümesi almalısınız. Örnek bir dönüş şöyle görünür:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Döndürün ilk bölümünde, eylem kimlikleri tarafından belirtilen sıralı eylemlerin bir listesi bulunur. Bir makale için eylem KIMLIĞI bir URL 'dir. Genel isteğin Ayrıca sistem tarafından oluşturulan benzersiz `<eventId>`bir de vardır.

Daha sonra, bu olaydaki ilk içerik öğesine tıkladıysanız, `<actionId3>`yani. Daha sonra, aşağıdaki `<eventId>` gibi başka bir istek ile, ödül API aracılığıyla özel karar alma hizmeti için bir ödül bildirebilirsiniz:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Son olarak, Özel Karar Alma Hizmeti tarafından kabul edilecek makalelerin (Eylemler) listesini döndüren eylem kümesi API 'sini sağlamanız gerekir. Bu API 'yi bir RSS akışı olarak aşağıda gösterildiği gibi uygulayın:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Burada, her üst düzey `<item>` öğe bir makale açıklar. `<link>` Zorunludur ve özel karar alma hizmeti tarafından bir eylem kimliği olarak kullanılır. 15 `<date>` ' ten fazla makaleleriniz varsa (Standart bir RSS biçiminde) belirtin. En son 15 makale kullanılır. , `<title>` İsteğe bağlıdır ve makale için metinle ilgili özellikler oluşturmak için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

* Daha ayrıntılı bir örnek için [öğreticide](custom-decision-service-tutorial-news.md) çalışın.
* Belirtilen işlevsellik hakkında daha fazla bilgi edinmek için başvuru [API](custom-decision-service-api-reference.md) 'sine başvurun.
