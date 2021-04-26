---
title: Azure Cosmos DB isteklerinizin maliyetini en iyi duruma getirme
description: Bu makalede, Azure Cosmos DB istek verirken maliyetlerin nasıl iyileştirileceği açıklanır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 36ecef007e10f9a090dbabc8b5a91fd473930141
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102633861"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Azure Cosmos DB istek maliyetini iyileştirme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bu makalede okuma ve yazma isteklerinin [Istek birimlerine](request-units.md) nasıl çevireceği ve bu isteklerin maliyetinin nasıl iyileştirileceği açıklanır. Okuma işlemleri, nokta okumaları ve sorgular içerir. Yazma işlemleri, öğelerin INSERT, Replace, DELETE ve upsert öğelerini içerir.

Azure Cosmos DB, bir kapsayıcı içindeki öğelerde çalışan zengin bir veritabanı işlemleri kümesi sunar. Bu işlemlerden her biriyle ilişkilendirilmiş maliyet, işlemi tamamlamak için gereken CPU, GÇ ve belleğe göre değişiklik gösterir. Donanım kaynaklarını düşünmek ve yönetmek yerine bir istek birimi (RU), bir isteğe yönelik çeşitli veritabanı işlemlerini gerçekleştirmek için gereken kaynaklar için tek bir ölçü olarak düşünebilirsiniz.

## <a name="measuring-the-ru-charge-of-a-request"></a>Bir isteğin RU ücretlendirmesini ölçme

Gerçek maliyetlerini anlamak ve ayrıca iyileştirmelerin verimliliğini değerlendirmek için isteklerinizin RU ücretini ölçmek önemlidir. Bu maliyeti Azure portal kullanarak veya SDK 'lardan biri aracılığıyla Azure Cosmos DB geri gönderilen yanıtı inceleyerek getirebilirsiniz. Bunun nasıl elde edilebileceği hakkında ayrıntılı yönergeler için bkz. [istek birimi ücreti bulma Azure Cosmos DB](find-request-unit-charge.md) .

## <a name="reading-data-point-reads-and-queries"></a>Veri okuma: nokta okuma ve sorguları

Azure Cosmos DB içindeki okuma işlemleri, genellikle aşağıdaki gibi, RU tüketimine göre daha hızlı/en verimli ve daha az verimlidir.  

* Nokta okuma (tek öğe KIMLIĞINDE ve bölüm anahtarında anahtar/değer araması).
* Tek bir bölüm anahtarı içinde bir filtre yan tümcesi ile sorgulayın.
* Herhangi bir özellikte eşitlik veya Aralık filtresi yan tümcesi olmadan sorgu.
* Filtre olmadan sorgulayın.

### <a name="role-of-the-consistency-level"></a>Tutarlılık düzeyinin rolü

**Güçlü** veya **sınırlanmış stalet** [tutarlılık düzeylerini](consistency-levels.md)kullanırken, herhangi bir okuma IŞLEMININ (okuma veya sorgulama) ru maliyeti iki katına çıkar.

### <a name="point-reads"></a>Nokta okuma

Bir nokta okuma (kullanılan tutarlılık düzeyinin yanı sıra) için RU ücreti etkileyen tek etken, alınan öğenin boyutudur. Aşağıdaki tabloda, boyutu 1 KB ve 100 KB olan öğeler için, nokta okumalarının RU maliyeti gösterilmektedir.

| **Öğe boyutu** | **Bir noktanın okunan maliyeti** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RU |

Nokta okuma (öğe KIMLIĞINDE anahtar/değer aramaları) en etkili okuma türü olduğundan, mümkün olduğunda öğe KIMLIĞINIZIN anlamlı bir değere sahip olduğundan emin olmanız gerekir. bu sayede öğelerinizi bir nokta okuma (sorgu yerine) ile getirebilirsiniz.

### <a name="queries"></a>Sorgular

