---
title: ML işlem hatlarını oluşturun, çalıştırın, & izleyin
titleSuffix: Azure Machine Learning
description: Oluşturun ve Python için Azure Machine Learning SDK'sı ile işlem hattı öğrenme bir makine çalıştırın. Machine Learning (ML) aşamalarını birlikte barındıran iş akışlarını oluşturmak ve yönetmek için ML işlem hatlarını kullanın. Bu aşamalar veri hazırlama, model eğitimi, model dağıtımı ve çıkarım/Puanlama içerir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 9e731ff55aa4b37d0777cf9eefb14bb111b73070
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173998"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Azure Machine Learning SDK ile makine öğrenimi işlem hatları oluşturma ve çalıştırma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, [Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)kullanarak [makine öğrenimi ardışık düzeni](concept-ml-pipelines.md) oluşturmayı, yayımlamayı, çalıştırmayı ve izlemeyi öğreneceksiniz.  Çeşitli ML aşamalarını içeren bir iş akışı oluşturmak için **ml işlem hatlarını** kullanın ve ardından bu işlem hattını daha sonra erişmek veya başkalarıyla paylaşmak için Azure Machine Learning çalışma alanınıza yayımlayın.  ML işlem hatları, çeşitli hesaplar kullanılarak, yeniden çalıştırmak yerine adımları yeniden kullanarak ve diğer kişilerle ML iş akışlarını paylaşarak Batch Puanlama senaryolarında idealdir.

