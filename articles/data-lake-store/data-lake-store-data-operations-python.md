---
title: 'Python: Azure Data Lake Storage 1. dosya sistemi işlemleri | Microsoft Docs'
description: Data Lake Storage 1. dosya sistemiyle çalışmak için Python SDK 'yı nasıl kullanacağınızı öğrenin.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f2ee982e2c1e1c363a391779721f848b8ae6afd5
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088902"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Python kullanarak Azure Data Lake Storage 1. dosya sistemi işlemleri
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Bu makalede, Azure Data Lake Storage 1. üzerinde dosya sistemi işlemleri gerçekleştirmek için Python SDK 'yı nasıl kullanacağınızı öğreneceksiniz. Python kullanarak Data Lake Storage 1. hesap yönetimi işlemlerinin nasıl gerçekleştirileceği hakkında yönergeler için bkz. [Python kullanarak Data Lake Storage 1. hesap yönetimi işlemleri](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Önkoşullar

* **Python**. Python’u [buradan](https://www.python.org/downloads/) indirebilirsiniz. Bu makalede Python 3.6.2 kullanılmıştır.

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage 1. hesabı**. [Azure Portal kullanarak Azure Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-portal.md)yönergelerini izleyin.

## <a name="install-the-modules"></a>Modülleri yükleme

Python kullanarak Data Lake Storage 1. çalışmak için üç modül yüklemeniz gerekir.

* `azure-mgmt-resource` modülü, Active Directory gibi şeyler için Azure modüllerini içerir.
* Azure Data Lake Storage 1. hesap yönetimi işlemlerini içeren modül.`azure-mgmt-datalake-store` Bu modülle ilgili daha fazla bilgi için bkz. [Azure-MGMT-datalake-Store modülü başvurusu](/python/api/azure-mgmt-datalake-store/).
* Azure Data Lake Storage 1. dosya sistemi işlemlerini içeren modül.`azure-datalake-store` Bu modülle ilgili daha fazla bilgi için bkz. [Azure-datalake-Store dosya-sistem modülü başvurusu](https://azure-datalake-store.readthedocs.io/en/latest/).

Modülleri yüklemek için aşağıdaki komutları kullanın.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

1. Tercih ettiğiniz IDE’de yeni bir Python uygulaması (örneğin, **örneğim.py**) oluşturun.

2. Gerekli modülleri içeri aktarmak için aşağıdaki satırları ekleyin

   ```
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Değişiklikleri örneğim.py uygulamasına kaydedin.

## <a name="authentication"></a>Authentication

Bu bölümde Azure AD ile gerçekleştirilen farklı kimlik doğrulama yöntemlerinden bahsedeceğiz. Şu seçenekleri kullanabilirsiniz:

* Uygulamanız için son kullanıcı kimlik doğrulaması için bkz. [Python kullanarak Data Lake Storage 1. Ile Son Kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-python.md).
* Uygulamanıza yönelik hizmetten hizmete kimlik doğrulaması için bkz. [Python kullanarak Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Dosya sistemi istemcisini oluşturma

Aşağıdaki kod parçacığı ilk olarak Data Lake Storage 1. hesabı istemcisini oluşturur. Bir Data Lake Storage 1. hesabı oluşturmak için istemci nesnesini kullanır. Kod parçacığı son olarak bir dosya sistemi istemci nesnesi oluşturur.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Dizin oluşturma

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Dosyayı karşıya yükleme


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Dosya indirme

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Bir dizini silme

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Sonraki adımlar
* [Python kullanarak Data Lake Storage 1. hesap yönetimi işlemleri](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Data Lake Storage 1. Python (FileSystem) başvurusu](https://azure-datalake-store.readthedocs.io/en/latest)
* [Azure Data Lake Storage 1. uyumlu açık kaynak büyük veri uygulamaları](data-lake-store-compatible-oss-other-applications.md)
