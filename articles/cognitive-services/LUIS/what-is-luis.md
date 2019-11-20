---
title: Language Understanding (LUIS) nedir?
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS), genel anlamı tahmin etmek ve ilgili, ayrıntılı bilgileri çekme amacıyla kullanıcının konuşmasına, doğal dil metnine özel makine öğrenimi zekası uygulayan bulut tabanlı API hizmetidir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 8ee22a2a8a12eb85439e191bc21e6cf391bea3f8
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612841"
---
# <a name="what-is-language-understanding-luis"></a>Language Understanding (LUIS) nedir?

Language Understanding (LUIS), genel anlamı tahmin etmek ve ilgili, ayrıntılı bilgileri çekme amacıyla kullanıcının konuşmasına, doğal dil metnine özel makine öğrenimi zekası uygulayan bulut tabanlı API hizmetidir. 

LUIS için istemci uygulaması, bir görevi tamamlamak için kullanıcıyla doğal dil kullanarak iletişim kuran konuşma uygulamasıdır. İstemci uygulamalarına örnek olarak sosyal medya uygulamaları, sohbet botları ve konuşma özellikli masaüstü uygulamaları verilebilir.  

![Bilişsel hizmetler Language Understanding (LUSıS) ile çalışan 3 istemci uygulamasının kavramsal resmi](./media/luis-overview/luis-entry-point.png "Bilişsel hizmetler Language Understanding (LUSıS) ile çalışan 3 istemci uygulamasının kavramsal resmi")

## <a name="use-luis-in-a-chat-bot"></a>Sohbet botunda LUIS kullanımı

<a name="Accessing-LUIS"></a>

Lua uygulaması yayımlandıktan sonra, bir istemci uygulama, LUSıS doğal dil işleme uç nokta [API][endpoint-apis] 'sine bir işlem (metin) gönderir ve sonuçları JSON yanıtları olarak alır. Sık kullanılan LUIS istemci uygulamalarından biri, sohbet botudur.


![Kullanıcı metnini doğal dil anlama (NLP) ile tahmin etmek için, lu](./media/luis-overview/LUIS-chat-bot-request-response.svg "Kullanıcı metnini doğal dil anlama ile tahmin etmek için, lu")

|Adım|Eylem|
|:--|:--|
|1|İstemci uygulaması, kullanıcının "İK temsilcimi aramak istiyorum." şeklindeki _konuşmasını_ (kendi kullandıkları kelimelerle) bir HTTP isteği olarak LUIS uç noktasına gönderir.|
|2|LUO, uygulamanıza zeka eklemek için özel dil modellerinizi yapmanızı sağlar. Makine tarafından öğrenilen dil modelleri kullanıcının yapılandırılmamış giriş metnini alır ve en iyi amaç olan `HRContact`JSON biçimli bir yanıt döndürür. JSON uç nokta yanıtı minimumda sorgu konuşmasını ve en yüksek puanlı amacı içerir. Ayrıca, _kişi türü_ varlığı gibi verileri de ayıklayabilir.|
|3|İstemci uygulaması, JSON yanıtını kullanarak kullanıcının isteklerini gerçekleştirmeyle ilgili kararları verir. Bu kararlar, bot Framework kodunda karar ağacı ve diğer hizmetlere çağrılar içerebilir. |

LUIS uygulaması, istemci uygulamasının akıllı seçimler yapabilmesi için gerekli bilgileri sunar. LUIS bu seçenekleri sağlamaz. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Doğal dil işleme

LUSıS uygulamanız, etki alanına özgü doğal dil modeli içerir. LUIS uygulamasını önceden oluşturulmuş bir etki alanı modeliyle başlatabilir, kendi modelinizi oluşturabilir veya önceden oluşturulmuş etki alanının belirli bölümlerini kendi özel bilgilerinizle karıştırabilirsiniz.

* **Önceden oluşturulmuş model**: LUIS amaç, konuşma ve önceden oluşturulmuş varlık içeren birçok önceden oluşturulmuş etki alanı modeline sahiptir. Önceden oluşturulmuş modelin amaçlarını ve konuşmalarını kullanmak zorunda kalmadan önceden oluşturulmuş varlıkları kullanabilirsiniz. [Önceden oluşturulmuş etki alanı modelleri](luis-how-to-use-prebuilt-domains.md), tasarımın tamamını içerir ve LUIS hizmetini kullanmaya başlamak için ideal bir yoldur.

* **Özel model** LUO, amaçları ve varlıkları dahil kendi özel modellerinizi belirlemek için size çeşitli yollar sağlar. Varlıklar, makine tarafından öğrenilen varlıkları, belirli veya değişmez varlıkları ve makine tarafından öğrenilen ve değişmez değer birleşimini içerir.

