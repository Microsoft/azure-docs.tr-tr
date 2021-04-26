---
title: Veri kalıcılığını yapılandırma-Redsıs için Premium Azure önbelleği
description: Premium katman Azure önbelleğinizi Redsıs örnekleri için yapılandırma ve yönetme hakkında bilgi edinin
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 9d11d17f90dcd6335fcaf6bd48a44037777a087e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601394"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>Redsıs örneği için Premium bir Azure önbelleği için veri kalıcılığını yapılandırma

[Redsıs kalıcılığı](https://redis.io/topics/persistence) , redin içinde depolanan verileri kalıcı hale getirebilmeniz için izin verir. Ayrıca, anlık görüntüler alabilir ve verileri yedekleyebilir ve bu da donanım arızası durumunda yükleyebilirsiniz. Bu, tüm verilerin bellekte depolandığı temel veya standart katmandan çok büyük bir avantajdır ve önbellek düğümlerinin kapatılmasında oluşabilecek bir hata olması durumunda olası veri kaybı olabilir. 

Redsıs için Azure önbelleği, aşağıdaki modelleri kullanarak Redsıs kalıcılığı sağlar:

* **RDB kalıcılığı** -RDB (redsıs veritabanı) kalıcılığı yapılandırıldığında, Redsıs Için Azure önbelleği, yapılandırılabilir bir yedekleme sıklığına bağlı olarak, redsıs ikili biçimindeki bir redin ikili biçiminde diske (bir Azure depolama hesabında) Redsıs Için Azure önbelleğinin bir anlık görüntüsünü devam ettirir. Hem birincil hem de çoğaltma önbelleğini devre dışı bırakan çok zararlı bir olay oluşursa, önbellek en son anlık görüntü kullanılarak yeniden oluşturulur. RDB kalıcılığı 'nin [avantajları](https://redis.io/topics/persistence#rdb-advantages) ve [dezavantajları](https://redis.io/topics/persistence#rdb-disadvantages) hakkında daha fazla bilgi edinin.
* **AOF kalıcılığı** -AOF (yalnızca ekleme dosyası) kalıcılığı yapılandırıldığında, reddir Için Azure önbelleği, her yazma işlemini saniyede en az bir kez bir Azure depolama hesabına kaydeder. Hem birincil hem de çoğaltma önbelleğini devre dışı bırakan çok zararlı bir olay oluşursa, önbellek, depolanan yazma işlemleri kullanılarak yeniden oluşturulur. AOF kalıcılığın [avantajları](https://redis.io/topics/persistence#aof-advantages) ve [dezavantajları](https://redis.io/topics/persistence#aof-disadvantages) hakkında daha fazla bilgi edinin.

Kalıcılık, Redsıs verilerini sahip olduğunuz ve yönettiğiniz bir Azure depolama hesabına yazar. Önbellek oluşturma sırasında ve mevcut Premium önbellekler için **kaynak menüsünde** **yeni Azure önbelleğinden** yapılandırma yapabilirsiniz.

> [!NOTE]
> 
> Kalıcı olduğunda Azure Storage verileri otomatik olarak şifreler. Şifreleme için kendi anahtarlarınızı kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Key Vault Ile müşteri tarafından yönetilen anahtarlar](../storage/common/storage-service-encryption.md).
> 
> 

## <a name="set-up-data-persistence"></a>Veri kalıcılığını ayarlama

1. Premium önbellek oluşturmak için [Azure Portal](https://portal.azure.com) oturum açın ve **kaynak oluştur**' u seçin. Önbellekleri Azure portalında oluşturabileceğiniz gibi, Resource Manager şablonlarını, PowerShell'i veya Azure CLI'sini kullanarak da oluşturabilirsiniz. Redu için Azure önbelleği oluşturma hakkında daha fazla bilgi için bkz. [önbellek oluşturma](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Kaynak oluştur.":::
   
2. **Yeni** sayfada **veritabanları** ' nı seçin ve ardından **redsıs için Azure önbelleği**' ni seçin.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Redsıs için Azure önbelleği ' ni seçin.":::

3. **Yeni Redis Cache** sayfasında, yeni Premium önbelleğiniz için ayarları yapılandırın.
   
   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS adı** | Genel olarak benzersiz bir ad girin. | Önbellek adı yalnızca rakam, harf veya kısa çizgi içeren 1 ile 63 karakter arasında bir dize olmalıdır. Ad bir sayı veya harfle başlamalı ve bitmeli ve ardışık kısa çizgi içeremez. Önbellek örneğinizin *ana bilgisayar adı* *\<DNS name> . Redis.cache.Windows.net* olacaktır. | 
   | **Abonelik** | Açılır ve aboneliğinizi seçin. | Redsıs örneği için bu yeni Azure önbelleğinin oluşturulacağı abonelik. | 
   | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Önbelleğinizin ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
   | **Konum** | Açılır ve bir konum seçin. | Önbelleğinizi kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |
   | **Önbellek türü** | Açılır ve Premium özellikleri yapılandırmak için Premium önbellek seçin. Ayrıntılar için bkz. [redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/). |  Fiyatlandırma katmanı önbellek için kullanılabilen boyut, performans ve özellikleri belirler. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleği 'Ne genel bakış](cache-overview.md). |

4. **Ağ** sekmesini seçin veya sayfanın altındaki **ağ** düğmesine tıklayın.

5. **Ağ** sekmesinde, bağlantı yönteminizi seçin. Premium önbellek örnekleri için genel IP adresleri veya hizmet uç noktaları aracılığıyla ya da özel bir uç nokta kullanarak özel olarak bağlanabilir.

6. **İleri: Gelişmiş** sekmesini seçin veya sayfanın altındaki **İleri: Gelişmiş** düğmesine tıklayın.

7. Premium önbellek örneğinin **Gelişmiş** SEKMESINDE, TLS olmayan bağlantı noktası, kümeleme ve veri kalıcılığı için ayarları yapılandırın. Veri kalıcılığı için **RDB** ya da **AOF** kalıcılığı seçebilirsiniz. 

8. RDB kalıcılığı etkinleştirmek için **RDB** ' e tıklayın ve ayarları yapılandırın. 
   
   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Yedekleme sıklığı** | Açılır ve bir yedekleme aralığı seçin, **15 dakika**, **30 dakika**, **60 dakika**, **6 saat**, **12 saat** ve **24 saat** arasında seçim yapın. | Önceki yedekleme işlemi başarıyla tamamlandıktan sonra ve yeni bir yedekleme tamamlandığında bu Aralık sona erdiğinde, bu Aralık başlatılır. | 
   | **Depolama hesabı** | Açılır ve depolama hesabınızı seçin. | Önbellek ile aynı bölgede ve abonelikte bir depolama hesabı seçmeniz gerekir ve Premium depolamada daha yüksek aktarım hızı bulunduğundan **Premium Depolama** hesabı önerilir.  | 
   | **Depolama anahtarı** | Açılan ve kullanılacak **birincil anahtarı** ya da **İkincil anahtarı** seçin. | Kalıcılık hesabınız için depolama anahtarı yeniden oluşturulursa, istenen anahtarı **depolama anahtarı** açılır listesinden yeniden yapılandırmanız gerekir. | 

    Yedekleme sıklığı aralığı geçtiğinde ilk yedekleme başlatılır.
    
   > [!NOTE]
   > RDB dosyaları depolama alanına yedeklendiğinde, bunlar sayfa Blobları biçiminde depolanır.

9. Sürekliliği etkinleştirmek için **AOF** 'ye tıklayın ve ayarları yapılandırın. 
   
   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **İlk depolama hesabı** | Açılır ve depolama hesabınızı seçin. | Bu depolama hesabı, önbellek ile aynı bölgede ve abonelikte olmalıdır ve Premium depolamada daha yüksek aktarım hızı bulunduğundan **Premium Depolama** hesabı önerilir. | 
   | **İlk depolama anahtarı** | Açılan ve kullanılacak **birincil anahtarı** ya da **İkincil anahtarı** seçin. | Kalıcılık hesabınız için depolama anahtarı yeniden oluşturulursa, istenen anahtarı **depolama anahtarı** açılır listesinden yeniden yapılandırmanız gerekir. | 
   | **İkinci depolama hesabı** | Seçim Açılır ve ikincil depolama hesabınızı seçin. | İsteğe bağlı olarak ek bir depolama hesabı yapılandırabilirsiniz. İkinci bir depolama hesabı yapılandırılmışsa, çoğaltma önbelleğine yazma işlemleri bu ikinci depolama hesabına yazılır. | 
   | **İkinci depolama anahtarı** | Seçim Açılan ve kullanılacak **birincil anahtarı** ya da **İkincil anahtarı** seçin. | Kalıcılık hesabınız için depolama anahtarı yeniden oluşturulursa, istenen anahtarı **depolama anahtarı** açılır listesinden yeniden yapılandırmanız gerekir. | 

    BIR dizi kalıcılığı etkinleştirildiğinde, önbelleğe yazma işlemleri belirlenen depolama hesabına (veya ikinci bir depolama hesabı yapılandırdıysanız hesaplar) kaydedilir. Hem birincil hem de çoğaltma önbelleğinin kullanıldığı çok önemli bir hata durumunda, önbelleğin yeniden oluşturulması için depolanan AOF günlüğü kullanılır.

10. **Sonraki: Etiketler** sekmesini seçin veya sayfanın altındaki **Sonraki: Etiketler** düğmesine tıklayın.

11. İsteğe bağlı olarak, **Etiketler** sekmesinde, kaynağı sınıflandırmak istiyorsanız ad ve değeri girin. 

12. **Gözden geçir ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, gözden geçir + Oluştur sekmesine götürülürsünüz.

13. Yeşil doğrulama başarılı iletisi göründüğünde **Oluştur**' u seçin.

Önbelleğin oluşturulması biraz zaman alır. Redsıs **genel bakış** sayfasında ilerlemeyi izleyebilirsiniz. **Durum** **çalışıyor** olarak görüntülendiğinde, önbellek kullanıma hazırdır. 

## <a name="persistence-faq"></a>Kalıcılık SSS
Aşağıdaki liste, Redsıs kalıcılığı için Azure önbelleği hakkında sık sorulan soruların yanıtlarını içerir.

* [Daha önce oluşturulmuş bir önbellekte kalıcılığı etkinleştirebilir miyim?](#can-i-enable-persistence-on-a-previously-created-cache)
* [AOF ve RDB kalıcılığını aynı anda etkinleştirebilir miyim?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Hangi Kalıcılık modelini seçmem gerekir?](#which-persistence-model-should-i-choose)
* [Farklı bir boyuta ölçeklendirdiğimde ve ölçekleme işleminden önce yapılmış bir yedekleme geri yüklendiğinde ne olur?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [Aynı depolama hesabını iki farklı önbellekte Kalıcılık için kullanabilir miyim?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
* [Veri kalıcılığı sırasında kullanılan depolama alanı için ücretlendirilecektir](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)

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

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>Aynı depolama hesabını iki farklı önbellekte Kalıcılık için kullanabilir miyim?
Evet, iki farklı önbellekte Kalıcılık için aynı depolama hesabını kullanabilirsiniz

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Önbelleği oluşturduktan sonra RDB yedekleme sıklığını değiştirebilir miyim?
Evet, **veri kalıcılığı** DIKEY penceresinde RDB kalıcılığı için yedekleme sıklığını değiştirebilirsiniz. Yönergeler için bkz. Redsıs kalıcılığını yapılandırma.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>60 dakikalık bir RDB yedekleme sıklığım neden olursa yedeklemeler arasında 60 dakikadan fazla olur?
Bir önceki yedekleme işlemi başarıyla tamamlanana kadar RDB Kalıcılık yedekleme sıklığı aralığı başlatılmaz. Yedekleme sıklığı 60 dakikadır ve başarılı bir şekilde tamamlanışında bir yedekleme işlemi 15 dakika sürüyorsa, sonraki yedekleme, önceki yedeklemenin başlangıç zamanından sonra 75 dakika sonrasına kadar başlamaz.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Yeni bir yedekleme yapıldığında eski RDB yedeklemelere ne olur?
En güncel olanı hariç tüm RDB Kalıcılık yedeklemeleri otomatik olarak silinir. Bu silme işlemi hemen gerçekleşmeyebilir, ancak eski yedeklemeler süresiz olarak kalıcı olmaz.


### <a name="when-should-i-use-a-second-storage-account"></a>İkinci bir depolama hesabı ne zaman kullanmalıyım?

Önbellekte beklenen küme işlemlerinden daha yüksek olduğunu düşünüyorsanız, AOF kalıcılığı için ikinci bir depolama hesabı kullanmanız gerekir.  İkincil depolama hesabının ayarlanması, önbelleğin depolama bant genişliği sınırlarına ulaşmasını sağlamaya yardımcı olur.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Önbelleğim genelinde, gecikme süresine veya performansumu etkiler mi?

Önbellek en yüksek yükün (CPU ve sunucu yükünün her ikisi de %90 altında) altındayken, AOF kalıcılığı %15 ila %20 oranında performansı etkiler. Önbellek Bu limitlerin içindeyse gecikme sorunları olmamalıdır. Ancak, bu sınırlara, AOF etkinleştirildikten daha erken ulaşacaktır.

### <a name="how-can-i-remove-the-second-storage-account"></a>İkinci Depolama hesabını nasıl kaldırabilirim?

İkinci Depolama hesabını ilk depolama hesabıyla aynı olacak şekilde ayarlayarak, AOF kalıcılığı ikincil depolama hesabını kaldırabilirsiniz. Mevcut önbellekler için, **veri Kalıcılık** dikey penceresine önbelleğiniz için **Kaynak menüsünden** erişilir. Kalıcılığı devre dışı bırakmak için **devre dışı** öğesine tıklayın.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Yeniden yazma nedir ve önbelleğinizi nasıl etkiler?

AOF dosyası yeterince büyük olduğunda otomatik olarak bir yeniden yazma işlemi önbellekte sıraya alınır. Yeniden yazma işlemi, geçerli veri kümesini oluşturmak için gereken en düşük işlem kümesi ile AOF dosyasını yeniden boyutlandırır. Yeniden yazar sırasında, büyük veri kümeleriyle ilgilenirken, daha önce performans sınırlarına ulaşılmayı bekler. AOF dosyası daha büyük olduğu için yeniden yazar daha az olur, ancak oluştuğunda önemli miktarda zaman alır.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>AOF özellikli bir önbelleği ölçeklendirirken ne beklenmem gerekir?

Ölçeklendirme sırasında AOF dosyası önemli ölçüde büyükse, ölçeklendirme tamamlandıktan sonra dosyayı yeniden yüklendiği için ölçek işleminin beklenenden uzun sürmesine izin bekler.

Ölçeklendirme hakkında daha fazla bilgi için bkz. [farklı boyuta ölçeklendirdiğimde ne olur ve ölçeklendirme işleminden önce yapılmış bir yedekleme geri yüklenir?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Amy verileri depolama alanında nasıl düzenlenir?

AOF dosyalarında depolanan veriler, verileri depolamaya kaydetme performansını artırmak için düğüm başına birden çok sayfa bloblarına bölünmüştür. Aşağıdaki tabloda her bir fiyatlandırma katmanı için kaç sayfa Blobları kullanıldığını göstermektedir:

| Premium katmanı | Bloblar |
|--------------|-------|
| P1           | parça başına 4    |
| P2           | parça başına 8    |
| P3           | parça başına 16   |
| P4           | parça başına 20   |

Kümeleme etkinleştirildiğinde, önbellekteki her parça önceki tabloda gösterildiği gibi kendi sayfa Blobları kümesine sahiptir. Örneğin, üç parçalı bir P2 önbelleği, AOF dosyasını 24 sayfa Blobları arasında dağıtır (3 parçalarla parça başına 8 blob).

Yeniden yazma işleminden sonra depolama alanında iki dosya kümesi vardır. Yeniden yazma işlemi arka planda gerçekleşir ve ilk dosya kümesine eklenir, yeniden yazma sırasında önbelleğe gönderilen işlemleri ikinci kümeyi ekleyerek ayarlayın. Bir yedek geçici olarak hata durumunda saklanır, ancak bir yeniden yazma işlemi tamamlandıktan sonra hemen silinir.

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>Veri kalıcılığı sırasında kullanılan depolama alanı için ücretlendirilmem gerekir mi?

Evet, kullanılmakta olan depolama hesabının fiyatlandırma modeline göre kullanılan depolama alanı için ücretlendirilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
Redsıs özellikleri için Azure önbelleği hakkında daha fazla bilgi edinin.

* [Redsıs Premium hizmet katmanları için Azure önbelleği](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
