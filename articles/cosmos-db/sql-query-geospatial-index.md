---
title: Azure Cosmos DB Jeo uzamsal verileri dizinle
description: Uzamsal verileri Azure Cosmos DB ile dizinle
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336223"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Azure Cosmos DB Jeo uzamsal verileri dizinle
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB veritabanı altyapısını gerçekten şema belirsiz olacak şekilde tasarlıyoruz ve JSON için birinci sınıf desteğini sunduk. Azure Cosmos DB için en iyi duruma getirilmiş yazma veritabanı altyapısı, GeoJSON standardında temsil edilen uzamsal verileri yerel olarak anlamıştır.

Bir Nutshell 'de geometri, coğrafi olmayan koordinatlardan 2B düzlemin üzerine yansıtıldıysa, daha sonra **quadtree** kullanılarak hücrelere aşamalı olarak bölünür. Bu hücreler, noktaların yerini koruyan bir **Tepbert alanı doldurma eğrisi** içindeki hücrenin konumuna göre 1G ile eşleştirilir. Ayrıca, konum verileri dizinlendiğinde, **mozaik döşeme** olarak bilinen bir işlemden geçer, diğer bir deyişle, bir konumdan kesişen tüm hücreler Azure Cosmos DB dizininde anahtar olarak tanımlanır ve saklanır. Sorgu zamanında, işaret ve çokgenler gibi bağımsız değişkenler de ilgili hücre KIMLIĞI aralıklarını ayıklamak için de tesseldir ve sonra dizinden veri almak için kullanılır.

(Tüm yollar) için uzamsal dizin içeren bir dizin oluşturma ilkesi belirtirseniz `/*` , kapsayıcıda bulunan tüm veriler etkili uzamsal sorgular için dizinlenir.

> [!NOTE]
> Azure Cosmos DB noktaların, LineStrings, çokgenler ve MultiPolygon dizinini oluşturmayı destekler. Bu türlerden birini dizinlemek istiyorsanız, diğer tüm türleri otomatik olarak dizinliyoruz. Diğer bir deyişle, poligonları dizinleriniz, ayrıca noktaları, LineStrings ve MultiPolygon dizinliyoruz. Yeni bir uzamsal türün dizinlemesi, bu türde geçerli bir GeoJSON verisi olmadığı müddetçe, yazma RU ücreti veya dizin boyutunu etkilemez.

## <a name="modifying-geospatial-configuration"></a>Jeo-uzamsal yapılandırması değiştiriliyor

Kapsayıcıda Jeo-uzamsal **yapılandırması** , uzamsal verilerin nasıl dizine alınacağını belirtir. Kapsayıcı başına bir **Jeo-uzamsal yapılandırma** belirtin: Coğrafya veya Geometry.

