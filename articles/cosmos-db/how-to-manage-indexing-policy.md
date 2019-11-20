---
title: Azure Cosmos DB Dizin oluşturma ilkelerini yönetme
description: Azure Cosmos DB 'da dizin oluşturma ilkelerini yönetmeyi öğrenin
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: thweiss
ms.openlocfilehash: 46d0124eb701b0c2d779a96c8efd50ba43e8fc07
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034441"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB Dizin oluşturma ilkelerini yönetme

Azure Cosmos DB, veriler her kapsayıcı için tanımlanan [Dizin oluşturma ilkelerinin](index-policy.md) dizinlenmiştir. Yeni oluşturulan kapsayıcılar için varsayılan dizin oluşturma ilkesi herhangi bir dize veya sayı için Aralık dizinleri uygular. Bu ilke, kendi özel dizin oluşturma ilkenize göre geçersiz kılınabilir.

## <a name="indexing-policy-examples"></a>Dizin oluşturma ilkesi örnekleri

Burada, Azure portal üzerinde sunulduklarında, JSON biçiminde gösterilen dizin oluşturma ilkelerine ilişkin bazı örnekler verilmiştir. Aynı parametreler Azure CLı veya herhangi bir SDK aracılığıyla ayarlanabilir.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Bazı özellik yollarını seçmeli olarak hariç tutmak için devre dışı bırakma ilkesi

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Bu dizin oluşturma ilkesi, ```kind```, ```dataType``` ve ```precision``` değerlerini varsayılan değerlerine el ile ayarlayan bir değere eşdeğerdir. Bu özelliklerin artık açıkça ayarlanması gerekmez ve bunları Dizin ilkenizde tamamen atlayabilirsiniz (Yukarıdaki örnekte gösterildiği gibi).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Bazı özellik yollarını seçmeli olarak dahil etmek için katılım ilkesi

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Bu dizin oluşturma ilkesi, ```kind```, ```dataType``` ve ```precision``` değerlerini varsayılan değerlerine el ile ayarlayan bir değere eşdeğerdir. Bu özelliklerin artık açıkça ayarlanması gerekmez ve bunları Dizin ilkenizde tamamen atlayabilirsiniz (Yukarıdaki örnekte gösterildiği gibi).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> Azure Cosmos DB, modelinize eklenebilen yeni bir özelliğin önceden oluşturulmasını sağlamak için bir **kabul etme** dizin oluşturma ilkesinin kullanılması genellikle önerilir.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Yalnızca belirli bir özellik yolunda uzamsal dizin kullanma

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Bileşik dizin oluşturma ilkesi örnekleri

