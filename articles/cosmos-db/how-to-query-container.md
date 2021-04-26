---
title: Azure Cosmos DB'de kapsayıcıları sorgulama
description: Bölüm içi ve çapraz bölüm sorgularını kullanarak Azure Cosmos DB kapsayıcıları sorgulama hakkında bilgi edinin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 5cd90e994e620960e0d974ef7609a67f8a5eb58b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448568"
---
# <a name="query-an-azure-cosmos-container"></a>Azure Cosmos kapsayıcısını sorgulama
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Azure Cosmos DB bir kapsayıcının (koleksiyon, grafik veya tablo) nasıl sorgulanacağı açıklanmaktadır. Özellikle, Bölüm içi ve çapraz bölüm sorgularının Azure Cosmos DB içinde nasıl çalıştığını ele alır.

## <a name="in-partition-query"></a>Bölüm içi sorgu

Kapsayıcılardan veri sorguladığınızda, sorguda bir bölüm anahtarı filtresi belirtilmişse Azure Cosmos DB sorgu otomatik olarak optimize eder. Sorguyu, filtrede belirtilen bölüm anahtarı değerlerine karşılık gelen [fiziksel bölümlere](partitioning-overview.md#physical-partitions) yönlendirir.

Örneğin, aşağıdaki sorguyu üzerinde bir eşitlik filtresiyle göz önünde bulundurun `DeviceId` . Bu sorguyu üzerinde bölümlenen bir kapsayıcıda çalıştırırsanız `DeviceId` , bu sorgu tek bir fiziksel bölüme filtre uygulanır.

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Önceki örnekte olduğu gibi, bu sorgu da tek bir bölüme filtre de sağlar. Üzerinde ek filtre ekleme `Location` bunu değiştirmez:

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Bölüm anahtarında Aralık filtresi bulunan ve tek bir fiziksel bölüm kapsamına alınmayacak bir sorgu aşağıda verilmiştir. Bölüm içi bir sorgu olması için sorgunun, Bölüm anahtarını içeren bir eşitlik filtresi olması gerekir:

```sql
SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Bölümler arası sorgu

Aşağıdaki sorguda, Bölüm anahtarında () bir filtre yok `DeviceId` . Bu nedenle, her bir bölümün dizininde çalıştığı tüm fiziksel bölümlere karşı bir yelpamalıdır:

```sql
SELECT * FROM c WHERE c.Location = 'Seattle`
```

Her fiziksel bölümün kendi dizini vardır. Bu nedenle, bir kapsayıcıda çapraz bölümlü bir sorgu çalıştırdığınızda fiziksel bölüm *başına* bir sorgu verimli bir şekilde çalıştırıyorsunuz demektir. Azure Cosmos DB, sonuçları farklı fiziksel bölümler arasında otomatik olarak toplar.

Farklı fiziksel bölümlerdeki dizinler diğerinden bağımsızdır. Azure Cosmos DB genel dizin yok.

## <a name="parallel-cross-partition-query"></a>Paralel bölümler arası sorgu

Azure Cosmos DB SDK 'Ları 1.9.0 ve üzeri paralel sorgu yürütme seçeneklerini destekler. Paralel bölümler arası sorgular, düşük gecikme süreli bölümler arası sorgular yürütmenize olanak tanır.

Aşağıdaki parametreleri ayarlayarak paralel sorgu yürütme işlemini yönetebilirsiniz:

- **MaxConcurrency**: kapsayıcının bölümlerine en fazla eşzamanlı ağ bağlantısı sayısını ayarlar. Bu özelliği olarak ayarlarsanız `-1` , SDK paralellik derecesini yönetir.  `MaxConcurrency`Olarak ayarlanırsa `0` , kapsayıcının bölümlerine tek bir ağ bağlantısı vardır.

- **MaxBufferedItemCount**: Sorgu gecikme süresiyle istemci tarafı bellek kullanımı arasında denge kurar. Bu seçenek atlanırsa veya-1 ' e ayarlanırsa, SDK paralel sorgu yürütme sırasında arabelleğe alınan öğe sayısını yönetir.

Azure Cosmos DB, bölümler arası sorguları paralel hale getirmek özelliği nedeniyle, sistemin [fiziksel bölümler](partitioning-overview.md#physical-partitions)eklemesi sırasında sorgu gecikmesi genellikle iyi ölçeklenecektir. Ancak, toplam fiziksel bölüm sayısı arttıkça RU ücreti önemli ölçüde artar.

Bir çapraz bölüm sorgusu çalıştırdığınızda, aslında ayrı ayrı fiziksel bölüm başına ayrı bir sorgu oluşturursunuz. Çapraz bölümleme sorguları sorguları, varsa dizini kullanır, ancak bunlar, Bölüm içi sorgularda neredeyse verimli değildir.

## <a name="useful-example"></a>Yararlı örnek

Bölümler arası sorguları daha iyi anlamak için bir benzerleme vurguladı şunlardır:

Farklı grup oluşturma için paketler sunmaları gereken bir teslim sürücünüz olduğunu düşünelim. Her grup karmaşık, şirket içinde tüm yerleşik birim numaralarına sahip olan bir liste içerir. Her bir grubu bir fiziksel bölüm ve her bir liste için fiziksel bölümün diziniyle karşılaştırabiliriz.

Bu örneği kullanarak Bölüm içi ve çapraz bölüm sorgularını karşılaştırabiliriz:

### <a name="in-partition-query"></a>Bölüm içi sorgu

Teslim sürücüsü doğru grup karmaşık olduğunu biliyorsa (fiziksel bölüm), bu durumda doğru binaya hemen sürücü olabilir. Sürücü, yerleşik birim numaraları (Dizin) için Grup karmaınızın listesini denetleyebilir ve uygun paketleri hızla teslim edebilir. Bu durumda, sürücü, bir grup için karmaşık olan herhangi bir zamanda veya çabaya yol görmez.

### <a name="cross-partition-query-fan-out"></a>Bölümler arası sorgu (fan)

Teslim sürücüsü doğru grup karmaşık (fiziksel bölüm) ' i bilmiyor ise, her bir grup oluşturmaya yönelik olarak disk oluşturmak ve listeyi tüm yerleşik birim numaralarıyla (Dizin) kontrol etmek gerekir. Her apartman karmaşıkına ulaştıktan sonra, bunların her bir yerleşik adres listesini yine de kullanabiliyor. Bununla birlikte, herhangi bir paket alıcılarının da orada yoksa, her grup karmasın listesini denetmaları gerekir. Bu, çapraz bölüm sorgularının çalışma şekli olur. Dizini (Her kapıda gizlenmesi gerekmez) kullandıklarında, her fiziksel bölümün dizinini ayrı ayrı denetlemesi gerekir.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Çapraz bölüm sorgusu (yalnızca birkaç fiziksel bölüme kapsamlı)

Teslim sürücüsü, tüm paket alıcılarının belirli bir birkaç grup içinde canlı olduğunu biliyorsa, her biri için bir kez sürücü gerekmez. Birkaç grup oluşturma ile birlikte, yalnızca tek bir bina ziyaret etenden daha fazla iş gerektirecektir, teslim sürücüsü hala önemli bir zaman ve çaba tasarrufu sağlar. Bir sorgu, anahtar sözcüğü ile filtresinde bölüm anahtarına sahipse `IN` , yalnızca ilgili fiziksel bölümün veri dizinlerini kontrol eder.

## <a name="avoiding-cross-partition-queries"></a>Bölümler arası sorgulardan kaçınma

Çoğu kapsayıcı için bazı bölümler arası sorgulara sahip olursunuz. Bazı çapraz bölüm sorgularının olması Tamam! Neredeyse tüm sorgu işlemleri bölümler arasında desteklenir (mantıksal bölüm anahtarları ve fiziksel bölümler). Azure Cosmos DB Ayrıca, sorgu altyapısında ve istemci SDK 'lerinde, fiziksel bölümlerde sorgu yürütmeye paralel hale getirmek için birçok iyileştirme vardır.

Çoğu okuma ağır senaryosunda, sorgu filtrelerinizin en yaygın özelliğini seçmeniz önerilir. Ayrıca bölüm anahtarınızın diğer [bölüm anahtarı seçim en iyi uygulamalarına](partitioning-overview.md#choose-partitionkey)uyduğundan emin olun.

Çapraz bölüm sorgularının genellikle büyük kapsayıcılarla önemli olması önlenir. Fiziksel bölümde sorgu filtresiyle eşleşen hiçbir öğe olmasa bile, her bir fiziksel bölümün bir dizinini her denetleyişinizde en az 2,5 RU yaklaşık bir ücret ödersiniz. Bu nedenle, yalnızca bir (veya yalnızca birkaç) fiziksel bölümünüz varsa, çapraz bölüm sorguları Bölüm içi sorgulardan çok daha fazla RU 'yi tüketmez.

Fiziksel bölüm sayısı, sağlanan RU miktarına bağlıdır. Her bir fiziksel bölüm, sağlanan en fazla 10.000 RU 'ya izin verir ve en fazla 50 GB veri saklayabilir. Azure Cosmos DB fiziksel bölümleri sizin için otomatik olarak yönetecektir. Kapsayıcıdaki fiziksel bölüm sayısı, sağlanan aktarım hızına ve tüketilen depolamaya bağımlıdır.

İş yükünüz aşağıdaki ölçütleri karşılıyorsa, bölümler arası sorgulardan kaçınmaya çalışın:
- 30.000 RU 'ın üzerinde sağlanmış olduğunu planlayın
- 100 GB 'den fazla veri depolamayı planlayın

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB bölümlendirme hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
- [Azure Cosmos DB'de yapay bölümleme anahtarları](synthetic-partition-keys.md)
