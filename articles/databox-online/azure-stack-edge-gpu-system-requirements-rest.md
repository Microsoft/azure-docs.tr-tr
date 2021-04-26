---
title: Microsoft Azure Stack Edge BLOB depolama gereksinimleri | Microsoft Docs
description: Azure Stack Edge blob depolaması için API 'Ler, SDK 'Lar ve istemci kitaplıkları için desteklenen sürümler hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 7deba32eb9d0e098b75f98cc81fac2c01b8bb7f8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567262"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Azure Stack Edge BLOB depolama gereksinimleri

Bu makalede Azure API 'Leri, Azure istemci kitaplıkları ve Azure Stack Edge blob depolamayla desteklenen araçların sürümleri listelenir. Azure Stack Edge BLOB depolama, Azure ile tutarlı semantiğe sahip blob yönetim işlevleri sağlar. Bu makalede ayrıca Azure depolama hizmetlerinden bilinen Azure Stack Edge BLOB depolama farklılıkları özetlenmektedir.

Azure Stack Edge BLOB depolama alanına bağlanmadan önce bilgileri dikkatlice incelemenizi ve sonra gerektiği gibi geri başvurmalarını öneririz.

## <a name="storage-differences"></a>Depolama farklılıkları

|     Özellik                                             |     Azure Depolama                                     |     Azure Stack Edge blob depolaması |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure Dosya Depolama                                   |    Bulut tabanlı SMB dosya paylaşımları destekleniyor              |    Desteklenmez      |
|    Depolama hesabı türü                                 |    Genel amaçlı ve Azure Blob depolama hesapları    |    Yalnızca genel amaçlı v1|
|    Blob adı                                            |    1.024 karakter (2.048 bayt)                     |    880 karakter (1.760 bayt)|
|    Blok Blobu en büyük boyutu                              |    4,75 TB (100 MB X 50.000 blok)                   |    Azure Stack Edge için 4,75 TB (100 MB x 50.000 blok)|
|    Sayfa Blobu en büyük boyut                               |    8 TB                                               |    1 TB                   |
|    Sayfa Blobu sayfa boyutu                                  |    512 bayt                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Desteklenen API sürümleri

Azure Storage Service API 'lerinin aşağıdaki sürümleri Azure Stack Edge BLOB depolama ile desteklenir.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 sürümleri

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Desteklenen Azure istemci kitaplıkları

Azure Stack Edge blob depolaması için belirli istemci kitaplıkları ve belirli uç nokta soneki gereksinimleri vardır. Azure Stack Edge BLOB depolama uç noktaları, Azure Blob depolama REST API en son sürümü ile tam eşlik içermez; [Azure Stack Edge için desteklenen API sürümleri](#supported-api-versions)bölümüne bakın. Depolama istemci kitaplıkları için, REST API uyumlu sürümü bilmeniz gerekir.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 sürümleri

Azure Stack Edge blob depolaması için aşağıdaki Azure istemci Kitaplığı sürümleri desteklenir.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>PHP istemcisini besteci aracılığıyla yüklemesi-güncel

PHP istemcisini besteci aracılığıyla yüklemek için:

1. Aşağıdaki kodla projenin kökünde composer.jsadlı bir dosya oluşturun (örneğin, Azure Depolama Blobu hizmetini kullanır).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. `composer.phar`Proje köküne indirin.

3. Şunu çalıştırın: php besteci. phar Install.


## <a name="endpoint-declaration"></a>Uç nokta bildirimi

Azure Stack Edge BLOB depolama SDK 'Sı, uç nokta son eki- `<device serial number>.microsoftdatabox.com` Azure Stack Edge etki alanını tanımlar. Blob hizmeti uç noktası hakkında daha fazla bilgi için [Azure Stack Edge Pro GPU ile depolama hesapları aracılığıyla veri aktarımı](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)konusuna gidin.


## <a name="examples"></a>Örnekler

### <a name="net"></a>.NET

Azure Stack Edge blob depolaması için uç nokta soneki `app.config` dosyada belirtilir:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Azure Stack Edge blob depolaması için uç nokta soneki bağlantı dizesinin kurulumunda belirtilir:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Azure Stack Edge blob depolaması için, uç nokta soneki bildirim örneğinde belirtilir:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Azure Stack Edge blob depolaması için, uç nokta soneki bağlantı dizesinin kurulumunda belirtilir:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Azure Stack Edge blob depolaması için, uç nokta soneki bağlantı dizesinin kurulumunda belirtilir:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Azure Stack Edge blob depolaması için, uç nokta soneki bildirim örneğinde belirtilir:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Azure Stack Edge blob depolaması için, uç nokta soneki bağlantı dizesinin kurulumunda belirtilir:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Sonraki adımlar

* [GPU ile Azure Stack Edge Pro 'yu dağıtmaya hazırlanma](azure-stack-edge-gpu-deploy-prep.md)