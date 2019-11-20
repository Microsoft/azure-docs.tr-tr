---
title: Tarayıcıdan API çağırma-Özel Karar Alma Hizmeti
titlesuffix: Azure Cognitive Services
description: Bir tarayıcıdan Özel Karar Alma Hizmeti doğrudan API çağrıları yaparak bir Web sayfasını iyileştirme.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 28ad4681242765bf2da9b1f13dc828e23cce1794
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707261"
---
# <a name="call-api-from-a-browser"></a>Tarayıcıdan API çağrısı yapma

Bu makale, Azure Özel Karar Alma Hizmeti API 'Lerine doğrudan bir tarayıcıdan çağrı yapmanıza yardımcı olur.

Önce [uygulamanızı](custom-decision-service-get-started-register.md)kaydettiğinizden emin olun.

Başlayalım. Uygulamanız bir ön sayfa olarak modellenmiştir ve bu, çeşitli makale sayfalarına bağlanır. Ön sayfa, makale sayfalarının sıralamasını belirtmek için Özel Karar Alma Hizmeti kullanır. Ön sayfanın HTML kafasını aşağıdaki kodu ekleyin:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` Bağımsız değişkeni, işlenecek URL 'lerin sıralamasını içerir. Daha fazla bilgi için bkz. başvuru [API 'si](custom-decision-service-api-reference.md).

Bir kullanıcıyı işlemek için en üstteki makaleye tıklayın, ön sayfada aşağıdaki kodu çağırın:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Burada, `data` `callback()` işlevinin bağımsız değişkenidir. Bu [öğreticide](custom-decision-service-tutorial-news.md#use-the-apis)bir uygulama örneği bulunabilir.

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

Burada, her üst düzey `<item>` öğe bir makale açıklar. `<link>` Zorunludur ve özel karar alma hizmeti tarafından bir eylem kimliği olarak kullanılır. 15 `<date>` ' ten fazla makaleleriniz varsa belirtin (Standart bir RSS biçiminde). En son 15 makale kullanılır. , `<title>` İsteğe bağlıdır ve makale için metinle ilgili özellikler oluşturmak için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

* Daha ayrıntılı bir örnek için [öğreticide](custom-decision-service-tutorial-news.md) çalışın.
* Belirtilen işlevsellik hakkında daha fazla bilgi edinmek için başvuru [API](custom-decision-service-api-reference.md) 'sine başvurun.
