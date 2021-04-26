---
title: Machine Learning veri kümeleriyle eğitme
titleSuffix: Azure Machine Learning
description: Verilerinizi, Azure Machine Learning veri kümeleriyle model eğitimi için yerel veya uzak işlem için nasıl kullanılabilir hale yapacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 0b2bb49863e07e6f06512e868ed12ecf00cc11c2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872398"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Azure Machine Learning veri kümeleriyle modelleri eğitme 

Bu makalede makine öğrenimi modellerini eğitmek için [Azure Machine Learning veri kümeleriyle](/python/api/azureml-core/azureml.core.dataset%28class%29) nasıl çalışacağınızı öğreneceksiniz.  Bağlantı dizeleri veya veri yolları hakkında endişelenmeden, yerel veya uzaktan işlem Hedefinizdeki veri kümelerini kullanabilirsiniz. 

Azure Machine Learning veri kümeleri, [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig), [hyperdrive](/python/api/azureml-train-core/azureml.train.hyperdrive)ve [Azure Machine Learning işlem hatları](./how-to-create-machine-learning-pipelines.md)gibi Azure Machine Learning eğitim işlevleriyle sorunsuz bir tümleştirme sağlar.

Verilerinizi model eğitimi için kullanılabilir hale getirmek için hazır değilseniz, ancak verilerinizi veri araştırması için Not defterinize yüklemek istiyorsanız, bkz. veri [kümenizdeki verileri keşfetme](how-to-create-register-datasets.md#explore-data). 

## <a name="prerequisites"></a>Önkoşullar

Veri kümeleri oluşturup eğitmeniz için şunlar gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md).

* Paketi içeren [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/install) (>= 1.13.0) `azureml-datasets` .

> [!Note]
> Bazı veri kümesi sınıflarının [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) paketine bağımlılıkları vardır. Linux kullanıcıları için, bu sınıflar yalnızca şu dağıtımlarda desteklenir: Red Hat Enterprise Linux, Ubuntu, Fedora ve CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Machine Learning eğitim betiklerine veri kümelerini kullanma

Henüz bir veri kümesi olarak kayıtlı olmayan yapılandırılmış veriler varsa, bir TabularDataset oluşturun ve bunu yerel veya uzaktan denemenize yönelik eğitim betiğinizdeki doğrudan kullanın.

Bu örnekte, kayıtlı olmayan bir [Tabulardataset](/python/api/azureml-core/azureml.data.tabulardataset) oluşturun ve bunu eğitim için [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) nesnesinde bir betik bağımsız değişkeni olarak belirtirsiniz. Bu TabularDataset 'i çalışma alanınızdaki diğer denemeleri birlikte yeniden kullanmak istiyorsanız, bkz. [veri kümelerini çalışma alanınıza kaydetme](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>TabularDataset oluşturma

Aşağıdaki kod, bir Web URL 'sinden kayıtlı olmayan bir TabularDataset oluşturur.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset nesneleri, Not defterinize çıkmadan tanıdık veri hazırlama ve eğitim kitaplıklarıyla çalışabilmeniz için TabularDataset 'teki verileri bir Pandas veya Spark veri çerçevesine yükleme olanağı sağlar.

### <a name="access-dataset-in-training-script"></a>Eğitim betiğinde veri kümesine erişme

Aşağıdaki kod, eğitim çalıştırmanızı yapılandırırken belirlediğiniz bir betik bağımsız değişkenini yapılandırır `--input-data` (sonraki bölüme bakın). Tablosal veri kümesi bağımsız değişken değeri olarak geçirildiğinde, Azure ML, veri kümesinin KIMLIĞINI çözer ve daha sonra eğitim betiğinizdeki veri kümesine erişmek için kullanabilirsiniz (betiğinizdeki veri kümesinin adını veya KIMLIĞINI kodlamadan bağımsız olarak kodlayın). Daha sonra, [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) eğitim öncesinde daha fazla veri araştırması ve hazırlığı için bu veri kümesini bir Pandas dataframe 'e yüklemek için yöntemini kullanır.

> [!Note]
> Özgün veri kaynağınız NaN, boş dizeler veya boş değerler içeriyorsa, kullandığınızda `to_pandas_dataframe()` Bu değerler *null* değer olarak değişir.

Hazırlanan verileri, bellek içi Pandas dataframe 'ten yeni bir veri kümesine yüklemeniz gerekiyorsa, verileri bir Parquet gibi yerel bir dosyaya yazın ve bu dosyadan yeni bir veri kümesi oluşturun. [Veri kümeleri oluşturma](how-to-create-register-datasets.md)hakkında daha fazla bilgi edinin.

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Eğitim çalıştırmasını yapılandırma

Eğitim çalıştırmasını yapılandırmak ve göndermek için bir [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) nesnesi kullanılır.

Bu kod, şunu belirten bir ScriptRunConfig nesnesi oluşturur `src`

