---
title: Azure Cosmos DB Jeo uzamsal ve GeoJSON konum verileri
description: Azure Cosmos DB ve SQL API ile uzamsal nesneler oluşturmayı anlayın.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: b20c72ae3ed8a8fffa02fc3a2c86f9f73ba2663b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692139"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Azure Cosmos DB Jeo uzamsal ve GeoJSON konum verileri
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makale, Azure Cosmos DB Jeo uzamsal işlevselliğe giriş niteliğindedir. Jeo-uzamsal dizin oluşturma hakkındaki belgelerimizi okuduktan sonra aşağıdaki soruları cevaplayabilirsiniz:

* Uzamsal verileri Azure Cosmos DB depolamak Nasıl yaparım??
* SQL ve LINQ 'te Azure Cosmos DB uzamsal verileri nasıl sorgulayabilir miyim?
* Azure Cosmos DB uzamsal dizin oluşturmayı etkinleştirmek veya devre dışı bırakmak Nasıl yaparım??

## <a name="spatial-data-use-cases"></a>Uzamsal veri kullanım örnekleri

Jeo-uzamsal veriler genellikle yakınlık sorguları içerir, örneğin "geçerli konumumun yakınında tüm kahve dükleleri bul". Yaygın kullanım örnekleri şunlardır:

* Belirli bulunan pazarlama girişimlerini yönlendiren coğrafi konum analizi.
* Perakende ve sağlık gibi birden çok sektörde konum tabanlı kişiselleştirme.
* Lojistik geliştirmesi, aktarım iyileştirmesi için.
* Özellikle sigorta ve finans şirketleri için risk analizi.
* Uyarılar ve bildirimler için durumsal tanıma.

## <a name="introduction-to-spatial-data"></a>Uzamsal verilere giriş

Uzamsal veriler, alan içindeki nesnelerin konumunu ve şeklini tanımlar. Çoğu uygulamada, bu, dünya ve jeo uzamsal verilerdeki nesnelere karşılık gelir. Uzamsal veriler, bir kişinin konumunu, ilgi çekici veya bir şehrin veya bir Gölü sınırının veya Gölü sınırının temsil edilebilmesi için kullanılabilir.

Azure Cosmos DB SQL API 'SI iki uzamsal veri türünü destekler: **geometri** veri türü ve **Coğrafya** veri türü.

- **Geometri** türü, bir Euclidean (düz) koordinat sistemindeki verileri temsil eder
- **Coğrafya** türü, bir yuvarlak dünya koordinat sistemindeki verileri temsil eder.

## <a name="supported-data-types"></a>Desteklenen veri türleri

