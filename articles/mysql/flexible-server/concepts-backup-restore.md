---
title: MySQL için Azure veritabanı esnek sunucusu 'nda yedekleme ve geri yükleme
description: MySQL için Azure veritabanı esnek sunucusu ile yedekleme ve geri yükleme kavramlarını öğrenin
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2d69427f9f11a47cedeccb4b1da38b770952f029
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240775"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>MySQL için Azure veritabanı esnek sunucusu 'nda yedekleme ve geri yükleme (Önizleme)

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

MySQL için Azure veritabanı esnek sunucu, otomatik olarak sunucu yedeklemeleri oluşturur ve bunları bölge içinde yerel olarak yedekli depolama alanında güvenli bir şekilde depolar. Sunucunuzu belirli bir noktaya geri yüklemek için yedeklemeler kullanılabilir. Yedekleme ve geri yükleme her iş sürekliliği stratejisinin temel parçalarıdır çünkü bunlar verilerinizi yanlışlıkla bozulmalara veya silmelere karşı korur.

## <a name="backup-overview"></a>Backup’a genel bakış

Esnek sunucu, veri dosyalarının anlık görüntü yedeklemelerini alır ve yerel olarak yedekli bir depolama alanında depolar. Sunucu ayrıca işlem günlükleri yedeklemesi de gerçekleştirir ve bunları yerel yedekli depolamada depolar. Bu yedeklemeler, yapılandırılmış yedekleme saklama döneminizin içindeki herhangi bir zamanda bir sunucuyu geri yüklemenize olanak tanır. Varsayılan yedekleme saklama süresi yedi gündür. Veritabanı yedeklemesini isteğe bağlı olarak 1 ile 35 gün yapılandırabilirsiniz. Rest 'de depolanan veriler için AES 256 bit şifreleme kullanılarak tüm yedeklemeler şifrelenir.

Bu yedekleme dosyaları verilemez. Yedeklemeler yalnızca esnek sunucudaki geri yükleme işlemleri için kullanılabilir. Ayrıca, bir MySQL istemcisinden [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) kullanarak bir veritabanını kopyalayabilirsiniz.

## <a name="backup-frequency"></a>Yedekleme sıklığı

Esnek sunuculardaki yedeklemeler anlık görüntü tabanlıdır. İlk tam anlık görüntü yedeklemesi, sunucu oluşturulduktan hemen sonraya zamanlanır. Bu ilk tam anlık görüntü yedeklemesi sunucunun temel yedeklemesi olarak tutulur. Sonraki anlık görüntü yedeklemeleri yalnızca değişiklik yedeğidir.

Anlık görüntü değişiklik yedekleri günde en az bir kez gerçekleştirilir. Anlık görüntü değişiklik yedekleri belirli bir plana göre gerçekleştirilmez. Son değişiklik yedeğinden sonra MySQL içindeki ikili günlükleri 50 GB'ın üzerine çıkmadığı sürece anlık görüntü değişiklik yedekleri 24 saatte bir gerçekleştirilir. Bir gün içinde en fazla altı anlık görüntü değişiklik yedeği alınabilir. İşlem günlüğü yedeklemeleri her beş dakikada bir gerçekleşir.

## <a name="backup-retention"></a>Yedekleme dosyası saklama

Veritabanı yedeklemeleri, bir bölgedeki üç kopya içinde depolanan yerel bir yedekli depolama (LRS) içinde depolanır. Yedeklemeler, sunucusundaki yedekleme bekletme süresi ayarına göre tutulur. Varsayılan saklama süresi yedi gün olan 1 ile 35 gün arasında bir saklama süresi seçebilirsiniz. Azure portal kullanarak yedekleme yapılandırmasını güncelleştirerek, sunucu oluşturma sırasında veya daha sonra saklama süresini ayarlayabilirsiniz.

