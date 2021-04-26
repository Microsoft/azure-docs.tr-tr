---
title: ML işlem hatlarında verileri taşıma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning işlem hatlarını veri alma ve ardışık düzen adımları arasında verileri yönetme ve taşıma hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 3f6071813a8189605d632231a5f9b8942068a48a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067444"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>ML işlem hattı adımlarına ve adımlar arasında veri taşıma (Python)

Bu makalede, bir Azure Machine Learning işlem hattındaki adımlar arasında veri alma, dönüştürme ve taşıma kodu sağlanmaktadır. Verilerin Azure Machine Learning nasıl çalıştığına ilişkin genel bakış için bkz. [Azure Storage hizmetlerindeki verilere erişme](how-to-access-data.md). Azure Machine Learning işlem hatlarının avantajları ve yapısı için bkz. [Azure Machine Learning işlem hatları nedir?](concept-ml-pipelines.md).

Bu makalede nasıl yapılacağı gösterilmektedir:

- `Dataset`Önceden var olan veriler için nesneleri kullanma
- Adımlarınız içindeki verilere erişin
- `Dataset`Verileri, eğitim ve doğrulama alt kümeleri gibi alt kümelere bölme
- `OutputFileDatasetConfig`Sonraki işlem hattı adımına veri aktarmak için nesne oluşturma
- İşlem `OutputFileDatasetConfig` hattı adımlarına girdi olarak nesneleri kullanma
- `Dataset`Kalıcı hale getirmek istediğiniz yeni nesneler oluşturun `OutputFileDatasetConfig`

## <a name="prerequisites"></a>Önkoşullar

Şunlara ihtiyacınız var:

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