Azure portal **Coğrafya** ve **geometri** uzamsal türü arasında geçiş yapabilirsiniz. Geometri uzamsal türüne geçmeden önce [sınırlayıcı kutusuyla geçerli bir uzamsal geometri dizin oluşturma ilkesi](#geometry-data-indexing-examples) oluşturmanız önemlidir.

Azure portal içinde **Veri Gezgini** **Jeo-uzamsal yapılandırması** nasıl ayarlanır:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Jeo-uzamsal yapılandırma ayarlanıyor":::

Ayrıca, `geospatialConfig` **Jeo-uzamsal yapılandırmayı** ayarlamak için .NET SDK 'sında de değişiklik yapabilirsiniz:

Belirtilmemişse, `geospatialConfig` Varsayılan olarak Coğrafya veri türü olur. `geospatialConfig`Öğesini değiştirdiğinizde, kapsayıcıdaki tüm mevcut Jeo uzamsal verilerin yeniden dizinlenir.

İşte, Jeo-uzamsal veri türünü, `geometry` `geospatialConfig` özelliği ayarlanarak ve **sıçrama dingbox** ekleyerek olarak değiştirme örneği:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Coğrafya veri dizin oluşturma örnekleri

Aşağıdaki JSON kod parçacığında **Coğrafya** veri türü için etkin bir dizin oluşturma ilkesi gösterilmiştir. Coğrafya veri türü ile uzamsal veriler için geçerlidir ve uzamsal sorgulama için belgeler içinde bulunan coğrafi JSON noktası, Çokgen, MultiPolygon veya LineString dizimiyle dizin oluşturulur. Azure portal kullanarak dizin oluşturma ilkesini değiştiriyorsanız, kapsayıcıda uzamsal dizin oluşturmayı etkinleştirmek üzere dizin oluşturma ilkesi için aşağıdaki JSON 'u belirtebilirsiniz:

**Coğrafya uzamsal dizin oluşturma ile kapsayıcı dizin oluşturma ilkesi JSON**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Belge içindeki konum GeoJSON değeri hatalı biçimlendirilmiş veya geçersiz ise, uzamsal sorgulama için dizin oluşturulmaz. ST_ISVALID ve ST_ISVALIDDETAILED kullanarak konum değerlerini doğrulayabilirsiniz.

[Dizin oluşturma Ilkesini](how-to-manage-indexing-policy.md) Azure CLI, PowerShell veya HERHANGI bir SDK kullanarak da değiştirebilirsiniz.

## <a name="geometry-data-indexing-examples"></a>Geometri veri dizin oluşturma örnekleri

Coğrafya veri türü ile benzer olan **geometri** veri türü ile, dizine eklenecek ilgili yolları ve türleri belirtmeniz gerekir. Ayrıca, `boundingBox` söz konusu yolun dizine alınması istenen alanı belirtmek için dizin oluşturma ilkesi içinde de bir de belirtmeniz gerekir. Her Jeo-uzamsal yol kendi gerektirir `boundingBox` .

Sınırlayıcı kutu aşağıdaki özelliklerden oluşur:

- **XMin**: en düşük dizinli x koordinatı
- **yMin**: en düşük dizinli y koordinatı
- **xmax**: en fazla dizinli x koordinatı
- **YMax**: en fazla dizinli y koordinatı

Geometrik veriler sonsuz olabilecek bir düzlemi kapladığı için sınırlayıcı bir kutu gereklidir. Ancak uzamsal dizinler, sınırlı bir alan gerektirir. **Coğrafya** veri türü Için, Dünya sınırı ve bir sınırlayıcı kutu ayarlamanız gerekmez.

Verilerinizin tümünü (veya çoğunu) içeren bir sınırlayıcı kutu oluşturun. Yalnızca sınırlayıcı kutunun içindeki nesneler üzerinde hesaplanan işlemler, uzamsal dizin kullanabilir. Sınırlayıcı kutuyu gerekenden büyük hale getirmek, sorgu performansını olumsuz yönde etkiler.

**Geospatialconfig** ile **geometri** verilerinin dizinini oluşturan örnek bir dizin oluşturma ilkesi aşağıda verilmiştir `geometry` :

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
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Yukarıdaki dizin oluşturma ilkesinde x koordinatları için (-10, 10) bir **sıçrama Dingbox** ve y koordinatları için (-20, 20) vardır. Yukarıdaki dizin oluşturma ilkesiyle kapsayıcı, tamamen bu bölgedeki tüm noktaları, çokgenler, MultiPolygon ve LineStrings dizinini dizinleyecek.

> [!NOTE]
> Veri türü olan bir kapsayıcıya **sıçrama Dingbox** ile bir dizin oluşturma ilkesi eklemeye çalışırsanız `geography` , başarısız olur. Bir sıçrama kutusu eklemeden önce kapsayıcının **Geospatialconfig** ' i değiştirmelisiniz `geometry` .  Veri ekleyebilir ve dizin oluşturma ilkenizin geri kalanını (yollar ve türler gibi), kapsayıcının Jeo uzamsal veri türünü seçmeden önce ya da sonra değiştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure Cosmos DB Jeo-uzamsal desteği kullanmaya nasıl başladığınıza öğrendiğinize göre şunları yapabilirsiniz:

* [Azure Cosmos db sorgu](sql-query-getting-started.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB uzamsal verileri sorgulama](sql-query-geospatial-query.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Jeo-uzamsal ve coğrafi JSON konumu verileri](sql-query-geospatial-intro.md) hakkında daha fazla bilgi edinin
