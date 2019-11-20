---
title: Azure Cosmos DB Graph veritabanı için bölgesel uç noktalar
description: Uygulamanız için en yakın grafik veritabanı uç noktasına bağlanmayı öğrenin
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/09/2019
ms.openlocfilehash: 6bce69889910296f5ff28090e62cfe27c0640d95
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755026"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Azure Cosmos DB Graph hesabının bölgesel uç noktaları
Azure Cosmos DB Graph veritabanı, uygulamaların birden çok okuma uç noktası kullanabilmesi için [küresel olarak dağıtılır](distribute-data-globally.md) . Birden çok konumda yazma erişimi gerektiren uygulamalar, [çok yöneticili](how-to-multi-master.md) bir özelliği etkinleştirmelidir.

Birden fazla bölge seçme nedenleri:
1. **Yatay okuma ölçeklenebilirliği** -uygulama yükü arttıkça, okuma trafiğini farklı Azure bölgelerine yönlendiren akıllıca olabilir.
2. **Düşük gecikme süresi** -okuma ve yazma trafiğini en yakın Azure bölgesine yönlendirerek her bir geçiş için ağ gecikme süresi ek yükünü azaltabilirsiniz.

Cosmos DB hesapta Azure Resource Manager ilkesi ayarlanarak **veri** yerleşimi gereksinimi elde edilir. Müşteri, verileri Cosmos DB çoğaltan bölgeleri sınırlayabilir.

## <a name="traffic-routing"></a>Trafik yönlendirme

Cosmos DB Graph veritabanı altyapısı, her biri birden çok küme içeren birden çok bölgede çalışmaktadır. Her kümenin yüzlerce makinesi vardır. Cosmos DB Graph hesabı DNS CNAME *AccountName.Gremlin.Cosmos.Azure.com* , BIR kümenin DNS kaydını çözer. Yük dengeleyicinin tek bir IP adresi, iç küme topolojisini gizler.

Cosmos DB Graph hesabının her bölgesi için bölgesel DNS CNAME kaydı oluşturulur. Bölgesel uç noktanın biçimi *AccountName-Region.Gremlin.Cosmos.Azure.com*. Bölgesel uç noktanın bölge kesimi, [Azure bölge](https://azure.microsoft.com/global-infrastructure/regions) adından tüm boşluklar kaldırılarak elde edilir. Örneğin, `"contoso"` genel veritabanı hesabının `"East US 2"` bölgesi bir DNS CNAME *contoso-eastus2.Gremlin.Cosmos.Azure.com*

TinkerPop Gremlin istemcisi tek bir sunucu ile çalışacak şekilde tasarlanmıştır. Uygulama, okuma ve yazma trafiği için genel yazılabilir DNS CNAME 'i kullanabilir. Bölge kullanan uygulamalar, okuma trafiği için bölgesel uç nokta kullanmalıdır. Yalnızca belirli bir bölge yazmaları kabul edecek şekilde yapılandırıldıysa, yazma trafiği için bölgesel uç noktası kullanın. 

> [!NOTE]
> Cosmos DB Graph Engine, yazma bölgesine giden trafiği çağırarak okuma bölgesinde yazma işlemini kabul edebilir. Çapraz geçiş gecikmesini arttığı ve gelecekte kısıtlamalara tabi olan salt okuma bölgesine yazma gönderilmesi önerilmez.

Genel veritabanı hesabı CNAME her zaman geçerli bir yazma bölgesine işaret eder. Yazma bölgesinin sunucu tarafı yük devretmesi sırasında, Cosmos DB genel veritabanı hesabı CNAME 'i yeni bölgeyi gösterecek şekilde güncelleştirir. Uygulama yük devretmeden sonra trafiği yeniden işleyemez, genel veritabanı hesabı DNS CNAME 'i kullanmalıdır.

> [!NOTE]
> Cosmos DB, trafiği çağıranın coğrafi yakınına göre yönlendirmez. Benzersiz uygulama gereksinimlerine göre doğru bölgeyi seçmek her bir uygulamaya kadar yapılır.

## <a name="portal-endpoint-discovery"></a>Portal uç noktası bulma

Azure Cosmos DB Graph hesabı için bölgelerin listesini almanın en kolay yolu, Azure portal genel bakış dikey pencerıdır. Bu, bölgeyi sık değiştirmeyecek veya uygulama yapılandırması aracılığıyla listeyi güncelleştirmek için bir yola sahip olan uygulamalar için çalışır.

![Portaldan Cosmos DB Graph hesabının bölgelerini alma](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

Aşağıdaki örnekte bölgesel Gremlin uç noktasına erişim genel ilkeleri gösterilmektedir. Uygulama, uygulamasının örneklendirilecek trafik sayısını ve bunlara karşılık gelen Gremlin istemcilerinin sayısını göz önünde bulundurmalıdır.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>SDK uç noktası bulma

Uygulama, Graph hesabının okuma ve yazma konumlarını saptamak için [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) kullanabilir. Bu konumlar, sunucu tarafında veya otomatik yük devretmede el ile yeniden yapılandırma yoluyla herhangi bir zamanda değiştirilebilir.

TinkerPop Gremlin SDK Cosmos DB Graph veritabanı hesap bölgelerini bulma API 'sine sahip değil. Çalışma zamanı uç noktası keşfi gerektiren uygulamaların, işlem alanında 2 ayrı SDK barındırması gerekir.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Sonraki adımlar
* Azure Cosmos DB 'de [veritabanı hesapları denetimini yönetme](how-to-manage-database-account.md)
* Azure Cosmos DB [yüksek kullanılabilirlik](high-availability.md)
* [Azure Cosmos DB ile küresel dağıtım](global-dist-under-the-hood.md)
* Azure Cosmos DB için [Azure CLI örnekleri](cli-samples.md)