- [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)veya [Azure Machine Learning Studio](https://ml.azure.com/)'ya erişim.

- Azure Machine Learning çalışma alanı.
  
  [Azure Machine Learning bir çalışma alanı oluşturun](how-to-manage-workspace.md) ya da Python SDK 'sı aracılığıyla mevcut bir tane kullanın. `Workspace`Ve sınıfını içeri aktarın `Datastore` ve işlevini kullanarak dosyadaki abonelik bilgilerinizi yükleyin `config.json` `from_config()` . Bu işlev, varsayılan olarak geçerli dizindeki JSON dosyasını arar, ancak kullanarak dosyayı işaret etmek için bir yol parametresi de belirtebilirsiniz `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Önceden varolan bazı veriler. Bu makalede bir [Azure Blob kapsayıcısının](../storage/blobs/storage-blobs-overview.md)kullanımı kısaca gösterilmektedir.

- İsteğe bağlı: [Azure MACHINE LEARNING SDK ile makine öğrenimi işlem hatları oluşturma ve çalıştırma](./how-to-create-machine-learning-pipelines.md)bölümünde açıklananlar gibi mevcut bir Machine Learning işlem hattı.

## <a name="use-dataset-objects-for-pre-existing-data"></a>`Dataset`Önceden var olan veriler için nesneleri kullanma 

Bir işlem hattına veri almanın tercih edilen yolu bir [veri kümesi](/python/api/azureml-core/azureml.core.dataset%28class%29) nesnesi kullanmaktır. `Dataset` nesneler, çalışma alanı genelinde kullanılabilir kalıcı verileri temsil eder.

Nesneleri oluşturmak ve kaydettirmek için birçok yol vardır `Dataset` . Tablo veri kümeleri, bir veya daha fazla dosyada kullanılabilen sınırlandırılmış verilere yöneliktir. Dosya veri kümeleri, ikili veriler (örneğin, görüntüler) veya ayrıştırılacak veriler içindir. Nesneleri oluşturmanın en basit programlama yolları, `Dataset` çalışma alanı depolamada veya genel URL 'lerde Mevcut blobları kullanmaktır:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
```

Farklı seçeneklere ve farklı kaynaklara sahip veri kümeleri oluşturma, bunları kaydetme ve Azure Machine Learning Kullanıcı arabiriminde gözden geçirme, veri boyutunun işlem kapasitesiyle nasıl etkileşime gireceğini anlama ve bunların sürümü oluşturma hakkında daha fazla seçenek için bkz. [Azure Machine Learning veri kümeleri oluşturma](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Veri kümelerini betiğe geçirin

Veri kümesinin yolunu betiğe geçirmek için `Dataset` nesnenin `as_named_input()` metodunu kullanın. Sonuç `DatasetConsumptionConfig` nesnesini betiğe bir bağımsız değişken olarak geçirebilir ya da işlem `inputs` hattı betiğinizin bağımsız değişkenini kullanarak veri kümesini kullanarak alabilirsiniz `Run.get_context().input_datasets[]` .

Adlandırılmış bir giriş oluşturduktan sonra, erişim modunu seçebilirsiniz: `as_mount()` veya `as_download()` . Betik, veri kümenizdeki tüm dosyaları işliyorsa ve işlem kaynağınızın disk veri kümesi için yeterince büyükse, indirme erişimi modu daha iyi bir seçimdir. İndirme erişimi modu, çalışma zamanında veri akışı yükünü ortadan kaldırır. Betiğinizin bir veri kümesinin alt kümesine erişmesi veya işlem için çok büyük olması durumunda bağlama erişim modunu kullanın. Daha fazla bilgi için [bağlama ve indirme](./how-to-train-with-datasets.md#mount-vs-download) bölümünü okuyun

Bir veri kümesini ardışık düzen adımınızda geçirmek için:

1. `TabularDataset.as_named_input()` `FileDataset.as_named_input()` Bir nesne oluşturmak için veya (sonunda ' sonunda yok) kullanın `DatasetConsumptionConfig`
1. `as_mount()` `as_download()` Erişim modunu ayarlamak için veya kullanın
1. `arguments`Ya da bağımsız değişkenini kullanarak veri kümelerini ardışık düzen adımlarınızı geçirin `inputs`

Aşağıdaki kod parçacığında, bu adımları oluşturucunun içinde birleştirmenin yaygın bir deseninin gösterildiği gösterilmektedir `PythonScriptStep` : 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> Tüm bu bağımsız değişkenlerin (yani,,, `"train_data"` `"train.py"` `cluster` ve `iris_dataset` ) değerlerini kendi verilerinize göre değiştirmeniz gerekir. Yukarıdaki kod parçacığı yalnızca çağrının formunu gösterir ve bir Microsoft örneğinin parçası değildir. 

Ayrıca, ve gibi yöntemleri kullanarak `random_split()` `take_sample()` birden çok giriş oluşturabilir veya ardışık düzen adımınıza geçirilen veri miktarını azaltabilirsiniz:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Komut dosyası içindeki veri kümelerine erişin

İşlem hattı adım betiğinizin adlandırılmış girdileri, nesne içindeki bir sözlük olarak mevcuttur `Run` . Kullanarak etkin `Run` nesneyi alın `Run.get_context()` ve kullanarak adlandırılmış girişlerin sözlüğünü alın `input_datasets` . Nesneyi bağımsız değişken yerine `DatasetConsumptionConfig` bağımsız değişkenini kullanarak geçirtiniz `arguments` `inputs` kodu kullanarak verilere erişin `ArgParser` . Her iki teknik de aşağıdaki kod parçacığında gösterilmiştir.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()],
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Geçirilen değer, veri kümesi dosyalarının yolu olacak.

Ayrıca kayıtlı bir doğrudan erişim de mümkündür `Dataset` . Kayıtlı veri kümeleri kalıcı olduğundan ve bir çalışma alanı genelinde paylaşıldığından, bunları doğrudan alabilirsiniz:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> Önceki kod parçacıkları, çağrıların biçimini gösterir ve bir Microsoft örneğinin parçası değildir. Çeşitli bağımsız değişkenleri kendi projenizdeki değerlerle değiştirmeniz gerekir.

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>`OutputFileDatasetConfig`Ara veriler için kullanın

`Dataset`Nesneler yalnızca kalıcı verileri temsil ederken, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) nesne (ler) ardışık düzen adımlarında **ve** kalıcı çıkış verilerinden geçici veri çıktısı için kullanılabilir. `OutputFileDatasetConfig` BLOB depolama, FileShare, adlsgen1 veya adlsgen2 'e veri yazılmasını destekler. Bağlama modunu ve karşıya yükleme modunu destekler. Bağlama modunda, bağlı dizine yazılan dosyalar dosya kapatıldığında kalıcı olarak depolanır. Karşıya yükleme modunda, çıkış dizinine yazılan dosyalar işin sonuna yüklenir. İş başarısız olursa veya iptal edilirse, çıkış dizini karşıya yüklenmeyecektir.

 `OutputFileDatasetConfig` nesnenin varsayılan davranışı, çalışma alanının varsayılan veri deposuna yazılır. `OutputFileDatasetConfig`Nesnelerinizi `PythonScriptStep` `arguments` parametresine geçirin.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Bir çalıştırmanın sonunda nesnenizin içeriğini karşıya yüklemeyi tercih edebilirsiniz `OutputFileDatasetConfig` . Bu durumda, `as_upload()` işlevini nesneniz ile birlikte kullanın `OutputFileDatasetConfig` ve hedefteki mevcut dosyaların üzerine yazılıp yazılmayacağını belirtin. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=(blob_store, 'outputdataset')).as_upload(overwrite=False)
```

> [!NOTE]
> ' A eş zamanlı yazma `OutputFileDatasetConfig` başarısız olur. Aynı anda tek bir kullanmayı denemeyin `OutputFileDatasetConfig` . `OutputFileDatasetConfig`Dağıtılmış eğitim kullanılırken olduğu gibi çoklu işleme durumunda tek bir paylaşmayın. 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>`OutputFileDatasetConfig`Eğitim adımının çıkışları olarak kullanın

İşlem hattınızın `PythonScriptStep` adımında programın bağımsız değişkenlerini kullanarak sağlanan çıkış yollarını alabilirsiniz. Bu ilk adımsa ve çıkış verilerini başlatacaksa belirtilen yolda dizini oluşturmanız gerekir. Daha sonra içine dahil etmek istediğiniz dosyaları yazabilirsiniz `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>`OutputFileDatasetConfig`İlk olmayan adımlara giriş olarak oku

İlk işlem hattı adımı, yola bazı veriler yazar `OutputFileDatasetConfig` ve bu ilk adımın çıktısı haline gelirse, sonraki bir adımda giriş olarak kullanılabilir. 

Aşağıdaki kodda: 

* `step1_output_data` PythonScriptStep çıktısının, `step1` `my_adlsgen2` karşıya yükleme erişim modunda ADLS Gen 2 veri deposuna yazıldığını belirtir. ADLS Gen 2 veri depolarına geri veri yazmak için [rol izinlerini ayarlama](how-to-access-data.md#azure-data-lake-storage-generation-2) hakkında daha fazla bilgi edinin. 

* `step1`Tamamlandıktan sonra ve çıkış, tarafından belirtilen hedefe yazıldıktan sonra `step1_output_data` , Step2 `step1_output_data` bir giriş olarak kullanıma hazırdır. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>`OutputFileDatasetConfig`Yeniden kullanım için nesneleri kaydetme

`OutputFileDatasetConfig`Deneme sürenizin süresinden daha uzun bir süre için kullanılabilir hale getirmek istiyorsanız, denemeleri genelinde paylaşmak ve yeniden kullanmak üzere çalışma alanınıza kaydedin.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>`OutputFileDatasetConfig`Artık gerekli olmadığında içerikleri Sil

Azure, ile yazılmış ara verileri otomatik olarak silmez `OutputFileDatasetConfig` . Çok sayıda gereksiz veri için depolama ücretlerinden kaçınmak için şunlardan birini yapmalısınız:

* İşlem hattı çalıştırmasının sonunda, artık gerekli olmadığında ara verileri program aracılığıyla silme
* Ara veriler için kısa vadeli depolama ilkesiyle BLOB depolama kullanma (bkz. [Azure Blob depolama erişim katmanlarını otomatikleştirerek maliyetleri iyileştirme](../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)) 
* Daha uzun süre gerekli olmayan verileri düzenli olarak gözden geçirin ve silin

Daha fazla bilgi için bkz. [Azure Machine Learning için maliyetleri planlayın ve yönetin](concept-plan-manage-cost.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Machine Learning veri kümesi oluşturma](how-to-create-register-datasets.md)
* [Azure Machine Learning SDK ile makine öğrenimi işlem hatları oluşturma ve çalıştırma](./how-to-create-machine-learning-pipelines.md)