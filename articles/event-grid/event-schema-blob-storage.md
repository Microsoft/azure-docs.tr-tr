---
title: Event Grid kaynak olarak Azure Blob depolama
description: Azure Event Grid ile BLOB depolama olayları için sunulan özellikleri açıklar
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 893e86ecf220ceb327eed9c6f95be4c7ed1afb1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363653"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Event Grid kaynağı olarak Azure Blob depolama

Bu makalede, BLOB depolama olaylarına yönelik özellikler ve şema sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](event-schema.md). Ayrıca, Azure Blob depolamayı bir olay kaynağı olarak kullanmak için hızlı başlayan ve öğreticilerin bir listesini sağlar.


>[!NOTE]
> Yalnızca **StorageV2 (genel amaçlı v2)**, **blok Blobstorage** ve **blobstorage** türündeki depolama hesapları olay tümleştirmesini destekler. **Depolama (genel amaçlı v1)** Event Grid ile *tümleştirmeyi desteklemez.*

## <a name="available-event-types"></a>Kullanılabilir olay türleri

### <a name="list-of-events-for-blob-rest-apis"></a>Blob REST API 'Lerinin olay listesi

Bu olaylar, bir istemci blob REST API 'Lerini çağırarak bir blobu oluşturduğunda, değiştirdiği veya sildiği zaman tetiklenir.

> [!NOTE]
> `$logs`Ve `$blobchangefeed` kapsayıcıları Event Grid ile tümleştirmemişse, bu kapsayıcılardaki etkinlik olay oluşturmaz. Ayrıca, *`(abfss://URI) `* hiyerarşik olmayan ad alanı etkin hesaplar için DFS uç noktası kullanılması olay oluşturmaz, ancak blob uç noktası *`(wasb:// URI)`* olaylar oluşturur.

 |Olay adı |Description|
 |----------|-----------|
 |**Microsoft. Storage. Bloboluşturuldu** |Bir blob oluşturulduğunda veya değiştirildiğinde tetiklenir. <br>Özellikle, bu olay, istemci `PutBlob` `PutBlockList` `CopyBlob` BLOB REST API mevcut olan, veya işlemlerini kullandıklarında tetiklenir.   |
 |**Microsoft. Storage. BlobDeleted** |Blob silindiğinde tetiklenir. <br>Özellikle, bu olay, istemci `DeleteBlob` Blob REST API kullanılabilir olan işlemi çağırdığında tetiklenir. |

> [!NOTE]
> **Azure Blob depolama** Için, **Microsoft. Storage. blobcreated** olayının yalnızca bir Blok Blobu tamamen yürütüldüğü zaman tetiklendiğinden emin olmak istiyorsanız,, `CopyBlob` `PutBlob` ve REST API çağrıları için olayı filtreleyin `PutBlockList` . Bu API çağrıları, **Microsoft. Storage. BlobCreated** olayını yalnızca veriler bir blok blobuna tam olarak kaydedildikten sonra tetikler. Filtre oluşturmayı öğrenmek için bkz. [Event Grid olayları filtreleme](./how-to-filter-events.md).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage Gen 2 REST API 'Lerinin olayları listesi

Bu olaylar, depolama hesabında hiyerarşik bir ad alanı etkinleştirirseniz ve istemciler Azure Data Lake Storage 2. REST API 'Leri kullanıyorsa tetiklenir. Azure Data Lake Storage 2. hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2. giriş](../storage/blobs/data-lake-storage-introduction.md).

|Olay adı|Description|
|----------|-----------|
|**Microsoft. Storage. Bloboluşturuldu** | Bir blob oluşturulduğunda veya değiştirildiğinde tetiklenir. <br>Özellikle, istemciler `CreateFile` `FlushWithClose` Azure Data Lake Storage 2. REST API kullanılabilir olan ve işlemlerini kullandıklarında bu olay tetiklenir. |
|**Microsoft. Storage. BlobDeleted** |Blob silindiğinde tetiklenir. <br>Özellikle, bu olay ayrıca istemciler `DeleteFile` Azure Data Lake Storage 2. REST API kullanılabilir olan işlemi çağırdığında tetiklenir. |
|**Microsoft. Storage. Blobyeniden adlandırıldı**|Bir blob yeniden adlandırıldığında tetiklenir. <br>Özellikle, istemciler `RenameFile` Azure Data Lake Storage 2. REST API kullanılabilir olan işlemi kullandıklarında bu olay tetiklenir.|
|**Microsoft. Storage. DirectoryCreated**|Bir dizin oluşturulduğunda tetiklenir. <br>Özellikle, istemciler `CreateDirectory` Azure Data Lake Storage 2. REST API kullanılabilir olan işlemi kullandıklarında bu olay tetiklenir.|
|**Microsoft. Storage. Directoryyeniden adlandırıldı**|Bir dizin yeniden adlandırıldığında tetiklenir. <br>Özellikle, istemciler `RenameDirectory` Azure Data Lake Storage 2. REST API kullanılabilir olan işlemi kullandıklarında bu olay tetiklenir.|
|**Microsoft. Storage. DirectoryDeleted**|Bir dizin silindiğinde tetiklenir. <br>Özellikle, istemciler `DeleteDirectory` Azure Data Lake Storage 2. REST API kullanılabilir olan işlemi kullandıklarında bu olay tetiklenir.|

