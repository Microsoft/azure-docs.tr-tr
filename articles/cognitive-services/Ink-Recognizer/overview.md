---
title: Mürekkep tanıyıcı nedir? -Ink tanıyıcı API 'SI
titleSuffix: Azure Cognitive Services
description: Mürekkep vuruşu verilerinin giriş olarak tanımlanmasını ve kullanılmasını sağlamak için mürekkep tanıyıcıyı uygulamalarınızın, Web siteleriniz, araçlarınızla ve diğer çözümlerle tümleştirin.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: c90d656539a2ed64b416dc9b8e7e11e205b98ee6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478363"
---
# <a name="what-is-the-ink-recognizer-api"></a>Mürekkep Tanıma API’si nedir?


Mürekkep tanıyıcı bilişsel hizmeti, dijital mürekkep içeriğini çözümlemek ve tanımak için bulut tabanlı bir REST API sağlar. Optik karakter tanıma (OCR) kullanan hizmetlerden farklı olarak, API, giriş olarak dijital mürekkep kontur verileri gerektirir. Dijital mürekkep vuruşları, dijital kalemlerin veya parmakların gibi giriş araçlarının hareketini temsil eden, zaman sıralı 2B puntolar (X, Y koordinatları) kümesidir. Daha sonra bu şekil, girdiden şekilleri ve el yazısı içeriğini tanır ve tüm tanınan varlıkları içeren bir JSON yanıtı döndürür.

![API 'ye mürekkep konturu girişi göndermeyi açıklayan bir akış çizelgesi](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Özellikler

Mürekkep tanıyıcı API 'SI sayesinde, uygulamalarınızda el yazısı içeriğini kolayca tanıyabilirsiniz. 

|Özellik  |Açıklama  |
|---------|---------|
| El yazısı tanıma | 63 çekirdek [dilinde ve yerel ayarlarda](language-support.md)el ile yazılmış içeriği tanıyın. | 
| Düzen tanıma | Dijital mürekkep içeriğiyle ilgili yapısal bilgiler alın. İçeriği, paragrafları, satırları, sözcükleri ve madde işaretli listeleri yazma bölümlerine bölün. Uygulamalarınız daha sonra otomatik liste biçimlendirme ve şekil hizalama gibi ek özellikler oluşturmak için düzen bilgilerini kullanabilir. |
| Şekil tanıma | Not alırken en yaygın olarak kullanılan [geometrik şekilleri](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) tanır. |
| Birleşik şekiller ve metin tanıma | Şekillere veya el yazısı içeriğine ait olan mürekkep vuruşlarını ve bunları ayrı olarak sınıflandırın.|

## <a name="workflow"></a>İş akışı

Mürekkep tanıyıcı API 'SI, bir Web hizmeti olduğundan, HTTP istekleri yapan ve JSON 'u ayrıştırabilen herhangi bir programlama dilinden çağrı yapmayı kolaylaştırır.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Kaydolduktan sonra:

1. Mürekkep vuruşu verilerinizi alın ve geçerli JSON olarak [biçimlendirin](concepts/send-ink-data.md#sending-ink-data) .
1. Verilerinize yönelik olarak mürekkep tanıyıcı API 'sine bir istek gönderin.
1. Döndürülen JSON iletisini ayrıştırarak API yanıtını işleyin.

## <a name="next-steps"></a>Sonraki adımlar

Mürekkep tanıyıcı API 'sine çağrı yapmaya başlamak için aşağıdaki dillerde bir hızlı başlangıç yapın.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

Mürekkep tanıma API 'sinin dijital bir mürekkep uygulamasında nasıl çalıştığını görmek için GitHub 'da aşağıdaki örnek uygulamalara göz atın:
* [C# Evrensel Windows Platformu (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript web tarayıcı uygulaması](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java ve Android mobil uygulaması](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift ve iOS mobil uygulaması](https://go.microsoft.com/fwlink/?linkid=2089805)
