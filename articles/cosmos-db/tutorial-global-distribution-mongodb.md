---
title: MongoDB için Azure Cosmos DB API 'sini kullanarak küresel dağıtım öğreticisi
description: MongoDB için Azure Cosmos DB API 'sini kullanarak genel dağıtımı ayarlamayı öğrenin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 5a9c618fdcf2e904a2a3c03b3ae6b5477cd47c4e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754769"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sini kullanarak genel dağıtılmış veritabanını ayarlama

Bu makalede, genel olarak dağıtılmış bir veritabanını kurmak ve MongoDB için Azure Cosmos DB API 'sini kullanarak bu sunucuya bağlanmak için Azure portal nasıl kullanacağınızı göstereceğiz.

Bu makale aşağıdaki görevleri kapsar: 

> [!div class="checklist"]
> * Azure portalını kullanarak genel dağıtımı yapılandırma
> * [MongoDB için Azure Cosmos DB API](mongodb-introduction.md) 'sini kullanarak genel dağıtımı yapılandırma

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Bölgesel kurulumunuzu doğrulama 
MongoDB için Cosmos DB API 'siyle genel yapılandırmanızı denetetmenin basit bir yolu, Mongo kabuğu 'ndan *IsMaster ()* komutunu kullanmaktır.

Mongo Kabuğunuzdan:

   ```
      db.isMaster()
   ```
   
Örnek sonuçlar:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Tercih edilen bir bölgeye bağlanma 

MongoDB için Azure Cosmos DB API 'SI, genel olarak dağıtılan bir veritabanı için koleksiyonunuzun okuma tercihini belirtmenize olanak sağlar. Hem düşük gecikmeli okumalar hem de genel yüksek kullanılabilirlik için, koleksiyonunuzun okuma tercihini *en yakın* olarak ayarlamanızı öneririz. En yakın bölgeden okumak için *en yakın* okuma tercihi yapılandırılır.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Birincil okuma/yazma bölgesi ve olağanüstü durum kurtarma (DR) senaryoları için ikincil bölge içeren uygulamalar için, koleksiyonunuzun okuma tercihini *ikincil tercih edilen* olarak ayarlamanızı öneririz. Birincil bölgenin kullanılamadığı ikincil bölgeden okumak için *ikincil tercih edilen* okuma tercihi yapılandırılır.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Son olarak, okuma bölgelerinizi kendiniz belirtmek istiyorsanız. Okuma tercihiniz içinde bölge Etiketini ayarlayabilirsiniz.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Hepsi bu kadar. Böylece bu öğretici tamamlanmış olur. [Azure Cosmos DB’deki tutarlılık düzeyleri](consistency-levels.md) bölümünü okuyarak genel olarak çoğaltılan hesabınızın tutarlılığının nasıl yönetileceğini öğrenebilirsiniz. Ayrıca genel veritabanı çoğaltmasının Azure Cosmos DB’de nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure Cosmos DB ile verileri genel olarak dağıtma](distribute-data-globally.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdakileri yaptınız:

> [!div class="checklist"]
> * Azure portalını kullanarak genel dağıtımı yapılandırma
> * MongoDB için Cosmos DB API 'sini kullanarak genel dağıtımı yapılandırma

Artık Azure Cosmos DB yerel öykünücüsünü kullanarak yerel olarak geliştirme konusunda bilgi almak için sonraki öğreticiye geçebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos DB öykünücüsü ile yerel olarak geliştirme](local-emulator.md)
