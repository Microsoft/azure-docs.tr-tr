---
title: Azure 'da depolama hizmetlerine kimlik tabanlı veri erişimi
titleSuffix: Machine Learning
description: Azure 'daki depolama hizmetlerine Azure Machine Learning veri depoları ve Machine Learning Python SDK ile bağlanmak için kimlik tabanlı veri erişimini nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210661"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Kimlik tabanlı veri erişimi (Önizleme) kullanarak depolamaya bağlanma

>[!IMPORTANT]
> Bu makalede sunulan özellikler önizleme aşamasındadır. Bunlar, herhangi bir zamanda değişebilir, [deneysel](/python/api/overview/azure/ml/#stable-vs-experimental) Önizleme özellikleri olarak düşünülmelidir.

Bu makalede, [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)aracılığıyla kimlik tabanlı veri erişimini ve Azure Machine Learning veri depolarını kullanarak Azure 'da depolama hizmetlerine nasıl bağlanacağınızı öğreneceksiniz.  

Genellikle, veri depoları depolama hizmetine erişim izninizin olduğunu onaylamak için kimlik bilgisi tabanlı veri erişimini kullanır. Bunlar, çalışma alanıyla ilişkili [anahtar kasasındaki](https://azure.microsoft.com/services/key-vault/) abonelik kimliğiniz ve belirteç yetkilendirmesi gibi bağlantı bilgilerini tutar. Kimlik tabanlı veri erişimi kullanan bir veri deposu oluşturduğunuzda, depolama hizmetine erişim izninizin olduğunu doğrulamak için Azure hesabınız ([Azure Active Directory belirteç](../active-directory/fundamentals/active-directory-whatis.md)) kullanılır. Bu senaryoda kimlik doğrulama kimlik bilgileri kaydedilmez. Yalnızca depolama hesabı bilgileri veri deposunda depolanır. 

Erişim anahtarları veya hizmet sorumluları gibi kimlik bilgisi tabanlı kimlik doğrulaması kullanan veri depoları oluşturmak için bkz. [Azure 'da depolama hizmetlerine bağlanma](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Azure Machine Learning 'de kimlik tabanlı veri erişimi

Azure Machine Learning ' de kimlik tabanlı veri erişimi uygulayabileceğiniz iki senaryo vardır. Bu senaryolar, gizli verilerle çalışırken ve daha ayrıntılı veri erişim yönetimine ihtiyaç duyduğunuzda kimlik tabanlı erişim için uygun bir uygulamadır:

- Depolama hizmetlerine erişme
- Özel verilerle eğitim makine öğrenimi modelleri

### <a name="accessing-storage-services"></a>Depolama hizmetlerine erişme

Depolama hizmetlerine, Azure Machine Learning veri depoları veya [Azure Machine Learning veri kümeleri](how-to-create-register-datasets.md)ile kimlik tabanlı veri erişimi aracılığıyla bağlanabilirsiniz. 

Kimlik doğrulama kimlik bilgileriniz genellikle depolama hizmetine erişim izninizin olduğundan emin olmak için kullanılan bir veri deposunda tutulur. Bu kimlik bilgileri veri depoları aracılığıyla kaydedildiğinde, çalışma alanı okuyucu rolüne sahip tüm kullanıcılar bunları alabilir. Bu erişim ölçeği, bazı kuruluşlarda ilgili bir güvenlik sorunu olabilir. [Çalışma alanı okuyucu rolü hakkında daha fazla bilgi edinin.](how-to-assign-roles.md#default-roles) 

Kimlik tabanlı veri erişimi kullandığınızda Azure Machine Learning, veri deposunda kimlik bilgilerinizi tutmak yerine veri erişimi kimlik doğrulaması için Azure Active Directory belirtecinizi ister. Bu yaklaşım, depolama düzeyinde veri erişimi yönetimine izin verir ve kimlik bilgilerinin gizli kalmasını sağlar. 

Aynı davranış şunları yaptığınızda geçerlidir:

* [Doğrudan depolama URL 'lerinden bir veri kümesi oluşturun](#use-data-in-storage). 
* Yerel bilgisayarınızdaki veya [işlem örneğinizin](concept-compute-instance.md)Jupyter Notebook aracılığıyla Etkileşimli verilerle çalışın.

> [!NOTE]
> Kimlik bilgileri tabanlı kimlik doğrulaması aracılığıyla depolanan kimlik bilgileri, abonelik kimliklerini, paylaşılan erişim imzasını (SAS) belirteçlerini ve istemci kimlikleri ve kiracı kimlikleri gibi depolama erişim anahtarı ve hizmet sorumlusu bilgilerini içerir.

### <a name="model-training-on-private-data"></a>Özel veriler üzerinde model eğitimi

Belirli makine öğrenimi senaryoları, özel verilerle eğitim modelleri içerir. Bu gibi durumlarda, veri bilimcilerinin, gizli giriş verilerine maruz kalmadan eğitim iş akışlarını çalıştırması gerekir. Bu senaryoda, veri erişimi kimlik doğrulaması için eğitim işlem tarafından yönetilen bir kimlik kullanılır. Bu yaklaşım, Storage Admins 'in eğitim işini çalıştırmak için eğitim işlemini kullandığı yönetilen kimliğe Depolama Blobu veri okuyucusu erişimi vermesini sağlar. Bireysel veri bilimcilerine erişim verilmesi gerekmez. Daha fazla bilgi için bkz. [bir işlem kümesi üzerinde yönetilen kimlik ayarlama](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

- Desteklenen depolama türü olan bir Azure depolama hesabı. Bu depolama türleri önizlemede destekleniyor: 
    - [Azure Blob Depolama](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage 2. Nesil](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Veritabanı](../azure-sql/database/sql-database-paas-overview.md)

- [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/install).

- Azure Machine Learning çalışma alanı.
  
  [Azure Machine Learning bir çalışma alanı oluşturun](how-to-manage-workspace.md) ya da [Python SDK 'sı aracılığıyla mevcut bir tane](how-to-manage-workspace.md#connect-to-a-workspace)kullanın. 

## <a name="storage-access-permissions"></a>Depolama erişim izinleri

Azure 'da depolama hizmetinize güvenli bir şekilde bağlandığınızdan emin olmak için Azure Machine Learning, karşılık gelen veri depolamaya erişmek için izninizin olmasını gerektirir.

Kimlik tabanlı veri erişimi yalnızca aşağıdaki depolama hizmetlerine yönelik bağlantıları destekler:

* Azure Blob Depolama
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Veritabanı

Bu depolama hizmetlerine erişmek için en az [Depolama Blobu veri okuyucusu](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) erişiminizin olması gerekir. Yalnızca depolama hesabı sahipleri [Azure Portal aracılığıyla erişim düzeyinizi değiştirebilir](../storage/common/storage-auth-aad-rbac-portal.md).

Bir modeli uzak bir işlem hedefinde eğliyorsanız, işlem kimliği depolama hizmetinden en az Depolama Blobu veri okuyucusu rolüne verilmelidir. [Bir işlem kümesinde yönetilen kimlik ayarlamayı](how-to-create-attach-compute-cluster.md#managed-identity)öğrenin.

## <a name="work-with-virtual-networks"></a>Sanal ağlarla çalışma

Azure Machine Learning, varsayılan olarak, bir güvenlik duvarının arkasında veya bir sanal ağda yer alan bir depolama hesabıyla iletişim kuramaz.

Depolama hesaplarını yalnızca belirli sanal ağların içinden erişime izin verecek şekilde yapılandırabilirsiniz. Bu yapılandırma, verilerin ağ dışından sızmasını sağlamak için ek adımlar gerektirir. Bu davranış, kimlik bilgisi tabanlı veri erişimi için aynıdır. Daha fazla bilgi için bkz. [sanal ağ senaryolarını yapılandırma](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Veri depoları oluşturma ve kaydetme

Azure 'da bir depolama hizmetini bir veri deposu olarak kaydettiğinizde, bu veri deposunu otomatik olarak oluşturur ve belirli bir çalışma alanına kaydedersiniz. Gerekli izin türleri hakkında rehberlik için bkz. [depolama erişim izinleri](#storage-access-permissions) . Sanal ağların arkasında veri depolamaya bağlanma hakkında ayrıntılı bilgi için bkz. [sanal ağlarla çalışma](#work-with-virtual-networks) .

Aşağıdaki kodda,,, `sas_token` `account_key` `subscription_id` ve hizmet sorumlusu gibi kimlik doğrulama parametrelerinin yokluğuna dikkat edin `client_id` . Bu atlama, Azure Machine Learning kimlik doğrulaması için kimlik tabanlı veri erişimini kullanacağını gösterir. Veri depolarının oluşturulması genellikle bir not defterinde veya Studio aracılığıyla etkileşimli olarak gerçekleşir. Bu nedenle Azure Active Directory belirteciniz veri erişimi kimlik doğrulaması için kullanılır.

> [!NOTE]
> Veri deposu adları yalnızca küçük harfler, rakamlar ve alt çizgilerden oluşmalıdır. 

### <a name="azure-blob-container"></a>Azure Blob kapsayıcısı

Bir Azure Blob kapsayıcısını bir veri deposu olarak kaydetmek için kullanın [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Aşağıdaki kod, `credentialless_blob` veri deposunu oluşturur, `ws` çalışma alanına kaydeder ve `blob_datastore` değişkenine atar. Bu veri deposu, `my_container_name` depolama hesabındaki blob kapsayıcısına erişir `my-account-name` .

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage 1. bağlanan bir veri deposunu kaydetmek için [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) kullanın.

Aşağıdaki kod, `credentialless_adls1` veri deposunu oluşturur, `workspace` çalışma alanına kaydeder ve `adls_dstore` değişkenine atar. Bu veri deposu `adls_storage` Azure Data Lake Storage hesabına erişir.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage 2. bağlanan bir veri deposunu kaydetmek için [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) kullanın.

Aşağıdaki kod, `credentialless_adls2` veri deposunu oluşturur, `ws` çalışma alanına kaydeder ve `adls2_dstore` değişkenine atar. Bu veri deposu, depolama hesabındaki dosya sistemine erişir `tabular` `myadls2` .  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Depolamadaki verileri kullanma

Azure Machine Learning depolamayla verilerinizle etkileşim kurarken [Azure Machine Learning veri kümelerini](how-to-create-register-datasets.md) kullanmanızı öneririz. 

Veri kümeleri, eğitim gibi makine öğrenimi görevleri için verilerinizi geç tarafından değerlendirilen bir tüketilebilir nesneye paketleyin. Ayrıca, veri kümeleri ile Azure Blob depolama gibi Azure depolama hizmetlerinden ve bir işlem hedefine Azure Data Lake Storage her türlü biçimdeki dosyaları [indirebilir veya bağlayabilirsiniz](how-to-train-with-datasets.md#mount-vs-download) .


Kimlik tabanlı veri erişimi olan veri kümeleri oluşturmak için aşağıdaki seçeneklere sahip olursunuz. Bu tür veri kümesi oluşturma, veri erişimi kimlik doğrulaması için Azure Active Directory belirtecinizi kullanır. 

*  Kimlik tabanlı veri erişimi de kullanan veri mağazalarından başvuru yolları. 
<br>Aşağıdaki örnekte, `blob_datastore` zaten var ve kimlik tabanlı veri erişimi kullanılıyor.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Veri deposu oluşturmayı atlayın ve doğrudan depolama URL 'Lerinden veri kümeleri oluşturun. Bu işlevsellik şu anda yalnızca Azure bloblarını ve Azure Data Lake Storage 1. ve Gen2 desteklemektedir.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Kimlik tabanlı veri erişimiyle oluşturulmuş bir veri kümesini tüketen bir eğitim işi gönderdiğinizde, eğitim sürecinin yönetilen kimliği veri erişimi kimlik doğrulaması için kullanılır. Azure Active Directory belirtecinizin kullanımı kullanılmıyor. Bu senaryo için, işlem yönetilen kimliğinin depolama hizmetinden en az Depolama Blobu veri okuyucusu rolüne sahip olduğundan emin olun. Daha fazla bilgi için bkz. [işlem kümelerinde yönetilen kimliği ayarlama](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Machine Learning veri kümesi oluşturma](how-to-create-register-datasets.md)
* [Veri kümeleriyle eğitme](how-to-train-with-datasets.md)
* [Anahtar tabanlı veri erişimi ile bir veri deposu oluşturma](how-to-access-data.md)
