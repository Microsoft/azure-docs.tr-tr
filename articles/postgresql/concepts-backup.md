---
title: Yedekleme ve geri yükleme-PostgreSQL için Azure veritabanı-tek sunucu
description: Otomatik yedeklemeler hakkında bilgi edinin ve PostgreSQL için Azure veritabanınızı sunucu-tek sunucu olarak geri yükleyin.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: db3b62e7ce07c1e10bc5030c37cb8957d281ea05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100517306"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda yedekleme ve geri yükleme-tek sunucu

PostgreSQL için Azure veritabanı otomatik olarak sunucu yedeklemeleri oluşturur ve bunları Kullanıcı tarafından yerel olarak yedekli veya coğrafi olarak yedekli depolama olarak yapılandırılmış şekilde depolar. Sunucunuzu belirli bir noktaya geri yüklemek için yedeklemeler kullanılabilir. Yedekleme ve geri yükleme her iş sürekliliği stratejisinin temel parçalarıdır çünkü bunlar verilerinizi yanlışlıkla bozulmalara veya silmelere karşı korur.

## <a name="backups"></a>Yedeklemeler

PostgreSQL için Azure veritabanı, veri dosyalarının ve işlem günlüğünün yedeklerini alır. Desteklenen en fazla depolama boyutuna bağlı olarak, tam ve fark yedeklemeleri (4 TB maksimum depolama sunucusu) veya anlık görüntü yedeklemeleri (en fazla 16 TB depolama sunucusu) sunuyoruz. Bu yedeklemeler, yapılandırılmış yedekleme saklama döneminizin içindeki herhangi bir zamanda bir sunucuyu geri yüklemenize olanak tanır. Varsayılan yedekleme saklama süresi yedi gündür. İsteğe bağlı olarak 35 güne kadar yapılandırma yapabilirsiniz. Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir.

Bu yedekleme dosyaları verilemez. Yedeklemeler yalnızca PostgreSQL için Azure veritabanı 'nda geri yükleme işlemleri için kullanılabilir. Bir veritabanını kopyalamak için [pg_dump](howto-migrate-using-dump-and-restore.md) kullanabilirsiniz.

### <a name="backup-frequency"></a>Yedekleme sıklığı

#### <a name="servers-with-up-to-4-tb-storage"></a>4 TB 'a kadar depolama alanı olan sunucular

En fazla 4 TB depolama alanı destekleyen sunucular için, tam yedeklemeler her hafta bir kez gerçekleşir. Değişiklik yedeklemeleri günde iki kez gerçekleşir. İşlem günlüğü yedeklemeleri her beş dakikada bir gerçekleşir.


#### <a name="servers-with-up-to-16-tb-storage"></a>16 TB 'a kadar depolama alanı olan sunucular

