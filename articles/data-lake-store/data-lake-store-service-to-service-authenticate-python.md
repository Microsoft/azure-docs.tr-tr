---
title: 'Hizmetten hizmete kimlik doğrulaması: Azure Active Directory kullanarak Azure Data Lake Storage 1. Python | Microsoft Docs'
description: Python kullanarak Azure Active Directory kullanarak Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması elde etme hakkında bilgi edinin
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
ms.openlocfilehash: b63209c9174867e69356bb6800d70502f2afdaa4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088821"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Python kullanarak Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması
> [!div class="op_single_selector"]
> * [Java kullanma](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK’yı kullanma](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python’u kullanma](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API’sini kullanma](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Bu makalede, Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması yapmak için Python SDK 'sını nasıl kullanacağınızı öğreneceksiniz. Python kullanarak Data Lake Storage 1. Son Kullanıcı kimlik doğrulaması için bkz. [Python kullanarak Data Lake Storage 1. Ile Son Kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Önkoşullar

* **Python**. Python’u [buradan](https://www.python.org/downloads/) indirebilirsiniz. Bu makalede Python 3.6.2 kullanılmıştır.

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Active Directory "Web" uygulaması oluşturun**. [Azure Active Directory kullanarak, Data Lake Storage 1. Ile hizmetten hizmete kimlik doğrulaması](data-lake-store-service-to-service-authenticate-using-active-directory.md)adımlarını tamamlamış olmanız gerekir.

## <a name="install-the-modules"></a>Modülleri yükleme

Python kullanarak Data Lake Storage 1. çalışmak için üç modül yüklemeniz gerekir.

* `azure-mgmt-resource` modülü, Active Directory gibi şeyler için Azure modüllerini içerir.
* Data Lake Storage 1. hesap yönetimi işlemlerini içeren modül.`azure-mgmt-datalake-store` Bu modülle ilgili daha fazla bilgi için bkz. [Azure Data Lake Storage 1. Management Module Reference](/python/api/azure-mgmt-datalake-store/).
* Data Lake Storage 1. dosya sistemi işlemlerini içeren modül.`azure-datalake-store` Bu modülle ilgili daha fazla bilgi için bkz. [Azure-datalake-Store FileSystem Module Reference](https://azure-datalake-store.readthedocs.io/en/latest/).

Modülleri yüklemek için aşağıdaki komutları kullanın.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

1. Tercih ettiğiniz IDE’de yeni bir Python uygulaması (örneğin, **örneğim.py**) oluşturun.

2. Gerekli modülleri içeri aktarmak için aşağıdaki kod parçacığını ekleyin

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Değişiklikleri örneğim.py uygulamasına kaydedin.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Hesap yönetimi için gizli anahtarla hizmetten hizmete kimlik doğrulaması

Data Lake Storage 1. hesabı oluşturma, Data Lake Storage 1. hesabı silme gibi Data Lake Storage 1. için Azure AD 'de hesap yönetimi işlemlerinde kimlik doğrulaması yapmak üzere bu kod parçacığını kullanın. Aşağıdaki kod parçacığı, mevcut bir Azure AD "Web App" uygulamasının bir uygulama/hizmet sorumlusu için istemci gizliliğini kullanarak, uygulamanızın etkileşimli olmayan kimlik doğrulaması için kullanılabilir.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Dosya sistemi işlemleri için gizli anahtarla hizmetten hizmete kimlik doğrulaması

Klasör oluşturma, dosya yükleme vb. gibi Data Lake Storage 1. dosya sistemi işlemleri için Azure AD 'de kimlik doğrulaması yapmak üzere aşağıdaki kod parçacığını kullanın. Gizli anahtar / hizmet sorumlusu kullanılarak aşağıdaki kod parçacığı uygulamanızın etkileşimli olmayan kimlik doğrulaması için kullanılabilir. Bunu mevcut Azure AD "Web App" uygulaması ile birlikte kullanın.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Python kullanarak Data Lake Storage 1. kimlik doğrulaması yapmak için hizmetten hizmete kimlik doğrulamasını nasıl kullanacağınızı öğrendiniz. Artık Data Lake Storage 1. ile çalışmak için Python kullanma hakkında konuşabilecek aşağıdaki makalelere bakabilirsiniz.

* [Python kullanarak Data Lake Storage 1. hesap yönetimi işlemleri](data-lake-store-get-started-python.md)
* [Python kullanarak Data Lake Storage 1. veri işlemleri](data-lake-store-data-operations-python.md)


