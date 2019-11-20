---
title: Redsıs için Azure önbelleğinde veri kaybına sorun giderme
description: Redsıs için Azure Cache ile veri kaybı sorunlarını çözmeyi öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 77493675de0a654d3bb510f7cda22a2abbca0aa2
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121501"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Redsıs için Azure önbelleğinde veri kaybına sorun giderme

Bu makalede Redsıs için Azure önbelleğinde oluşabilecek gerçek veya algılanan veri kayıplarının nasıl tanılanması anlatılmaktadır.

> [!NOTE]
> Bu kılavuzdaki sorun giderme adımlarından bazıları Redsıs komutları çalıştırma ve çeşitli performans ölçümlerini izleme yönergelerini içerir. Daha fazla bilgi ve yönergeler için [ek bilgi](#additional-information) bölümündeki makalelere bakın.
>

## <a name="partial-loss-of-keys"></a>Anahtarların kısmi kaybı

Redsıs için Azure önbelleği, bellekte depolandıktan sonra anahtarları rastgele olarak silmez. Ancak, süre sonu veya çıkarma ilkelerine ve açık anahtar silme komutlarına yanıt olarak anahtarları kaldırır. Premium veya standart Azure önbelleğindeki ana düğüme, redin örneği için yazılmış anahtarlar aynı zamanda bir çoğaltmada doğrudan kullanılabilir olmayabilir. Veriler, ana bilgisayardan çoğaltmaya zaman uyumsuz ve engellenmeyen bir şekilde çoğaltılır.

Bu anahtarların önbelleğinizi kaybolduğunu fark ederseniz, aşağıdaki olası nedenleri kontrol edin:

| Nedeni | Açıklama |
|---|---|
| [Anahtar süre sonu](#key-expiration) | Anahtarlar, üzerinde ayarlanan zaman aşımları nedeniyle kaldırılır. |
| [Anahtar çıkarma](#key-eviction) | Anahtarlar bellek baskısı altında kaldırılır. |
| [Anahtar silme](#key-deletion) | Anahtarlar açık silme komutları tarafından kaldırılır. |
| [Zaman uyumsuz çoğaltma](#async-replication) | Veri çoğaltma gecikmeleri nedeniyle anahtarlar çoğaltma üzerinde kullanılamaz. |

### <a name="key-expiration"></a>Anahtar süre sonu

Anahtar bir zaman aşımı atanmışsa ve bu süre geçtiğinde Redsıs için Azure önbelleği bir anahtarı otomatik olarak kaldırır. Redsıs anahtar süre sonu hakkında daha fazla bilgi için, [süre](http://redis.io/commands/expire) sonu komut belgelerine bakın. Ayrıca, zaman aşımı değerleri [set](http://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)ve diğer **\*Store** komutları kullanılarak ayarlanabilir.

Kaç anahtarın dolduğunu gösteren istatistikleri almak için, [Info](http://redis.io/commands/info) komutunu kullanın. `Stats` bölümü, süre dolma anahtarlarının toplam sayısını gösterir. `Keyspace` bölümü, zaman aşımları ve ortalama zaman aşımı değeri olan anahtarların sayısı hakkında daha fazla bilgi sağlar.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Ayrıca, anahtarın eksik olduğu zaman arasında bir bağıntı olup olmadığını ve zaman aşımına uğradı anahtarların bir ani bir şekilde olduğunu görmek için önbelleğiniz için tanılama ölçümlerine bakabilirsiniz. Bu tür sorunların hatalarını ayıklamak için anahtar alanı bildirimleri veya **izleyicisini** kullanma hakkında bilgi için [redsıs anahtar alanı isabetsizliği hata ayıklamanın](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) ek başlığına bakın.

### <a name="key-eviction"></a>Anahtar çıkarma

Redin için Azure önbelleği, verileri depolamak için bellek alanı gerektirir. Gerektiğinde kullanılabilir belleği boşaltmak için anahtarları temizler. [Info](http://redis.io/commands/info) komutunda **used_memory** veya **used_memory_rss** değerleri yapılandırılmış **MaxMemory** ayarına yaklaşımında, redsıs için Azure önbelleği, [önbellek ilkesine](http://redis.io/topics/lru-cache)göre bellekten anahtar çıkarma işlemi başlatır.

Çıkarılan anahtarların sayısını [Info](http://redis.io/commands/info) komutunu kullanarak izleyebilirsiniz:

```
# Stats

evicted_keys:13224
```

Ayrıca, anahtarın eksik olduğu zaman arasında bir bağıntı olup olmadığını ve çıkarılan anahtarların bir ani bir şekilde olduğunu görmek için önbelleğiniz için tanılama ölçümlerine bakabilirsiniz. Bu tür sorunların hatalarını ayıklamak için anahtar alanı bildirimleri veya **izleyicisini** kullanma hakkında bilgi için [redsıs anahtar alanı isabetsizliği hata ayıklamanın](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) ek başlığına bakın.

### <a name="key-deletion"></a>Anahtar silme

Redsıs istemcileri Redsıs için Azure Cache 'ten anahtar açıkça kaldırmak üzere [del](http://redis.io/commands/del) veya [hdel](http://redis.io/commands/hdel) komutunu verebilir. [Info](http://redis.io/commands/info) komutunu kullanarak silme işlemlerinin sayısını izleyebilirsiniz. **Del** veya **hdel** komutları çağrılırsa, `Commandstats` bölümünde listelenecektir.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Zaman uyumsuz çoğaltma

Standart veya Premium katmanda Redsıs örneği için herhangi bir Azure önbelleği, bir ana düğüm ve en az bir çoğaltma ile yapılandırılır. Veriler, bir arka plan işlemi kullanılarak ana bilgisayardan bir kopyaya zaman uyumsuz olarak kopyalanır. [Redis.io](http://redis.io/topics/replication) Web sitesi, redsıs veri çoğaltmasının genel olarak nasıl çalıştığını açıklar. İstemcilerin redde sık yazacağı senaryolarda, bu çoğaltmanın anlık olduğu garanti edildiğinden kısmi veri kaybı oluşabilir. Örneğin *, bir istemci kendisine bir anahtar* yazdığında, ancak arka plan işleminin bu anahtarı çoğaltmaya gönderme şansı *olmadan önce* , çoğaltma yeni ana öğe olarak geçtiğinde anahtar kaybedilir.

## <a name="major-or-complete-loss-of-keys"></a>Anahtarların büyük veya tamamen kaybolması

En fazla veya tüm anahtarlar önbelleğinizi kaybolduysa, aşağıdaki olası nedenleri kontrol edin:

| Nedeni | Açıklama |
|---|---|
| [Anahtar Temizleme](#key-flushing) | Anahtarlar el ile temizlendi. |
| [Yanlış veritabanı seçimi](#incorrect-database-selection) | Redo için Azure önbelleği varsayılan olmayan bir veritabanı kullanacak şekilde ayarlanmıştır. |
| [Redsıs örneği hatası](#redis-instance-failure) | Redsıs sunucusu kullanılamıyor. |

### <a name="key-flushing"></a>Anahtar Temizleme

İstemciler, *tek* bir veritabanındaki tüm anahtarları kaldırmak Için [flushdb](http://redis.io/commands/flushdb) komutunu çağırabilir veya bir redsıs önbelleğindeki *tüm veritabanlarından tüm* anahtarları kaldırmak için [flushall](http://redis.io/commands/flushall) ' a çağrı yapabilir. Anahtarların temizlenmiş olup olmadığını öğrenmek için, [Info](http://redis.io/commands/info) komutunu kullanın. `Commandstats` bölümünde, **Temizleme** komutunun çağrılıp çağrılmayacağı gösterilmektedir:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Yanlış veritabanı seçimi

Redsıs için Azure önbelleği varsayılan olarak **DB0** veritabanını kullanır. Başka bir veritabanına geçiş yaparsanız (örneğin, **DB1**) ve bundan sonra anahtarları okumaya çalışırsanız, redin Için Azure önbelleği bunları orada bulamaz. Her veritabanı mantıksal olarak ayrı bir birimdir ve farklı bir veri kümesi tutar. Diğer kullanılabilir veritabanlarını kullanmak ve bunların her birinde anahtarları aramak için [Seç](http://redis.io/commands/select) komutunu kullanın.

### <a name="redis-instance-failure"></a>Redsıs örneği hatası

Redsıs, bellek içi veri deposudur. Veriler redo önbelleğini barındıran fiziksel veya sanal makinelerde tutulur. Temel katmandaki Redsıs örneği için bir Azure önbelleği yalnızca tek bir sanal makinede (VM) çalışır. Bu VM kapalıysa, önbellekte depoladığınız tüm veriler kaybolur. 

Standart ve Premium katmanlardaki önbellekler, çoğaltılan bir yapılandırmada iki VM kullanarak veri kaybına karşı daha fazla esneklik sunar. Bu tür bir önbellekteki ana düğüm başarısız olduğunda, çoğaltma düğümü verileri otomatik olarak sunacak şekilde alır. Bu sanal makineler, aynı anda kullanılamaz duruma gelme olasılığını en aza indirmek için hatalar ve güncelleştirmeler için ayrı etki alanlarında bulunur. Ancak büyük bir veri merkezi kesintisi olursa VM 'Ler yine de devam edebilir. Bu nadir durumlarda verileriniz kaybedilir.

Bu altyapı hatalarıyla karşı verilerinizin korunmasını artırmak için [redsıs veri kalıcılığı](http://redis.io/topics/persistence) ve [coğrafi çoğaltma](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) kullanmayı göz önünde bulundurun.

## <a name="additional-information"></a>Ek bilgiler

- [Redsıs sunucu tarafı sorunları için Azure Cache sorunlarını giderme](cache-troubleshoot-server.md)
- [Redne teklif teklifi ve boyutu için Azure önbelleği kullanmalıyım?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Redsıs için Azure önbelleğini izleme](cache-how-to-monitor.md)
- [Redsıs komutlarını nasıl çalıştırabilirim?](cache-faq.md#how-can-i-run-redis-commands)
