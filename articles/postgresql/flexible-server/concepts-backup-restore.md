---
title: PostgreSQL için Azure veritabanı 'nda yedekleme ve geri yükleme-esnek sunucu
description: PostgreSQL için Azure veritabanı ile yedekleme ve geri yükleme kavramlarını öğrenin-esnek sunucu
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d0e79e42c7c004638336ada23de663bbe74b7e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92532654"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nda yedekleme ve geri yükleme-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Yedeklemeler, herhangi bir iş sürekliliği stratejisinin önemli bir bölümünü oluşturur. Verileri yanlışlıkla bozulma veya silme işleminden korumaya yardımcı olurlar. PostgreSQL için Azure veritabanı-esnek sunucu, sunucunuzu otomatik olarak yedekler ve 35 güne kadar olan süre yedeklemelerini korur. Geri yükleme sırasında, saklama dönemi içinde geri yüklemek istediğiniz tarihi ve saati belirtebilirsiniz. Geri yükleme ve kurtarma için genel süre, veritabanı dosyalarının boyutuna ve gerçekleştirilecek kurtarma miktarına bağlıdır. 

### <a name="backup-process-in-flexible-server"></a>Esnek sunucuda yedekleme işlemi
İlk anlık görüntü yedeklemesi, esnek sunucu oluşturulduktan hemen sonraya zamanlanır. Daha sonra, veri dosyalarının günlük anlık görüntü yedeklemesi gerçekleştirilir. Yedeklemeler, bölge içinde yedekli bir depolama alanında depolanır. İşlem günlükleri (yazma öncesi Günlükler-WAL) de sürekli olarak arşivlenir, böylece son kaydedilen işleme geri yükleyebilirsiniz. Bu veri ve günlük yedeklemeleri, bir sunucuyu yapılandırdığınız yedekleme saklama süresi içinde herhangi bir noktaya geri yüklemenize olanak tanır. Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir.

Veritabanı yüksek kullanılabilirlik ile yapılandırıldıysa, birincil olarak günlük anlık görüntüler gerçekleştirilir ve sürekli günlük yedeklemeleri bekleme modundan gerçekleştirilir.

> [!IMPORTANT]
>Durdurulmuş sunucularda yedeklemeler gerçekleştirilmez. Ancak, veritabanı 7 gün sonra otomatik olarak başlatıldığında veya Kullanıcı tarafından başlatıldıktan sonra yedeklemeler sürdürülür.

Yedeklemeler yalnızca esnek sunucu içindeki geri yükleme işlemleri için kullanılabilir. Verileri esnek sunucuya dışarı veya dışarı aktarmak istiyorsanız, [döküm ve geri yükleme](../howto-migrate-using-dump-and-restore.md) yöntemini kullanırsınız.


### <a name="backup-retention"></a>Yedekleme dosyası saklama

Yedeklemeler, sunucu için yedekleme bekletme süresi ayarına göre tutulur. 7 ila 35 gün arasında bir saklama süresi seçebilirsiniz. Varsayılan saklama süresi yedi gündür. Saklama süresini sunucu oluşturma sırasında ayarlayabilir veya daha sonra güncelleştirebilirsiniz. Yedeklemeler durdurulmuş sunucular bile tutulur.

Yedekleme bekletme süresi, kullanılabilir yedeklemeleri temel aldığı için zaman içinde bir noktadan sonra geri yüklemenin ne kadar geri alınacağını yönetir \' . Yedekleme bekletme süresi Ayrıca bir geri yükleme perspektifinden kurtarma penceresi olarak da ele alınabilir. Yedekleme saklama süresi içinde bir zaman içindeki bir anlık geri yükleme gerçekleştirmek için gereken tüm yedeklemeler, yedekleme depolama alanında tutulur. Örneğin, yedekleme saklama süresi yedi güne ayarlanırsa, kurtarma penceresi son yedi gün olarak değerlendirilir. Bu senaryoda, son yedi gün içinde sunucuyu geri yüklemek ve kurtarmak için gereken tüm veriler ve Günlükler korunur. 


