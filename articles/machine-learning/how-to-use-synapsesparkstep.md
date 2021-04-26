---
title: Machine Learning ardışık düzeninde Apache Spark kullanma (Önizleme)
titleSuffix: Azure Machine Learning
description: Veri işleme Apache Spark kullanmak için Azure SYNAPSE Analytics çalışma alanınızı Azure Machine Learning işlem hattınızla ilişkilendirin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 2c69ec853cdeeed6f9e28fb9f2884053580ce08e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576389"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Machine Learning işlem hattınızda Apache Spark (Azure SYNAPSE Analytics tarafından desteklenir) kullanma (Önizleme)

Bu makalede, bir Azure Machine Learning ardışık düzeninde veri hazırlama adımının işlem hedefi olarak Azure SYNAPSE Analytics tarafından desteklenen Apache Spark havuzlarını nasıl kullanacağınızı öğreneceksiniz. Tek bir işlem hattının, veri hazırlama veya eğitim gibi belirli bir adıma uygun işlem kaynaklarını nasıl kullanabileceğinizi öğreneceksiniz. Spark adımında verilerin nasıl hazırlandığını ve bir sonraki adıma nasıl geçtiğini görürsünüz. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Önkoşullar

* Tüm işlem hattı kaynaklarınızın tutulacağı bir [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md) oluşturun.

* Azure Machine Learning SDK 'yı yüklemek için [geliştirme ortamınızı yapılandırın](how-to-configure-environment.md) veya SDK 'nın zaten yüklü olduğu bir [Azure Machine Learning işlem örneği](concept-compute-instance.md) kullanın.