Yedekleme bekletme süresi, kullanılabilir yedeklemeleri temel aldığı için zaman içinde ne kadar geri yükleme işlemi gerçekleştirilebileceğini yönetir. Yedekleme bekletme süresi Ayrıca bir geri yükleme perspektifinden kurtarma penceresi olarak da ele alınabilir. Yedekleme saklama süresi içinde bir zaman içindeki bir anlık geri yükleme gerçekleştirmek için gereken tüm yedeklemeler, yedekleme depolama alanında tutulur. Örneğin, yedekleme Bekletme dönemi yedi güne ayarlanırsa, kurtarma penceresi son yedi gün olarak değerlendirilir. Bu senaryoda, son yedi gün içinde sunucuyu geri yüklemek için gereken tüm yedeklemeler korunur. Yedi günlük bir yedekleme bekletme penceresiyle, veritabanı anlık görüntüleri ve işlem günlüğü yedeklemeleri, son sekiz güne (pencereden önce 1 gün) depolanır.

## <a name="backup-storage-cost"></a>Yedekleme depolama maliyeti

Esnek sunucu, sağlanan sunucu depolama alanınızı ek bir ücret ödemeden yedekleme depolama alanı olarak %100 ' e kadar sağlar. Kullanılan ek yedekleme depolama alanı aylık GB olarak ücretlendirilir. Örneğin, 250 GB depolama alanı olan bir sunucu sağladıysanız, ek ücret ödemeden sunucu yedeklemeleri için kullanılabilir 250 GB depolama alanı vardır. Günlük yedekleme kullanımı 25 GB ise, en fazla 10 gün ücretsiz yedekleme depolama alanı olabilir. 250 GB 'tan fazla yedeklemeler için tüketilen depolama, [fiyatlandırma modeline](https://azure.microsoft.com/pricing/details/mysql/)göre ücretlendirilir.

Bir sunucu tarafından kullanılan yedekleme depolama alanını izlemek için Azure portal bulunan Azure Izleyici 'de bulunan [yedekleme depolama alanı](../concepts-monitoring.md) ölçüsünü kullanabilirsiniz. Kullanılan **yedekleme depolama** ölçümü, sunucu için ayarlanan yedekleme Bekletme dönemi temelinde saklanan tüm veritabanı yedeklemeleri ve günlük yedeklemeleri tarafından tüketilen depolama alanının toplamını temsil eder. Sunucu üzerindeki yoğun işlem etkinliği yedekleme depolama alanı kullanımının toplam veritabanı boyutundan bağımsız olarak artmasına neden olabilir.

Yedekleme depolama maliyetini denetlemenin birincil yolu, uygun yedekleme saklama süresini ayarlamadır. 1 ila 35 gün arasında bir saklama süresi seçebilirsiniz.

> [!IMPORTANT]
> Bölgesel olarak yedekli yüksek kullanılabilirlik yapılandırmasında yapılandırılan bir veritabanı sunucusundan yedeklemeler, anlık görüntü yedeklemeleriyle en düşük düzeyde olduğundan, birincil veritabanı sunucusundan yapılır.

> [!IMPORTANT]
> Coğrafi olarak yedekli yedeklemeler, esnek sunucu ile Şu anda desteklenmiyor.

## <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme

MySQL için Azure veritabanı esnek sunucusunda, bir zaman içinde geri yükleme gerçekleştirildiğinde, kaynak sunucunuz ile aynı bölgedeki esnek sunucu yedeklemelerinden yeni bir sunucu oluşturulur. İşlem katmanı, sanal çekirdek sayısı, depolama boyutu, yedekleme saklama süresi ve yedek artıklık seçeneği için özgün sunucunun yapılandırmasıyla oluşturulur. Ayrıca, sanal ağ ve güvenlik duvarı gibi Etiketler ve ayarlar kaynak sunucudan devralınır. Geri yüklenen sunucunun işlem ve depolama katmanı, yapılandırma ve güvenlik ayarları geri yükleme tamamlandıktan sonra değiştirilebilir.

> [!NOTE]
> Geri yükleme işleminden sonra varsayılan değerlere sıfırlanan (ve birincil sunucudan kopyalanmayan) iki sunucu parametresi vardır
> *   time_zone-bu değer varsayılan değer SISTEMINE ayarlanır
> *   event_scheduler-event_scheduler, geri yüklenen sunucuda kapalı olarak ayarlanmıştır

Bir noktadan noktaya geri yükleme, birden çok senaryoda faydalıdır. Yaygın olarak kullanılan bazı kullanım durumları şunlardır-
-   Kullanıcı veritabanındaki verileri yanlışlıkla sildiğinde
-   Kullanıcı önemli bir tabloyu veya veritabanını bırakır
-   Kullanıcı uygulaması yanlışlıkla bir uygulama hatası nedeniyle hatalı veriler içeren iyi verilerin üzerine yazar.

[Azure Portal](how-to-restore-server-portal.md)aracılığıyla en son geri yükleme noktası ve özel geri yükleme noktası arasında seçim yapabilirsiniz.

-   **En son geri yükleme noktası**: en son geri yükleme noktası, sunucuyu kaynak sunucuda gerçekleştirilen son yedeklemeye geri yüklemenize yardımcı olur. Geri yükleme için zaman damgası portalda da görüntülenir. Bu seçenek, sunucuyu en güncel duruma hızlıca geri yüklemek için yararlıdır.
-   **Özel geri yükleme noktası**: Bu, bu esnek sunucu için tanımlanan Bekletme dönemi içinde herhangi bir noktayı seçmenizi sağlar. Bu seçenek, bir kullanıcı hatasından kurtarmak üzere sunucunun kesin bir noktada geri yüklenmesi için yararlıdır.

Tahmini kurtarma süresi, veritabanı boyutları, işlem günlüğü yedekleme boyutu, SKU 'nun işlem boyutu ve geri yükleme saati gibi çeşitli faktörlere bağlıdır. İşlem günlüğü kurtarma işlemi, geri yükleme işleminin bir parçası olarak en çok zaman alır. Geri yükleme süresi tam veya fark anlık görüntü yedekleme zamanlamalarına yakınsa, işlem günlüğü uygulaması en düşük olduğundan geri yüklemeler daha hızlıdır. Sunucunuza yönelik doğru kurtarma süresini tahmin etmek için, ortamınızda çok sayıda ortama özel değişken olduğu için test etmenizi kesinlikle öneririz.

> [!IMPORTANT]
> Bölge yedekli yüksek kullanılabilirliğe sahip bir esnek sunucuyu geri yüklüyorsanız, geri yüklenen sunucu birincil sunucunuz ile aynı bölgede ve bölgede yapılandırılır ve HA olmayan bir modda tek bir esnek sunucu olarak dağıtılır. Esnek sunucu için [bölge yedekli yüksek kullanılabilirlik](concepts-high-availability.md) bölümüne bakın.

> [!IMPORTANT]
> Silinen sunucular **geri yüklenemez.** Sunucuyu silerseniz, sunucuya ait olan tüm veritabanları da silinir ve kurtarılamaz. Sunucu kaynaklarını korumak için dağıtım sonrası, yanlışlıkla silme veya beklenmeyen değişikliklerden, Yöneticiler [Yönetim kilitlerinin](../../azure-resource-manager/management/lock-resources.md)faydalanabilir.

## <a name="perform-post-restore-tasks"></a>Geri yükleme sonrası görevleri gerçekleştirme

**En son geri yükleme noktasından** veya **özel geri yükleme noktası** kurtarma mekanizmasından geri yükleme yapıldıktan sonra, kullanıcılarınızın ve uygulamalarınızın yedeklemesini ve çalışmasını sağlamak için aşağıdaki görevleri gerçekleştirmeniz gerekir:

-   Yeni sunucu özgün sunucunun yerini alacak şekilde, istemcileri ve istemci uygulamalarını yeni sunucuya yeniden yönlendirin.
-   Kullanıcıların bağlanabilmesi için uygun sunucu düzeyinde güvenlik duvarının ve sanal ağ kurallarının bulunduğundan emin olun.
-   Uygun oturum açma ve veritabanı düzeyi izinlerinin yerinde olduğundan emin olun.
-   Uyarıları uygun şekilde yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

-   [İş sürekliliği](./concepts-business-continuity.md) hakkında bilgi edinin
-   [Bölge yedekli yüksek kullanılabilirlik](./concepts-high-availability.md) hakkında bilgi edinin
-   [Yedekleme ve kurtarma](./concepts-backup-restore.md) hakkında bilgi edinin