### <a name="backup-storage-cost"></a>Yedekleme depolama maliyeti

Esnek sunucu, sağlanan sunucu depolama alanınızı ek bir ücret ödemeden yedekleme depolama alanı olarak %100 ' e kadar sağlar. Kullanılan ek yedekleme depolama alanı aylık GB olarak ücretlendirilir. Örneğin, 250 GiB depolama alanı olan bir sunucu sağladıysanız, ek ücret ödemeden yedekleme depolama kapasitesi 250 GiB vardır. Günlük yedekleme kullanımı 25 GiB ise, en fazla 10 gün ücretsiz yedekleme depolama alanı olabilir. 250 GiB 'yi aşan yedekleme depolama tüketimi [fiyatlandırma modeline](https://azure.microsoft.com/pricing/details/postgresql/)göre ücretlendirilir.

Sunucu tarafından kullanılan yedekleme depolama alanını izlemek için Azure portaldaki [Kullanılan yedekleme depolama alanı](../concepts-monitoring.md) ölçümünü kullanabilirsiniz. Kullanılan yedekleme depolama alanı ölçümü, sunucu için ayarlanan yedekleme saklama süresi temel alınarak saklanan tüm veritabanı yedeklemeleri ile günlük yedeklemeleri tarafından kullanılan depolama alanının toplamını temsil eder.  Sunucu üzerindeki yoğun işlem etkinliği yedekleme depolama alanı kullanımının toplam veritabanı boyutundan bağımsız olarak artmasına neden olabilir.

Yedekleme depolama maliyetini denetlemenin birincil yolu, uygun yedekleme saklama süresini ayarlayarak ve istediğiniz kurtarma hedeflerinizi karşılamak için doğru yedekleme artıklığı seçeneklerini belirleyerek yapılır.

> [!IMPORTANT]
> Coğrafi olarak yedekli yedeklemeler, esnek sunucu ile Şu anda desteklenmiyor.

## <a name="point-in-time-restore-overview"></a>Zamana noktaya geri yüklemeye genel bakış

Esnek sunucu ' da, bir zaman içinde geri yükleme işlemi gerçekleştirmek, \' kaynak sunucunuz ile aynı bölgede esnek sunucu s yedeklemelerinden yeni bir sunucu oluşturur. Fiyatlandırma Katmanı, işlem oluşturma, sanal çekirdek sayısı, depolama boyutu, yedekleme saklama süresi ve yedek artıklık seçeneği için kaynak sunucunun yapılandırmasıyla oluşturulur. Ayrıca etiketler ve ayarlar (VNET ve güvenlik ayarları gibi) kaynak sunucudan devralınır. 

 > [!IMPORTANT]
> Bölgesel olarak yedekli yüksek kullanılabilirliğe sahip bir esnek sunucuyu geri yüklüyorsanız, geri yüklenen sunucu yüksek kullanılabilirlik olmadan ve birincil sunucunuz ile aynı bölgede yapılandırılır. 

 ### <a name="restore-process"></a>Geri yükleme işlemi

Fiziksel veritabanı dosyaları ilk olarak, anlık görüntü yedeklemelerinden sunucunun veri konumuna geri yüklenir. İstenen noktadan daha önce alınmış olan uygun yedekleme otomatik olarak seçilir ve geri yüklenir. Daha sonra, bir kurtarma işlemi, veritabanını tutarlı bir duruma getirmek için WAL dosyalarını kullanarak başlatılır. 

 Örneğin, yedeklemelerin her gece 23.00 aralığındaki değerler 'de gerçekleştirildiğini varsayalım. Geri yükleme noktası 15 Ağustos 2020 ' de ise, 2020 14 Ağustos 10:00 ' nin günlük yedeklemesi geri yüklenir. Veritabanı 25 Ağustos 2020 tarihine kadar, 24 Ağustos 10:00'da işlem günlükleri yedeklemesini kullanarak kurtarılacak. 

 Veritabanı sunucunuzu geri yüklemek için lütfen [Bu adımlara](./how-to-restore-server-portal.md) bakın.

> [!IMPORTANT]
> Esnek sunucu 'daki geri yükleme işlemleri yeni bir veritabanı sunucusu oluşturur ve var olan veritabanı sunucusunun üzerine yazmaz.

Bir noktadan noktaya geri yükleme, birden çok senaryoda faydalıdır. Örneğin, bir kullanıcı verileri yanlışlıkla sildiğinde, önemli bir tabloyu veya veritabanını bırakır veya bir uygulama hata nedeniyle yanlışlıkla hatalı verileri olan iyi verilerin üzerine yazar. İşlem günlüklerinin sürekli yedeklemesi nedeniyle son işleme geri yükleme yapabileceksiniz.

En erken geri yükleme noktası ve özel geri yükleme noktası arasında seçim yapabilirsiniz.

-   **En erken geri yükleme noktası**: saklama döneminize bağlı olarak, geri yükleme için en erken zaman olur. En eski yedekleme zamanı otomatik olarak seçilir ve portalda görüntülenir. Bu, zaman içinde belirli bir testi incelemek veya bir test yapmak istiyorsanız kullanışlıdır.

-   **Özel geri yükleme noktası**: Bu seçenek, bu esnek sunucu için tanımlanan Bekletme dönemi içinde herhangi bir noktayı seçmenizi sağlar. Varsayılan olarak, UTC olarak en son zaman otomatik seçilir ve test amaçlarınız için en son kaydedilen işleme geri yüklemek istiyorsanız yararlı olur. İsteğe bağlı olarak başka günler ve saat seçebilirsiniz. 

Kurtarılacak tahmini süre, veritabanı boyutu, işlem günlüklerinin hacmi, ağ bant genişliği ve aynı bölgedeki aynı bölgede Kurtarılan toplam veritabanı sayısı gibi çeşitli faktörlere bağlıdır. Genel kurtarma süresi genellikle birkaç dakikadan birkaç saate kadar sürer.


> [!IMPORTANT]
> Silinen sunucular **geri yüklenemez.** Sunucuyu silerseniz, sunucuya ait olan tüm veritabanları da silinir ve kurtarılamaz. Sunucu kaynaklarını korumak için dağıtım sonrası, yanlışlıkla silme veya beklenmeyen değişikliklerden, Yöneticiler [Yönetim kilitlerinin](../../azure-resource-manager/management/lock-resources.md)faydalanabilir.

## <a name="perform-post-restore-tasks"></a>Geri yükleme sonrası görevleri gerçekleştirme

Veritabanını geri yükledikten sonra, kullanıcılarınızın ve uygulamalarınızın yedeklemesini ve çalışmasını sağlamak için aşağıdaki görevleri gerçekleştirebilirsiniz:

-   Yeni sunucu özgün sunucunun yerini alacak şekilde, istemcileri ve istemci uygulamalarını yeni sunucuya yeniden yönlendirin.

-   Kullanıcıların bağlanabilmesi için uygun sunucu düzeyi güvenlik duvarı ve VNet kurallarının bulunduğundan emin olun. Bu kurallar, özgün sunucudan üzerine kopyalanmaz.
  
-   Geri yüklenen sunucunun işlemi gerektiğinde yukarı/aşağı ölçeklendirilebilir.

-   Uygun oturum açma ve veritabanı düzeyi izinlerinin yerinde olduğundan emin olun.

-   Uyarıları uygun şekilde yapılandırın.
  
-  Yüksek kullanılabilirliğiyle yapılandırılmış veritabanını geri yüklediyseniz ve geri yüklenen sunucuyu yüksek kullanılabilirliğe sahip olarak yapılandırmak istiyorsanız, [adımları](./how-to-manage-high-availability-portal.md)takip edebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

-   [İş sürekliliği](./concepts-business-continuity.md) hakkında bilgi edinin
-   [Bölge yedekli yüksek kullanılabilirlik](./concepts-high-availability.md) hakkında bilgi edinin
-   [Nasıl geri](./how-to-restore-server-portal.md) yükleyeceğinizi öğrenin