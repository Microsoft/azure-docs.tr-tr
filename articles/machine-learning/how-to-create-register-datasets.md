---
title: Azure Machine Learning veri kümeleri oluşturma
titleSuffix: Azure Machine Learning
description: Machine Learning deneme çalıştırmaları için verilerinize erişmek üzere Azure Machine Learning veri kümeleri oluşturmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: f47d610a24de2cfc8f1131f61afc8c8173a34376
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786630"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning veri kümeleri oluşturma

Bu makalede, Azure Machine Learning Python SDK ile yerel veya uzak denemeleri verilerinize erişmek için Azure Machine Learning veri kümeleri oluşturmayı öğreneceksiniz. Veri kümelerinin Azure Machine Learning genel veri erişimi iş akışına uygun olduğunu anlamak için, [güvenli erişim verileri](concept-data.md#data-workflow) makalesine bakın.

Bir veri kümesi oluşturduğunuzda ilgili veri kaynağı konumuna bir başvurunun yanı sıra meta verilerinin bir kopyasını oluşturmuş olursunuz. Veriler mevcut konumunda kaldığı için ek depolama ücreti ödemeniz ve veri kaynaklarınızın bütünlüğünü riske çıkarmazsınız. Ayrıca, veri kümeleri, iş akışı performans hızına yardımcı olan geç değerlendirilir. Veri mağazalarından, genel URL 'Lerden ve [Azure açık veri](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)kümelerinden veri kümeleri oluşturabilirsiniz.

Düşük kod deneyimi için [Azure Machine Learning Studio ile Azure Machine Learning veri kümeleri oluşturun.](how-to-connect-data-ui.md#create-datasets)

Azure Machine Learning veri kümeleri ile şunları yapabilirsiniz:

* Veri kümeleri tarafından başvurulan, depolama alanındaki verilerin tek bir kopyasını saklayın.

* Bağlantı dizeleri veya veri yolları hakkında endişelenmeden model eğitimi sırasında verilere sorunsuzca erişin. [Veri kümeleriyle eğitme hakkında daha fazla bilgi edinin](how-to-train-with-datasets.md).

* Veri paylaşma ve diğer kullanıcılarla işbirliği yapma.

## <a name="prerequisites"></a>Önkoşullar

Veri kümeleri oluşturmak ve bunlarla çalışmak için şunlar gerekir:

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md).

* Azureml [için Azure Machine Learning SDK 'sı](/python/api/overview/azure/ml/install), azureml veri kümesi paketini içerir.

    * Tümleşik Not defterleri ve SDK 'nın zaten yüklü olduğu tam olarak yapılandırılmış ve yönetilen bir geliştirme ortamı olan [Azure Machine Learning işlem örneği](how-to-create-manage-compute-instance.md)oluşturun.

    **OR**

    * Kendi Jupyter Not defteriniz üzerinde çalışın ve [bu yönergelerle](/python/api/overview/azure/ml/install)SDK 'yı kendiniz de yüklersiniz.

> [!NOTE]
> Bazı veri kümesi sınıflarının yalnızca 64 bitlik Python ile uyumlu olan [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) paketine bağımlılıkları vardır. Linux kullanıcıları için, bu sınıflar yalnızca şu dağıtımlara göre desteklenir: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), demı (8, 9) ve CentOS (7). Desteklenmeyen kaldırmalar kullanıyorsanız, devam etmek için lütfen [Bu kılavuzu](/dotnet/core/install/linux) Izleyerek .net Core 2,1 ' i yükleyebilirsiniz. 

## <a name="compute-size-guidance"></a>İşlem boyutu kılavuzu

Bir veri kümesi oluştururken, işlem işleme gücünden ve verilerinizin belleğindeki boyutunu gözden geçirin. Depolama alanındaki verilerinizin boyutu bir veri çerçevesindeki verilerin boyutuyla aynı değildir. Örneğin, CSV dosyalarındaki veriler bir veri çerçevesinde en fazla 10 x genişleyebilir, bu nedenle 1 GB CSV dosyası bir veri çerçevesinde 10 GB olabilir. 

Verileriniz sıkıştırılmışsa, daha fazla genişleyebilir; sıkıştırılmış Parquet biçiminde depolanan 20 GB görece seyrek veri, bellekte ~ 800 GB 'a genişleyebilir. Parquet dosyaları bir sütunlu biçimde veri depolarsanız, yalnızca sütunların yarısını içeriyorsa yalnızca ~ 400 GB 'yi belleğe yüklemeniz gerekir.

[Azure Machine Learning veri işlemeyi en iyi duruma getirme hakkında daha fazla bilgi edinin](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Veri kümesi türleri

Kullanıcıların eğitiminde nasıl tükettiği temel alınarak iki veri kümesi türü vardır; Dosya veri kümeleri ve Tabulardataset. Her iki tür de, estimators,, oto ml, hiper sürücü ve işlem hatları içeren Azure Machine Learning eğitim iş akışlarında kullanılabilir. 

### <a name="filedataset"></a>Dosya veri kümesi

Bir [dosya veri kümesi](/python/api/azureml-core/azureml.data.file_dataset.filedataset) , veri mağazalarınızın veya genel URL 'nizin tek veya birden çok dosyasına başvurur. Verileriniz zaten temizdir ve eğitim denemeleri 'de kullanıma hazırsanız, dosyaları dosya veri kümesi nesnesi olarak işlem dosyalarınıza [indirebilir veya bağlayabilirsiniz](how-to-train-with-datasets.md#mount-vs-download) . 

Kaynak dosyalar herhangi bir biçimde olduğundan, derin öğrenme dahil olmak üzere daha geniş bir makine öğrenimi senaryosu sağlayan herhangi bir biçimde olduğundan, makine öğrenimi iş akışlarınız için dosya veri kümelerini öneririz.

[Python SDK](#create-a-filedataset) veya [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) ile bir dosya veri kümesi oluşturun.
### <a name="tabulardataset"></a>TabularDataset

[Tabulardataset](/python/api/azureml-core/azureml.data.tabulardataset) , belirtilen dosya veya dosya listesini ayrıştırarak verileri tablolu biçimde temsil eder. Bu sayede, not defterinizin ayrılmasına gerek kalmadan tanıdık veri hazırlama ve eğitim kitaplıklarıyla çalışabilmeniz için verileri bir Pandas veya Spark veri çerçevesine hazırlama yeteneği sağlar. `TabularDataset`. Csv,. tsv, [. Parquet](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-), [. Jsonl dosyalarından](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--)ve [SQL sorgu sonuçlarından](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)bir nesne oluşturabilirsiniz.

Tabulardataset ile, verilerdeki bir sütundan veya bir zaman serisi nitelik sağlamak için yol deseninin verilerinin depolandığı her yerde bir zaman damgası belirtebilirsiniz. Bu belirtim zamana göre kolay ve etkili filtrelemeye olanak tanır. Bir örnek için, [NOAA Hava durumu verileri Içeren tablolu zaman serisiyle ılgılı API tanıtımı](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)bölümüne bakın.

[Python SDK](#create-a-tabulardataset) veya [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets)Ile tabulardataset oluşturun.

>[!NOTE]
> Azure Machine Learning Studio ile oluşturulan [OTOMATIK ml](concept-automated-ml.md) iş akışları Şu anda yalnızca Tabulardataset 'i destekliyor. 

## <a name="access-datasets-in-a-virtual-network"></a>Bir sanal ağdaki veri kümelerine erişme

Çalışma alanınız bir sanal ağda ise, veri kümesini doğrulamayı atlayacak şekilde yapılandırmanız gerekir. Bir sanal ağda veri depoları ve veri kümeleri kullanma hakkında daha fazla bilgi için bkz. [bir çalışma alanını ve ilişkili kaynakları güvenli hale getirme](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Veri depolarından veri kümeleri oluşturma

Verilere Azure Machine Learning tarafından erişilebilmeleri için, veri kümelerinin [Azure Machine Learning veri depolarında](how-to-access-data.md) veya Web URL 'lerinde yer almalıdır. 

> [!TIP] 
> Kimlik tabanlı veri erişimi olan depolama URL 'lerinden doğrudan veri kümeleri oluşturabilirsiniz. [Kimlik tabanlı veri erişimi (Önizleme) ile depolama 'Ya bağlanma](how-to-identity-based-data-access.md) hakkında daha fazla bilgi edinin<br><br>
Bu özellik, [deneysel](/python/api/overview/azure/ml/#stable-vs-experimental) Önizleme özelliğine sahiptir ve herhangi bir zamanda değişebilir. 

 
Bir veri deposundan Python SDK 'Sı ile veri kümeleri oluşturmak için:

1. `contributor` `owner` Kayıtlı Azure Machine Learning veri deposundaki temeldeki depolama hizmetine sahip olduğunuzu veya bu hizmete erişiminizin olduğunu doğrulayın. [Azure Portal depolama hesabı Izinlerinizi denetleyin](../role-based-access-control/check-access.md).

1. Veri deposundaki yollara başvurarak veri kümesini oluşturun. Birden çok veri mağazasında birden çok yoldan bir veri kümesi oluşturabilirsiniz. İçinden veri kümesi oluşturabileceğiniz dosya sayısı veya veri boyutu için sabit sınır yoktur. 

> [!NOTE]
> Her veri yolu için, depolama hizmetine bir dosya veya klasöre işaret edilip edilmeyeceğini denetlemek için birkaç istek gönderilir. Bu ek yük, performansın düşmesine veya başarısız olmasına neden olabilir. İçindeki 1000 dosya içeren bir klasöre başvuran bir veri kümesi, bir veri yoluna başvurulur. En iyi performansı elde etmek için veri depolarında 100 ' den az yola başvuruda bulunan veri kümesi oluşturmanızı öneririz.

### <a name="create-a-filedataset"></a>Dosya veri kümesi oluşturma

[`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) `FileDatasetFactory` Dosyaları herhangi bir biçimde yüklemek ve kayıtsız bir dosya veri kümesi oluşturmak için sınıfındaki yöntemini kullanın. 

Depolama alanı bir sanal ağın veya güvenlik duvarının arkasındaysa, `validate=False` yöntebinizdeki parametreyi ayarlayın `from_files()` . Bu, ilk doğrulama adımını atlar ve veri kümenizi bu güvenli dosyalardan oluşturmanıza da emin olmanızı sağlar. [Veri depolarını ve veri kümelerini bir sanal ağda kullanma](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)hakkında daha fazla bilgi edinin.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Çalışma alanınızdaki deneme genelindeki veri kümelerini yeniden kullanmak ve paylaşmak için [veri kümenizi kaydedin](#register-datasets). 

> [!TIP] 
> Dosyaları yerel bir dizinden karşıya yükleyin ve tek bir yöntemde, [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-)genel önizleme yöntemiyle bir dosya veri kümesi oluşturun. Bu yöntem, [deneysel](/python/api/overview/azure/ml/#stable-vs-experimental) Önizleme özelliğine sahiptir ve herhangi bir zamanda değişebilir. 
> 
>  Bu yöntem, verileri temel depolama verilerinize yükler ve sonuç olarak depolama maliyetlerine uygulanır. 

### <a name="create-a-tabulardataset"></a>TabularDataset oluşturma

[`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) `TabularDatasetFactory` . Csv veya. tsv biçimindeki dosyaları okumak ve kayıtlı olmayan bir TabularDataset oluşturmak için sınıfındaki yöntemini kullanın. . Parquet biçimindeki dosyaları okumak için [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) yöntemini kullanın. Birden çok dosyadan okuyorsanız, sonuçlar tek tablolu bir gösterimde toplanacaktır. 

Desteklenen dosya biçimleri ve söz dizimi ve tasarım desenleri hakkında bilgi için bkz. [Tabulardatasetfactory başvuru belgeleri](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) . 

Depolama alanı bir sanal ağın veya güvenlik duvarının arkasındaysa, `validate=False` yöntebinizdeki parametreyi ayarlayın `from_delimited_files()` . Bu, ilk doğrulama adımını atlar ve veri kümenizi bu güvenli dosyalardan oluşturmanıza da emin olmanızı sağlar. [Veri depolarını ve veri kümelerini bir sanal ağda](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)kullanma hakkında daha fazla bilgi edinin.

Aşağıdaki kod, mevcut çalışma alanını ve istenen veri deposunu ada göre alır. Ardından `path` , yeni bir TabularDataset oluşturmak için veri deposunu ve dosya konumlarını parametreye geçirir `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Veri şemasını ayarla

Varsayılan olarak, bir TabularDataset oluşturduğunuzda, sütun veri türleri otomatik olarak algılanır. Çıkarılan türler beklentilerinizle eşleşmiyorsa, aşağıdaki kodla sütun türlerini belirterek veri kümesi şemanızı güncelleştirebilirsiniz. Parametresi `infer_column_type` yalnızca sınırlandırılmış dosyalardan oluşturulmuş veri kümeleri için geçerlidir. [Desteklenen veri türleri hakkında daha fazla bilgi edinin](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|INDEKS|PassengerId|Kalan|PClass|Name|Komutu|Yaş|SibSp|Parch|Bilet|Tarifeli havayolu|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Yanlış|3|Braund, Mr. Owen HARRIS|erkek|22.0|1|0|A/5 21171|7,2500||S
1|2|Doğru|1|Hanler, Mrs. John Bradley (çiçek)...|kadın|38,0|1|0|BILGISAYAR 17599|71,2833|C85|C
2|3|Doğru|3|Heıkkinen, Isabetsizlik. Laina|kadın|26.0|0|0|STON/O2. 3101282|7,9250||S

Çalışma alanınızdaki denemeleri genelinde veri kümelerini yeniden kullanmak ve paylaşmak için [veri kümenizi kaydedin](#register-datasets).

## <a name="wrangle-data"></a>Wrangle verileri
Veri kümenizi [oluşturup kaydettikten sonra](#register-datasets) , model eğitimi 'nden önce veri denetimi ve [araştırma](#explore-data) için Not defterinize yükleyebilirsiniz. 

Herhangi bir veri için bir denetimi veya araştırma yapmanız gerekmiyorsa bkz. veri [kümeleriyle eğmek](how-to-train-with-datasets.md)üzere ml denemeleri göndermek için eğitim betiklerinizde veri kümelerini kullanma.

### <a name="filter-datasets-preview"></a>Veri kümelerini filtrele (Önizleme)

Filtreleme özellikleri, sahip olduğunuz veri kümesinin türüne bağlıdır. 
> [!IMPORTANT]
> Veri kümelerini önizleme yöntemiyle filtreleyerek, [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) [deneysel](/python/api/overview/azure/ml/#stable-vs-experimental) önizleme özelliği bulunur ve herhangi bir zamanda değişebilir. 
> 
**Tabulardataset 'Ler için** [keep_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) ve [drop_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) yöntemleriyle sütunları tutabilir veya kaldırabilirsiniz.

Bir TabularDataset içindeki belirli bir sütun değerine göre satırları filtrelemek için [Filter ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) yöntemini (Önizleme) kullanın. 

Aşağıdaki örnekler, belirtilen ifadeleri temel alan, kayıtlı olmayan bir veri kümesini döndürür.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**Filedataset 'lerdeki** her satır bir dosyanın yoluna karşılık gelir, bu nedenle sütun değerine göre filtreleme yararlı değildir. Ancak, verileri, CreationTime, boyut vb. gibi meta verilere göre [filtreleyebilirsiniz](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) .

Aşağıdaki örnekler, belirtilen ifadeleri temel alan, kayıtlı olmayan bir veri kümesini döndürür.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

[Veri etiketleme projelerinden](how-to-create-labeling-projects.md) oluşturulan **etiketlenmiş veri kümeleri** özel bir durumdur. Bu veri kümeleri, görüntü dosyalarından oluşan bir TabularDataset türüdür. Bu tür veri kümeleri için, meta verilere göre ve ve gibi sütun değerlerine göre resimleri [filtreleyebilirsiniz](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) `label` `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data-preview"></a>Bölüm verileri (Önizleme)

Bir `partitions_format` TabularDataset veya FileDataset oluştururken parametresini ekleyerek bir veri kümesini bölümleyebilirsiniz. 

> [!IMPORTANT]
> Veri kümesi bölümlerinin oluşturulması [deneysel](/python/api/overview/azure/ml/#stable-vs-experimental) önizleme yeteneğidir ve herhangi bir zamanda değişebilir. 

Bir veri kümesini bölümleyerek, her dosya yolunun bölüm bilgileri, belirtilen biçime göre sütunlara ayıklanır. Biçim, dosya yolunun sonuna kadar ilk bölüm anahtarının konumundan başlamalıdır. 

Örneğin, `../Accounts/2019/01/01/data.jsonl` bölümün bölüm adı ve saatine göre olduğu yol verildiğinde; değeri `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` ' accounts ' değeri Ile ' partitiondate ' DateTime sütunu ve değeri ile ' partitiondate ' dize sütununu oluşturur `2019-01-01` .

Verileriniz zaten mevcut bölümlere sahipse ve bu biçimi korumak istiyorsanız, `partitioned_format` [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) bir dosya veri kümesi oluşturmak için yöntemizin parametresini ekleyin. 

Varolan bölümleri koruyan bir TabularDataset oluşturmak için, `partitioned_format` [from_parquet_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) veya [from_delimited_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) metoduna parametresini ekleyin.

Aşağıdaki örnek,
* Bölümlenmiş dosyalardan bir dosya veri kümesi oluşturur.
* Bölüm anahtarlarını alır
* Kullanarak yeni, dizinli bir dosya veri kümesi oluşturur
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

Ayrıca, [partitions_by ()](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-) yöntemiyle Tabulardataset için yeni bir bölüm yapısı da oluşturabilirsiniz.

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

>[!IMPORTANT]
> TabularDataset bölümleri, birçok model uygulamasında ParallelRunStep verilerinize giriş olarak Azure Machine Learning işlem hatlarına da uygulanabilir. [Birçok model Hızlandırıcı belgelerindeki](https://github.com/microsoft/solution-accelerator-many-models/blob/master/01_Data_Preparation.ipynb)bir örneğe bakın.

## <a name="explore-data"></a>Verileri inceleme

Verilerinizi wrangini tamamladıktan sonra, veri kümenizi [kaydedebilir](#register-datasets) ve model eğitimine başlamadan önce veri araştırması için Not defterinize yükleyebilirsiniz.

Dosya veri kümeleri için, veri kümenizi **bağlayabilir** veya **indirebilir** ve normalde veri araştırması için kullandığınız Python kitaplıklarını uygulayabilirsiniz. [Bağlama vs indirmesi hakkında daha fazla bilgi edinin](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Tabulardataset 'ler için, [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) verilerinizi bir dataframe içinde görüntülemek için yöntemini kullanın. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|INDEKS|PassengerId|Kalan|PClass|Name|Komutu|Yaş|SibSp|Parch|Bilet|Tarifeli havayolu|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Yanlış|3|Braund, Mr. Owen HARRIS|erkek|22.0|1|0|A/5 21171|7,2500||S
1|2|Doğru|1|Hanler, Mrs. John Bradley (çiçek)...|kadın|38,0|1|0|BILGISAYAR 17599|71,2833|C85|C
2|3|Doğru|3|Heıkkinen, Isabetsizlik. Laina|kadın|26.0|0|0|STON/O2. 3101282|7,9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Pandas dataframe 'ten bir veri kümesi oluşturma

Bellek Pandas dataframe 'ten bir TabularDataset oluşturmak için, verileri bir CSV gibi yerel bir dosyaya yazın ve veri kümenizi bu dosyadan oluşturun. Aşağıdaki kod bu iş akışını gösterir.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> Ortak önizleme yöntemlerine sahip tek bir yöntemle bir WITH Memory Spark veya Pandas dataframe öğesinden bir TabularDataset oluşturun ve kaydedin [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Bu kayıt yöntemleri [deneysel](/python/api/overview/azure/ml/#stable-vs-experimental) önizleme özellikleridir ve herhangi bir zamanda değişebilir. 
> 
>  Bu yöntemler, verileri temel depolama verilerinize yükler ve sonuç olarak depolama maliyetlerine uygulanır. 

## <a name="register-datasets"></a>Veri kümelerini Kaydet

Oluşturma işlemini gerçekleştirmek için, veri kümelerinizi bir çalışma alanına kaydedin. Çalışma alanınızda [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) başkalarıyla paylaşmak ve çalışma alanınızdaki denemeleri genelinde yeniden kullanmak için veri kümelerini çalışma alanınıza kaydetmek üzere yöntemini kullanın:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Azure Resource Manager kullanarak veri kümeleri oluşturma

Üzerinde [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) veri kümeleri oluşturmak için kullanılabilecek birçok şablon vardır.

Bu şablonları kullanma hakkında daha fazla bilgi için bkz. [Azure Machine Learning için bir çalışma alanı oluşturmak üzere Azure Resource Manager şablonu kullanma](how-to-create-workspace-template.md).


## <a name="create-datasets-from-azure-open-datasets"></a>Azure açık veri kümelerinde veri kümeleri oluşturma

[Azure açık veri](https://azure.microsoft.com/services/open-datasets/) kümeleri, daha doğru modeller için makine öğrenimi çözümlerine senaryoya özgü özellikler eklemek için kullanabileceğiniz, seçkin ortak veri kümeleridir. Veri kümeleri, makine öğrenimi modellerini ve zenginleştirme çözümlerini eğitmenize yardımcı olan hava durumu, Census, tatiller, genel güvenlik ve konum için genel etki alanı verilerini içerir. Açık veri kümeleri bulutta Microsoft Azure ve hem SDK hem de Studio 'ya dahildir.

[Azure açık veri kümelerinden Azure Machine Learning veri kümeleri](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)oluşturmayı öğrenin. 

## <a name="train-with-datasets"></a>Veri kümeleriyle eğitme

ML modellerinizi eğitmek için Machine Learning denemeleri 'te veri kümelerinizi kullanın. [Veri kümeleriyle eğitme hakkında daha fazla bilgi edinin](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Sürüm veri kümeleri

Yeni bir sürüm oluşturarak aynı ada sahip yeni bir veri kümesini kaydedebilirsiniz. Veri kümesi sürümü, deneme veya gelecekteki bir çoğaltma için veri kümesinin belirli bir sürümünü uygulayabilmeniz için verilerinizin durumuna yer işaretinin bir yoludur. [Veri kümesi sürümleri](how-to-version-track-datasets.md)hakkında daha fazla bilgi edinin.
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Sonraki adımlar

* [Veri kümeleriyle eğitme hakkında](how-to-train-with-datasets.md)bilgi edinin.
* [Tabulardataset ile eğilmesi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)için otomatik makine öğrenimini kullanın.
* Daha fazla veri kümesi eğitimi örnekleri için bkz. [örnek Not defterleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
