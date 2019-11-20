---
title: Azure Işlevlerinde bağlantıları yönetme
description: Statik bağlantı istemcileri kullanarak Azure Işlevlerinde performans sorunlarından kaçınmaya nasıl engel olabileceğinizi öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 26702ae63dcb7aadb96b5bf77f96a44f7d6776f5
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114319"
---
# <a name="manage-connections-in-azure-functions"></a>Azure Işlevlerinde bağlantıları yönetme

Bir işlev uygulamasındaki işlevler kaynakları paylaşır. Bu paylaşılan kaynaklar arasında bağlantılar: HTTP bağlantıları, veritabanı bağlantıları ve Azure depolama gibi hizmetlere bağlantılar. Aynı anda çok sayıda işlev çalıştığında, kullanılabilir bağlantıların tükenme olasılığı vardır. Bu makalede, işlevlerinizin, gerekenden daha fazla bağlantı kullanmaktan kaçınmak için nasıl kodleneceği açıklanır.

## <a name="connection-limit"></a>Bağlantı sınırı

Bir işlev uygulaması bir [korumalı alan ortamında](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)çalıştığından, kullanılabilir bağlantı sayısı kısmen sınırlıdır. Korumalı alanın kodunuzda hangi kısıtlamalardan biri, örnek başına 600 etkin (1.200 toplam) bağlantı olan giden bağlantı sayısı için bir sınırlamadır. Bu sınıra ulaştığınızda, işlevler çalışma zamanı şu iletiyi günlüklere Yazar: `Host thresholds exceeded: Connections`. Daha fazla bilgi için bkz. [işlevler hizmet limitleri](functions-scale.md#service-limits).

Bu sınır örnek başına. Ölçek denetleyicisi daha fazla isteği işlemek için [işlev uygulama örnekleri eklerse](functions-scale.md#how-the-consumption-and-premium-plans-work) , her örneğin bağımsız bir bağlantı sınırı vardır. Bu, genel bağlantı sınırı olmadığı anlamına gelir ve tüm etkin örneklerde 600 ' den çok etkin bağlantınız olabilir.

Sorun giderirken, işlev uygulamanız için Application Insights etkinleştirdiğinizden emin olun. Application Insights, yürütme gibi işlev uygulamalarınız için ölçümleri görüntülemenize olanak sağlar. Daha fazla bilgi için bkz. [Application Insights Telemetriyi görüntüleme](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statik istemciler

Gerekenden daha fazla bağlantı tutmaya kaçınmak için, her bir işlev çağrısında yeni bir tane oluşturmak yerine istemci örneklerini yeniden kullanın. İşlevinizi yazmak isteyebileceğiniz herhangi bir dil için istemci bağlantılarını yeniden kullanmanızı öneririz. Örneğin, [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [documentclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)ve Azure Storage istemcileri gibi .NET istemcileri, tek bir statik istemci kullanırsanız bağlantıları yönetebilir.

Azure Işlevleri uygulamasında hizmete özel bir istemci kullanırken izlenecek bazı yönergeler aşağıda verilmiştir:

- Her işlev çağrısında yeni bir istemci oluşturmayın.
- Her işlev çağrısının kullanabileceği tek bir statik istemci oluşturun.
- Farklı işlevler aynı hizmeti kullanıyorsa, paylaşılan bir yardımcı sınıfta tek bir statik istemci oluşturmayı *düşünün* .

## <a name="client-code-examples"></a>İstemci kodu örnekleri

Bu bölümde, işlev kodunuzda istemcileri oluşturmak ve kullanmak için en iyi yöntemler gösterilmektedir.

### <a name="httpclient-example-c"></a>HttpClient örneği (C#)

Statik bir [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) örneği C# oluşturan işlev kodu örneği aşağıda verilmiştir:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

.NET 'teki [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) hakkında sık sorulan sorular "İstemcimin atılmalıdır mi?" Genel olarak, bunları kullanarak işiniz bittiğinde uygulayan `IDisposable` nesneleri atıyorsunuz. Ancak, işlev sona erdiğinde bu işlemi yapmadığınızda, bir statik istemciyi atmayın. Statik istemcinin uygulamanızın süresini gerçek zamanlı olarak istiyor.

### <a name="http-agent-examples-javascript"></a>HTTP Aracısı örnekleri (JavaScript)

Daha iyi bağlantı yönetimi seçenekleri sağladığından, [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) `node-fetch` modül gibi yerel olmayan yöntemler yerine yerel sınıfı kullanmanız gerekir. Bağlantı parametreleri, `http.agent` sınıfındaki seçenekler aracılığıyla yapılandırılır. HTTP aracısında kullanılabilen ayrıntılı seçenekler için, bkz. [Yeni Aracı (\[Seçenekler\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

`http.globalAgent` Tarafından`http.request()` kullanılan genel sınıf, bu değerlerin tümünü kendi varsayılanlarına ayarlanmış olarak içerir. Işlevlerde bağlantı sınırlarını yapılandırmak için önerilen yöntem, genel olarak en yüksek sayıyı ayarlayamaktır. Aşağıdaki örnek, işlev uygulaması için maksimum yuva sayısını ayarlar:

```js
http.globalAgent.maxSockets = 200;
```

 Aşağıdaki örnek yalnızca bu istek için özel bir HTTP aracısına sahip yeni bir HTTP isteği oluşturur:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient kod örneği (C#)

[Documentclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) bir Azure Cosmos DB örneğine bağlanır. Azure Cosmos DB belge, [Uygulamanızın ömrü boyunca tek bir Azure Cosmos db istemci kullanmanızı](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage)önerir. Aşağıdaki örnek, bir işlevinde bunu yapmak için bir model gösterir:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient kodu örneği (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) bir Azure Cosmos DB örneğine bağlanır. Azure Cosmos DB belge, [Uygulamanızın ömrü boyunca tek bir Azure Cosmos db istemci kullanmanızı](../cosmos-db/performance-tips.md#sdk-usage)önerir. Aşağıdaki örnek, bir işlevinde bunu yapmak için bir model gösterir:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient bağlantıları

İşlev kodunuz, bir SQL ilişkisel veritabanına bağlantı kurmak için SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) için .NET Framework veri sağlayıcısı kullanabilir. Bu aynı zamanda, [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)gibi ADO.NET kullanan veri çerçeveleri için temel sağlayıcıdır. [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) ve [documentclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) bağlantılarından farklı olarak, ADO.NET varsayılan olarak bağlantı havuzu uygular. Ancak hala bağlantı dışında çalışmaya devam edebilirsiniz. Daha fazla bilgi için bkz. [SQL Server bağlantı havuzu (ADO.net)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Entity Framework gibi bazı veri çerçeveleri, genellikle bir yapılandırma dosyasının **connectionStrings** bölümünden bağlantı dizelerini alır. Bu durumda, işlev uygulaması ayarlarınızın **bağlantı dizeleri** koleksiyonuna ve yerel projenizde [yerel. Settings. json dosyasında](functions-run-local.md#local-settings-file) SQL veritabanı bağlantı dizelerini açıkça eklemeniz gerekir. İşlev kodunuzda [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) 'ın bir örneğini oluşturuyorsanız, bağlantı dizesi değerini diğer Bağlantılarınızdaki **uygulama ayarlarında** depolamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Statik istemcileri neden önerdiğimiz hakkında daha fazla bilgi için bkz. [Hatalı örnek oluşturma kötü modeli](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Daha fazla Azure Işlevleri performans ipucu için bkz. [Azure işlevlerinin performansını ve güvenilirliğini iyileştirme](functions-best-practices.md).
