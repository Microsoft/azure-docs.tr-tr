---
title: Bir modelin Web hizmeti haline geldiği
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) modelinizin bir Web hizmetine yönelik geliştirme denemenizin nasıl ilerlediğini gösteren bir genel bakış.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: ce1e7d3b3b9908d5c4608f6ab62e9b743f80c0b0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838007"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Machine Learning Studio (klasik) modelinin bir deneyden bir Web hizmetine nasıl ilerlemesi
Azure Machine Learning Studio (klasik), tahmine dayalı bir analiz modelini temsil eden bir ***deneme*** geliştirmenize, çalıştırmanıza, test etmenize ve tekrarlamenize olanak sağlayan etkileşimli bir tuval sağlar. Şunları yapmak için kullanabileceğiniz çok çeşitli modüller mevcuttur:

* Denemenize veri giriş
* Verileri işleme
* Makine öğrenimi algoritmaları kullanarak bir modeli eğitme
* Modeli puanlama
* Sonuçları değerlendirin
* Çıkış son değerleri

Deneme hesabınızı karşıladıktan sonra, kullanıcıların yeni verileri gönderebilmesi ve sonuçları geri alabilmesi için ***Klasik bir Azure Machine Learning Web hizmeti*** veya ***Yeni bir Azure Machine Learning Web hizmeti*** olarak dağıtabilirsiniz.

Bu makalede, Machine Learning modelinizin bir geliştirme denemenizin bir operasyonel Web hizmetine nasıl ilerlediğini gösteren bir genel bakış sunuyoruz.

> [!NOTE]
> Makine öğrenimi modellerinin geliştirilmesi ve dağıtılması için başka yollar vardır, ancak bu makale Machine Learning Studio (klasik) ' i nasıl kullanacağınızı bir şekilde odaklanır. Örneğin, R ile klasik bir tahmine dayalı Web hizmeti oluşturma açıklamasını okumak için bkz. Web günlüğü gönderi [derlemesi & RStudio ve Azure Machine Learning Studio kullanarak tahmine dayalı Web Apps dağıtma](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Azure Machine Learning Studio (klasik), tahmine *dayalı bir analiz modeli*geliştirmenize ve dağıtmanıza yardımcı olacak şekilde tasarlandığından, tahmine dayalı bir analiz modeli içermeyen bir deneme geliştirmek için Studio (klasik) kullanmak mümkündür. Örneğin, bir deneme yalnızca veri girişi, işleyebilir ve sonra sonuçların çıktısını alabilir. Tahmine dayalı analiz denemesinin olduğu gibi, bu tahmine dayalı olmayan denemeyi bir Web hizmeti olarak dağıtabilirsiniz, ancak deneme eğitim veya Puanlama bir makine öğrenimi modeli olmadığından daha basit bir işlemdir. Bu şekilde Studio 'Yu (klasik) kullanmaya yönelik tipik bir yöntem olmasa da, Studio 'Nun (klasik) nasıl çalıştığına ilişkin ayrıntılı bir açıklama verebilmemiz için bunu tartışmaya ekleyeceğiz.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Tahmine dayalı Web hizmeti geliştirme ve dağıtma
Machine Learning Studio (klasik) kullanarak geliştirdikçe ve dağıtırken tipik bir çözümün izlediği aşamalar aşağıda verilmiştir:

