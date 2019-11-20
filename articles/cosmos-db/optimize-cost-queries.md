---
title: Azure Cosmos DB sorguları çalıştırmak için istek birimlerini ve maliyeti iyileştirin
description: Bir sorgu için istek birimi ücretlerini değerlendirmeyi ve sorguyu performans ve maliyet açısından en uygun hale getirmeyi öğrenin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 376c1a32a70951448b35a4c02022719229a3aad2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753298"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Azure Cosmos DB 'de sorgu maliyetini iyileştirin

Azure Cosmos DB, bir kapsayıcı içindeki öğelerde çalışan ilişkisel ve hiyerarşik sorgular da dahil olmak üzere zengin bir veritabanı işlemleri kümesi sunar. Bu işlemlerden her biriyle ilişkilendirilmiş maliyet, işlemi gerçekleştirmek için gereken CPU, GÇ ve belleğe göre değişir. Donanım kaynaklarını düşünmek ve yönetmek yerine bir istek birimi (RU), bir isteğe yönelik çeşitli veritabanı işlemlerini gerçekleştirmek için gereken kaynaklar için tek bir ölçü olarak düşünebilirsiniz. Bu makalede, bir sorgu için istek birimi ücretlerinin nasıl değerlendirileceği ve sorgunun performans ve maliyet açısından en iyi hale getirileceği açıklanır. 

Azure Cosmos DB sorguları genellikle aktarım açısından en hızlı/en etkili ve daha az verimlidir  

* Tek bir bölüm anahtarında ve öğe anahtarında işlem al.

* Tek bir bölüm anahtarı içinde bir filtre yan tümcesi ile sorgulayın.

* Herhangi bir özellikte eşitlik veya Aralık filtresi yan tümcesi olmadan sorgu.

* Filtre olmadan sorgulayın.

