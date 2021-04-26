---
title: ML işlem hatlarını oluşturma ve çalıştırma
titleSuffix: Azure Machine Learning
description: Machine Learning (ML) aşamalarını birlikte barındıran iş akışlarını oluşturmak ve yönetmek için makine öğrenimi işlem hatlarını oluşturun ve çalıştırın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 38fd5b779c3a8ae71c2e4fafcaf65921b1be3f93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642264"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Azure Machine Learning SDK ile makine öğrenimi işlem hatları oluşturma ve çalıştırma

Bu makalede, [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)kullanarak [makine öğrenimi ardışık düzenleri](concept-ml-pipelines.md) oluşturmayı ve çalıştırmayı öğreneceksiniz. Çeşitli ML aşamalarını biraraya uygulayan bir iş akışı oluşturmak için **ml işlem hatlarını** kullanın. Ardından, daha sonra erişmek veya başkalarıyla paylaşmak için bu işlem hattını yayımlayın. Modelinizin gerçek dünyada nasıl çalıştığını görmek ve veri kayması 'nı algılamak için ML işlem hatlarını izleyin. ML işlem hatları, çeşitli hesaplar kullanılarak, bunları yeniden çalıştırmak yerine adımları yeniden kullanarak ve diğer kişilerle ML iş akışlarını paylaşarak Batch Puanlama senaryolarında idealdir.

