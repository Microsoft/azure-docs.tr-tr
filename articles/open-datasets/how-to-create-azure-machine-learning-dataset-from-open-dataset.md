---
title: Azure açık veri kümeleri ile veri kümeleri oluşturma
titleSuffix: Azure Open Datasets
description: Azure açık veri kümelerinden Azure Machine Learning veri kümesi oluşturmayı öğrenin.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 1ed37ebd798f7ebef59ea48ce7e2084b331d2bba
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065421"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure açık veri kümelerinde Azure Machine Learning veri kümeleri oluşturma

Bu makalede, [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) veri kümeleri ve [Azure açık veri kümeleri](./index.yml)ile yerel veya uzak makine öğrenimi denemeleri nasıl seçeceğinizi öğreneceksiniz. 

Azure Machine Learning bir veri [kümesi](../machine-learning/how-to-create-register-datasets.md)oluşturarak, veri kaynağı konumuna, meta verilerinin bir kopyasıyla birlikte bir başvuru oluşturursunuz. Veri kümeleri geç olarak değerlendirildiğinden ve veriler mevcut konumunda kaldığı için
* Ek depolama maliyeti yoktur.
* Özgün veri kaynaklarınızı yanlışlıkla değiştirmeyi riske eklemeyin. 
* ML iş akışı performans hızlarını geliştirir.

