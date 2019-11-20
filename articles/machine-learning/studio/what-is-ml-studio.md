---
title: ML Studio nedir (klasik)
titleSuffix: Azure
description: Azure Machine Learning Studio (klasik), bir dizi algoritmaların ve modüllerden kullanıma hazırlama kitaplığından hızlı bir şekilde model oluşturmaya yönelik bir sürükle ve bırak aracıdır.
services: machine-learning
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 10/17/2019
ms.openlocfilehash: 325278ee0222070ce7c02c1d72961f0431284677
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670541"
---
# <a name="what-is-machine-learning-studio-classic"></a>Machine Learning Studio (klasik) nedir?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (klasik), verilerinizde tahmine dayalı analiz çözümleri oluşturmak, test etmek ve dağıtmak için kullanabileceğiniz işbirliğine dayalı, sürükle ve bırak aracıdır.  Machine Learning Studio klasik sürümü, modelleri, Excel gibi özel uygulamalar veya BI araçları tarafından kolayca tüketilen Web Hizmetleri olarak yayımlar.

Machine Learning Studio (klasik) veri bilimi, tahmine dayalı analiz, bulut kaynakları ve verilerinizin karşılabileceği yerdir.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Machine Learning Studio (klasik) etkileşimli çalışma alanı
Tahmine dayalı bir analiz modeli geliştirmek için genellikle bir veya daha fazla kaynaktaki verileri kullanır, çeşitli veri işleme ve istatistiksel işlevler aracılığıyla bu verileri dönüştürür ve analiz eder ve bir sonuç kümesi oluşturur. Bir modelin bu şekilde geliştirilmesi yinelemeli bir işlemdir. Çeşitli işlevleri ve bunların parametrelerini değiştirirken, eğitilmiş ve verimli bir model elde ettiğinizi düşüneceğiniz ana kadar sonuçlarınız yakınsanır.

Azure Machine Learning Studio klasik sürümü, tahmine dayalı bir analiz modelini kolayca oluşturmak, test etmek ve yinelemek için etkileşimli ve görsel bir çalışma alanı sağlar. ***Veri kümelerini*** ve analiz ***modüllerini*** etkileşimli bir tuvale sürükleyip bırakın ve Machine Learning Studio (klasik) ' de çalıştırdığınız bir ***deneme***oluşturmak için bunları birbirine bağlayarak. Model tasarımınızı yinelemek için, denemeyi düzenleyin, isterseniz bir kopyasını kaydedin ve yeniden çalıştırın. Hazır olduğunuzda, ***eğitim denemenizi*** bir ***tahmine dayalı denemeye*** dönüştürebilir ve ardından modelinize başkaları tarafından erişilebilmesi için bunu bir ***web hizmeti*** olarak yayımlayabilirsiniz.

Programlama gerekmez, tahmine dayalı analiz modelinizi oluşturmak için veri kümelerini ve modülleri görsel olarak bağlayın.

