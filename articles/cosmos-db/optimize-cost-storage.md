---
title: Depolama maliyetini en uygun Azure Cosmos DB
description: Bu makalede, Azure Cosmos DB depolanan veriler için depolama maliyetlerinin nasıl yönetileceği açıklanmaktadır
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 1508adda761fcba7ba70df3bb212d3eb4e32f242
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754954"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Depolama maliyetini en uygun Azure Cosmos DB

Azure Cosmos DB, sınırsız depolama ve aktarım hızı sunar. Azure Cosmos kapsayıcılarınızda veya veritabanlarında sağlamanız/yapılandırmanız gereken aktarım hızının aksine, depolama alanı tüketim temelinde faturalandırılır. Yalnızca kullandığınız mantıksal depolama alanı için faturalandırılır ve herhangi bir depolamayı önceden ayırmanız gerekmez. Depolama, bir Azure Cosmos kapsayıcısına eklediğiniz veya kaldırdığınız verilere göre otomatik olarak ölçeği artırır ve kapatır.

## <a name="storage-cost"></a>Depolama maliyeti

Depolama birimi, GBs birimiyle faturalandırılır. Yerel SSD ile desteklenen depolama, verileriniz ve dizin oluşturma tarafından kullanılır. Kullanılan toplam depolama alanı, Azure Cosmos DB kullandığınız tüm bölgelerde kullanılan veriler ve dizinler için gereken depolama alanına eşittir. Azure Cosmos hesabını üç bölgede genel olarak çoğaltdıysanız, bu üç bölgenin her birinde toplam depolama maliyeti üzerinden ödeme yaparsınız. Depolama gereksinimini tahmin etmek için bkz. [Kapasite planlayıcısı](https://www.documentdb.com/capacityplanner) aracı. Azure Cosmos DB 'deki depolama maliyeti $0,25 GB/ay, en son güncelleştirmeler için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın. Azure Cosmos Kapsayıcınız tarafından kullanılan depolamayı, depolama alanınızı izlemek için, bkz. [izleyici Azure Cosmos DB](monitor-accounts.md)) makaleyi tespit etmek için uyarılar ayarlayabilirsiniz.

## <a name="optimize-cost-with-item-size"></a>Öğe boyutuyla maliyeti iyileştirin

Azure Cosmos DB, en iyi performans ve maliyet avantajları için öğe boyutunun 2 MB veya daha az olmasını bekler. 2 MB 'tan fazla veri depolamak için herhangi bir öğeye ihtiyacınız varsa, öğe şemasını yeniden tasarlamayı düşünün. Şemayı yeniden tasarlayamayacağınızı nadir bir olayda, öğeyi alt öğeleri içine bölebilir ve ortak bir tanımlayıcı (ID) ile mantıksal olarak bağlayabilirsiniz. Tüm Azure Cosmos DB özellikleri, bu mantıksal tanımlayıcıya bağlayarak sürekli olarak çalışır.

## <a name="optimize-cost-with-indexing"></a>Dizin oluşturma ile maliyeti iyileştirme

Varsayılan olarak, veriler otomatik olarak dizinlenir ve bu, tüketilen toplam depolamayı artırabilir. Bununla birlikte, bu ek yükü azaltmak için özel dizin ilkeleri de uygulayabilirsiniz. İlke aracılığıyla ayarlanmayan otomatik dizin oluşturma işlemi, öğe boyutunun% 10-20 ' sidir. Dizin ilkelerini kaldırarak veya özelleştirerek, yazma işlemleri için ek ücret ödemezsiniz ve ek verimlilik kapasitesi gerektirmez. Özel dizin oluşturma ilkelerini yapılandırmak için [Azure Cosmos DB 'de dizin oluşturma](indexing-policies.md) bölümüne bakın. Daha önce ilişkisel veritabanları ile çalıştıysanız, "her şeyi dizine" veya daha yüksek depolama alanının kattığını düşünebilirsiniz. Ancak, Azure Cosmos DB, ortanca durumunda çok daha düşüktür. Azure Cosmos DB, dizinin depolama ek yükü genellikle düşüktür (% 10-20) alt depolama alanı için tasarlandığından, Otomatik Dizin oluşturma özelliği de vardır. Dizin oluşturma ilkesini yöneterek Dizin parmak izini ve sorgu performansını daha ayrıntılı bir şekilde kontrol edebilirsiniz.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Yaşam süresi ile maliyeti iyileştirin ve akışı değiştirin

