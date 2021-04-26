---
title: Büyük bölüm anahtarı ile Azure Cosmos kapsayıcıları oluşturma
description: Azure portal ve farklı SDK 'Ları kullanarak büyük bölüm anahtarı ile Azure Cosmos DB bir kapsayıcı oluşturmayı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ad26d63ca06f5a46a4a1f77d329d04896e96c52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339315"
---
# <a name="create-containers-with-large-partition-key"></a>Büyük bölüm anahtarı ile kapsayıcılar oluşturma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB, verilerin yatay ölçeklenmesini sağlamak için karma tabanlı bölümleme şeması kullanır. Mayıs 3 2019 ' den önce oluşturulan tüm Azure Cosmos kapsayıcıları, Bölüm anahtarının ilk 100 baytlarına göre karmayı hesaplayan bir karma işlev kullanır. Aynı ilk 100 bayta sahip birden çok bölüm anahtarı varsa, bu mantıksal bölümler hizmet tarafından aynı mantıksal bölüm olarak değerlendirilir. Bu, bölüm boyutu kotasının hatalı olması ve bölüm anahtarları genelinde uygulanan benzersiz dizinlerin oluşmasına neden olabilir. Bu sorunu gidermek için büyük bölüm anahtarları sunulmuştur. Azure Cosmos DB artık 2 KB 'a kadar olan büyük bölüm anahtarlarını destekliyor.

Büyük bölüm anahtarları, karma işlevin gelişmiş bir sürümünün işlevselliği kullanılarak desteklenir. Bu, büyük bölüm anahtarlarından 2 KB 'a kadar benzersiz bir karma değer üretebilirler. Bu karma sürüm, Bölüm anahtarının boyutundan bağımsız olarak yüksek bölümlü anahtar kardinalitesiyle senaryolar için de önerilir. Bölüm anahtarı kardinalitesi benzersiz mantıksal bölümlerin sayısı olarak tanımlanır. Örneğin, bir kapsayıcıdaki ~ 30000 mantıksal bölümlerinin sırası. Bu makalede, Azure portal ve farklı SDK 'Ları kullanarak büyük bölüm anahtarı ile bir kapsayıcının nasıl oluşturulacağı açıklanır.

## <a name="create-a-large-partition-key-azure-portal"></a>Büyük bölüm anahtarı oluşturma (Azure portal)

Büyük bölüm anahtarı oluşturmak için, Azure portal kullanarak yeni bir kapsayıcı oluşturduğunuzda, **bölüm anahtarımın anahtarından 100 daha büyük olduğunu** denetleyin. Büyük bölüm anahtarlarına ihtiyacınız yoksa veya 1,18 'den eski SDK sürümünde çalışan uygulamalarınız varsa onay kutusunun seçimini kaldırın.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Azure portal kullanarak büyük bölüm anahtarları oluşturma":::

## <a name="create-a-large-partition-key-powershell"></a>Büyük bölüm anahtarı oluşturma (PowerShell)

Büyük bölüm anahtarı desteğiyle bir kapsayıcı oluşturmak için, bkz.

* [Büyük bölüm anahtarı boyutuyla Azure Cosmos kapsayıcısı oluşturma](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Büyük bölüm anahtarı oluşturma (.NET SDK)

.NET SDK kullanarak büyük bölüm anahtarı ile bir kapsayıcı oluşturmak için, `PartitionKeyDefinitionVersion.V2` özelliğini belirtin. Aşağıdaki örnek, PartitionKeyDefinition nesnesi içinde Version özelliğinin nasıl ekleneceğini ve PartitionKeyDefinitionVersion. v2 olarak nasıl ayarlanacağını gösterir.

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>Desteklenen SDK sürümleri

Büyük bölüm anahtarları, SDK 'ların aşağıdaki en düşük sürümleriyle desteklenir:

|SDK türü  | En düşük sürüm   |
|---------|---------|
|.NET     |    1,18     |
|Java eşitleme     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | `2017-05-03`istek üst bilgisini kullanarak sürümünden daha yüksek `x-ms-version` .|
| Resource Manager şablonu | `"version":2`nesne içindeki özelliğini kullanarak sürüm 2 `partitionKey` . |

Şu anda, Power BI ve Azure Logic Apps içinde büyük bölüm anahtarı ile kapsayıcıları kullanamazsınız. Kapsayıcıları, bu uygulamalardan büyük bir bölüm anahtarı olmadan kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
* [Azure Cosmos DB'de İstek birimleri](request-units.md)
* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md)
* [Azure Cosmos hesabıyla çalışma](./account-databases-containers-items.md)