Veri kümelerinin Azure Machine Learning genel veri erişimi iş akışına uygun olduğunu anlamak için, [güvenli erişim verileri](../machine-learning/concept-data.md#data-workflow) makalesine bakın.

Azure açık veri kümeleri, tahmine dayalı çözümlerinizi zenginleştirmek ve bunların doğruluğunu artırmak üzere senaryoya özgü özellikler eklemek için kullanabileceğiniz, seçkin ortak veri kümeleridir. Makine öğrenimi modellerini eğmenize yardımcı olabilecek ortak etki alanı verilerine yönelik [açık veri kümeleri kataloğuna](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) bakın, örneğin:

* [gü](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [görselleştirmenizdeki](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [lerin](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [Ortak Güvenlik](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

Açık veri kümeleri bulutta Microsoft Azure ve hem [Azure Machine Learning Python SDK](#create-datasets-with-the-sdk) hem de [Azure Machine Learning Studio](#create-datasets-with-the-studio)'ya dahil edilmiştir.


## <a name="prerequisites"></a>Önkoşullar

Bu makalede şunlar gerekir:

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

* [Azure Machine Learning çalışma alanı](../machine-learning/how-to-manage-workspace.md).

* Paketi içeren [Python için Azure Machine Learning SDK 'sı yüklendi](/python/api/overview/azure/ml/install) `azureml-datasets` .

    * Tümleşik Not defterleri ve SDK 'nın zaten yüklü olduğu tam olarak yapılandırılmış ve yönetilen bir geliştirme ortamı olan [Azure Machine Learning işlem örneği](../machine-learning/how-to-create-manage-compute-instance.md)oluşturun.

    **OR**

    * Kendi Python ortamınızda çalışın ve [bu yönergelerle](/python/api/overview/azure/ml/install)SDK 'yı kendiniz de yüklersiniz.

> [!NOTE]
> Bazı veri kümesi sınıflarının yalnızca 64 bitlik Python ile uyumlu olan azureml-dataprep paketine bağımlılıkları vardır. Linux kullanıcıları için, bu sınıflar yalnızca şu dağıtımlara göre desteklenir: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), demı (8, 9) ve CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>SDK ile veri kümeleri oluşturma

Python SDK 'sında Azure Open DataSet sınıfları aracılığıyla Azure Machine Learning veri kümeleri oluşturmak için paketini ile yüklediğinizden emin olun `pip install azureml-opendatasets` . Her ayrık veri kümesi SDK 'daki kendi sınıfıyla temsil edilir ve bazı sınıflar Azure Machine Learning [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)veya her ikisi olarak kullanılabilir. Sınıfların tam listesi için [başvuru belgelerine](/python/api/azureml-opendatasets/azureml.opendatasets) bakın `opendatasets` .

`opendatasets` `TabularDataset` `FileDataset` Dosyaları doğrudan yönetmenize ve/veya indirmenizi sağlayan bir veya olarak belirli sınıfları alabilirsiniz. Diğer sınıflar **yalnızca** `get_tabular_dataset()` `get_file_dataset()` Python SDK 'sindeki sınıfından veya işlevlerini kullanarak bir veri kümesi alabilir `Dataset` .

Aşağıdaki kod, MNIST `opendatasets` sınıfının bir `TabularDataset` veya döndürebilir `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

Bu örnekte, diabetes `opendatasets` sınıfı yalnızca olarak kullanılabilir `TabularDataset` , bu nedenle kullanımı `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Veri kümelerini Kaydet

Çalışma alanınızdaki Azure Machine Learning bir veri kümesini kaydedin, böylece bunları başkalarıyla paylaşabilir ve çalışma alanınızda denemeleri genelinde yeniden kullanabilirsiniz. Açık veri kümelerinde oluşturulan bir Azure Machine Learning veri kümesini kaydettiğinizde, hiçbir veri hemen indirilir, ancak istendiğinde (örneğin, eğitim sırasında) merkezi bir depolama konumundan erişilir.

Veri kümelerinizi bir çalışma alanı ile kaydetmek için [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#register-workspace--name--description-none--tags-none--create-new-version-false-) yöntemini kullanın. 

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Studio ile veri kümeleri oluşturma

Ayrıca, tüm beceri seviyelerinin veri bilimi senaryolarına yönelik veri bilimi senaryoları gerçekleştirmek için Machine Learning araçları 'nı içeren birleştirilmiş bir Web arabirimi olan [Azure Machine Learning Studio](https://ml.azure.com)Ile Azure açık veri kümelerinden Azure Machine Learning veri kümeleri oluşturabilirsiniz.

> [!Note]
> Azure Machine Learning Studio aracılığıyla oluşturulan veri kümeleri otomatik olarak çalışma alanına kaydedilir.

1. Çalışma alanınızda **varlıklar**' ın altındaki **veri kümeleri** sekmesini seçin. **Veri kümesi oluştur** aşağı açılan menüsünde **açık veri kümeleri ' nden** seçim yapın.

    ![UI ile veri kümesini açma](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Kutucuğunu seçerek bir veri kümesi seçin. (Arama çubuğunu kullanarak filtreleme seçeneğiniz vardır.) **İleri ' yi** seçin.

    ![Veri kümesi seçin](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Veri kümesinin kaydedileceği bir ad seçin ve isteğe bağlı olarak, kullanılabilir filtreleri kullanarak verileri filtreleyin. Bu durumda, **genel tatiller** veri kümesi için zaman aralığını bir yıla, ülke kodunu Ise yalnızca bizimle filtreleyerek filtreleyebilirsiniz. Alan açıklamaları ve tarih aralıkları gibi veri ayrıntıları için [Azure açık veri kümeleri kataloğuna](https://azure.microsoft.com/services/open-datasets/catalog) bakın. **Oluştur**’u seçin.

    ![Veri kümesi params 'i ayarlama ve veri kümesi oluşturma](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Veri kümesi artık veri **kümeleri** altında çalışma alanınızda kullanılabilir. Bunu, oluşturduğunuz diğer veri kümeleriyle aynı şekilde kullanabilirsiniz.


## <a name="access-datasets-for-your-experiments"></a>Denemeleri için veri kümelerine erişin

ML modellerinizi eğitmek için Machine Learning denemeleri 'te veri kümelerinizi kullanın. [Veri kümeleriyle eğitme hakkında daha fazla bilgi edinin](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Örnek not defterleri

Açık veri kümesi işlevlerinin örnekleri ve gösterileri için bkz. bu [örnek Not defterleri](samples.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Ilk ml modelinizi eğitme](../machine-learning/tutorial-1st-experiment-sdk-train.md).

* [Veri kümeleriyle eğitme](../machine-learning/how-to-train-with-datasets.md).

* [Bir Azure Machine Learning veri kümesi oluşturun](../machine-learning/how-to-create-register-datasets.md).
