---
title: Kurumsal kavramlar-LUSıS
titleSuffix: Azure Cognitive Services
description: Büyük luya uygulamalarına yönelik tasarım kavramlarını veya LUSıS dahil birden çok uygulamayı anlayın ve birlikte Soru-Cevap Oluşturma.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 3f52dc502233cbab42ae5decff70b77c41c38e0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609694"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Bir LUSıS uygulaması için Kurumsal Stratejiler
Kurumsal uygulamanız için bu tasarım stratejilerini gözden geçirin.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Kotanın ötesinde LUSıS isteklerini tahmin ettiğinizde

Lua 'nın, Azure kaynağının fiyatlandırma katmanına bağlı olarak aylık bir kotası ve saniye başına kotayı vardır. 

LUSıS uygulama isteği hızlarınız izin verilen [Kota oranını](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)aşarsa şunları yapabilirsiniz:

* Yükü [aynı uygulama tanımına](#use-multiple-apps-with-same-app-definition)sahip daha fazla lusıs uygulamasına yayın. Bu, isteğe bağlı olarak, bir [KAPSAYıCıDAN](luis-container-howto.md)halsıs çalıştırmayı içerir. 
* Uygulamaya [birden çok anahtar](#assign-multiple-luis-keys-to-same-app) oluşturun ve atayın. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Aynı uygulama tanımıyla birden çok uygulama kullanma
Özgün LUO uygulamasını dışarı aktarıp uygulamayı ayrı uygulamalara geri alın. Her uygulamanın kendi uygulama KIMLIĞI vardır. Yayımladığınızda, tüm uygulamalarda aynı anahtarı kullanmak yerine, her uygulama için ayrı bir anahtar oluşturun. Tek bir uygulamanın aşırı olmaması için yükü tüm uygulamalarda dengeleyin. Kullanımı izlemek için [Application Insights](./luis-csharp-tutorial-bf-v4.md) ekleyin. 

Tüm uygulamalar arasında aynı en üst amaca ulaşmak için birinci ve ikinci amaç arasındaki amaç tahmininin çok fazla olduğundan emin olun ve uygulamalar arasında küçük değişimlere yönelik uygulamalar arasında farklı sonuçlar verir. 

Bu eşdüzey uygulamalara eğitim yaparken, [tüm verilerle eğdiğinizden](luis-how-to-train.md#train-with-all-data)emin olun.

Tek bir uygulamayı ana öğe olarak belirleyin. İnceleme için önerilen tüm söyleyime ana uygulamaya eklenmelidir, daha sonra diğer tüm uygulamalara geri taşınır. Bu, uygulamanın tam olarak dışarı aktarılması veya ana bilgisayardan alt öğe arasında etiketlenmiş bir şekilde yükleniyor. Yükleme işlemi, [tek](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) bir şekilde veya bir [toplu iş](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)için [luya](luis-reference-regions.md) Web sitesinden veya yazma API 'sinden yapılabilir. 

Her iki haftada bir, etkin öğrenme için [uç nokta çeşidinin](luis-how-to-review-endpoint-utterances.md) ve sonra yeniden eğitme ve yeniden yayımlama gibi düzenli bir inceleme zamanlayın. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Aynı uygulamaya birden çok LUO anahtarı atama
LUSıS uygulamanız, tek anahtarınızın kotasının izin verdiğinden daha fazla uç nokta isabetlerinin alırsa, LUO uygulamasına daha fazla anahtar oluşturun ve atayın. Uç nokta anahtarları arasında uç nokta sorgularını yönetmek için bir Traffic Manager veya yük dengeleyici oluşturun. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Tek parçalı uygulamanız yanlış bir amaç döndürürse
Uygulamanız çok çeşitli Kullanıcı çeşitlerini tahmin etmek istiyorsanız, [dağıtım modelini](#dispatch-tool-and-model)uygulamayı göz önünde bulundurun. Tek parçalı bir uygulamayı bölmek, LUTO 'nın, ana uygulama ve alt uygulamalar arasındaki amaçlar arasında kafa ortadan kaldırma yerine, amaçlar arasında algılamayı başarıyla odaklamasına olanak tanır. 

Her iki hafta gibi etkin öğrenme için [uç nokta utinlerini düzenli olarak gözden geçirin](luis-how-to-review-endpoint-utterances.md) , sonra yeniden eğitme ve yeniden yayımlayın. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>500 ' den fazla amaç olması gerektiğinde
500 ' den fazla amaç içeren bir Office Yardımcısı geliştirdiğinizi varsayın. 200 amaçları, toplantılar planlanmasıyla ilgili ise, 200 200 anımsatıcılar hakkında, çalışma arkadaşları hakkında bilgi almak ve her bir grubun tek bir uygulamada olması için adım adım, Grup amaçlarını ve her bir amacı içeren bir en üst düzey uygulama oluşturmak için 200. En üst düzey uygulamayı derlemek için [dağıtım modelini](#dispatch-tool-and-model) kullanın. Ardından, [dağıtım modelinin öğreticisinde](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs)gösterildiği gibi basamaklı çağrıyı kullanmak için bot ' ı değiştirin. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Birkaç LUO ve QnA Maker uygulaması birleştirmeniz gerektiğinde
Bir bot 'a yanıt vermesi gereken birkaç LUVE QnA Maker uygulaması varsa, en üst düzey uygulamayı derlemek için [dağıtım modelini](#dispatch-tool-and-model) kullanın.  Ardından, [dağıtım modelinin öğreticisinde](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs)gösterildiği gibi basamaklı çağrıyı kullanmak için bot ' ı değiştirin. 

## <a name="dispatch-tool-and-model"></a>Dağıtım aracı ve modeli
Birden çok LUO ve/veya Soru-Cevap Oluşturma uygulamalarını bir üst LUO uygulamasında birleştirmek için [Botbuilder-Tools](https://github.com/Microsoft/botbuilder-tools) ' da bulunan [dağıtım][dispatch-tool] komut satırı aracını kullanın. Bu yaklaşım, ayrı uygulamalardaki tüm konuları ve farklı alt konu etki alanlarını içeren bir üst etki alanınız olmasına olanak sağlar. 

![Dağıtım mimarisinin kavramsal görüntüsü](./media/luis-concept-enterprise/dispatch-architecture.png)

Ana etki alanı,, uygulamalar listesinde adlı bir sürüm ile LUSıS 'de belirtilmiştir `Dispatch` . 

Sohbet bot, utterance 'i alır, ardından tahmin için üst LUO uygulamasına gönderir. Üst uygulamadan alınan en iyi tahmin amacı, hangi LUO alt uygulamasının bir sonraki çağrıldığını belirler. Sohbet botu, daha belirli bir tahmin için alt uygulamaya gelen söylenişi 'yi gönderir.

Bot Builder v4 [dağıtıcısı-Application-öğreticisi](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs)' nden bu çağrı hiyerarşisinin nasıl yapıldığını anlayın.  

### <a name="intent-limits-in-dispatch-model"></a>Dağıtım modelinde amaç limitleri
Bir dağıtım uygulamasının 500 dağıtım kaynağı, en fazla 500 hedefleri ile eşdeğerdir. 

## <a name="more-information"></a>Daha fazla bilgi

* [Bot Framework SDK 'Sı](https://github.com/Microsoft/botframework)
* [Dağıtım modeli öğreticisi](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs)
* [CLı dağıtma](https://github.com/Microsoft/botbuilder-tools)
* Dağıtım modeli bot örneği- [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Sonraki adımlar

* [Toplu işi test](luis-how-to-batch-test.md) etme hakkında bilgi edinin

[dispatcher-application-tutorial]: /azure/bot-service/bot-builder-tutorial-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool