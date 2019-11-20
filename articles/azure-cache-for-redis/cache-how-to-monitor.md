---
title: Redsıs için Azure önbelleğini izleme
description: Redsıs örnekleri için Azure önbelleğinizin durumunu ve performansını nasıl izleyeceğinizi öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 73e1e3bfbc84e6264897d571fca1bf31061d7ab6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122756"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Redsıs için Azure önbelleğini izleme
Redsıs için Azure önbelleği, önbellek örneklerinizi izlemeye yönelik çeşitli seçenekler sunmak için [Azure izleyici](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 'yi kullanır. Ölçümleri görüntüleyebilir, ölçüm grafiklerini başlangıç panosuna sabitleyebilir, izleme grafiklerinin tarih ve saat aralığını özelleştirebilir, grafiklerden ölçümleri ekleyebilir ve kaldırabilir ve belirli koşullar karşılandığında uyarılar ayarlayabilirsiniz. Bu araçlar, Redsıs örnekleri için Azure önbelleğinizin durumunu izlemenize ve önbelleğe alma uygulamalarınızı yönetmenize yardımcı olur.

Redsıs örnekleri için Azure Cache ölçümleri, dakikada yaklaşık olarak iki kez Redsıs [Info](https://redis.io/commands/info) komutu kullanılarak toplanır ve 30 gün boyunca otomatik olarak depolanır (bkz. farklı bir bekletme ilkesi yapılandırmak için [önbellek ölçümlerini dışarı aktarma](#export-cache-metrics) ), böylece ölçüm grafiklerinde görüntülenebilmeleri ve uyarı kuralları tarafından değerlendirilir. Her önbellek ölçümü için kullanılan farklı BILGI değerleri hakkında daha fazla bilgi için bkz. [kullanılabilir ölçümler ve raporlama aralıkları](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Önbellek ölçümlerini görüntülemek için [Azure Portal](https://portal.azure.com)önbellek örneğinizi [inceleyin](cache-configure.md#configure-azure-cache-for-redis-settings) .  Redin için Azure Cache, **genel bakış** dikey penceresinde ve **redin ölçümleri** dikey penceresinde bazı yerleşik grafikler sağlar. Her bir grafik, ölçümler eklenerek veya kaldırılarak ve Raporlama aralığı değiştirilerek özelleştirilebilir.

![Redsıs ölçümleri](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Önceden yapılandırılmış ölçüm grafiklerini görüntüleme

**Genel bakış** dikey penceresinde aşağıdaki önceden yapılandırılmış izleme grafikleri vardır.

* [İzleme grafikleri](#monitoring-charts)
* [Kullanım grafikleri](#usage-charts)

### <a name="monitoring-charts"></a>İzleme grafikleri
**Genel bakış** dikey penceresindeki **izleme** bölümünde **isabetler ve Isabetsizlik**, **alır ve ayarlar**, **Bağlantılar**ve **Toplam komut** grafikleri bulunur.

![İzleme grafikleri](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Kullanım grafikleri
**Genel bakış** dikey penceresindeki **kullanım** bölümünde **redsıs sunucu yükü**, **bellek kullanımı**, **ağ bant genişliği**ve **CPU kullanımı** grafikleri bulunur ve ayrıca önbellek örneğinin **fiyatlandırma katmanı** görüntülenir.

![Kullanım grafikleri](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Fiyatlandırma katmanı** , önbellek fiyatlandırma katmanını görüntüler ve önbelleği farklı bir fiyatlandırma katmanına [ölçeklendirmek](cache-how-to-scale.md) için kullanılabilir.

## <a name="view-metrics-with-azure-monitor"></a>Azure izleyici ile ölçümleri görüntüleme
Redsıs ölçümlerini görüntülemek ve Azure Izleyici 'yi kullanarak özel grafikler oluşturmak için **kaynak menüsünde** **ölçümler** ' e tıklayın ve istediğiniz ölçümleri, raporlama aralığını, grafik türünü ve daha fazlasını kullanarak grafiğinizi özelleştirin.

![Redsıs ölçümleri](./media/cache-how-to-monitor/redis-cache-monitor.png)

Azure Izleyici kullanarak ölçümler ile çalışma hakkında daha fazla bilgi için, bkz. [Microsoft Azure ölçümlere genel bakış](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Önbellek ölçümlerini dışarı aktar
Varsayılan olarak, Azure Izleyici 'deki önbellek ölçümleri [30 gün boyunca depolanır](../azure-monitor/platform/data-platform-metrics.md) ve sonra silinir. Önbellek ölçümlerinizi 30 günden daha uzun bir süre boyunca kalıcı hale getirmek için [bir depolama hesabı](../azure-monitor/platform/archive-diagnostic-logs.md) belirleyebilir ve önbellek ölçümleriniz Için bir **bekletme (gün)** ilkesi belirtebilirsiniz. 

Önbellek ölçümleriniz için bir depolama hesabı yapılandırmak için:

1. **Redsıs dikey penceresinde Azure önbelleğindeki** **Kaynak menüsünden** **Tanılamalar** ' a tıklayın.
2. Öğesine **tıklayın.**
3. **Arşivi bir depolama hesabına**çekin.
4. Önbellek ölçümlerinin kaydedileceği depolama hesabını seçin.
5. **1 dakikalık** onay kutusunu işaretleyin ve bir **bekletme (gün)** ilkesi belirtin. Herhangi bir bekletme ilkesi uygulamak ve verileri süresiz olarak saklamak istemiyorsanız, **bekletme (gün)** seçeneğini **0**olarak ayarlayın.
6. **Kaydet**’e tıklayın.

![Redsıs tanılama](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Önbellek ölçümlerinizi depolamaya arşivleme ek olarak, [bunları bir olay hub 'ına de bağlayabilir veya Azure izleyici günlüklerine gönderebilirsiniz](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>
>

Ölçümlerinize erişmek için, bu makalede daha önce açıklanan Azure portal görüntüleyebilirsiniz ve [Azure Izleyici ölçümleri REST API](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)kullanarak da erişebilirsiniz.

> [!NOTE]
> Depolama hesaplarını değiştirirseniz, daha önce yapılandırılan depolama hesabındaki veriler indirilmek üzere kullanılabilir kalır, ancak Azure portal gösterilmez.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Kullanılabilir ölçümler ve raporlama aralıkları
Önbellek ölçümleri, **Geçmiş saat**, **bugün**, **geçen hafta**ve **özel**gibi birkaç Raporlama aralığı kullanılarak raporlanır. Her ölçüm grafiğinin **ölçüm** dikey penceresi grafikteki her ölçüm için Ortalama, en düşük ve en yüksek değerleri görüntüler ve bazı ölçümler Raporlama aralığı için bir toplam görüntüler. 

Her ölçüm iki sürüm içerir. Bir ölçüm tüm önbelleğin performansını ölçer ve [kümeleme](cache-how-to-premium-clustering.md)kullanan önbellekler için, ad içinde `(Shard 0-9)` içeren ikinci bir sürümü bir önbellekte tek bir parça için performansı ölçer. Örneğin, bir önbellekte 4 parça varsa `Cache Hits` tüm önbelleğin toplam isabet miktarı olur ve `Cache Hits (Shard 3)` yalnızca önbelleğin söz konusu parçasına ait olan isabetlerğidir.

> [!NOTE]
> Önbellek bağlı etkin istemci uygulamaları olmadan boşta kaldığında bile, bağlı istemciler, bellek kullanımı ve gerçekleştirilen işlemler gibi bazı önbellek etkinliklerini görebilirsiniz. Bu etkinlik, Redsıs örneği için Azure önbelleğinin çalışması sırasında normaldir.
> 
> 

| Ölçüm | Açıklama |
| --- | --- |
| Önbellek ısabetleri |Belirtilen Raporlama aralığı sırasında başarılı anahtar arama sayısı. Bu, Redsıs [Info](https://redis.io/commands/info) komutundan `keyspace_hits` eşlenir. |
| Önbellek gecikmesi (Önizleme) | Önbelleğin düğümler arası gecikme süresi temel alınarak hesaplanan önbelleğin gecikmesi. Bu ölçüm mikrosaniye cinsinden ölçülür ve üç boyuta sahiptir: belirtilen Raporlama aralığı sırasında önbelleğin ortalama, en düşük ve en fazla gecikme süresini temsil eden "AVG", "min" ve "Max". |
| Önbellek Isabetsizliği |Belirtilen Raporlama aralığı sırasında başarısız anahtar aramalarının sayısı. Bu, Redsıs ıNFO komutundan `keyspace_misses` eşlenir. Önbellek isabetsizliği, önbellekte bir sorun olduğu anlamına gelmez. Örneğin, önbelleğe alma programlama deseninin kullanıldığı bir uygulama, bir öğe için ilk önbellekte görünür. Öğe yoksa (önbellek isabetsizliği), öğe veritabanından alınır ve bir dahaki sefer için önbelleğe eklenir. Önbellek isabetsizliği, önbelleğe alma programlama deseninin normal davranışıdır. Önbellek isabetsizlik sayısı beklenenden yüksekse, önbellekten dolduran ve okunan uygulama mantığını inceleyin. Bellek baskısı nedeniyle Öğeler önbellekten çıkarılacaksa, bazı önbellek isabetsizliği olabilir, ancak bellek basıncını izlemek için daha iyi bir ölçüm `Used Memory` veya `Evicted Keys`. |
| Önbellek Okuma |Belirtilen Raporlama aralığı sırasında önbellekten saniye başına megabayt (MB/sn) cinsinden okunan veri miktarı. Bu değer, önbelleği barındıran sanal makineyi destekleyen ve Redspecific olmayan ağ arabirimi kartlarından türetilir. **Bu değer, bu önbellek tarafından kullanılan ağ bant genişliğine karşılık gelir. Sunucu tarafı ağ bant genişliği sınırları için uyarı ayarlamak istiyorsanız bu `Cache Read` sayacını kullanarak oluşturun. Çeşitli önbellek fiyatlandırma katmanları ve boyutları için gözlenen bant genişliği sınırları için [Bu tabloya](cache-faq.md#cache-performance) bakın.** |
| Önbellek yazma |Belirtilen Raporlama aralığı sırasında önbelleğe saniye başına megabayt (MB/sn) cinsinden yazılan veri miktarı. Bu değer, önbelleği barındıran sanal makineyi destekleyen ve Redspecific olmayan ağ arabirimi kartlarından türetilir. Bu değer, istemciden önbelleğe gönderilen verilerin ağ bant genişliğine karşılık gelir. |
| Bağlı Istemciler |Belirtilen Raporlama aralığı sırasında önbelleğe yönelik istemci bağlantısı sayısı. Bu, Redsıs ıNFO komutundan `connected_clients` eşlenir. [Bağlantı sınırına](cache-configure.md#default-redis-server-configuration) ulaşıldığında önbelleğe yapılan bağlantı denemeleri başarısız olur. Etkin istemci uygulaması olmasa bile, iç süreçler ve bağlantılar nedeniyle bağlı istemcilerin birkaç örneği olabilir. |
| CPU |Belirtilen Raporlama aralığı sırasında bir yüzde olarak Redsıs sunucusu için Azure önbelleğinin CPU kullanımı. Bu değer, işletim sistemi `\Processor(_Total)\% Processor Time` performans sayacı ile eşlenir. |
| Hatalar | Belirli bir Raporlama aralığı sırasında önbelleğin karşılaştığı belirli sorunlar ve performans sorunları. Bu ölçüm, farklı hata türlerini temsil eden sekiz boyuta sahiptir, ancak gelecekte daha fazla eklenebilir. Şu anda temsil edilen hata türleri şunlardır: <br/><ul><li>**Yük devretme** – bir önbellek yük devretmediğinde (alt öğe ana için yükseltir)</li><li>**DataLoss** – önbellekte DataLoss olduğunda</li><li>**UnresponsiveClients** – istemciler sunucudan yeterince hızlı veri okumuyor</li><li>**AOF** : AOF kalıcılığı ile ilgili bir sorun olduğunda</li><li>**RDB** : RDB kalıcılığı ile ilgili bir sorun olduğunda</li><li>**Içeri aktarma** – RDB içeri aktarma ile ilgili bir sorun olduğunda</li><li>**Dışarı aktar** : RDB dışarı aktarma ile ilgili bir sorun olduğunda</li></ul> |
| Çıkarılan anahtarlar |`maxmemory` sınırı nedeniyle belirtilen Raporlama aralığı sırasında önbellekten çıkarılan öğe sayısı. Bu, Redsıs ıNFO komutundan `evicted_keys` eşlenir. |
| Süre dolma anahtarları |Belirtilen Raporlama aralığı sırasında önbellekten geçen öğe sayısı. Bu değer Redsıs ıNFO komutundan `expired_keys` eşlenir.|
| İyorsa |Belirtilen Raporlama aralığı sırasında önbellekten alınan alma işlemlerinin sayısı. Bu değer, Redsıs ıNFO All komutundan aşağıdaki değerlerin toplamıdır: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`ve `cmdstat_getrange`, ve Raporlama aralığı sırasında önbellek isabetlerinin ve isabetsizlik toplamına eşdeğerdir. |
| İşlem/saniye | Belirtilen Raporlama aralığı sırasında önbellek sunucusu tarafından saniye başına işlenen komutların toplam sayısı.  Bu değer Redsıs ıNFO komutundan "instantaneous_ops_per_sec" ile eşlenir. |
| Redsıs sunucu yükü |Reddir sunucusunun işleme meşgul olduğu ve iletiler için boşta beklememe döngülerinin yüzdesi. Bu sayaç 100 'e ulaşırsa, Redo sunucusu bir performans üst sınırına ulaştığında ve CPU daha hızlı çalışmayı işleyemez. Yüksek Redsıs sunucu yükünü görüyorsanız, zaman aşımı özel durumlarını istemcide görürsünüz. Bu durumda, verilerinizi birden çok önbellekte ölçeklendirmeyi veya Bölümlendirmeyi düşünmelisiniz. |
| Yapar |Belirtilen Raporlama aralığı sırasında önbelleğe yönelik ayarlanan işlem sayısı. Bu değer, Redsıs ıNFO All komutundan aşağıdaki değerlerin toplamıdır: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`ve `cmdstat_setnx`. |
| Toplam anahtar sayısı  | Geçmiş raporlama dönemi boyunca önbellekte bulunan en fazla anahtar sayısı. Bu, Redsıs ıNFO komutundan `keyspace` eşlenir. Temel alınan ölçümler sisteminin bir sınırlaması nedeniyle, kümelemenin etkinleştirildiği önbellekler için, toplam anahtar, Raporlama aralığı sırasında en fazla sayıda anahtara sahip parça en fazla anahtar sayısını döndürür.  |
| Toplam Işlem sayısı |Belirtilen Raporlama aralığı sırasında önbellek sunucusu tarafından işlenen komutların toplam sayısı. Bu değer Redsıs ıNFO komutundan `total_commands_processed` eşlenir. Redin için Azure Cache 'in yalnızca pub/Sub için kullanıldığı durumlarda `Cache Hits`, `Cache Misses`, `Gets`veya `Sets`için hiçbir ölçüm olmaz, ancak yayımlama/alt işlemler için önbellek kullanımını yansıtan `Total Operations` ölçümleri olacaktır. |
| Kullanılan bellek |Belirtilen Raporlama aralığı boyunca önbellekte bulunan anahtar/değer çiftleri için kullanılan önbellek bellek miktarı. Bu değer Redsıs ıNFO komutundan `used_memory` eşlenir. Bu, meta veri veya parçalama içermez. |
| Kullanılan bellek yüzdesi | Belirtilen Raporlama aralığı boyunca kullanılan toplam belleğin yüzdesi.  Bu, yüzdeyi hesaplamak için Redsıs ıNFO komutundan "used_memory" değerine başvurur. |
| Kullanılan bellek RSS |Parçalama ve meta veriler dahil olmak üzere, belirtilen Raporlama aralığı sırasında MB cinsinden kullanılan önbellek bellek miktarı. Bu değer Redsıs ıNFO komutundan `used_memory_rss` eşlenir. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Uyarılar
Ölçümlere ve etkinlik günlüklerine göre uyarı alacak şekilde yapılandırmanızı ayarlayabilirsiniz. Azure İzleyici, tetiklendiğinde aşağıdaki işlemleri yapmak üzere bir uyarı yapılandırmanıza olanak tanır:

* E-posta bildirimi gönderme
* Web kancası çağırma
* Bir Azure Mantıksal Uygulamasını çağırma

Önbelleğiniz için uyarı kurallarını yapılandırmak üzere **kaynak menüsünde** **Uyarı kuralları** ' na tıklayın.

![İzleme](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Uyarıları yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [uyarılara genel bakış](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Etkinlik Günlükleri
Etkinlik günlükleri, Redsıs örnekleri için Azure önbelleğiniz üzerinde gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Daha önce "Denetim günlükleri" veya "işletimsel Günlükler" olarak bilinirdi. Etkinlik günlüklerini kullanarak, Redsıs örnekleri için Azure önbelleğiniz üzerinde herhangi bir yazma işlemi (PUT, POST, DELETE) için "ne, kim ve ne zaman" i belirleyebilirsiniz. 

> [!NOTE]
> Etkinlik günlükleri okuma (GET) işlemlerini içermez.
>
>

Önbelleğiniz için etkinlik günlüklerini görüntülemek için **kaynak menüsünde** **etkinlik günlükleri** ' ne tıklayın.

Etkinlik günlükleri hakkında daha fazla bilgi için bkz. [Azure etkinlik günlüğü 'Ne genel bakış](../azure-monitor/platform/activity-logs-overview.md).











