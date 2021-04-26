---
title: Azure bilişsel hizmetler geliştirme seçenekleri
description: Azure bilişsel hizmetler 'i istemci kitaplıkları, REST API 'Ler, Logic Apps, güç otomatikleştirme, Azure Işlevleri, Azure App Service, Azure Databricks ve çok daha fazlası gibi farklı geliştirme ve dağıtım seçenekleriyle nasıl kullanacağınızı öğrenin.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 975f7eec31839aefcb1782f573d0210df29a4d00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98873578"
---
# <a name="cognitive-services-development-options"></a>Bilişsel Hizmetler dağıtım seçenekleri

Bu belge, Azure bilişsel hizmetler 'i kullanmaya başlamanıza yardımcı olmak için geliştirme ve Dağıtım seçeneklerine yönelik yüksek düzeyde bir genel bakış sağlar.

Azure bilişsel hizmetler, geliştiricilerin, makine öğrenimine gerek kalmadan uygulamalarına ve ürünlerine zeka oluşturmalarına olanak tanıyan bulut tabanlı AI hizmetlerdir. Bilişsel hizmetler sayesinde, Microsoft tarafından uygulamanızda kullanılmak üzere hazırlanan, eğitilen ve güncellenen AI özelliklerine veya modellerine erişebilirsiniz. Birçok durumda, iş gereksinimleriniz için modelleri özelleştirme seçeneğiniz de vardır. 

Bilişsel hizmetler dört kategoride düzenlenmiştir: karar, dil, konuşma ve vizyon. Genellikle bu hizmetlere REST API 'Leri, istemci kitaplıkları ve Microsoft tarafından sunulan özel araçlar (komut satırı arabirimleri gibi) üzerinden erişirsiniz. Ancak, bu başarılı için yalnızca bir yoldur. Azure aracılığıyla ayrıca çeşitli geliştirme seçeneklerine erişebilirsiniz:

* Logic Apps ve güç otomatikleştirme gibi Otomasyon ve tümleştirme araçları.
* Azure Işlevleri ve App Service gibi dağıtım seçenekleri. 
* Güvenli erişim için bilişsel hizmetler Docker kapsayıcıları.
* Büyük veri senaryoları için Apache Spark, Azure Databricks, Azure SYNAPSE Analytics ve Azure Kubernetes hizmeti gibi araçlar. 

' İ atlamadan önce bilişsel hizmetlerin birincil olarak iki ayrı görev için kullanıldığını bilmeniz önemlidir. Gerçekleştirmek istediğiniz göreve bağlı olarak, aralarından seçim yapabileceğiniz farklı geliştirme ve dağıtım seçenekleriniz vardır. 

