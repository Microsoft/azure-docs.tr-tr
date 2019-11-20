---
title: Kurumsal kavramlar-LUSıS
titleSuffix: Azure Cognitive Services
description: Büyük LUIS uygulama ve LUIS ve soru-cevap Oluşturucu birlikte dahil olmak üzere birden fazla uygulama için tasarım kavramları anlayın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619930"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Bir LUIS uygulaması için Kurumsal stratejileri
Bu tasarım stratejiler Kurumsal uygulamanız için gözden geçirin.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Kotayı aşan LUIS istekler zaman beklediğiniz

Lua 'nın, Azure kaynağının fiyatlandırma katmanına bağlı olarak aylık bir kotası ve saniye başına kotayı vardır. 

LUSıS uygulama isteği hızlarınız izin verilen [Kota oranını](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)aşarsa şunları yapabilirsiniz:

* Yükü [aynı uygulama tanımına](#use-multiple-apps-with-same-app-definition)sahip daha fazla lusıs uygulamasına yayın. Bu, isteğe bağlı olarak, bir [KAPSAYıCıDAN](luis-container-howto.md)halsıs çalıştırmayı içerir. 
* Uygulamaya [birden çok anahtar](#assign-multiple-luis-keys-to-same-app) oluşturun ve atayın. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Birden fazla uygulama ile aynı uygulama tanımı kullanın
Özgün LUIS uygulaması dışarı aktardıktan sonra ayrı uygulamalarda uygulama içeri aktarın. Her uygulamanın kendi uygulama kimliği vardır. Tüm uygulamalar arasında aynı anahtarı kullanarak yerine yayımladığınızda, her uygulama için ayrı bir anahtar oluşturun. Böylece hiç tek uygulama doludur tüm uygulamalar arasında yük dengeleyin. Ekleme [Application Insights](luis-tutorial-bot-csharp-appinsights.md) kullanımını izlemek için. 

Tüm uygulamalar arasında aynı üst amaç alabilmek için birinci ve ikinci amacı arasında hedefi tahmin LUIS konuşma küçük çeşitleri için uygulamalar arasında farklı sonuçlar vermek kafanız, olmadığını yetecek kadar geniş olduğundan emin olun. 

Bu eşdüzey uygulamalara eğitim yaparken, [tüm verilerle eğdiğinizden](luis-how-to-train.md#train-with-all-data)emin olun.

Tek bir uygulama yöneticisi olarak belirleyin. Gözden geçirme için önerilen herhangi bir konuşma ana uygulamaya eklenen ardından için tüm diğer uygulamaları geri taşınır. Uygulamanın tam bir dışarı aktarma veya etiketli konuşma asıl alt öğelerine yüklenirken budur. Yükleme araçtan yapılabilir [LUIS](luis-reference-regions.md) Web sitesi veya geliştirme API için bir [tek utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) veya bir [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Her iki haftada bir, etkin öğrenme için [uç nokta çeşidinin](luis-how-to-review-endpoint-utterances.md) ve sonra yeniden eğitme ve yeniden yayımlama gibi düzenli bir inceleme zamanlayın. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Birden çok LUIS anahtarları aynı uygulamaya atama
LUIS uygulamanızı daha fazla uç noktası İsabeti tek anahtarının kota izin verdiğinden, oluşturma ve daha fazla anahtarları LUIS uygulaması için atama alırsa. Bir traffic manager oluşturma veya yük dengeleyici uç nokta sorguları uç nokta anahtarlarını yönetme. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Tek parça uygulamanızı yanlış hedefi döndürdüğünde
Uygulamanızı çok çeşitli kullanıcı konuşma tahmin etmek için geliyorsa, uygulamayı düşünün [gönderme modeli](#dispatch-tool-and-model). Tek parça bir uygulamayı kesme LUIS üst uygulama arasında hedefleri ve alt uygulamalar arasında yanıltıcı yerine başarıyla ıntents arasında odağı algılama sağlar. 

Düzenli bir zamanlama [konuşma uç noktası incelenmesi](luis-how-to-review-endpoint-utterances.md) iki haftada gibi etkin olarak öğrenmeye için daha sonra yeniden eğitme ve yeniden yayımlayın. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>500'den fazla hedefleri gerektiğinde
500 ' den fazla amaç içeren bir Office Yardımcısı geliştirdiğinizi varsayın. 200 amacı, Toplantı zamanlama için ilişkiliyse, ilgili anımsatıcılar 200 olan, iş arkadaşlarınızın hakkında bilgi alma 200 olduğundan ve e-posta göndermek için 200 olan, grubunun hedefleri böylece her grubu tek bir uygulama olarak, ardından her hedefi içeren üst düzey bir uygulama oluşturabilirsiniz. En üst düzey uygulamayı derlemek için [dağıtım modelini](#dispatch-tool-and-model) kullanın. Ardından, [dağıtım modelinin öğreticisinde](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)gösterildiği gibi basamaklı çağrıyı kullanmak için bot ' ı değiştirin. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Çeşitli LUIS ve soru-cevap Oluşturucu uygulamaları birleştirmek gerektiğinde
Bir bot 'a yanıt vermesi gereken birkaç LUVE QnA Maker uygulaması varsa, en üst düzey uygulamayı derlemek için [dağıtım modelini](#dispatch-tool-and-model) kullanın.  Ardından, [dağıtım modelinin öğreticisinde](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)gösterildiği gibi basamaklı çağrıyı kullanmak için bot ' ı değiştirin. 

## <a name="dispatch-tool-and-model"></a>Dağıtım aracı ve modeli
Birden çok LUO ve/veya Soru-Cevap Oluşturma uygulamalarını bir üst LUO uygulamasında birleştirmek için [Botbuilder-Tools](https://github.com/Microsoft/botbuilder-tools) ' da bulunan [dağıtım][dispatch-tool] komut satırı aracını kullanın. Bu yaklaşım, tüm konular ve farklı alt konu etki alanlarını ayrı uygulamalar da dahil olmak üzere bir üst etki alanınız olanak tanır. 

![Kavramsal gönderme mimarisi görüntüsü](./media/luis-concept-enterprise/dispatch-architecture.png)

Üst etki alanını adlı bir sürümle LUIS içinde belirtilir `Dispatch` uygulamalar listesinde. 

Sohbet bot, utterance 'i alır, ardından tahmin için üst LUO uygulamasına gönderir. Üst uygulamadan alınan en iyi tahmin amacı, hangi LUO alt uygulamasının bir sonraki çağrıldığını belirler. Sohbet botu, daha belirli bir tahmin için alt uygulamaya gelen söylenişi 'yi gönderir.

Bot Builder v4 [dağıtıcısı-Application-öğreticisi](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)' nden bu çağrı hiyerarşisinin nasıl yapıldığını anlayın.  

### <a name="intent-limits-in-dispatch-model"></a>Gönderme modeli hedefi sınırları
Gönderme uygulama en fazla 500 gönderme kaynağı, 500 hedefleri için eşdeğer yok. 

## <a name="more-information"></a>Daha fazla bilgi

* [Bot Framework SDK 'Sı](https://github.com/Microsoft/botframework)
* [Dağıtım modeli öğreticisi](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [CLı dağıtma](https://github.com/Microsoft/botbuilder-tools)
* Dağıtım modeli bot örneği- [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Sonraki adımlar

* Bilgi edinmek için nasıl [toplu test](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
