---
title: Azure Cosmos DB kullanırken sorgu sorunlarını tanılama ve giderme
description: Azure Cosmos DB SQL sorgu sorunlarını belirlemeyi, tanılamayı ve sorun gidermeyi öğrenin.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: d0dd9a371c4912cae0e74b214c673c629fc1ff55
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515806"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Azure Cosmos DB sorgu performansının sorunlarını giderme
Bu makalede, SQL sorgu sorunlarını Azure Cosmos DB belirleme, tanılama ve sorunlarını giderme konuları ele alınmaktadır. Azure Cosmos DB sorgularda en iyi performansı elde etmek için aşağıdaki sorun giderme adımlarını izleyin. 

## <a name="collocate-clients-in-same-azure-region"></a>Aynı Azure bölgesindeki istemcileri birlikte bulun 
Olası en düşük gecikme süresi, çağıran uygulamanın sağlanan Azure Cosmos DB uç noktası ile aynı Azure bölgesinde bulunduğundan emin olarak elde edilir. Kullanılabilir bölgelerin listesi için bkz. [Azure bölgeleri](https://azure.microsoft.com/global-infrastructure/regions/#services) makalesi.

## <a name="check-consistency-level"></a>Tutarlılık düzeyini denetle
[Tutarlılık düzeyi](consistency-levels.md) , performansı ve ücretleri etkileyebilir. Tutarlılık düzeyinin verilen senaryoya uygun olduğundan emin olun. Daha fazla ayrıntı için bkz. [tutarlılık düzeyi seçme](consistency-levels-choosing.md).

## <a name="log-the-executed-sql-query"></a>Yürütülen SQL sorgusunu günlüğe kaydet 

Yürütülen SQL sorgusunu bir depolama hesabında veya tanılama günlüğü tablosunda günlüğe kaydedebilirsiniz. [Tanılama günlükleri aracılığıyla SQL sorgu günlükleri](logging.md#turn-on-logging-in-the-azure-portal) , karıştırılmış sorguyu seçtiğiniz bir depolama hesabında günlüğe kaydetmenize olanak tanır. Bu, daha yüksek RUs 'yi kullanan günlüklere baklamanızı ve sorgu bulmayı sağlar. Daha sonra, QueryRuntimeStatistics içindeki gerçek sorguyla eşleştirmek için etkinlik kimliğini kullanabilirsiniz. Sorgu güvenlik amacı ve sorgu parametresi adları ve WHERE yan tümcelerinde değerleri gerçek adlardan ve değerlerden farklı olacak şekilde belirlenir. Yürütülen sorguların uzun süreli olarak bekletilmesini sağlamak için depolama hesabını günlüğe kaydetme kullanabilirsiniz.  

## <a name="log-query-metrics"></a>Günlük sorgusu ölçümleri

Yavaş `QueryMetrics` veya pahalı sorguların sorunlarını gidermek için kullanın. 

  * Yanıtta `FeedOptions.PopulateQueryMetrics = true` olacak`QueryMetrics` şekilde ayarlayın.
  * `QueryMetrics`sınıfında, öğesinin `QueryMetrics`dize `.ToString()` gösterimini almak için çağrılabilecek aşırı yüklenmiş bir işlev vardır. 
  * Ölçümler, diğerleri arasında aşağıdaki öngörüleri türetmek için kullanılabilir: 
  
      * Sorgu işlem hattının herhangi bir belirli bileşeninin olağan dışı bir şekilde (yüzlerce milisaniyelik veya daha fazla) tamamlanmasını belirtir. 

          * Bölümüne bakın `TotalExecutionTime`.
          * `TotalExecutionTime` Sorgunun son yürütme zamanından daha küçükse, istemci tarafında veya ağda saat harcanmaktadır. İstemci ve Azure bölgesinin birlikte bulunduğundan emin olun.
      
      * Çözümlenen belgelerde hatalı pozitif sonuçlar olup olmadığı (çıkış belgesi sayısı alınan belge sayısından çok daha az ise).  

          * Bölümüne bakın `Index Utilization`.
          * `Index Utilization`= (Döndürülen belge sayısı/yüklenen belge sayısı)
          * Döndürülen belge sayısı yüklenen sayıdan çok daha küçükse, yanlış pozitif sonuçlar çözümlenmekte olur.
          * Daha dar filtrelerle alınmakta olan belge sayısını sınırlayın.  

      * Tek tek gidiş dönüşlerin `Partition Execution Timeline` `QueryMetrics`nasıl olduğu (dize gösteriminden bkz.). 
      * Sorgunun yüksek istek ücreti tüketip tüketilmediğini belirtir. 

Daha fazla ayrıntı için bkz. [SQL sorgu yürütme ölçümleri alma](profile-sql-api-query.md) makalesi.
      
## <a name="tune-query-feed-options-parameters"></a>Sorgu Akışı Seçenekleri Parametrelerini Ayarlama 
İsteğin [Akış Seçenekleri](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) Parametreleri aracılığıyla sorgu performansı ayarlanabilir. Aşağıdaki seçenekleri ayarlamayı deneyin:

  * Önce `MaxDegreeOfParallelism` -1 olarak ayarlayın ve ardından farklı değerler genelinde performansı karşılaştırın. 
  * Önce `MaxBufferedItemCount` -1 olarak ayarlayın ve ardından farklı değerler genelinde performansı karşılaştırın. 
  * \- `MaxItemCount` 1 olarak ayarlayın.

Farklı değerlerin performansını karşılaştırırken, 2, 4, 8, 16 vb. gibi değerleri deneyin.
 
## <a name="read-all-results-from-continuations"></a>Devamlılıklardan tüm sonuçları okuyun
Tüm sonuçları almadığınızı düşünüyorsanız devamlılığı tamamen boşalttığınızdan emin olun. Başka bir deyişle, devamlılık belirtecinin askıya alınacağı daha fazla belgesi varken sonuçları okumayı sürdürün.

Aşağıdaki desenlerden herhangi biriyle tam boşaltma gerçekleştirilebilir:

  * Devamlılık boş olmadığından sonuçları işlemeye devam edin.
  * Sorgu daha fazla sonuç içerdiğinden işleme devam edin. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Dizin kullanan sistem işlevlerini seçin
İfade, bir dize değerleri aralığına çevrilebiliyorsa dizini kullanabilir; aksi takdirde dizini kullanamaz. 

Dizini kullanılabilecek dize işlevlerinin listesi aşağıdadır: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    İşte birkaç sorgu örneği: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Dizin tarafından sunulmayan filtredeki (veya WHERE yan tümcesindeki) sistem işlevlerinden kaçının. Bu tür sistem işlevlerinin bazı örnekleri şunlardır, büyük, daha düşüktür.
  * Mümkün olduğunda sorguları bölüm anahtarı filtrelemesi kullanacak şekilde yazın.
  * Performansı elde etmek için, filtrede üst/alt ' i çağırmayı önleyin. Bunun yerine, ekleme sırasında değerlerin büyük küçük harflerini normalleştirin. Değerlerin her biri için istenen büyük küçük harfe sahip değeri ekleyin veya hem özgün değeri hem de değeri istenen büyük küçük harfe ekleyin. 

    Örneğin:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Bu durumda, "alı" i büyük harfle depolayın veya "ali" değerini özgün değeri ve "alı" olarak depolayın. 
    
    JSON veri büyük küçük harf normalleştirilirse sorgu şu şekilde olur:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    İkinci sorgu, değerleri "alı" ile karşılaştırmak için değerlerin her birinde dönüşümler yapılmasını gerektirmediğinden daha iyi performans sağlayacaktır.

Daha fazla sistem işlevi ayrıntıları için bkz. [sistem işlevleri](sql-query-system-functions.md) makalesi.

## <a name="check-indexing-policy"></a>Dizin oluşturma ilkesini denetle
Geçerli [Dizin Oluşturma İlkesinin](index-policy.md) en iyi olduğunu doğrulamak için:

  * Sorgularda kullanılan tüm JSON yollarının, daha hızlı okuma için dizin oluşturma ilkesine eklendiğinden emin olun.
  * Daha fazla performans yazma için sorgularda kullanılmayan yolları dışlayın.

Daha fazla ayrıntı için bkz. [Dizin oluşturma Ilkesini yönetme](how-to-manage-indexing-policy.md) makalesi.

## <a name="spatial-data-check-ordering-of-points"></a>Uzamsal veriler: Noktaların sıralamasını denetleme
İçinde bir Çokgen noktalarının saat yönünün tersi düzende belirtilmesi gerekir. Bir çokgenin belirtilen saat yönünde sırayla bölgesinde tersini temsil eder.

## <a name="optimize-join-expressions"></a>JOIN ifadelerini iyileştirme
`JOIN`ifadeler, büyük çapraz ürünlere genişleyebilir. Mümkün olduğunda, daha dar bir filtre yoluyla daha küçük bir arama alanına karşı sorgulayın.

Çoklu değer alt sorguları, `JOIN` `WHERE` yan Tümcecikteki tüm çapraz birleşimler yerine her bir SELECT-many ifadesinden sonra gelen koşulları ileterek ifadeleri iyileştirebilir. Ayrıntılı bir örnek için bkz. [JOIN deyimlerini iyileştirme](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) makalesi.

## <a name="optimize-order-by-expressions"></a>SıRALAMA ölçütü ifadelerini iyileştirme 
`ORDER BY`alanlar seyrek olursa veya dizin ilkesine dahil edilmamışsa sorgu performansı düşebilir.

  * Zaman gibi seyrek alanlar için, filtre ile mümkün olduğunca fazla arama alanını azaltın. 
  * Tek özellik `ORDER BY`için Dizin ilkesine özelliği ekleyin. 
  * Birden çok özellik `ORDER BY` ifadesi için, sıralanan alanlarda [bileşik bir dizin](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) tanımlayın.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Yüklenen ve işlenen birçok büyük belge
Bir sorgu için gereken saat ve RUs yalnızca yanıtın boyutuna bağlı değildir, ayrıca sorgu işleme ardışık düzeni tarafından gerçekleştirilen işe da bağımlıdır. Zaman ve ru, tüm sorgu işleme işlem hattının yaptığı iş miktarı ile orantılı olarak artar. Büyük belgeler için daha fazla iş yapılır, bu nedenle büyük belgeleri yüklemek ve işlemek için daha fazla zaman ve RUs gerekir.

## <a name="low-provisioned-throughput"></a>Düşük sağlanan aktarım hızı
Sağlanan aktarım hızının iş yükünü işleyediğinden emin olun. Etkilenen koleksiyonlar için RU bütçesini yükseltin.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>En son SDK sürümüne yükseltmeyi deneyin
En son SDK 'yı öğrenmek için bkz. [SDK indirme ve sürüm notları](sql-api-sdk-dotnet.md) makalesi.

## <a name="next-steps"></a>Sonraki adımlar
Sorgu başına ru ölçüsünde aşağıdaki belgelere bakın, sorgularınızı ayarlamak için yürütme istatistiklerini alın ve daha fazlasını yapın:

* [.NET SDK kullanarak SQL sorgusu yürütme ölçümlerini alma](profile-sql-api-query.md)
* [Azure Cosmos DB ile sorgu performansını ayarlama](sql-api-sql-query-metrics.md)
* [.NET SDK’sı için performans ipuçları](performance-tips.md)
