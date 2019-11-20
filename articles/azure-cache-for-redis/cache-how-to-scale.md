---
title: Redsıs için Azure önbelleğini ölçeklendirme
description: Redsıs örnekleri için Azure önbelleğinizi ölçeklendirmeyi öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 04/11/2017
ms.author: yegu
ms.openlocfilehash: 0fe4092a93e34d6e6b4bb4c4441609f696518b86
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122130"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Redsıs için Azure önbelleğini ölçeklendirme
Redin için Azure önbelleğinde, önbellek boyutu ve özellikleri seçiminde esneklik sağlayan farklı önbellek teklifleri vardır. Bir önbellek oluşturulduktan sonra, uygulamanızın gereksinimlerinin değiştirilmesi durumunda önbelleğin boyutunu ve fiyatlandırma katmanını ölçeklendirebilirsiniz. Bu makalede, Azure portal ve Azure PowerShell ve Azure CLı gibi araçları kullanarak önbelleğinizi nasıl ölçekleyebilirsiniz.

## <a name="when-to-scale"></a>Ne zaman ölçeklendirme yapılmalıdır?
Redsıs için Azure cache [izleme](cache-how-to-monitor.md) özelliklerini kullanarak önbelleğinizin sistem durumunu ve performansını izleyebilir ve önbelleğin ne zaman ölçekleneceğini belirlemenize yardımcı olabilirsiniz. 

Ölçeklendirmeniz gerekip gerekmediğini belirlemenize yardımcı olması için aşağıdaki ölçümleri izleyebilirsiniz.

* Redsıs sunucu yükü
* Bellek kullanımı
* Ağ bant genişliği
* CPU kullanımı