Tek tek özellikler için yolların dahil edilmesi veya dışlanması buna ek olarak, bileşik bir dizin de belirtebilirsiniz. Birden çok özellik için `ORDER BY` yan tümcesine sahip bir sorgu gerçekleştirmek istiyorsanız, bu özelliklerde bir [bileşik dizin](index-policy.md#composite-indexes) gereklidir. Ayrıca, bileşik dizinlerin filtre içeren sorgularda bir performans avantajı ve farklı özelliklerde ORDER BY yan tümcesi vardır.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>İçin tanımlanan bileşik dizin (ad ASC, Age DESC):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Ad ve yaş üzerindeki yukarıdaki bileşik dizin, sorgu #1 ve sorgu #2 için gereklidir:

Sorgu #1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Sorgu #2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Bu bileşik dizin sorgu #3 ve sorgu #4 yararlı olacak ve filtreleri iyileştirtirecektir:

Sorgu #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Sorgu #4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>İçin Birleşik Dizin tanımlandı (ad ASC, Age ASC) ve (ad ASC, Age DESC):

Aynı dizin oluşturma ilkesi içinde birden çok farklı bileşik dizin tanımlayabilirsiniz.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>İçin tanımlanan bileşik dizin (ad ASC, Age ASC):

Sıralamayı belirtmek için isteğe bağlıdır. Belirtilmemişse, düzen artan olur.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Tüm özellik yollarını dışlama ancak dizin oluşturmayı etkin tutma

Bu ilke, [yaşam süresi (TTL) özelliğinin](time-to-live.md) etkin olduğu, ancak ikincil dizin gerekmediği durumlarda (Azure Cosmos DB saf anahtar-değer deposu olarak kullanmak için) kullanılabilir.

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Dizin oluşturma

Bu ilke Dizin oluşturmayı devre dışı bırakır. @No__t-0 `none` olarak ayarlanırsa, kapsayıcıda bir TTL ayarlayamazsınız.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Dizin oluşturma ilkesi güncelleştiriliyor

Azure Cosmos DB, dizin oluşturma ilkesi aşağıdaki yöntemlerden herhangi biri kullanılarak güncelleştirilebilen olabilir:

- Azure portal
- Azure CLı 'yi kullanma
- PowerShell 'i kullanma
- SDK 'Lardan birini kullanma

Dizin [oluşturma ilkesi güncelleştirmesi](index-policy.md#modifying-the-indexing-policy) bir dizin dönüşümünü tetikler. Bu dönüşümün ilerleme durumu SDK 'lardan de izlenebilir.

> [!NOTE]
> Dizin oluşturma ilkesi güncelleştirilirken Azure Cosmos DB yazma işlemleri kesintiye uğramadan olur. Yeniden dizinleme sırasında, Dizin güncelleştirildiğinden sorgular kısmi sonuçlar döndürebilir.

## <a name="use-the-azure-portal"></a>Azure Portal’ı kullanma

Azure Cosmos kapsayıcıları dizin oluşturma ilkelerini, Azure portal doğrudan düzenlemenize izin veren bir JSON belgesi olarak depolar.

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

1. Yeni bir Azure Cosmos hesabı oluşturun veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve üzerinde çalışmak istediğiniz kapsayıcıyı seçin.

1. **Ölçek & ayarları**' na tıklayın.

1. Dizin oluşturma ilkesi JSON belgesini değiştirme ( [aşağıdaki](#indexing-policy-examples)örneklere bakın)

1. İşiniz bittiğinde **Kaydet** ' e tıklayın.

![Azure portal kullanarak dizin oluşturmayı yönetme](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Azure CLI kullanma

Özel bir dizin oluşturma ilkesiyle kapsayıcı oluşturmak için bkz. [CLI kullanarak özel dizin ilkesiyle kapsayıcı oluşturma](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>PowerShell'i kullanma

Özel bir dizin oluşturma ilkesiyle kapsayıcı oluşturmak için bkz. [PowerShell kullanarak özel dizin ilkesiyle kapsayıcı oluşturma](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk-v2"></a>.NET SDK v2 'yi kullanma

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 'deki `DocumentCollection` nesnesi, `IndexingMode` ' ü değiştirmenize ve `IncludedPaths` ve `ExcludedPaths` ' i eklemenize veya kaldırmanıza imkan tanıyan bir `IndexingPolicy` özelliği sunar.

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Dizin dönüştürme ilerlemesini izlemek için, `PopulateQuotaInfo` özelliğini `true` olarak ayarlayan bir `RequestOptions` nesnesi geçirin.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>.NET SDK V3 'i kullanma

[.NET SDK V3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) 'deki `ContainerProperties` nesnesi (kullanımı ile Ilgili [Bu hızlı başlangıç](create-sql-api-dotnet.md) ), `IndexingMode` ' ü değiştirmenize ve `IncludedPaths` ve `ExcludedPaths` ' i değiştirmenize veya kaldırmanıza imkan tanıyan bir `IndexingPolicy` özelliği sunar.

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Dizin dönüştürme ilerlemesini izlemek için, `PopulateQuotaInfo` özelliğini `true` olarak ayarlayan `RequestOptions` nesnesini geçirin, sonra `x-ms-documentdb-collection-index-transformation-progress` yanıt üstbilgisinden değeri alın.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Yeni bir kapsayıcı oluştururken özel bir dizin oluşturma ilkesi tanımlarken, SDK V3's Fluent API, bu tanımı kısa ve verimli bir şekilde yazmanıza izin verir:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Java SDK 'sını kullanma

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 'sindeki `DocumentCollection` nesnesi (kullanımı ile Ilgili [Bu hızlı başlangıç](create-sql-api-java.md) ) `getIndexingPolicy()` ve `setIndexingPolicy()` yöntemlerini gösterir. İşledikleri `IndexingPolicy` nesnesi dizin oluşturma modunu değiştirmenize ve dahil edilen ve dışlanan yolları eklemenize veya kaldırmanıza olanak sağlar.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
ExcludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Bir kapsayıcıda Dizin dönüştürme ilerlemesini izlemek için, kota bilgilerinin doldurulduğu `RequestOptions` nesnesini geçirin ve ardından `x-ms-documentdb-collection-index-transformation-progress` yanıt üstbilgisinden değeri alın.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Node. js SDK 'sını kullanma

[Node. js SDK](https://www.npmjs.com/package/@azure/cosmos) 'dan `ContainerDefinition` arabirimi (kullanımı ile Ilgili [Bu hızlı başlangıç](create-sql-api-nodejs.md) ), `indexingMode` ' ü değiştirmenize ve `includedPaths` ve `excludedPaths` ' i değiştirmenize veya kaldırmanıza imkan tanıyan bir `indexingPolicy` özelliği sunar.

Kapsayıcının ayrıntılarını alma

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Dizin oluşturma modunu tutarlı olarak ayarlayın

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Uzamsal dizin dahil dahil edilen yol ekleme

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Dışlanan yol Ekle

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Kapsayıcıyı değişikliklerle güncelleştirme

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Bir kapsayıcıda Dizin dönüştürme ilerlemesini izlemek için, `populateQuotaInfo` özelliğini `true` olarak ayarlayan `RequestOptions` nesnesini geçirin, sonra `x-ms-documentdb-collection-index-transformation-progress` yanıt üstbilgisinden değeri alın.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Python SDK 'sını kullanma

[Python SDK 'yı](https://pypi.org/project/azure-cosmos/) kullanırken (kullanımıyla Ilgili [Bu hızlı başlangıç](create-sql-api-python.md) için bkz.) kapsayıcı yapılandırması bir sözlük olarak yönetilir. Bu sözlükten, dizin oluşturma ilkesine ve tüm özniteliklerine erişmek mümkündür.

Kapsayıcının ayrıntılarını alma

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Dizin oluşturma modunu tutarlı olarak ayarlayın

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Dahil edilen yol ve uzamsal dizin ile bir dizin oluşturma ilkesi tanımlama

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Dışlanan bir yol ile dizin oluşturma ilkesi tanımlama

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Bileşik dizin ekleme

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Kapsayıcıyı değişikliklerle güncelleştirme

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde dizin oluşturma hakkında daha fazla bilgi edinin:

- [Dizine genel bakış](index-overview.md)
- [Dizin oluşturma ilkesi](index-policy.md)