> [!NOTE]
> **Azure Data Lake Storage 2.** Için, **Microsoft. Storage. blobcreated** olayının yalnızca bir Blok Blobu tamamen yürütüldüğü zaman tetiklendiğinden emin olmak istiyorsanız, `FlushWithClose` REST API çağrısının olayını filtreleyin. Bu API çağrısı, **Microsoft. Storage. BlobCreated** olayını yalnızca veriler bir blok blobuna tam olarak kaydedildikten sonra tetikler. Filtre oluşturmayı öğrenmek için bkz. [Event Grid olayları filtreleme](./how-to-filter-events.md).

## <a name="example-event"></a>Örnek olay
Bir olay tetiklendiğinde, Event Grid hizmeti bu olayla ilgili verileri bu olay ile abone olacak şekilde gönderir. Bu bölüm, her BLOB depolama etkinliği için verilerin nasıl görüneceğine ilişkin bir örnek içerir.

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

BLOB depolama hesabının hiyerarşik bir ad alanı varsa, veriler önceki örneğe benzer ve bu değişiklikler özel durumuyla benzerdir:

* `dataVersion`Anahtar değerine ayarlanır `2` .

* `data.api`Anahtar dize veya olarak ayarlanır `CreateFile` `FlushWithClose` .

* `contentOffset`Anahtar, veri kümesine dahil edilir.

> [!NOTE]
> Uygulamalar `PutBlockList` hesaba yeni bir blob yükleme işlemini kullanıyorsa, veriler bu değişiklikleri içermez.

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

BLOB depolama hesabının hiyerarşik bir ad alanı varsa, veriler önceki örneğe benzer ve bu değişiklikler özel durumuyla benzerdir:

* `dataVersion`Anahtar değerine ayarlanır `2` .

* `data.api`Anahtar dizeye ayarlanır `DeleteFile` .

* `url`Anahtar yolu içerir `dfs.core.windows.net` .

> [!NOTE]
> Uygulamalar `DeleteBlob` hesaptan bir blobu silmek için işlemi kullanıyorsa, veriler bu değişiklikleri içermez.

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

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft. Storage. BlobCreated olayı

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobCreated olayı (Data Lake Storage 2.)

BLOB depolama hesabının hiyerarşik bir ad alanı varsa, veriler önceki örneğe benzer ve bu değişiklikler özel durumuyla benzerdir:

* `data.api`Anahtar dize veya olarak ayarlanır `CreateFile` `FlushWithClose` .
* `contentOffset`Anahtar, veri kümesine dahil edilir.

> [!NOTE]
> Uygulamalar `PutBlockList` hesaba yeni bir blob yükleme işlemini kullanıyorsa, veriler bu değişiklikleri içermez.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft. Storage. BlobDeleted olayı

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-11-07T20:09:22.5674003Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobDeleted olayı (Data Lake Storage 2.)

BLOB depolama hesabının hiyerarşik bir ad alanı varsa, veriler önceki örneğe benzer ve bu değişiklikler özel durumuyla benzerdir:


* `data.api`Anahtar dizeye ayarlanır `DeleteFile` .
* `url`Anahtar yolu içerir `dfs.core.windows.net` .

> [!NOTE]
> Uygulamalar `DeleteBlob` hesaptan bir blobu silmek için işlemi kullanıyorsa, veriler bu değişiklikleri içermez.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft. Storage. Blobyeniden adlandırılmış olay

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "type": "Microsoft.Storage.BlobRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft. Storage. DirectoryCreated olayı

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "type": "Microsoft.Storage.DirectoryCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft. Storage. Directoryyeniden adlandırılan olay

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "type": "Microsoft.Storage.DirectoryRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft. Storage. DirectoryDeleted olayı

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "type": "Microsoft.Storage.DirectoryDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

---


