---
title: Azure Event Grid BLOB depolama olay şeması
description: Azure Event Grid ile BLOB depolama olayları için sunulan özellikleri açıklar
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 0ab81d3c1d4c68827cf1569bf4a22c3311fe355d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555830"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>BLOB depolama için olay şeması Azure Event Grid

Bu makalede, BLOB depolama olaylarına yönelik özellikler ve şema sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](event-schema.md).

Örnek betiklerin ve öğreticilerin bir listesi için bkz. [depolama olay kaynağı](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Blob REST API 'Lerinin olay listesi

Bu olaylar, bir istemci blob REST API 'Lerini çağırarak bir blobu oluşturduğunda, değiştirdiği veya sildiği zaman tetiklenir.

 |Olay adı |Açıklama|
 |----------|-----------|
 |**Microsoft. Storage. Bloboluşturuldu** |Bir blob oluşturulduğunda veya değiştirildiğinde tetiklenir. <br>Özellikle, bu olay, istemci blob REST API kullanılabilir `PutBlob`, `PutBlockList` veya `CopyBlob` işlemlerini kullandıklarında tetiklenir.   |
 |**Microsoft. Storage. BlobDeleted** |Blob silindiğinde tetiklenir. <br>Özellikle, bu olay, istemci blob REST API kullanılabilir `DeleteBlob` işlemini çağırdığında tetiklenir. |

> [!NOTE]
> **Microsoft. Storage. BlobCreated** olayının yalnızca bir Blok Blobu tamamen yürütüldüğü zaman tetiklendiğinden emin olmak istiyorsanız, `CopyBlob`, `PutBlob` ve `PutBlockList` REST API çağrılarına yönelik olayı filtreleyin. Bu API çağrıları, **Microsoft. Storage. BlobCreated** olayını yalnızca veriler bir blok blobuna tam olarak kaydedildikten sonra tetikler. Filtre oluşturmayı öğrenmek için bkz. [Event Grid olayları filtreleme](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage Gen 2 REST API 'Lerinin olayları listesi

Bu olaylar, depolama hesabında hiyerarşik bir ad alanı etkinleştirirseniz ve istemciler Azure Data Lake Storage 2. REST API 'Leri çağırdığında tetiklenir.

|Olay adı|Açıklama|
|----------|-----------|
|**Microsoft. Storage. Bloboluşturuldu** | Bir blob oluşturulduğunda veya değiştirildiğinde tetiklenir. <br>Özellikle, istemciler Azure Data Lake Storage 2. REST API kullanılabilir `CreateFile` ve `FlushWithClose` işlemlerini kullandıklarında bu olay tetiklenir. |
|**Microsoft. Storage. BlobDeleted** |Blob silindiğinde tetiklenir. <br>Özellikle, bu olay, istemciler Azure Data Lake Storage 2. REST API kullanılabilir `DeleteFile` işlemini çağırdığında da tetiklenir. |
|**Microsoft. Storage. Blobyeniden adlandırıldı**|Bir blob yeniden adlandırıldığında tetiklenir. <br>Özellikle, bu olay, istemciler Azure Data Lake Storage 2. REST API kullanılabilir `RenameFile` işlemini kullandıklarında tetiklenir.|
|**Microsoft. Storage. DirectoryCreated**|Bir dizin oluşturulduğunda tetiklenir. <br>Özellikle, bu olay, istemciler Azure Data Lake Storage 2. REST API kullanılabilir `CreateDirectory` işlemini kullandıklarında tetiklenir.|
|**Microsoft. Storage. Directoryyeniden adlandırıldı**|Bir dizin yeniden adlandırıldığında tetiklenir. <br>Özellikle, bu olay, istemciler Azure Data Lake Storage 2. REST API kullanılabilir `RenameDirectory` işlemini kullandıklarında tetiklenir.|
|**Microsoft. Storage. DirectoryDeleted**|Bir dizin silindiğinde tetiklenir. <br>Özellikle, bu olay, istemciler Azure Data Lake Storage 2. REST API kullanılabilir `DeleteDirectory` işlemini kullandıklarında tetiklenir.|

> [!NOTE]
> **Microsoft. Storage. BlobCreated** olayının yalnızca bir Blok Blobu tamamen yürütüldüğü zaman tetiklendiğinden emin olmak istiyorsanız, `FlushWithClose` REST API çağrısının olayını filtreleyin. Bu API çağrısı, **Microsoft. Storage. BlobCreated** olayını yalnızca veriler bir blok blobuna tam olarak kaydedildikten sonra tetikler. Filtre oluşturmayı öğrenmek için bkz. [Event Grid olayları filtreleme](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Olay yanıtının içeriği

Bir olay tetiklendiğinde, Event Grid hizmeti bu olayla ilgili verileri bu olay ile abone olacak şekilde gönderir.

Bu bölüm, her BLOB depolama etkinliği için verilerin nasıl görüneceğine ilişkin bir örnek içerir.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft. Storage. BlobCreated olayı

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobCreated olayı (Data Lake Storage 2.)

BLOB depolama hesabının hiyerarşik bir ad alanı varsa, veriler önceki örneğe benzer ve bu değişiklikler hariç olacak şekilde görünür:

* @No__t_0 anahtarı bir `2` değerine ayarlanır.

* @No__t_0 anahtarı dize `CreateFile` veya `FlushWithClose` olarak ayarlanır.

* @No__t_0 anahtarı veri kümesine dahil edilir.

> [!NOTE]
> Uygulamalar hesaba yeni bir blob yüklemek için `PutBlockList` işlemini kullanıyorsa, veriler bu değişiklikleri içermez.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft. Storage. BlobDeleted olayı

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobDeleted olayı (Data Lake Storage 2.)

BLOB depolama hesabının hiyerarşik bir ad alanı varsa, veriler önceki örneğe benzer ve bu değişiklikler hariç olacak şekilde görünür:

* @No__t_0 anahtarı bir `2` değerine ayarlanır.

* @No__t_0 anahtarı dize `DeleteFile` olarak ayarlanır.

* @No__t_0 anahtarı `dfs.core.windows.net` yolunu içerir.

> [!NOTE]
> Uygulamalar hesaptan bir blobu silmek için `DeleteBlob` işlemini kullanıyorsa, veriler bu değişiklikleri içermez.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft. Storage. Blobyeniden adlandırılmış olay

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft. Storage. DirectoryCreated olayı

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft. Storage. Directoryyeniden adlandırılan olay

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft. Storage. DirectoryDeleted olayı

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Olay özellikleri

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| konu başlığı | string | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| Konu | string | Olay konusunun yayımcı tanımlı yolu. |
| Türü | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| eventTime | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| id | string | Etkinliğin benzersiz tanımlayıcısı. |
| Verileri | object | BLOB depolama olay verileri. |
| Veri sürümü | string | Veri nesnesinin şema sürümü. Yayımcı, şema sürümünü tanımlar. |
| metadataVersion | string | Olay meta verilerinin şema sürümü. Event Grid üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| api | string | Olayı tetikleyen işlem. |
| Clientrequestıd 'ye sahip | string | depolama API 'SI işlemi için istemci tarafından sağlanmış bir istek kimliği. Bu kimlik, günlüklerdeki "istemci-istek-kimliği" alanı kullanılarak Azure depolama tanılama günlükleri ile ilişkilendirmek için kullanılabilir ve "x-MS-Client-Request-ID" üst bilgisi kullanılarak istemci isteklerinde sağlanabilirler. [Günlük biçimine](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)bakın. |
| No | string | Depolama API 'SI işlemi için hizmet tarafından oluşturulan istek kimliği. , Günlüklerdeki "istek-kimliği-üst bilgi" alanı kullanılarak Azure depolama tanılama günlükleri ile ilişkilendirmek için kullanılabilir ve ' x-MS-Request-id ' üst bilgisinde API çağrısını başlatma işleminden döndürülür. [Günlük biçimine](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)bakın. |
| Özelliği | string | İşlemleri koşullu olarak gerçekleştirmek için kullanabileceğiniz değer. |
| contentType | string | Blob için belirtilen içerik türü. |
| contentLength | integer | Blobun bayt cinsinden boyutu. |
| BlobType | string | Blob türü. Geçerli değerler "BlockBlob" ya da "PageBlob". |
| Contenentoffset | number | Olay tetikleme uygulamasının dosyaya yazmayı tamamladığı noktada gerçekleştirilen bir yazma işleminin bayt cinsinden değeri. <br>Yalnızca hiyerarşik bir ad alanına sahip BLOB depolama hesaplarında tetiklenen olaylar için görüntülenir.|
| Hedef URL |string | İşlem tamamlandıktan sonra var olacak dosyanın URL 'si. Örneğin, bir dosya yeniden adlandırılırsa, `destinationUrl` özelliği yeni dosya adının URL 'sini içerir. <br>Yalnızca hiyerarşik bir ad alanına sahip BLOB depolama hesaplarında tetiklenen olaylar için görüntülenir.|
| sourceUrl |string | İşlemden önce var olan dosyanın URL 'si. Örneğin, bir dosya yeniden adlandırılırsa `sourceUrl`, yeniden adlandırma işleminden önce özgün dosya adının URL 'sini içerir. <br>Yalnızca hiyerarşik bir ad alanına sahip BLOB depolama hesaplarında tetiklenen olaylar için görüntülenir. |
| url | string | Blobun yolu. <br>İstemci bir blob REST API kullanıyorsa, URL bu yapıya sahiptir: *\<storage-Account-name \>. blob.core.windows.net/\<container-adı \> / \<file-name \>* . <br>İstemci bir Data Lake Storage REST API kullanıyorsa, URL bu yapıya sahiptir: *\<storage-Account-name \>. dfs.core.windows.net/\<file-sistem-adı \> / \<file-Name*\>. |
| öz | string | tüm alt dizinlerde işlemi gerçekleştirmek için `True`; Aksi takdirde `False`. <br>Yalnızca hiyerarşik bir ad alanına sahip BLOB depolama hesaplarında tetiklenen olaylar için görüntülenir. |
| yana | string | Belirli bir blob adı için olayların mantıksal dizisini temsil eden donuk bir dize değeri.  Kullanıcılar, aynı blob adında iki olayın göreli sırasını anlamak için standart dize karşılaştırması kullanabilir. |
| storageDiagnostics | object | Azure depolama hizmeti tarafından zaman içinde Tanılama verileri dahildir. Varsa, olay tüketicileri tarafından yok sayılacak. |

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).
* BLOB depolama olayları ile çalışmaya giriş için bkz. [route BLOB depolama olayları-Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
