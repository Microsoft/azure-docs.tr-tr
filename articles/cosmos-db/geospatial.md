---
title: Azure Cosmos DB SQL API hesabı, Jeo-uzamsal verilerle çalışma
description: Dizin oluşturma ve Azure Cosmos DB ve SQL API'si ile uzamsal nesnelerini sorgula öğrenin.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 1b26f78c6d44123ef1baa3c55fd16c3340d59dd4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616852"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB SQL API hesabı ile Jeo-uzamsal ve GeoJSON konum verileri kullanın

Bu makalede, Azure Cosmos DB Jeo-uzamsal işlevler için giriş niteliğindedir. Şu anda depolamak ve Jeo-uzamsal veri erişimi yalnızca Cosmos DB SQL API hesabı tarafından desteklenir. Bu makaleyi okuduktan sonra aşağıdaki soruları yanıtlamak mümkün olacaktır:

* Azure Cosmos DB'de nasıl uzamsal veri depoluyor?
* Azure Cosmos DB'de SQL ve LINQ Jeo-uzamsal verileri nasıl sorgulama yapabilirsiniz?
* Nasıl etkinleştirmek veya Azure Cosmos DB'de uzamsal dizin oluşturmayı devre dışı?

Bu makalede, SQL API'si ile uzamsal veri ile nasıl çalışılacağını gösterir. Bkz. Bu [GitHub projesini](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) kod örnekleri için.

