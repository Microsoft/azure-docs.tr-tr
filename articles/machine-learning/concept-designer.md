---
title: Tasarımcı ile ML modelleri derleme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning için tasarımcıyı oluşturan hüküm, kavramlar ve iş akışı hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: e6738bf944c5a80d0cb54432ade7555ebdcfbd51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503549"
---
# <a name="what-is-azure-machine-learning-designer"></a>Azure Machine Learning tasarımcısı nedir? 


Azure Machine Learning tasarımcı, makine öğrenimi modelleri oluşturmak için etkileşimli bir tuvalde [veri kümelerini](#datasets) ve [modülleri](#module) görsel olarak bağlamanıza olanak tanır. Tasarımcı ile çalışmaya başlama hakkında bilgi edinmek için bkz [. Öğretici: tasarımcı ile otomobil fiyatını tahmin](tutorial-designer-automobile-price-train-score.md) etme

![Azure Machine Learning tasarımcı örneği](./media/concept-designer/designer-drag-and-drop.gif)

Tasarımcı, şu gibi paylaşılan kaynakları düzenlemek için Azure Machine Learning [çalışma alanınızı](concept-workspace.md) kullanır:

+ [Pipelines](#pipeline)
+ [Veri kümeleri](#datasets)
+ [İşlem kaynakları](#compute)
+ [Kayıtlı modeller](concept-azure-machine-learning-architecture.md#models)
+ [Yayınlanan işlem hatları](#publish)
+ [Gerçek zamanlı uç noktalar](#deploy)

## <a name="model-training-and-deployment"></a>Model eğitimi ve dağıtımı

Tasarımcı, makine öğrenimi modellerini derlemek, test etmek ve dağıtmak için size görsel bir tuval sağlar. Tasarımcı ile şunları yapabilirsiniz:

+ [Veri kümelerini](#datasets) ve [modülleri](#module) tuval üzerine sürükleyip bırakın.
+ İşlem [hattı taslağı](#pipeline-draft)oluşturmak için modülleri bağlayın.
+ Azure Machine Learning çalışma alanınızdaki işlem kaynaklarını kullanarak bir işlem [hattı çalıştırması](#pipeline-run) gönderebilirsiniz.
+ **Eğitim** işlem hatlarınızı **çıkarım** işlem hatlarına dönüştürün.
+ Farklı parametrelerle ve veri kümeleriyle çalışan yeni bir işlem hattı göndermek için işlem hatlarınızı bir REST **ardışık düzen uç noktasına** [yayımlayın](#publish) .
    + Parametreleri ve veri kümelerini değiştirirken birden çok modeli eğitmek için tek bir işlem hattını yeniden kullanmak üzere bir **eğitim işlem hattı** yayımlayın.
    + Daha önce eğitilen bir modeli kullanarak yeni verilerde tahmine dayalı hale getirmek için bir **toplu çıkarım işlem hattı** yayımlayın.
+ Gerçek zamanlı yeni verilerde tahmine dayalı hale getirmek için gerçek zamanlı bir uç noktaya gerçek zamanlı bir **çıkarım işlem hattı** [dağıtın](#deploy) .

![Tasarımcıda eğitim, toplu çıkarım ve gerçek zamanlı çıkarım için iş akışı diyagramı](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>İşlem Hattı

İşlem [hattı](concept-azure-machine-learning-architecture.md#ml-pipelines) , bağlandığınız veri kümeleri ve analitik modüllerden oluşur. İşlem hatları birçok kullanımı vardır: tek bir modeli gösteren bir işlem hattını veya birden çok modeli gösteren bir işlem hattı yapabilirsiniz. Tahminleri gerçek zamanlı veya toplu olarak uygulayan bir işlem hattı oluşturabilir veya yalnızca verileri temizlemeleri için bir işlem hattı oluşturabilirsiniz. İşlem hatları, işinizi yeniden kullanmanıza ve projelerinizi düzenlemenize olanak tanır.

### <a name="pipeline-draft"></a>Ardışık düzen taslağı

Tasarımcıda bir işlem hattını düzenlerken, ilerleme durumu bir **ardışık düzen taslağı** olarak kaydedilir. Bir işlem hattı taslağını, modüller ekleyerek veya kaldırarak, işlem hedeflerini yapılandırarak, parametreler oluşturarak ve benzeri bir noktada düzenleyebilirsiniz.

Geçerli bir işlem hattı şu özelliklere sahiptir:

* Veri kümeleri yalnızca modüllere bağlanabilir.
* Modüller yalnızca veri kümelerine ya da diğer modüllere bağlanabilir.
* Modüller için tüm giriş bağlantı noktalarında veri akışına bir bağlantı bulunmalıdır.
* Her modül için gerekli tüm parametrelerin ayarlanması gerekir.

İşlem hattı taslağı çalıştırmaya hazırsanız bir işlem hattı çalıştırması gönderilir.

### <a name="pipeline-run"></a>İşlem hattı çalıştırması

İşlem hattını her çalıştırdığınızda işlem hattının yapılandırması ve sonuçları, çalışma alanınızda işlem **hattı çalıştırması** olarak depolanır. Sorun giderme veya denetim amaçlarıyla incelemek için herhangi bir işlem hattı çalıştırmasına geri dönebilirsiniz. Düzenlemeniz için yeni bir işlem hattı taslağı oluşturmak üzere bir işlem hattı çalıştırmasını **klonlayın** .

İşlem hattı çalıştırmaları, çalıştırma geçmişini düzenlemek için [denemeleri](concept-azure-machine-learning-architecture.md#experiments) olarak gruplandırılır. Her işlem hattı çalıştırması için denemeyi ayarlayabilirsiniz. 

## <a name="datasets"></a>Veri kümeleri

Machine Learning veri kümesi, verilerinize erişmeyi ve bunlarla çalışmayı kolaylaştırır. Birkaç örnek veri kümesi, daha sonra denemek için tasarımcıya dahildir. Gerektiğinde daha [fazla veri kümesi kaydedebilirsiniz.](how-to-create-register-datasets.md)

## <a name="module"></a>Modül

Bir modül, verilerinizde gerçekleştirebileceğiniz bir algoritmadır. Tasarımcı, veri giriş işlevlerinden eğitim, Puanlama ve doğrulama işlemlerine kadar çeşitli modüller içerir.

Bir modül, modülün iç algoritmalarını yapılandırmak için kullanabileceğiniz parametreler kümesine sahip olabilir. Tuvalde bir modül seçtiğinizde, modülün parametreleri tuvalin sağındaki Özellikler bölmesinde görüntülenir  Modelinizi ayarlamak için, bu bölmedeki parametreleri değiştirebilirsiniz. Tasarımcıda bireysel modüller için işlem kaynaklarını ayarlayabilirsiniz. 

:::image type="content" source="./media/concept-designer/properties.png" alt-text="Modül özellikleri":::


Kullanılabilir makine öğrenimi algoritmalarının kitaplığı üzerinden gezinerek bazı yardım için bkz. [algoritma & modül başvurusuna genel bakış](algorithm-module-reference/module-reference.md). Algoritma seçme konusunda yardım için [Azure Machine Learning algoritması oluşturma sayfasına](algorithm-cheat-sheet.md)bakın.

## <a name="compute-resources"></a><a name="compute"></a> İşlem kaynakları

İş hattınızı çalıştırmak ve dağıtılan modellerinizi gerçek zamanlı uç noktalar veya ardışık düzen uç noktaları olarak barındırmak (Batch çıkarımı için) için çalışma alanınızdan işlem kaynaklarını kullanın. Desteklenen işlem hedefleri şunlardır:

| İşlem hedefi | Eğitim | Dağıtım |
| ---- |:----:|:----:|
| Azure Machine Learning işlem | ✓ | |
| Azure Machine Learning işlem örneği | ✓ | |
| Azure Kubernetes Service | | ✓ |

İşlem hedefleri [Azure Machine Learning çalışma alanınıza](concept-workspace.md)iliştirilir. İşlem hedeflerinizi [Azure Machine Learning Studio](https://ml.azure.com)'daki çalışma alanınızda yönetirsiniz.

## <a name="deploy"></a>Dağıtma

Gerçek zamanlı bir işlem hattı gerçekleştirmek için, bir işlem hattını **gerçek zamanlı uç nokta** olarak dağıtmanız gerekir. Gerçek zamanlı uç nokta, bir dış uygulama ve Puanlama modeliniz arasında bir arabirim oluşturur. Gerçek zamanlı bir uç noktaya yapılan çağrı, uygulamaya gerçek zamanlı olarak tahmin sonuçları döndürür. Gerçek zamanlı bir uç noktaya çağrı yapmak için, uç noktasını dağıtırken oluşturulan API anahtarını geçirirsiniz. Uç nokta, Web programlama projeleri için popüler bir mimari seçimi olan REST 'i temel alır.

Gerçek zamanlı uç noktaların bir Azure Kubernetes hizmet kümesine dağıtılması gerekir.

Modelinizi dağıtmayı öğrenmek için bkz. [öğretici: tasarımcı ile makine öğrenimi modeli dağıtma](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Yayımlama

Ardışık Düzen **uç noktasına** bir işlem hattı da yayımlayabilirsiniz. Gerçek zamanlı bir uç noktaya benzer şekilde, bir ardışık düzen uç noktası, REST çağrılarını kullanarak dış uygulamalardan yeni işlem hattı çalıştırmaları göndermenize olanak tanır. Ancak, bir ardışık düzen uç noktası kullanarak gerçek zamanlı olarak veri gönderemez veya alamazsınız.

Yayınlanan işlem hatları esnektir, modelleri eğitme veya yeniden eğitme, [toplu iş](how-to-run-batch-predictions-designer.md)için bilgi işlem gerçekleştirme, yeni verileri işleme ve çok daha fazlasını yapmak için kullanılabilirler. Çoklu işlem hatlarını tek bir ardışık düzen uç noktasına yayımlayabilir ve hangi ardışık düzen sürümünün çalıştırılacağını belirtebilirsiniz.

Yayımlanan bir işlem hattı, her modülün ardışık düzen taslağı içinde tanımladığınız işlem kaynakları üzerinde çalışır.

Tasarımcı, SDK ile aynı [Publishedpipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline) nesnesini oluşturur.

## <a name="next-steps"></a>Sonraki adımlar

* Öğretici ile tahmine dayalı analiz ve makine öğrenimi temellerini öğrenin [: tasarımcı ile otomobil fiyatlarını tahmin](tutorial-designer-automobile-price-train-score.md) etme
* Mevcut [Tasarımcı örneklerini](samples-designer.md) gereksinimlerinize uyarlayacak şekilde nasıl değiştireceğinizi öğrenin.