## <a name="event-properties"></a>Olay özellikleri

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `topic` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `eventType` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `eventTime` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | BLOB depolama olay verileri. |
| `dataVersion` | string | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| `metadataVersion` | string | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `source` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `type` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `time` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | BLOB depolama olay verileri. |
| `specversion` | string | CloudEvents şema belirtimi sürümü. |

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `api` | dize | Olayı tetikleyen işlem. |
| `clientRequestId` | string | depolama API 'SI işlemi için istemci tarafından sağlanmış bir istek KIMLIĞI. Bu KIMLIK, günlüklerdeki "istemci-istek-kimliği" alanı kullanılarak Azure depolama tanılama günlükleri ile ilişkilendirmek için kullanılabilir ve "x-MS-Client-Request-ID" üst bilgisi kullanılarak istemci isteklerinde sağlanabilirler. [Günlük biçimine](/rest/api/storageservices/storage-analytics-log-format)bakın. |
| `requestId` | string | Depolama API 'SI işlemi için hizmet tarafından oluşturulan istek KIMLIĞI. , Günlüklerdeki "istek-kimliği-üst bilgi" alanı kullanılarak Azure depolama tanılama günlükleri ile ilişkilendirmek için kullanılabilir ve ' x-MS-Request-id ' üst bilgisinde API çağrısını başlatma işleminden döndürülür. [Günlük biçimine](/rest/api/storageservices/storage-analytics-log-format)bakın. |
| `eTag` | string | İşlemleri koşullu olarak çalıştırmak için kullanabileceğiniz değer. |
| `contentType` | string | Blob için belirtilen içerik türü. |
| `contentLength` | tamsayı | Blobun bayt cinsinden boyutu. |
| `blobType` | string | Blob türü. Geçerli değerler "BlockBlob" ya da "PageBlob". |
| `contentOffset` | sayı | Olay tetikleme uygulamasının dosyaya yazmayı tamamladığı noktada gerçekleştirilen bir yazma işleminin bayt cinsinden değeri. <br>Yalnızca hiyerarşik bir ad alanına sahip BLOB depolama hesaplarında tetiklenen olaylar için görüntülenir.|
| `destinationUrl` |string | İşlem tamamlandıktan sonra var olacak dosyanın URL 'si. Örneğin, bir dosya yeniden adlandırılırsa, `destinationUrl` özelliği yeni dosya adının URL 'sini içerir. <br>Yalnızca hiyerarşik bir ad alanına sahip BLOB depolama hesaplarında tetiklenen olaylar için görüntülenir.|
| `sourceUrl` |string | İşlem tamamlanmadan önce var olan dosyanın URL 'si. Örneğin, bir dosya yeniden adlandırılırsa, `sourceUrl` yeniden adlandırma işleminden önce özgün dosya adının URL 'sini içerir. <br>Yalnızca hiyerarşik bir ad alanına sahip BLOB depolama hesaplarında tetiklenen olaylar için görüntülenir. |
| `url` | string | Blobun yolu. <br>İstemci bir blob REST API kullanıyorsa, URL bu yapıya sahiptir: `<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>` . <br>İstemci bir Data Lake Storage REST API kullanıyorsa, URL bu yapıya sahiptir: `<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>` . |
| `recursive` | string | `True` işlemi tüm alt dizinlerde çalıştırmak için; Aksi takdirde `False` . <br>Yalnızca hiyerarşik bir ad alanına sahip BLOB depolama hesaplarında tetiklenen olaylar için görüntülenir. |
| `sequencer` | string | Belirli bir blob adı için olayların mantıksal dizisini temsil eden donuk bir dize değeri.  Kullanıcılar, aynı blob adında iki olayın göreli sırasını anlamak için standart dize karşılaştırması kullanabilir. |
| `storageDiagnostics` | object | Azure depolama hizmeti tarafından zaman içinde Tanılama verileri dahildir. Varsa, olay tüketicileri tarafından yok sayılacak. |

## <a name="tutorials-and-how-tos"></a>Öğreticiler ve nasıl yapılır kılavuzları
|Başlık  |Açıklama  |
|---------|---------|
| [Hızlı başlangıç: Blob Depolama olaylarını Azure CLı ile özel bir Web uç noktasına yönlendirme](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Blob Depolama olaylarını bir Web kancasına göndermek için Azure CLı 'nın nasıl kullanılacağını gösterir. |
| [Hızlı başlangıç: Blob Depolama olaylarını PowerShell ile özel bir Web uç noktasına yönlendirme](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Blob Depolama olaylarını bir Web kancasına göndermek için Azure PowerShell nasıl kullanacağınızı gösterir. |
| [Hızlı başlangıç: Azure portal Blob Depolama olaylarını oluşturun ve yönlendirin](blob-event-quickstart-portal.md) | Portal 'ın Blob Depolama olaylarını bir Web kancasına göndermek için nasıl kullanılacağını gösterir. |
| [Azure CLı: bir BLOB depolama hesabı için olaylara abone olma](./scripts/event-grid-cli-blob.md) | BLOB depolama hesabı için olaya abone olan örnek komut dosyası. Olayı bir Web kancasına gönderir. |
| [PowerShell: bir BLOB depolama hesabı için olaylara abone olma](./scripts/event-grid-powershell-blob.md) | BLOB depolama hesabı için olaya abone olan örnek komut dosyası. Olayı bir Web kancasına gönderir. |
| [Kaynak Yöneticisi şablonu: BLOB depolama ve abonelik oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Bir Azure Blob depolama hesabı dağıtır ve o depolama hesabı için olaylara abone olur. Olayları bir Web kancasına gönderir. |
| [Genel Bakış: BLOB depolama olaylarına yeniden davranıyor](../storage/blobs/storage-blob-event-overview.md) | Blob depolamayı Event Grid tümleştirmeyle ilgili genel bakış. |

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).
* BLOB depolama olayları ile çalışmaya giriş için bkz. [route BLOB depolama olayları-Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
