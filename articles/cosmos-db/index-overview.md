---
title: Azure Cosmos DB 'de dizin oluşturma
description: Azure Cosmos DB ' de dizin oluşturmanın nasıl çalıştığını anlayın.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: d679208914eb7d1f74bfaec77fbcff196909a2f4
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299790"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB Dizin oluşturma-genel bakış

Azure Cosmos DB, şema veya dizin yönetimiyle uğraşmak zorunda kalmadan uygulamanızda yineleme yapmanızı sağlayan şemadan bağımsız bir veritabanıdır. Varsayılan olarak, Azure Cosmos DB herhangi bir şemayı tanımlamaya veya ikincil dizinleri yapılandırmaya gerek kalmadan, [kapsayıcıdaki](databases-containers-items.md#azure-cosmos-containers) tüm öğeler için her özelliği otomatik olarak dizine ekler.

Bu makalenin amacı, Azure Cosmos DB verileri nasıl dizinleyen ve sorgu performansını artırmak için dizinleri nasıl kullandığını açıklamaktır. [Dizin oluşturma ilkelerinin](index-policy.md)nasıl özelleştirileceğine ilişkin araştırmadan önce bu bölümden gitmeniz önerilir.

## <a name="from-items-to-trees"></a>Öğelerden ağaçlara

Bir öğe kapsayıcıda her depolandığında, içeriği bir JSON belgesi olarak yansıtıldıktan sonra ağaç gösterimine dönüştürülür. Bu, söz konusu öğenin her özelliğinin bir ağaçta düğüm olarak temsil edildiği anlamına gelir. Sözde kök düğüm, öğenin tüm ilk düzey özelliklerine üst öğe olarak oluşturulur. Yaprak düğümleri bir öğe tarafından taşınan gerçek skaler değerleri içerir.

Örnek olarak, şu öğeyi göz önünde bulundurun:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Aşağıdaki ağaç tarafından temsil edilir:

![Ağaç olarak temsil edilen önceki öğe](./media/index-overview/item-as-tree.png)

Dizilerin ağaçta nasıl kodlandığını unutmayın: dizideki her giriş dizideki bu girdinin diziniyle etiketlenmiş bir ara düğüm alır (0, 1 vb.).

## <a name="from-trees-to-property-paths"></a>Ağaçlardan Özellik yollarına

Azure Cosmos DB öğeleri ağaçlara dönüştürmesinin nedeni, özelliklerin bu ağaçlar içindeki yollarıyla başvurulmasını sağlar. Bir özelliğin yolunu almak için, ağacı kök düğümden bu özelliğe çapraz geçiş yapabilir ve her bir çapraz düğüm etiketini birleştirebilirsiniz.

Yukarıda açıklanan örnek öğeden her bir özelliğin yolları aşağıda verilmiştir:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Bir öğe yazıldığında, Azure Cosmos DB her bir özelliğin yolunu ve karşılık gelen değerini etkin bir şekilde dizine ekler.

## <a name="index-kinds"></a>Dizin türleri

Azure Cosmos DB Şu anda üç tür dizini desteklemektedir.

### <a name="range-index"></a>Aralık dizini

**Aralık** dizini sıralı ağaç benzeri yapıyı temel alır. Aralık Dizin türü için kullanılır:

- Eşitlik sorguları:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Bir dizi öğesinde eşitlik eşleşmesi
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Aralık sorguları:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (@no__t için geçerlidir-0, `<`, `>=`, `<=`, `!=`)

- Bir özelliğin varlığı denetleniyor:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Dize öneki eşleşiyor (CONTAINS anahtar sözcüğü Aralık dizininden yararlanamaz):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY` sorguları:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` sorguları:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Aralık dizinleri, skaler değerlerde (dize veya sayı) kullanılabilir.

### <a name="spatial-index"></a>Uzamsal dizin

**Uzamsal** dizinler,-Points, çizgiler, çokgenler ve MultiPolygon gibi Jeo-uzamsal nesnelerde verimli sorgular sağlar. Bu sorgular ST_DISTANCE, ST_WITHIN, ST_INTERSECTS anahtar sözcüklerini kullanır. Aşağıda, uzamsal dizin türü kullanan bazı örnekler verilmiştir:

- Jeo-uzamsal uzaklık sorguları:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Sorgular içindeki Jeo uzamsal:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Jeo-uzamsal Kesiştirme sorguları:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Uzamsal dizinler, doğru biçimli [geojson](geospatial.md) nesnelerinde kullanılabilir. Noktaları, LineStrings, çokgenler ve MultiPolygon Şu anda desteklenmektedir.

### <a name="composite-indexes"></a>Bileşik dizinler

**Bileşik** dizinler, birden çok alanda işlem gerçekleştirirken verimliliği artırır. Bileşik dizin türü için kullanılır:

- birden çok özelliklerde `ORDER BY` sorgular:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Filtre ve `ORDER BY` olan sorgular. Filter özelliği `ORDER BY` yan tümcesine eklenirse, bu sorgular bileşik bir dizin kullanabilir.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- En az bir özelliğin eşitlik filtresi olduğu iki veya daha fazla özelliğe filtre içeren sorgular

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Bir filtre koşulunun Dizin türünde kullanıldığı sürece sorgu altyapısı, kalanı taramadan önce bunu değerlendirir. Örneğin, @no__t gibi bir SQL sorgunuz varsa-0

* Yukarıdaki sorgu önce dizin kullanılarak firstName = "Andrew" olan girdileri filtreleyecek. Ardından, CONTAINS filtre koşulunu değerlendirmek için firstName = "Andrew" girdilerini sonraki bir işlem hattı aracılığıyla geçirin.

* Dizini kullanan ek filtre koşulları ekleyerek dizini (ör. IÇERIR) kullanmayan işlevleri kullanırken, sorguları hızlandırabilir ve tam kapsayıcı taramalarından kaçınabilirsiniz. Filter yan tümceleri sırası önemli değildir. Sorgu altyapısı, hangi koşulların daha seçmeli olduğunu anlayabilir ve sorguyu uygun şekilde çalıştıracaktır.


## <a name="querying-with-indexes"></a>Dizinlerle sorgulama

Verileri dizinlerken ayıklanan yollar, bir sorgu işlenirken dizinde arama yapmayı kolaylaştırır. Dizinli yolların listesini içeren bir sorgunun `WHERE` yan tümcesini eşleştirerek sorgu koşulu ile eşleşen öğeleri çok hızlı bir şekilde tanımlamak mümkündür.

Örneğin, aşağıdaki sorguyu göz önünde bulundurun: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Sorgu koşulu (herhangi bir konumda ülke olarak "Fransa" bulunduğu öğeler üzerinde filtreleme) aşağıdaki kırmızı renkle eşleşen yol ile eşleşir:

![Ağaç içindeki belirli bir yolu eşleştirme](./media/index-overview/matching-path.png)

> [!NOTE]
> Tek bir özelliğe göre siparişlerin *her zaman* bir Aralık dizinine ihtiyacı olan `ORDER BY` yan tümcesi, başvurduğu yolun bir tane yoksa başarısız olur. Benzer şekilde, birden çok özelliğe göre siparişlerin *her zaman* bir bileşik dizine ihtiyacı olan `ORDER BY` sorgusu.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde dizin oluşturma hakkında daha fazla bilgi edinin:

- [Dizin oluşturma ilkesi](index-policy.md)
- [Dizin oluşturma ilkesini yönetme](how-to-manage-indexing-policy.md)
