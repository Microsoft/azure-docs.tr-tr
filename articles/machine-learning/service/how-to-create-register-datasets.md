---
title: Verilere erişmek için Azure Machine Learning veri kümeleri oluşturma
titleSuffix: Azure Machine Learning
description: Machine Learning deneme çalıştırmaları için verilerinize erişmek üzere Azure Machine Learning veri kümeleri oluşturmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: b4439df5ced1f76b4cb69feadf862638ab223d56
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123308"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning veri kümeleri oluşturma

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, yerel veya uzak denemeleri verilerinize erişmek için Azure Machine Learning veri kümeleri oluşturmayı öğreneceksiniz.

Azure Machine Learning veri kümeleri ile şunları yapabilirsiniz:

* Veri kümeleri tarafından başvurulan, depolama alanındaki verilerin tek bir kopyasını saklayın.

* Bağlantı dizeleri veya veri yolları hakkında endişelenmeden model eğitimi sırasında verilere sorunsuzca erişin.

* Veri paylaşma ve diğer kullanıcılarla işbirliği yapma.

## <a name="prerequisites"></a>Önkoşullar

Veri kümeleri oluşturmak ve bunlarla çalışmak için şunlar gerekir:

* Azure aboneliği. Bir tane yoksa, başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md).

* Azureml [için Azure Machine Learning SDK 'sı](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), azureml veri kümesi paketini içerir.

> [!NOTE]
> Bazı veri kümesi sınıflarının [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketine bağımlılıkları vardır. Linux kullanıcıları için, bu sınıflar yalnızca şu dağıtımlarda desteklenir: Red Hat Enterprise Linux, Ubuntu, Fedora ve CentOS.

## <a name="dataset-types"></a>Veri kümesi türleri

Kullanıcıların eğitiminde kullanıcıları nasıl tükettiği temel alınarak iki veri kümesi türü vardır:

* [Tabulardataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) , belirtilen dosya veya dosya listesini ayrıştırarak verileri tablolu biçimde temsil eder. Bu, verileri bir Pandas veya Spark DataFrame 'e nasıl bir şekilde sunmanızı sağlar. . Csv,. tsv ve Parquet dosyalarından ve SQL sorgu sonuçlarından `TabularDataset` bir nesne oluşturabilirsiniz. Tüm liste için bkz. [Tabulardatasetfactory sınıfı](https://aka.ms/tabulardataset-api-reference).

* [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) sınıfı, veri mağazalarınızın veya genel URL 'nizin tek veya birden çok dosyasına başvurur. Bu yöntemde, dosyaları dosya veri kümesi nesnesi olarak işlem dosyalarınıza indirebilir veya bağlayabilirsiniz. Dosyalar, derin öğrenme dahil olmak üzere daha geniş bir makine öğrenimi senaryolarına izin veren herhangi bir biçimde olabilir.

Yaklaşan API değişiklikleri hakkında daha fazla bilgi edinmek için bkz. [DataSet API Change bildirimi](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Veri kümeleri oluşturma

Bir veri kümesi oluşturarak, veri kaynağı konumuna, meta verilerinin bir kopyasıyla birlikte bir başvuru oluşturursunuz. Veriler mevcut konumunda kaldığı için ek depolama ücreti ödemeniz gerekmez. Python SDK veya çalışma alanı giriş sayfasını (Önizleme) kullanarak hem `TabularDataset` hem de `FileDataset` veri kümeleri oluşturabilirsiniz.

Azure Machine Learning tarafından erişilebilmesi için, veri kümelerinin [Azure veri depoları](how-to-access-data.md) veya genel Web URL 'lerinde yollardan oluşturulması gerekir.

### <a name="use-the-sdk"></a>SDK 'Yı kullanma

Python SDK kullanarak bir [Azure veri deposundan](how-to-access-data.md) veri kümeleri oluşturmak için:

1. Kayıtlı Azure veri deposuna `contributor` veya `owner` erişiminizin olduğunu doğrulayın.

1. Veri deposundaki bir yola başvurarak veri kümesini oluşturun:

    ```Python
    from azureml.core.workspace import Workspace
    from azureml.core.datastore import Datastore
    from azureml.core.dataset import Dataset
    
    datastore_name = 'your datastore name'
    
    # get existing workspace
    workspace = Workspace.from_config()
    
    # retrieve an existing datastore in the workspace by name
    datastore = Datastore.get(workspace, datastore_name)
    ```

#### <a name="create-a-tabulardataset"></a>TabularDataset oluşturma

SDK aracılığıyla veya Azure Machine Learning Studio kullanarak Tabulardataset oluşturulabilir. Bir zaman serisini nitelik sağlamak için verilerdeki bir sütundan veya verilerin depolandığı yol düzeninden bir zaman damgası belirtebilirsiniz. Bu belirtim zamana göre kolay ve etkili filtrelemeye olanak tanır.

. Csv veya. tsv biçimindeki dosyaları okumak ve kayıtlı olmayan bir TabularDataset oluşturmak için `TabularDatasetFactory` sınıfındaki [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) yöntemini kullanın. Birden çok dosyadan okuyorsanız, sonuçlar tek tablolu bir gösterimde toplanacaktır.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Varsayılan olarak, bir TabularDataset oluşturduğunuzda, sütun veri türleri otomatik olarak algılanır. Çıkarılan türler beklentilerinizle eşleşmiyorsa, aşağıdaki kodu kullanarak sütun türlerini belirtebilirsiniz. [Desteklenen veri türleri hakkında daha fazla bilgi](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)edinebilirsiniz.

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |Passengerıd|Kalan|PClass|Ad|Komutu|Yaş|SibSp|Parch|Ticket|Tarifeli havayolu|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen HARRIS|erkek|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Hanler, Mrs. John Bradley (çiçek)...|kadın|38,0|1|0|BILGISAYAR 17599|71,2833|C85|C
2|3|True|3|Heıkkinen, Isabetsizlik. Laina|kadın|26,0|0|0|STON/O2. 3101282|7,9250||S

Azure SQL veritabanı 'ndan okumak için `TabularDatasetFactory` sınıfında [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) yöntemi kullanın:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

Tabulardataset ' te, bir zaman serisi nitelik sağlamak için veri içindeki bir sütundan veya yol deseninin verilerinin depolandığı her yerde bir zaman damgası belirtebilirsiniz. Bu belirtim zamana göre kolay ve etkili filtrelemeye olanak tanır.

Zaman damgası sütununu belirtmek ve zamana göre filtrelemeyi etkinleştirmek için`TabularDataset` sınıfındaki [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) yöntemi kullanın. Daha fazla bilgi için bkz. [NOAA Hava durumu verileri Içeren tablolu zaman serisiyle ılgılı API tanıtımı](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Dosya veri kümesi oluşturma

Dosyaları herhangi bir biçimde yüklemek ve kaydedilmemiş bir dosya veri kümesi oluşturmak için `FileDatasetFactory` sınıfında [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) yöntemi kullanın:

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [
                  (datastore, 'animals')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Uygulama oluşturun 
Aşağıdaki adımlar ve animasyon, Azure Machine Learning Studio https://ml.azure.combir veri kümesi oluşturmayı gösterir.

![UI ile veri kümesi oluşturma](media/how-to-create-register-datasets/create-dataset-ui.gif)

Studio 'da bir veri kümesi oluşturmak için:
1. https://ml.azure.com'de oturum açın.
1. Sol bölmedeki **varlıklar** bölümünde **veri kümeleri** ' ni seçin. 
1. Veri kümenizin kaynağını seçmek için **veri kümesi oluştur** ' u seçin. Bu kaynak yerel dosyalar, bir veri deposu veya genel URL olabilir.
1. Veri kümesi türü için **tablo** veya **Dosya** seçin.
1. Ayarları gözden geçirmek **ve önizleme**, **şema** ve **ayrıntıları onaylamak** için **İleri** ' yi seçin; dosya türüne göre akıllıca doldurulur. Seçimlerinizi denetlemek ve veri kümenizi oluşturmadan önce daha fazla yapılandırmak için bu formları kullanın.  
1. Veri kümesi oluşturmayı gerçekleştirmek için **Oluştur** ' u seçin.

## <a name="register-datasets"></a>Veri kümelerini Kaydet

Oluşturma işlemini gerçekleştirmek için, veri kümelerinizi bir çalışma alanına kaydedin. Veri kümelerini başkalarıyla paylaşmak ve bunları çeşitli denemeleri genelinde yeniden kullanmak için çalışma alanınıza kaydetmek üzere [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) yöntemini kullanın:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Azure Machine Learning Studio aracılığıyla oluşturulan veri kümeleri otomatik olarak çalışma alanına kaydedilir.

## <a name="create-datasets-with-azure-open-datasets"></a>Azure açık veri kümeleri ile veri kümeleri oluşturma

[Azure açık veri](https://azure.microsoft.com/services/open-datasets/) kümeleri, daha doğru modeller için makine öğrenimi çözümlerine senaryoya özgü özellikler eklemek için kullanabileceğiniz, seçkin ortak veri kümeleridir. Veri kümeleri, makine öğrenimi modellerini ve zenginleştirme çözümlerini eğitmenize yardımcı olan hava durumu, Census, tatiller, genel güvenlik ve konum için genel etki alanı verilerini içerir. Açık veri kümeleri bulutta Microsoft Azure ve hem SDK hem de çalışma alanı kullanıcı arabirimine dahildir.

### <a name="use-the-sdk"></a>SDK 'Yı kullanma

SDK 'da Azure açık veri kümeleri ile veri kümeleri oluşturmak için paketi `pip install azureml-opendatasets`yüklediğinizden emin olun. Her ayrık veri kümesi SDK 'daki kendi sınıfıyla temsil edilir ve belirli sınıflar `TabularDataset`, `FileDataset`veya her ikisi olarak sunulur. Sınıfların tam listesi için [başvuru belgelerine](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) bakın.

Çoğu sınıf öğesinden devralınır ve bir `TabularDataset`örneğinden döndürülür. Bu sınıfların örneklerine `PublicHolidays`, `BostonSafety`ve `UsPopulationZip`dahildir. Bu sınıf türlerinden bir `TabularDataset` oluşturmak için oluşturucuyu bağımsız değişken olmadan kullanın. Açık veri kümelerinden oluşturulan bir veri kümesini kaydettiğinizde, hiçbir veri hemen indirilir, ancak istendiğinde (eğitim sırasında, örneğin, merkezi bir depolama konumundan), verilere daha sonra erişilir. 

```python
from azureml.opendatasets import UsPopulationZip

tabular_dataset = UsPopulationZip()
tabular_dataset = tabular_dataset.register(workspace=workspace, name="pop data", description="US population data by zip code")
```

Dosyaları doğrudan yönetmenize ve/veya indirmenizi sağlayan bir `TabularDataset` veya `FileDataset`olarak belirli sınıfları alabilirsiniz. Diğer sınıflar yalnızca `get_tabular_dataset()` ya da `get_file_dataset()` işlevlerini kullanarak bir veri kümesi alabilir. Aşağıdaki kod örneği, bu sınıf türleri için birkaç örnek gösterir:

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

### <a name="use-the-ui"></a>Kullanıcı arabirimini kullanma

Ayrıca, Kullanıcı arabirimi aracılığıyla açık veri kümeleri sınıflarından veri kümeleri oluşturabilirsiniz. Çalışma alanınızda **varlıklar**' ın altındaki **veri kümeleri** sekmesini seçin. **Veri kümesi oluştur** aşağı açılan menüsünde **açık veri kümeleri ' nden**seçim yapın.

![UI ile veri kümesini açma](media/how-to-create-register-datasets/open-datasets-1.png)

Kutucuğunu seçerek bir veri kümesi seçin. (Arama çubuğunu kullanarak filtreleme seçeneğiniz vardır.) **İleri ' yi**seçin.

![Veri kümesi seçin](media/how-to-create-register-datasets/open-datasets-2.png)

Veri kümesinin kaydedileceği bir ad seçin ve isteğe bağlı olarak, kullanılabilir filtreleri kullanarak verileri filtreleyin. Bu durumda, genel tatiller veri kümesi için zaman aralığını bir yıla, ülke kodunu ise yalnızca BIZIMLE filtreleyerek filtreleyebilirsiniz. **Oluştur**'u seçin.

![Veri kümesi params 'i ayarlama ve veri kümesi oluşturma](media/how-to-create-register-datasets/open-datasets-3.png)

Veri kümesi artık veri **kümeleri**altında çalışma alanınızda kullanılabilir. Bunu, oluşturduğunuz diğer veri kümeleriyle aynı şekilde kullanabilirsiniz.

## <a name="version-datasets"></a>Sürüm veri kümeleri

Yeni bir sürüm oluşturarak aynı ada sahip yeni bir veri kümesini kaydedebilirsiniz. Veri kümesi sürümü, deneme veya gelecekteki bir çoğaltma için veri kümesinin belirli bir sürümünü uygulayabilmeniz için verilerinizin durumuna yer işaretinin bir yoludur. [Veri kümesi sürümleri](how-to-version-track-datasets.md)hakkında daha fazla bilgi edinin.
```Python
# create a TabularDataset from Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-datasets-in-your-script"></a>Betiğinizdeki veri kümelerine erişin

Kayıtlı veri kümelerine, Azure Machine Learning işlem gibi işlem kümelerinde hem yerel olarak hem de uzaktan erişilebilir. Kayıtlı veri kümenize denemeleri üzerinden erişmek için aşağıdaki kodu kullanarak çalışma alanınıza ve kayıtlı veri kümesine ad ile erişin. Varsayılan olarak, `Dataset` sınıfındaki [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) yöntemi, çalışma alanına kayıtlı olan veri kümesinin en son sürümünü döndürür.

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

## <a name="next-steps"></a>Sonraki adımlar

* [Veri kümeleriyle eğitme hakkında](how-to-train-with-datasets.md)bilgi edinin.
* [Tabulardataset ile eğilmesi](https://aka.ms/automl-dataset)için otomatik makine öğrenimini kullanın.
* Daha fazla veri kümesi eğitimi örnekleri için bkz. [örnek Not defterleri](https://aka.ms/dataset-tutorial).