Önbelleğinizin artık uygulamanızın gereksinimlerini karşılamadığını belirlerseniz, uygulamanız için uygun olan daha büyük veya daha küçük bir önbellek fiyatlandırma katmanına ölçeklendirebilirsiniz. Hangi önbellek fiyatlandırma katmanını kullanacağınızı belirleme hakkında daha fazla bilgi için bkz. [redne Için Azure Cache ve size ne kadar bellek kullanmalıyım?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="scale-a-cache"></a>Bir önbelleği ölçeklendirme
Önbelleğinizi ölçeklendirmek için [Azure Portal](https://portal.azure.com) [önbelleğe](cache-configure.md#configure-azure-cache-for-redis-settings) gidin ve **Kaynak menüsünden** **Ölçekle** ' ye tıklayın.

![Ölçek](./media/cache-how-to-scale/redis-cache-scale-menu.png)

**Fiyatlandırma katmanı Seç** dikey penceresinde istediğiniz fiyatlandırma katmanını seçin ve **Seç**' e tıklayın.

![Fiyatlandırma katmanı][redis-cache-pricing-tier-blade]


Aşağıdaki kısıtlamalara sahip farklı bir fiyatlandırma katmanına ölçeklendirebilirsiniz:

* Daha yüksek bir fiyatlandırma katmanından daha düşük bir fiyatlandırma katmanına ölçeklendiremez.
  * **Premium** önbellekten bir **Standart** veya **temel** önbelleğe ölçeklendiremez.
  * **Standart** bir önbellekten, **temel** bir önbellekten ölçeklendiremez.
* **Temel** bir önbellekten **Standart** bir önbelleğe ölçeklendirebilirsiniz, ancak aynı anda boyutu değiştiremezsiniz. Farklı bir boyuta ihtiyacınız varsa, sonraki ölçekleme işlemini istenen boyuta getirebilirsiniz.
* **Temel** önbellekten doğrudan **Premium** önbelleğe ölçeklendiremez. İlk olarak, bir ölçeklendirme işleminde **temel** olarak **Standart** ve sonra da sonraki ölçekleme işleminde **Standart** 'den **Premium** 'a ölçeklendirin.
* Daha büyük bir boyuttan **C0 (250 MB)** boyutuna kadar ölçeklendiremez.
 
Önbellek yeni fiyatlandırma katmanına ölçeklendirilirken, **redsıs dikey penceresinde Azure önbelleğinde** **ölçekleme** durumu görüntülenir.

![Ölçeklendirme][redis-cache-scaling]

Ölçeklendirme tamamlandığında, durum **ölçeklendirmeden** **çalışır**olarak değişir.

## <a name="how-to-automate-a-scaling-operation"></a>Ölçeklendirme işlemini otomatikleştirme
Azure portal önbellek örneklerinizi ölçeklendirmenin yanı sıra, PowerShell cmdlet 'leri, Azure CLı ve Microsoft Azure Yönetim kitaplıklarını (MAML) kullanarak ölçeklendirebilirsiniz. 

* [PowerShell kullanarak ölçeklendirme](#scale-using-powershell)
* [Azure CLı kullanarak ölçeklendirme](#scale-using-azure-cli)
* [MAML kullanarak ölçeklendirme](#scale-using-maml)

### <a name="scale-using-powershell"></a>PowerShell kullanarak ölçeklendirme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

`Size`, `Sku`veya `ShardCount` özellikleri değiştirildiğinde [set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) cmdlet 'Ini kullanarak PowerShell ile redsıs örnekleri Için Azure önbelleğinizi ölçekleyebilirsiniz. Aşağıdaki örnek, `myCache` adlı bir önbelleğin 2,5 GB önbellek olarak nasıl ölçeklendirilebilen gösterilmektedir. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

PowerShell ile ölçeklendirme hakkında daha fazla bilgi için bkz. [PowerShell kullanarak redsıs Için Azure önbelleğini ölçekleme](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Azure CLı kullanarak ölçeklendirme
Azure CLı kullanarak Redsıs örnekleri için Azure önbelleğinizi ölçeklendirmek üzere, istenen ölçeklendirme işlemine bağlı olarak, `azure rediscache set` komutunu çağırın ve yeni boyut, SKU veya küme boyutu içeren istenen yapılandırma değişikliklerini geçirin.

Azure CLı ile ölçeklendirme hakkında daha fazla bilgi için bkz. [redsıs için mevcut bir Azure önbelleğinin ayarlarını değiştirme](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>MAML kullanarak ölçeklendirme
[Microsoft Azure Yönetim kitaplıklarını (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)kullanarak redsıs örnekleri Için Azure önbelleğinizi ölçeklendirmek için, `IRedisOperations.CreateOrUpdate` metodunu çağırın ve `RedisProperties.SKU.Capacity`yeni boyutunu geçirin.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Daha fazla bilgi için bkz. [MAML Için Azure önbelleğini redml Ile yönetme](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) örneği.

## <a name="scaling-faq"></a>Ölçeklendirme hakkında SSS
Aşağıdaki liste, Redsıs ölçeklendirmesi için Azure önbelleği hakkında sık sorulan soruların yanıtlarını içerir.

* [Premium önbellekten ölçeklendirebilir miyim?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Ölçeklendirdikten sonra, önbellek adı veya erişim anahtarlarımı değiştirmem gerekiyor mu?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Ölçeklendirme nasıl çalışır?](#how-does-scaling-work)
* [Ölçeklendirme sırasında önbelleğim verileri kaybedecek mıyım?](#will-i-lose-data-from-my-cache-during-scaling)
* [Özel Veritabanlarım, ölçekleme sırasında etkileniyor mu?](#is-my-custom-databases-setting-affected-during-scaling)
* [Önbelleğim ölçekleme sırasında kullanılabilir olacak mı?](#will-my-cache-be-available-during-scaling)
* Coğrafi çoğaltma yapılandırıldığında, neden önbelleğinizi ölçeklendirmem veya bir kümedeki parçaları değiştirmem mümkün değil mi?
* [Desteklenmeyen işlemler](#operations-that-are-not-supported)
* [Ölçeklendirme ne kadar sürer?](#how-long-does-scaling-take)
* [Ölçeklendirmenin tamamlandığını nasıl anlayabilirim?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Premium önbellekten ölçeklendirebilir miyim?
* **Premium** önbellekten, **temel** veya **Standart** fiyatlandırma katmanına kadar ölçeklendiremez.
* Bir **Premium** önbellek fiyatlandırma katmanından diğerine ölçeklendirebilirsiniz.
* **Temel** önbellekten doğrudan **Premium** önbelleğe ölçeklendiremez. İlk olarak, bir ölçeklendirme işleminde **temel** olarak **Standart** ve sonra da sonraki ölçekleme işleminde **Standart** 'den **Premium** 'a ölçeklendirin.
* **Premium** önbelleğinizi oluştururken kümeleme 'yi etkinleştirdiyseniz, [küme boyutunu değiştirebilirsiniz](cache-how-to-premium-clustering.md#cluster-size). Önbelleğiniz kümeleme etkin olmadan oluşturulduysa, kümelendirmeyi daha sonra yapılandırabilirsiniz.
  
  Daha fazla bilgi için bkz. [redsıs Için Premium Azure önbelleği için kümeleme yapılandırma](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Ölçeklendirdikten sonra, önbellek adı veya erişim anahtarlarımı değiştirmem gerekiyor mu?
Hayır, bir ölçeklendirme işlemi sırasında önbellek adınız ve anahtarlarınız değiştirilmez.

### <a name="how-does-scaling-work"></a>Ölçeklendirme nasıl çalışır?
* **Temel** bir önbellek farklı bir boyuta ölçeklenirse, kapanır ve yeni bir önbellek yeni boyut kullanılarak sağlanır. Bu süre boyunca önbellek kullanılamaz ve önbellekteki tüm veriler kaybolur.
* **Temel** bir önbellek **Standart** bir önbelleğe ölçeklenirse, bir çoğaltma önbelleği sağlanır ve veriler birincil önbellekten çoğaltma önbelleğine kopyalanır. Önbellek ölçeklendirme işlemi sırasında kullanılabilir kalır.
* **Standart** bir önbellek farklı bir boyuta veya **Premium** bir önbelleğe ölçeklendiğinde, çoğaltmalardan biri kapatılır ve yeni boyut ve üzerinden aktarılan veriler için yeniden sağlanmış olur ve daha sonra, başka bir çoğaltma, yeniden sağlanmadan önce bir yük devretme gerçekleştirir ve bu işlem, önbellek düğümlerinden birinin başarısızlığı sırasında oluşan işleme benzer.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Ölçeklendirme sırasında önbelleğim verileri kaybedecek mıyım?
* **Temel** bir önbellek yeni bir boyuta ölçeklenirken, tüm veriler kaybedilir ve ölçekleme işlemi sırasında önbellek kullanılamaz.
* **Temel** bir önbellek **Standart** bir önbelleğe ölçeklenirse, önbellekteki veriler genellikle korunur.
* **Standart** önbellek daha büyük bir boyuta veya katmana ölçeklendirildiğinde veya **Premium** önbellek daha büyük bir boyuta ölçeklenirse, tüm veriler genellikle korunur. **Standart** veya **Premium** bir önbelleğin boyutunu daha küçük bir boyuta ölçeklendirirken veriler, ölçeklendiği sırada yeni boyutla ilgili önbellekte ne kadar veri olduğunu kaybetmiş olabilir. Ölçeği azaltma sırasında veriler kaybolursa, anahtarlar [AllKeys-LRU](https://redis.io/topics/lru-cache) çıkarma ilkesi kullanılarak kaldırılır. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Özel Veritabanlarım, ölçekleme sırasında etkileniyor mu?
Önbellek oluşturma sırasında `databases` ayarı için özel bir değer yapılandırdıysanız, bazı fiyatlandırma katmanlarının farklı [veritabanı sınırlarına](cache-configure.md#databases)sahip olduğunu aklınızda bulundurun. Bu senaryoda ölçeklendirilirken bazı konular aşağıda verilmiştir:

* Geçerli katmandan daha düşük bir `databases` sınırı olan bir fiyatlandırma katmanına ölçeklendirirken:
  * Tüm fiyatlandırma katmanları için 16 olan varsayılan `databases`sayısını kullanıyorsanız, hiçbir veri kaybolmaz.
  * Ölçeklendirilen katmana yönelik limitlerde yer alan özel sayıda `databases` kullanıyorsanız, bu `databases` ayarı korunur ve hiçbir veri kaybolmaz.
  * Yeni katmanın sınırlarını aşan özel sayıda `databases` kullanıyorsanız, `databases` ayarı yeni katmanın sınırlarına indirgenmiştir ve kaldırılan veritabanlarındaki tüm veriler kaybolur.
* Geçerli katmana göre aynı veya daha yüksek `databases` bir fiyatlandırma katmanına ölçeklendirirken, `databases` ayarınız korunur ve hiçbir veri kaybolmaz.

Standart ve Premium önbellekler kullanılabilirlik için% 99,9 SLA 'ya sahip olsa da veri kaybı için SLA yoktur.

### <a name="will-my-cache-be-available-during-scaling"></a>Önbelleğim ölçekleme sırasında kullanılabilir olacak mı?
* Ölçek işlemi sırasında **Standart** ve **Premium** önbellekler kullanılabilir durumda kalır. Ancak, standart ve Premium önbellekler ölçeklendirilirken ve ayrıca temel ve standart önbelleklere ölçeklendirilirken bağlantı sinyalleri 'ler meydana gelebilir. Bu bağlantı bliclarının küçük ve redsıs istemcilerinin, bağlantısını anında ayarlayabilmeleri gerekir.
* **Temel** önbellekler, ölçeklendirme işlemleri sırasında farklı bir boyuta göre çevrimdışı. Temel önbellekler **temel** ve **Standart** arasında ölçeklendirilirken kullanılabilir olmaya devam eder ancak küçük bir bağlantı BLIP ile karşılaşabilir. Bir bağlantı Blip oluşması durumunda, redsıs istemcilerinin, bağlantısını anında yeniden kurabilmesi gerekir.


### <a name="scaling-limitations-with-geo-replication"></a>Coğrafi çoğaltma ile ölçeklendirme sınırlamaları

İki önbellek arasında coğrafi çoğaltma bağlantısı ekledikten sonra bir ölçeklendirme işlemi başlatamaz veya bir kümedeki parça sayısını değiştiremeyeceksiniz. Bu komutları vermek için önbelleğin bağlantısını kaldırmanız gerekir. Daha fazla bilgi için bkz. [Coğrafi çoğaltmayı yapılandırma](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Desteklenmeyen işlemler
* Daha yüksek bir fiyatlandırma katmanından daha düşük bir fiyatlandırma katmanına ölçeklendiremez.
  * **Premium** önbellekten bir **Standart** veya **temel** önbelleğe ölçeklendiremez.
  * **Standart** bir önbellekten, **temel** bir önbellekten ölçeklendiremez.
* **Temel** bir önbellekten **Standart** bir önbelleğe ölçeklendirebilirsiniz, ancak aynı anda boyutu değiştiremezsiniz. Farklı bir boyuta ihtiyacınız varsa, sonraki ölçekleme işlemini istenen boyuta getirebilirsiniz.
* **Temel** önbellekten doğrudan **Premium** önbelleğe ölçeklendiremez. İlk olarak, tek bir ölçeklendirme işleminde **temel** ve **Standart** arasında ölçeklendirin ve sonraki bir işlemde **Standart** 'dan **Premium** 'a ölçeklendirin.
* Daha büyük bir boyuttan **C0 (250 MB)** boyutuna kadar ölçeklendiremez.

Bir ölçeklendirme işlemi başarısız olursa, hizmet işlemi döndürmeye çalışır ve önbellek özgün boyutuna döndürülür.


### <a name="how-long-does-scaling-take"></a>Ölçeklendirme ne kadar sürer?
Önbellekte ne kadar veri olduğuna bağlı olarak ölçekleme yaklaşık 20 dakika sürer.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Ölçeklendirmenin tamamlandığını nasıl anlayabilirim?
Azure portal ölçeklendirme işleminin devam ettiğini görebilirsiniz. Ölçeklendirme tamamlandığında, önbelleğin durumu **çalışıyor**olarak değişir.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



