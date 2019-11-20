---
title: Veri kümesi sürümü oluşturma
titleSuffix: Azure Machine Learning service
description: Veri kümelerinizi en iyi şekilde nasıl yükleyeceğinizi ve makine öğrenimi ardışık düzenleri ile sürüm oluşturmanın nasıl çalıştığını öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: ''
ms.openlocfilehash: 426a93473b969c166a847374d1b4c039055e92d5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716106"
---
# <a name="version-and-track-datasets-in-experiments"></a>Denemeleri içinde veri kümelerini sürüm ve izleme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, reproducibility için Azure Machine Learning veri kümelerini nasıl kullanacağınızı ve izleyeceğinizi öğreneceksiniz. Veri kümesi sürümü oluşturma, gelecekteki denemeleri için veri kümesinin belirli bir sürümünü uygulayabilmeniz için verilerinizin durumuna yer işaretinin bir yoludur.

Tipik sürüm oluşturma senaryoları:

* Yeniden eğitim için yeni veriler kullanılabilir olduğunda
* Farklı veri hazırlama veya özellik Mühendisliği yaklaşımları uygularken

## <a name="prerequisites"></a>Ön koşullar

Bu öğretici için şunlar gerekir:

- [Python için Azure Machine Learning SDK 'sı yüklendi](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Bu SDK, [azureml veri kümesi](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) paketini içerir.
    
- [Azure Machine Learning çalışma alanı](concept-workspace.md). Aşağıdaki kodu çalıştırarak mevcut bir tane alın veya [Yeni bir çalışma alanı oluşturun](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning veri kümesi](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Veri kümesi sürümlerini kaydetme ve alma

Bir veri kümesini kaydederek denemeleri ve iş arkadaşlarınızla paylaşabilir, yeniden kullanabilir ve paylaşabilirsiniz. Aynı ada sahip birden çok veri kümesini kaydedebilir ve adı ve sürüm numarasına göre belirli bir sürümü alabilirsiniz.

### <a name="register-a-dataset-version"></a>Veri kümesi sürümünü kaydetme

Aşağıdaki kod, `create_new_version` parametresini `True`ayarlayarak `titanic_ds` veri kümesinin yeni bir sürümünü kaydeder. Çalışma alanına kayıtlı mevcut `titanic_ds` veri kümesi yoksa, kod, `titanic_ds` adlı yeni bir veri kümesi oluşturur ve sürümünü 1 olarak ayarlar.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Bir veri kümesini ada göre al

Varsayılan olarak, `Dataset` sınıfındaki [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) yöntemi, çalışma alanına kayıtlı veri kümesinin en son sürümünü döndürür. 

Aşağıdaki kod `titanic_ds` veri kümesinin 1. sürümünü alır.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Sürüm oluşturma en iyi uygulaması

Bir veri kümesi sürümü oluşturduğunuzda, çalışma alanıyla birlikte verilerin ek bir kopyasını *oluşturuyoruz.* Veri kümeleri depolama hizmetinizdeki verilere başvurduğundan, depolama hizmetiniz tarafından yönetilen tek bir Truth kaynağı vardır.

>[!IMPORTANT]
> Veri kümeniz tarafından başvurulan verilerin üzerine yazılır veya silinirse, veri kümesinin belirli bir sürümünü çağırmak *değişikliği döndürmez.*

Verileri bir veri kümesinden yüklediğinizde, veri kümesi tarafından başvurulan geçerli veri içeriği her zaman yüklenir. Her veri kümesi sürümünün tekrarlanabilir olduğundan emin olmak istiyorsanız, veri kümesi sürümü tarafından başvurulan veri içeriğini değiştirmenizi öneririz. Yeni veriler geldiğinde, yeni veri dosyalarını ayrı bir veri klasörüne kaydedin ve sonra bu yeni klasörden verileri dahil etmek için yeni bir veri kümesi sürümü oluşturun.

Aşağıdaki görüntü ve örnek kod, veri klasörlerinizi yapılandırmak ve bu klasörlere başvuran veri kümesi sürümleri oluşturmak için önerilen yöntemi gösterir:

![Klasör yapısı](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>İşlem hattı çıkış veri kümesi sürümü

Bir veri kümesini her bir Machine Learning ardışık düzen adımının girişi ve çıktısı olarak kullanabilirsiniz. İşlem hatlarını yeniden çalıştırdığınızda, her bir ardışık düzen adımının çıktısı yeni bir veri kümesi sürümü olarak kaydedilir.

Machine Learning işlem hatları, işlem hattı her yeniden çalıştırıldığında her adımın çıkışını yeni bir klasöre doldurduğundan, sürümlü çıkış veri kümeleri tekrarlanabilir.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Denemeleri içinde veri kümelerini izleme

Her bir Machine Learning denemesi için, girdi olarak kullanılan veri kümelerini kayıtlı modelin `Run` nesnesi aracılığıyla kolayca izleyebilirsiniz.

Modelleri veri kümelerine kaydetmek için aşağıdaki kodu kullanın:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Kayıttan sonra, Python veya [Azure Machine Learning Studio](https://ml.azure.com/)kullanarak veri kümesiyle kayıtlı modellerin listesini görebilirsiniz.

Aşağıdaki kod, deneme çalıştırması ile hangi giriş veri kümelerinin kullanıldığını izlemek için [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) yöntemini kullanır:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

Ayrıca, [Azure Machine Learning Studio](https://ml.azure.com/)kullanarak `input_datasets` de bulabilirsiniz. 

Aşağıdaki görüntüde Azure Machine Learning Studio bir deneyin giriş veri kümesinin nerede bulunacağı gösterilmektedir. Bu örnekte, **denemeleri** bölmenize gidin ve denemenizin belirli bir çalışması için **özellikler** sekmesini açın `keras-mnist`.

![Giriş veri kümeleri](media/how-to-version-datasets/input-datasets.png)

Veri kümenizi kullanan modelleri de bulabilirsiniz. Aşağıdaki görünüm, **varlıklar**altındaki **veri kümeleri** bölmesinden yapılır. Veri kümesini seçin ve ardından bu veri kümesini kullanan modellerin listesi için **modeller** sekmesini seçin. 

![Giriş veri kümesi modelleri](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Veri kümeleriyle eğitme](how-to-train-with-datasets.md)
* [Daha fazla örnek veri kümesi Not defteri](https://aka.ms/dataset-tutorial)