Azure Cosmos DB [geojson belirtimi](https://tools.ietf.org/html/rfc7946)kullanılarak temsil edilen Jeo uzamsal nokta verilerinin dizinlenmesini ve sorgulanmasını destekler. GeoJSON veri yapıları her zaman geçerli JSON nesneleridir ve bu nedenle, herhangi bir özel araç veya kitaplık olmadan Azure Cosmos DB kullanılarak depolanabilir ve sorgulanırlar.

Azure Cosmos DB, aşağıdaki uzamsal veri türlerini destekler:

- Seçeneğinin
- LineString
- Poligon
- MultiPolygon

### <a name="points"></a>Noktalar

**Nokta** , alanda tek bir konum gösterir. Jeo-uzamsal verilerde bir nokta, bir Market Mağazası, bir bilgi noktası, bir otomobil veya bir şehirin açık adresi olabilecek tam konumu temsil eder.  Bir nokta, koordinat çiftini, boylam ve enlem kullanılarak GeoJSON (ve Azure Cosmos DB) olarak temsil edilir.

Aşağıda nokta için bir JSON örneği verilmiştir:

**Azure Cosmos DB noktaları**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Uzamsal veri türleri, konum verilerini içeren bir kullanıcı profili örneğinde gösterildiği gibi bir Azure Cosmos DB belgeye gömülebilir:

**Azure Cosmos DB depolanan konum ile profili kullan**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Geometri koordinat sistemindeki noktaları

**Geometri** veri türü Için, GeoJSON belirtimi ilk olarak yatay ekseni ve dikey eksen ikincisini belirtir.

### <a name="points-in-a-geography-coordinate-system"></a>Coğrafya koordinat sistemindeki noktaları

**Coğrafya** veri türü Için geojson belirtimi, önce boylam ve enlem Second 'u belirtir. Diğer eşleme uygulamalarında olduğu gibi, boylam ve enlem, derece cinsinden açılı ve temsil edilir. Boylam değerleri, ana meridyenlerden ölçülür ve-180 derece ile 180,0 derece arasındadır ve enlem değerleri eşlerden ölçülür ve-90,0 derece ile 90,0 derece arasındadır.

Azure Cosmos DB, WGS-84 başvuru sistemine göre gösterilen koordinatları yorumlar. Koordinat başvuru sistemleri hakkında daha fazla bilgi için aşağıya bakın.

### <a name="linestrings"></a>LineStrings

**LineStrings** , boşluk içinde iki veya daha fazla noktadan oluşan bir seriyi ve bunları bağlayan çizgi segmentlerini temsil eder. Jeo-uzamsal veriler ' de, LineStrings genellikle highor veya Rivers 'ı temsil etmek için kullanılır.

**GeoJSON 'da LineStrings**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Gen

**Çokgen** , kapalı bir LineString oluşturan bağlantılı noktaların bir sınırıdır. Çokgenler yaygın olarak, şehir ve eyalet gibi politik veya siyasi bir vergi gibi doğal formaları temsil etmek için kullanılır. Azure Cosmos DB bir çokgen örneği aşağıda verilmiştir:

**GeoJSON 'da çokgenler**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 32, -5 ],
        [ 32, -4.7 ],
        [ 31.8, -4.7 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON belirtimi, geçerli çokgenler için, kapatılan bir şekil oluşturmak için, belirtilen son koordinat çiftinin ilki ile aynı olması gerekir.
>
> Bir çokgen içindeki noktaların saat yönünde sıralı sırada belirtilmesi gerekir. Saat yönünde belirtilen bir çokgen, içindeki bölgenin tersini temsil eder.
>
>

### <a name="multipolygons"></a>MultiPolygon

Bir **MultiPolygon** sıfır veya daha fazla poligoluşan bir dizidir. **MultiPolygon** , kenarlarından çakışamaz veya herhangi bir ortak alana sahip olamaz. Bir veya daha fazla noktaya dokunabilir.

**GeoJSON içinde MultiPolygon**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Başvuru sistemlerini koordine et

Dünya şekli düzensiz olduğundan, coğrafi bölge Jeo uzamsal verilerinin koordinatları, her biri kendi başvuru ve ölçü birimleri ile birlikte çok sayıda koordinat başvuru sisteminde (yukarı) temsil edilir. Örneğin, "ABD Ulusal ızgarası", Birleşik Krallık için doğru bir başvuru sistemidir.

Günümüzde kullanımda olan en popüler dünyayı dünya çapındaki sistem  [WGS-84](https://earth-info.nga.mil/GandG/update/index.php)' dir. GPS cihazları ve Google Maps ve Bing Haritalar API 'Leri dahil olmak üzere birçok eşleme hizmeti WGS-84 kullanır. Azure Cosmos DB Coğrafya Jeo uzamsal verilerinin dizinlenmesini ve sorgulanmasını destekler ve yalnızca WGS-84 leri kullanılarak.

## <a name="creating-documents-with-spatial-data"></a>Uzamsal verilerle belge oluşturma
GeoJSON değerleri içeren belgeler oluşturduğunuzda, kapsayıcının dizin oluşturma ilkesine uygun olarak bir uzamsal dizin ile otomatik olarak dizinlenir. Python veya Node.js gibi dinamik olarak yazılmış bir dilde Azure Cosmos DB SDK 'Sı ile çalışıyorsanız, geçerli bir GeoJSON oluşturmanız gerekir.

**Node.jsJeo uzamsal verilerle belge oluşturma**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

SQL API 'leriyle çalışıyorsanız, `Point` `LineString` `Polygon` `MultiPolygon` `Microsoft.Azure.Cosmos.Spatial` uygulama nesnelerinize konum bilgilerini eklemek için ad alanı içinde,, ve sınıflarını kullanabilirsiniz. Bu sınıflar, uzamsal verilerin seri hale getirilmesi ve coğrafi serisini kaldırma işlemini basitleştirmeye yardımcı olur.

**.NET 'te Jeo-uzamsal verilerle belge oluşturma**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Enlem ve Boylam bilgisine sahip değilseniz, ancak şehir veya ülke/bölge gibi fiziksel adreslere veya konuma sahipseniz, Bing Haritalar REST hizmetleri gibi bir coğrafi kodlama hizmeti kullanarak gerçek koordinatları arayabilirsiniz. [Burada](/bingmaps/rest-services/)Bing Haritalar coğrafi kodlama hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure Cosmos DB Jeo-uzamsal desteği kullanmaya nasıl başladığınıza öğrendiğinize göre şunları yapabilirsiniz:

* [Azure Cosmos db sorgu](sql-query-getting-started.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB uzamsal verileri sorgulama](sql-query-geospatial-query.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Ile dizin uzamsal verileri](sql-query-geospatial-index.md) hakkında daha fazla bilgi edinin