Bu makale bir öğretici değildir. İlk işlem hattınızı oluşturma hakkında yönergeler için bkz. [öğretici: Batch Puanlama için Azure Machine Learning işlem hattı oluşturma](tutorial-pipeline-batch-scoring-classification.md) veya [Python 'da Azure Machine Learning ARDıŞıK düzeninde otomatik ml kullanma](how-to-use-automlstep-in-pipelines.md). 

ML görevlerinin CI/CD otomasyonu için [Azure](/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2fmachine-learning%2fservice%2fcontext%2fml-context&tabs=yaml) işlem hattı adlı farklı türde bir işlem hattı kullanabilirsiniz, ancak bu işlem hattı türü çalışma alanınızda depolanmaz. [Bu farklı işlem hatlarını karşılaştırın](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Oluşturduğunuz ML ardışık düzenleri Azure Machine Learning [çalışma](how-to-manage-workspace.md)alanınızın üyeleri tarafından görülebilir. 

ML ardışık düzenleri işlem hedefleri üzerinde yürütülür (bkz. [Azure Machine Learning işlem hedefleri nedir](./concept-compute-target.md)). İşlem hatları, desteklenen [Azure depolama](../storage/index.yml) konumlarına ve bu konumlardan veri okuyup yazabilir.

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

## <a name="prerequisites"></a>Önkoşullar

* Tüm işlem hattı kaynaklarınızın tutulacağı bir [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md) oluşturun.

* Azure Machine Learning SDK 'yı yüklemek için [geliştirme ortamınızı yapılandırın](how-to-configure-environment.md) veya SDK 'nın zaten yüklü olduğu bir [Azure Machine Learning işlem örneği](concept-compute-instance.md) kullanın.

Çalışma alanınızı ekleyerek başlayın:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Makine öğrenimi kaynaklarını ayarlama

ML işlem hattı çalıştırmak için gereken kaynakları oluşturma:

* İşlem hattı adımlarında gereken verilere erişmek için kullanılan bir veri deposu ayarlayın.

* Bir `Dataset` nesnesi, bir veri deposu içinde bulunan veya ' de erişilebilir olan kalıcı verileri işaret etmek üzere yapılandırın. İşlem `OutputFileDatasetConfig` hattı adımları arasında geçirilen geçici veriler için bir nesne yapılandırın. 

* Ardışık düzen adımlarınızın çalıştırılacağı [işlem hedeflerini](concept-azure-machine-learning-architecture.md#compute-targets) ayarlayın.

### <a name="set-up-a-datastore"></a>Veri deposu ayarlama

Bir veri deposu, işlem hattının erişim için verileri depolar. Her çalışma alanının varsayılan bir veri deposu vardır. Daha fazla veri deposu kaydedebilirsiniz. 

Çalışma alanınızı oluşturduğunuzda, [Azure dosyaları](../storage/files/storage-files-introduction.md) ve [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md) alanı çalışma alanına eklenir. Azure Blob depolamaya bağlanmak için varsayılan bir veri deposu kaydedilir. Daha fazla bilgi edinmek için bkz. [Azure dosyaları, Azure Blob 'ları veya Azure diskleri ne zaman kullanılacağına karar verme](../storage/common/storage-introduction.md). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Adımlar genellikle verileri kullanır ve çıkış verileri oluşturur. Bir adım, model gibi verileri, model ve bağımlı dosyaları içeren bir dizini veya geçici verileri oluşturabilir. Bu veriler daha sonra işlem hattının ilerleyen adımları için kullanılabilir. İşlem hattınızı verilerinize bağlama hakkında daha fazla bilgi edinmek için [verilere erişme ve veri](how-to-access-data.md) [kümelerini kaydetme](how-to-create-register-datasets.md)makalelerine bakın. 

### <a name="configure-data-with-dataset-and-outputfiledatasetconfig-objects"></a>`Dataset`Ve nesneleriyle veri yapılandırma `OutputFileDatasetConfig`

Bir işlem hattına veri sağlamanın tercih edilen yolu bir [veri kümesi](/python/api/azureml-core/azureml.core.dataset.Dataset) nesnesidir. Nesne, veya bir veri `Dataset` deposundan veya Web URL 'sinde erişilebilir olan verileri işaret eder. `Dataset`Sınıf soyuttur, bu nedenle bir veya daha fazla dosyaya başvuruda bulunan bir veya `FileDataset` `TabularDataset` daha fazla veri içeren bir veya daha fazla dosyadan oluşturulan bir örneğini (bir veya daha fazla dosyaya başvuran) oluşturacaksınız.

`Dataset` [From_files](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) veya [from_delimited_files](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-)gibi bir using yöntemleri oluşturursunuz.

```python
from azureml.core import Dataset

my_dataset = Dataset.File.from_files([(def_blob_store, 'train-images/')])
```

Ara veriler (veya bir adımın çıktısı) bir [Outputfiledatasetconfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) nesnesiyle temsil edilir. `output_data1` bir adımın çıktısı olarak üretilir. İsteğe bağlı olarak, bu veriler çağırarak bir veri kümesi olarak kaydedilebilir `register_on_complete` . Tek bir adımda oluşturun `OutputFileDatasetConfig` ve bunu başka bir adıma giriş olarak kullanırsanız, adımlar arasındaki veri bağımlılığı işlem hattında örtük bir yürütme sırası oluşturur.

`OutputFileDatasetConfig` nesneler bir dizin döndürür ve varsayılan olarak çıktıyı çalışma alanının varsayılan veri deposuna yazar.

```python
from azureml.data import OutputFileDatasetConfig

output_data1 = OutputFileDatasetConfig(destination = (datastore, 'outputdataset/{run-id}'))
output_data_dataset = output_data1.register_on_complete(name = 'prepared_output_data')

```

> [!IMPORTANT]
> Kullanılarak depolanan ara veri `OutputFileDatasetConfig` , Azure tarafından otomatik olarak silinmez.
> İşlem hattı çalıştırmasının sonunda ara verileri program aracılığıyla silmeniz, kısa veri saklama ilkesiyle bir veri deposu kullanmanız veya el ile temizlemeniz düzenli olarak yapmanız gerekir.

> [!TIP]
> Yalnızca elinizde işe uygun dosyaları karşıya yükleyin. Veri dizinindeki dosyalarda yapılan her değişiklik, yeniden kullanım belirtilmiş olsa bile işlem hattının bir sonraki çalıştırılması sırasında adımı yeniden çalıştırma nedeni olarak görülür. 

## <a name="set-up-a-compute-target"></a>İşlem hedefi ayarlama


Azure Machine Learning, __işlem__ (veya __işlem hedefi__) terimi, Machine Learning ardışık düzeninde hesaplama adımlarını gerçekleştiren makinelere veya kümelere başvurur.   İşlem hedeflerinin tam listesi için bkz. [model eğitimi için işlem hedefleri](concept-compute-target.md#train) ve bunları oluşturma ve çalışma alanınıza iliştirme için [işlem hedefleri oluşturma](how-to-create-attach-compute-studio.md) .   Bir işlem hedefi oluşturma ve ekleme işlemi, bir modeli eğitmek veya bir ardışık düzen adımını çalıştırmak için aynıdır. İşlem hedefini oluşturup iliştirdikten sonra işlem `ComputeTarget` [hattı adımınızda](#steps)nesnesini kullanın.

> [!IMPORTANT]
> Uzak işlerin içinden işlem hedeflerinde yönetim işlemleri gerçekleştirme desteklenmez. Makine öğrenmesi işlem hatları uzak iş olarak gönderildiğinden işlem hattının içinden işlem hedeflerinde yönetim işlemlerini kullanmayın.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning işlem

Adımlarınızı çalıştırmak için bir Azure Machine Learning işlem oluşturabilirsiniz. Diğer işlem hedeflerine yönelik kod, türe bağlı olarak biraz farklı parametrelerle benzerdir. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

## <a name="configure-the-training-runs-environment"></a>Eğitim çalıştırmasının ortamını yapılandırma

Bir sonraki adım, uzaktan eğitim çalıştırmasının eğitim adımları için gereken tüm bağımlılıklara sahip olduğundan emin olmanızı sağlamak. Bağımlılıklar ve çalışma zamanı bağlamı bir nesne oluşturularak ve yapılandırılarak ayarlanır `RunConfiguration` . 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

Yukarıdaki kodda bağımlılıkları işlemek için iki seçenek gösterilmektedir. , İle gösterildiği gibi `USE_CURATED_ENV = True` , yapılandırma, seçkin bir ortama dayalıdır. Seçkin ortamlar, ortak bağımlı kitaplıklar ile "önceden kullanıma hazır" olur ve çevrimiçi hale getirmek için daha hızlı olabilir. Seçkin ortamlar [Microsoft Container Registry](https://hub.docker.com/publishers/microsoftowner)önceden oluşturulmuş Docker görüntülerine sahiptir. Daha fazla bilgi için bkz. [Azure Machine Learning seçkin ortamlar](resource-curated-environments.md).

Olarak değiştirirseniz gerçekleştirilecek yol, `USE_CURATED_ENV` `False` bağımlılıklarınızı açıkça ayarlamaya yönelik bir model gösterir. Bu senaryoda, yeni bir özel Docker görüntüsü oluşturulur ve kaynak grubunuzda bir Azure Container Registry kaydedilir (bkz. [Azure 'da özel Docker kapsayıcısı kayıt defterlerine giriş](../container-registry/container-registry-intro.md)). Bu görüntünün oluşturulması ve kaydedilmesi birkaç dakika sürer.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>İşlem hattı adımlarınızı oluşturun

İşlem kaynağı ve ortamı oluşturulduktan sonra, işlem hattının adımlarını tanımlamaya hazırsanız. Azure Machine Learning SDK aracılığıyla sunulan birçok yerleşik adım vardır. Bu, [ `azureml.pipeline.steps` paketin başvuru belgelerinde](/python/api/azureml-pipeline-steps/azureml.pipeline.steps)görebileceğiniz gibidir. En esnek sınıf, Python betiği çalıştıran [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)'dir.

```python
from azureml.pipeline.steps import PythonScriptStep
dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"
# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Yukarıdaki kodda tipik bir ilk işlem hattı adımı gösterilmektedir. Veri hazırlama kodunuz bir alt dizinde (Bu örnekte, `"prepare.py"` dizininde `"./dataprep.src"` ) bulunur. İşlem hattı oluşturma sürecinin bir parçası olarak, bu dizin sıkıştırıldı ve ' a yüklenir `compute_target` ve bu adımda, için değer olarak belirtilen betiği çalıştırılır `script_name` .

`arguments`Değerler, adımın giriş ve çıkışları belirler. Yukarıdaki örnekte, taban çizgisi verileri veri kümesidir `my_dataset` . Kod, olarak belirttiğinden karşılık gelen veriler işlem kaynağına indirilir `as_download()` . Komut dosyası, `prepare.py` veri dönüştürme görevlerinin her şey için uygun olduğunu ve bu verileri `output_data1` türüne göre çıkış yapar `OutputFileDatasetConfig` . Daha fazla bilgi için bkz. [ml ardışık düzen adımlarına ve arasında veri taşıma (Python)](how-to-move-data-in-out-of-pipelines.md). Bu adım, yapılandırma kullanılarak tarafından tanımlanan makinede çalıştırılır `compute_target` `aml_run_config` . 

`allow_reuse`Gereksiz yeniden çalışma olanağı sunan çevikliği ortadan kaldıran önceki sonuçların () yeniden kullanılması, işbirliği yapılan bir ortamda işlem hattı kullanılırken anahtardır. Script_name, girişleri ve bir adımın parametreleri aynı kaldığında, yeniden kullanım varsayılan davranıştır. Yeniden kullanım izni verildiğinde, önceki çalıştırmanın sonuçları hemen sonraki adıma gönderilir. `allow_reuse`Olarak ayarlanırsa `False` , işlem hattı yürütmesi sırasında bu adım için her zaman yeni bir çalıştırma oluşturulacaktır.

Tek bir adımla işlem hattı oluşturmak mümkündür, ancak neredeyse her zaman genel işleminizi birkaç adıma bölmeyi tercih edersiniz. Örneğin, veri hazırlama, eğitim, model karşılaştırma ve dağıtım için adımlara sahip olabilirsiniz. Örneğin, yukarıda belirtilen tarihten sonra bir `data_prep_step` sonraki adım eğitim olabilir:

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = OutputFileDatasetConfig(name = "training_results",
    destination = def_blob_store)

    
train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1.as_input(), "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Yukarıdaki kod, veri hazırlama adımındaki koda benzerdir. Eğitim kodu, veri hazırlama kodundan ayrı bir dizindir. `OutputFileDatasetConfig`Veri hazırlama adımının çıktısı, `output_data1` eğitim adımında _giriş_ olarak kullanılır. `OutputFileDatasetConfig` `training_results` Sonraki karşılaştırma veya dağıtım adımının sonuçlarını tutmak için yeni bir nesne oluşturulur. 

Diğer kod örnekleri için bkz. [bir adım ml işlem hattı oluşturma](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb) ve [çalışma tamamlandıktan sonra verileri veri depolarına geri yazma](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

Adımlarınızı tanımladıktan sonra, bu adımların bazılarını veya tümünü kullanarak işlem hattını oluşturursunuz.

> [!NOTE]
> Adımları tanımlarken veya işlem hattını oluştururken Azure Machine Learning hiçbir dosya veya veri yüklenmedi. [Denemeler. gönder ()](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-)öğesini çağırdığınızda dosyalar yüklenir.

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

### <a name="use-a-dataset"></a>Veri kümesi kullanma 

Azure Blob depolama, Azure dosyaları, Azure Data Lake Storage 1., Azure Data Lake Storage 2., Azure SQL veritabanı ve PostgreSQL için Azure veritabanı 'nda oluşturulan veri kümeleri herhangi bir işlem hattı adımına giriş olarak kullanılabilir. Çıktıyı bir [Datatransferstep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep), [databricksstep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep)öğesine yazabilir veya belirli bir veri deposuna veri yazmak Istiyorsanız [outputfiledatasetconfig](/python/api/azureml-core/azureml.data.outputfiledatasetconfig)komutunu kullanın. 

> [!IMPORTANT]
> Kullanılarak bir veri deposuna çıkış verileri yazmak `OutputFileDatasetConfig` yalnızca Azure blob, Azure dosya paylaşımında, ADLS Gen 1 ve Gen 2 veri depolarında desteklenir. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Daha sonra [Run.input_datasets](/python/api/azureml-core/azureml.core.run.run#input-datasets) sözlüğünü kullanarak işlem hattınızda veri kümesini elde edersiniz.

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Satır `Run.get_context()` vurgulamaya değer. Bu işlev `Run` geçerli deneysel çalıştırmayı temsil eden bir alır. Yukarıdaki örnekte, kayıtlı bir veri kümesini almak için kullanırız. Nesnenin diğer yaygın kullanımı, `Run` hem denemeyi hem de deneme sürümünün bulunduğu çalışma alanını almak için: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Verileri geçirmek ve erişmek için alternatif yollar da dahil olmak üzere daha fazla ayrıntı için bkz. [ml ardışık düzen adımları (Python) arasında veri taşıma](how-to-move-data-in-out-of-pipelines.md).

## <a name="caching--reuse"></a>Önbelleğe alma & yeniden kullanım  

İşlem hatlarınızın davranışını iyileştirmek ve özelleştirmek için, önbelleğe alma ve yeniden kullanma konusunda birkaç şey yapabilirsiniz. Örneğin, şunları yapabilirsiniz:
+ Adım tanımı sırasında ayar **çalıştırma çıkışının varsayılan yeniden kullanımını devre dışı bırakın** `allow_reuse=False` . [](/python/api/azureml-pipeline-steps/) Gereksiz çalıştırmaları ortadan kaldıran çeviklik sunarak, birlikte çalışma sırasında işlem hattı kullanılırken anahtar kullanın. Ancak, yeniden kullanım dışı bırakabilirsiniz.
+ İle **çalıştırılan tüm adımlarda çıkış yeniden oluşturmayı zorla**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Varsayılan olarak, `allow_reuse` adımlar için etkinleştirilir ve `source_directory` adım tanımında belirtilen, karma hale getirilir. Bu nedenle, belirli bir adımın betiği aynı ( `script_name` , girişler ve parametreler) olarak kalırsa ve ' de başka hiçbir şey ` source_directory` değiştiyse, önceki bir adım çalıştırmasının çıkışı yeniden kullanılır, iş işleme gönderilmez ve önceki çalıştırmanın sonuçları hemen bir sonraki adımda kullanılabilir.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="submit-the-pipeline"></a>İşlem hattını gönderme

İşlem hattını gönderdiğinizde, Azure Machine Learning her adımın bağımlılıklarını denetler ve belirttiğiniz kaynak dizinin bir anlık görüntüsünü yükler. Kaynak dizin belirtilmemişse, geçerli yerel dizin yüklenir. Anlık görüntü Ayrıca çalışma alanınızdaki denemenin bir parçası olarak depolanır.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> Daha fazla bilgi için bkz. [anlık görüntüler](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Bir işlem hattını ilk kez çalıştırdığınızda Azure Machine Learning:

* Proje anlık görüntüsünü, çalışma alanıyla ilişkili blob depolamadan işlem hedefine indirir.
* İşlem hattının her adımına karşılık gelen bir Docker görüntüsü oluşturur.
* Her adım için Docker görüntüsünü kapsayıcı kayıt defterinden işlem hedefine indirir.
* `Dataset`Ve nesnelerine erişimi yapılandırır `OutputFileDatasetConfig` . `as_mount()`Erişim modu için, sigorta, sanal erişim sağlamak için kullanılır. Bağlama desteklenmiyorsa veya Kullanıcı farklı erişim olarak belirtilmişse `as_upload()` , veriler bunun yerine işlem hedefine kopyalanır.

* Adımı adım tanımında belirtilen işlem hedefi içinde çalıştırır. 
* Adım tarafından belirtilen Günlükler, STDOUT ve STDERR, ölçümler ve çıkış gibi yapıtlar oluşturur. Bu yapıtlar daha sonra karşıya yüklenir ve kullanıcının varsayılan veri deposunda tutulur.

![Bir deneme işlem hattı olarak çalıştırma diyagramı](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Daha fazla bilgi için bkz. [deneme sınıfı](/python/api/azureml-core/azureml.core.experiment.experiment) başvurusu.

## <a name="use-pipeline-parameters-for-arguments-that-change-at-inference-time"></a>Çıkarımı zamanında değişen bağımsız değişkenler için işlem hattı parametrelerini kullanma

Bazen, bir işlem hattındaki tek adımlarla ilgili bağımsız değişkenler geliştirme ve eğitim dönemiyle ilgilidir: eğitim fiyatları ve itici güç gibi şeyler veya veri ya da yapılandırma dosyalarının yolları. Bir model dağıtıldığında, Inse olduğunuz bağımsız değişkenleri dinamik olarak geçirmek isteyeceksiniz (diğer bir deyişle, modeli yanıtlamak için yapılandırdığınız sorgu!). Bu tür bağımsız değişken işlem hattı parametreleri yapmalısınız. Bunu Python 'da yapmak için, `azureml.pipeline.core.PipelineParameter` Aşağıdaki kod parçacığında gösterildiği gibi sınıfını kullanın:

```python
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="pipeline_arg", default_value="default_val")
train_step = PythonScriptStep(script_name="train.py",
                            arguments=["--param1", pipeline_param],
                            target=compute_target,
                            source_directory=project_folder)
```

### <a name="how-python-environments-work-with-pipeline-parameters"></a>Python ortamları işlem hattı parametreleriyle nasıl çalışır?

Daha önce, [eğitim çalıştırmasının ortamını yapılandırma](#configure-the-training-runs-environment)bölümünde anlatıldığı gibi, ortam durumu ve Python kitaplığı bağımlılıkları bir nesnesi kullanılarak belirtilir `Environment` . Genellikle, var olan `Environment` adı ve isteğe bağlı olarak bir sürümü belirterek mevcut bir ad belirtebilirsiniz:

```python
aml_run_config = RunConfiguration()
aml_run_config.environment.name = 'MyEnvironment'
aml_run_config.environment.version = '1.0'
```

Ancak, işlem `PipelineParameter` hattı adımlarınız için çalışma zamanında değişkenleri dinamik olarak ayarlamak üzere nesneleri kullanmayı seçerseniz, varolan bir başvuru için bu tekniği kullanamazsınız `Environment` . Bunun yerine, nesneleri kullanmak istiyorsanız `PipelineParameter` `environment` , ' ın alanını `RunConfiguration` bir nesnesine ayarlamanız gerekir `Environment` . Bu tür bir, `Environment` bunun, dış Python paketlerinin bağımlılıklarını doğru şekilde ayarlamış olduğundan emin olmak sizin sorumluluğunuzdadır.


## <a name="view-results-of-a-pipeline"></a>İşlem hattının sonuçlarını görüntüleme

Studio 'daki tüm işlem hatlarınızın ve çalıştırma ayrıntılarının listesini görüntüleyin:

1. [Azure Machine Learning Studio](https://ml.azure.com)'da oturum açın.

1. [Çalışma alanınızı görüntüleyin](how-to-manage-workspace.md#view).

1. Tüm işlem hattı çalıştırmalarını görmek için sol tarafta işlem **hatları** ' nı seçin.
 ![makine öğrenimi ardışık düzenleri listesi](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Çalıştırma sonuçlarını görmek için belirli bir işlem hattı seçin.

### <a name="git-tracking-and-integration"></a>Git izleme ve Tümleştirme

Kaynak dizinin yerel bir git deposu olduğu bir eğitim çalıştırması başlattığınızda, depo hakkındaki bilgiler çalıştırma geçmişinde depolanır. Daha fazla bilgi için bkz. [Azure Machine Learning Için git tümleştirmesi](concept-train-model-git-integration.md).

## <a name="next-steps"></a>Sonraki adımlar

- İşlem hattınızı iş arkadaşlarınızla veya müşterilerle paylaşmak için bkz. [makine öğrenimi işlem hatlarını yayımlama](how-to-deploy-pipelines.md)
- Makine öğrenimi işlem hatlarını daha fazla araştırmak için [GitHub 'da bu jupi not defterlerini](https://aka.ms/aml-pipeline-readme) kullanın
- [Azureml-işlem hatları-Core](/python/api/azureml-pipeline-core/) paketi ve [azureml-işlem hatları-adımlar](/python/api/azureml-pipeline-steps/) paketi için SDK başvuru yardımına bakın
- Hata ayıklama ve sorun giderme işlem hatları için [nasıl yapılır](how-to-debug-pipelines.md) ipuçları =
- Not defterlerini çalıştırmayı öğrenmek için bkz. [Hizmeti keşfetmek için Jupyter not defterlerini kullanma](samples-notebooks.md).
