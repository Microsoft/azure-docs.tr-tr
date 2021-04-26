---
title: Azure Blob depolama izleme verileri başvurusu | Microsoft Docs
description: Azure Blob depolamadaki verileri izlemek için günlük ve ölçüm başvurusu.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: b37586f66106e33b2a8dad034a6e7c131484be73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571620"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Azure Blob depolama izleme verileri başvurusu

Azure depolama için izleme verilerini toplama ve çözümleme hakkında ayrıntılı bilgi için bkz. [Azure Storage 'ı izleme](monitor-blob-storage.md) .

## <a name="metrics"></a>Ölçümler

Aşağıdaki tablolarda, Azure depolama için toplanan platform ölçümleri listelenmektedir. 

### <a name="capacity-metrics"></a>Kapasite ölçümleri

Kapasite ölçümleri değerleri günlük olarak (24 saate kadar) yenilenir. Zaman dilimi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm kapasite ölçümleri için desteklenen zaman dilimi bir saattir (PT1H).

Azure depolama, Azure Izleyici 'de aşağıdaki kapasite ölçümlerini sağlar.

#### <a name="account-level"></a>Hesap düzeyi

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Blob depolama

Bu tabloda [BLOB depolama ölçümleri](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices)gösterilmektedir.

| Metric | Açıklama |
| ------------------- | ----------------- |
| BlobCapacity | Depolama hesabında kullanılan BLOB depolama alanı toplamı. <br/><br/> Birim: bayt <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 <br/> Boyutlar: **Blobtype** ve **blobtier** ([tanım](#metrics-dimensions)) |
| BlobCount    | Depolama hesabında depolanan BLOB nesnelerinin sayısı. <br/><br/> Birim: sayım <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 <br/> Boyutlar: **Blobtype** ve **blobtier** ([tanım](#metrics-dimensions)) |
| BlobProvisionedSize | Depolama hesabında sağlanan depolama miktarı. Bu ölçüm yalnızca Premium Depolama hesapları için geçerlidir. <br/><br/> Birim: bayt <br/> Toplama türü: Ortalama |
| ContainerCount    | Depolama hesabındaki kapsayıcıların sayısı. <br/><br/> Birim: sayım <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 |
| Dizin kapasitesi     | ADLS 2. hiyerarşik dizin tarafından kullanılan depolama miktarı <br/><br/> Birim: bayt <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 |

### <a name="transaction-metrics"></a>İşlem ölçümleri

İşlem ölçümleri, Azure depolama 'daki bir depolama hesabına yönelik her istek için Azure Izleyici 'ye dağıtılır. Depolama hesabınızda hiçbir etkinlik olmaması durumunda, dönem içinde işlem ölçümlerinde hiç veri olmayacaktır. Tüm işlem ölçümleri hem hesapta hem de BLOB depolama hizmeti düzeyinde kullanılabilir. Zaman dilimi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm işlem ölçümleri için desteklenen saat graıns PT1H ve PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Ölçüm boyutları

Azure depolama, Azure Izleyici 'de ölçümler için aşağıdaki boyutları destekler.

### <a name="dimensions-available-to-all-storage-services"></a>Tüm depolama hizmetleri için kullanılabilen Boyutlar

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Blob depolamaya özgü Boyutlar

| Boyut adı | Description |
| ------------------- | ----------------- |
| **BlobType** | Yalnızca blob ölçümleri için blob türü. Desteklenen değerler **Blockblob**, **pageblob** ve **Azure Data Lake Storage**. Ekleme Blobları **Blockblob**'a dahildir. |
| **BlobTier** | Azure depolama, blob nesne verilerini en düşük maliyetli biçimde depolamanıza olanak sağlayan farklı erişim katmanları sunar. Bkz. [Azure Storage blob katmanında](../blobs/storage-blob-storage-tiers.md)daha fazla bilgi. Desteklenen değerler şunlardır: <br/> <li>**Sık** erişimli: etkin katman</li> <li>**Cool**: Cool Tier</li> <li>**Arşiv**: Arşiv katmanı</li> <li>**Premium**: Blok Blobu için Premium katman</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Premium Sayfa Blobu için katman türleri</li> <li>**Standart**: standart sayfa blobu için katman türü</li> <li>**Katmanlanmadan**: genel amaçlı v1 depolama hesabı için katman türü</li> |

Boyutları destekleyen ölçümler için, karşılık gelen ölçüm değerlerini görmek için boyut değerini belirtmeniz gerekir. Örneğin, başarılı yanıtlar için  **işlem** değerine bakarsanız, **responseType** boyutunu **başarılı** olarak filtrelemeniz gerekir. Blok Blobu için **Blobcount** değerine bakarsanız, **Blobtype** boyutunu **blockblob** ile filtrelemeniz gerekir.

## <a name="resource-logs-preview"></a>Kaynak günlükleri (Önizleme)

> [!NOTE]
> Azure Izleyici 'de Azure depolama günlükleri genel önizlemede bulunur ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, genel amaçlı v1 ve genel amaçlı v2 depolama hesaplarında blob 'lar (Azure Data Lake Storage 2.), dosyalar, kuyruklar, tablolar, Premium Depolama hesapları için Günlükler sunar. Klasik depolama hesapları desteklenmez.

Aşağıdaki tabloda Azure Izleyici günlüklerinde veya Azure Storage 'da toplandıklarında Azure depolama kaynak günlüklerinin özellikleri listelenmektedir. Özellikler işlemi, hizmeti ve işlemi gerçekleştirmek için kullanılan yetkilendirme türünü anlatmaktadır.

### <a name="fields-that-describe-the-operation"></a>İşlemi tanımlayan alanlar

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>İşlemin nasıl doğrulandığını tanımlayan alanlar

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Hizmeti tanımlayan alanlar

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Ayrıca bkz.

- Bkz. Azure Storage izleme açıklaması için bkz. [Azure Storage](monitor-blob-storage.md) 'ı izleme.
- Azure kaynaklarını izleme hakkında ayrıntılı bilgi için bkz. Azure [izleyici ile Azure kaynaklarını izleme](../../azure-monitor/essentials/monitor-azure-resource.md) .
