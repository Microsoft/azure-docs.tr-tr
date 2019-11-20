---
title: Mürekkep Tanıma API’sine mürekkep verisi gönderme
titleSuffix: Azure Cognitive Services
description: Farklı uygulamalar için mürekkep Çözümleyicisi API 'sini çağırma hakkında bilgi edinin
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274483"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Mürekkep Tanıma API’sine mürekkep verisi gönderme 

Dijital mürekkep oluşturma, el yazısı ve çizim gibi girdilerin dijital sunumunun yapılmasını sağlayan teknolojilere başvurur. Bu, genellikle ekran kalemi gibi giriş cihazlarının hareketlerini yakalayan bir çizim tablası kullanılarak elde edilir. Cihazlar zengin dijital mürekkep oluşturma deneyimleri sunmaya devam ederken yapay zeka ve makine öğrenimi, yazılı metin ve şekillerin tüm bağlamlarda tanınmasını sağlar. Mürekkep tanıyıcı API 'SI mürekkep vuruşları göndermenizi ve bunlarla ilgili ayrıntılı bilgi almanızı sağlar. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Mürekkep tanıyıcı API 'SI ile OCR Hizmetleri

Mürekkep tanıyıcı API 'SI, optik karakter tanıma (OCR) kullanmaz. OCR Hizmetleri, el yazısı ve metin tanıma sağlamak için görüntülerden piksel verilerini işler. Bu bazen çevrimdışı tanıma olarak adlandırılır. Bunun yerine, mürekkep tanıyıcı API 'SI, giriş cihazı kullanıldığı için yakalanan dijital mürekkep konturu verileri gerektirir. Dijital mürekkep verilerini bu şekilde işlemek, OCR Hizmetleri ile karşılaştırıldığında daha doğru tanıma sonuçları üretebilir. 

## <a name="sending-ink-data"></a>Mürekkep verileri gönderiliyor

Mürekkep tanıyıcı API 'SI, bir giriş cihazı tarafından oluşturulan mürekkep konturlarını temsil eden X ve Y koordinatlarını, yükseltilmemiş olduğu zaman algılama yüzeyine dokunduğu andan itibaren gösterir. Her konturun noktaları, virgülle ayrılmış değerler dizesi olmalıdır ve aşağıdaki örnekte olduğu gibi JSON 'da biçimlendirilmelidir. Ayrıca, her mürekkep vuruşunun her istekte benzersiz bir KIMLIĞI olmalıdır. KIMLIK aynı istek içinde yinelenirse, API bir hata döndürür. En doğru tanıma sonuçları için, ondalık ayırıcıdan sonra en az sekiz basamak vardır. Tuvalin başlangıç noktası (0, 0), mürekkep tuvalinin sol üst köşesinden varsayılır.

> [!NOTE]
> Aşağıdaki örnek geçerli bir JSON değil. [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909)'da tam bir mürekkep tanıyıcı JSON isteği bulabilirsiniz.
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Mürekkep tanıyıcı yanıtı

Mürekkep tanıyıcı API 'SI, mürekkep içeriğinden tanınan nesneler hakkında bir çözümleme yanıtı döndürür. Yanıt, farklı mürekkep vuruşları arasındaki ilişkileri tanımlayan tanıma birimlerini içerir. Örneğin, ayrı ayrı şekiller oluşturan konturlar farklı birimlerde yer alır. Her birim, tanınan nesne, koordinatları ve diğer çizim özniteliklerini içeren mürekkep vuruşları hakkında ayrıntılı bilgiler içerir.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Mürekkep tanıyıcı API 'SI tarafından tanınan şekiller

Mürekkep tanıyıcı API 'SI, notta en yaygın olarak kullanılan şekilleri tanımlayabilir. Aşağıdaki görüntüde bazı temel örnekler gösterilmektedir. API tarafından tanınan şekillerin ve diğer mürekkep içeriğinin tam listesi için [API başvurusu makalesine](https://go.microsoft.com/fwlink/?linkid=2089907)bakın. 

![Mürekkep tanıyıcı API 'SI tarafından tanınan şekillerin listesi](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Önerilen çağırma desenleri

Uygulamanıza göre farklı desenlerdeki mürekkep tanıyıcı REST API çağırabilirsiniz. 

### <a name="user-initiated-api-calls"></a>Kullanıcı tarafından başlatılan API çağrıları

Kullanıcı girişi alan bir uygulama oluşturuyorsanız (örneğin, bir not alma veya ek açıklama uygulaması), bu kullanıcılara mürekkep tanıyıcı API 'sine ne zaman ve hangi mürekkebin gönderileceğini denetlemek isteyebilirsiniz. Bu işlevsellik, metin ve şekillerin her ikisi de tuvalde varsa ve kullanıcılar her biri için farklı eylemler gerçekleştirmek istedikleri zaman yararlıdır. Kullanıcıların API 'ye ne gönderileceğini seçmesini sağlayan seçim özelliklerini (kement veya diğer geometrik seçim aracı gibi) eklemeyi düşünün.  

### <a name="app-initiated-api-calls"></a>Uygulama tarafından başlatılan API çağrıları

Ayrıca, uygulamanızın bir zaman aşımından sonra mürekkep tanıyıcı API 'sini çağırmasını sağlayabilirsiniz. Geçerli mürekkep vuruşlarını API 'ye düzenli olarak göndererek, tanıma sonuçlarını API 'nin yanıt süresini geliştirirken oluşturuldukları sırada saklayabilirsiniz. Örneğin, Kullanıcı tarafından tamamlandığını algıladıktan sonra API 'ye bir satır el yazısı metin gönderebilirsiniz. 

Tanınma sonuçlarının daha fazla olması, mürekkep vuruşlarının birbirleriyle ilgili özellikleri hakkında bilgi verir. Örneğin, aynı sözcük, çizgi, liste, paragraf veya şekli biçimlendirmek için hangi vuruşların gruplandığını. Bu bilgiler, örneğin, bir defada vuruş grupları seçebilerek uygulamanızın mürekkep seçimi özelliklerini geliştirebilir.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Windows Ink ile mürekkep tanıyıcı API 'sini tümleştirme

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) , farklı bir cihaz yelpazilerinde dijital mürekkep deneyimlerini etkinleştirmek için araçlar ve teknolojiler sağlar. Dijital mürekkep vuruşlarını görüntüleyen ve yorumlayan uygulamalar oluşturmak için Windows Ink platformunu mürekkep tanıyıcı API 'SI ile birleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Mürekkep tanıyıcı API nedir?](../overview.md)
* [Mürekkep tanıyıcı REST API başvurusu](https://go.microsoft.com/fwlink/?linkid=2089907)

* Şunu kullanarak dijital mürekkep konturu verileri göndermeye başla:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