* Azure SYNAPSE Analytics çalışma alanı ve Apache Spark havuzu oluşturun (bkz. [hızlı başlangıç: SYNAPSE Studio kullanarak sunucusuz Apache Spark havuzu oluşturma](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Azure Machine Learning çalışma alanınızı ve Azure SYNAPSE Analytics çalışma alanınızı bağlama 

Apache Spark havuzlarınızı bir Azure SYNAPSE Analytics çalışma alanında oluşturup yönetebilirsiniz. Bir Apache Spark havuzunu Azure Machine Learning çalışma alanıyla bütünleştirmek için [Azure SYNAPSE Analytics çalışma alanına bağlamanız](how-to-link-synapse-ml-workspaces.md)gerekir. 

**Bağlı hizmetler** sayfasını kullanarak Azure Machine Learning Studio Kullanıcı arabirimi aracılığıyla bir Apache Spark havuzu ekleyebilirsiniz. İşlem **Ekle** seçeneği ile **işlem** sayfası aracılığıyla da yapabilirsiniz.

Ayrıca, SDK aracılığıyla veya bir ARM şablonu aracılığıyla bir Apache Spark havuzu iliştirebilirsiniz (Bu [örnek ARM şablonuna](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)bakın). 

ARM şablonunu izlemek, bağlı hizmeti eklemek ve Apache Spark havuzunu aşağıdaki kodla eklemek için komut satırını kullanabilirsiniz:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Azure SYNAPSE Analytics çalışma alanına başarıyla bağlantı sağlamak için Azure SYNAPSE Analytics çalışma alanı kaynağında sahip rolüne sahip olmanız gerekir. Azure portal erişiminizi denetleyin.
> Bağlı hizmet, bir sistem tarafından atanmış kimliği (SAı) oluşturduğunuzda alır. Spark işini gönderebilmesi için, bu bağlantı hizmetini SYNAPSE Studio 'dan "SYNAPSE Apache Spark Administrator" rolü olarak atamanız gerekir (bkz. [SYNAPSE Studio 'Da SYNAPSE RBAC rol atamalarını yönetme](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). Ayrıca, Azure Machine Learning çalışma alanının kullanıcısına kaynak yönetimi Azure portal "katkıda bulunan" rolü sağlamanız gerekir.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Azure SYNAPSE Analytics çalışma alanınız ile Azure Machine Learning çalışma alanınız arasında bağlantı oluşturun veya alın

Çalışma alanınızdaki bağlı hizmetleri, şu gibi kodla alabilirsiniz:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

İlk olarak, `Workspace.from_config()` ' deki yapılandırmayı kullanarak Azure Machine Learning çalışma alanınıza erişir `config.json` (bkz. [öğretici: geliştirme ortamınızda Azure Machine Learning kullanmaya başlama](tutorial-1st-experiment-sdk-setup-local.md)). Daha sonra kod, çalışma alanındaki tüm bağlı hizmetleri yazdırır. Son olarak, `LinkedService.get()` adlı bağlı bir hizmeti alır `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Azure Machine Learning için Apache Spark havuzunuzu bir işlem hedefi olarak iliştirme

Machine Learning işlem hattınızdaki bir adımı desteklemek üzere Apache Spark havuzunuzu kullanmak için, `ComputeTarget` aşağıdaki kodda gösterildiği gibi işlem hattı adımı için bir olarak eklemeniz gerekir.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

İlk adım, ' i yapılandırmaktır `SynapseCompute` . `linked_service`Bağımsız değişkeni, `LinkedService` önceki adımda oluşturduğunuz veya aldığınız nesnedir. `type`Bağımsız değişken olmalıdır `SynapseSpark` . `pool_name`İçindeki bağımsız değişken, `SynapseCompute.attach_configuration()` Azure SYNAPSE Analytics çalışma alanınızdaki mevcut bir havuzla aynı olmalıdır. Azure SYNAPSE Analytics çalışma alanında Apache Spark havuzu oluşturma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: SYNAPSE Studio kullanarak sunucusuz Apache Spark havuzu oluşturma](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). Türü `attach_config` `ComputeTargetAttachConfiguration` .

Yapılandırma oluşturulduktan sonra,,, `ComputeTarget` `Workspace` `ComputeTargetAttachConfiguration` ve Machine Learning çalışma alanındaki işlem için başvurmak istediğiniz adı geçirerek bir makine öğrenimi oluşturursunuz. ' A çağrı `ComputeTarget.attach()` zaman uyumsuzdur, bu nedenle çağrı tamamlanana kadar örnek bloklar olur.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>`SynapseSparkStep`Bağlı Apache Spark havuzunu kullanan bir oluşturma

[Apache Spark havuzundaki](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) örnek Not defteri Spark işi basit bir makine öğrenimi ardışık düzeni tanımlar. İlk olarak, Not defteri, önceki adımda tanımlanan tarafından desteklenen bir veri hazırlama adımını tanımlar `synapse_compute` . Daha sonra, Not defteri bir işlem hedefi tarafından desteklenen bir eğitim adımını eğitim için daha uygun bir şekilde tanımlar. Örnek Not defteri, veri girişini ve çıktıyı göstermek için Titanic acil durum veritabanını kullanır; Aslında verileri temizetmez veya tahmine dayalı bir model yapmaz. Bu örnekte gerçek eğitim olmadığından, eğitim adımında pahalı, CPU tabanlı bir işlem kaynağı kullanılmaktadır.

Veriler `DatasetConsumptionConfig` , tablo verilerini veya dosya kümelerini içerebilen nesneler aracılığıyla makine öğrenimi ardışık düzenine akar. Veriler genellikle bir çalışma alanının veri deposundaki blob depolamadaki dosyalardan gelir. Aşağıdaki kod, bir makine öğrenimi ardışık düzeni için giriş oluşturmak üzere bazı tipik kodları göstermektedir:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

Yukarıdaki kod, dosyanın `Titanic.csv` BLOB depolama alanında olduğunu varsayar. Kod, dosyanın bir ve olarak nasıl okunacağını gösterir `TabularDataset` `FileDataset` . Bu kod yalnızca tanıtım amaçlıdır çünkü yinelenen girdileri kafa karıştırıcı, tek bir veri kaynağını hem tablo içeren bir kaynak hem de yalnızca dosya olarak yorumlama.

> [!Important]
> Bir as girişi kullanabilmek için `FileDataset` `azureml-core` sürümünüz en az olmalıdır `1.20.0` . Sınıfını kullanarak bunu belirtme `Environment` aşağıda açıklanmaktadır.

Bir adım tamamlandığında, aşağıdakine benzer bir kod kullanarak çıkış verilerini depolamayı tercih edebilirsiniz:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

Bu durumda, verileri `datastore` adlı bir dosyada depolanır `test` ve makine öğrenimi çalışma alanında `Dataset` adı ile birlikte kullanılabilir `registered_dataset` .

Verilerin yanı sıra bir işlem hattı adımında adım adım Python bağımlılıkları olabilir. Ayrı `SynapseSparkStep` nesneler, kendi tam Azure Synapse Apache Spark yapılandırmalarını da belirtebilir. Bu, `azureml-core` paket sürümünün en az olması gerektiğini belirten aşağıdaki kodda gösterilmiştir `1.20.0` . (Daha önce belirtildiği gibi, için bu gereksinimin `azureml-core` bir `FileDataset` giriş olarak kullanılması gerekir.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

Yukarıdaki kod, Azure Machine Learning işlem hattının tek bir adımını belirtir. Bu adım `environment` belirli bir sürümü belirtir `azureml-core` ve gerektiğinde diğer Conda veya PIP bağımlılıklarını ekleyebilir.

, `SynapseSparkStep` Zip olur ve yerel bilgisayardan alt dizinden karşıya yüklenir `./code` . Bu dizin işlem sunucusunda yeniden oluşturulacaktır ve adım dosyayı `dataprep.py` bu dizinden çalıştırır. `inputs` `outputs` Bu adımın ve `step1_input1` `step1_input2` `step1_output` daha önce tartışılan nesneler, ve ' dir. Betikteki bu değerlere erişmenin en kolay yolu, `dataprep.py` bunları adıyla ilişkilendirmekte `arguments` .

Oluşturucu denetim Apache Spark sonraki bağımsız değişkenler kümesi `SynapseSparkStep` . , `compute_target` `'link1-spark01'` Daha önce bir işlem hedefi olarak iliştirildiğimiz bir. Diğer parametreler, kullanmak istediğimiz belleği ve çekirdekleri belirler.

Örnek Not defteri, için aşağıdaki kodu kullanır `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Bu "veri hazırlama" betiği gerçek bir veri dönüştürmesi yapmaz, ancak verilerin nasıl alınacağını, bir Spark veri çerçevesine nasıl dönüştürüleceğini ve bazı temel Apache Spark işleme nasıl yapılacağını gösterir. Azure Machine Learning Studio çıktıyı, alt çalıştırmayı açıp **çıktılar + Günlükler** sekmesini seçerek ve `logs/azureml/driver/stdout` dosyayı açarak aşağıdaki şekilde gösterildiği gibi bulabilirsiniz.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Alt çalıştırmanın stdout sekmesini gösteren Studio ekran görüntüsü":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>İşlem hattında kullanın `SynapseSparkStep`

Aşağıdaki örnek, `SynapseSparkStep` [önceki bölümde](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool)oluşturulan çıktısını kullanır. İşlem hattındaki diğer adımlar kendi benzersiz ortamlarına sahip olabilir ve eldeki göreve uygun farklı işlem kaynaklarında çalıştırılabilir. Örnek Not defteri, küçük bir CPU kümesi üzerinde "eğitim adımını" çalıştırır:

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

Yukarıdaki kod, gerekirse yeni işlem kaynağını oluşturur. Ardından, `step1_output` sonuç eğitim adımının girişine dönüştürülür. `as_download()`Seçeneği, verilerin işlem kaynağına taşındığı anlamına gelir ve daha hızlı erişim elde edilir. Veriler, yerel işlem sabit sürücüsüne sığmayacak kadar büyükse, `as_mount()` VERILERI sigorta dosya sistemi aracılığıyla akışa almak için seçeneğini kullanabilirsiniz. `compute_target`Bu ikinci adımın, `'cpucluster'` `'link1-spark01'` veri hazırlama adımında kullandığınız kaynak değil. Bu adım, `train.py` önceki adımda kullandığınız yerine basit bir program kullanır `dataprep.py` . `train.py`Örnek not defterinde ayrıntılarını görebilirsiniz.

Tüm adımlarınızı tanımladıktan sonra işlem hattınızı oluşturup çalıştırabilirsiniz. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Yukarıdaki kod, Azure SYNAPSE Analytics ( `step_1` ) ve eğitim adımı () tarafından desteklenen Apache Spark havuzlarındaki veri hazırlama adımından oluşan bir işlem hattı oluşturur `step_2` . Azure, adımlar arasındaki veri bağımlılıklarını inceleyerek yürütme grafiğini hesaplar. Bu durumda, yalnızca gerekli olması gereken basit bir bağımlılık vardır `step2_input` `step1_output` .

İçin yapılan çağrı, `pipeline.submit` gerekirse, `synapse-pipeline` zaman uyumsuz olarak çağrılan ve zaman uyumsuz olarak çalıştırılan bir deneme başlatır. İşlem hattının içindeki tek adımlar, bu ana çalıştırmanın alt çalıştırmaları olarak çalıştırılır ve Studio 'nun denemeleri sayfasında izlenebilir ve incelenebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Makine öğrenimi işlem hatlarını yayımlama ve izleme](how-to-deploy-pipelines.md) 
* [Azure Machine Learning'i izleme](monitor-azure-machine-learning.md)
* [Python 'da Azure Machine Learning işlem hattında otomatik ML kullanma](how-to-use-automlstep-in-pipelines.md)
