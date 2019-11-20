---
title: Tasarımcı ile ML modelleri derleme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning için tasarımcıyı oluşturan hüküm, kavramlar ve iş akışı hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517871"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Azure Machine Learning tasarımcı (Önizleme) nedir? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Azure Machine Learning tasarımcı, kod yazmadan, verileri dağıtmanıza, eğitmenize, test etmenize, dağıtmanıza, yönetmenize ve izlemenize olanak sağlar.

Programlama gerekmez, modelinizi oluşturmak için [veri kümelerini](#datasets) ve [modülleri](#module) görsel olarak bağlamanız gerekir.

Tasarımcı Azure Machine Learning [çalışma alanınızı](concept-workspace.md) şu amaçlarla kullanır:

+ Çalışma alanında işlem [hatları](#pipeline) oluşturun, düzenleyin ve çalıştırın.
+ [Veri kümelerine](#datasets)erişin.
+ İşlem hattını çalıştırmak için çalışma alanındaki [işlem kaynaklarını](#compute) kullanın. 
+ [Modelleri](concept-azure-machine-learning-architecture.md#models)kaydedin.
+ İşlem hatlarını REST uç noktaları olarak [yayımlayın](#publish) .
+ Modelleri, çalışma alanındaki bilgi işlem kaynaklarında ardışık düzen uç noktaları (toplu çıkarım için) veya gerçek zamanlı uç noktalar olarak [dağıtın](#deployment) .

![Tasarımcıya genel bakış](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>İş akışı

Tasarımcı, bir modeli hızlıca oluşturmak, test etmek ve bir model üzerinde yinelemek için etkileşimli, görsel bir tuval sağlar. 

+ [Veri kümelerini](#datasets) ve [modülleri](#module) tuval üzerine sürükleyip bırakın.
+ Bir işlem [hattı](#pipeline)oluşturmak için modülleri birbirine bağlayın.
+ Machine Learning hizmet çalışma alanının işlem kaynağını kullanarak ardışık düzeni çalıştırın.
+ İşlem hattını düzenleyerek ve yeniden çalıştırarak model tasarımınızda yineleme yapın.
+ Hazırsanız, **eğitim** işlem hattınızı bir **çıkarım ardışık düzenine**dönüştürün.
+ İşlem hattınızı, kendisini oluşturduğu Python kodu olmadan yeniden göndermek istiyorsanız bir REST uç noktası olarak [yayımlayın](#publish) .
+ Modelinize başkaları tarafından erişilebilmesi için, çıkarım ardışık düzenini bir ardışık düzen uç noktası veya gerçek zamanlı uç nokta olarak [dağıtın](#deployment) .

## <a name="pipeline"></a>İşlem hattı

Sıfırdan bir ML işlem [hattı](concept-azure-machine-learning-architecture.md#ml-pipelines) oluşturun veya var olan bir örnek işlem hattını şablon olarak kullanın. İşlem hattını her çalıştırdığınızda, yapıtlar çalışma alanınızda depolanır. İşlem hattı çalıştırmaları [denemeleri](concept-azure-machine-learning-architecture.md#experiments)olarak gruplandırılır.

İşlem hattı, bir model oluşturmak için birlikte bağlandığınız veri kümeleri ve analitik modüllerden oluşur. Özel olarak, geçerli bir işlem hattı şu özelliklere sahiptir:

* Veri kümeleri yalnızca modüllere bağlı olabilir.
* Modüller, veri kümelerine ya da diğer modüllere bağlı olabilir.
* Modüller için tüm giriş bağlantı noktalarında veri akışına bir bağlantı bulunmalıdır.
* Her modül için gerekli tüm parametrelerin ayarlanması gerekir.


Tasarımcı ile çalışmaya başlama hakkında bilgi edinmek için bkz. [öğretici: tasarımcı ile otomobil fiyatını tahmin](tutorial-designer-automobile-price-train-score.md)etme.

## <a name="datasets"></a>Veri kümeleri

Machine Learning veri kümesi, verilerinize erişmeyi ve bunlarla çalışmayı kolaylaştırır. Bir dizi örnek veri kümesi, tasarımcı ile denemeler yapmak için eklenmiştir. Gerektiğinde daha [fazla veri kümesi kaydedebilirsiniz.](./how-to-create-register-datasets.md)

## <a name="module"></a>Modül

Bir modül, verilerinizde gerçekleştirebileceğiniz bir algoritmadır. Tasarımcı, veri giriş işlevlerinden eğitim, Puanlama ve doğrulama işlemlerine kadar birçok modül içerir.

Bir modül, modülün iç algoritmalarını yapılandırmak için kullanabileceğiniz parametreler kümesine sahip olabilir. Tuvalde bir modül seçtiğinizde, modülün parametreleri tuvalin sağındaki Özellikler bölmesinde görüntülenir. Modelinizi ayarlamak için, bu bölmedeki parametreleri değiştirebilirsiniz.

![Modül özellikleri](media/ui-concept-visual-interface/properties.png)

Kullanılabilir makine öğrenimi algoritmalarının kitaplığı ile ilgili bazı yardım için bkz. [algoritma & modül başvurusuna genel bakış](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>İşlem kaynakları

İş hattınızı çalıştırmak ve dağıtılan modellerinizi gerçek zamanlı uç noktalar veya ardışık düzen uç noktaları olarak barındırmak (Batch çıkarımı için) için çalışma alanınızdan işlem kaynaklarını kullanın. Desteklenen işlem hedefleri şunlardır:

| İşlem hedefi | Eğitim | Dağıtım |
| ---- |:----:|:----:|
| Azure Machine Learning işlem | ✓ | |
| Azure Kubernetes Service | | ✓ |

İşlem hedefleri Machine Learning [çalışma alanınıza](concept-workspace.md)iliştirilir. İşlem hedeflerinizi [Azure Machine Learning Studio](https://ml.azure.com)'daki çalışma alanınızda yönetirsiniz.

## <a name="publish"></a>Yayımlama

İşlem hattınızı hazırladıktan sonra, bunu bir REST uç noktası olarak yayımlayabilirsiniz. Bir [Publishedpipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) , kendisini oluşturan Python kodu olmadan gönderilebilir.

Ayrıca, bir PublishedPipeline, farklı PipelineParameter değerleri ve girişleri ile bir işlem hattını yeniden göndermek için kullanılabilir.

## <a name="deployment"></a>Dağıtım

Tahmine dayalı modeliniz hazırlandıktan sonra, tasarımcıyı bir işlem hattı uç noktası veya gerçek zamanlı uç nokta olarak doğrudan tasarımcıdan dağıtın.

Ardışık düzen uç noktası, farklı PipelineParameter değerleri ve toplu iş çıkarımı girişleri ile bir işlem hattı çalıştırması gönderebileceğiniz bir PublishedPipeline.

Gerçek zamanlı uç nokta, bir uygulama ile Puanlama modeliniz arasında bir arabirim sağlar. Bir dış uygulama, Puanlama modeliyle gerçek zamanlı olarak iletişim kurabilir. Gerçek zamanlı bir uç noktaya yapılan çağrı, bir dış uygulamaya tahmin sonuçları döndürür. Gerçek zamanlı bir uç noktaya çağrı yapmak için, uç noktasını dağıtırken oluşturulan bir API anahtarı geçirirsiniz. Uç nokta, Web programlama projeleri için popüler bir mimari seçimi olan REST 'i temel alır.

Modelinizi dağıtmayı öğrenmek için bkz. [öğretici: tasarımcı ile makine öğrenimi modeli dağıtma](tutorial-designer-automobile-price-deploy.md).

## <a name="moving-from-the-visual-interface-to-the-designer"></a>Görsel arabirimden tasarımcıya geçme

Görsel Arabirim (Önizleme) güncelleştirildi ve artık Azure Machine Learning tasarımcı (Önizleme). Tasarımcı, Azure Machine Learning diğer özellikleriyle tamamen tümleştirilen bir ardışık düzen tabanlı arka uç kullanmak üzere tasarlanmıştır. 

Bu güncelleştirmelerin sonucunda, görsel arabirim için bazı kavramlar ve terimler değiştirilmiştir veya yeniden adlandırılacaktır. En önemli kavramsal değişiklikler için aşağıdaki tabloya bakın. 

| Tasarımcıda kavram | Daha önce görsel arabirimde |
| ---- |:----:|
| Ardışık düzen taslağı | Deneyin |
| Gerçek zamanlı uç nokta | Web hizmeti |

### <a name="migrating-to-the-designer"></a>Tasarımcıya geçme

Varolan Visual Interface denemeleri ve Web hizmetlerini, tasarımcıda işlem hatlarına ve gerçek zamanlı uç noktalara dönüştürebilirsiniz. Visual Interface varlıklarınızı geçirmek için aşağıdaki adımları kullanın:

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Sonraki adımlar

* Öğretici ile tahmine dayalı analiz ve makine öğrenimi hakkında temel bilgileri öğrenin [: tasarımcı ile otomobil fiyatlarını tahmin](tutorial-designer-automobile-price-train-score.md) etme
* Örneklerden birini kullanın ve gereksinimlerinize göre değiştirin:

- [Örnek 1-gerileme: bir otomobil fiyatını tahmin edin](how-to-designer-sample-regression-automobile-price-basic.md)
- [Örnek 2-gerileme: otomobil fiyatlandırma için algoritmaları karşılaştırın](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Örnek 3-Özellik seçimi ile sınıflandırma: gelir tahmini](how-to-designer-sample-classification-predict-income.md)
- [Örnek 4-sınıflandırma: kredi riskini tahmin etme (maliyet duyarlı)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Örnek 5-sınıflandırma: karmaşıklığı tahmin etme](how-to-designer-sample-classification-churn.md)
- [Örnek 6-sınıflandırma: uçuş gecikmelerini tahmin etme](how-to-designer-sample-classification-flight-delay.md)
- [Örnek 7-metin sınıflandırması: Vikipedi SP 500 veri kümesi](how-to-designer-sample-text-classification.md)

