---
title: Azure Cosmos DB ' de daha yüksek kullanılabilirlik ve performans için iyileştirmek üzere özel eşitleme uygulama
description: Azure Cosmos DB ' de daha yüksek kullanılabilirlik ve performans için iyileştirmek üzere özel eşitleme uygulamayı öğrenin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 1fdd05f8a4757a49414a2a03c8f991a80186ed44
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755054"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Daha yüksek kullanılabilirlik ve performans için iyileştirmek üzere özel eşitleme uygulayın

Azure Cosmos DB, tutarlılık, performans ve kullanılabilirlik arasında zorunluluğunu getirir dengelemek üzere arasından seçim yapabileceğiniz [beş iyi tanımlanmış tutarlılık düzeyi](consistency-levels.md) sunar. Güçlü tutarlılık, Azure Cosmos hesabının kullanılabildiği her bölgede verilerin zaman uyumlu olarak çoğaltılmasını ve sürekli olarak kalıcı olmasını sağlamaya yardımcı olur. Bu yapılandırma en yüksek düzeyde dayanıklılık sağlar, ancak performans ve kullanılabilirlik maliyetlerine gelir. Uygulamanızın, kullanılabilirliği tehlikeye atmadan uygulama gereksinimlerini karşılayacak şekilde denetlemesini veya rahatlılığını istiyorsanız, istediğiniz dayanıklılık düzeyini elde etmek için uygulama katmanında *özel eşitleme* kullanabilirsiniz.

Aşağıdaki görüntüde özel eşitleme modeli görsel olarak gösterilmiştir:

![Özel eşitleme](./media/how-to-custom-synchronization/custom-synchronization.png)

Bu senaryoda, bir Azure Cosmos kapsayıcısı, birden çok kıtadaki çeşitli bölgelerde küresel olarak çoğaltılır. Bu senaryodaki tüm bölgeler için güçlü tutarlılık kullanılması performansı etkiler. Yazma gecikmesinden ödün vermeden daha yüksek düzeyde veri dayanıklılığı sağlamak için, uygulama aynı [oturum belirtecini](how-to-manage-consistency.md#utilize-session-tokens)paylaşan iki istemci kullanabilir.

İlk istemci, verileri yerel bölgeye yazabilir (örneğin, ABD Batı). İkinci istemci (örneğin, ABD Doğu) eşitlemeyi sağlamak için kullanılan bir okuma istemcinin olması gerekir. Yazma yanıtından oturum belirtecini aşağıdaki okumaya ayırarak, okuma ABD Doğu yazma eşitlemesini sağlar. Azure Cosmos DB, yazma işlemlerinin en az bir bölge tarafından görülmesini sağlar. Özgün yazma bölgesi kapalıysa, bölgesel kesintiden etkilenmeleri garanti edilir. Bu senaryoda her yazma işlemi ABD Doğu eşitlenir ve tüm bölgelerde güçlü tutarlılık sağlamak için gecikme süresini azaltır. Her bölgede yazma gerçekleştiği çok yöneticili bir senaryoda bu modeli, paralel olarak birden çok bölgeye eşitlemek için genişletebilirsiniz.

## <a name="configure-the-clients"></a>İstemcileri yapılandırma

Aşağıdaki örnek, özel eşitleme için iki istemciyi örnekleyen bir veri erişim katmanını gösterir:

### <a name="net-v2-sdk"></a>.Net v2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net v3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Özel eşitlemeyi uygulama

İstemciler başlatıldıktan sonra, uygulama yerel bölgeye (ABD Batı) yazma işlemleri gerçekleştirebilir ve yazma işlemlerini ABD Doğu için aşağıdaki gibi zorunlu hale getirebilirsiniz.

### <a name="net-v2-sdk"></a>.Net v2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net v3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

Modeli, paralel olarak birden çok bölgeye eşitlenecek şekilde genişletebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB genel dağıtım ve tutarlılık hakkında daha fazla bilgi edinmek için şu makaleleri okuyun:

* [Azure Cosmos DB doğru tutarlılık düzeyini seçin](consistency-levels-choosing.md)
* [Azure Cosmos DB tutarlılık, kullanılabilirlik ve performans avantajları](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB tutarlılığı yönetme](how-to-manage-consistency.md)
* [Azure Cosmos DB bölümlendirme ve veri dağıtımı](partition-data.md)
