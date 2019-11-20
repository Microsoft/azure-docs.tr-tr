---
title: 'Son Kullanıcı kimlik doğrulaması: Azure Active Directory kullanarak Azure Data Lake Storage 1. Python | Microsoft Docs'
description: Python ile Azure Active Directory kullanarak Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması elde etme hakkında bilgi edinin
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6a7e1b36e01094ea8ce65a785de0aad9494f4dbb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088869"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Python kullanarak Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması
> [!div class="op_single_selector"]
> * [Java kullanma](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK’yı kullanma](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python’u kullanma](data-lake-store-end-user-authenticate-python.md)
> * [REST API’sini kullanma](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Bu makalede, Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması yapmak için Python SDK 'sını kullanma hakkında bilgi edineceksiniz. Son Kullanıcı kimlik doğrulaması, daha fazla iki kategoriye ayrılabilir:

* Multi-Factor Authentication olmadan son kullanıcı kimlik doğrulaması
* Multi-Factor Authentication ile son kullanıcı kimlik doğrulaması

Bu seçeneklerin her ikisi de bu makalede ele alınmıştır. Python kullanarak Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması için bkz. [Python kullanarak Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Önkoşullar

* **Python**. Python’u [buradan](https://www.python.org/downloads/) indirebilirsiniz. Bu makalede Python 3.6.2 kullanılmıştır.

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).

* **Bir Azure Active Directory "yerel" uygulaması oluşturun**. [Azure Active Directory kullanarak Data Lake Storage 1. Ile Son Kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-using-active-directory.md)adımlarını tamamlamış olmanız gerekir.

## <a name="install-the-modules"></a>Modülleri yükleme

Python kullanarak Data Lake Storage 1. çalışmak için üç modül yüklemeniz gerekir.

* `azure-mgmt-resource` modülü, Active Directory gibi şeyler için Azure modüllerini içerir.
* Azure Data Lake Storage 1. hesap yönetimi işlemlerini içeren modül.`azure-mgmt-datalake-store` Bu modülle ilgili daha fazla bilgi için bkz. [Azure Data Lake Storage 1. Management Module Reference](/python/api/azure-mgmt-datalake-store/).
* Azure Data Lake Storage 1. dosya sistemi işlemlerini içeren modül.`azure-datalake-store` Bu modülle ilgili daha fazla bilgi için bkz. [Azure-datalake-Store FileSystem Module Reference](https://azure-datalake-store.readthedocs.io/en/latest/).

Modülleri yüklemek için aşağıdaki komutları kullanın.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

1. Tercih ettiğiniz IDE 'de, yeni bir Python uygulaması oluşturun, örneğin, **Mysample.py**.

2. Gerekli modülleri içeri aktarmak için aşağıdaki kod parçacığını ekleyin

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Değişiklikleri örneğim.py uygulamasına kaydedin.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Multi-Factor Authentication ile son kullanıcı kimlik doğrulaması

### <a name="for-account-management"></a>Hesap yönetimi için

Data Lake Storage 1. hesapta hesap yönetimi işlemleri için Azure AD 'de kimlik doğrulaması yapmak üzere aşağıdaki kod parçacığını kullanın. Aşağıdaki kod parçacığını uygulamanızda multi-factor authentication ile kimlik doğrulaması gerçekleştirmek için kullanabilirsiniz. Mevcut bir Azure AD **Yerel** uygulaması için aşağıdaki değerleri girin.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Dosya sistemi işlemleri için

Data Lake Storage 1. bir hesapta dosya sistemi işlemleri için Azure AD 'de kimlik doğrulaması yapmak üzere bunu kullanın. Aşağıdaki kod parçacığını uygulamanızda multi-factor authentication ile kimlik doğrulaması gerçekleştirmek için kullanabilirsiniz. Mevcut bir Azure AD **Yerel** uygulaması için aşağıdaki değerleri girin.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Multi-Factor Authentication olmadan son kullanıcı kimlik doğrulaması

Bu kullanım dışıdır. Daha fazla bilgi için bkz. [Python SDK kullanarak Azure kimlik doğrulaması](/azure/python/python-sdk-azure-authenticate).
   
## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Python kullanarak Azure Data Lake Storage 1. kimlik doğrulaması yapmak için son kullanıcı kimlik doğrulamasını nasıl kullanacağınızı öğrendiniz. Artık Azure Data Lake Storage 1. ile çalışmak için Python kullanma hakkında konuşabilecek aşağıdaki makalelere bakabilirsiniz.

* [Python kullanarak Data Lake Storage 1. hesap yönetimi işlemleri](data-lake-store-get-started-python.md)
* [Python kullanarak Data Lake Storage 1. veri işlemleri](data-lake-store-data-operations-python.md)

