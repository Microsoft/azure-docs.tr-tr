---
title: Konuşmaları zenginleştirmek için NLP bilişsel hizmetler 'i kullanma
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler, Language Understanding ve Soru-Cevap Oluşturma her biri farklı bir amaca sahip iki doğal dil işleme hizmeti sağlar. Her bir hizmetin ne zaman kullanılacağını ve birbirini nasıl zorluk yaptığını anlayın.
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: 32159b37d3d1a8609181d81dc1a73f27177adb85
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818208"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>Bot konuşmalarını zenginleştirmek için bilişsel hizmetler 'i doğal dil işleme (NLP) ile birlikte kullanma

Bilişsel hizmetler, [Language Understanding](what-is-luis.md) ve [soru-cevap oluşturma](../qnamaker/overview/overview.md)her biri farklı bir amaca sahip iki doğal dil işleme hizmeti sağlar. Her bir hizmetin ne zaman kullanılacağını ve birbirini nasıl zorluk yaptığını anlayın. 

Doğal dil işleme (NLP), bir sohbet bot gibi istemci uygulamanızın, doğal dil kullanarak kullanıcılarınızla çalışmasına izin verir. Kullanıcı tümce veya tümcecik girer. Kullanıcının metninde zayıf dilbilgisi, yazım ve noktalama olabilir. Bilişsel hizmet, Kullanıcı tümcesi aracılığıyla çalışarak sohbet bot 'ın kullanıcıya yardımcı olması için gereken bilgileri geri alabilir. 

## <a name="cognitive-services-with-nlp"></a>NLP ile bilişsel hizmetler

Language Understanding (LUSıS) ve Soru-Cevap Oluşturma NLP sağlar. İstemci uygulaması doğal dil metni gönderir. Hizmet metni alır, işler ve bir sonuç döndürür. 

## <a name="when-to-use-each-service"></a>Her hizmetin ne zaman kullanılacağı

Language Understanding (LUSıS) ve Soru-Cevap Oluşturma farklı sorunları çözüyor. LUO, bir kullanıcının metninin amacını (utterance olarak bilinir) belirler, Soru-Cevap Oluşturma Kullanıcı metninin (sorgu olarak bilinir) yanıtını belirler. 

Doğru hizmeti seçmek için, istemci uygulamasından gelen Kullanıcı metnini ve istemci uygulamanın bilişsel hizmetten alınması gereken bilgileri anlamanız gerekir.

Sohbet botunuzu `How do I get to the Human Resources building on the Seattle North campus?`metin alırsa, her bir hizmetin metinle nasıl çalıştığını anlamak için aşağıdaki grafiği kullanın.

|Hizmet|İstemci uygulaması şunları belirler|
|--|--|
|LUIS|**Kullanıcının** metin düzeyini belirler; hizmet soruya cevap vermez. Örneğin, bu metin `FindLocation` hedefi ile eşleşen olarak sınıflandırıldı.<br>|
|Soru-Cevap Oluşturucu|Özel bir bilgi bankasından **sorunun yanıtını döndürür** . Örneğin, bu metin `Get on the #9 bus and get off at Franklin street`statik metin yanıtı ile soru olarak belirlenir.|
|||

## <a name="when-do-you-use-luis"></a>Lune zaman kullanıyorsunuz? 

Sohbet botu içindeki bir işlemin parçası olarak söyleyici 'yi bilmeniz gerektiğinde LUO 'u kullanın. Örnek metin ile devam etmek `How do I get to the Human Resources building on the Seattle North campus?`, kullanıcının amacı bir konum bulmaktan haberdar olduktan sonra, yanıtı almak için bir taşıma sunucusu gibi başka bir hizmete söylenişi (varlıklarla kullanıma hazır) hakkındaki ayrıntıları geçirebilirsiniz. 

Amacı öğrenmek için LUO ve Soru-Cevap Oluşturma birleştirmeniz gerekmez. 

Bu söylemeye yönelik iki hizmeti, sohbet botunuzun, belirli statik metin yanıtını (Soru-Cevap Oluşturma kullanarak) bulmak için metinleri ve varlıkları temel alarak işlemesi gerekiyorsa kullanabilirsiniz.

## <a name="when-do-you-use-qna-maker"></a>Soru-Cevap Oluşturma ne zaman kullanıyorsunuz? 

