---
title: Veri kümesi sürümü oluşturma
titleSuffix: Azure Machine Learning
description: Machine Learning veri kümelerinin nasıl kullanıldığını ve makine öğrenimi ardışık düzenleri ile sürüm oluşturmanın nasıl çalıştığını öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: fde25e4ba75bfb86c9837582d7168f85335836b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520549"
---
# <a name="version-and-track-azure-machine-learning-datasets"></a>Azure Machine Learning veri kümelerini sürüm ve izleme

Bu makalede, reproducibility için Azure Machine Learning veri kümelerini nasıl kullanacağınızı ve izleyeceğinizi öğreneceksiniz. Veri kümesi sürümü oluşturma, gelecekteki denemeleri için veri kümesinin belirli bir sürümünü uygulayabilmeniz için verilerinizin durumuna yer işaretinin bir yoludur.

Tipik sürüm oluşturma senaryoları:

* Yeniden eğitim için yeni veriler kullanılabilir olduğunda
* Farklı veri hazırlama veya özellik Mühendisliği yaklaşımları uygularken

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici için şunlar gerekir:

- [Python için Azure Machine Learning SDK 'sı yüklendi](/python/api/overview/azure/ml/install). Bu SDK, [azureml veri kümesi](/python/api/azureml-core/azureml.core.dataset) paketini içerir.
    
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

Aşağıdaki kod, `titanic_ds` parametresini olarak ayarlayarak veri kümesinin yeni bir sürümünü kaydeder `create_new_version` `True` . `titanic_ds`Çalışma alanına kayıtlı mevcut bir veri kümesi yoksa, kod adıyla yeni bir veri kümesi oluşturur `titanic_ds` ve sürümünü 1 olarak ayarlar.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Bir veri kümesini ada göre al

Varsayılan olarak, sınıfındaki [get_by_name ()](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) yöntemi, `Dataset` çalışma alanına kayıtlı veri kümesinin en son sürümünü döndürür. 

Aşağıdaki kod, `titanic_ds` veri kümesinin 1. sürümünü alır.

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

![Klasör yapısı](./media/how-to-version-track-datasets/folder-image.png)

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

## <a name="version-an-ml-pipeline-output-dataset"></a>Bir ML işlem hattı çıkış veri kümesi sürümü

Her [ml ardışık düzen](concept-ml-pipelines.md) adımının giriş ve çıkış olarak bir veri kümesi kullanabilirsiniz. İşlem hatlarını yeniden çalıştırdığınızda, her bir ardışık düzen adımının çıktısı yeni bir veri kümesi sürümü olarak kaydedilir.

ML işlem hatları, her adımın çıkışını ardışık düzen yeniden her yeniden çalıştırıldığında yeni bir klasöre doldurur. Bu davranış, sürümlü çıktı veri kümelerinin tekrarlanabilir olmasını sağlar. İşlem hatlarında [veri kümeleri](./how-to-create-machine-learning-pipelines.md#steps)hakkında daha fazla bilgi edinin.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>Denemeleri 'inizdeki verileri izleme

Azure Machine Learning, denemenizin tamamında giriş ve çıkış veri kümeleri olarak verilerinizi izler.  

Aşağıda, verilerinizin **giriş veri kümesi** olarak izlendiği senaryolar verilmiştir. 

* `DatasetConsumptionConfig` `inputs` `arguments` `ScriptRunConfig` Deneme çalıştırmasının gönderilmesi sırasında nesnenizin veya parametresi aracılığıyla nesne olarak. 

* Get_by_name () veya get_by_id () gibi yöntemler betikte çağrıldığında. Bu senaryo için, çalışma alanına kaydettiğinizde veri kümesine atanan ad, görüntülenecek addır. 

Aşağıda, verilerinizin **Çıkış veri kümesi** olarak izlendiği senaryolar verilmiştir.  

* `OutputFileDatasetConfig` `outputs` Deneme çalıştırması gönderirken bir nesneyi veya parametresi aracılığıyla geçirin `arguments` . `OutputFileDatasetConfig` nesneler, ardışık düzen adımları arasında veri kalıcı hale getirmek için de kullanılabilir. Bkz [. ml ardışık düzen adımları arasında verileri taşıma.](how-to-move-data-in-out-of-pipelines.md)
  
* Betiğe bir veri kümesi kaydettirin. Bu senaryo için, çalışma alanına kaydettiğinizde veri kümesine atanan ad, görüntülenecek addır. Aşağıdaki örnekte, `training_ds` görüntülenecek addır.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* Betikte kaydedilmemiş bir veri kümesiyle alt çalıştırma gönder. Bu, anonim olarak kaydedilmiş bir veri kümesine neden olur.

### <a name="trace-datasets-in-experiment-runs"></a>Deneme çalıştırmaları 'nda veri kümelerini izleme

Her Machine Learning deneme için, deneme nesnesiyle giriş olarak kullanılan veri kümelerini kolayca izleyebilirsiniz `Run` .

Aşağıdaki kod, [`get_details()`](/python/api/azureml-core/azureml.core.run.run#get-details--) deneme çalıştırması ile hangi giriş veri kümelerinin kullanıldığını izlemek için yöntemini kullanır:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Ayrıca, `input_datasets` [Azure Machine Learning Studio 'yu]()kullanarak denemeleri adresinden bulabilirsiniz. 

Aşağıdaki görüntüde Azure Machine Learning Studio 'da bir deneyin giriş veri kümesinin nerede bulunacağı gösterilmektedir. Bu örnekte, **denemeleri** bölmenize gidin ve denemenizin belirli bir çalışması için **Özellikler** sekmesini açın `keras-mnist` .

![Giriş veri kümeleri](./media/how-to-version-track-datasets/input-datasets.png)

Modelleri veri kümelerine kaydetmek için aşağıdaki kodu kullanın:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Kayıt sonrasında, Python kullanarak veri kümesiyle kaydedilmiş modellerin listesini görebilir veya [Studio](https://ml.azure.com/)'ya gidebilirsiniz.

Aşağıdaki görünüm, **varlıklar** altındaki **veri kümeleri** bölmesinden yapılır. Veri kümesini seçin ve ardından veri kümesine kayıtlı modellerin listesi için **modeller** sekmesini seçin. 

![Giriş veri kümesi modelleri](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Veri kümeleriyle eğitme](how-to-train-with-datasets.md)
* [Daha fazla örnek veri kümesi Not defteri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)