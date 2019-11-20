---
title: Bilişsel hizmetler ve Machine Learning
titleSuffix: Azure Cognitive Services
description: Azure Bilişsel Hizmetler’in, makine öğrenmesine yönelik diğer Azure teklifleriyle birlikte nasıl çalıştığını öğrenin.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: 48bac68b518706cf3a23d2283da7cda49846e0a7
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795825"
---
# <a name="cognitive-services-and-machine-learning"></a>Bilişsel Hizmetler ve makine öğrenmesi

Bilişsel hizmetler, nesnelerin veya yüzlerinin tanınması için metin analizi veya görüntüleri analiz etme gibi genel sorunları çözümlemek için makine öğrenimi özellikleri sağlar. Bu hizmetleri kullanmak için özel makine öğrenimine veya veri bilimi bilgisine ihtiyacınız yoktur. 

Bilişsel [Hizmetler](welcome.md) , her biri farklı, genelleştirilmiş tahmin özelliklerini destekleyen bir hizmet grubudur. Hizmetler, doğru hizmeti bulmanıza yardımcı olmak için farklı kategorilere ayrılmıştır. 

|Hizmet kategorisi|Amaç|
|--|--|
|[Seçim](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Bilgiye dayalı ve verimli kararlar almak için öneriler sunan uygulamalar derleyin.|
|[Dil](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Önceden oluşturulmuş betiklerle uygulamalarınızın doğal dil işlemesine, duyguları değerlendirmesine ve kullanıcıların ne istediğini tanımayı öğrenmesine imkan sağlayın.|
|[Search](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Uygulamalarınıza Bing Arama API'leri ekleyin ve tek bir API çağrısıyla milyarlarca web sayfası, resim, video ve haberi tarama imkanından yararlanın.|
|[Konuşmayı](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Konuşmaları metne, metinleri kulağa doğal gelen konuşmalara dönüştürün. Bir dilden başka bir dile çeviri yapın, konuşmacı doğrulama ve tanıma özelliğini etkinleştirin.|
|[Minin](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Resimlerinizi, videolarınızı ve dijital mürekkep içeriğinizi tanıyın, tanımlayın, altyazı ekleyin, dizine alın ve yönetin.|
||||

Bilişsel hizmetler 'i şu durumlarda kullanın:

* Genelleştirilmiş bir çözüm kullanabilirler.
* Bir programlama REST API veya SDK 'dan çözüme erişin. 

Şu durumlarda başka bir makine öğrenimi çözümü kullanın:

* Algoritmayı seçmeniz ve çok özel verilerde eğitmeniz gerekir.

## <a name="what-is-machine-learning"></a>Makine öğrenimi nedir?

Makine öğrenimi, belirli bir ihtiyacı çözümlemek için verileri ve algoritmayı birlikte getiren bir kavramdır. Veriler ve algoritma eğitilirken, çıktı farklı verilerle tekrar kullanabileceğiniz bir modeldir. Eğitilen model yeni verileri temel alan öngörüler sağlar. 

Machine Learning sistemi oluşturma işlemi, Machine Learning veya veri bilimi hakkında bazı bilgiler gerektirir.

Makine öğrenimi, [Azure Machine Learning (AML) ürünleri ve Hizmetleri](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)kullanılarak sağlanır.

## <a name="what-is-a-cognitive-service"></a>Bilişsel hizmet nedir?

Bilişsel hizmet, bir makine öğrenimi çözümünde bileşenlerin bir kısmını veya tümünü sağlar: veri, algoritma ve eğitilen model. Bu hizmetler, makine öğrenimi veya veri bilimi konusunda deneyim sahibi olmadan verileriniz hakkında genel bilgi gerektirmek üzere tasarlanmıştır. Bu hizmetler hem REST API hem de dil tabanlı SDK 'lar sağlar. Sonuç olarak, hizmetleri kullanmak için programlama dili bilgisine sahip olmanız gerekir.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Bilişsel hizmetler ve Azure Machine Learning (AML) nasıl benzerdir?

Her ikisi de iş işlemlerini geliştirmek üzere yapay zeka (AI) uygulamanın son hedefine sahiptir, ancak her birinin ilgili tekliflerde bunu sağladığı her bir farklılık farklıdır. 

Genellikle, izleyiciler farklıdır:

* Bilişsel hizmetler, makine öğrenimi deneyimi olmayan geliştiriciler içindir.
* Azure Machine Learning, veri bilimcileri için tasarlanmıştır. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Bilişsel hizmet, Machine Learning 'ten farklı midir?

Bilişsel hizmet, sizin için eğitilen bir model sağlar. Bu, verileri ve algoritmayı bir REST API birlikte ve bir SDK 'dan kullanılabilir hale getirir. Senaryonuza bağlı olarak bu hizmeti dakikalar içinde de uygulayabilirsiniz.  Bilişsel hizmet, resimlerdeki metin veya öğe tanımlamalarında anahtar ifadeler gibi genel sorunlara yanıt verir. 

Machine Learning, genellikle başarıyla uygulanması için daha uzun bir süre gerektiren bir işlemdir. Bu süre, bir bilişsel hizmet tarafından sunulan aynı işlevselliğe ulaşmak için veri toplama, Temizleme, dönüştürme, algoritma seçimi, model eğitimi ve dağıtım üzerinde harcanmıştır. Machine Learning sayesinde, yüksek düzeyde özelleştirilmiş ve/veya belirli sorunlara yanıt vermek mümkündür. Machine Learning sorunları, sorunun önemli ve verilerin yanı sıra veri bilimi 'nin uzmanlığı ile ilgili konular hakkında bilgi sahibi olmanızı gerektirir.

## <a name="what-kind-of-data-do-you-have"></a>Ne tür veriler kullanıyorsunuz?

Bilişsel hizmetler, bir hizmet grubu olarak, eğitilen model için hiçbiri, bazıları veya tüm özel verileri gerektirebilir. 

### <a name="no-additional-training-data-required"></a>Ek eğitim verileri gerekmez

Tam eğitilen bir model sağlayan hizmetler _siyah kutu_olarak kabul edilebilir. Bunların nasıl çalıştığını veya bunları eğitede hangi verilerin kullanıldığını bilmeniz gerekmez. Tahmin almak için verilerinizi tamamen eğitilen bir modele taşıyın. 

### <a name="some-or-all-training-data-required"></a>Eğitim verilerinin bazıları veya tümü gereklidir

Bazı hizmetler kendi verilerinizi getirip bir modeli eğmenize imkan tanır. Bu sayede, kendi verileriniz ile hizmetin verilerini ve algoritmasını kullanarak modeli genişletebilirsiniz. Çıktı, gereksinimlerinize göre eşleşir. Kendi verilerinizi getirdiğinizde, verileri hizmete özgü bir şekilde etiketlemeniz gerekebilir. Örneğin, çiçekleri tanımlamak için bir modeli eğitmeniz durumunda, her görüntüde çiçek konumunun yanı sıra modeli eğitme gibi çiçek görüntülerinin kataloğunu sağlayabilirsiniz. 

Bir hizmet kendi verilerini iyileştirmek için veri sağlamanıza _izin_ verebilir. Bir hizmet, veri sağlamanızı _gerektirebilir_ . 

### <a name="real-time-or-near-real-time-data-required"></a>Gerçek zamanlı veya neredeyse gerçek zamanlı veriler gerekiyor

Etkin bir model oluşturmak için bir hizmetin gerçek zamanlı veya neredeyse gerçek zamanlı verilere ihtiyacı olabilir. Bu hizmetler, model verilerini önemli miktarda işler. 

## <a name="service-requirements-for-the-data-model"></a>Veri modeli için hizmet gereksinimleri

Aşağıdaki veriler her hizmeti, izin verdiği veya gerektirdiği veri türüne göre kategorilere ayırır.

|Bilişsel hizmet|Eğitim verisi gerekli değil|Eğitim verilerinin bazılarını veya tümünü sağlarsınız|Gerçek zamanlı veya neredeyse gerçek zamanlı veri toplama|
|--|--|--|--|
|[Anomali algılayıcısı](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Arama |x|||
|[Görüntü İşleme](./Computer-vision/Home.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Özel Görüntü İşleme](./Custom-Vision-Service/home.md)||x||
|[Ünüzü](./Face/Overview.md)|x|x||
|[Form tanıyıcı](./form-recognizer/overview.md)||x||
|[Modern okuyucu](./immersive-reader/overview.md)|x|||
|[Mürekkep tanıyıcı](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Kişiselleştirici](./personalizer/what-is-personalizer.md)|sayı|sayı|x|
|[Soru-Cevap Oluşturma](./QnAMaker/Overview/overview.md)||x||
|[Konuşmacı tanıyıcı](./speaker-recognition/home.md)||x||
|[Konuşma metni okuma (TTS)](speech-service/text-to-speech.md)|x|x||
|[Konuşmayı metne dönüştürme (STT)](speech-service/speech-to-text.md)|x|x||
|[Konuşma Çevirisi](speech-service/speech-translation.md)|x|||
|[Metin Analizi](./text-analytics/overview.md)|x|||
|[Translator Metin Çevirisi](./translator/translator-info-overview.md)|x|||
|[Translator Metin Çevirisi-özel çevirici](./translator/custom-translator/overview.md)||x||

\* Kişiselleştirici, ilkenizi ve verilerinizi değerlendirmek için yalnızca hizmet tarafından toplanan eğitim verilerine (gerçek zamanlı olarak çalışırken) ihtiyaç duyuyor. Kişiselleştirici, ön veya Batch eğitimi için büyük geçmiş veri kümelerine gerek duyuyor. 

## <a name="where-can-you-use-cognitive-services"></a>Bilişsel Hizmetleri nerede kullanabilirsiniz?
 
Hizmetler, REST API veya SDK çağrısı yapan tüm uygulamalar için kullanılır. Uygulama örnekleri arasında Web siteleri, botlar, sanal veya karma gerçeklik, masaüstü ve mobil uygulamalar sayılabilir. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure Bilişsel Arama bilişsel hizmetlerle nasıl ilişkilidir?

[Azure bilişsel arama](../search/search-what-is-azure-search.md) , isteğe bağlı olarak, dizin oluşturma iş yükleri için görüntü ve doğal dil işleme eklemek üzere bilişsel hizmetler kullanan ayrı bir bulut arama hizmetidir. Bilişsel hizmetler, tek tek API 'Leri çevreleyecek [yerleşik yetenekler](../search/cognitive-search-predefined-skills.md) aracılığıyla Azure bilişsel arama kullanıma sunulur. İzlenecek yollar için ücretsiz bir kaynak kullanabilirsiniz, ancak daha büyük birimler için [faturalanabilir bir kaynak](../search/cognitive-search-attach-cognitive-services.md) oluşturma ve ekleme planlaması gerçekleştirebilirsiniz.

## <a name="how-can-you-use-cognitive-services"></a>Bilişsel hizmetleri nasıl kullanabilirsiniz?

Her hizmet, verileriniz hakkında bilgi sağlar. Konuşmayı (ses) metne dönüştürme, metni birçok dile çevirme ve ardından bir bilgi tabanından yanıt almak için çevrilmiş dilleri kullanma gibi çözümleri zincirlemek üzere Hizmetleri birlikte birleştirebilirsiniz. Bilişsel hizmetler, kendi kendilerine akıllı çözümler oluşturmak için kullanılabilir olsa da, modelleri desteklemek veya geliştirme sürecini hızlandırmak için geleneksel makine öğrenimi projeleriyle de birleştirilebilir. 

Diğer makine öğrenimi araçları için aktarılmış modeller sağlayan bilişsel hizmetler:

|Bilişsel hizmet|Model bilgileri|
|--|--|
|[Özel Görüntü İşleme](./custom-vision-service/home.md)|Android için TensorFlow için [dışarı aktarma](./Custom-Vision-Service/export-model-python.md) , IOS11 Için Coreml, Windows ml IÇIN onnx|

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Mimari Kılavuzu-Microsoft 'ta makine öğrenimi ürünleri nelerdir?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Machine Learning-derin öğrenme ve makine öğrenimine giriş](../machine-learning/service/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Sonraki adımlar

* Bilişsel hizmet hesabınızı [Azure Portal](cognitive-services-apis-create-account.md) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)ile oluşturun.
* Bilişsel bir hizmette [kimlik doğrulaması](authentication.md) yapmayı öğrenin.
* Sorun tanımlama ve hata ayıklama için [Tanılama günlüğünü](diagnostic-logging.md) kullanın. 
* Bir Docker [kapsayıcısında](cognitive-services-container-support.md)bilişsel bir hizmet dağıtın.
* [Hizmet güncelleştirmeleriyle](https://azure.microsoft.com/updates/?product=cognitive-services)güncel tutun.