[Azure bölgelerinin](./concepts-pricing-tiers.md#storage)bir alt kümesinde, tüm yeni sağlanan sunucular 16 TB 'a kadar depolamayı destekleyebilir. Bu büyük depolama sunucularındaki yedeklemeler anlık görüntü tabanlıdır. İlk tam anlık görüntü yedeklemesi, sunucu oluşturulduktan hemen sonraya zamanlanır. Bu ilk tam anlık görüntü yedeklemesi sunucunun temel yedeklemesi olarak tutulur. Sonraki anlık görüntü yedeklemeleri yalnızca değişiklik yedeğidir. Anlık görüntü değişiklik yedekleri belirli bir plana göre gerçekleştirilmez. Bir gün içinde üç fark anlık görüntü yedeklemesi yapılır. İşlem günlüğü yedeklemeleri her beş dakikada bir gerçekleşir. 

### <a name="backup-retention"></a>Yedekleme dosyası saklama

Yedeklemeler, sunucusundaki yedekleme bekletme süresi ayarına göre tutulur. 35 gün günlük bir saklama süresi seçebilirsiniz. Varsayılan saklama süresi 7 gündür. [Azure Portal](./howto-restore-server-portal.md#set-backup-configuration) veya [Azure CLI](./howto-restore-server-cli.md#set-backup-configuration)kullanarak yedekleme yapılandırmasını güncelleştirerek sunucu oluşturma sırasında veya daha sonra saklama süresini ayarlayabilirsiniz. 

Yedekleme bekletme süresi, kullanılabilir yedeklemeler temel aldığı için zaman içinde bir nokta geri yüklemesi alma süresinin ne kadar geri alınacağını yönetir. Yedekleme bekletme süresi Ayrıca bir geri yükleme perspektifinden kurtarma penceresi olarak da ele alınabilir. Yedekleme saklama süresi içinde bir zaman içindeki bir anlık geri yükleme gerçekleştirmek için gereken tüm yedeklemeler, yedekleme depolama alanında tutulur. Örneğin, yedekleme Bekletme dönemi 7 gün olarak ayarlandıysa, kurtarma penceresi son 7 gün olarak değerlendirilir. Bu senaryoda, son 7 gün içinde sunucuyu geri yüklemek için gereken tüm yedeklemeler korunur. Yedi günlük bir yedekleme bekletme penceresi ile:
- 4 TB 'a kadar depolama alanı olan sunucular en fazla 2 tam veritabanı yedeklemesi, tüm değişiklik yedeklemeleri ve en erken tam veritabanı yedeklemesinden bu yana gerçekleştirilen işlem günlüğü yedeklemelerini korur.
-   16 TB 'a kadar depolamaya sahip sunucular, son 8 gündeki tüm fark anlık görüntülerini ve işlem günlüğü yedeklemelerini tam veritabanı anlık görüntüsünü tutar.

### <a name="backup-redundancy-options"></a>Yedekleme artıklığı seçenekleri

PostgreSQL için Azure veritabanı, Genel Amaçlı ve bellek için Iyileştirilmiş katmanlardaki yerel olarak yedekli veya coğrafi olarak yedekli yedekleme depolaması arasında seçim yapmak için esneklik sağlar. Yedeklemeler coğrafi olarak yedekli yedekleme depolaması 'nda depolandığında, yalnızca sunucunuzun barındırıldığı bölge içinde depolanmaz, ancak aynı zamanda [eşleştirilmiş bir veri merkezine](../best-practices-availability-paired-regions.md)çoğaltılır. Bu, daha iyi koruma ve olağanüstü durum durumunda sunucunuzu farklı bir bölgede geri yükleme yeteneği sağlar. Temel katman yalnızca yerel olarak yedekli yedekleme depolama alanı sunar.

> [!IMPORTANT]
> Yerel olarak yedekli veya coğrafi olarak yedekli depolamayı yedekleme amacıyla yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. Sunucu sağlandıktan sonra yedekleme alanı yedekliliği seçeneğini değiştiremezsiniz.

### <a name="backup-storage-cost"></a>Yedekleme depolama maliyeti

PostgreSQL için Azure veritabanı, sağlanan sunucu depolama alanınızı ek bir ücret ödemeden yedekleme depolama alanı olarak %100 ' e kadar sağlar. Kullanılan ek yedekleme depolama alanı aylık GB olarak ücretlendirilir. Örneğin, 250 GB depolama alanı olan bir sunucu sağladıysanız, ek ücret ödemeden sunucu yedeklemeleri için kullanılabilir 250 GB ek depolama alanı vardır. 250 GB 'tan fazla yedeklemeler için tüketilen depolama, [fiyatlandırma modeline](https://azure.microsoft.com/pricing/details/postgresql/)göre ücretlendirilir.

Bir sunucu tarafından kullanılan yedekleme depolama alanını izlemek için Azure portal bulunan Azure Izleyici 'de bulunan [yedekleme depolama alanı](concepts-monitoring.md) ölçüsünü kullanabilirsiniz. Kullanılan yedekleme depolama ölçümü, tüm tam veritabanı yedeklemeleri, fark yedeklemeleri ve sunucu için ayarlanan yedekleme Bekletme dönemi temel alınarak korunan depolama alanının toplamını temsil eder. Yedeklemelerin sıklığı Service tarafından yönetilmektedir ve daha önce açıklanmıştır. Sunucu üzerindeki yoğun işlem etkinliği yedekleme depolama alanı kullanımının toplam veritabanı boyutundan bağımsız olarak artmasına neden olabilir. Coğrafi olarak yedekli depolama için, yedekleme depolama alanı kullanımı yerel olarak yedekli depolama alanının iki katından oluşur. 

Yedekleme depolama maliyetini denetlemenin birincil yolu, uygun yedekleme saklama süresini ayarlayarak ve istediğiniz kurtarma hedeflerinizi karşılamak için doğru yedekleme artıklığı seçeneklerini belirleyerek yapılır. 7 ile 35 gün arasında bir bekletme dönemi seçebilirsiniz. Genel Amaçlı ve bellek için Iyileştirilmiş sunucular, yedeklemeler için coğrafi olarak yedekli depolamaya sahip olmak için seçim yapabilir.

## <a name="restore"></a>Geri Yükleme

PostgreSQL için Azure veritabanı 'nda geri yükleme gerçekleştirmek, özgün sunucunun yedeklemelerinden yeni bir sunucu oluşturur. 

İki adet geri yükleme türü mevcuttur:

- Tek **noktaya geri yükleme** , yedekleme artıklığı seçeneğiyle birlikte kullanılabilir ve özgün sunucunuz ile aynı bölgede yeni bir sunucu oluşturur.
- **Coğrafi geri yükleme** yalnızca, sunucunuzu coğrafi olarak yedekli depolama için yapılandırdıysanız kullanılabilir ve sunucunuzu farklı bir bölgeye geri yüklemenize olanak tanır.

Tahmini kurtarma süresi, veritabanı boyutları, işlem günlüğü boyutu, ağ bant genişliği ve aynı bölgedeki aynı bölgede Kurtarılan toplam veritabanı sayısı gibi çeşitli faktörlere bağlıdır. Kurtarma süresi, son veri yedeklemeye ve gerçekleştirilen kurtarma gereksinimlerinin miktarına bağlı olarak değişir. Genellikle 12 saatten daha düşüktür.

> [!NOTE] 
> Kaynak PostgreSQL sunucunuz, müşteri tarafından yönetilen anahtarlarla şifrelenirse, daha fazla dikkat edilmesi için lütfen [belgelere](concepts-data-encryption-postgresql.md) bakın. 

> [!NOTE]
> Silinen bir PostgreSQL sunucusunu geri yüklemek istiyorsanız [burada](howto-restore-dropped-server.md)belgelenen yordamı izleyin.

### <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme

Yedekleme yedeklemenizin seçeneğinden bağımsız olarak, yedekleme saklama döneminizin içinde herhangi bir noktaya geri yükleme gerçekleştirebilirsiniz. Özgün sunucu ile aynı Azure bölgesinde yeni bir sunucu oluşturulur. Fiyatlandırma Katmanı, işlem oluşturma, sanal çekirdek sayısı, depolama boyutu, yedekleme saklama süresi ve yedek artıklık seçeneği için özgün sunucunun yapılandırmasıyla oluşturulur.

Bir noktadan noktaya geri yükleme, birden çok senaryoda faydalıdır. Örneğin, bir kullanıcı verileri yanlışlıkla sildiğinde, önemli bir tabloyu veya veritabanını bırakır veya bir uygulama hata nedeniyle yanlışlıkla hatalı verileri olan iyi verilerin üzerine yazar.

Son beş dakika içinde zaman içindeki bir noktaya geri yükleyebilmeniz için bir sonraki işlem günlüğü yedeklemesinin tamamlanmasını beklemeniz gerekebilir.

Bırakılan bir tabloyu geri yüklemek istiyorsanız, 
1. Kaynak sunucuyu, zaman içinde nokta yöntemini kullanarak geri yükleyin.
2. Geri yüklenen sunucudan kullanarak tablonun dökümünü alın `pg_dump` .
3. Özgün sunucudaki kaynak tabloyu yeniden adlandır.
4. Özgün sunucudaki psql komut satırını kullanarak tablo içeri aktarın.
5. Geri yüklenen sunucuyu isteğe bağlı olarak silebilirsiniz.

>[!Note]
> Aynı sunucu için aynı anda birden fazla geri yükleme oluşturmanız önerilir. 

### <a name="geo-restore"></a>Coğrafi geri yükleme

Sunucunuzu coğrafi olarak yedekli yedeklemeler için yapılandırdıysanız, hizmeti hizmetin kullanılabildiği başka bir Azure bölgesine geri yükleyebilirsiniz. 4 TB 'a kadar depolamayı destekleyen sunucular, coğrafi olarak eşleştirilmiş bölgeye veya 16 TB 'a kadar depolamayı destekleyen herhangi bir bölgeye geri yüklenebilir. 16 TB 'a kadar depolamayı destekleyen sunucular için coğrafi yedeklemeler, 16 TB sunucu destekleyen her bölgede geri yüklenebilir. Desteklenen bölgelerin listesi için [PostgreSQL Için Azure veritabanı fiyatlandırma katmanlarını](concepts-pricing-tiers.md) gözden geçirin.

Coğrafi geri yükleme, sunucunuzun barındırıldığı bölgedeki bir olay nedeniyle kullanılamadığında varsayılan kurtarma seçeneğidir. Bir bölgedeki büyük ölçekli bir olay veritabanı uygulamanızın kullanılamamasına neden olursa, coğrafi olarak yedekli yedeklerden bir sunucuyu başka bir bölgedeki sunucuya geri yükleyebilirsiniz. Bir yedeklemenin alınması ve farklı bölgeye çoğaltılma arasında bir gecikme vardır. Bu gecikme bir saat kadar sürebilir. bu nedenle, bir olağanüstü durum oluşursa bir saatlik veri kaybı olabilir.

Coğrafi geri yükleme sırasında, değiştirilebilecek sunucu yapılandırması işlem oluşturma, sanal çekirdek, yedekleme saklama süresi ve yedekleme artıklığı seçeneklerini içerir. Fiyatlandırma katmanını değiştirme (temel, Genel Amaçlı veya bellek için Iyileştirilmiş) veya depolama boyutu desteklenmez.

> [!NOTE]
> Kaynak sunucunuz altyapıyı çift şifrelemeyi kullanıyorsa, sunucuyu geri yüklemek için kullanılabilir bölgeler dahil olmak üzere sınırlamalar vardır. Daha fazla ayrıntı için lütfen [altyapıya çift şifrelemeye](concepts-infrastructure-double-encryption.md) bakın.

### <a name="perform-post-restore-tasks"></a>Geri yükleme sonrası görevleri gerçekleştirme

Kurtarma mekanizmasından geri yükleme yapıldıktan sonra, kullanıcılarınızın ve uygulamalarınızın yedeklenmesi ve çalışması için aşağıdaki görevleri gerçekleştirmeniz gerekir:

- Yeni sunucu özgün sunucunun yerini alacak şekilde, istemcileri ve istemci uygulamalarını yeni sunucuya yeniden yönlendirin. Ayrıca, Kullanıcı adını da olarak değiştirin `username@new-restored-server-name` .
- Kullanıcıların bağlanabilmesi için uygun sunucu düzeyi güvenlik duvarı ve VNet kurallarının bulunduğundan emin olun. Bu kurallar, özgün sunucudan üzerine kopyalanmaz.
- Uygun oturum açma ve veritabanı düzeyi izinlerinin yerinde olduğundan emin olun
- Uyarıları uygun şekilde yapılandırma

## <a name="next-steps"></a>Sonraki adımlar

-  [Azure Portal](howto-restore-server-portal.md)kullanarak nasıl geri yükleyeceğinizi öğrenin.
-  [Azure CLI](howto-restore-server-cli.md)kullanarak geri yüklemeyi öğrenin.
- İş sürekliliği hakkında daha fazla bilgi edinmek için bkz. [iş sürekliliği genel bakış](concepts-business-continuity.md).