* Betikleriniz için bir komut dosyası dizini. Bu dizindeki dosyaların tümü yürütülmek üzere küme düğümlerine yüklenir.
* Eğitim betiği, *train_titanic. Kopyala*.
* Eğitim için giriş veri kümesi, `titanic_ds` komut dosyası bağımsız değişkeni olarak. Azure ML, betiğe geçirildiğinde bu veri kümesinin karşılık gelen KIMLIĞINE çözüm olarak çözer.
* Çalıştırma için işlem hedefi.
* Çalıştırmanın ortamı.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Dosyaları uzaktan işlem hedeflerine bağlama

Yapılandırılmamış verileriniz varsa, bir [dosya veri kümesi](/python/api/azureml-core/azureml.data.filedataset) oluşturun ve veri dosyalarınızı, eğitim için uzaktan işlem hedefi için kullanılabilir hale getirmek üzere bağlayın veya indirin. Uzaktan Eğitim denemeleri için [bağlama ve indirme](#mount-vs-download) bilgilerini ne zaman kullanacağınızı öğrenin. 

Aşağıdaki örnek, 

* Eğitim verileriniz için bir giriş dosyası veri kümesi oluşturur `mnist_ds` .
* Eğitim sonuçlarının nereye yazılacağını belirtir ve bu sonuçları bir dosya veri kümesi olarak yükseltir.
* Giriş veri kümesini işlem hedefine bağlar.

> [!Note]
> Özel bir Docker temel görüntüsü kullanıyorsanız, `apt-get install -y fuse` veri kümesi bağlama 'nın çalışması için bir bağımlılık olarak kullanarak sigortası yüklemeniz gerekir. [Özel bir yapı görüntüsü](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)oluşturmayı öğrenin.

Not defteri örneği için bkz. [veri girişi ve çıkışı ile bir eğitim çalıştırmasını yapılandırma](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

### <a name="create-a-filedataset"></a>Dosya veri kümesi oluşturma

Aşağıdaki örnek, Web URL 'lerinden, kayıtsız bir dosya veri kümesi oluşturur `mnist_data` . Bu dosya veri kümesi, eğitim çalıştıralım için giriş verileri.

Diğer kaynaklardan [veri kümeleri oluşturma](how-to-create-register-datasets.md) hakkında daha fazla bilgi edinin.

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>Eğitim çıkışının nereden yazılacağı

Bir [Outputfiledatasetconfig nesnesiyle](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)eğitim sonuçlarınızı nereye yazacağınızı belirtebilirsiniz. 

OutputFileDatasetConfig nesneleri şunları yapmanıza izin verir: 

* Bir çalıştırmanın çıkışını belirttiğiniz bulut depolama alanına takın veya karşıya yükleyin.
* Çıktıyı bir dosya veri kümesi olarak şu desteklenen depolama türlerine Kaydet:
    * Azure blobu
    * Azure dosya paylaşımı
    * Azure Data Lake Storage nesilleri 1 ve 2
* Eğitim çalıştırmaları arasında veri kökenini izleyin.

Aşağıdaki kod, eğitim sonuçlarının `outputdataset` varsayılan blob veri deposunda bulunan klasöre bir dosya veri kümesi olarak kaydedilmesi gerektiğini belirtir `def_blob_store` . 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>Eğitim çalıştırmasını yapılandırma

Oluşturucunun parametresi aracılığıyla bağlanırken veri kümesini bir bağımsız değişken olarak geçirmeyi öneririz `arguments` `ScriptRunConfig` . Bunu yaparak eğitim betiğinizdeki veri yolunu (takma noktası) bağımsız değişkenler aracılığıyla alırsınız. Bu şekilde, yerel hata ayıklama ve herhangi bir bulut platformunda uzaktan eğitim için aynı eğitim betiğini kullanabilirsiniz.

Aşağıdaki örnek ile FileDataset içinde geçen bir ScriptRunConfig oluşturur `arguments` . Çalışmayı gönderdikten sonra, veri kümesi tarafından başvurulan veri dosyaları `mnist_ds` işlem hedefine bağlanır ve eğitim sonuçları, `outputdataset` varsayılan veri deposundaki belirtilen klasöre kaydedilir.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Basit eğitim betiği

Aşağıdaki betik ScriptRunConfig aracılığıyla gönderilir. `mnist_ds `Veri kümesini girdi olarak okur ve dosyayı `outputdataset` varsayılan blob veri deposundaki klasöre yazar `def_blob_store` .

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Bağlama vs indirmesi

Azure Blob depolama, Azure dosyaları, Azure Data Lake Storage 1., Azure Data Lake Storage 2., Azure SQL veritabanı ve PostgreSQL için Azure veritabanı tarafından oluşturulan veri kümelerinde herhangi bir biçimdeki dosyaları bağlama veya indirme işlemi desteklenir. 

**Bir veri kümesini bağladığınızda,** veri kümesinin başvurduğu dosyaları bir dizine (bağlama noktası) ekler ve işlem hedefinde kullanılabilir hale getirin. Bağlama, Azure Machine Learning Işlem, sanal makineler ve HDInsight dahil olmak üzere Linux tabanlı hesaplar için desteklenir. 

Bir veri kümesini **indirdiğinizde** , veri kümesi tarafından başvurulan tüm dosyalar işlem hedefine indirilir. Tüm işlem türleri için indirme desteklenir. 

Betiğinizin veri kümesi tarafından başvurulan tüm dosyaları işliyorsa ve işlem diskiniz tam veri kümesine uyuyorsa, depolama hizmetlerinden veri akışı yükünü ortadan kaldırmak için indirme önerilir. Veri boyutunuz işlem diski boyutunu aşarsa, indirme mümkün değildir. Bu senaryo için, işleme sırasında yalnızca komut dosyası tarafından kullanılan veri dosyaları yüklendiğinden, bağlama yapmanız önerilir.

Aşağıdaki kod, şu `dataset` adreste geçici dizine bağlar: `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Makine öğrenimi betiklerine veri kümeleri al

Kayıtlı veri kümelerine, Azure Machine Learning işlem gibi işlem kümelerinde hem yerel olarak hem de uzaktan erişilebilir. Kayıtlı veri kümenize denemeleri üzerinden erişmek için aşağıdaki kodu kullanarak çalışma alanınıza erişin ve daha önce gönderdiğiniz çalıştırmada kullanılan veri kümesini alın. Varsayılan olarak, [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) sınıfındaki yöntemi, `Dataset` çalışma alanına kayıtlı veri kümesinin en son sürümünü döndürür.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Eğitim sırasında kaynak koduna erişin

Azure Blob depolama, bir Azure dosya paylaşımından daha yüksek işleme hızına sahiptir ve paralel olarak başlatılan çok sayıda işi ölçeklendirecektir. Bu nedenle, çalıştırmaların kaynak kodu dosyalarını aktarmak için blob depolamayı kullanacak şekilde yapılandırılmasını öneririz.

Aşağıdaki kod örneği, kaynak kodu aktarımları için kullanılacak blob veri deposuna ait çalıştırma yapılandırması ' nda belirtir.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Not defteri örnekleri

+ Ek veri kümesi örnekleri ve kavramlar için bkz. [DataSet Not defterleri](https://aka.ms/dataset-tutorial).
+ Bkz. ML işlem hatlarında [parametrize veri kümelerini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)öğrenin.

## <a name="troubleshooting"></a>Sorun giderme

* **DataSet başlatılamadı: bağlama noktasının hazır olması beklenirken zaman aşımı oluştu**: 
  * Herhangi bir giden [ağ güvenlik grubu](../virtual-network/network-security-groups-overview.md) kuralına sahip değilseniz ve `azureml-sdk>=1.12.0` `azureml-dataset-runtime` onun bağımlılıklarını, belirli bir alt sürüm için en son olacak şekilde güncelleştirebilir veya bir çalıştırmada kullanıyorsanız, bu düzeltme ile en son düzeltme ekine sahip olması için ortamınızı yeniden oluşturun. 
  * Kullanıyorsanız `azureml-sdk<1.12.0` , en son sürüme yükseltin.
  * Giden NSG kurallarınız varsa, hizmet etiketi için tüm trafiğe izin veren bir giden kuralı olduğundan emin olun `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Aşırı yüklenmiş AzureFile depolama

Bir hata alırsanız `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , aşağıdaki geçici çözümleri uygulayın.

Veri aktarımı gibi diğer iş yükleri için dosya paylaşma 'yı kullanıyorsanız, dosya paylaşımının çalıştırmaları için kullanılabilmesi için blob 'ları kullanmak, bu nedenle söz konusu çalışma. İş yükünü iki farklı çalışma alanı arasında da bölebilirsiniz.

### <a name="passing-data-as-input"></a>Verileri giriş olarak geçirme

*  **TypeError: Fılenotfound: böyle bir dosya veya dizin yok**: sağladığınız dosya yolu dosyanın bulunduğu yere değilse bu hata oluşur. Dosyaya başvurduğunuzdan emin olmanız gerekir. bu şekilde, veri kümenizi işlem Hedefinizdeki bağladığınız konum ile tutarlıdır. Belirleyici bir durum sağlamak için, bir veri kümesini bir işlem hedefine bağlamak için soyut yolun kullanılmasını öneririz. Örneğin, aşağıdaki kodda, veri kümesini işlem hedefinin FileSystem kökünün altına bağlamamız gerekir `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Önde gelen eğik çizgi '/' dahil değilseniz, `/mnt/batch/.../tmp/dataset` veri kümesinin bağlanmasını istediğiniz yeri belirtmek için işlem hedefi üzerinde çalışma dizinini (.) ön eki uygulamanız gerekir.


## <a name="next-steps"></a>Sonraki adımlar

* Tabulardataset ile [makine öğrenimi modellerini otomatik olarak eğitme](how-to-configure-auto-train.md#data-source-and-format) .

* Dosya veri kümeleri ile [görüntü sınıflandırma modellerini eğitme](https://aka.ms/filedataset-samplenotebook) .

* İşlem [hatları kullanarak veri kümeleriyle eğitme](./how-to-create-machine-learning-pipelines.md).
