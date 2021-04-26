---
title: Language Understanding (LUA) genel bakış
description: Language Understanding (LUSıS)-, anlam tahmin etmek ve bilgi ayıklamak için, doğal dilde makine öğrenimini kullanan bulut tabanlı bir API hizmetidir.
keywords: Azure, yapay zeka, AI, doğal dil işleme, NLP, doğal dil anlama, NLU, LUO, konuşma, AI sohbet botu, NLP AI, Azure LUL
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/13/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f46586b3f120cf191d88b7de9cf8686ca9b16cca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503781"
---
# <a name="what-is-language-understanding-luis"></a>Language Understanding (LUIS) nedir?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUSıS), bir kullanıcının konuşma, genel anlamı tahmin etmek için doğal dil metnine özel makine öğrenimi zekası uygulayan ve ilgili ve ayrıntılı bilgileri kullanıma sunan bulut tabanlı bir konuşma hizmetidir. LUıS, [özel portalı](https://www.luis.ai), [API 'ler][endpoint-apis] ve [SDK istemci kitaplıkları](client-libraries-rest-api.md)aracılığıyla erişim sağlar.

İlk kullanıcı için, bu adımları izleyerek [BASIS portalında](sign-in-luis-portal.md "Lug portalında oturum açma") çalışmaya BAŞLAYıN, BASIS [önceden oluşturulmuş etki alanı](luis-get-started-create-app.md) uygulamalarını deneyebilir veya [uygulamanızı](get-started-portal-build-app.md)oluşturabilirsiniz.

Bu belge aşağıdaki makale türlerini içerir:  

* [**Hızlı**](luis-get-started-create-app.md) başlangıçlarda, hizmette istek yapma konusunda size kılavuzluk eden başlangıç yönergeleri bulunur.  
* [**Nasıl yapılır kılavuzlarında**](luis-how-to-start-new-app.md) , hizmetin daha belirli veya özelleştirilmiş yollarla kullanılmasına ilişkin yönergeler bulunur.  
* [**Kavramlar**](artificial-intelligence.md) , hizmet işlevselliği ve özelliklerinin ayrıntılı açıklamalarını sağlar.  
* [**Öğreticiler**](tutorial-intents-only.md) daha fazla iş çözümlerinde hizmeti bir bileşen olarak nasıl kullanacağınızı gösteren kılavuzlardır.  

## <a name="what-does-luis-offer"></a>Lune sunuyor? 

* **Basitlik**: lusıs, ŞIRKET içi AI uzmanlığı veya önceki bir makine öğrenimi bilgisi gereksiniminden sizin için bir yük devreder. Yalnızca birkaç tıklamayla, kendi konuşma AI uygulamanızı oluşturabilirsiniz. [Hızlı](get-started-portal-build-app.md)başlangıçlarımızın birini izleyerek özel uygulamanızı oluşturabilir veya [önceden oluşturulmuş etki alanı](luis-get-started-create-app.md) uygulamalarımızın birini kullanabilirsiniz.
* **Güvenlik, gizlilik ve uyumluluk**: Azure altyapısı tarafından desteklenen lusıs, kurumsal düzeyde güvenlik, gizlilik ve uyumluluk sunar. Verileriniz sizinki kalır; verilerinizi dilediğiniz zaman silebilirsiniz. Verileriniz depolama sırasında şifrelenir. Bunun hakkında daha fazla bilgi için [burayı](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy) okuyun.
* **Tümleştirme**: Lusıs uygulamanızı [Microsoft Bot Framework](https://docs.microsoft.com/composer/tutorial/tutorial-luis), [soru-cevap oluşturma](../QnAMaker/choose-natural-language-processing-service.md)ve [konuşma hizmeti](../Speech-Service/quickstarts/intent-recognition.md)gibi diğer Microsoft hizmetleriyle kolayca tümleştirin.


## <a name="luis-scenarios"></a>LUSıS senaryoları
* [Kurumsal düzeyde konuşma bot oluştur](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot): Bu başvuru mimarisi, Azure bot Framework kullanılarak kurumsal düzeyde bir konuşma bot (sohbet botu) oluşturmayı açıklar.
* [Commerce sohbet botu](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot): birlikte Azure bot hizmeti ve Language Understanding hizmeti, geliştiricilerin bankacılık, seyahat ve eğlence gibi çeşitli senaryolar için konuşma arabirimleri oluşturmalarına olanak tanır.
* [Bir ses Yardımcısı kullanarak IoT cihazlarını denetleme](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant): bağlı televizyonunuzdaki veya Cuma 'ya bağlı bir güç tesisinin cihazlarından internet erişimli cihazlarınızla sorunsuz konuşma arabirimleri oluşturun.


## <a name="application-development-life-cycle"></a>Uygulama geliştirme yaşam döngüsü

![LUSıS uygulama geliştirme yaşam döngüsü](./media/luis-overview/luis-dev-lifecycle.png "LUSıS uygulaması geliştirme yaşam döngüsü")

-   **Plan**: Kullanıcıların uygulamanızı kullanabileceği senaryoları belirler. Tanınması gereken eylemleri ve ilgili bilgileri tanımlayın.
-   **Oluşturma**: uygulamanızı geliştirmek için Authoring Resource kullanın. [Hedefleri](luis-concept-intent.md) ve [varlıkları](luis-concept-entity-types.md)tanımlayarak başlayın. Ardından, her amaç [için eğitim](luis-concept-utterance.md) elde edin. 
-   **Test ve geliştirme**: uygulamanın nasıl davrandığını bir fikir sahibi olmak için modelinizin diğer deneyimlerle test edilmesine başlayın ve herhangi bir geliştirme gerekli olup olmadığına karar verebilirsiniz. Uygulamanızı, bu [en iyi yöntemleri](luis-concept-best-practices.md)izleyerek geliştirebilirsiniz. 
-   **Yayımla**: uygulamanızı tahmin için dağıtın ve tahmin kaynağını kullanarak uç noktayı sorgulayın. [Burada](luis-how-to-azure-subscription.md#luis-resources)yazma ve tahmin kaynakları hakkında daha fazla bilgi edinin. 
-   **Connect**: [Microsoft Bot Framework](https://docs.microsoft.com/composer/tutorial/tutorial-luis), [soru-cevap oluşturma](../QnAMaker/choose-natural-language-processing-service.md)ve [konuşma hizmeti](../Speech-Service/quickstarts/intent-recognition.md)gibi diğer hizmetlere bağlanın. 
-   **İyileştir**: gerçek yaşam örnekleriyle uygulamanızı iyileştirmek için [uç nokta balonları gözden geçirin](luis-concept-review-endpoint-utterances.md)

Uygulamanızı planlama ve oluşturma hakkında daha fazla bilgi [edinin.](luis-how-plan-your-app.md)

## <a name="next-steps"></a>Sonraki adımlar

* Hizmet ve [belge yenilikleri](whats-new.md "Yenilikler")
* [LUIS uygulaması oluşturma](tutorial-intents-only.md)
* [API başvurusu][endpoint-apis]
* [En iyi uygulamalar](luis-concept-best-practices.md)
* LUSıS için [Geliştirici kaynakları](developer-reference-resource.md "Geliştirici kaynakları") .
* [Hedefleri](luis-concept-intent.md "hedefleri") ve [varlıkları](luis-concept-entity-types.md "varlıklar")kullanarak [Uygulamanızı planlayın](luis-how-plan-your-app.md "Uygulamanızı planlama") .

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
