---
title: Azure Cosmos kapsayıcısı için benzersiz anahtarlar tanımlama
description: Azure Cosmos kapsayıcısı için benzersiz anahtarlar tanımlama hakkında bilgi edinin
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: thweiss
ms.openlocfilehash: 2f61afd12a50b1b135419585bfada52d7ef98f3a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811650"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Azure Cosmos kapsayıcısı için benzersiz anahtarlar tanımlama

Bu makalede, Azure Cosmos kapsayıcısı oluştururken [benzersiz anahtarları](unique-keys.md) tanımlamanın farklı yolları sunulmaktadır. Azure portal veya SDK 'Lardan birini kullanarak bu işlemi gerçekleştirmek mümkün değildir.

## <a name="use-the-azure-portal"></a>Azure Portal’ı kullanma

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-sql-api-dotnet.md#create-account) veya mevcut olanı seçin.

1. **Veri Gezgini** bölmesini açın ve üzerinde çalışmak istediğiniz kapsayıcıyı seçin.

1. **Yeni kapsayıcıya**tıklayın.

1. **Kapsayıcı Ekle** iletişim kutusunda, **+** benzersiz anahtar Ekle ' ye tıklayarak benzersiz bir anahtar girişi ekleyin.

1. Benzersiz anahtar kısıtlamasının yolunu girin

1. Gerekirse **+ benzersiz anahtar Ekle** ' ye tıklayarak daha fazla benzersiz anahtar girişi ekleyin

    ![Azure portal 'de benzersiz anahtar kısıtlama girişinin ekran görüntüsü](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>Powershell'i kullanma

Benzersiz anahtarlarla bir kapsayıcı oluşturmak için bkz. [benzersiz anahtar ve TTL Ile Azure Cosmos kapsayıcısı oluşturma](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk-v2"></a>.NET SDK v2 'yi kullanma

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)kullanarak yeni bir kapsayıcı oluştururken, benzersiz anahtar kısıtlamalarını tanımlamak için `UniqueKeyPolicy` nesnesi kullanılabilir.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>.NET SDK V3 'i kullanma

[.NET SDK V3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)kullanarak yeni bir kapsayıcı oluştururken, benzersiz anahtarları kısa ve okunabilir bir şekilde BILDIRMEK için SDK 'nın Fluent API kullanın.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Java SDK 'sını kullanma

[Java SDK 'sını](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)kullanarak yeni bir kapsayıcı oluştururken, benzersiz anahtar kısıtlamalarını tanımlamak için `UniqueKeyPolicy` nesnesi kullanılabilir.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Node. js SDK 'sını kullanma

[Node. js SDK 'sını](https://www.npmjs.com/package/@azure/cosmos)kullanarak yeni bir kapsayıcı oluştururken, benzersiz anahtar kısıtlamalarını tanımlamak için `UniqueKeyPolicy` nesnesi kullanılabilir.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Python SDK 'sını kullanma

[Python SDK](https://pypi.org/project/azure-cosmos/)kullanarak yeni bir kapsayıcı oluştururken, benzersiz anahtar kısıtlamaları parametre olarak geçirilen sözlüğün bir parçası olarak belirtilebilir.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Sonraki adımlar

- [Bölümlendirme](partition-data.md) hakkında daha fazla bilgi edinin
- [Dizin oluşturmanın nasıl çalıştığını](index-overview.md) keşfet
