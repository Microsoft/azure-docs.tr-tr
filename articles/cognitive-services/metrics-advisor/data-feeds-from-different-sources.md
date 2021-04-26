---
title: Farklı kaynaklardan ölçüm Danışmanı 'na veri akışları ekleme
titleSuffix: Azure Cognitive Services
description: Ölçüm danışmanına farklı veri akışları ekleme
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: 4fd01256d94fbcb18fe8437be00c84e49d98f7d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606156"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Farklı veri kaynaklarından ölçüm Danışmanı 'na veri akışları ekleme

Farklı türlerde veri kaynaklarını ölçüm Danışmanı 'na bağlamaya yönelik ayarları ve gereksinimleri bulmak için bu makaleyi kullanın. Verilerinizi ölçüm Danışmanı ile kullanmaya yönelik temel kavramlar hakkında bilgi edinmek için [verilerinizi](how-tos/onboard-your-data.md) nasıl ekleyeceğinizi okuduğunuzdan emin olun. \

## <a name="supported-authentication-types"></a>Desteklenen kimlik doğrulama türleri

| Kimlik doğrulaması türleri | Description |
| ---------------------|-------------|
|**Temel** | Veri kaynaklarına erişmek için temel parametreleri sağlayabilmeniz gerekir. Örneğin, bir bağlantı dizesi veya anahtar. Veri akışı yöneticileri bu kimlik bilgilerini görüntüleyebiliyor. |
| **AzureManagedIdentity** | Azure kaynakları için [Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md) Azure Active Directory özelliğidir. Azure hizmetleri 'ni Azure AD 'de otomatik olarak yönetilen bir kimlikle sağlar. Kimliği, Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz.|
| **Azuressqlconnectionstring**| AzureSQL Bağlantı dizenizi ölçüm Danışmanı 'nda bir **kimlik bilgisi varlığı** olarak depolayın ve ölçüm verilerini her seferinde doğrudan kullanın. Yalnızca kimlik bilgileri varlığının yöneticileri bu kimlik bilgilerini görüntüleyebilir, ancak yetkili görüntüleyicilerin kimlik bilgileri için ayrıntıları bilmeleri gerekmeden veri akışları oluşturmalarına olanak sağlar. |
| **DataLakeGen2SharedKey**| Data Lake hesabı anahtarınızı, ölçüm Danışmanı 'nda bir **kimlik bilgisi varlığı** olarak depolayın ve ölçüm verilerini her seferinde doğrudan kullanın. Yalnızca kimlik bilgisi varlığının yöneticileri bu kimlik bilgilerini görüntüleyebilir, ancak yetkili görüntüleyicilerin kimlik bilgisi ayrıntılarını bilmeleri gerekmeden veri akışı oluşturmalarına olanak sağlar.|
| **Hizmet sorumlusu**| Hizmet sorumlunuzu ölçüm Danışmanı 'nda bir **kimlik bilgisi varlığı** olarak depolayın ve ölçüm verilerini her seferinde doğrudan kullanın. Yalnızca kimlik bilgisi varlığının yöneticileri kimlik bilgilerini görüntüleyebilir, ancak yetkili görüntüleyicilerin kimlik bilgisi ayrıntılarını bilmeleri gerekmeden veri akışı oluşturmalarına olanak sağlar.|
| **Anahtar kasasından hizmet sorumlusu**|Hizmet sorumlunuzu, ölçüm Danışmanı 'nda **kimlik bilgisi varlığı** olarak bir anahtar kasasında depolayın ve ölçüm verilerini her seferinde doğrudan kullanın. Yalnızca bir **kimlik bilgisi varlığının** yöneticileri kimlik bilgilerini görüntüleyebilir, ancak aynı zamanda görüntüleyicilerin ayrıntılı kimlik bilgilerini bilmesi gerekmeden veri akışı oluşturabildiklerini de bırakabilir. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Desteklenen veri kaynakları ve ilgili kimlik doğrulama türleri


