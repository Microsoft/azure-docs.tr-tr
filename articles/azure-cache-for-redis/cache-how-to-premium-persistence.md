---
title: Redsıs için Premium Azure önbelleği için veri kalıcılığını yapılandırma
description: Premium katman Azure önbelleğinizi Redsıs örnekleri için yapılandırma ve yönetme hakkında bilgi edinin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: yegu
ms.openlocfilehash: b74a16735b44d081a79b17716bdbc72357a36013
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122732"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Redsıs için Premium Azure önbelleği için veri kalıcılığını yapılandırma
Redsıs için Azure önbelleğinde, kümeleme, kalıcılık ve sanal ağ desteği gibi Premium katman özellikleri de dahil olmak üzere, önbellek boyutu ve özellikleri seçimine esneklik sağlayan farklı önbellek teklifleri vardır. Bu makalede, Redsıs örneği için Premium bir Azure önbelleğinde kalıcılığı yapılandırma açıklanmaktadır.

Diğer Premium önbellek özellikleri hakkında daha fazla bilgi için bkz. [Redsıs Premium katmanı Için Azure önbelleğine giriş](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Veri kalıcılığı nedir?
[Redsıs kalıcılığı](https://redis.io/topics/persistence) , redin içinde depolanan verileri kalıcı hale getirebilmeniz için izin verir. Ayrıca, anlık görüntüler alabilir ve verileri yedekleyebilir ve bu da donanım arızası durumunda yükleyebilirsiniz. Bu, tüm verilerin bellekte depolandığı temel veya standart katmandan çok büyük bir avantajdır ve önbellek düğümlerinin kapatılmasında oluşabilecek bir hata olması durumunda olası veri kaybı olabilir. 

Redsıs için Azure önbelleği, aşağıdaki modelleri kullanarak Redsıs kalıcılığı sağlar:

* **RDB kalıcılığı** -RDB (redsıs veritabanı) kalıcılığı yapılandırıldığında, Redsıs Için Azure önbelleği, yapılandırılabilir bir yedekleme sıklığına bağlı olarak redsıs binary biçimindeki redin Için Azure önbelleğinin bir anlık görüntüsünü diske devam ettirir. Hem birincil hem de çoğaltma önbelleğini devre dışı bırakan çok zararlı bir olay oluşursa, önbellek en son anlık görüntü kullanılarak yeniden oluşturulur. RDB kalıcılığı 'nin [avantajları](https://redis.io/topics/persistence#rdb-advantages) ve [dezavantajları](https://redis.io/topics/persistence#rdb-disadvantages) hakkında daha fazla bilgi edinin.
* **AOF kalıcılığı** -AOF (yalnızca ekleme dosyası) kalıcılığı yapılandırıldığında, reddir Için Azure önbelleği, her yazma işlemini saniyede en az bir kez bir Azure depolama hesabına kaydeder. Hem birincil hem de çoğaltma önbelleğini devre dışı bırakan çok zararlı bir olay oluşursa, önbellek, depolanan yazma işlemleri kullanılarak yeniden oluşturulur. AOF kalıcılığın [avantajları](https://redis.io/topics/persistence#aof-advantages) ve [dezavantajları](https://redis.io/topics/persistence#aof-disadvantages) hakkında daha fazla bilgi edinin.

Kalıcı hale getirme, önbellek oluşturma sırasında ve mevcut Premium önbellekler için **kaynak menüsünde** **yeni Azure önbelleğinden redin** dikey penceresinden yapılandırılır.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Premium fiyatlandırma katmanı seçildikten sonra **redsıs kalıcılığı ' ne**tıklayın.

![Redsıs kalıcılığı][redis-cache-persistence]

Sonraki bölümdeki adımlarda, yeni Premium önbelleğinizin Redsıs kalıcılığı nasıl yapılandırılacağı açıklanır. Redsıs kalıcılığı yapılandırıldıktan sonra, yeni Premium önbelleğinizi Redsıs kalıcılığı ile oluşturmak için **Oluştur** ' a tıklayın.

## <a name="enable-redis-persistence"></a>Redsıs kalıcılığını etkinleştir

Redsıs kalıcılığı, **RDB** ya da **AOF** kalıcılığı seçerek **redsıs veri kalıcılığı** dikey penceresinde etkinleştirilir. Yeni önbellekler için, önceki bölümde açıklandığı gibi, önbellek oluşturma işlemi sırasında bu dikey pencereye erişilir. Mevcut önbellekler için, **redsıs veri kalıcılığı** dikey penceresine önbelleğiniz için **Kaynak menüsünden** erişilir.

![Redsıs ayarları][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>RDB kalıcılığını yapılandırma

RDB kalıcılığı etkinleştirmek için **RDB**' e tıklayın. Daha önce etkinleştirilmiş bir Premium önbellekte RDB kalıcılığı devre dışı bırakmak için **devre dışı**' ya tıklayın.

![Redsıs RDB kalıcılığı][redis-cache-rdb-persistence]

Yedekleme aralığını yapılandırmak için, açılan listeden bir **yedekleme sıklığı** seçin. Seçimler **15 dakika**, **30 dakika**, **60 dakika**, **6 saat**, **12 saat**ve **24 saat**içerir. Önceki yedekleme işlemi başarıyla tamamlandıktan sonra ve yeni bir yedekleme tamamlandığında bu Aralık sona erdiğinde, bu Aralık başlatılır.

Depolama **hesabı** ' na tıklayarak kullanılacak depolama hesabını seçin ve **depolama anahtarı** açılan listesinden kullanılacak **birincil anahtarı** ya da **İkincil anahtarı** seçin. Önbellek ile aynı bölgede bir depolama hesabı seçmeniz gerekir ve Premium depolamada daha yüksek aktarım hızı bulunduğundan **Premium Depolama** hesabı önerilir. 

> [!IMPORTANT]
> Kalıcılık hesabınız için depolama anahtarı yeniden oluşturulursa, istenen anahtarı **depolama anahtarı** açılır listesinden yeniden yapılandırmanız gerekir.
> 
> 

Kalıcılık yapılandırmasını kaydetmek için **Tamam** ' ı tıklatın.

Yedekleme sıklığı aralığı geçtiğinde sonraki yedekleme (veya yeni önbelleklere ilk yedekleme) başlatılır.

## <a name="configure-aof-persistence"></a>AOF kalıcılığı yapılandırma

AOF kalıcılığı etkinleştirmek için **AOF**öğesine tıklayın. Önceden etkinleştirilmiş bir Premium önbellekte kalıcılığı devre dışı bırakmak için **devre dışı**' ya tıklayın.

![Redin AOF kalıcılığı][redis-cache-aof-persistence]

AOF kalıcılığı yapılandırmak için bir **Ilk depolama hesabı**belirtin. Bu depolama hesabı, önbellek ile aynı bölgede olmalıdır ve Premium depolamada daha yüksek aktarım hızı bulunduğundan **Premium Depolama** hesabı önerilir. İsteğe bağlı olarak, **Ikinci depolama hesabı**adlı ek bir depolama hesabı yapılandırabilirsiniz. İkinci bir depolama hesabı yapılandırılmışsa, çoğaltma önbelleğine yazma işlemleri bu ikinci depolama hesabına yazılır. Yapılandırılmış her depolama hesabı için, **depolama anahtarı** açılan listesinden kullanılacak **birincil anahtarı** veya **İkincil anahtarı** seçin. 

> [!IMPORTANT]
> Kalıcılık hesabınız için depolama anahtarı yeniden oluşturulursa, istenen anahtarı **depolama anahtarı** açılır listesinden yeniden yapılandırmanız gerekir.
> 
> 

BIR dizi kalıcılığı etkinleştirildiğinde, önbelleğe yazma işlemleri belirlenen depolama hesabına (veya ikinci bir depolama hesabı yapılandırdıysanız hesaplar) kaydedilir. Hem birincil hem de çoğaltma önbelleğinin kullanıldığı çok önemli bir hata durumunda, önbelleğin yeniden oluşturulması için depolanan AOF günlüğü kullanılır.

## <a name="persistence-faq"></a>Kalıcılık SSS
Aşağıdaki liste, Redsıs kalıcılığı için Azure önbelleği hakkında sık sorulan soruların yanıtlarını içerir.

* [Daha önce oluşturulmuş bir önbellekte kalıcılığı etkinleştirebilir miyim?](#can-i-enable-persistence-on-a-previously-created-cache)
* [AOF ve RDB kalıcılığını aynı anda etkinleştirebilir miyim?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Hangi Kalıcılık modelini seçmem gerekir?](#which-persistence-model-should-i-choose)
* [Farklı bir boyuta ölçeklendirdiğimde ve ölçekleme işleminden önce yapılmış bir yedekleme geri yüklendiğinde ne olur?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB kalıcılığı
* [Önbelleği oluşturduktan sonra RDB yedekleme sıklığını değiştirebilir miyim?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [60 dakikalık bir RDB yedekleme sıklığım neden olursa yedeklemeler arasında 60 dakikadan fazla olur?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Yeni bir yedekleme yapıldığında eski RDB yedeklemelere ne olur?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF kalıcılığı
* [İkinci bir depolama hesabı ne zaman kullanmalıyım?](#when-should-i-use-a-second-storage-account)
* [Önbelleğim genelinde, gecikme süresine veya performansumu etkiler mi?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [İkinci Depolama hesabını nasıl kaldırabilirim?](#how-can-i-remove-the-second-storage-account)
* [Yeniden yazma nedir ve önbelleğinizi nasıl etkiler?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [AOF özellikli bir önbelleği ölçeklendirirken ne beklenmem gerekir?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Amy verileri depolama alanında nasıl düzenlenir?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Daha önce oluşturulmuş bir önbellekte kalıcılığı etkinleştirebilir miyim?
Evet, Redsıs kalıcılığı hem önbellek oluşturma hem de mevcut Premium önbellekler için yapılandırılabilir.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>AOF ve RDB kalıcılığını aynı anda etkinleştirebilir miyim?

Hayır, aynı anda yalnızca RDB veya AOF 'ı etkinleştirebilirsiniz.

### <a name="which-persistence-model-should-i-choose"></a>Hangi Kalıcılık modelini seçmem gerekir?

AOF kalıcılığı her yazma işlemi, performansı en az etkiyle yapılandırılan yedekleme aralığına göre kaydeden RDB kalıcılığı ile karşılaştırıldığında bir günlüğe yazma işlemlerini kaydeder. Birincil hedefiniz veri kaybını en aza indirmektir ve önbelleğiniz için bir azalmayı işleyebiliyorsanız, AOF kalıcılığı seçin. Önbelleğiniz üzerinde en iyi verimi sürdürmek istiyorsanız, ancak yine de veri kurtarma mekanizması istiyorsanız RDB kalıcılığı ' ni seçin.

* RDB kalıcılığı 'nin [avantajları](https://redis.io/topics/persistence#rdb-advantages) ve [dezavantajları](https://redis.io/topics/persistence#rdb-disadvantages) hakkında daha fazla bilgi edinin.
* AOF kalıcılığın [avantajları](https://redis.io/topics/persistence#aof-advantages) ve [dezavantajları](https://redis.io/topics/persistence#aof-disadvantages) hakkında daha fazla bilgi edinin.

AOF kalıcılığı kullanırken performans hakkında daha fazla bilgi için bkz. [önbelleğim genelinde, gecikme süresini veya performansını nasıl etkiler?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Farklı bir boyuta ölçeklendirdiğimde ve ölçekleme işleminden önce yapılmış bir yedekleme geri yüklendiğinde ne olur?

Hem RDB hem de sürekliliği için:

* Daha büyük bir boyuta ölçeklendirdiyseniz, hiçbir etkisi olmaz.
* Daha küçük bir boyuta ölçeklendirdiyseniz ve yeni boyutunuz için [veritabanları sınırından](cache-configure.md#databases) daha büyük bir özel [Veritabanınız](cache-configure.md#databases) ayarınız varsa, bu veritabanlarındaki veriler geri yüklenmez. Daha fazla bilgi için bkz. [ölçeklendirme sırasında özel Veritabanlarım ayarlarım etkilendi mi?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Daha küçük bir boyuta ölçeklendirdiyseniz ve son yedeklemeden tüm verileri tutmak için daha küçük boyutta bir boşluk yoksa, anahtarlar geri yükleme işlemi sırasında, genellikle [AllKeys-LRU](https://redis.io/topics/lru-cache) çıkarma ilkesi kullanılarak çıkarılacaktır.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Önbelleği oluşturduktan sonra RDB yedekleme sıklığını değiştirebilir miyim?
Evet, **redsıs veri kalıcılığı** DIKEY penceresinde RDB kalıcılığı için yedekleme sıklığını değiştirebilirsiniz. Yönergeler için bkz. Redsıs kalıcılığını yapılandırma.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>60 dakikalık bir RDB yedekleme sıklığım neden olursa yedeklemeler arasında 60 dakikadan fazla olur?
Bir önceki yedekleme işlemi başarıyla tamamlanana kadar RDB Kalıcılık yedekleme sıklığı aralığı başlatılmaz. Yedekleme sıklığı 60 dakikadır ve başarılı bir şekilde tamamlanışında bir yedekleme işlemi 15 dakika sürüyorsa, sonraki yedekleme, önceki yedeklemenin başlangıç zamanından sonra 75 dakika sonrasına kadar başlamaz.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Yeni bir yedekleme yapıldığında eski RDB yedeklemelere ne olur?
En güncel olanı hariç tüm RDB Kalıcılık yedeklemeleri otomatik olarak silinir. Bu silme işlemi hemen gerçekleşmeyebilir, ancak eski yedeklemeler süresiz olarak kalıcı olmaz.


### <a name="when-should-i-use-a-second-storage-account"></a>İkinci bir depolama hesabı ne zaman kullanmalıyım?

Önbellekte beklenen küme işlemlerinden daha yüksek olduğunu düşünüyorsanız, AOF kalıcılığı için ikinci bir depolama hesabı kullanmanız gerekir.  İkincil depolama hesabının ayarlanması, önbelleğin depolama bant genişliği sınırlarına ulaşmasını sağlamaya yardımcı olur.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Önbelleğim genelinde, gecikme süresine veya performansumu etkiler mi?

Önbellek en yüksek yükün (CPU ve sunucu yükünün her ikisi de %90 altında) altındayken, AOF kalıcılığı %15 ila %20 oranında performansı etkiler. Önbellek Bu limitlerin içindeyse gecikme sorunları olmamalıdır. Ancak, bu sınırlara, AOF etkinleştirildikten daha erken ulaşacaktır.

### <a name="how-can-i-remove-the-second-storage-account"></a>İkinci Depolama hesabını nasıl kaldırabilirim?

İkinci Depolama hesabını ilk depolama hesabıyla aynı olacak şekilde ayarlayarak, AOF kalıcılığı ikincil depolama hesabını kaldırabilirsiniz. Yönergeler için bkz. [AOF kalıcılığı yapılandırma](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Yeniden yazma nedir ve önbelleğinizi nasıl etkiler?

AOF dosyası yeterince büyük olduğunda otomatik olarak bir yeniden yazma işlemi önbellekte sıraya alınır. Yeniden yazma işlemi, geçerli veri kümesini oluşturmak için gereken en düşük işlem kümesi ile AOF dosyasını yeniden boyutlandırır. Yeniden yazar sırasında, büyük veri kümeleriyle ilgilenirken, daha önce performans sınırlarına ulaşılmayı bekler. AOF dosyası daha büyük olduğu için yeniden yazar daha az olur, ancak oluştuğunda önemli miktarda zaman alır.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>AOF özellikli bir önbelleği ölçeklendirirken ne beklenmem gerekir?

Ölçeklendirme sırasında AOF dosyası önemli ölçüde büyükse, ölçeklendirme tamamlandıktan sonra dosyayı yeniden yüklendiği için ölçek işleminin beklenenden uzun sürmesine izin bekler.

Ölçeklendirme hakkında daha fazla bilgi için bkz. [farklı boyuta ölçeklendirdiğimde ne olur ve ölçeklendirme işleminden önce yapılmış bir yedekleme geri yüklenir?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Amy verileri depolama alanında nasıl düzenlenir?

AOF dosyalarında depolanan veriler, verileri depolamaya kaydetme performansını artırmak için düğüm başına birden çok sayfa bloblarına bölünmüştür. Aşağıdaki tabloda her bir fiyatlandırma katmanı için kaç sayfa Blobları kullanıldığını göstermektedir:

| Premium katman | Bloblar |
|--------------|-------|
| P1           | parça başına 4    |
| P2           | parça başına 8    |
| P3           | parça başına 16   |
| P4           | parça başına 20   |

Kümeleme etkinleştirildiğinde, önbellekteki her parça önceki tabloda gösterildiği gibi kendi sayfa Blobları kümesine sahiptir. Örneğin, üç parçalı bir P2 önbelleği, AOF dosyasını 24 sayfa Blobları arasında dağıtır (3 parçalarla parça başına 8 blob).

Yeniden yazma işleminden sonra depolama alanında iki dosya kümesi vardır. Yeniden yazma işlemi arka planda gerçekleşir ve ilk dosya kümesine eklenir, yeniden yazma sırasında önbelleğe gönderilen işlemleri ikinci kümeyi ekleyerek ayarlayın. Bir yedek geçici olarak hata durumunda saklanır, ancak bir yeniden yazma işlemi tamamlandıktan sonra hemen silinir.


## <a name="next-steps"></a>Sonraki adımlar
Daha fazla Premium önbellek özelliği kullanmayı öğrenin.

* [Redsıs Premium katmanı için Azure önbelleğine giriş](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