Yanıt statik bilgi tabanınız varsa Soru-Cevap Oluşturma kullanın. Bu Bilgi Bankası, PDF 'ler ve URL 'Ler gibi belgelerle oluşturduğunuz gereksinimlerinize göre özel olarak tasarlanmıştır. 

Örnek utterance ile devam etmek `How do I get to the Human Resources building on the Seattle North campus?`, metni, yayımlanan Soru-Cevap Oluşturma hizmetinize bir sorgu olarak gönderin ve en iyi yanıtı alın. 

Sorunun yanıtını öğrenmek için LUO ve Soru-Cevap Oluşturma birleştirmeniz gerekmez.

Bu utterance için iki hizmeti birleştirebilirsiniz. sohbet botu, metinleri ve varlıkları temel alarak (LUSıS kullanarak) ve yanıtı bulur (Soru-Cevap Oluşturma kullanarak).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Bilgi tabanınız tamamlanmadığında her iki hizmeti kullanın

Soru-Cevap Oluşturma bilgi tabanınızı oluşturuyorsanız, ancak konu etki alanının değiştiğini biliyorsanız (zamanında bilgi gibi), LUVE Soru-Cevap Oluşturma hizmetlerini birleştirebilirsiniz. Bu, bilgi bankasındaki bilgileri kullanmanıza olanak sağlar, ancak Ayrıca bir kullanıcının amaç olduğunu anlamak için LUSıS 'yi de kullanabilirsiniz. İstemci uygulaması amaç olduktan sonra, başka bir kaynaktan ilgili bilgileri talep edebilir. 

İstemci uygulamanızın puanlar için LUVE Soru-Cevap Oluşturma yanıtlarını izlemesi gerekir. Soru-Cevap Oluşturma skoru rastgele bir eşiğin altındaysa, bu bilgileri bir üçüncü taraf hizmetine iletmek için LUSıS 'den döndürülen amaç ve varlık bilgilerini kullanın.

Örnek metin ile devam `How do I get to the Human Resources building on the Seattle North campus?`, Soru-Cevap Oluşturma düşük Güvenirlik puanı döndürdüğünü varsayalım. Bu bilgileri başka bir yanıt için bir eşleme veya arama hizmetine göndermek için, LUO, `FindLocation` ve `Human Resources building` ve `Seattle North campus`gibi ayıklanan varlıklardan döndürülen amacı kullanın. 

Bu üçüncü taraf yanıtını doğrulama için kullanıcıya sunabilirsiniz. Kullanıcının onayını aldıktan sonra, bilginizi büyütmek üzere bilgileri eklemek için Soru-Cevap Oluşturma 'e geri dönebilirsiniz. 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Sohbet bot 'ta daha fazla bilgi gerektiğinde her iki hizmeti de kullanın

Sohbet botunuzun hizmetin sağladığından daha fazla bilgiye ihtiyacı varsa, bir karar ağacına devam etmek için her iki hizmeti kullanın ve istemci uygulamasında her iki yanıtı da işleyin. 

Her iki hizmet ile birlikte çalışmak üzere bir işlem oluşturmaya yardımcı olmak için bot Framework **[DISPATCH CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** aracını kullanın. Bu araç, BASıS ve Soru-Cevap Oluşturma arasında dağıtım yapan bir amaç uygulaması oluşturur. 

Bu tür bir sohbet bot 'ı uygulamak için, bot Oluşturucu örneği [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) **olan NLP 'yi Dispatch**, in veya [Node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)ile birlikte kullanın. 

## <a name="best-practices"></a>En iyi uygulamalar

Her hizmet için en iyi yöntemleri uygulayın:

* [Lusıs](luis-concept-best-practices.md) en iyi uygulamaları
* En iyi [soru-cevap oluşturma](../qnamaker/concepts/best-practices.md) uygulamalar

## <a name="see-also"></a>Ayrıca bkz.

* [Language Understanding (LUIS)](what-is-luis.md)
* [Soru-Cevap Oluşturma](../qnamaker/overview/overview.md)
* [CLı dağıtma](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot Framework örnekleri](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot hizmeti](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure bot öykünücüsü](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot Framework Web sohbeti](https://github.com/microsoft/BotFramework-WebChat)