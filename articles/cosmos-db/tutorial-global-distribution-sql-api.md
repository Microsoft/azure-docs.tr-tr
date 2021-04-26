---
title: "Öğretici: SQL API 'SI için Azure Cosmos DB küresel dağıtım öğreticisi"
description: "Öğretici: .NET, Java, Python ve çeşitli diğer SDK 'lar ile SQL API 'sini kullanarak Azure Cosmos DB genel dağıtımı ayarlamayı öğrenin"
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: aacb8d4ffb98d553b17aa52e4c4b11a4837dc1c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337906"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Öğretici: SQL API 'sini kullanarak genel dağıtım Azure Cosmos DB ayarlama
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Azure Cosmos DB genel dağıtımı ayarlamak ve sonra SQL API 'sini kullanarak bağlanmak için Azure portal nasıl kullanacağınızı göstereceğiz.

Bu makale aşağıdaki görevleri kapsar: 

> [!div class="checklist"]
> * Azure portalını kullanarak genel dağıtımı yapılandırma
> * [SQL API 'lerini](./introduction.md) kullanarak genel dağıtımı yapılandırma

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> SQL API 'sini kullanarak tercih edilen bir bölgeye bağlanma

[Genel dağıtımdan](distribute-data-globally.md) yararlanmak için istemci uygulamaları, belge işlemlerini gerçekleştirmek için kullanılacak bölgelerin tercihe göre sıralanmış listesini belirtebilir. Azure Cosmos DB hesap yapılandırmasına, geçerli bölgesel kullanılabilirliğe ve belirtilen tercih listesine göre yazma ve okuma işlemlerini gerçekleştirmek için SQL SDK tarafından en iyi uç nokta seçilir.

SQL SDK’ları kullanılarak bağlantı başlatırken bu tercih listesi belirtilir. SDK 'lar, `PreferredLocations` Azure bölgelerinin sıralı listesi olan isteğe bağlı bir parametreyi kabul eder.

SDK, tüm yazma işlemlerini otomatik olarak geçerli yazma bölgesine gönderir. Tüm okumalar, tercih edilen konumlar listesindeki ilk kullanılabilir bölgeye gönderilir. İstek başarısız olursa, istemci listeyi sonraki bölgeye devreder.

SDK yalnızca tercih edilen konumlarda belirtilen bölgelerden okumaya çalışır. Bu nedenle, örneğin, Azure Cosmos hesabı dört bölgede kullanılabiliyorsa ancak istemci içinde yalnızca iki okuma (yazma olmayan) bölgesi belirtiyorsa, `PreferredLocations` içinde belirtilmeyen okuma bölgesinden okuma yapılmaz `PreferredLocations` . Listede belirtilen okuma bölgeleri `PreferredLocations` kullanılamıyorsa, okuma yazma bölgesinden alınır.

Uygulama, SDK tarafından seçilen geçerli yazma uç noktasını ve okuma uç noktasını, `WriteEndpoint` `ReadEndpoint` sdk sürüm 1,8 ve üzeri sürümlerde bulunan iki özelliği denetleyerek ve kullanılabilir. `PreferredLocations`Özellik ayarlanmamışsa, tüm istekler geçerli yazma bölgesinden sunulacaktır.

Tercih edilen konumları belirtmezseniz, ancak `setCurrentLocation` yöntemini kullandıysanız, SDK, tercih edilen konumları istemcinin üzerinde çalıştığı geçerli bölgeye göre otomatik olarak doldurur. SDK, bölgeleri bir bölgenin yakınlığını geçerli bölgeye göre sıralar.

## <a name="net-sdk"></a>.NET SDK

SDK herhangi bir kod değişikliği olmadan kullanılabilir. Bu durumda SDK hem okuma hem de yazma işlemlerini otomatik olarak geçerli yazma bölgesine yönlendirir.

.NET SDK’nın 1.8 ve sonraki sürümlerinde, DocumentClient oluşturucusu için ConnectionPolicy parametresi, Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations adlı bir özelliğe sahiptir. Bu özellik `<string>` Koleksiyonu türünde olup bölge adlarının listesini içermelidir. Dize değerleri, [Azure bölgeleri][regions] sayfasındaki Bölge adı sütununa göre, ilk ve son karakterden önce veya sonra boşluk olmadan biçimlendirilir.

Geçerli yazma ve okuma uç noktaları sırasıyla DocumentClient.WriteEndpoint ve DocumentClient.ReadEndpoint içinde kullanılabilir.

> [!NOTE]
> Uç noktaların URL’leri, uzun ömürlü sabitler olarak değerlendirilmemelidir. Bu noktada hizmet bunları güncelleştirebilir. SDK bu değişikliği otomatik olarak işler.
>

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET v2 SDK kullanıyorsanız, `PreferredLocations` tercih edilen bölgeyi ayarlamak için özelliğini kullanın.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

Alternatif olarak, `SetCurrentLocation` özelliğini kullanabilir ve SDK 'ya yakınlık temelinde tercih edilen konumu seçmesini sağlayabilirsiniz.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET v3 SDK kullanıyorsanız, `ApplicationPreferredRegions` tercih edilen bölgeyi ayarlamak için özelliğini kullanın.

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

Alternatif olarak, `ApplicationRegion` özelliğini kullanabilir ve SDK 'ya yakınlık temelinde tercih edilen konumu seçmesini sağlayabilirsiniz.

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> Uç noktaların URL’leri, uzun ömürlü sabitler olarak değerlendirilmemelidir. Bu noktada hizmet bunları güncelleştirebilir. SDK bu değişikliği otomatik olarak işler.
>
>

Aşağıda, Node.js/Javascriptiçin bir kod örneği verilmiştir.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK'sı

Aşağıdaki kod, Python SDK kullanarak tercih edilen konumların nasıl ayarlanacağını gösterir:

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> Java v4 SDK 'Sı

Aşağıdaki kod, Java SDK kullanarak tercih edilen konumların nasıl ayarlanacağını gösterir:

# <a name="async"></a>[Eş](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) zaman uyumsuz API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[Eşitle](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) eşitleme API 'si

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST

Bir veritabanı hesabı birden çok bölgede kullanıma sunulduktan sonra istemciler, bu URI üzerinde bir GET isteği gerçekleştirerek kullanılabilirliğini sorgulayabilir `https://{databaseaccount}.documents.azure.com/`

Hizmet, bölgelerin listesini ve çoğaltmalar için karşılık gelen Azure Cosmos DB uç nokta URI’lerini döndürür. Yanıtta geçerli yazma bölgesi belirtilir. Daha sonra istemci aşağıdaki gibi diğer tüm REST API istekleri için uygun uç noktayı seçebilir.

Örnek yanıt

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* Tüm PUT, POST ve DELETE istekleri, belirtilen yazma URI’sine gitmelidir
* Tüm ve diğer salt okuma istekleri (örneğin sorgular), istemci seçiminin herhangi bir uç noktasına gidebilir

Salt okunur bölgelere yönelik yazma istekleri, HTTP hata kodu 403 (“Yasak”) ile başarısız olur.

Yazma bölgesi istemcinin ilk bulma aşamasından sonra değişirse, önceki yazma bölgesine sonraki yazma işlemleri HTTP hata kodu 403 ("yasak") ile başarısız olur. İstemci daha sonra güncelleştirilmiş yazma bölgesini almak için bölgelerin listesini ALIR (GET).

Hepsi bu kadar. Böylece bu öğretici tamamlanmış olur. [Azure Cosmos DB’deki tutarlılık düzeyleri](consistency-levels.md) bölümünü okuyarak genel olarak çoğaltılan hesabınızın tutarlılığının nasıl yönetileceğini öğrenebilirsiniz. Ayrıca genel veritabanı çoğaltmasının Azure Cosmos DB’de nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure Cosmos DB ile verileri genel olarak dağıtma](distribute-data-globally.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdakileri yaptınız:

> [!div class="checklist"]
> * Azure portalını kullanarak genel dağıtımı yapılandırma
> * SQL API’lerini kullanarak genel dağıtımı yapılandırma

Artık Azure Cosmos DB yerel öykünücüsünü kullanarak yerel olarak geliştirme konusunda bilgi almak için sonraki öğreticiye geçebilirsiniz.

> [!div class="nextstepaction"]
> [Öykünücü ile yerel olarak geliştirme](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/