ML görevlerinin CI/CD otomasyonu için [Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) işlem hattı adlı farklı türde bir işlem hattı kullanabilirsiniz, ancak bu işlem hattı türü hiçbir şekilde çalışma alanınız içinde depolanmaz. [Bu farklı işlem hatlarını karşılaştırın](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Veri hazırlama ve model eğitimi gibi bir ML işlem hattının her aşaması bir veya daha fazla adım içerebilir.

Oluşturduğunuz ML ardışık düzenleri Azure Machine Learning [çalışma](how-to-manage-workspace.md)alanınızın üyeleri tarafından görülebilir. 

ML ardışık düzenleri, hesaplama için uzak işlem hedeflerini ve bu işlem hattı ile ilişkili ara ve nihai verilerin depolanmasını kullanır. Desteklenen [Azure depolama](https://docs.microsoft.com/azure/storage/) konumlarına ve bunlara veri okuyup yazabilir.

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

## <a name="prerequisites"></a>Önkoşullar

* Oluşturma bir [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md) tüm işlem hattı kaynakları tutmak için.

* Azure Machine Learning SDK 'yı yüklemek için [geliştirme ortamınızı yapılandırın](how-to-configure-environment.md) veya SDK 'nın zaten yüklü olduğu bir [Azure MACHINE LEARNING Not defteri VM](concept-azure-machine-learning-architecture.md#compute-instance) 'si kullanın.

Çalışma alanınızı ekleyerek başlayın:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Machine learning kaynaklarını ayarlama

ML işlem hattı çalıştırmak için gereken kaynakları oluşturma:

* İşlem hattı adımları gerekli verilere erişmek için kullanılan bir veri deposunu ayarlayın.

* Bir veri deposu içinde bulunan veya içinde erişilebilir olan verileri göstermek için bir `DataReference` nesnesi yapılandırın.

* Ayarlanan [hedefleri işlem](concept-azure-machine-learning-architecture.md#compute-targets) işlem hattı adımlarınızı çalıştırılacağı.

### <a name="set-up-a-datastore"></a>Bir veri deposu ayarlayın

Bir veri deposuna erişmek işlem hattının verileri depolar. Her bir çalışma alanı bir varsayılan veri deposu sahiptir. Ek veri depoları kaydedebilirsiniz. 

Çalışma alanınızı oluşturduğunuzda, [Azure dosyaları](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) ve [Azure Blob depolama](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) alanı çalışma alanına eklenir. Azure Blob depolamaya bağlanmak için varsayılan bir veri deposu kaydedilir. Daha fazla bilgi edinmek için bkz. [Azure dosyaları, Azure Blob 'ları veya Azure diskleri ne zaman kullanılacağına karar verme](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Bunlar işlem hatlarınızı erişilebilir olması için veri deposu, veri dosyaları veya dizinleri yükleyin. Bu örnek, BLOB depolama alanını veri deposu olarak kullanır:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Bir işlem hattı, bir veya daha fazla adımdan oluşur. Bir adım, bir işlem hedefte çalıştırma bir birimdir. Adımlar veri kaynaklarını ve "Ara" veri üretir. Bir adım, veri modeli, modeli ve bağımlı dosyaları ile bir dizin veya geçici veri oluşturabilirsiniz. Bu veriler daha sonra ardışık düzende sonraki diğer adımları için kullanılabilir.

### <a name="configure-data-reference"></a>Veri başvurusu yapılandırma

Başvurulan veri kaynağı bir işlem hattı, bir adım bir giriş olarak oluşturduğunuz. Bir veri kaynağında bir işlem hattı tarafından temsil edilen bir [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) nesne. `DataReference` Nesne işaret kendini veya bir veri deposundan erişilebilir veriler.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Ara veriler (veya bir adımın çıkış) tarafından temsil edilen bir [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) nesne. `output_data1`, bir adımın çıktısı olarak üretilir ve bir veya daha fazla sonraki adım girişi olarak kullanılır. `PipelineData`, adımlar arasında bir veri bağımlılığı sunar ve işlem hattında örtük bir yürütme sırası oluşturur. Bu nesne daha sonra işlem hattı adımları oluşturulurken kullanılacaktır.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Veri kümelerini kullanarak verileri yapılandırma

Bir dosyada veya dosya kümesinde depolanan tablo verileri varsa, bir [Tabulardataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) , `DataReference`için etkili bir alternatiftir. `TabularDataset` nesneler sürüm oluşturma, yayılma ve Özet istatistiklerini destekler. `TabularDataset`s, geç değerlendirilir (Python üreticileri gibi) ve onları bölerek veya filtreleyerek alt Kümelemeye etkilidir. `FileDataset` sınıfı, bir veya daha fazla dosyayı temsil eden benzer geç değerlendirilmiş veriler sağlar. 

[From_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-)gibi yöntemleri kullanarak bir `TabularDataset` oluşturun.

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 [From_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)kullanarak `FileDataset` oluşturursunuz.

 [& Register veri kümeleri](how-to-create-register-datasets.md) veya [Bu örnek Not defteri](https://aka.ms/tabulardataset-samplenotebook)' nden veri kümeleriyle çalışma hakkında daha fazla bilgi edinebilirsiniz.

## <a name="set-up-compute-target"></a>İşlem Hedefi ' ayarlayın

Azure Machine Learning, hesaplama __(veya__ __işlem hedefi__) terimi, Machine Learning ardışık düzeninde hesaplama adımlarını gerçekleştiren makinelere veya kümelere başvurur.   İşlem hedeflerinin tam listesi için bkz. [model eğitimi için işlem hedefleri](how-to-set-up-training-targets.md) ve bunları oluşturma ve çalışma alanınıza iliştirme.  Bir işlem hedefi oluşturma ve ekleme işlemi, bir modeli eğitmek veya bir ardışık düzen adımını çalıştırmak olmanıza bakılmaksızın aynıdır. İşlem hedefini oluşturup iliştirdikten sonra, işlem [hattı adımınızdaki](#steps)`ComputeTarget` nesnesini kullanın.

> [!IMPORTANT]
> İşlem hedeflerinde yönetim işlemleri gerçekleştirmek uzak işlerin içinden desteklenmez. Makine öğrenimi ardışık düzenleri uzak bir iş olarak gönderildiğinden, işlem hedefleri üzerinde yönetim işlemlerini işlem hattı içinden kullanmayın.

Aşağıdakiler için işlem hedefleri oluşturma ve ekleme örnekleri verilmiştir:

* Azure Machine Learning işlem
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning işlem

Adımlarınızı çalıştırmak için bir Azure Machine Learning işlem oluşturabilirsiniz.

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

### <a id="databricks"></a>Azure Databricks

Azure Databricks, Azure bulutta Apache Spark tabanlı bir ortam olan. Bir Azure Machine Learning işlem hattı ile işlem hedefi olarak kullanılabilir.

Kullanmadan önce bir Azure Databricks çalışma alanı oluşturun. Bir çalışma alanı kaynağı oluşturmak için Azure Databricks belge [üzerinde bir Spark Işi çalıştırma](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) ' ya bakın.

Azure Databricks bir işlem hedefi olarak eklemek için aşağıdaki bilgileri sağlayın:

* __Databricks işlem adı__: Bu işlem kaynağına atamak istediğiniz ad.
* __Databricks çalışma alanı adı__: Azure Databricks çalışma alanı adı.
* __Databricks erişim belirteci__: Azure Databricks için kimlik doğrulaması yapmak için kullanılan erişim belirteci. Bir erişim belirteci oluşturmak için bkz: [kimlik doğrulaması](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) belge.

Aşağıdaki kod, Azure Machine Learning SDK ile Azure Databricks işlem hedefi olarak nasıl ekleneceğini gösterir:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Daha ayrıntılı bir örnek için GitHub 'daki [örnek bir not defteri](https://aka.ms/pl-databricks) bölümüne bakın.

### <a id="adla"></a>Azure Data Lake Analytics'i

Azure Data Lake Analytics, Azure bulutta büyük veri analiz platformudur. Bir Azure Machine Learning işlem hattı ile işlem hedefi olarak kullanılabilir.

Kullanmadan önce bir Azure Data Lake Analytics hesabı oluşturun. Bu kaynak oluşturmak için bkz [Azure Data Lake Analytics ile çalışmaya başlama](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) belge.

Data Lake Analytics işlem hedefi olarak eklemek için Azure Machine Learning SDK'yı kullanın ve aşağıdaki bilgileri sağlayın:

* __İşlem adı__: Bu işlem kaynağına atamak istediğiniz ad.
* __Kaynak grubu__: Data Lake Analytics hesabını içeren kaynak grubu.
* __Hesap adı__: Data Lake Analytics hesap adı.

Aşağıdaki kod, Data Lake Analytics işlem hedefi olarak eklemek gösterilmektedir:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Daha ayrıntılı bir örnek için GitHub 'daki [örnek bir not defteri](https://aka.ms/pl-adla) bölümüne bakın.

> [!TIP]
> Azure Machine Learning işlem hatlarını, yalnızca varsayılan Data Lake Analytics hesabı veri deposunda depolanan verilerle çalışabilirsiniz. Veri gerekirse çalışma varsayılandan farklı bir depoda, kullanabilirsiniz bir [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) eğitim önce verileri kopyalamak için.

## <a id="steps"></a>İşlem hattı adımlarınızı oluşturun

Çalışma alanınıza bir işlem hedefi oluşturup iliştirdikten sonra bir işlem hattı adımı tanımlamaya hazırsınızdır. Azure Machine Learning SDK üzerinden sunulan birçok yerleşik adım vardır. Bu adımların en temel sürümü, belirtilen bir işlem hedefinde bir Python betiği çalıştıran bir [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py).

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Gereksiz yeniden çalışma olanağı sunan çevikliği ortadan kaldıran, önceki sonuçların (`allow_reuse`) kullanılması, işbirliği ortamında işlem hatları kullanırken anahtardır. Script_name, girişleri ve bir adımın parametreleri aynı kaldığında, yeniden kullanım varsayılan davranıştır. Adımın çıkışı yeniden kullanıldığında, iş işleme gönderilmez, bunun yerine önceki çalıştırmanın sonuçları hemen sonraki adımın çalıştırmasında kullanılabilir. `allow_reuse` false olarak ayarlandıysa işlem hattı yürütme sırasında bu adım için her zaman yeni bir çalıştırma oluşturulacaktır. 

Adımlarınızı tanımladıktan sonra, bu adımların bazılarını veya tümünü kullanarak işlem hattını oluşturursunuz.

> [!NOTE]
> Adımları tanımlarken veya işlem hattını oluştururken Azure Machine Learning hiçbir dosya veya veri yüklenmedi.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

Aşağıdaki örnek, daha önce oluşturulan Azure Databricks işlem hedefini kullanır: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Veri kümesi kullanma 

İşlem hattınızda bir `TabularDataset` veya `FileDataset` kullanmak için, [as_named_input (ad)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-)çağırarak bir [Datasettüketimptionconfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) nesnesine açmanız gerekir. Bu `DatasetConsumptionConfig` nesnesini, işlem hattı adımınızda `inputs` biri olarak geçirirsiniz. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Ardından, [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) sözlüğünü kullanarak veri kümesini işlem hattınızda elde edersiniz.

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Daha fazla bilgi için bkz. [Azure-işlem hattı-adımlar paketi](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) ve işlem [hattı sınıfı](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) başvurusu.

## <a name="submit-the-pipeline"></a>İşlem hattı gönderin

İşlem hattını gönderdiğinizde, Azure Machine Learning her adımın bağımlılıklarını denetler ve belirttiğiniz kaynak dizinin bir anlık görüntüsünü yükler. Kaynak dizin belirtilirse, geçerli yerel dizine yüklenir. Anlık görüntü Ayrıca çalışma alanınızdaki denemenin bir parçası olarak depolanır.

> [!IMPORTANT]
> Dosyaların anlık görüntüye eklenmesini engellemek için, dizinde bir [. gitignore](https://git-scm.com/docs/gitignore) veya `.amlignore` dosyası oluşturun ve dosyaları bu dosyaya ekleyin. `.amlignore` dosyası [. gitignore](https://git-scm.com/docs/gitignore) dosyası ile aynı söz dizimini ve desenleri kullanır. Her iki dosya de varsa `.amlignore` dosyası önceliklidir.
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
* Bir adımda `DataReference` nesne belirtilmişse veri deposunu takar. Bağlama desteklenmiyorsa, veri işlem hedefine bunun yerine kopyalanır.
* Adımı adım tanımında belirtilen işlem hedefi içinde çalıştırır. 
* Adım tarafından belirtilen Günlükler, STDOUT ve STDERR, ölçümler ve çıkış gibi yapıtlar oluşturur. Bu yapıtlar daha sonra karşıya yüklenir ve kullanıcının varsayılan veri deposunda tutulur.

![Bir deneme işlem hattı olarak çalıştırma diyagramı](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Daha fazla bilgi için bkz. [deneme sınıfı](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) başvurusu.

### <a name="view-results-of-a-pipeline"></a>İşlem hattının sonuçlarını görüntüleme

Studio 'daki tüm işlem hatlarınızın ve çalıştırma ayrıntılarının listesini görüntüleyin:

1. [Azure Machine Learning Studio](https://ml.azure.com)'da oturum açın.

1. [Çalışma alanınızı görüntüleyin](how-to-manage-workspace.md#view).

1. Tüm işlem hattı çalıştırmalarını görmek için sol tarafta işlem **hatları** ' nı seçin.
 ![machine learning işlem hatlarını listesi](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Çalıştırma sonuçları görmek için belirli bir işlem hattını seçin.

## <a name="git-tracking-and-integration"></a>Git izleme ve Tümleştirme

Kaynak dizinin yerel bir git deposu olduğu bir eğitim çalıştırması başlattığınızda, depo hakkındaki bilgiler çalıştırma geçmişinde depolanır. Daha fazla bilgi için bkz. [Azure Machine Learning Için git tümleştirmesi](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Bir işlem hattı yayımlama

Bir işlem hattı çalıştırma için farklı girişlerle daha sonra yeniden yayımlayabilirsiniz. Önceden yayınlanmış bir işlem hattının REST uç noktası için parametreleri kabul etmek üzere, yayımlamadan önce işlem hattını parametreleştirin.

1. Bir işlem hattı parametresi oluşturmak için bir [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) varsayılan değeri olan nesne.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Bu ekleme `PipelineParameter` gibi herhangi bir işlem hattı'ndaki adımları bir parametre olarak nesne:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     target=compute_target,
     source_directory=project_folder)
   ```

3. Çağrıldığında bir parametre kabul eder Bu işlem hattı yayımlayın.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Yayımlanan bir işlem hattı çalıştırma

Yayımlanan tüm işlem hatları bir REST uç noktasına sahiptir. Bu uç nokta, Python olmayan istemciler gibi dış sistemlerden işlem hattının çalıştırılmasını çağırır. Bu uç nokta, toplu Puanlama ve yeniden eğitim senaryolarında "Managed yinelenebilirlik" öğesini sunar.

Önceki işlem hattının çalıştırılmasını çağırmak için, [Azurecliauthentication sınıf](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) başvurusunda açıklandığı gibi Azure Active Directory bir kimlik doğrulama üst bilgisi belirtecine ihtiyacınız vardır veya Azure Machine Learning not defterindeki [kimlik doğrulamasında](https://aka.ms/pl-restep-auth) daha fazla bilgi edinebilirsiniz.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Sürümlü ardışık düzen uç noktası oluşturma
Birden çok yayınlanan işlem hattı arkasında bir ardışık düzen uç noktası oluşturabilirsiniz. Bu, yayımlanmış bir işlem hattı gibi kullanılabilir ancak, ML işlem hatlarınızı yinelemek ve güncellemek için size sabit bir REST uç noktası verir.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>İşlem hattı uç noktasına iş gönderme
Bir işi bir ardışık düzen uç noktasının varsayılan sürümüne gönderebilirsiniz:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Ayrıca, belirli bir sürüme iş gönderebilirsiniz:
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Aynı REST API kullanılarak aynı şekilde gerçekleştirilebilir:
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Studio 'da yayınlanmış işlem hatlarını kullanma

Ayrıca, Studio 'dan yayımlanmış bir işlem hattı da çalıştırabilirsiniz:

1. [Azure Machine Learning Studio](https://ml.azure.com)'da oturum açın.

1. [Çalışma alanınızı görüntüleyin](how-to-manage-workspace.md#view).

1. Sol tarafta **uç noktalar**' ı seçin.

1. Üstte **ardışık düzen uç noktaları**' nı seçin.
 makine öğrenimi yayımlanan işlem hatları ![listesi](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. İşlem hattı uç noktasının önceki çalıştırmalarının sonuçlarını çalıştırmak, kullanmak veya gözden geçirmek için belirli bir işlem hattı seçin.


### <a name="disable-a-published-pipeline"></a>Yayımlanmış bir ardışık düzeni devre dışı bırakma

Yayınlanan işlem hatları listenizden bir işlem hattını gizlemek için, bunu Studio 'da veya SDK 'dan devre dışı bırakabilirsiniz:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

`p.enable()`için yeniden etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [Publishedpipeline sınıf](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) başvurusu.


## <a name="caching--reuse"></a>Önbelleğe alma & yeniden kullanım  

İşlem hatlarınızın davranışını iyileştirmek ve özelleştirmek için, önbelleğe alma ve yeniden kullanma konusunda birkaç şey yapabilirsiniz. Örneğin, şunları yapabilirsiniz:
+ Adım [tanımı](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)sırasında `allow_reuse=False` ayarlayarak **adım çalıştırma çıkışının varsayılan yeniden kullanımını devre dışı bırakın** . Gereksiz çalıştırmaları ortadan kaldıran çeviklik sunarak, birlikte çalışma sırasında işlem hattı kullanılırken anahtar kullanın. Ancak, yeniden kullanım dışı bırakabilirsiniz.
+ `hash_paths=['<file or directory']` kullanarak diğer dosyalara ve dizinlere yönelik source_directory mutlak bir yolu veya göreli yolları da içerecek şekilde, **karma kodu kodun ötesine genişletin**. 
+ `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)` **Çalıştırma içindeki tüm adımlarda çıkış yeniden oluşturmayı zorla**

Varsayılan olarak, adımlar için `allow_reuse` etkindir ve yalnızca ana betik dosyası karma hale getirilir. Bu nedenle, belirli bir adımın betiği aynı (`script_name`, girişler ve parametreler) olarak kalırsa, önceki bir adım çalıştırmasının çıkışı yeniden kullanılır, iş işleme gönderilmez ve önceki çalıştırmanın sonuçları hemen bir sonraki adımda kullanılabilir.  

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Sonraki adımlar

- Kullanım [github'da bu Jupyter not defterlerini](https://aka.ms/aml-pipeline-readme) machine learning işlem hatlarını daha fazla araştırmak için.
- [Azureml işlem hatları-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) paketi ve [azureml-işlem hatları-adımlar](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) paketi için SDK başvuru yardımına bakın.
- Hata ayıklama ve işlem hatlarında sorun [giderme hakkında ipuçları için bkz](how-to-debug-pipelines.md) .

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
