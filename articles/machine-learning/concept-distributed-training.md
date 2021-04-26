---
title: Dağıtılmış eğitim nedir?
titleSuffix: Azure Machine Learning
description: Ne tür dağıtılmış eğitim Azure Machine Learning destekleyeceğinizi ve dağıtılmış eğitim için sunulan açık kaynak çerçevesi tümleştirmelerini öğrenin.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f87175500fcf5bdbcf9a5c2f499f6bab96b37b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102498974"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Azure Machine Learning ile dağıtılmış eğitim

Bu makalede, dağıtılmış eğitim ve Azure Machine Learning derin öğrenme modelleri için nasıl destekleyeceğinizi öğreneceksiniz. 

Dağıtılmış eğitiminde, bir modeli eğitme iş yükü, çalışan düğümleri olarak adlandırılan birden çok mini işlemci arasında bölünür ve paylaşılır. Bu çalışan düğümleri, model eğitiminin hızlandırılmasına yönelik paralel olarak çalışır. Dağıtılmış eğitim geleneksel ML modelleri için kullanılabilir, ancak eğitim derin sinir ağları için [derin öğrenme](concept-deep-learning-vs-machine-learning.md) gibi işlem ve zaman yoğun görevler için daha uygundur. 

## <a name="deep-learning-and-distributed-training"></a>Derin öğrenme ve dağıtılmış eğitim 

İki temel dağıtılmış eğitim türü vardır: [veri paralelliği](#data-parallelism) ve [model paralelliği](#model-parallelism). Derin öğrenme modelleriyle ilgili dağıtılmış eğitim için, [Python 'daki Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro) popüler çerçeveler, PyTorch ve TensorFlow ile tümleştirmeleri destekler. Her iki çerçeve de Dağıtılmış eğitim için veri paralelliğini artırır ve işlem hızlarını iyileştirmek için [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) özelliğinden yararlanabilir. 

* [PyTorch ile dağıtılmış eğitim](how-to-train-pytorch.md#distributed-training)

* [TensorFlow ile dağıtılmış eğitim](how-to-train-tensorflow.md#distributed-training)

Dağıtılmış eğitim gerektirmeyen ML modelleriyle ilgili olarak, Python SDK 'sını kullanarak modelleri eğmenin farklı yolları için [Azure Machine Learning modelleri eğitme](concept-train-machine-learning-model.md#python-sdk) konusuna bakın.

## <a name="data-parallelism"></a>Veri paralelliği

Veri paralelliği, iki dağıtılmış eğitim yaklaşımını uygulamanın en kolay yoludur ve çoğu kullanım durumu için yeterlidir.

Bu yaklaşımda veriler, bölüm sayısının işlem kümesinde kullanılabilir düğümlerin toplam sayısına eşit olduğu bölümlere ayrılır. Model bu çalışan düğümlerinin her birine kopyalanır ve her çalışan, verilerin kendi alt kümesinde çalışır. Her bir düğümün eğitilen modeli desteklemek için kapasiteye sahip olması gerektiğini unutmayın. Bu, modelin her düğüme tamamen sığması gerekir. Aşağıdaki diyagramda bu yaklaşımın görsel bir gösterimi sunulmaktadır.

![Veri-paralellik-kavram-diyagram](./media/concept-distributed-training/distributed-training.svg)

Her düğüm, kendi eğitim örnekleri ve etiketli çıkışlar için tahminleri arasındaki hataları bağımsız olarak hesaplar. Sırasıyla, her düğüm kendi modelini hatalara göre güncelleştirir ve ilgili modellerini güncelleştirmek için diğer düğümlerdeki tüm değişikliklerini iletmelidir. Bu, çalışan düğümlerinin, tutarlı bir modele eğitim aldığından emin olmak için Batch hesaplamasının sonundaki model parametrelerini veya degradelerini eşitlemesini gerektiren anlamına gelir. 

## <a name="model-parallelism"></a>Model paralelliği

Ağ paralelliği olarak da bilinen model paralelliği ' nde, model farklı düğümlerde eşzamanlı olarak çalışabilecek farklı parçalara bölünür ve her biri aynı verilerde çalışır. Bu yöntemin ölçeklenebilirliği, algoritmanın görev paralel hale getirme derecesine bağlıdır ve veri paralelliği uygulamak daha karmaşıktır. 

Model paralelliği ' nde, çalışan düğümlerinin, genellikle her bir ileri veya geri yayılma adımı için yalnızca bir kez paylaşılan parametreleri eşitlenmesi gerekir. Ayrıca, aynı eğitim verilerinde modelin alt bölümünde her düğüm üzerinde çalışırken daha büyük modeller sorun değildir.

## <a name="next-steps"></a>Sonraki adımlar

* Python SDK ile [model eğitimi için işlem hedeflerini nasıl kullanacağınızı](how-to-set-up-training-targets.md) öğrenin.
* Teknik bir örnek için bkz. [başvuru mimarisi senaryosu](/azure/architecture/reference-architectures/ai/training-deep-learning).
* [TensorFlow Ile ml modellerini eğitme](how-to-train-tensorflow.md).
* [PyTorch Ile ml modellerini eğitme](how-to-train-pytorch.md).