![Dağıtım akışı](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Şekil 1-tipik bir tahmine dayalı analiz modelinin aşamaları*

### <a name="the-training-experiment"></a>Eğitim denemesi
***Eğitim*** denemesi, Web hizmetinizi Machine Learning Studio (klasik) geliştirmenin ilk aşamasıdır. Eğitim denemesinin amacı, bir makine öğrenimi modeli geliştirmek, test etmek, yinelemek ve en sonunda eğitmeniz için size bir yer sağlamaktır. En iyi çözümü yazarken aynı anda birden çok modeli eğitebilirsiniz, ancak denemeye başladıktan sonra tek bir eğitilen model seçip denemeyi deneyden çıkarabilirsiniz. Tahmine dayalı analiz denemesi geliştirme hakkında bir örnek için bkz. [Azure Machine Learning Studio (klasik) üzerinde kredi riski değerlendirmesi için tahmine dayalı bir analiz çözümü geliştirme](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Tahmine dayalı deneme
Eğitim denemenize yönelik eğitilen bir modelden sonra eğitim denemenizin bir tahmine dayalı olarak dönüştürülmesi işlemini başlatmak için **Web hizmeti ayarla** ' ya ve Machine Learning Studio (klasik) ' de tahmine **dayalı Web hizmeti** ' ni seçin.  ***deneme***. Tahmine dayalı denemenin amacı, eğitilen modelinizin yeni verileri, son olarak Azure Web hizmeti olarak kullanım açısından kullanıma hazır hale gelmesini sağlamak için kullanmaktır.

Bu dönüştürme işlemi sizin için aşağıdaki adımlarda yapılır:

* Eğitim için kullanılan modül kümesini tek bir modüle dönüştürün ve eğitilen bir model olarak kaydedin
* Puanlama ile ilgili olmayan tüm gereksiz modülleri ortadan kaldırın
* Nihai Web hizmetinin kullanacağı giriş ve çıkış bağlantı noktalarını ekleyin

Tahmine dayalı denemenizin bir Web hizmeti olarak dağıtılmaya hazırlanmak için daha fazla değişiklik yapmak isteyebilirsiniz. Örneğin, Web hizmetinin yalnızca bir sonuç alt kümesini çıkışı istiyorsanız, çıkış bağlantı noktasından önce bir filtreleme modülü ekleyebilirsiniz.

Bu dönüştürme işleminde eğitim denemesi atılır. İşlem tamamlandığında, Studio 'da (klasik) iki sekmeye sahipsiniz: biri eğitim deneçi ve bir diğeri de tahmin denemesi için. Bu şekilde, Web hizmetinizi dağıtmadan ve tahmine dayalı denemeyi yeniden oluşturmadan önce eğitim denemesine değişiklikler yapabilirsiniz. Ya da başka bir deneme satırı başlatmak için eğitim denemenizin bir kopyasını kaydedebilirsiniz.

> [!NOTE]
> Tahmine **dayalı Web hizmeti** ' ne tıkladığınızda, eğitim denemenizi tahmine dayalı bir deneyle dönüştürmek için otomatik bir işlem başlatır ve bu, çoğu durumda iyi çalışır. Eğitim denemeniz karmaşık ise (örneğin, bir araya katmanız gereken birden fazla yolunuz varsa), bu dönüştürmeyi el ile yapmayı tercih edebilirsiniz. Daha fazla bilgi için bkz. [Azure Machine Learning Studio dağıtım için modelinizi hazırlama (klasik)](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>Web hizmeti
Tahmine dayalı denemenizin hazırlanmasını karşıladıktan sonra, hizmetinizi klasik Web hizmeti olarak veya Azure Resource Manager göre yeni bir Web hizmeti olarak dağıtabilirsiniz. Modelinizi *Klasik bir Machine Learning Web hizmeti*olarak dağıtarak, **Web Hizmeti Dağıt** ' a tıklayın ve Web hizmeti **Dağıt [klasik]** seçeneğini belirleyin. *Yeni Machine Learning Web hizmeti*olarak dağıtmak Için **Web Hizmeti Dağıt** ' a tıklayın ve **Web Hizmeti Dağıt [yeni]** seçeneğini belirleyin. Kullanıcılar artık, Web hizmeti REST API kullanarak modelinize veri gönderebilir ve sonuçları geri alabilir. Daha fazla bilgi için bkz. [Azure Machine Learning web hizmetini kullanma](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Olağan dışı durum: tahmin edilmeyen bir Web hizmeti oluşturma
Denemeniz, tahmine dayalı bir analiz modeli eğitmezse, hem eğitim denemesi hem de Puanlama denemesi oluşturmanız gerekmez; yalnızca bir deneme vardır ve bunu bir Web hizmeti olarak dağıtabilirsiniz. Machine Learning Studio (klasik), denemenizin, kullandığınız modülleri çözümleyerek tahmine dayalı bir model içerip içermediğini algılar.

Denemenizin üzerinde tekrarladıktan ve bununla ilgili memnun olduktan sonra:

1. **Web hizmeti ayarla** ' ya tıklayın ve **yeniden eğitim Web hizmeti** ' ni seçin-giriş ve çıkış düğümleri otomatik olarak eklenir
2. **Çalıştır** 'a tıklayın
3. **Web Hizmeti Dağıt** ' a tıklayın ve dağıtmak istediğiniz ortama göre Web Hizmeti Dağıt **[klasik]** veya **Web hizmeti dağıtma [yeni]** seçeneğini belirleyin.

Web hizmetiniz artık dağıtılır ve bunu bir tahmine dayalı Web hizmeti gibi erişebilir ve yönetebilirsiniz.

## <a name="updating-your-web-service"></a>Web hizmetinizi güncelleştirme
Denemenizi bir Web hizmeti olarak dağıttığınıza göre, güncelleştirmeniz gerekiyorsa ne olur?

Bu, güncelleştirmek için gerekenler 'e bağlıdır:

**Girişi veya çıktıyı değiştirmek veya Web hizmetinin verileri nasıl kullandığını değiştirmek istiyorsanız**

Modeli değiştirmiyorsanız, ancak yalnızca Web hizmetinin verileri nasıl işlediğini değiştiriyorsanız, tahmine dayalı denemeyi düzenleyebilir ve ardından **Web Hizmeti Dağıt** ' a tıklayıp Web Hizmeti Dağıt **[klasik]** ' i veya Web hizmeti dağıtma [ **Yeni]** seçeneğini belirleyebilirsiniz. Web hizmeti durdurulmuş, güncelleştirilmiş tahmine dayalı deneme dağıtılır ve Web hizmeti yeniden başlatılır.

İşte örnek: tahmine dayalı denemenizin tüm giriş verisi satırını tahmin edilen sonuçla döndürdüğünü varsayın. Web hizmetinin yalnızca sonucu döndürmesini istediğinize karar verebilirsiniz. Bu nedenle, sonuç dışında sütunları hariç tutmak için, çıkış bağlantı noktasından hemen önce, tahmine dayalı denemenize bir **Proje sütunları** modülü ekleyebilirsiniz. **Web Hizmeti Dağıt** ' a tıklayıp Web **Hizmeti Dağıt [klasik]** seçeneğini belirlediğinizde veya Web **hizmetini [yeni] yeniden dağıtırken** , Web hizmeti güncellenir.

**Modeli yeni verilerle yeniden eğiteetmek istiyorsunuz**

Makine öğrenimi modelinizi korumak istiyorsanız, ancak yeni verilerle yeniden eğmek istiyorsanız iki seçeneğiniz vardır:

1. **Web hizmeti çalışırken modeli yeniden eğmek** isterseniz, tahmine dayalı Web hizmeti çalışırken modelinize yeniden eğmek istiyorsanız bunu, eğitim denemenize ***yeniden eğitim***denemesi yaparak, daha sonra  ***yeniden eğitim Web* hizmeti**olarak dağıtabilirsiniz. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [Machine Learning modellerini programlı olarak yeniden eğitme](/azure/machine-learning/studio/retrain-machine-learning-model).
2. **Özgün eğitim denemesine dönün ve modelinizi geliştirmek için farklı eğitim verileri kullanın** . tahmine dayalı denemenizin Web hizmetiyle bağlantısı vardır, ancak eğitim denemesi bu şekilde doğrudan bağlantılı değildir. Özgün Eğitim denemesini değiştirip **Web hizmeti ayarla**' ya tıkladığınızda, dağıtıldığında *Yeni* bir Web hizmeti oluşturacak *Yeni* bir tahmine dayalı deneme oluşturulur. Yalnızca özgün Web hizmetini güncelleştirmez.

   Eğitim denemesini değiştirmeniz gerekiyorsa dosyayı açın ve **farklı kaydet** ' e tıklayarak bir kopyasını oluşturun. Bu, özgün eğitim denemenize, tahmine dayalı denemenize ve Web hizmetine dokunmaz. Artık, yaptığınız değişikliklerle yeni bir Web hizmeti oluşturabilirsiniz. Yeni Web hizmetini dağıttıktan sonra, önceki Web hizmetini durdurup durdurmayacağınıza veya yeni bir hizmet ile birlikte çalışmaya devam edebilirsiniz.

**Farklı bir model eğitme**

Farklı bir makine öğrenimi algoritması seçme, farklı bir eğitim yöntemi vb. gibi orijinal tahmine dayalı denemenize değişiklikler yapmak istiyorsanız, modelinize yeniden eğitim sağlamak için yukarıda açıklanan ikinci yordamı izlemeniz gerekir: Eğitim denemesi, **farklı kaydet** ' e tıklayarak bir kopya oluşturun, ardından modelinizi geliştirme, tahmine dayalı deneme oluşturma ve Web hizmetini dağıtma yeni yolunu başlatın. Bu, özgün bir hizmetle ilgisi olmayan yeni bir Web hizmeti oluşturur. bir veya her ikisinin de çalışmaya devam etmesini seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Geliştirme ve deneme süreci hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* denemeyi dönüştürme- [modelinizi Azure Machine Learning Studio dağıtım için hazırlama (klasik)](convert-training-experiment-to-scoring-experiment.md)
* Web hizmetini dağıtma- [Azure Machine Learning Web hizmeti dağıtma](deploy-a-machine-learning-web-service.md)
* Model oluşturma [Machine Learning modelleri programlama yoluyla yeniden eğitme](/azure/machine-learning/studio/retrain-machine-learning-model)

Bütün işlem örnekleri için bkz.:

* [Machine Learning öğreticisi: Azure Machine Learning Studio ilk denemenizi oluşturma (klasik)](create-experiment.md)
* [İzlenecek yol: Azure Machine Learning 'de kredi riski değerlendirmesi için tahmine dayalı bir analiz çözümü geliştirme](tutorial-part1-credit-risk.md)