## <a name="build-the-luis-model"></a>LUIS modelini derleme
Modeli [yazma](https://go.microsoft.com/fwlink/?linkid=2092087) API 'Leriyle veya [LUIS portalı](https://www.luis.ai)ile oluşturun.

LUIS modeli, **[amaçlar](luis-concept-intent.md)** olarak adlandırılan kullanıcı amacı kategorileriyle başlar. Her amaç için kullanıcı **[konuşmaları](luis-concept-utterance.md)** örneklerine ihtiyaç duyulur. Her söylük ayıklanmak gereken verileri sağlayabilir. 

|Örnek kullanıcı konuşması|Amaç|Ayıklanan veriler|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|açık|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|13, Bob|

## <a name="query-prediction-endpoint"></a>Sorgu tahmin uç noktası

Uygulamanız eğitildikten ve uç noktada yayımlandıktan sonra, istemci uygulaması, tahmin [uç noktası](https://go.microsoft.com/fwlink/?linkid=2092356) API 'sine bir konuşma gönderir. API, uygulamayı Analize analiz için uygular ve tahmin sonuçlarıyla bir JSON biçiminde yanıt verir.  

JSON uç nokta yanıtı minimumda sorgu konuşmasını ve en yüksek puanlı amacı içerir. Ayrıca, aşağıdaki **kişi türü** varlığı ve genel yaklaşım gibi verileri de ayıklayabilir. 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Model tahminini geliştirme

LUP uygulamanız yayımlandıktan ve gerçek Kullanıcı dıklılığını aldıktan sonra, Lud, tahmin doğruluğunu artırmak için uç nokta dıklarınızın etkin bir şekilde [öğrenilmesine](luis-concept-review-endpoint-utterances.md) olanak sağlar. 

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Geliştirme yaşam döngüsü
LUO, tüm [geliştirme yaşam döngüsüyle](luis-concept-app-iteration.md)tümleştirilecek diğer Luo yazarlarıyla birlikte araçlar, sürüm oluşturma ve işbirliği sağlar. 

## <a name="implementing-luis"></a>LUIS uygulama
Language Understanding (LUU), bir REST API olarak HTTP isteğiyle herhangi bir ürünle, hizmette veya çerçevede kullanılabilir. Aşağıdaki liste, LUIS ile birlikte en çok kullanılan Microsoft ürünlerini ve hizmetlerini göstermektedir.

En çok kullanılan LUIS istemci uygulamaları şunlardır:
* [Web uygulaması botu](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0), kullanıcıyla metin girişi aracılığıyla konuşmak için hızlıca LUIS destekli bir sohbet botu oluşturur. Tüm bot deneyimi için [bot Framework][bot-framework] sürüm [4. x](https://github.com/Microsoft/botbuilder-dotnet) 'i kullanır.

LUIS'i hızlı ve kolay bir şekilde botla birlikte kullanmanızı sağlayacak uygulamalar:
* [LUSıS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) NPM paketi, tek başına bir komut satırı aracı olarak veya içeri aktarma olarak ile yazma ve tahmin sağlar. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen, dışarı aktarılan bir LUIS modelinden ayrıntılı C# ve typescript kaynak kodu yazmak için kullanılan bir araçtır.
* [Gönderme](https://aka.ms/dispatch-tool), çeşitli LUIS ve Soru-Cevap Oluşturma uygulamalarının gönderme modelini kullanan bir üst uygulamadan kullanılmasını sağlar.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown, botunuz için dil modellerini yönetmenize yardımcı olan bir komut satırı aracıdır.
* [Bot Framework-besteci](https://github.com/microsoft/BotFramework-Composer) -geliştiriciler ve çok disiplinli takımlar Için Microsoft bot Framework ile botları ve konuşma deneyimleri oluşturmaya yönelik tümleşik bir geliştirme aracı

LUIS ile kullanılan diğer Bilişsel Hizmetler:
* [Soru-cevap oluşturma][qnamaker] , bir soru ve yanıt bilgi bankasındaki çeşitli metin türlerinin birleştirilmesine izin verir.
* [Konuşma hizmeti](../Speech-Service/overview.md), sözlü dil isteklerini metne dönüştürür. 
* [Konuşma öğrenici](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview), LUIS ile daha hızlı bir şekilde sohbet botları oluşturmanızı sağlar.

LUŞU kullanan örnekler:
* [Konuşma AI](https://github.com/Microsoft/AI) GitHub deposu.
* [Bot Framework-bot örnekleri](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Sonraki adımlar

* [Yenilikler](whats-new.md)
* [Önceden oluşturulmuş](luis-get-started-create-app.md) veya [özel](luis-quickstart-intents-only.md) etki alanıyla yeni bir LUIS uygulaması yazma.
* Genel IoT uygulamasının [tahmin uç noktasını sorgulama](luis-get-started-get-intent-from-browser.md). 
* LUSıS için [Geliştirici kaynakları](developer-reference-resource.md) . 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
