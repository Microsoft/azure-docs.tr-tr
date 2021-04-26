---
title: BLOB depolama gereksinimlerini Data Box Microsoft Azure | Microsoft Docs
description: Azure Data Box blob depolaması için API 'Ler, SDK 'Lar ve istemci kitaplıkları için desteklenen sürümler hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91744125"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box BLOB depolama gereksinimleri

Bu makalede, Azure API 'Leri, Azure istemci kitaplıkları ve Data Box blob depolamayla desteklenen araçların sürümleri listelenir. Data Box BLOB depolama, Azure ile tutarlı semantiklerle blob yönetim işlevleri sağlar. Bu makalede ayrıca Azure depolama hizmetlerinden bilinen Azure Data Box BLOB depolama farklılıkları özetlenmektedir.

Data Box BLOB depolama alanına bağlanmadan önce bilgileri dikkatlice incelemenizi ve sonra gerektiği gibi geri başvurmalarını öneririz.


## <a name="storage-differences"></a>Depolama farklılıkları

|     Özellik                                             |     Azure Depolama                                     |     Data Box Blob depolama alanı |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure Dosya Depolama                                   |    Bulut tabanlı SMB dosya paylaşımları destekleniyor              |    Desteklenmez      |
|    Bekleyen veriler için hizmet şifrelemesi                  |    256 bit AES şifrelemesi                             |    256 bit AES şifrelemesi |
|    Depolama hesabı türü                                 |    Genel amaçlı ve Azure Blob depolama hesapları    |    Yalnızca genel amaçlı v1|
|    Blob adı                                            |    1.024 karakter (2.048 bayt)                     |    880 karakter (1.760 bayt)|
|    Blok Blobu en büyük boyutu                              |    4,75 TB (100 MB X 50.000 blok)                   |    Azure Data Box v 3,0 ve sonraki sürümleri için 4,75 TB (100 MB x 50.000 blok).|
|    Sayfa Blobu en büyük boyut                               |    8 TB                                               |    1 TB                   |
|    Sayfa Blobu sayfa boyutu                                  |    512 bayt                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Desteklenen API sürümleri

Azure Storage Service API 'lerinin aşağıdaki sürümleri Data Box BLOB depolama ile desteklenir.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 sürümleri

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Desteklenen Azure istemci kitaplıkları

Data Box BLOB depolama için belirli istemci kitaplıkları ve belirli uç nokta soneki gereksinimleri vardır. Data Box BLOB depolama uç noktaları, Azure Blob depolama REST API en son sürümü ile tam eşlik içermez; [Azure Data Box 3,0 ve üzeri sürümler için desteklenen sürümlere](#supported-api-versions)bakın. Depolama istemci kitaplıkları için, REST API uyumlu sürümü bilmeniz gerekir.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 sürümleri

Data Box BLOB depolama için aşağıdaki Azure istemci Kitaplığı sürümleri desteklenir.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>PHP istemcisini besteci aracılığıyla Install-Current

Besteci aracılığıyla yüklemek için: (blobu örnek olarak al).
1. Aşağıdaki kodla projenin kökünde composer.jsadlı bir dosya oluşturun:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. `composer.phar`Proje köküne indirin.

3. Şunu çalıştırın: php besteci. phar Install.

### <a name="endpoint-declaration"></a>Uç nokta bildirimi

Data Box BLOB depolama SDK 'Sı, uç nokta son eki- `<device serial number>.microsoftdatabox.com` Data Box etki alanını tanımlar. Blob hizmeti uç noktası hakkında daha fazla bilgi için [Data Box BLOB depolama aracılığıyla bağlanma](data-box-deploy-copy-data-via-rest.md)bölümüne gidin.
 
## <a name="examples"></a>Örnekler

### <a name="net"></a>.NET

Data Box BLOB depolama için, uç nokta soneki `app.config` dosyada belirtilir:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Data Box blob depolaması için, uç nokta soneki bağlantı dizesinin kurulumunda belirtilir:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Data Box blob depolaması için, uç nokta soneki bildirim örneğinde belirtilir:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Data Box blob depolaması için, uç nokta soneki bağlantı dizesinin kurulumunda belirtilir:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Data Box blob depolaması için, uç nokta soneki bağlantı dizesinin kurulumunda belirtilir:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Data Box blob depolaması için, uç nokta soneki bildirim örneğinde belirtilir:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Data Box blob depolaması için, uç nokta soneki bağlantı dizesinin kurulumunda belirtilir:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Box dağıtın](data-box-deploy-ordered.md)
