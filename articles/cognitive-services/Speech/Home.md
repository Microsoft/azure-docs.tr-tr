---
title: Microsoft Bing Konuşma hizmeti | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Kullanıcılarınıza gerçek zamanlı etkileşim de dahil olmak üzere, uygulamalarınıza konuşma odaklı eylemler eklemek için Microsoft konuşma API 'sini kullanın.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966641"
---
# <a name="what-is-bing-speech"></a>Bing Konuşma nedir?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bulut tabanlı Microsoft Bing Konuşma API'si geliştiricilere, uygulamalarında sesli komut denetimi, doğal konuşma konuşması kullanan kullanıcı iletişim kutusu, konuşma dökümü ve dikte etme gibi güçlü konuşma özellikli Özellikler oluşturmanın kolay bir yolunu sunar. Microsoft konuşma API 'SI, hem *metin* hem de *metin okuma* dönüştürmeyi destekler.

- **Konuşmayı metne** dönüştürme API, insan konuşmanızı, uygulamanızı denetlemek için giriş veya komut olarak kullanılabilecek metne dönüştürür.
- **Metin okuma** API, metni uygulamanızın kullanıcısına oynatılabilecek ses akışlarına dönüştürür.

## <a name="speech-to-text-speech-recognition"></a>Konuşmayı metne dönüştürme (konuşma tanıma)

Microsoft konuşma *tanıma API 'si* , uygulamanızın kullanıcıya görüntüleyebilen veya komut girişi olarak üzerinde işlem yapması için ses akışlarını metne dönüştürür. Geliştiricilerin uygulamalarına konuşma eklemesi için iki yol sunar: REST API 'Leri **veya** WebSocket tabanlı istemci kitaplıkları.

- [REST API 'leri](GetStarted/GetStartedREST.md): Geliştiriciler, konuşma tanıma için uygulamalarından gelen HTTP çağrılarını kullanabilirler.
- [İstemci kitaplıkları](GetStarted/GetStartedClientLibraries.md): Geliştiriciler, gelişmiş özellikler için Microsoft konuşma istemci kitaplıklarını indirebilir ve uygulamalarına bağlanabilir.  İstemci kitaplıkları, farklı diller (C#, Java, JavaScript, ObjectiveC) kullanan çeşitli platformlarda (Windows, Android, iOS) kullanılabilir. REST API 'Lerinin aksine, istemci kitaplıkları WebSocket tabanlı protokolü kullanır.

| Uygulama alanları | [REST API'ler](GetStarted/GetStartedREST.md) | [İstemci kitaplıkları](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Kısa bir konuşulan sesi (örneğin, ses uzunluğu < 15 s) ara sonuçlar olmadan Dönüştür | Evet | Evet |
| Uzun bir sesi dönüştürme (> 15 s) | Hayır | Evet |
| Ara sonuçlarla istenen ses akışını | Hayır | Evet |
| LUSıS kullanarak sesten dönüştürülen metni anlayın | Hayır | Evet |

Geliştiricilerin hangi yaklaşım tarafından seçtiğine (REST API 'Leri veya istemci kitaplıkları), Microsoft konuşma hizmeti aşağıdakileri destekler:

- Cortana, Office dikte, Office Translator ve diğer Microsoft ürünleri tarafından kullanılan Microsoft 'un gelişmiş konuşma tanıma teknolojileri.
- Gerçek zamanlı sürekli tanıma. Konuşma tanıma API 'SI, kullanıcıların sesi gerçek zamanlı olarak metne almasını sağlar ve şimdiye kadar tanınan sözcüklerin ara sonuçlarını almak için destekler. Konuşma hizmeti, konuşma sonu algılamayı de destekler. Ayrıca kullanıcılar, büyük harfler, noktalama, bir küfür ve metin normalleştirmesi gibi ek biçimlendirme özellikleri seçebilirler.
- *Etkileşimli*, *konuşma*ve *dikte* senaryoları için iyileştirilmiş konuşma tanıma sonuçlarını destekler. [Özel konuşma tanıma hizmeti](../custom-speech-service/cognitive-services-custom-speech-home.md) , özelleştirilmiş dil modelleri ve akustik modeller gerektiren Kullanıcı senaryolarında, uygulamanız ve kullanıcılarınız için uygun konuşma modelleri oluşturmanızı sağlar.
- Birden çok diatadaki birçok konuşulan dili destekler. Her tanınma modundaki desteklenen dillerin tam listesi için bkz. [tanıma dilleri](api-reference-rest/supportedlanguages.md).
- Dil anlama ile tümleştirme. Giriş sesini metne dönüştürmenin yanı sıra *konuşmayı metne* dönüştürme, uygulamanın metnin ne anlama geldiğini anlamak için ek bir yetenek sağlar. Tanınan metinden amaçları ve varlıkları ayıklamak için [Language Understanding Intelligent Service (Luo)](../LUIS/what-is-luis.md) kullanır.

### <a name="next-steps"></a>Sonraki adımlar

- Microsoft konuşma tanıma hizmetini [REST API 'leri](GetStarted/GetStartedREST.md) veya [istemci kitaplıkları](GetStarted/GetStartedClientLibraries.md)ile kullanmaya başlayın.
- Tercih ettiğiniz programlama dilindeki [örnek uygulamalara](samples.md) göz atın.
- [Microsoft Speech protokol](API-Reference-REST/websocketprotocol.md) AYRıNTıLARıNı ve API başvurularını bulmak için başvuru bölümüne gidin.

## <a name="text-to-speech-speech-synthesis"></a>Metinden konuşmaya (konuşma senşi)

*Metin okuma* API 'Ler, yapılandırılmış metni bir ses akışına dönüştürmek için REST kullanır. API 'Ler, çeşitli sesler ve dillerde konuşma dönüştürmeye hızlı metin sağlar. Ayrıca kullanıcılar ayrıca, telaffuz, hacim, sıklık gibi ses özelliklerini değiştirme olanağına sahiptir. SSML etiketlerini kullanma.

### <a name="next-steps"></a>Sonraki adımlar

- Microsoft Text to konuşma hizmetini kullanmaya başlayın: [Metin okuma API'si Başvurusu](api-reference-rest/bingvoiceoutput.md). Metin Okuma tarafından desteklenen dillerin ve seslerin tam listesi için bkz. [desteklenen yerel ayarlar ve ses yazı tipleri](api-reference-rest/bingvoiceoutput.md#SupLocales).