## <a name="introduction-to-spatial-data"></a>Uzamsal veri giriş
Uzamsal veri şekli alan nesne ve konumunu açıklar. Çoğu uygulamada bunlar dünya ve Jeo-uzamsal veriler üzerinde nesnelere karşılık gelir. Uzamsal veriler, bir kişi, yer ilgi veya bir şehir veya bir gölü sınırını konumunu temsil etmek için kullanılabilir. Ortak kullanım durumları genellikle yakınlık sorguları, örneğin içeren, "tüm kahve dükkanları bulma geçerli konumunuzu." 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB, dizin oluşturma ve kullanma temsil Jeo-uzamsal noktası verileri Sorgulama destekler [GeoJSON belirtimi](https://tools.ietf.org/html/rfc7946). Depolanabilir ve herhangi bir özel araçları veya kitaplıkları olmadan Azure Cosmos DB kullanarak sorgulanan GeoJSON veri yapıları her zaman geçerli JSON nesnesi olur. Azure Cosmos DB SDK'ları yardımcı sınıflar ve kolayca uzamsal verilerle çalışmak yöntemler sağlar. 

### <a name="points-linestrings-and-polygons"></a>Noktaları, LineStrings ve çokgenler
A **noktası** alanındaki tek bir konumu belirtir. Jeo-uzamsal verileri, adres Market, bilgi noktası, bir otomobilin ya da bir şehir olabilir tam konumu bir noktayı temsil eder.  Bir noktayı kullanarak kendi koordinat GeoJSON (ve Azure Cosmos DB) çifti veya boylam ve enlem temsil edilir. JSON'noktasına yönelik bir örnek aşağıda verilmiştir.

**Azure cosmos DB'de noktaları**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> GeoJSON belirtimi boylam belirtir. ilk ve enlem ikinci. Gibi diğer eşleme uygulamalardaki boylam ve enlem açıları olan ve derece cinsinden temsil. Boylam değerleri asal Meridyen ölçülür ve -180 derece ve 180.0 derece arasında ve enlem değerleri ekvatorun ölçülür ve 90.0 derece-90.0 derece arasında. 
> 
> Azure Cosmos DB koordinatları 84 WGS başvuru sistemi temsil olarak yorumlar. Koordinat başvuru sistemleri hakkında daha fazla ayrıntı için aşağıya bakın.
> 
> 

Bu bir Azure Cosmos DB belgesinde konum verileri içeren bir kullanıcı profili, bu örnekte gösterildiği gibi eklenebilir:

**Azure Cosmos DB'de depolanan konumu ile profili kullanma**

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

Noktalarına ek olarak, GeoJSON LineStrings ve çokgenler destekler. **LineStrings** iki veya daha fazla puan alan ve bunları bağlayın çizgi parçaları bir dizi temsil eder. Jeo-uzamsal verileri LineStrings Otoyollar veya rivers temsil etmek için yaygın olarak kullanılır. A **Çokgen** kapalı LineString forms bir sınır noktalarının bağlı olduğu. Çokgen lakes gibi doğal formations veya siyasi işletmek zorundayız Şehir ve durumlar gibi temsil etmek için yaygın olarak kullanılır. Bir çokgenin Azure Cosmos DB'de örneği aşağıda verilmiştir. 

**GeoJSON'daki çokgenler**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON belirtimi geçerli çokgenler için sağlanan son koordinat çifti kapalı şekli oluşturmak için birinci ile aynı olması gerekir.
> 
> İçinde bir Çokgen noktalarının saat yönünün tersi düzende belirtilmesi gerekir. Bir çokgenin belirtilen saat yönünde sırayla bölgesinde tersini temsil eder.
> 
> 

GeoJSON Point, LineString ve Çokgen yanı sıra, ayrıca isteğe bağlı özellikler coğrafi konum ilişkilendirmek nasıl yanı sıra birden çok Jeo-uzamsal konumlar grubuna nasıl için gösterimi belirtir. bir **özellik**. Bu nesnelerin geçerli JSON olduğundan, bunlar tüm depolanabilir ve Azure Cosmos DB'de işlenebilir. Ancak Azure Cosmos DB yalnızca otomatik dizin oluşturma noktalarını destekler.

### <a name="coordinate-reference-systems"></a>Başvuru koordinat sistemi
Dünya şeklini düzensiz olduğundan, Jeo-uzamsal veriler koordinatlarını birçok koordinat başvuru sistemi (CRS), her biri kendi çerçeveler başvuru ve ölçü temsil edilir. Örneğin, "ulusal kılavuz, Britanya" başvuru sistemi doğru ise, Birleşik Krallık ancak dışında bu. 

Kullanılan en popüler CRS bugün dünya Geodetic sistemidir [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). GPS cihazların ve Google Haritalar ve Bing haritaları API'si dahil olmak üzere birçok eşleme hizmetlerin WGS 84 kullanın. Azure Cosmos DB dizinleme ve Jeo-uzamsal verileri yalnızca WGS 84 CRS kullanarak sorgulama destekler. 

## <a name="creating-documents-with-spatial-data"></a>Uzamsal veri ile belge oluşturma
GeoJSON değerleri içeren belgeleri oluşturduğunuzda, bunlar olan bir uzamsal dizin kapsayıcısının dizin oluşturma ilkesini anlaşmalara uygun şekilde otomatik olarak dizine eklenir. Bir Azure Cosmos DB SDK, Python veya Node.js gibi dinamik olarak yazılan bir dilde ile çalışıyorsanız, geçerli GeoJSON oluşturmanız gerekir.

**Node.js'de Jeo-uzamsal veri ile belge oluşturma**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

SQL API ile çalışıyorsanız, kullanabileceğiniz `Point` ve `Polygon` içindeki sınıfları `Microsoft.Azure.Documents.Spatial` uygulaması nesnelerinizi içindeki konum bilgilerini eklemek için ad alanı. Bu sınıfların serileştirme ve seri durumundan çıkarma uzamsal veri GeoJSON içine basitleştirmeye yardımcı olur.

**. NET'te Jeo-uzamsal veri ile belge oluşturma**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Enlem ve Boylam bilgisine sahip değilseniz, ancak şehir veya ülke/bölge gibi fiziksel adreslere veya konuma sahipseniz, Bing Haritalar REST hizmetleri gibi bir coğrafi kodlama hizmeti kullanarak gerçek koordinatları arayabilirsiniz. Bing Haritalar ile coğrafi kodlama hakkında daha fazla bilgi [burada](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Uzamsal türler sorgulanıyor
Biz Jeo-uzamsal veriler ekleme işlemini Anlamadıysanız ayırdıktan sonra SQL ve LINQ kullanarak Azure Cosmos DB kullanarak bu verileri sorgulamak nasıl bir göz atalım.

### <a name="spatial-sql-built-in-functions"></a>Uzamsal SQL yerleşik işlevler
Azure Cosmos DB, Jeo-uzamsal sorgulamak için aşağıdaki açık Jeo-uzamsal Consortium (OGC) yerleşik işlevleri destekler. Tam bir set SQL dilinde yerleşik işlevler hakkında daha fazla bilgi için bkz. [Azure Cosmos DB'yi sorgulama](how-to-sql-query.md).

|**Kullanım**|**Açıklama**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | İki GeoJSON noktası, çokgen veya LineString ifadeler uzaklığı döndürür.|
|ST_WITHIN (spatial_expr, spatial_expr) | İlk GeoJSON nesne (noktası, çokgen veya LineString) ikinci GeoJSON nesne içinde (noktası, çokgen veya LineString) olup olmadığını gösteren bir Boole ifadesi döndürür.|
|ST_INTERSECTS (spatial_expr, spatial_expr)| İki belirtilen GeoJSON nesnesi (noktası, çokgen veya LineString) kesişen olup olmadığını belirten bir Boole ifadesi döndürür.|
|ST_ISVALID| Belirtilen GeoJSON noktası, çokgen veya LineString ifade geçerli olup olmadığını gösteren bir Boole değeri döndürür.|
| ST_ISVALIDDETAILED| Bir Boole değeri içeren bir JSON değeri, belirtilen GeoJSON noktası, çokgen veya LineString ifade geçerliyse ve geçersiz değeri döndürür, ayrıca bir dize değeri olarak nedeni.|

Uzamsal İşlevler, uzamsal veri yakınlık sorguları gerçekleştirmek için kullanılabilir. Örneğin, ST_DISTANCE yerleşik işlevi kullanarak belirtilen konumun içinde 30 KM ailesi tüm belgeleri döndüren bir sorgu aşağıdadır. 

**Sorgu**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Sonuçlar**

    [{
      "id": "WakefieldFamily"
    }]

Uzamsal dizin oluşturma ilkenizi dizin oluşturma dahil, "uzaklık sorguları" verimli bir şekilde dizini alabilecektir. Uzamsal dizin oluşturma ile ilgili daha fazla bilgi için aşağıdaki bölümüne bakın. Bir uzamsal dizin için belirtilen yollara sahip değilseniz, hala uzamsal sorgular belirterek gerçekleştirebilirsiniz `x-ms-documentdb-query-enable-scan` istek üst bilgisi "true" değeri olarak ayarlanmış. . NET'te, bu isteğe bağlı olarak geçirerek yapılabilir **FeedOptions** sorgularla bağımsız değişkeni [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) true olarak ayarlanmış. 

ST_WITHIN bir noktası Çokgen içinde kaynaklandığını, kontrol etmek için kullanılabilir. Yaygın olarak çokgenler, posta kodları, durumu sınırları veya doğal formations gibi sınırlarını temsil etmek için kullanılır. Uzamsal dizin oluşturma ilkenizi dizin oluşturma dahil, yeniden ardından "içinde" sorguları verimli bir şekilde dizini alabilecektir. 

ST_WITHIN Çokgen bağımsız değişkenleri yalnızca tek bir halka içerebilir, diğer bir deyişle, çokgenler bunlara boşluklar içermemelidir. 

**Sorgu**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Sonuçlar**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Konum değeri ya da hatalı biçimlendirilmiş veya geçersiz bağımsız değişken, ardından olarak değerlendirilen belirtilen varsa Azure Cosmos DB sorgusu nasıl eşleşmeyen türler işlerinde benzer **tanımlanmamış** ve değerlendirilen belgenin sorgu sonuçlarından atlanacak. Sorgunuzu hiçbir sonuç döndürmezse, hata ayıklama ST_ISVALIDDETAILED için uzamsal türü neden geçersiz çalıştırın.     
> 
> 

Azure Cosmos DB ayrıca Ters sorgular gerçekleştirdiğini destekler, diğer bir deyişle, çokgenler veya Azure Cosmos DB'de satırları dizin sonra belirli bir noktaya içeren alanlar için sorgu. Bu Düzen yaygın olarak Lojistik bir kamyon girdiğinde veya belirlenen bir alan bırakır, örneğin, tanımlamak için kullanılır. 

**Sorgu**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Sonuçlar**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID ve ST_ISVALIDDETAILED uzamsal nesne geçerli olup olmadığını denetlemek için kullanılabilir. Örneğin, aşağıdaki sorguyu dışı aralık enlem değeri (-132.8) ile bir noktası geçerliliğini denetler. ST_ISVALID yalnızca bir Boole değeri döndürür ve ST_ISVALIDDETAILED Boolean ve neden geçersiz değerlendirilir nedeni içeren bir dize döndürür.

**Sorgu**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Sonuçlar**

    [{
      "$1": false
    }]

Bu işlevler, çokgenler doğrulamak için de kullanılabilir. Örneğin, burada ST_ISVALIDDETAILED kapalı bir Çokgen doğrulamak için kullanırız. 

**Sorgu**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Sonuçlar**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ içinde .NET SDK'sı sorgulanıyor
SQL .NET SDK'sını da sağlayıcıları yöntem Saplaması `Distance()` ve `Within()` LINQ ifadeler içinde kullanmak için. Bu yöntem SQL LINQ sağlayıcısı çevirir eşdeğer SQL yerleşik işlev çağrıları çağrı (ST_DISTANCE ve ST_WITHIN sırasıyla). 

Aşağıda, "location" değeri, LINQ kullanarak belirtilen noktanın bir yarıçapı içinde olan Azure Cosmos kapsayıcısındaki tüm belgeleri bulan LINQ sorgusunun bir örneği verilmiştir.

**LINQ sorgusu için uzaklık**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Benzer şekilde, belirtilen kutusu/Çokgen içinde "Konum" olan belgeleri bulmak için bir sorgu aşağıdadır. 

**İçinde LINQ sorgusu için**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Biz LINQ ve SQL kullanarak belgeleri sorgulama işlemini Anlamadıysanız ayırdıktan sonra Azure Cosmos DB, uzamsal dizin oluşturma için yapılandırmak nasıl bir göz atalım.

## <a name="indexing"></a>Dizinleme
İçinde açıklandığı [belirsiz şema dizinini Azure Cosmos DB ile](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) kağıt tasarladığımız Azure Cosmos DB'nin Veritabanı Altyapısı'olacak şekilde gerçekten şemadan ve JSON için birinci sınıf destek sağlar. Azure Cosmos DB, yazma için iyileştirilmiş veritabanı altyapısı GeoJSON standart olarak temsil edilen uzamsal veriler (noktaları, çokgenler ve satırları) yerel olarak anlıyor.

Buna koysalar geometri öngörülen günlerdeki 2B düzlemine üzerine geodetic koordinatları sonra kullanarak hücrelere aşamalı olarak bölünmüş bir **quadtree**. Bu hücre içinde hücrenin konumuna bağlı olarak 1b eşlenir bir **Hilbert boşluk doldurma eğri**, yerleşim yeri noktalarının korur. Konum verileri dizine, ayrıca, bilinen bir işlemle gerekmesi **Mozaik döşeme**, diğer bir deyişle, bir konuma kesişen tüm hücreler tanımlanır ve Azure Cosmos DB dizini anahtar olarak depolanır. Sorgu zamanında noktaları ve Çokgenleri gibi bağımsız değişkenleri ayrıca ilgili hücre kimliği aralıkları ayıklamak için grubun Mozaik ve dizinden veri almak için kullanılır.

Uzamsal dizin içeren bir dizin oluşturma ilkesini belirtirseniz, / * (tüm yolları), sonra koleksiyon içinde bulunan tüm noktalarını uzamsal için etkili sorgular (ST_WITHIN ve ST_DISTANCE) dizinlenir. Uzaysal dizinler değil duyarlık değeri vardır ve her zaman varsayılan duyarlık değeri kullanır.

> [!NOTE]
> Azure Cosmos DB, noktaları, çokgenler ve LineStrings otomatik dizin oluşturma destekler.
> 
> 

Aşağıdaki JSON kod parçacığında, uzamsal, diğer bir deyişle dizin oluşturma özelliği etkinleştirilmiş olan bir dizin oluşturma ilkesini gösterir, uzamsal sorgulama için belgeler içinde bulunan herhangi bir GeoJSON noktası dizini. Azure portalını kullanarak bir dizin oluşturma ilkesini değiştiriyorsanız, koleksiyon üzerinde dizinleme uzamsal etkinleştirmek için aşağıdaki JSON için dizin oluşturma ilkesini belirtebilirsiniz.

**Koleksiyon dizin oluşturma ilkesi JSON ile uzamsal noktaları ve Çokgenleri için etkin**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Bu noktaları içeren tüm yollar için açık uzamsal dizin ile bir koleksiyon oluşturma işlemi gösterilmektedir. NET'te bir kod parçacığı aşağıdadır. 

**Uzamsal dizin ile bir koleksiyon oluşturun**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Ve İşte bir mevcut koleksiyonu içindeki belgeler depolanan tüm noktaları üzerinden uzamsal dizin oluşturma avantajından yararlanmak için nasıl değiştirebilirsiniz.

**Uzamsal dizin ile bir mevcut koleksiyonu değiştirmek**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> ' % S'konum GeoJSON değeri belge içinde hatalı biçimlendirilmiş veya geçersiz ise, ardından uzamsal sorgulama için dizine değil. Konum değerleri ST_ISVALID ve ST_ISVALIDDETAILED kullanarak doğrulayabilirsiniz.
> 
> Koleksiyon tanımınız bir bölüm anahtarı içeriyorsa, dönüştürme ilerleme dizin bildirilmedi. 
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Azure Cosmos DB'de Jeo-uzamsal destek kullanmaya başlamak öğrendiniz, sonra şunları yapabilirsiniz:

* İle kodlamaya başlayın [github'da Jeo-uzamsal .NET kodu örnekleri](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Jeo-uzamsal, sorgulama ile yaşayın [Azure Cosmos DB sorgu oyun alanı](https://www.documentdb.com/sql/demo#geospatial)
* Daha fazla bilgi edinin [Azure Cosmos DB sorgusu](how-to-sql-query.md)
* Daha fazla bilgi edinin [Azure Cosmos DB dizinleme ilkeleri](index-policy.md)

