---
title: Bilişsel hizmetler ve Machine Learning
titleSuffix: Azure Cognitive Services
description: Azure Bilişsel Hizmetler’in, makine öğrenmesine yönelik diğer Azure teklifleriyle birlikte nasıl çalıştığını öğrenin.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 7946bc23f766eaf99860fa764ee7b542036b5109
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075789"
---
# <a name="cognitive-services-and-machine-learning"></a>Bilişsel Hizmetler ve makine öğrenmesi

Bilişsel hizmetler, nesnelerin veya yüzlerinin tanınması için metin analizi veya görüntüleri analiz etme gibi genel sorunları çözümlemek için makine öğrenimi özellikleri sağlar. Bu hizmetleri kullanmak için makine öğrenmesi veya veri bilimi alanında bilgi sahibi olmanıza gerek yoktur. 

Bilişsel [Hizmetler](./what-are-cognitive-services.md) , her biri farklı, genelleştirilmiş tahmin özelliklerini destekleyen bir hizmet grubudur. Hizmetler, doğru hizmeti bulmanıza yardımcı olmak için farklı kategorilere ayrılmıştır. 

|Hizmet kategorisi|Amaç|
|--|--|
|[Karar](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Bilgiye dayalı ve verimli kararlar almak için öneriler sunan uygulamalar derleyin.|
|[Dil](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Önceden oluşturulmuş betiklerle uygulamalarınızın doğal dil işlemesine, duyguları değerlendirmesine ve kullanıcıların ne istediğini tanımayı öğrenmesine imkan sağlayın.|
|[Ara](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Uygulamalarınıza Bing Arama API'lerini ekleyerek tek bir API çağrısıyla milyarlarca web sayfasını, görüntüyü, videoyu ve haberi tarayabilirsiniz.|
|[Konuşma](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Konuşmaları metne, metinleri kulağa doğal gelen konuşmalara dönüştürün. Bir dilden başka bir dile çeviri yapın, konuşmacı doğrulama ve tanıma özelliğini etkinleştirin.|
|[Görsel](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Resimlerinizi, videolarınızı ve dijital mürekkep içeriğinizi tanıyın, tanımlayın, altyazı ekleyin, dizine alın ve yönetin.|

Bilişsel hizmetler 'i şu durumlarda kullanın:

* Genelleştirilmiş bir çözüm kullanabilirler.
* Bir programlama REST API veya SDK 'dan çözüme erişin. 

Şu durumlarda başka bir makine öğrenimi çözümü kullanın:

* Algoritmayı seçmeniz ve çok özel verilerde eğitmeniz gerekir.

## <a name="what-is-machine-learning"></a>Makine öğrenmesi nedir?

Makine öğrenimi, belirli bir ihtiyacı çözümlemek için verileri ve algoritmayı birlikte getiren bir kavramdır. Veriler ve algoritma eğitilirken, çıktı farklı verilerle tekrar kullanabileceğiniz bir modeldir. Eğitilen model yeni verileri temel alan öngörüler sağlar. 

Machine Learning sistemi oluşturma işlemi, Machine Learning veya veri bilimi hakkında bazı bilgiler gerektirir.

Makine öğrenimi, [Azure Machine Learning (AML) ürünleri ve Hizmetleri](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context)kullanılarak sağlanır.

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

Tam eğitilen bir model sağlayan hizmetler _donuk bir kutu_ olarak kabul edilebilir. Bunların nasıl çalıştığını veya bunları eğitede hangi verilerin kullanıldığını bilmeniz gerekmez. Tahmin almak için verilerinizi tamamen eğitilen bir modele taşıyın. 

### <a name="some-or-all-training-data-required"></a>Eğitim verilerinin bazıları veya tümü gereklidir

Bazı hizmetler kendi verilerinizi getirip bir modeli eğmenize imkan tanır. Bu sayede, kendi verileriniz ile hizmetin verilerini ve algoritmasını kullanarak modeli genişletebilirsiniz. Çıktı, gereksinimlerinize göre eşleşir. Kendi verilerinizi getirdiğinizde, verileri hizmete özgü bir şekilde etiketlemeniz gerekebilir. Örneğin, çiçekleri tanımlamak için bir modeli eğitmeniz durumunda, her görüntüde çiçek konumunun yanı sıra modeli eğitme gibi çiçek görüntülerinin kataloğunu sağlayabilirsiniz. 

Bir hizmet kendi verilerini iyileştirmek için veri sağlamanıza _izin_ verebilir. Bir hizmet, veri sağlamanızı _gerektirebilir_ . 

### <a name="real-time-or-near-real-time-data-required"></a>Gerçek zamanlı veya neredeyse gerçek zamanlı veriler gerekiyor

Etkin bir model oluşturmak için bir hizmetin gerçek zamanlı veya neredeyse gerçek zamanlı verilere ihtiyacı olabilir. Bu hizmetler, model verilerini önemli miktarda işler. 

## <a name="service-requirements-for-the-data-model"></a>Veri modeli için hizmet gereksinimleri

Aşağıdaki veriler her hizmeti, izin verdiği veya gerektirdiği veri türüne göre kategorilere ayırır.

|Bilişsel hizmet|Eğitim verisi gerekli değil|Eğitim verilerinin bazılarını veya tümünü sağlarsınız|Gerçek zamanlı veya neredeyse gerçek zamanlı veri toplama|
|--|--|--|--|
|[Anomali Algılayıcısı](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Arama |x|||
|[Görüntü İşleme](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Özel Görüntü İşleme](./custom-vision-service/overview.md)||x||
|[Yüz Tanıma](./Face/Overview.md)|x|x||
|[Form Tanıma](./form-recognizer/overview.md)||x||
|[Tam Ekran Okuyucu](./immersive-reader/overview.md)|x|||
|[Mürekkep Tanıma](/previous-versions/azure/cognitive-services/Ink-Recognizer/overview)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Kişiselleştirme](./personalizer/what-is-personalizer.md)|sayı|sayı|x|
|[Soru-Cevap Oluşturma](./QnAMaker/Overview/overview.md)||x||
|[Konuşmacı tanıyıcı](./speech-service/speaker-recognition-overview.md)||x||
|[Konuşma metni okuma (TTS)](speech-service/text-to-speech.md)|x|x||
|[Konuşmayı metne dönüştürme (STT)](speech-service/speech-to-text.md)|x|x||
|[Konuşma Çevirisi](speech-service/speech-translation.md)|x|||
|[Metin Analizi](./text-analytics/overview.md)|x|||
|[Translator](./translator/translator-info-overview.md)|x|||
|[Translator-özel çevirici](./translator/custom-translator/overview.md)||x||

* Kişiselleştirici, ilkenizi ve verilerinizi değerlendirmek için yalnızca hizmet tarafından toplanan eğitim verilerine (gerçek zamanlı olarak çalışırken) ihtiyaç duyuyor. Kişiselleştirici, ön veya Batch eğitimi için büyük geçmiş veri kümelerine gerek duyuyor. 

## <a name="where-can-you-use-cognitive-services"></a>Bilişsel Hizmetleri nerede kullanabilirsiniz?
 
Hizmetler, REST API veya SDK çağrısı yapan tüm uygulamalar için kullanılır. Uygulama örnekleri arasında Web siteleri, botlar, sanal veya karma gerçeklik, masaüstü ve mobil uygulamalar sayılabilir. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure Bilişsel Arama bilişsel hizmetlerle nasıl ilişkilidir?

[Azure bilişsel arama](../search/search-what-is-azure-search.md) , isteğe bağlı olarak, dizin oluşturma iş yükleri için görüntü ve doğal dil işleme eklemek üzere bilişsel hizmetler kullanan ayrı bir bulut arama hizmetidir. Bilişsel hizmetler, tek tek API 'Leri çevreleyecek [yerleşik yetenekler](../search/cognitive-search-predefined-skills.md) aracılığıyla Azure bilişsel arama kullanıma sunulur. İzlenecek yollar için ücretsiz bir kaynak kullanabilirsiniz, ancak daha büyük birimler için [faturalanabilir bir kaynak](../search/cognitive-search-attach-cognitive-services.md) oluşturma ve ekleme planlaması gerçekleştirebilirsiniz.

## <a name="how-can-you-use-cognitive-services"></a>Bilişsel hizmetleri nasıl kullanabilirsiniz?

Her hizmet, verileriniz hakkında bilgi sağlar. Konuşmayı (ses) metne dönüştürme, metni birçok dile çevirme ve ardından bir bilgi tabanından yanıt almak için çevrilmiş dilleri kullanma gibi çözümleri zincirlemek üzere Hizmetleri birlikte birleştirebilirsiniz. Bilişsel hizmetler, kendi kendilerine akıllı çözümler oluşturmak için kullanılabilir olsa da, modelleri desteklemek veya geliştirme sürecini hızlandırmak için geleneksel makine öğrenimi projeleriyle de birleştirilebilir. 

Diğer makine öğrenimi araçları için aktarılmış modeller sağlayan bilişsel hizmetler:

|Bilişsel hizmet|Model bilgileri|
|--|--|
|[Özel Görüntü İşleme](./custom-vision-service/overview.md)|Android için TensorFlow için [dışarı aktarma](./custom-vision-service/export-model-python.md) , IOS11 Için Coreml, Windows ml IÇIN onnx|

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Mimari Kılavuzu-Microsoft 'ta makine öğrenimi ürünleri nelerdir?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Machine Learning-derin öğrenme ve makine öğrenimine giriş](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Sonraki adımlar

* Bilişsel hizmet hesabınızı [Azure Portal](cognitive-services-apis-create-account.md) veya [Azure CLI](./cognitive-services-apis-create-account-cli.md)ile oluşturun.
* Bilişsel bir hizmette [kimlik doğrulaması](authentication.md) yapmayı öğrenin.
* Sorun tanımlama ve hata ayıklama için [Tanılama günlüğünü](diagnostic-logging.md) kullanın. 
* Bir Docker [kapsayıcısında](cognitive-services-container-support.md)bilişsel bir hizmet dağıtın.
* [Hizmet güncelleştirmeleriyle](https://azure.microsoft.com/updates/?product=cognitive-services)güncel tutun.