* [Tahmin ve analiz için geliştirme seçenekleri](#development-options-for-prediction-and-analysis)
* [Modelleri özelleştirmeye ve yapılandırmaya yönelik araçlar](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Tahmin ve analiz için geliştirme seçenekleri 

Modelleri özelleştirmek ve yapılandırmak için kullanacağınız araçlar, bilişsel hizmetler 'i çağırmak için kullanabileenlerden farklıdır. Çoğu bilişsel hizmetler, tüm özelleştirmeler olmadan veri göndermenizi ve Öngörüler almanızı sağlar. Örnek: 

* Sözcükleri ve tümceleri algılamak veya çerçevedeki kişilerin sayısını saymak için Görüntü İşleme hizmetine görüntü gönderebilirsiniz
* Konuşma hizmetine bir ses dosyası gönderebilir ve sonra da konuşmayı aynı anda metne çevirebilirsiniz
* Form tanıyıcı hizmetine bir PDF gönderebilir ve bu hücrelerin içindeki tabloları, hücreleri ve metni algılayabilir ve koordinatlara ve ayrıntılara sahip bir JSON çıkışı alırsınız

Azure, birçok farklı Kullanıcı türü için tasarlanan ve bilişsel hizmetler ile kullanılabilen çok çeşitli araçlar sunar. Tasarımcı odaklı araçlar, kullanımı en kolayıdır ve hızlı bir şekilde ayarlanır ve otomatikleştirilmesi, ancak özelleştirmeye geldiğinde sınırlamalar olabilir. REST API 'lerimiz ve istemci kitaplıklarımız kullanıcılara daha fazla denetim ve esneklik sağlar, ancak bir çözüm oluşturmak için daha fazla çaba, zaman ve uzmanlık gerektirir. REST API 'Leri ve istemci kitaplıklarını kullanıyorsanız, C#, Java, Python, JavaScript veya başka bir popüler programlama dili gibi modern programlama dilleri ile çalışmanız için rahat bir beklentisi vardır. 

Bilişsel hizmetlerle çalışabilmeniz için kullanabileceğiniz farklı yöntemlere göz atalım.

### <a name="client-libraries-and-rest-apis"></a>İstemci kitaplıkları ve REST API’leri

Bilişsel hizmetler istemci kitaplıkları ve REST API 'Leri hizmetinize doğrudan erişim sağlar. Bu araçlar bilişsel hizmetler 'e, taban çizgisi modellerine programlı erişim sağlar ve birçok durumda, modellerinizi ve çözümlerinizi programlı bir şekilde özelleştirmenize olanak tanır. 

* **Hedef Kullanıcı (ler)**: geliştiriciler ve veri bilimcileri
* **Avantajlar**: Hizmetleri herhangi bir dil ve ortamdan çağırmak için en büyük esnekliği sağlar. 
* **UI**: yok-yalnızca kod
* **Abonelik (ler)**: Azure hesabı + bilişsel hizmetler kaynakları

Kullanılabilir istemci kitaplıkları ve REST API 'Ler hakkında daha fazla bilgi edinmek istiyorsanız, bilişsel [Hizmetler genel bakımızı](index.yml) kullanarak çekme, karar verme, dil ve konuşmaya yönelik hızlı başlangıçlardan birini kullanmaya başlayın.

### <a name="cognitive-services-for-big-data"></a>Büyük Veri için Bilişsel Hizmetler

Büyük veriler için bilişsel hizmetler sayesinde, akıllı modellerinizi doğrudan Apache Spark &trade; ve SQL hesaplamaları içine ekleyebilirsiniz. Bu araçlar, geliştiricilerin akıllı ve dağıtılmış uygulamalar oluşturmaya odaklanabilmeleri için düşük düzeyli ağ ayrıntılarından kurtulmasını sağlayabilir. Büyük veriler için bilişsel hizmetler aşağıdaki platformları ve bağlayıcıları destekler: Azure Databricks, Azure SYNAPSE, Azure Kubernetes hizmeti ve veri bağlayıcıları.

* **Hedef Kullanıcı**: veri bilimcileri ve veri mühendisleri
* **Avantajlar**: büyük veriler için Azure bilişsel hizmetler, kullanıcıların bilişsel hizmetler aracılığıyla Apache Spark kullanarak veri sanallaştırmasını sağlar &trade; . Herhangi bir veri deposu ile büyük ölçekli akıllı uygulamalar oluşturmak kolaydır.
* **UI**: yok-yalnızca kod
* **Abonelik (ler)**: Azure hesabı + bilişsel hizmetler kaynakları

Bilişsel hizmetler için büyük veriler hakkında daha fazla bilgi edinmek istiyorsanız, başlamak için iyi bir yere [genel bakış sunulmaktadır](./big-data/cognitive-services-for-big-data.md). Oluşturmaya başlamaya hazırsanız [Python](./big-data/samples-python.md) veya [Scala](./big-data/samples-scala.md) örneklerimizi deneyin.

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Işlevleri ve Azure hizmeti Web Işleri

[Azure işlevleri](../azure-functions/index.yml) ve [Azure App Service Web işleri](../app-service/index.yml) , geliştiriciler Için tasarlanan ve [Azure Uygulama Hizmetleri](../app-service/index.yml)üzerine inşa edilen kod ilk Tümleştirme Hizmetleri sağlar. Bu ürünler, kod yazmak için sunucusuz altyapı sağlar. Bu kod içinde, istemci kitaplıklarımızı ve REST API 'lerimizi kullanarak hizmetlerimize çağrı yapabilirsiniz. 

* **Hedef Kullanıcı (ler)**: geliştiriciler ve veri bilimcileri
* **Avantajlar**: olay tarafından tetiklenen kodu çalıştırmanıza olanak sağlayan sunucusuz işlem hizmeti. 
* **UI**: Evet
* **Abonelik (ler)**: Azure hesabı + bilişsel hizmetler kaynağı + Azure işlevleri aboneliği

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml) aynı iş akışı tasarımcısını ve bağlayıcıları Power otomatikleştir olarak paylaşmak, ancak Visual Studio ve DevOps ile tümleştirmeler dahil daha gelişmiş ve denetim sağlar. Güç otomatikleştirme, API 'Lerde bir ara sunucu veya sarmalayıcı sağlayan hizmete özel bağlayıcılar aracılığıyla bilişsel hizmetler kaynaklarınızla tümleştirmeyi kolaylaştırır. Bunlar, güç otomatikleştirmede kullanılabilen bağlayıcılardır. 

* **Hedef Kullanıcı**: geliştiriciler, TÜMLEŞTIRICILER, BT uzmanları, DevOps
* **Avantajlar**: bir düşük kod çözümünde gelişmiş seçenekler ve tümleştirme sağlayan tasarımcı-ilk (bildirim temelli) geliştirme modeli
* **UI**: Evet
* **Abonelikler**: Azure hesabı + bilişsel hizmetler kaynağı + Logic Apps dağıtımı

### <a name="power-automate"></a>Power Automate 

Güç otomatikleştirin, kod yazmadan uygulamalar ve hizmetler arasında otomatik iş akışları oluşturmanıza yardımcı olan bir hizmet olan güç [platformudur](/power-platform/) . Bir Power otomatikleştir çözümünde bilişsel hizmetler kaynağınız ile etkileşim kurmayı kolaylaştırmak için birkaç bağlayıcı sunuyoruz. Güç otomatikleştirme, Logic Apps üzerine kurulmuştur. 

* **Hedef Kullanıcı (ler**): iş kullanıcıları (analistleri) ve SharePoint yöneticileri
* **Avantajlar**: fare tıklamalarını kaydederek yinelenen el ile gerçekleştirilen görevleri otomatikleştirin, masaüstünüzdeki tuşları ve kopyalama adımları yapıştırın!
* **UI araçları**: Evet-yalnızca kullanıcı arabirimi
* **Abonelik (ler)**: Azure hesabı + bilişsel hizmetler kaynağı + Power otomatikleştir aboneliği + Office 365 aboneliği

### <a name="ai-builder"></a>AI Builder 

[AI Oluşturucu](/ai-builder/overview) , işleri otomatikleştirerek ve sonuçları tahmin ederek iş performansını geliştirmek için kullanabileceğiniz bir Microsoft güç platformu özelliğidir. AI Oluşturucu, bir nokta tıklama deneyimi aracılığıyla AI 'nin gücünü çözümlerinize getirir. Form tanıyıcı, Metin Analizi ve Görüntü İşleme gibi birçok bilişsel hizmet, burada doğrudan tümleşiktir ve kendi bilişsel hizmetlerinizi oluşturmanız gerekmez. 

* **Hedef Kullanıcı (ler**): iş kullanıcıları (analistleri) ve SharePoint yöneticileri
* **Avantajlar**: bir nokta ve tıklama DENEYIMI aracılığıyla AI 'nin gücünü getiren bir anahtar oluşturma çözümüdür. Kodlama veya veri bilimi becerileri gerekmez.
* **UI araçları**: Evet-yalnızca kullanıcı arabirimi
* **Abonelik (ler)**: AI Oluşturucu

### <a name="continuous-integration-and-deployment"></a>Sürekli tümleştirme ve dağıtım

Dağıtımlarınızı yönetmek için Azure DevOps ve GitHub eylemlerini kullanabilirsiniz. [Aşağıdaki bölümde](#continuous-integration-and-delivery-with-devops-and-github-actions) , konuşma ve Language UNDERSTANDING (Luu) hizmeti için özel modeller eğitmek ve DAĞıTMAK üzere CI/CD tümleştirmelerine yönelik iki örnek vardır. 

* **Hedef Kullanıcı**: geliştiriciler, veri bilimcileri ve veri mühendisleri
* **Avantajlar**: uygulamaları ve modelleri program aracılığıyla sürekli olarak ayarlamanıza, güncelleştirmenize ve dağıtmanıza olanak tanır. Verileri düzenli olarak kullanarak konuşma, görme, dil ve kararların modellerini geliştirme ve güncelleştirme için önemli bir avantaj vardır. 
* **UI araçları**: yok-yalnızca kod 
* **Abonelik (ler)**: Azure hesabı + bilişsel hizmetler kaynağı + GitHub hesabı

## <a name="tools-to-customize-and-configure-models"></a>Modelleri özelleştirmeye ve yapılandırmaya yönelik araçlar

Bilişsel hizmetlerle bir uygulama veya iş akışı oluşturan yolculukta ilerlemeniz sayesinde, istenen performansı elde etmek için modeli özelleştirmeniz gerektiğini fark edebilirsiniz. Hizmetlerimizin birçoğu, belirli iş gereksinimlerinizi karşılamak için önceden oluşturulmuş modellerin üzerine derlemenize olanak tanır. Tüm özelleştirilebilir hizmetlerimiz için hem işlem boyunca hem de kod odaklı eğitimlere yönelik API 'Ler için Kullanıcı arabirimi odaklı bir deneyim sağlıyoruz. Örnek:

* Bir Özel Konuşma Tanıma modelini, bir sözcük hata oranı (WER) ile tıp koşullarını doğru bir şekilde tanıyacak şekilde eğitebilmek istiyorsunuz %3
* Coniferous ve yaprak döken ağaçlar arasındaki farkı söyleyebilir Özel Görüntü İşleme bir görüntü Sınıflandırıcısı oluşturmak istiyorsunuz
* Gelişmiş bir otomatik müşteri deneyimi için kişisel ses verileriyle özel bir sinir sesi oluşturmak istiyorsunuz

Modelleri eğitmek ve yapılandırmak için kullanacağınız araçlar, bilişsel hizmetler 'i çağırmak için kullanabileenlerden farklıdır. Birçok durumda, özelleştirmeyi destekleyen bilişsel hizmetler, modelleri eğitmenizi, değerlendirmenize ve dağıtmanıza yardımcı olmak üzere tasarlanmış portallar ve Kullanıcı arabirimi araçları sağlar. Birkaç seçeneğe hızlıca göz atalım:<br><br>

| Yapı Taşı | Hizmet | Özelleştirme Kullanıcı arabirimi | Hızlı Başlangıç |
|--------|---------|------------------|------------|
| Görsel | Özel Görüntü İşleme | https://www.customvision.ai/ | [Hızlı Başlangıç](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Görsel | Form Tanıma | [Örnek etiketleme aracı](https://fott-preview.azurewebsites.net/) | [Hızlı Başlangıç](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Karar | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Hızlı Başlangıç](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Karar | Ölçüm Danışmanı | https://metricsadvisor.azurewebsites.net/  | [Hızlı Başlangıç](./metrics-advisor/quickstarts/web-portal.md) |
| Karar | Kişiselleştirme | Kullanıcı arabirimi, kişiselleştirici kaynağınız kapsamındaki Azure portal kullanılabilir. | [Hızlı Başlangıç](./personalizer/quickstart-personalizer-sdk.md) |
| Dil | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Dil | Soru-Cevap Oluşturucu | https://www.qnamaker.ai/ | [Hızlı Başlangıç](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Dil | Translator/özel çevirici | https://portal.customtranslator.azure.ai/ | [Hızlı Başlangıç](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Konuşma | Özel Komutlar | https://speech.microsoft.com/ | [Hızlı Başlangıç](./speech-service/custom-commands.md) |
| Konuşma | Özel Konuşma Tanıma | https://speech.microsoft.com/ | [Hızlı Başlangıç](./speech-service/custom-speech-overview.md) |
| Konuşma | Özel ses | https://speech.microsoft.com/ | [Hızlı Başlangıç](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>DevOps ve GitHub eylemleriyle sürekli tümleştirme ve teslim

Language Understanding ve konuşma hizmeti, Azure DevOps ve GitHub eylemleri tarafından desteklenen sürekli tümleştirme ve sürekli dağıtım çözümleri sunar. Bu araçlar, özel modellerin otomatik eğitimi, test edilmesi ve serbest yönetiminde kullanılır. 

* [Özel Konuşma Tanıma için CI/CD](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [LUSıS için CI/CD](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Şirket içi kapsayıcılar 

Bilişsel hizmetlerin birçoğu, şirket içi erişim ve kullanım için kapsayıcılarda dağıtılabilir. Bu kapsayıcıları kullanmak, bilişsel hizmetler 'i uyumluluk, güvenlik veya diğer işletimsel nedenlerle verilerinize daha yakın hale getirme esnekliği sunar. Bilişsel hizmetler kapsayıcılarının tam listesi için bkz. bilişsel [Hizmetler Için şirket içi kapsayıcılar](./cognitive-services-container-support.md).

## <a name="next-steps"></a>Sonraki adımlar
<!--
* Learn more about low code development options for Cognitive Services -->
* [Bilişsel hizmetler kaynağı oluşturma ve oluşturmaya başlama](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)