| Veri kaynakları | Kimlik doğrulama türleri |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Temel |
|[**Azure Blob depolama (JSON)**](#blob) | Temel<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Temel |
|[**Azure Veri Gezgini (Kusto)**](#kusto) | Temel<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Temel<br>DataLakeGen2SharedKey<br>Hizmet sorumlusu<br>Anahtar kasasından hizmet sorumlusu<br> |
|[**Azure SQL veritabanı/SQL Server**](#sql) | Temel<br>ManagedIdentity<br>Hizmet sorumlusu<br>Anahtar kasasından hizmet sorumlusu<br>Azuressqlconnectionstring
|[**Azure Tablo depolama**](#table) | Temel | 
|[**ElasticSearch**](#es) | Temel |
|[**Http isteği**](#http) | Temel | 
|[**Etkileyen (etkileyen XQL)**](#influxdb) | Temel |
|[**MongoDB**](#mongodb) | Temel |
|[**MySQL**](#mysql) | Temel |
|[**PostgreSQL**](#pgsql)| Temel|

Bir kimlik bilgisi varlığı * * oluşturun ve veri kaynaklarınızda kimlik doğrulaması yapmak için kullanın. Aşağıdaki bölümler, *temel* kimlik doğrulaması için gereken parametreleri belirtir. 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **Uygulama kimliği**: Application Insights API 'si kullanılırken bu uygulamayı tanımlamak için kullanılır. Uygulama KIMLIĞINI almak için aşağıdakileri yapın:

    1. Application Insights kaynağınız, API erişimi ' ne tıklayın.

    2. Ölçüm Danışmanı 'nda **uygulama kimliği** alanında oluşturulan uygulama kimliğini kopyalayın. 
    
    Daha fazla bilgi için bkz. [Azure bot hizmeti belgeleri](/azure/bot-service/bot-service-resources-app-insights-keys#application-id) .

* **API anahtarı**: API anahtarları, bu kaynağa erişmek için tarayıcı dışındaki uygulamalar tarafından kullanılır. API anahtarını almak için aşağıdakileri yapın:

    1. Application Insights kaynağında API erişimi ' ne tıklayın.

    2. API anahtarı oluştur ' a tıklayın.

    3. Kısa bir açıklama girin, Telemetriyi Oku seçeneğini işaretleyin ve anahtar oluştur düğmesine tıklayın.

    4. API anahtarını ölçüm Danışmanı 'ndaki **API anahtarı** alanına kopyalayın.

* **Sorgu**: Azure Application Insights günlükleri Azure Veri Gezgini kurulmuştur ve Azure izleyici günlük sorguları aynı kusto sorgu dilinin bir sürümünü kullanır. [Kusto sorgu dili belgeleri](/azure/data-explorer/kusto/query/) , dilin tüm ayrıntılarına sahiptir ve Application Insights bir sorgu yazmak için birincil kaynağınız olmalıdır. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob depolama (JSON)</span>

* **Bağlantı dizesi**: Bu dizeyi alma hakkında bilgi için bkz. Azure Blob depolama [bağlantı dizesi](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) makalesi.

* **Kapsayıcı**: ölçüm Danışmanı, tek bir kapsayıcı altında blob dosyaları (zaman damgası başına bir BLOB) olarak depolanan zaman serisi verilerinin olmasını bekler. Bu, kapsayıcı adı alanıdır.

* **BLOB şablonu**: Bu, blob dosya adlarının şablonudur. Örneğin: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. Aşağıdaki parametreler desteklenir:
  * `%Y` yıl olarak biçimlendirilir `yyyy`
  * `%m` ay olarak biçimlendirilir `MM`
  * `%d` gün olarak biçimlendirilir `dd`
  * `%h` saat olarak biçimlendirilir `HH`
  * `%M` dakika olarak biçimlendirilir `mm`

* **JSON biçimi sürümü**: JSON dosyalarındaki veri şemasını tanımlar. Şu anda ölçüm Danışmanı iki sürümü desteklemektedir:
  
  * v1 (varsayılan değer)

      Yalnızca ölçüm *adı* ve *değeri* kabul edilir. Örnek:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      Ölçüm *boyutları* ve *zaman damgası* da kabul edilir. Örnek:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

JSON dosyası başına yalnızca bir zaman damgasına izin verilir. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Bağlantı dizesi**: Azure Cosmos DB erişmek için bağlantı dizesi. Bu, **anahtarlar** içinde Cosmos DB kaynağında bulunabilir. 
* **Veritabanı**: Sorgulanacak veritabanı. Bu, **kapsayıcılar** bölümünde sayfa **tarama** sayfasında bulunabilir.
* **Koleksıyon kimliği**: sorgu yapılacak koleksiyon kimliği. Bu, **kapsayıcılar** bölümünde sayfa **tarama** sayfasında bulunabilir.
* **SQL sorgusu**: çok boyutlu zaman serisi verilerine veri almak ve bunları formüle eklemek IÇIN bir SQL sorgusu. `@StartTime` `@EndTime` Sorgunuzdaki ve değişkenlerini kullanabilirsiniz. Bunların biçimlendirilmesi gerekir: `yyyy-MM-dd HH:mm:ss` .

    Örnek sorgu:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    2019/12/12 'den bir veri dilimi için örnek sorgu:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Veri Gezgini (Kusto)</span>

* **Bağlantı dizesi**: ölçüm Danışmanı, Azure AD uygulama kimlik doğrulamasını kullanarak Azure Veri Gezgini (kusto) erişimi destekler. Bir Azure AD uygulaması oluşturup kaydetmeniz ve Azure Veri Gezgini veritabanına erişmek için bu uygulamayı yetkilendirmeniz gerekecektir. Bağlantı dizenizi almak için bkz. [Azure Veri Gezgini](/azure/data-explorer/provision-azure-ad-app) belgeleri.

* **Sorgu**: çok boyutlu zaman serisi verilerine veri almak ve bunları düzenlemek için bkz. [kusto Query Language](/azure/data-explorer/kusto/query) . `@StartTime` `@EndTime` Sorgunuzdaki ve değişkenlerini kullanabilirsiniz. Bunların biçimlendirilmesi gerekir: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage 2. Nesil</span>

* **Hesap adı**: Azure Data Lake Storage 2. hesap adı. Bu, **erişim anahtarlarındaki** Azure Storage hesabınızın (Azure Data Lake Storage 2.) kaynağında bulunabilir.

* **Hesap anahtarı**: lütfen Azure Data Lake Storage 2. erişmek için hesap adını belirtin. Bu, **erişim tuşları** ayarında Azure Storage hesabı (Azure Data Lake Storage 2.) kaynağında bulunabilir.

* **Dosya sistemi adı (kapsayıcı)**: ölçüm Danışmanı, tek bir kapsayıcı altında bir blob dosyası (zaman damgası başına bir BLOB) olarak depolanan zaman serisi verilerinizi bekler. Bu, kapsayıcı adı alanıdır. Bu, Azure depolama hesabınızın (Azure Data Lake Storage 2.) örneğinde bulunabilir ve ' blob hizmeti ' bölümünde ' kapsayıcılar ' öğesine tıklayabilirsiniz.

* **Dizin şablonu**: Bu, blob dosyasının dizin şablonudur. Örneğin: */%Y/%m/%d*. Aşağıdaki parametreler desteklenir:
  * `%Y` yıl olarak biçimlendirilir `yyyy`
  * `%m` ay olarak biçimlendirilir `MM`
  * `%d` gün olarak biçimlendirilir `dd`
  * `%h` saat olarak biçimlendirilir `HH`
  * `%M` dakika olarak biçimlendirilir `mm`

* **Dosya şablonu**: Bu, blob dosyasının dosya şablonudur. Örneğin: *X_% Y-% m-% d-% h-% M.json*. Aşağıdaki parametreler desteklenir:
  * `%Y` yıl olarak biçimlendirilir `yyyy`
  * `%m` ay olarak biçimlendirilir `MM`
  * `%d` gün olarak biçimlendirilir `dd`
  * `%h` saat olarak biçimlendirilir `HH`
  * `%M` dakika olarak biçimlendirilir `mm`

Şu anda ölçüm Danışmanı JSON dosyalarındaki veri şemasını aşağıda gösterildiği gibi destekler. Örnek:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL veritabanı | SQL Server</span>

* **Bağlantı dizesi**: ölçüm Danışmanı SQL Server veri kaynağı için bir [ADO.net stili bağlantı dizesi](/dotnet/framework/data/adonet/connection-string-syntax) kabul eder.

    Örnek bağlantı dizesi:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Sorgu**: çok boyutlu zaman serisi verilerine veri almak ve oluşturmak IÇIN bir SQL sorgusu. Sorgudaki bir değişkeni, `@StartTime` beklenen ölçüm değerini almaya yardımcı olmak için kullanabilirsiniz.

  * `@StartTime`: biçiminde bir tarih saat `yyyy-MM-dd HH:mm:ss`

    Örnek sorgu:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    2019/12/12 veri dilimi için yürütülen gerçek sorgu:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Tablo depolama</span>

* **Bağlantı dizesi**: lütfen bir SAS (paylaşılan erişim imzası) URL 'si oluşturun ve buraya girin. SAS URL 'SI oluşturmanın en kolay yolu Azure portalını kullanmaktır. Azure portal kullanarak grafik sayfasına gidebilirsiniz. Azure portal aracılığıyla bir SAS URL 'SI oluşturmak için, önce ayarlar bölümü altında erişmek istediğiniz depolama hesabına gidin ve paylaşılan erişim imzası ' na tıklayın. En az "Tablo" ve "nesne" onay kutularını işaretleyin ve ardından SAS ve bağlantı dizesi oluştur düğmesine tıklayın. Tablo hizmeti SAS URL 'SI, ölçüm Danışmanı çalışma alanındaki metin kutusunu kopyalamanız ve doldurmanız gereken şeydir.

* **Tablo adı**: sorgulanacak bir tablo belirtin. Bu, Azure depolama hesabı Örneğinizde bulunabilir. **Tablo hizmeti** bölümünde **Tablolar** ' a tıklayın.

* **Sorgu** Sorgunuzda ' i kullanabilirsiniz `@StartTime` . `@StartTime` betikte bir yyyy-MM-ddTHH: mm: ss biçim dizesiyle değiştirilmiştir. İpucu: belirli bir zaman aralığına sahip bir sorgu oluşturmak için Azure Depolama Gezgini 'ni kullanın ve çalışır durumda olduğundan emin olun ve ardından değişikliği yapın.

    ``` mssql
    date ge datetime'@StartTime' and date lt datetime'@EndTime'
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Ana bilgisayar**: elaun Search kümesinin ana konağını belirtin.
* **Bağlantı noktası**: elaun Search kümesinin ana bağlantı noktasını belirtin.
* **Yetkilendirme üst bilgisi**: elaun Search kümesinin yetkilendirme üst bilgi değerini belirtin.
* **Sorgu**: verileri almak için sorguyu belirtin. Yer tutucu @StartTime destekleniyor. ( örn. 2020-06-21T00:00:00Z verileri tamamlandığında, @StartTime = 2020-06-21T00:00:00)

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP isteği</span>

* **İstek URL 'si**: JSON DÖNDÜRESAĞLAYAN bir http url 'si. % Y,% m,% d,% h,% M yer tutucuları desteklenir:% Y = yıl, biçim yyyy,% m = ay,% d = gün,% m = şu biçimde Örneğin: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **Istek HTTP yöntemi**: get veya post kullanın.
* **İstek üst bilgisi**: temel kimlik doğrulaması eklenemiyor. 
* **İstek yükü**: yalnızca JSON yükü desteklenir. Yer tutucu @StartTime , yükte desteklenir. Yanıt şu JSON biçiminde olmalıdır: [{"timestamp": "2018-01-01T00:00:00Z", "Pazar": "en-US", "say": 11, "gelir": 1,23}, {"timestamp": "2018-01-01T00:00:00Z", "Pazar": "zh-CN", "Count": 22, "gelir": 4.56}]. (örneğin, 2020-06-21T00:00:00Z verileri yapıldığında @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">Etkileyen (etkileyen XQL)</span>

* **Bağlantı dizesi**: etkileyen bir bağlantı dizesi.
* **Veritabanı**: Sorgulanacak veritabanı.
* **Sorgu**: alma işlemi için verileri çok boyutlu zaman serisi verilerine almak ve düzenlemek için bir sorgu.
* **Kullanıcı adı**: kimlik doğrulaması için bu isteğe bağlıdır. 
* **Parola**: kimlik doğrulaması için bu isteğe bağlıdır. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Bağlantı dizesi**: MongoDB 'nize erişmek için bağlantı dizesi.
* **Veritabanı**: Sorgulanacak veritabanı.
* **Komut**: alma işlemi için verileri çok boyutlu zaman serisi verilerine almak ve düzenlemek için bir komut.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Bağlantı dizesi**: MySQL db 'nize erişmek için bağlantı dizesi.
* **Sorgu**: alma işlemi için verileri çok boyutlu zaman serisi verilerine almak ve düzenlemek için bir sorgu.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Bağlantı dizesi**: PostgreSQL DB 'nize erişmek için bağlantı dizesi.
* **Sorgu**: alma işlemi için verileri çok boyutlu zaman serisi verilerine almak ve düzenlemek için bir sorgu.

## <a name="next-steps"></a>Sonraki adımlar

* Ölçüm verilerinizin sisteme alınması beklenirken, [veri akışı yapılandırmalarının nasıl yönetileceği](how-tos/manage-data-feeds.md)hakkında bilgi edinin.
* Ölçüm verileriniz istendiğinde, [ölçümleri yapılandırabilir ve yapılandırmayı](how-tos/configure-metrics.md)tespit etmek için ince ayar yapabilirsiniz.
