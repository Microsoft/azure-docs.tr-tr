---
title: Azure Veri Gezgini Python kitaplığını kullanarak verileri alma
description: Bu makalede, Python kullanarak verileri Azure Veri Gezgini 'a alma (yükleme) hakkında bilgi edineceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f109f2dd45fe90884d3947b244b3dafffd547725
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68355924"
---
# <a name="ingest-data-using-the-azure-data-explorer-python-library"></a>Azure Veri Gezgini Python kitaplığını kullanarak verileri alma

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. Azure Veri Gezgini Python için iki istemci kitaplığı sağlar: [alma kitaplığı](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) ve [veri kitaplığı](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Bu kitaplıklar verileri bir kümeye almanıza (yüklemenize ve kodunuzdan verileri sorgulamanıza olanak tanır. Bu makalede, ilk olarak bir kümede tablo ve veri eşleme oluşturursunuz. Ardından veri alımını kümenin kuyruğuna ekler ve sonuçları doğrularsınız.

Bu makale bir [Azure Not defteri](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb)olarak da kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

* [Bir küme ve veritabanı](create-cluster-database-portal.md)

* Geliştirme bilgisayarınıza yüklenmiş [Python](https://www.python.org/downloads/)

## <a name="install-the-data-and-ingest-libraries"></a>Veri ve alma kitaplarını yükleme

*azure-kusto-data* ve *azure-kusto-ingest*'i yükleyin.

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>İçeri aktarma deyimlerini ve sabitlerini ekleme

Azure-kusto-Data sınıfından sınıfları içeri aktarın.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
```

Azure Veri Gezgini uygulamanın kimliğini doğrulamak için AAD kiracı kimliğinizi kullanır. Kiracı kimliğinizi bulmak için aşağıdaki URL'yi kullanın ve *YourDomain* yerine kendi etki alanınızı yazın.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Örneğin, etki alanınız *contoso.com* olduğunda URL şöyle olur: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Sonuçları görmek için bu URL'ye tıklayın; ilk satır aşağıdaki gibidir. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Bu örnekte kiracı kimliği `6babcaad-604b-40ac-a9d7-9fd97c0b779f` değeridir. Bu kodu çalıştırmadan önce AAD_TENANT_ID, KUSTO_URI, KUSTO_INGEST_URI ve KUSTO_DATABASE değerlerini ayarlayın.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE = "<DatabaseName>"
```

Şimdi bağlantı dizesini hazırlayın. Bu örnekte kümeye erişmek için cihaz kimlik doğrulaması kullanılır. [AAD uygulama sertifikası](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [AAD uygulama anahtarı](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)ve [AAD Kullanıcı ve parolasını](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)da kullanabilirsiniz.

Sonraki bir adımda hedef tabloyu ve eşlemeyi oluşturursunuz.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>Kaynak dosya bilgilerini ayarlama

Ek sınıfları içeri aktarın ve veri kaynak dosyası için sabitleri ayarlayın. Bu örnekte, Azure Blob Depolama'da barındırılan bir örnek dosya kullanılır. **StormEvents** örnek veri kümesi, [Ulusal Çevre Bilgileri Merkezleri](https://www.ncdc.noaa.gov/stormevents/)'nden gelen hava durumu verilerini içerir.

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + \
    CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-cluster"></a>Kümenizde tablo oluşturma

StormEvents.csv dosyasındaki verilerin şemasıyla eşleşen bir tablo oluşturun. Bu kod çalıştırıldığında, aşağıdakine benzer bir ileti döndürür: *Oturum açmak https://microsoft.com/devicelogin için, sayfayı açmak üzere bir Web tarayıcısı kullanın ve kimlik doğrulaması yapmak için F3W4VWZDM kodunu girin*. Adımları izleyerek oturum açın, sonra da dönüp bir sonraki kod bloğunu çalıştırın. Bağlantı kuran sonraki kod blokları için yeniden oturum açmak gerekir.

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>Veri alımı eşlemesini tanımlama

Gelen CSV verilerini tablo oluştururken kullanılan sütun adları ve veri türleriyle eşler. Bu, kaynak veri alanlarını hedef tablo sütunlarına eşler

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>Veri alımı için bir iletiyi kuyruğa alma

Blob depolamadan verileri çekmek ve bu verileri Azure Veri Gezgini'ne almak için bir iletiyi kuyruğa alın.

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv,
                                           mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
# FILE_SIZE is the raw size of the data in bytes
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')
```

## <a name="query-data-that-was-ingested-into-the-table"></a>Tabloya alınan verileri sorgulama

Kuyruğa eklenen veri alımının, verileri Azure Veri Gezgini'ne alma ve yükleme işlemini zamanlaması için beş ile on dakika arasında bekleyin. Ardından aşağıdaki kodu çalıştırarak StormEvents tablosundaki kayıtların sayısını alın.

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="run-troubleshooting-queries"></a>Sorun giderme sorguları çalıştırma

[https://dataexplorer.azure.com](https://dataexplorer.azure.com) adresinde oturum açın ve kümenize bağlanın. Son dört saatte hiç veri alımı hatası olup olmadığını görmek için veritabanınızda aşağıdaki komutu çalıştırın. Çalıştırmadan önce veritabanı adını değiştirin.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Son dört saatteki tüm veri alım işlemlerinin durumunu görüntülemek için aşağıdaki komutu çalıştırın. Çalıştırmadan önce veritabanı adını değiştirin.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Table == "StormEvents" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer makalelerimizi izlemeyi planlıyorsanız oluşturduğunuz kaynakları saklayın. Aksi takdirde, veritabanınızda aşağıdaki komutu çalıştırarak StormEvents tablosunu temizleyin.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Sonraki adımlar

* [Python kullanarak verileri sorgulama](python-query-data.md)