![Diyagram Azure Machine Learning Studio: denemeleri oluşturun, çok sayıda kaynak için verileri okuyun, puanlanmış verileri yazın, modeller yazın.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Machine Learning Studio (klasik) Azure Machine Learning nasıl farklıdır?

[Azure Machine Learning](../service/overview-what-is-azure-ml.md) , **hem SDK 'ları hem de** Azure Machine Learning tasarımcısını (Önizleme) sağlayarak verileri hızlı bir şekilde hazırlayın, makine öğrenimi modellerini eğitme ve dağıtın. Tasarımcı, Studio 'ya benzer bir sürükle ve bırak deneyimi sağlar (klasik). Ancak, Studio 'nun özel işlem platformunun (klasik) aksine, tasarımcı kendi işlem kaynaklarınızı kullanır ve Azure Machine Learning tamamen tümleşiktir.

Hızlı bir karşılaştırma aşağıda verilmiştir:

|| Machine Learning Studio (klasik) | Azure Machine Learning |
|---| --- | --- |
| Sürükle ve bırak arabirimi | Evet | Evet- [Azure Machine Learning tasarımcı (Önizleme)](../service/concept-designer.md) |
| Deneyin | Ölçeklenebilir (10 GB eğitim veri limiti) | İşlem hedefi ile ölçeklendirme |
| Sürükle ve bırak arabirimi için modüller | Sayısı | İlk popüler [modüller](../algorithm-module-reference/module-reference.md) kümesi|
|Eğitim işlem hedefleri| Özel işlem hedefi, yalnızca CPU desteği| Azure Machine Learning işlem (GPU veya CPU) ve Not defteri VM 'lerini destekler.<br/>([SDK 'da desteklenen diğer hesaplar](../service/concept-compute-target.md#train))|
|Inlekrime işlem hedefleri| Özel Web hizmeti biçimi özelleştirilebilir değil |  Azure Kubernetes hizmeti ve AML Işlem <br/>([SDK 'da desteklenen diğer hesaplar](../service/how-to-deploy-and-where.md)) |
| ML işlem hattı | Desteklenmiyor | Desteklenen işlem [hatları](../service/concept-ml-pipelines.md) |
| MLOps | Temel model yönetimi ve dağıtımı | Yapılandırılabilir dağıtım-model ve işlem hattı sürümü oluşturma ve izleme |
| Model biçimi | Özel biçim, yalnızca stüdyo | Eğitim iş türüne bağlı olarak standart biçim |
|Otomatik model eğitimi ve hyperparameter ayarlaması | Hayır | Henüz tasarımcıda değil <br/> ([SDK ve çalışma alanı giriş sayfasında desteklenir](../service/concept-automated-ml.md)) | 

Tasarımcıyı [öğreticiyle deneyin: tasarımcı ile otomobil fiyatını tahmin](../service/tutorial-designer-automobile-price-train-score.md) edin

> [!NOTE]
> Studio 'da oluşturulan modeller (klasik) Azure Machine Learning tarafından dağıtılamaz veya yönetilemez. Ancak, tasarımcıda oluşturulan ve dağıtılan modeller Azure Machine Learning çalışma alanı aracılığıyla yönetilebilir.

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Machine Learning Studio (klasik) genel bakış diyagramını indirin
**Microsoft Azure Machine Learning Studio (klasik) özelliklerine genel bakış** diyagramını indirin ve Machine Learning Studio (klasik) özelliklerine ilişkin üst düzey bir görünüm alın. Diyagramı yakınınızda tutmak için tabloid boyutunda (11 x 17 inç) yazdırabilirsiniz.

**Diyagramı buraya indirin: [Microsoft Azure Machine Learning Studio (klasik) özelliklerine genel bakış](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf) **
![Microsoft Azure Machine Learning Studio (klasik) özelliklerine genel bakış](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Studio (klasik) deneme bileşenleri
Bir deneme, tahmine dayalı bir modeli oluşturmak için birlikte bağladığınız analitik modüllere veri sağlayan veri kümelerinden oluşur. Geçerli bir deneme özellikle şu özelliklere sahiptir:

* Denemenin en az bir veri kümesi ve bir modülü vardır
* Veri kümeleri yalnızca modüllere bağlanabilir
* Modüller veri kümelerine veya diğer modüllere bağlanabilir
* Modüllerin tüm giriş bağlantı noktalarının veri akışına bir tür bağlantısı olması gerekir
* Her bir modül için gereken tüm parametreler ayarlanmalıdır

Bir denemeyi sıfırdan oluşturabilir veya var olan bir örnek denemeyi şablon olarak kullanabilirsiniz. Daha fazla bilgi için bkz. [Örnek denemeleri kopyalayarak yeni makine öğrenimi denemeleri oluşturma](sample-experiments.md).

Deneme oluşturma örneği için bkz. [Azure Machine Learning Studio basit bir deneme oluşturma (klasik)](create-experiment.md).

Tahmine dayalı analiz çözümü oluşturmaya yönelik daha kapsamlı bir anlatım için bkz. [Azure Machine Learning Studio (klasik) ile tahmine dayalı bir çözüm geliştirme](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Veri kümeleri
Veri kümesi, modelleme sürecinde kullanılabilmesi için Machine Learning Studio (klasik) karşıya yüklenmiş olan bir veri kümesidir. Deneme için Machine Learning Studio (klasik) bir dizi örnek veri kümesi bulunur ve ihtiyacınız olduğunda daha fazla veri kümesi yükleyebilirsiniz. Dahil olan veri kümelerine aşağıda birkaç örnek verilmiştir:

* **Çeşitli otomobiller için MPG verileri** - Otomobiller için silindir, beygir gücü, vb. sayısına göre tanımlanan galon başına mil (MPG) değerleri.
* **Meme kanseri verileri** - Meme kanseri tanılama verileri.
* **Orman yangını verileri** - Kuzey doğu Portekiz'de orman yangını boyutları.

Bir deneme oluştururken, tuvalin solunda kullanılabilen veri kümeleri listesinden seçim yapabilirsiniz.

Machine Learning Studio (klasik) eklenen örnek veri kümelerinin bir listesi için bkz. [Azure Machine Learning Studio (klasik) örnek veri kümelerini kullanma](use-sample-datasets.md).

### <a name="modules"></a>Modüller
Bir modül, verilerinizde gerçekleştirebileceğiniz bir algoritmadır.  Machine Learning Studio klasik sürümünde, veri giriş işlevlerinden eğitim, Puanlama ve doğrulama işlemlerine kadar birçok modül bulunur. Dahil olan modüllere aşağıda birkaç örnek verilmiştir:

* [ARFF 'e Dönüştür][convert-to-arff] -.net seri hale getirilmiş bir veri kümesini öznitelik Ilişkisi dosya biçimine dönüştürür (arff).
* [Temel Istatistikleri hesaplama][elementary-statistics] -ortalama, standart sapma vb. basit istatistikleri hesaplar.
* [Doğrusal regresyon][linear-regression] -çevrimiçi bir gradyan tabanlı doğrusal regresyon modeli oluşturur.
* [Puan modeli][score-model] -eğitilmiş bir sınıflandırma veya regresyon modeli puanları.

Bir deneme oluştururken, tuvalin solunda bulunan modüllerin listesinden seçim yapabilirsiniz.

Bir modül, modülün iç algoritmalarını yapılandırmak için kullanabileceğiniz parametreler kümesine sahip olabilir. Tuvalde bir modül seçtiğinizde, modülün parametreleri tuvalin sağındaki **Özellikler** bölmesinde görüntülenir Modelinizi ayarlamak için, bu bölmedeki parametreleri değiştirebilirsiniz.

Kullanılabilir makine öğrenimi algoritmalarının büyük kitaplığındaki bazı yardım için bkz. [Microsoft Azure Machine Learning Studio algoritmaları seçme (klasik)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Tahmine dayalı analiz web hizmetini dağıtma
Tahmine dayalı analiz modeliniz hazırlandıktan sonra, bunu bir Web hizmeti olarak Machine Learning Studio (klasik) ile dağıtabilirsiniz. Bu işlemle ilgili daha fazla bilgi için bkz. [Azure Machine Learning Web hizmeti dağıtma](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Sonraki adımlar
Tahmine dayalı analiz ve makine öğrenimine ilişkin temel bilgileri, [adım adım hızlı başlangıç](create-experiment.md) ve [örnek oluşturma](sample-experiments.md)aracılığıyla öğrenebilirsiniz.

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