Sorgular için istek birimleri, bir dizi etkene bağımlıdır. Örneğin, yüklenen/döndürülen Azure Cosmos öğelerinin sayısı, dizinde yapılan arama sayısı, sorgu derleme süresi vb. ayrıntılar. Azure Cosmos DB aynı verilerde yürütüldüğü sırada aynı sorgunun her zaman aynı sayıda istek birimi de tekrarlayarak yineleme yürütmelerinin aynısını kullanmasını güvence altına alır. Sorgu yürütme ölçümlerini kullanan sorgu profili, istek birimlerinin nasıl harcandığına ilişkin iyi bir fikir verir.  

Bazı durumlarda, sorgular kullanılabilir ru 'yı temel alarak mümkün olduğunca hızlı çalışacağı için bir dizi 200 ve 429 yanıt ve çok sayıda sorgu üzerinde değişken istek birimleri görebilirsiniz. Sunucu ve istemci arasında birden fazla sayfaya/gidiş dönüşe bir sorgu yürütme kesmesi görebilirsiniz. Örneğin, 10.000 öğe, her biri bu sayfada gerçekleştirilen hesaplamayı temel alarak ücretlendirilen birden çok sayfa olarak döndürülebilir. Bu sayfalar arasında toplama yaptığınızda, tüm sorgu için alacağınız aynı ru sayısını almalısınız.

#### <a name="metrics-for-troubleshooting-queries"></a>Sorgu sorunlarını giderme ölçümleri

Sorgular (Kullanıcı tanımlı işlevler dahil) tarafından tüketilen performans ve aktarım hızı genellikle işlev gövdesine bağımlıdır. Sorgu yürütmesinin UDF 'de ne kadar zaman harcandığını ve tüketilen saat sayısını bulmanın en kolay yolu, sorgu ölçümlerini etkinleştirir. .NET SDK kullanıyorsanız, SDK tarafından döndürülen örnek sorgu ölçümleri aşağıda verilmiştir:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>Sorguları iyileştirmek için en iyi uygulamalar 

Aşağıdaki en iyi yöntemleri maliyet için iyileştirirken göz önünde bulundurun:

* **Birden çok varlık türünü birlikte bulundurma**

   Birden çok varlık türünü tek veya daha küçük bir kapsayıcı içinde bulundurmanıza çalışın. Bu yöntem, yalnızca bir fiyatlandırma perspektifinden değil, sorgu yürütme ve işlemler için de avantaj verir. Sorgular tek bir kapsayıcıya kapsamlandırılır; saklı yordamlar/Tetikleyiciler aracılığıyla birden çok kayıt üzerinde Atomik işlemler, tek bir kapsayıcı içindeki bir bölüm anahtarının kapsamına alınır. Aynı kapsayıcı içindeki varlıkları birlikte bulundurma, kayıtlar arasındaki ilişkileri çözümlemek için ağ gidiş dönüş sayısını azaltabilir. Bu nedenle, uçtan uca performansı artırarak, daha büyük bir veri kümesi için birden çok kayıt üzerinde atomik işlemleri sağlar ve sonuç olarak maliyetleri düşürür. Tek veya daha küçük bir kapsayıcı içinde birden çok varlık türünü birlikte bulundurma senaryosunda, genellikle var olan bir uygulamayı geçiriyorsanız ve herhangi bir kod değişikliği yapmak istemediğiniz için, daha sonra veritabanı düzeyinde sağlama üretilen işi göz önünde bulundurmanız gerekir.  

* **Düşük istek birimleri/ikinci kullanım için ölçme ve ayarlama**

   Bir sorgunun karmaşıklığı, bir işlem için kaç tane istek birimi (ru) tüketildiğini etkiler. Koşulların sayısı, koşulların doğası, UDF sayısı ve kaynak veri kümesinin boyutu. Tüm bu faktörler, sorgu işlemlerinin maliyetini etkiler. 

