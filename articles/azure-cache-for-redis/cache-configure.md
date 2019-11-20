---
title: Redsıs için Azure önbelleğini yapılandırma
description: Redsıs için Azure önbelleği için varsayılan Redsıs yapılandırmasını anlayın ve Redsıs örnekleri için Azure önbelleğinizi nasıl yapılandıracağınızı öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 7c0642377e75e621e1774936262ffddd166ff06d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122866"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Redsıs için Azure önbelleğini yapılandırma
Bu konuda, Redsıs örnekleri için Azure önbelleğiniz için kullanılabilen yapılandırma açıklanmaktadır. Bu konu, Redsıs örnekleri için Azure önbelleği için varsayılan Redsıs sunucu yapılandırmasını da içerir.

> [!NOTE]
> Premium önbellek özelliklerini yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [kalıcılığı yapılandırma](cache-how-to-premium-persistence.md), [kümeleme yapılandırma](cache-how-to-premium-clustering.md)ve [sanal ağ desteğini yapılandırma](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Redsıs ayarları için Azure önbelleğini yapılandırma
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Redsıs ayarları için Azure önbelleği, **Kaynak menüsü**kullanılarak **redsıs dikey penceresinde Azure önbelleğinde** görüntülenir ve yapılandırılır.

![Redsıs ayarları için Azure önbelleği](./media/cache-configure/redis-cache-settings.png)

**Kaynak menüsünü**kullanarak aşağıdaki ayarları görüntüleyebilir ve yapılandırabilirsiniz.

* [Genel bakış](#overview)
* [Etkinlik Günlüğü](#activity-log)
* [Erişim denetimi (ıAM)](#access-control-iam)
* [Etiketler](#tags)
* [Sorunları tanılama ve çözme](#diagnose-and-solve-problems)
* [Ayarlar](#settings)
    * [Erişim tuşları](#access-keys)
    * [Gelişmiş ayarlar](#advanced-settings)
    * [Redsıs Danışmanı için Azure önbelleği](#azure-cache-for-redis-advisor)
    * [Ölçeklendirme](#scale)
    * [Redsıs kümesi boyutu](#cluster-size)
    * [Redis veri kalıcılığı](#redis-data-persistence)
    * [Güncelleştirmeleri zamanlama](#schedule-updates)
    * [Coğrafi çoğaltma](#geo-replication)
    * [Sanal Ağ](#virtual-network)
    * [Güvenlik duvarı](#firewall)
    * [Özelliklerinin](#properties)
    * [Kaynaktaki](#locks)
    * [Otomasyon betiği](#automation-script)
* Yönetim
    * [Veri içeri aktarma](#importexport)
    * [Verileri dışarı aktarma](#importexport)
    * [Yeniden başlatma](#reboot)
* [İzleme](#monitoring)
    * [Redsıs ölçümleri](#redis-metrics)
    * [Uyarı kuralları](#alert-rules)
    * [Tanılama](#diagnostics)
* & Sorun giderme ayarlarını destekler
    * [Kaynak durumu](#resource-health)
    * [Yeni destek isteği](#new-support-request)


## <a name="overview"></a>Genel Bakış

**Genel bakış** , önbelleğiniz hakkında ad, bağlantı noktaları, fiyatlandırma katmanı ve seçili önbellek ölçümleri gibi temel bilgileri sağlar.

### <a name="activity-log"></a>Etkinlik günlüğü

Önbelleğiniz üzerinde gerçekleştirilen eylemleri görüntülemek için **etkinlik günlüğü** ' ne tıklayın. Bu görünümü diğer kaynakları içerecek şekilde genişletmek için filtrelemeyi de kullanabilirsiniz. Denetim günlükleriyle çalışma hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../azure-resource-manager/resource-group-audit.md). Redsıs olayları için Azure önbelleğini izleme hakkında daha fazla bilgi için bkz. [işlemler ve uyarılar](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Erişim denetimi (ıAM)

**Erişim denetimi (IAM)** bölümü Azure Portal rol tabanlı erişim denetımı (RBAC) için destek sağlar. Bu yapılandırma, kuruluşların erişim yönetimi gereksinimlerini yalnızca ve tam olarak karşıladığından yardımcı olur. Daha fazla bilgi için [Azure Portal rol tabanlı erişim denetimi](../role-based-access-control/role-assignments-portal.md)bölümüne bakın.

### <a name="tags"></a>Etiketler

**Etiketler** bölümü, kaynaklarınızı düzenlemenize yardımcı olur. Daha fazla bilgi için bkz. [Etiketleri kullanarak Azure kaynaklarınızı düzenleme](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Sorunları tanılama ve çözme

Tanıla ' ya tıklayın ve sorunları çözmeye yönelik yaygın sorunlar ve stratejileri sağlamak için **sorunları çözün** .



## <a name="settings"></a>Ayarlar
**Ayarlar** bölümü önbelleğiniz için aşağıdaki ayarlara erişip yapılandırmanıza olanak tanır.

* [Erişim tuşları](#access-keys)
* [Gelişmiş ayarlar](#advanced-settings)
* [Redsıs Danışmanı için Azure önbelleği](#azure-cache-for-redis-advisor)
* [Ölçeklendirme](#scale)
* [Redsıs kümesi boyutu](#cluster-size)
* [Redis veri kalıcılığı](#redis-data-persistence)
* [Güncelleştirmeleri zamanlama](#schedule-updates)
* [Coğrafi çoğaltma](#geo-replication)
* [Sanal Ağ](#virtual-network)
* [Güvenlik duvarı](#firewall)
* [Özelliklerinin](#properties)
* [Kaynaktaki](#locks)
* [Otomasyon betiği](#automation-script)



### <a name="access-keys"></a>Erişim anahtarları
Önbelleğiniz için erişim anahtarlarını görüntülemek veya yeniden oluşturmak için **erişim tuşları** ' na tıklayın. Bu anahtarlar, önbelleğinize bağlanan istemciler tarafından kullanılır.

![Redsıs erişim tuşları için Azure önbelleği](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Gelişmiş ayarlar
Aşağıdaki ayarlar **Gelişmiş ayarlar** dikey penceresinde yapılandırılır.

* [Erişim bağlantı noktaları](#access-ports)
* [Bellek ilkeleri](#memory-policies)
* [Keyspace bildirimleri (Gelişmiş ayarlar)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Erişim bağlantı noktaları
SSL olmayan erişim yeni önbellekler için varsayılan olarak devre dışı bırakılmıştır. SSL olmayan bağlantı noktasını etkinleştirmek için, **Gelişmiş ayarlar** dikey PENCERESINDE **yalnızca SSL aracılığıyla erişime izin ver** ' **e tıklayın ve** ardından **Kaydet**' e tıklayın.

> [!NOTE]
> Redsıs için Azure önbelleğine SSL erişimi varsayılan olarak TLS 1,0 ' i destekler. Desteklenen en düşük TLS sürümü, **Gelişmiş ayarlar** dikey penceresinde **En düşük TLS sürümü** AÇıLAN listesini kullanarak isteniyorsa TLS 1,2 ' e kadar yükseltilebilir ve ardından **Kaydet**' e tıklayabilirsiniz.

![Redsıs erişim bağlantı noktaları için Azure önbelleği](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Bellek ilkeleri
**Gelişmiş ayarlar** dikey penceresinde **MaxMemory ilkesi**, **MaxMemory-ayrılmış**ve **maxfragmentationmemory-ayrılmış** ayarları, önbelleğin bellek ilkelerini yapılandırır.

![Redsıs MaxMemory Ilkesi için Azure önbelleği](./media/cache-configure/redis-cache-maxmemory-policy.png)

**MaxMemory ilkesi** , önbellek için çıkarma ilkesini yapılandırır ve aşağıdaki çıkarma ilkeleri arasından seçim yapmanıza olanak tanır:

* `volatile-lru`-bu, varsayılan çıkarma ilkesidir.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

`maxmemory` ilkeleri hakkında daha fazla bilgi için bkz. [çıkarma ilkeleri](https://redis.io/topics/lru-cache#eviction-policies).

**MaxMemory-Reserve** ayarı, yük devretme sırasında çoğaltma gibi önbellekte olmayan işlemler için ayrılan bellek miktarını MB olarak yapılandırır. Bu değer ayarlandığında, yüklemeniz farklılık gösterdiği zaman daha tutarlı bir Redto sunucu deneyimi sağlar. Bu değer, yazma ağır olan iş yükleri için daha yüksek olarak ayarlanmalıdır. Bellek bu gibi işlemler için ayrıldığınızda, önbelleğe alınmış verilerin depolanması için kullanılamaz.

**Maxfragmentationmemory-ayrılmış** ayarı, bellek parçalanması için ayrılan bellek miktarını MB olarak yapılandırır. Bu değeri ayarlamak, önbellek dolduğunda veya dolduğunda, parçalanma oranı yüksek olduğunda daha tutarlı bir Redsıs sunucu deneyimine sahip olmasını sağlar. Bellek bu gibi işlemler için ayrıldığınızda, önbelleğe alınmış verilerin depolanması için kullanılamaz.

Yeni bir bellek ayırma değeri seçerken göz önünde bulundurmanız gereken tek şey (**MaxMemory-Reserve** veya **maxfragmentationmemory-Reserve**), bu değişikliğin zaten büyük miktarda verilerle çalışan bir önbelleği nasıl etkileyebileceğini gösterebilir. Örneğin, 49 GB veri içeren bir 53 GB önbelleğiniz varsa, ayırma değerini 8 GB olarak değiştirirseniz bu değişiklik, sistem için kullanılabilir en yüksek belleği 45 GB 'a düşürülecektir. Geçerli `used_memory` veya `used_memory_rss` değerleriniz 45 GB 'ın yeni sınırından yüksekse, hem `used_memory` hem de `used_memory_rss` 45 GB 'ın altında olmadığından, sistemin verileri çıkarabilmeleri gerekir. Çıkarma, sunucu yükü ve bellek parçalanmasını artırabilir. `used_memory` ve `used_memory_rss`gibi önbellek ölçümleri hakkında daha fazla bilgi için bkz. [kullanılabilir ölçümler ve raporlama aralıkları](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> **MaxMemory-ayrýlmýþ** ve **maxfragmentationmemory-ayrýlmýþ** ayarları yalnızca standart ve Premium önbellekler için kullanılabilir.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace bildirimleri (Gelişmiş ayarlar)
Redsıs anahtar alanı bildirimleri, **Gelişmiş ayarlar** dikey penceresinde yapılandırılır. Anahtar alanı bildirimleri, bazı olaylar gerçekleştiğinde istemcilerin bildirim almasına izin verir.

![Redsıs Gelişmiş ayarları için Azure önbelleği](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Anahtar alanı bildirimleri ve **bildirim-keyspace-olaylar** ayarı yalnızca standart ve Premium önbellekler için kullanılabilir.
> 
> 

Daha fazla bilgi için bkz. [Redsıs keyspace bildirimleri](https://redis.io/topics/notifications). Örnek kod için [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) örneğindeki [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) dosyasına bakın.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Redsıs Danışmanı için Azure önbelleği
**Redsıs Advisor dikey penceresinde Azure önbelleği** önbelleğiniz için öneriler görüntüler. Normal işlemler sırasında hiçbir öneri gösterilmez. 

![Öneriler](./media/cache-configure/redis-cache-no-recommendations.png)

Önbelleğinizin yüksek bellek kullanımı, ağ bant genişliği veya sunucu yükü gibi işlemleri sırasında herhangi bir koşul oluşursa, **redsıs dikey penceresinde Azure önbelleğinde** bir uyarı görüntülenir.

![Öneriler](./media/cache-configure/redis-cache-recommendations-alert.png)

**Öneriler** dikey penceresinde daha fazla bilgi bulunabilir.

![Öneriler](./media/cache-configure/redis-cache-recommendations.png)

Bu ölçümleri, **redsıs dikey penceresinde Azure önbelleğinin** [Izleme grafikleri](cache-how-to-monitor.md#monitoring-charts) ve [kullanım grafikleri](cache-how-to-monitor.md#usage-charts) bölümlerinde izleyebilirsiniz.

Her fiyatlandırma katmanı, istemci bağlantıları, bellek ve bant genişliği için farklı sınırlara sahiptir. Önbelleğiniz, bu ölçümler için devamlı bir süre boyunca maksimum kapasiteye yaklaşırsa, bir öneri oluşturulur. **Öneriler** aracı tarafından gözden geçirilen ölçümler ve sınırlar hakkında daha fazla bilgi için aşağıdaki tabloya bakın:

| Redin ölçümü için Azure önbelleği | Daha fazla bilgi |
| --- | --- |
| Ağ bant genişliği kullanımı |[Önbellek performansı kullanılabilir bant genişliği](cache-faq.md#cache-performance) |
| Bağlı istemciler |[Varsayılan Redsıs sunucu yapılandırması-MaxClients](#maxclients) |
| Sunucu yükü |[Kullanım grafikleri-Redsıs sunucu yükü](cache-how-to-monitor.md#usage-charts) |
| Bellek kullanımı |[Önbellek performansı-boyut](cache-faq.md#cache-performance) |

Önbelleğinizi yükseltmek için, fiyatlandırma katmanını değiştirmek ve önbelleğinizi [ölçeklendirmek](#scale) üzere **Şimdi Yükselt** ' e tıklayın. Fiyatlandırma Katmanı seçme hakkında daha fazla bilgi için bkz. [redne Için Azure önbelleğinin ve boyutun ne kullanmalıyım?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Ölçek
Önbelleğiniz için fiyatlandırma katmanını görüntülemek veya değiştirmek için **Ölçeklendir** ' e tıklayın. Ölçeklendirme hakkında daha fazla bilgi için bkz. [redsıs Için Azure önbelleğini ölçeklendirme](cache-how-to-scale.md).

![Redsıs fiyatlandırma katmanı için Azure önbelleği](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redsıs kümesi boyutu
Kümeleme etkin olan çalışan bir Premium önbelleğin küme boyutunu değiştirmek için **(Önizleme) Reddir kümesi boyutu** ' na tıklayın.

> [!NOTE]
> Redsıs Premium katmanının Azure önbelleğinin genel kullanıma sunulduğuna karşın, Redsıs küme boyutu özelliği şu anda önizlemededir.
> 
> 

![Redsıs kümesi boyutu](./media/cache-configure/redis-cache-redis-cluster-size.png)

Küme boyutunu değiştirmek için kaydırıcıyı kullanın veya parça **sayısı** metin kutusunda 1 ile 10 arasında bir sayı yazın ve kaydetmek için **Tamam** ' a tıklayın.

> [!IMPORTANT]
> Redsıs Kümelemesi yalnızca Premium önbellekler için kullanılabilir. Daha fazla bilgi için bkz. [redsıs Için Premium Azure önbelleği için kümeleme yapılandırma](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Redis veri kalıcılığı
Premium önbelleğiniz için veri kalıcılığını etkinleştirmek, devre dışı bırakmak veya yapılandırmak üzere **redsıs veri kalıcılığı** ' na tıklayın. Redsıs için Azure önbelleği, [RDB kalıcılığı](cache-how-to-premium-persistence.md#configure-rdb-persistence) veya [AOF kalıcılığı](cache-how-to-premium-persistence.md#configure-aof-persistence)kullanarak redsıs kalıcılığı sağlar.

Daha fazla bilgi için bkz. [redsıs Için Premium Azure önbelleği için kalıcılığı yapılandırma](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Redsıs veri kalıcılığı yalnızca Premium önbellekler için kullanılabilir.
> 
> 

### <a name="schedule-updates"></a>Güncelleştirmeleri zamanlama
**Güncelleştirmeleri zamanla** dikey penceresi önbelleğiniz için redsıs sunucu güncelleştirmeleri için bir bakım penceresi atamanıza olanak tanır. 

> [!IMPORTANT]
> Bakım penceresi yalnızca Redsıs sunucu güncelleştirmeleri için geçerlidir ve önbelleği barındıran VM 'lerin işletim sisteminde herhangi bir Azure güncelleştirmesi ya da güncelleştirmesi için geçerli değildir.
> 
> 

![Güncelleştirmeleri zamanlama](./media/cache-configure/redis-schedule-updates.png)

Bir bakım penceresi belirtmek için, istenen günleri denetleyin ve her gün için bakım penceresi başlangıç saatini belirtip **Tamam**' a tıklayın. Bakım penceresi saati UTC 'dir.

> [!IMPORTANT]
> **Güncelleştirmeleri zamanla** Işlevi yalnızca Premium katman önbellekleri için kullanılabilir. Daha fazla bilgi ve yönergeler için bkz. [redsıs yönetimi Için Azure önbelleği-zamanlama güncelleştirmeleri](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Coğrafi çoğaltma

**Coğrafi çoğaltma** dikey penceresi, redsıs örnekleri Için iki Premium katman Azure önbelleğinin bağlanmasına yönelik bir mekanizma sağlar. Bir önbellek, birincil bağlı önbellek ve diğeri ikincil bağlantılı önbellek olarak atanır. İkincil bağlantılı önbellek salt okunurdur ve birincil önbelleğe yazılan veriler ikincil bağlantılı önbelleğe çoğaltılır. Bu işlev, Azure bölgeleri arasında bir önbelleği çoğaltmak için kullanılabilir.

> [!IMPORTANT]
> **Coğrafi çoğaltma** yalnızca Premium katman önbellekler için kullanılabilir. Daha fazla bilgi ve yönergeler için bkz. [redsıs Için Azure önbelleği Için Coğrafi çoğaltmayı yapılandırma](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Sanal Ağ
**Sanal ağ** bölümü önbelleğiniz için sanal ağ ayarlarını yapılandırmanıza olanak tanır. VNET desteğiyle Premium önbellek oluşturma ve ayarlarını güncelleştirme hakkında daha fazla bilgi için bkz. [Premium Azure önbelleği Için sanal ağ desteğini redsıs için yapılandırma](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Sanal ağ ayarları yalnızca önbellek oluşturma sırasında VNET desteğiyle yapılandırılmış olan Premium önbellekler için kullanılabilir. 
> 
> 

### <a name="firewall"></a>Güvenlik duvarı

Güvenlik duvarı kuralları yapılandırması, Redsıs katmanlarında tüm Azure önbelleği için kullanılabilir.

Önbellek güvenlik duvarı kurallarını görüntülemek ve yapılandırmak için **güvenlik duvarı** ' na tıklayın.

![Güvenlik duvarı](./media/cache-configure/redis-firewall-rules.png)

Bir başlangıç ve bitiş IP adresi aralığı ile güvenlik duvarı kuralları belirtebilirsiniz. Güvenlik duvarı kuralları yapılandırıldığında, yalnızca belirtilen IP adresi aralıklarından gelen istemci bağlantıları önbelleğe bağlanabilir. Bir güvenlik duvarı kuralı kaydedildiğinde, kural yürürlüğe girmeden önce kısa bir gecikme olur. Bu gecikme genellikle bir dakikadan azdır.

> [!IMPORTANT]
> Güvenlik duvarı kuralları yapılandırılmış olsa bile, Redsıs izleme sistemlerine yönelik Azure önbelleğinden gelen bağlantılara her zaman izin verilir.
> 
> 

### <a name="properties"></a>Özellikler
Önbellek uç noktası ve bağlantı noktaları da dahil olmak üzere önbelleğiniz hakkındaki bilgileri görüntülemek için **Özellikler** ' e tıklayın.

![Redsıs özellikleri için Azure önbelleği](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Kilitler
**Kilitler** bölümü, kuruluşunuzdaki diğer kullanıcıların yanlışlıkla önemli kaynakları silmesini veya değiştirmesini engellemek için bir aboneliği, kaynak grubunu veya kaynağı kilitlemenizi sağlar. Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Otomasyon betiği

Gelecekteki dağıtımlar için dağıtılan kaynaklarınızın bir şablonunu derlemek ve dışarı aktarmak için **Otomasyon betiği** ' ne tıklayın. Şablonlarla çalışma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonlarıyla kaynak dağıtma](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Yönetim ayarları
**Yönetim** bölümündeki ayarlar önbelleğiniz için aşağıdaki yönetim görevlerini gerçekleştirmenize olanak tanır. 

![Yönetim](./media/cache-configure/redis-cache-administration.png)

* [Veri içeri aktarma](#importexport)
* [Verileri dışarı aktarma](#importexport)
* [Yeniden başlatma](#reboot)


### <a name="importexport"></a>İçeri/Dışarı Aktarma
İçeri/dışarı aktarma, redsıs veritabanı (RDB) anlık görüntüsüne yönelik bir Azure önbelleğini bir Azure depolama hesabındaki bir sayfa blobuna içeri aktarıp dışarı aktararak önbellekteki verileri içeri aktarıp dışa aktarmanıza izin veren Redsıs veri yönetimi işlemi için bir Azure önbelleğidir. İçeri/dışarı aktarma, Redsıs örnekleri için farklı Azure önbelleği arasında geçiş yapmanızı veya kullanılmadan önce verileri kullanarak önbelleğin doldurulmasını sağlar.

İçeri aktarma, Linux, Windows veya Amazon Web Services ve diğer bulut sağlayıcılarından sonra, herhangi bir bulutta veya ortamda çalışan redin uyumlu RDB dosyalarını getirmek için kullanılabilir. Verilerin içeri aktarılması, önceden doldurulmuş verilerle bir önbellek oluşturmanın kolay bir yoludur. İçeri aktarma işlemi sırasında Redsıs için Azure önbelleği, RDB dosyalarını Azure depolama alanından belleğe yükler ve ardından anahtarları önbelleğe ekler.

Dışarı aktarma işlemi, redin için Azure önbelleğinde depolanan verileri Redsıs uyumlu RDB dosyalarına vermenize olanak tanır. Bu özelliği, Redsıs örneği için bir Azure önbelleğinden diğerine veya başka bir Redsıs sunucusuna veri taşımak için kullanabilirsiniz. Dışarı aktarma işlemi sırasında, Redsıs sunucu örneği için Azure önbelleğini barındıran VM 'de geçici bir dosya oluşturulur ve dosya belirtilen depolama hesabına yüklenir. Dışarı aktarma işlemi başarı veya başarısızlık durumu ile tamamlandığında geçici dosya silinir.

> [!IMPORTANT]
> İçeri/dışarı aktarma yalnızca Premium katman önbellekler için kullanılabilir. Daha fazla bilgi ve yönergeler için bkz. [redsıs Için Azure önbelleğinde verileri içeri ve dışarı aktarma](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Yeniden başlatma
**Yeniden başlatma** dikey penceresi önbelleğiniz düğümlerini yeniden başlatmanızı sağlar. Bu yeniden başlatma özelliği, bir önbellek düğümünde hata oluşursa uygulamanızı dayanıklılık açısından test etmenizi sağlar.

![Yeniden başlatma](./media/cache-configure/redis-cache-reboot.png)

Kümelemenin etkinleştirildiği Premium bir önbelleğiniz varsa, önbelleğin hangi parçaları yeniden başlatılacağını seçebilirsiniz.

![Yeniden başlatma](./media/cache-configure/redis-cache-reboot-cluster.png)

Önbelleğinizin bir veya daha fazla düğümünü yeniden başlatmak için, istenen düğümleri seçin ve **Yeniden Başlat**' a tıklayın. Kümelendirmeyi etkin bir Premium önbelleğiniz varsa, yeniden başlatılacak olan parça (ler) i seçin ve ardından **Yeniden Başlat**' a tıklayın. Birkaç dakika sonra, seçilen düğüm (ler) i yeniden başlatılır ve birkaç dakika sonra yeniden çevrimiçi olacak.

> [!IMPORTANT]
> Yeniden başlatma artık tüm fiyatlandırma katmanlarında kullanılabilir. Daha fazla bilgi ve yönergeler için bkz. [redsıs yönetimi Için Azure önbelleği-yeniden başlatma](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>İzleme

**İzleme** bölümü Redsıs Için Azure önbelleğiniz için tanılamayı ve izlemeyi yapılandırmanıza olanak tanır. Redsıs izleme ve Tanılama için Azure önbelleği hakkında daha fazla bilgi için bkz. [redsıs Için Azure önbelleğini izleme](cache-how-to-monitor.md).

![Tanılama](./media/cache-configure/redis-cache-diagnostics.png)

* [Redsıs ölçümleri](#redis-metrics)
* [Uyarı kuralları](#alert-rules)
* [Tanılama](#diagnostics)

### <a name="redis-metrics"></a>Redsıs ölçümleri
Önbelleğiniz için [ölçümleri görüntülemek](cache-how-to-monitor.md#view-cache-metrics) üzere **redsıs ölçümleri ' ne** tıklayın.

### <a name="alert-rules"></a>Uyarı kuralları

Redsıs ölçümleri için Azure önbelleği temelinde uyarıları yapılandırmak için **Uyarı kuralları** ' na tıklayın. Daha fazla bilgi için bkz. [Uyarılar](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Tanılama

Varsayılan olarak, Azure Izleyici 'deki önbellek ölçümleri [30 gün boyunca depolanır](../azure-monitor/platform/data-platform-metrics.md) ve sonra silinir. Önbellek ölçümlerini 30 günden daha uzun süreyle kalıcı hale getirmek için, önbellek tanılamayı depolamak için kullanılan [Depolama hesabını yapılandırmak](cache-how-to-monitor.md#export-cache-metrics) üzere **Tanılamalar** ' e tıklayın.

>[!NOTE]
>Önbellek ölçümlerinizi depolamaya arşivleme ek olarak, [bunları bir olay hub 'ına de bağlayabilir veya Azure izleyici günlüklerine gönderebilirsiniz](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>& Sorun giderme ayarlarını destekler
**Destek + sorun giderme** bölümündeki ayarlar, önbelleğiyle ilgili sorunları çözmeye yönelik seçenekler sağlar.

![Destek ve sorun giderme](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Kaynak durumu](#resource-health)
* [Yeni destek isteği](#new-support-request)

### <a name="resource-health"></a>Kaynak durumu
**Kaynak sistem durumu** , kaynağınızı izler ve beklendiği gibi çalışıp çalışmadığını bildirir. Azure Kaynak sistem durumu hizmeti hakkında daha fazla bilgi için bkz. [Azure Kaynak durumu genel bakış](../resource-health/resource-health-overview.md).

> [!NOTE]
> Kaynak sistem durumu şu anda bir sanal ağda barındırılan Redsıs örnekleri için Azure önbelleğinin sistem durumunu bildiremedi. Daha fazla bilgi için bkz. [BIR VNET 'te önbellek barındırırken tüm önbellek özelliklerini çalışma](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Yeni destek isteği
Önbelleğiniz için destek isteği açmak üzere **Yeni destek isteği** ' ne tıklayın.





## <a name="default-redis-server-configuration"></a>Varsayılan Redsıs sunucu yapılandırması
Redsıs örnekleri için yeni Azure önbelleği, aşağıdaki varsayılan Redsıs yapılandırma değerleriyle yapılandırılır:

> [!NOTE]
> Bu bölümdeki ayarlar `StackExchange.Redis.IServer.ConfigSet` yöntemi kullanılarak değiştirilemez. Bu yöntem, bu bölümdeki komutlardan biriyle çağrılırsa, aşağıdaki örneğe benzer bir özel durum oluşur:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> **Maksimum bellek ilkesi**gibi yapılandırılabilir değerler, Azure CLI veya PowerShell gibi Azure Portal veya komut satırı yönetim araçları aracılığıyla yapılandırılabilir.
> 
> 

| Ayar | Varsayılan değer | Açıklama |
| --- | --- | --- |
| `databases` |16 |Varsayılan veritabanı sayısı 16 ' dır, ancak fiyatlandırma katmanını temel alarak farklı bir sayı yapılandırabilirsiniz. <sup>1</sup> varsayılan veritabanı DB 0 ' dır, `dbid` `0` ve `databases - 1`arasında bir sayı olduğu `connection.GetDatabase(dbid)` kullanarak bağlantı başına temelinde farklı bir tane seçebilirsiniz. |
| `maxclients` |Fiyatlandırma katmanına bağlıdır<sup>2</sup> |Bu değer, aynı anda izin verilen en fazla bağlı istemci sayısıdır. Sınıra ulaşıldıktan sonra, ' en fazla istemci sayısına ulaşıldı ' hatası döndüren tüm yeni bağlantıları kapatır. |
| `maxmemory-policy` |`volatile-lru` |MaxMemory ilkesi, `maxmemory` (önbellek oluştururken seçtiğiniz önbellek sunumunun boyutu) ulaşıldığında redin neyin kaldırılacağını seçen ayardır. Redl için Azure Cache ile, bir LRU algoritması kullanarak bir süre sonu kümesi olan anahtarları kaldıran `volatile-lru`varsayılan ayardır. Bu ayar Azure portal yapılandırılabilir. Daha fazla bilgi için bkz. [bellek ilkeleri](#memory-policies). |
| `maxmemory-samples` |3 |Belleği kaydetmek için, LRU ve en düşük TTL algoritmaları, kesin algoritmalar yerine yaklaşık algoritmalardır. Varsayılan olarak redin üç anahtarı denetler ve en kısa süre önce kullanılan olanı seçer. |
| `lua-time-limit` |5\.000 |Bir Lua betiğinin en uzun yürütme süresi (milisaniye). Maksimum yürütme süresine ulaşıldığında, Redsıs, bir betiğin izin verilen en uzun süre sonunda hala yürütülmeye devam ettiği ve bir hata ile sorguları yanıtlamaya başlar. |
| `lua-event-limit` |500 |Betik olay kuyruğunun en büyük boyutu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |İstemci çıkış arabelleği sınırları, bir nedenden dolayı sunucudan veri okumayan istemcilerin bağlantısını kesmeyi zorlamak için kullanılabilir (yaygın bir nedenden dolayı bir yayın/alt istemci, yayımcı tarafından bu kadar hızlı bir şekilde ileti tüketemez). Daha fazla bilgi için bkz. [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>`databases` limiti redsıs fiyatlandırma katmanının her bir Azure önbelleği için farklıdır ve önbellek oluşturma sırasında ayarlanabilir. Önbellek oluşturma sırasında `databases` ayarı belirtilmemişse, varsayılan değer 16 ' dır.

* Temel ve standart önbellekler
  * C0 (250 MB) önbellek-16 veritabanına kadar
  * C1 (1 GB) önbellek-16 veritabanına kadar
  * C2 (2,5 GB) önbellek-16 veritabanına kadar
  * C3 (6 GB) önbellek-16 veritabanına kadar
  * C4 (13 GB) önbellek-32 veritabanına kadar
  * C5 (26 GB) önbellek-48 veritabanına kadar
  * C6 (53 GB) önbellek-en fazla 64 veritabanı
* Premium önbellekler
  * P1 (6 GB-60 GB)-16 veritabanına kadar
  * P2 (13 GB-130 GB)-en fazla 32 veritabanı
  * P3 (26 GB-260 GB)-en fazla 48 veritabanı
  * P4 (53 GB-530 GB)-en fazla 64 veritabanı
  * Redsıs kümesi etkin olan tüm Premium önbellekler-Redsıs kümesi yalnızca 0 veritabanının kullanımını destekler, böylece Redsıs kümesi etkin olan herhangi bir Premium önbellek için `databases` sınırı etkin bir şekilde 1 olur ve [Select](https://redis.io/commands/select) komutuna izin verilmez. Daha fazla bilgi için bkz. [kümelendirmeyi kullanmak için istemci Uygulamam üzerinde herhangi bir değişiklik yapmam gerekir mi?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Veritabanları hakkında daha fazla bilgi için bkz. [redsıs veritabanları nelerdir?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> `databases` ayarı yalnızca önbellek oluşturma sırasında ve yalnızca PowerShell, CLı veya diğer yönetim istemcileri kullanılarak yapılandırılabilir. PowerShell kullanarak önbellek oluşturma sırasında `databases` yapılandırma örneği için, bkz. [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup>`maxclients`, redsıs fiyatlandırma katmanının her bir Azure önbelleği için farklıdır.

* Temel ve standart önbellekler
  * C0 (250 MB) önbellek-256 ' e kadar bağlantı
  * C1 (1 GB) önbellek-1.000 bağlantı
  * C2 (2,5 GB) önbellek-2.000 ' e kadar bağlantı
  * C3 (6 GB) önbellek-5.000 'e kadar bağlantı
  * C4 (13 GB) önbellek-10.000 bağlantı
  * C5 (26 GB) önbellek-15.000 bağlantı
  * C6 (53 GB) önbellek-20.000 ' e kadar bağlantı
* Premium önbellekler
  * P1 (6 GB-60 GB)-en fazla 7.500 bağlantı
  * P2 (13 GB-130 GB)-en fazla 15.000 bağlantı
  * P3 (26 GB-260 GB)-en fazla 30.000 bağlantı
  * P4 (53 GB-530 GB)-en fazla 40.000 bağlantı

> [!NOTE]
> Her önbellek boyutu belirli sayıda bağlantıya *izin verdiğinden,* redde her bağlantı ile ilişkili ek yük vardır. Bu ek yükün bir örneği, TLS/SSL şifreleme sonucu olarak CPU ve bellek kullanımı olabilir. Belirli bir önbellek boyutu için en fazla bağlantı sınırı, hafif bir şekilde yüklenmiş önbelleği varsayar. Bağlantı yüklerinden yükleme, istemci *işlemlerinden yükleme,* sistem kapasitesini aşarsa, geçerli önbellek boyutu için bağlantı sınırını aşmasanız bile önbellek, kapasite sorunları yaşayabilir.
> 
> 



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redsıs için Azure önbelleğinde redsıs komutları desteklenmez
> [!IMPORTANT]
> Redsıs örnekleri için Azure önbelleğinin yapılandırması ve yönetimi Microsoft tarafından yönetildiğinden, aşağıdaki komutlar devre dışı bırakılmıştır. Bunları çağırmaya çalışırsanız, `"(error) ERR unknown command"`benzer bir hata iletisi alırsınız.
> 
> * BGREWRITEAOF
> * BGSAVE
> * KURULUMUNUN
> * H
> * GEÇIREMEZSINIZ
> * Kaydet
> * Event
> * SLA 'lar
> * KÜME kümesi yazma komutları devre dışı bırakıldı, ancak salt okunurdur küme komutlarına izin verilir.
> 
> 

Redsıs komutları hakkında daha fazla bilgi için bkz. [https://redis.io/commands](https://redis.io/commands).

## <a name="redis-console"></a>Redsıs konsolu
Redsıs örnekleri için Azure önbelleğiniz için, tüm önbellek katmanlarında Azure portal kullanılabilen **Redsıs konsolunu**kullanarak komutları güvenli bir şekilde verebilirsiniz.

> [!IMPORTANT]
> - Redin Konsolu [VNET](cache-how-to-premium-vnet.md)ile çalışmaz. Önbelleğiniz bir sanal ağın parçası olduğunda, yalnızca VNET 'teki istemciler önbelleğe erişebilir. Redsıs konsolu, sanal ağ dışında bir yerel tarayıcınızda çalıştığından, önbelleğinize bağlanamaz.
> - Redsıs için Azure önbelleğinde tüm Redın komutları desteklenmez. Redin için Azure önbelleği için devre dışı bırakılan Redsıs komutlarının bir listesi için, [redsıs Için Azure önbelleğinde desteklenmeyen önceki redin komutlarına](#redis-commands-not-supported-in-azure-cache-for-redis) bakın. Redsıs komutları hakkında daha fazla bilgi için bkz. [https://redis.io/commands](https://redis.io/commands).
> 
> 

Redsıs konsoluna erişmek için, **redsıs** dikey penceresinde **konsol** ' a tıklayın.

![Redsıs konsolu](./media/cache-configure/redis-console-menu.png)

Önbellek Örneğinizde komut vermek için konsola istenen komutu yazın.

![Redsıs konsolu](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Redsıs konsolunu Premium kümelenmiş önbelleğiyle kullanma

Reddo konsolunu Premium kümelenmiş önbellek ile kullanırken, tek bir parça için komut oluşturabilirsiniz. Belirli bir parça için bir komut vermek istiyorsanız, önce parça seçicide tıklayarak istenen parçaya bağlanın.

![Redsıs konsolu](./media/cache-configure/redis-console-premium-cluster.png)

Bağlı parçadan farklı bir parça içinde depolanan bir anahtara erişmeyi denerseniz, aşağıdaki iletiye benzer bir hata iletisi alırsınız:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

Önceki örnekte, parça 1 seçili parçadır, ancak `myKey`, hata iletisinin `(shard 0)` bölümü tarafından gösterildiği gibi parça 0 ' da bulunur. Bu örnekte, `myKey`erişmek için, parça seçiciyi kullanarak parça 0 ' ı seçin ve ardından istediğiniz komutu verin.


## <a name="move-your-cache-to-a-new-subscription"></a>Önbelleğinizi yeni bir aboneliğe taşıma
**Taşı**' ya tıklayarak önbelleğinizi yeni bir aboneliğe taşıyabilirsiniz.

![Redsıs için Azure önbelleğini taşıma](./media/cache-configure/redis-cache-move.png)

Kaynakları bir kaynak grubundan diğerine ve bir abonelikten diğerine taşıma hakkında daha fazla bilgi için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Sonraki adımlar
* Redsıs komutlarıyla çalışma hakkında daha fazla bilgi için bkz. [redsıs komutlarını nasıl çalıştırabilirim?](cache-faq.md#how-can-i-run-redis-commands)

