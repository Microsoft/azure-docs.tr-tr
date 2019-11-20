---
title: Azure Storage hizmetlerindeki verilere erişme
titleSuffix: Azure Machine Learning
description: Veri depolarını kullanarak eğitim sırasında Azure depolama hizmetlerine erişme hakkında bilgi edinin Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 2b76d8f25cfb8bd1dfda43c8383a538f8cf9769b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818451"
---
# <a name="access-data-in-azure-storage-services"></a>Azure Storage hizmetlerindeki verilere erişme
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Storage hizmetlerindeki verilerinize Azure Machine Learning veri depoları aracılığıyla kolayca nasıl erişebileceğinizi öğrenin. Veri depoları, abonelik KIMLIĞINIZ ve belirteç yetkilendirmesi gibi bağlantı bilgilerini depolamak için kullanılır. Datamağazaların kullanılması, betiklerinizde bulunan bağlantı bilgilerini sabit bir şekilde kodlamadan depolamaya erişmenizi sağlar. Bu [Azure depolama çözümlerinden](#matrix)veri depoları oluşturabilirsiniz. Desteklenmeyen depolama çözümleri için Machine Learning denemeleri sırasında veri çıkış maliyetini kaydetmek üzere verilerinizi desteklenen Azure depolama çözümlerimize taşımanızı öneririz. [Verilerinizi taşımayı öğrenin](#move). 

Bu nasıl yapılır, aşağıdaki görevlerin örneklerini gösterir:
* [Veri depolarını Kaydet](#access)
* [Çalışma alanından veri depoları al](#get)
* [Veri depolarını kullanarak verileri karşıya yükleme ve indirme](#up-and-down)
* [Eğitim sırasında verilere erişin](#train)
* [Verileri Azure 'a taşıma](#move)

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

- Azure [BLOB kapsayıcısı](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) veya [Azure dosya paylaşımıyla](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)Azure depolama hesabı.

- [Python için Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)veya [Azure Machine Learning Studio](https://ml.azure.com/)'ya erişim.

- Azure Machine Learning çalışma alanı. 
    - [Azure Machine Learning bir çalışma alanı oluşturun](how-to-manage-workspace.md) veya Python SDK 'sını kullanarak mevcut bir tane kullanın.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Veri depoları oluşturma ve kaydetme

Bir Azure depolama çözümünü bir veri deposu olarak kaydettiğinizde, bu veri deposunu otomatik olarak belirli bir çalışma alanında oluşturursunuz. Python SDK veya Azure Machine Learning Studio kullanarak bir çalışma alanına veri depoları oluşturabilir ve kaydedebilirsiniz.

### <a name="using-the-python-sdk"></a>Python SDK'yı kullanma

Tüm yazmaç yöntemleri [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) sınıftır ve register_azure_ * biçiminde bulunur.

Register () metodunu doldurmanız için gereken bilgiler [Azure Machine Learning Studio](https://ml.azure.com)aracılığıyla bulunabilir. Sol bölmede **depolama hesapları** ' nı seçin ve kaydettirmek istediğiniz depolama hesabını seçin. **Genel bakış** sayfası, hesap adı ve kapsayıcısı ya da dosya paylaşma adı gibi bilgiler sağlar. Hesap anahtarı veya SAS belirteci gibi kimlik doğrulama bilgileri için sol taraftaki **Ayarlar** bölmesi altındaki **Hesap anahtarlarına** gidin. 

Aşağıdaki örneklerde bir Azure Blob kapsayıcısını veya Azure dosya paylaşımının bir veri deposu olarak nasıl kaydedileceği gösterilmektedir.

+ **Azure Blob kapsayıcısı veri deposu**için [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) kullanın

    Aşağıdaki kod, `my_datastore`veri deposunu oluşturup `ws`çalışma alanına kaydeder. Bu veri deposu Azure Blob kapsayıcısına, Azure depolama hesabındaki `my_blob_container`, sağlanmış hesap anahtarını kullanarak `my_storage_account` erişir.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```
    Depolama hesabınız bir sanal ağda ise, yalnızca Azure blob veri deposu oluşturma desteklenir. Çalışma alanınızın depolama hesabınıza erişmesine izin vermek için, `True` `grant_workspace_access` parametresini ayarlayın.

+ **Azure dosya paylaşımında veri deposu**için [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)kullanın. 

    Aşağıdaki kod, `my_datastore`veri deposunu oluşturup `ws`çalışma alanına kaydeder. Bu veri deposu, Azure depolama hesabındaki `my_file_share`Azure dosya paylaşımına (`my_storage_account`, belirtilen hesap anahtarı kullanılarak) erişir.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Depolama yönergeleri

Azure Blob kapsayıcısını öneririz. Blob 'lar için hem standart hem de Premium depolama alanı kullanılabilir. Daha pahalı olsa da, özellikle büyük bir veri kümesine göre eğiteseniz, eğitimin hızını iyileştirebilecek daha hızlı üretilen iş hızları nedeniyle Premium Depolama önerilir. Depolama hesabı maliyet bilgileri için [Azure Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) ' na bakın.

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning Studio 'yu kullanma 

Azure Machine Learning Studio 'da birkaç adımda yeni bir veri deposu oluşturun.

1. [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.
1. **Yönet**altında sol bölmedeki **veri depoları** ' nı seçin.
1. **+ Yeni veri deposu**seçin.
1. Yeni veri deposu formunu doldurun. Form, Azure depolama türü ve kimlik doğrulama türü seçimlerini temel alan bir şekilde günceller.
  
Formu doldurmanız için gereken bilgiler [Azure Machine Learning Studio](https://ml.azure.com)aracılığıyla bulunabilir. Sol bölmede **depolama hesapları** ' nı seçin ve kaydettirmek istediğiniz depolama hesabını seçin. **Genel bakış** sayfası, hesap adı ve kapsayıcısı ya da dosya paylaşma adı gibi bilgiler sağlar. Hesap anahtarı veya SAS belirteci gibi kimlik doğrulama öğeleri için soldaki **Ayarlar** bölmesinde bulunan **Hesap anahtarlarına** gidin.

Aşağıdaki örnekte formun Azure blob veri deposu oluşturmak için nasıl görüneceğine ilişkin bir örnek gösterilmektedir. 
    
 ![Yeni veri deposu](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Çalışma alanınızdan veri depoları alın

Geçerli çalışma alanında kayıtlı belirli bir veri deposunu almak için, veri deposu sınıfında [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) static metodunu kullanın:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Belirli bir çalışma alanıyla kaydedilen veri depolarının listesini almak için, bir çalışma alanı nesnesi üzerinde [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) özelliğini kullanabilirsiniz:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Bir çalışma alanı oluşturduğunuzda, bir Azure Blob kapsayıcısı ve bir Azure dosya paylaşımında sırasıyla `workspaceblobstore` ve `workspacefilestore` adlı çalışma alanına kaydedilir. Blob kapsayıcısının bağlantı bilgilerini ve çalışma alanına bağlı depolama hesabında sağlanan dosya paylaşımının depolarlar. `workspaceblobstore` varsayılan veri deposu olarak ayarlanır.

Çalışma alanının varsayılan veri deposunu almak için:

```Python
datastore = ws.get_default_datastore()
```

Geçerli çalışma alanı için farklı bir varsayılan veri deposu tanımlamak üzere çalışma alanı nesnesi üzerinde [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) yöntemi kullanın:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Karşıya yükleme & indirme verileri
Aşağıdaki örneklerde açıklanan [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) ve [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) yöntemleri, [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) ve [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) sınıfları için özeldir ve aynı şekilde çalışır.

### <a name="upload"></a>Karşıya Yükle

 Python SDK kullanarak veri deposuna bir dizin ya da tek bir dosya yükleyin.

Bir dizini bir veri deposuna yüklemek için `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` parametresi, karşıya yüklenecek dosya paylaşımında (veya blob kapsayıcısında) konumu belirtir. Bu, verilerin köke yüklendiği `None`varsayılan olarak olur. Aksi takdirde, `target_path` konumundaki mevcut verilerin üzerine yazılırsa `overwrite=True`.

Ya da `upload_files()` yöntemi aracılığıyla veri deposuna tek tek dosyaların bir listesini yükleyin.

### <a name="download"></a>İndir

Benzer şekilde, verileri bir veri deposundan Yerel dosya sisteminize indirin.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` parametresi, verilerin indirileceği yerel dizinin konumudur. İndirilecek dosya paylaşımında (veya blob kapsayıcısında) klasörün yolunu belirtmek için bu yolu `prefix`sağlayın. `prefix` `None`, dosya paylaşımınızın (veya blob kapsayıcısının) tüm içerikleri indirilir.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Eğitim sırasında verilerinize erişin

> [!IMPORTANT]
> [Azure Machine Learning veri kümeleri](how-to-create-register-datasets.md) kullanmak, eğitiminde verilerinize erişmek için önerilen yeni yoldur. Veri kümeleri, Pandas veya Spark DataFrame 'e tablo verileri yükleyen ve Azure blob, Azure dosyası, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL ' den herhangi bir biçimde dosya indirme veya bağlama olanağı sunan işlevler sağlar. [Veri kümeleriyle eğitme](how-to-train-with-datasets.md)hakkında daha fazla bilgi edinin.

Aşağıdaki tabloda, işlem hedefine çalışma sırasında veri depolarını nasıl kullanacağınızı söyleyen Yöntemler listelenmiştir. 

Yapmanın|Yöntem|Açıklama|
----|-----|--------
Bağlama| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Veri deposunu işlem hedefine bağlamak için kullanın.
İndir|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Veri deposunun içeriğini `path_on_compute`tarafından belirtilen konuma indirmek için kullanın. <br><br> Bu indirme, çalıştırmadan önce oluşur.
Karşıya Yükle|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| `path_on_compute` tarafından belirtilen konumdan bir dosyayı veri deposuna yüklemek için kullanın. <br><br> Bu karşıya yükleme, çalıştırıldıktan sonra olur.

Veri deposundaki belirli bir klasöre veya dosyaya başvurmak ve işlem hedefinde kullanılabilir hale getirmek için veri deposu [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) yöntemini kullanın.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Belirtilen `datastore` veya `datastore.path` nesnesi, değeri, hedef işlem üzerindeki bağlama/indirme yolunu temsil eden biçim `"$AZUREML_DATAREFERENCE_XXXX"`ortam değişkeni adına çözümleniyor. Hedef işlem üzerindeki veri deposu yolu, eğitim betiğinin yürütme yoluyla aynı olmayabilir.

### <a name="examples"></a>Örnekler 

Aşağıdaki kod örnekleri, eğitim sırasında verilere erişmek için [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) sınıfına özgüdür. 

`script_params`, entry_script parametreleri içeren bir sözlüktür. Bunu kullanarak veri deposuna geçiş yapın ve verilerin işlem hedefi üzerinde nasıl kullanılabilir yapıldığını tanımlayabilirsiniz. Uçtan uca [öğreticimizden](tutorial-train-models-with-aml.md)daha fazla bilgi edinin.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Ayrıca veri depolarınızı/veritabanından veri bağlamak veya buralardan veri kopyalamak için Estimator Oluşturucusu `inputs` parametreye bir veri depoları listesi geçirebilirsiniz. Bu kod örneği:
* Eğitim betiğinizin `train.py` çalıştırılabilmesi için `datastore1` tüm içeriğini işlem hedefine indirir
* `train.py` çalıştırılmadan önce `datastore2` `'./foo'` klasörü işlem hedefine indirir
* Komut dosyanız çalıştıktan sonra işlem hedefinden dosya `'./bar.pkl'` `datastore3` yükler

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>İşlem ve veri deposu matrisi

Datamağazaların Şu anda aşağıdaki matriste listelenen depolama hizmetlerine bağlantı bilgilerini depolamayı desteklemektedir. Bu matris, farklı işlem hedefleri ve veri deposu senaryoları için kullanılabilir veri erişim işlevlerini görüntüler. [Azure Machine Learning için işlem hedefleri](how-to-set-up-training-targets.md#compute-targets-for-training)hakkında daha fazla bilgi edinin.

|İşlem|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [azuressqldatabasedatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Yerel|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Yok         |Yok                                                                         |
| Azure Machine Learning Işlem |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;işlem hatları](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;işlem hatları](concept-ml-pipelines.md)|Yok         |Yok                                                                         |
| Sanal makineler               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Yok         |Yok                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Yok         |Yok                                                                         |
| Veri aktarımı                  |[ML&nbsp;işlem hatları](concept-ml-pipelines.md)                                               |Yok                                           |[ML&nbsp;işlem hatları](concept-ml-pipelines.md)            |[ML&nbsp;işlem hatları](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;işlem hatları](concept-ml-pipelines.md)                                              |Yok                                           |[ML&nbsp;işlem hatları](concept-ml-pipelines.md)             |Yok                                                                         |
| Azure Batch                    |[ML&nbsp;işlem hatları](concept-ml-pipelines.md)                                               |Yok                                           |Yok         |Yok                                                                         |
| Azure Data Lake Analytics       |Yok                                           |Yok                                           |[ML&nbsp;işlem hatları](concept-ml-pipelines.md)             |Yok                                                                         |

> [!NOTE]
> Son derece yinelemeli, büyük veri işlemlerinin `as_mount()`yerine `as_download()` kullanarak daha hızlı çalıştığı senaryolar olabilir. Bu, experimentally doğrulanabilir.

### <a name="accessing-source-code-during-training"></a>Eğitim sırasında kaynak koduna erişme

Azure Blob depolama, Azure dosya paylaşımından daha yüksek aktarım hızına sahiptir ve paralel olarak başlatılan çok sayıda işi ölçeklendirecektir. Bu nedenle, çalıştırmaların kaynak kodu dosyalarını aktarmak için blob depolamayı kullanacak şekilde yapılandırılmasını öneririz.

Aşağıdaki kod örneği, kaynak kodu aktarımları için kullanılacak blob veri deposuna ait çalıştırma yapılandırması ' nda belirtir.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Puanlama sırasında verilere erişin

Azure Machine Learning, modellerinizi Puanlama için kullanmanın birkaç yolunu sağlar. Bu yöntemlerin bazıları veri depolarına erişim sağlamaz. Puanlama sırasında veri depolarına erişmenize izin veren yöntemleri anlamak için aşağıdaki tabloyu kullanın:

| Yöntem | Veri deposu erişimi | Açıklama |
| ----- | :-----: | ----- |
| [Toplu tahmin](how-to-run-batch-predictions.md) | ✔ | Büyük miktarlarda verileri zaman uyumsuz olarak tahmin edin. |
| [Web hizmeti](how-to-deploy-and-where.md) | &nbsp; | Modelleri Web hizmeti olarak dağıtın. |
| [IoT Edge modülü](how-to-deploy-and-where.md) | &nbsp; | Modelleri IoT Edge cihazlara dağıtın. |

SDK 'nın veri depolarına erişim sağlamadığı durumlarda, verilere erişmek için ilgili Azure SDK 'sını kullanarak özel kod oluşturabilirsiniz. Örneğin, [Python Için Azure depolama SDK 'sı](https://github.com/Azure/azure-storage-python) , bloblarda veya dosyalarda depolanan verilere erişmek için kullanabileceğiniz bir istemci kitaplığıdır.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Verileri desteklenen Azure depolama çözümlerine taşıma

Azure Machine Learning, Azure blob, Azure dosyası, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL verilerine erişimi destekler. Desteklenmeyen depolama için, Machine Learning denemeleri sırasında veri çıkış maliyetini kaydetmek üzere verilerinizi Azure Data Factory kullanarak desteklenen Azure depolama çözümlerimize taşımanızı öneririz. Azure Data Factory, ek bir ücret ödemeden Azure veri Hizmetleri, şirket içi veri kaynakları, Amazon S3 ve Redshift ve Google BigQuery dahil 80 ' den fazla yerleşik bağlayıcı ile verimli ve esnek veri aktarımı sağlar. [Azure Data Factory kullanarak verilerinizi taşımak için adım adım kılavuzu izleyin](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Sonraki adımlar

* [Bir modeli eğitme](how-to-train-ml-models.md).

* [Model dağıtın](how-to-deploy-and-where.md).