Azure Cosmos DB, sağlanan aktarım hızı modeli kullanılarak üretilen iş ve gecikme açısından öngörülebilir bir performans sağlar. Sağlanan aktarım hızı, saniye başına [Istek birimi](request-units.md) veya ru/sn cinsinden temsil edilir. Istek birimi (RU), bir isteği gerçekleştirmek için gerekli olan CPU, bellek, GÇ vb. işlem kaynakları üzerinde mantıksal bir soyutlamadır. Sağlanan aktarım hızı (ru), öngörülebilir aktarım hızı ve gecikme süresi sağlamak için kapsayıcı veya veritabanınıza ayrılır. Sağlanan aktarım hızı, Azure Cosmos DB her ölçekte öngörülebilir ve tutarlı performans, garantili düşük gecikme süresi ve yüksek kullanılabilirlik sağlamasına olanak sağlar. İstek birimleri, bir uygulamanın ihtiyacı olan kaynak sayısını kolaylaştıran normalleştirilmiş para birimini temsil eder.

İstek üstbilgisinde döndürülen istek ücreti, belirli bir sorgunun maliyetini gösterir. Örneğin, bir sorgu 1000 1 KB 'lik öğeler döndürürse, işlemin maliyeti 1000 ' dir. Bu nedenle, bir saniye içinde sunucu, sonraki istekleri sınırlayan orandan önce yalnızca iki istek için geçerlidir. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md) makalesi ve istek birimi hesaplayıcısı.

## <a name="writing-data"></a>Veri yazma

Bir öğe yazma ile ilgili RU maliyeti şunlara bağlıdır:

- Öğe boyutu.
- [Dizin oluşturma ilkesinin](index-policy.md) kapsadığı ve dizine alınması gereken özellik sayısı.

1 KB 'lık bir öğe, maliyetleri dizinlemeden ~ 5,5 ru etrafında ekleniyor. Aynı öğeyi eklemek için gereken ücret, bir öğe maliyetlerini iki kez değiştirme.

### <a name="optimizing-writes"></a>Yazmaları iyileştirme

Yazma işlemlerinin RU maliyetini en iyi hale getirmenin en iyi yolu, öğelerinizi ve dizine alınan özelliklerin sayısını doğru olarak boyutlandırmanız.

- Azure Cosmos DB çok büyük öğelerin depolanması, yüksek RU ücretlerine neden olur ve bir anti-model olarak kabul edilebilir. Özellikle, ikili içeriği veya üzerinde sorgu yapmanız gerekmeyen büyük parça metin öbeklerini saklamayın. En iyi uygulama, bu tür verilerin [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md) alanına yerleştirilme ve Azure Cosmos DB yazdığınız öğedeki bir başvuruyu (veya bağlantıyı) bir blob 'a depolamanızı kullanmaktır.
- Dizin oluşturma ilkenizi yalnızca sorgular filtrelemeniz için en iyi duruma getirme, yazma işlemlerinizin kullandığı ru 'da büyük bir farklılık yapabilir. Yeni bir kapsayıcı oluştururken, varsayılan dizin oluşturma ilkesi, öğelerinizde bulunan her özelliği ve her özelliği dizine ekler. Bu, geliştirme etkinlikleri için iyi bir varsayılan olsa da, üretim sırasında veya iş yükünüz önemli trafik almaya başladığında [Dizin oluşturma ilkenizi](how-to-manage-indexing-policy.md) yeniden değerlendirmek ve özelleştirmek kesinlikle önerilir.

Verilerin toplu olarak içe alımı gerçekleştirirken, bu tür işlemlerin RU tüketimini iyileştirmek üzere tasarlandığından [Azure Cosmos DB toplu yürütücü kitaplığı](bulk-executor-overview.md) kullanılması da önerilir. İsteğe bağlı olarak, aynı kitaplıkta oluşturulan [Azure Data Factory](../data-factory/introduction.md) de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, aşağıdaki makalelerle Azure Cosmos DB maliyet iyileştirmesi hakkında daha fazla bilgi edinebilirsiniz:

* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Azure Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB ayrılmış kapasite](cosmos-db-reserved-capacity.md) hakkında daha fazla bilgi edinin