Bir veya daha fazla bölümden verileri okuyan sorgular, daha yüksek gecikme süresine ve daha yüksek sayıda istek birimi tüketir. Her bölümde tüm özellikler için otomatik dizin oluşturma olduğundan, sorgu dizinden verimli bir şekilde sunulabilir. Paralellik seçeneklerini kullanarak birden çok bölüm kullanan sorguları daha hızlı yapabilirsiniz. Bölümlendirme ve bölüm anahtarları hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB bölümlendirme](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Bir sorgu için istek birimi ücreti değerlendir

Azure Cosmos kapsayıcılarınızda bazı verileri depoladıktan sonra, sorgularınızı oluşturmak ve çalıştırmak için Azure portal Veri Gezgini kullanabilirsiniz. Ayrıca, Veri Gezgini 'ni kullanarak sorguların maliyetini de alabilirsiniz. Bu yöntem, sisteminizin desteklediği tipik sorgular ve işlemlerle ilgili gerçek ücretler hakkında fikir verecektir.

SDK 'Ları kullanarak, programlı bir şekilde sorgu maliyeti de alabilirsiniz. Oluşturma, güncelleştirme veya silme gibi herhangi bir işlemin ek yükünü ölçmek için REST API kullanırken `x-ms-request-charge` üst bilgisini inceleyin. .NET veya Java SDK kullanıyorsanız, `RequestCharge` özelliği istek ücreti almak için eşdeğer bir özelliktir ve bu özellik Resourceres, FeedResponse içinde bulunur.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Bir sorgu için istek birimi ücretlendirmeyi etkileyen etmenler

Sorgular için istek birimleri, bir dizi etkene bağımlıdır. Örneğin, yüklenen/döndürülen Azure Cosmos öğelerinin sayısı, dizinde yapılan arama sayısı, sorgu derleme süresi vb. ayrıntılar. Azure Cosmos DB aynı verilerde yürütüldüğü sırada aynı sorgunun her zaman aynı sayıda istek birimi de tekrarlayarak yineleme yürütmelerinin aynısını kullanmasını güvence altına alır. Sorgu yürütme ölçümlerini kullanan sorgu profili, istek birimlerinin nasıl harcandığına ilişkin iyi bir fikir verir.  

Bazı durumlarda, sorgular kullanılabilir ru 'yı temel alarak mümkün olduğunca hızlı çalışacağı için bir dizi 200 ve 429 yanıt ve çok sayıda sorgu üzerinde değişken istek birimleri görebilirsiniz. Sunucu ve istemci arasında birden fazla sayfaya/gidiş dönüşe bir sorgu yürütme kesmesi görebilirsiniz. Örneğin, 10.000 öğe, her biri bu sayfada gerçekleştirilen hesaplamayı temel alarak ücretlendirilen birden çok sayfa olarak döndürülebilir. Bu sayfalar arasında toplama yaptığınızda, tüm sorgu için alacağınız aynı ru sayısını almalısınız.  

## <a name="metrics-for-troubleshooting"></a>Sorun giderme ölçümleri

Sorgular tarafından tüketilen performans ve aktarım hızı, Kullanıcı tanımlı işlevler (UDF 'ler) genellikle işlev gövdesine bağlıdır. Sorgu yürütmesinin UDF 'de ne kadar zaman harcandığını ve tüketilen saat sayısını bulmanın en kolay yolu, sorgu ölçümlerini etkinleştirir. .NET SDK kullanıyorsanız, SDK tarafından döndürülen örnek sorgu ölçümleri aşağıda verilmiştir:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Sorguları iyileştirmek için en iyi uygulamalar 

Aşağıdaki en iyi yöntemleri maliyet için iyileştirirken göz önünde bulundurun:

* **Birden çok varlık türünü birlikte bulundurma**

   Birden çok varlık türünü tek veya daha küçük bir kapsayıcı içinde bulundurmanıza çalışın. Bu yöntem, yalnızca bir fiyatlandırma perspektifinden değil, sorgu yürütme ve işlemler için de avantaj verir. Sorgular tek bir kapsayıcıya kapsamlandırılır; saklı yordamlar/Tetikleyiciler aracılığıyla birden çok kayıt üzerinde Atomik işlemler, tek bir kapsayıcı içindeki bir bölüm anahtarının kapsamına alınır. Aynı kapsayıcı içindeki varlıkları birlikte bulundurma, kayıtlar arasındaki ilişkileri çözümlemek için ağ gidiş dönüş sayısını azaltabilir. Bu nedenle, uçtan uca performansı artırarak, daha büyük bir veri kümesi için birden çok kayıt üzerinde atomik işlemleri sağlar ve sonuç olarak maliyetleri düşürür. Tek veya daha küçük bir kapsayıcı içinde birden çok varlık türünü birlikte bulundurma senaryosunda, genellikle var olan bir uygulamayı geçiriyorsanız ve herhangi bir kod değişikliği yapmak istemediğiniz için, daha sonra sağlamayı düşünmelisiniz veritabanı düzeyinde aktarım hızı.  

* **Düşük istek birimleri/ikinci kullanım için ölçme ve ayarlama**

   Bir sorgunun karmaşıklığı, bir işlem için kaç tane istek birimi (ru) tüketildiğini etkiler. Koşulların sayısı, koşulların doğası, UDF sayısı ve kaynak veri kümesinin boyutu. Tüm bu faktörler, sorgu işlemlerinin maliyetini etkiler. 

   İstek üstbilgisinde döndürülen istek ücreti, belirli bir sorgunun maliyetini gösterir. Örneğin, bir sorgu 1000 1 KB 'lik öğeler döndürürse, işlemin maliyeti 1000 ' dir. Bu nedenle, bir saniye içinde sunucu, sonraki istekleri sınırlayan orandan önce yalnızca iki istek için geçerlidir. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md) makalesi ve istek birimi hesaplayıcısı. 

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, aşağıdaki makalelerle Azure Cosmos DB maliyet iyileştirmesi hakkında daha fazla bilgi edinebilirsiniz:

* [Azure Cosmos fiyatlandırmasının nasıl çalıştığı](how-pricing-works.md) hakkında daha fazla bilgi edinin
* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Azure Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB ayrılmış kapasite](cosmos-db-reserved-capacity.md) hakkında daha fazla bilgi edinin