Verilere artık ihtiyaç duyduktan sonra, verileri [canlı](time-to-live.md)hale getirerek Azure Cosmos hesabınızdan kolaylıkla silebilir, [akışı değiştirebilir](change-feed.md) veya eski verileri Azure Blob depolama veya Azure veri ambarı gibi başka bir veri deposuna geçirebilirsiniz. Yaşam süresi veya TTL ile, Azure Cosmos DB belirli bir süre sonra bir kapsayıcıdan otomatik olarak öğe silme olanağı sağlar. Varsayılan olarak, kapsayıcı düzeyinde yaşam süresi ayarlayabilir ve değeri öğe temelinde geçersiz kılabilirsiniz. TTL 'yi bir kapsayıcıda veya bir öğe düzeyinde ayarladıktan sonra, Azure Cosmos DB son değiştirilme zamanından bu yana geçen süre sonunda bu öğeleri otomatik olarak kaldırır. Değişiklik akışını kullanarak verileri Azure Cosmos DB veya bir dış veri deposuna başka bir kapsayıcıya geçirebilirsiniz. Geçiş işlemi sıfır bir süre sürer ve geçişi tamamladığınızda, kaynak Azure Cosmos kapsayıcısını silmek için yaşam süresini silebilir veya yapılandırabilirsiniz.

## <a name="optimize-cost-with-rich-media-data-types"></a>Zengin medya veri türleriyle maliyeti iyileştirin 

Zengin medya türlerini (örneğin, videolar, görüntüler vb.) depolamak istiyorsanız, Azure Cosmos DB çeşitli seçenekleriniz vardır. Bunlardan biri, bu zengin medya türlerini Azure Cosmos öğeleri olarak depobir seçenektir. Öğe başına 2 MB 'lik bir sınır vardır ve veri öğesini birden çok alt tabloya zincirleyerek bu sınırdan kaçınabilirsiniz. Ya da Azure Blob depolama alanında bunları saklayabilir ve meta verileri Azure Cosmos öğelerinizden başvurmak için kullanabilirsiniz. Bu yaklaşımda birçok sayıda SP2 ve olumsuz yönleri vardır. İlk yaklaşım, düzenli Azure Cosmos öğelerinize ek olarak zengin medya veri türleri için gecikme süresi ve aktarım hızı küresel dağıtım özelliklerine sahip en iyi performansı sağlar. Ancak destek daha yüksek bir fiyatla sunulmaktadır. Medyayı Azure Blob depolamada depolayarak genel maliyetlerinizi düşürebilirsiniz. Gecikme süresi kritik ise, Azure Cosmos öğelerinden başvurulan zengin medya dosyaları için Premium Depolama kullanabilirsiniz. Bu, coğrafi kısıtlamayı aşmak için uç sunucudan daha düşük maliyetli görüntüler sunacak şekilde yerel olarak CDN ile tümleşir. Bu senaryonun aşağı tarafında, işlem maliyetlerini artıran iki hizmet Azure Cosmos DB ve Azure Blob depolama ile uğraşmanız gerekir. 

## <a name="check-storage-consumed"></a>Tüketilen depolamayı denetle

Azure Cosmos kapsayıcısının depolama tüketimini denetlemek için, kapsayıcıda bir HEAD veya GET isteği çalıştırabilir ve `x-ms-request-quota` ve `x-ms-request-usage` üst bilgilerini inceleyebilirsiniz. Alternatif olarak, .NET SDK ile çalışırken, kullanılan depolamayı almak için [Documentsizequota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100))ve [documentsizeusage](https://msdn.microsoft.com/library/azure/dn850324.aspx) özelliklerini kullanabilirsiniz.

## <a name="using-sdk"></a>SDK 'Yı kullanma

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, aşağıdaki makalelerle Azure Cosmos DB maliyet iyileştirmesi hakkında daha fazla bilgi edinebilirsiniz:

* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](optimize-cost-queries.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Azure Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin

