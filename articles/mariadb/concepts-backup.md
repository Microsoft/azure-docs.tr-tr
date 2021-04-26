---
title: Yedekleme ve geri yükleme-MariaDB için Azure veritabanı
description: Otomatik yedeklemeler ve MariaDB sunucusu için Azure veritabanınızı geri yükleme hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 8/13/2020
ms.openlocfilehash: 08e75f9eb5ea111cc977d02f66b945de4eae5126
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306186"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda yedekleme ve geri yükleme

MariaDB için Azure veritabanı otomatik olarak sunucu yedeklemeleri oluşturur ve bunları Kullanıcı tarafından yerel olarak yedekli veya coğrafi olarak yedekli depolama olarak yapılandırılmış şekilde depolar. Sunucunuzu belirli bir noktaya geri yüklemek için yedeklemeler kullanılabilir. Yedekleme ve geri yükleme her iş sürekliliği stratejisinin temel parçalarıdır çünkü bunlar verilerinizi yanlışlıkla bozulmalara veya silmelere karşı korur.

## <a name="backups"></a>Yedeklemeler

MariaDB için Azure veritabanı, veri dosyalarının ve işlem günlüğünün yedeklerini alır. Bu yedeklemeler, yapılandırılmış yedekleme saklama döneminizin içindeki herhangi bir zamanda bir sunucuyu geri yüklemenize olanak tanır. Varsayılan yedekleme saklama süresi yedi gündür. [İsteğe bağlı olarak](howto-restore-server-portal.md#set-backup-configuration) 35 güne kadar yapılandırma yapabilirsiniz. Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir.

Bu yedekleme dosyaları kullanıcılar kullanımına sunulmaz ve dışarı aktarılamaz. Bu yedeklemeler, yalnızca MySQL için Azure veritabanı 'nda geri yükleme işlemleri için kullanılabilir. Bir veritabanını kopyalamak için [mysqldump](howto-migrate-dump-restore.md) kullanabilirsiniz.

Yedekleme türü ve sıklığı, sunucular için arka uç depolamaya bağlıdır.

### <a name="backup-type-and-frequency"></a>Yedekleme türü ve sıklığı

#### <a name="basic-storage-servers"></a>Temel depolama sunucuları

Temel depolama, [temel katman sunucularını](concepts-pricing-tiers.md)destekleyen arka uç deposıdır. Temel depolama sunucularındaki yedeklemeler anlık görüntü tabanlıdır. Tam bir veritabanı anlık görüntüsü günlük olarak gerçekleştirilir. Temel depolama sunucuları için gerçekleştirilen fark yedeklemesi yoktur ve tüm anlık görüntü yedeklemeleri yalnızca tam veritabanı yedeklemelerdir.

İşlem günlüğü yedeklemeleri her beş dakikada bir gerçekleşir.

#### <a name="general-purpose-storage-servers-with-up-to-4-tb-storage"></a>4 TB 'a kadar depolama alanı içeren genel amaçlı depolama sunucuları

Genel amaçlı depolama, [genel amaçlı](concepts-pricing-tiers.md) ve [bellek için iyileştirilmiş katman](concepts-pricing-tiers.md) sunucusunu destekleyen arka uç deposıdır. 4 TB 'a kadar genel amaçlı depolamaya sahip sunucular için her hafta bir kez tam yedeklemeler gerçekleşir. Değişiklik yedeklemeleri günde iki kez gerçekleşir. İşlem günlüğü yedeklemeleri her beş dakikada bir gerçekleşir. 4 TB 'lık depolamaya kadar genel amaçlı depolama üzerindeki yedeklemeler anlık görüntü tabanlıdır ve yedekleme sırasında GÇ bant genişliği tüketir. 4 TB depolama alanı üzerinde büyük veritabanları (> 1 TB) için, şunları göz önünde bulundurmanız önerilir

- Yedekleme IOs için hesaba daha fazla IOPS sağlama veya
- Alternatif olarak, temel alınan depolama altyapısı tercih ettiğiniz [Azure Bölgelerinizdeki](./concepts-pricing-tiers.md#storage)kullanılabilir ise, 16 TB 'a kadar depolamayı destekleyen genel amaçlı depolamaya geçiş yapın. Genel amaçlı depolama için 16 TB 'a kadar depolamayı destekleyen ek bir ücret yoktur. 16 TB depolamaya geçiş konusunda yardım için lütfen Azure portal bir destek bileti açın.

#### <a name="general-purpose-storage-servers-with-up-to-16-tb-storage"></a>16 TB 'a kadar depolama alanı içeren genel amaçlı depolama sunucuları

[Azure bölgelerinin](./concepts-pricing-tiers.md#storage)bir alt kümesinde, tüm yeni sağlanan sunucular, 16 TB 'a kadar genel amaçlı depolama alanını destekleyebilir. Diğer bir deyişle, 16 TB 'a kadar depolama alanı, desteklendiği tüm [bölgeler](concepts-pricing-tiers.md#storage) için varsayılan genel amaçlı depolama alanı olur. Bu 16 TB depolama sunucularındaki yedeklemeler anlık görüntü tabanlıdır. İlk tam anlık görüntü yedeklemesi, sunucu oluşturulduktan hemen sonraya zamanlanır. Bu ilk tam anlık görüntü yedeklemesi sunucunun temel yedeklemesi olarak tutulur. Sonraki anlık görüntü yedeklemeleri yalnızca değişiklik yedeğidir.

Anlık görüntü değişiklik yedekleri günde en az bir kez gerçekleştirilir. Anlık görüntü değişiklik yedekleri belirli bir plana göre gerçekleştirilmez. Hareket günlüğü (MariaDB), son fark yedeklemesinden bu yana 50 GB 'yi aşmadığı takdirde, fark anlık görüntü yedeklemeleri 24 saatte bir gerçekleşir. Bir gün içinde en fazla altı anlık görüntü değişiklik yedeği alınabilir.

İşlem günlüğü yedeklemeleri her beş dakikada bir gerçekleşir.
 

### <a name="backup-retention"></a>Yedekleme dosyası saklama

Yedeklemeler, sunucusundaki yedekleme bekletme süresi ayarına göre tutulur. 35 gün günlük bir saklama süresi seçebilirsiniz. Varsayılan saklama süresi 7 gündür. [Azure Portal](howto-restore-server-portal.md#set-backup-configuration) veya [Azure CLI](howto-restore-server-cli.md#set-backup-configuration)kullanarak yedekleme yapılandırmasını güncelleştirerek sunucu oluşturma sırasında veya daha sonra saklama süresini ayarlayabilirsiniz. 

Yedekleme bekletme süresi, kullanılabilir yedeklemeler temel aldığı için zaman içinde bir nokta geri yüklemesi alma süresinin ne kadar geri alınacağını yönetir. Yedekleme bekletme süresi Ayrıca bir geri yükleme perspektifinden kurtarma penceresi olarak da ele alınabilir. Yedekleme saklama süresi içinde bir zaman içindeki bir anlık geri yükleme gerçekleştirmek için gereken tüm yedeklemeler, yedekleme depolama alanında tutulur. Örneğin, yedekleme Bekletme dönemi 7 gün olarak ayarlandıysa, kurtarma penceresi son 7 gün olarak değerlendirilir. Bu senaryoda, son 7 gün içinde sunucuyu geri yüklemek için gereken tüm yedeklemeler korunur. Yedi günlük bir yedekleme bekletme penceresi ile:

- 4 TB 'a kadar depolama alanı olan sunucular en fazla 2 tam veritabanı yedeklemesi, tüm değişiklik yedeklemeleri ve en erken tam veritabanı yedeklemesinden bu yana gerçekleştirilen işlem günlüğü yedeklemelerini korur.
-   16 TB 'a kadar depolamaya sahip sunucular, son 8 gündeki tüm fark anlık görüntülerini ve işlem günlüğü yedeklemelerini tam veritabanı anlık görüntüsünü tutar.

#### <a name="long-term-retention-of-backups"></a>Yedeklemeleri uzun süreli saklama
35 günden daha büyük olan yedeklemelerin uzun süreli tutulması henüz hizmet tarafından yerel olarak desteklenmiyor. Yedeklemeleri almak ve uzun süreli saklama için depolamak üzere mysqldump kullanma seçeneğiniz vardır. Destek ekibimiz, BT 'nin nasıl elde edilebileceği hakkında bir [adım adım makalede](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) oturum açtı. 

### <a name="backup-redundancy-options"></a>Yedekleme artıklığı seçenekleri

MariaDB için Azure veritabanı, Genel Amaçlı ve bellek için Iyileştirilmiş katmanlardaki yerel olarak yedekli veya coğrafi olarak yedekli yedekleme depolama arasında seçim yapmak için esneklik sağlar. Yedeklemeler coğrafi olarak yedekli yedekleme depolaması 'nda depolandığında, yalnızca sunucunuzun barındırıldığı bölge içinde depolanmaz, ancak aynı zamanda [eşleştirilmiş bir veri merkezine](../best-practices-availability-paired-regions.md)çoğaltılır. Bu, daha iyi koruma ve olağanüstü durum durumunda sunucunuzu farklı bir bölgede geri yükleme yeteneği sağlar. Temel katman yalnızca yerel olarak yedekli yedekleme depolama alanı sunar.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Yerel olarak yedekli iken coğrafi olarak yedekli yedekleme depolamasına taşıma
Yerel olarak yedekli veya coğrafi olarak yedekli depolamayı yedekleme amacıyla yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. Sunucu sağlandıktan sonra yedekleme alanı yedekliliği seçeneğini değiştiremezsiniz. Yedekleme depolama alanınızı yerel olarak yedekli depolama alanından coğrafi olarak yedekli depolamaya taşımak için yeni bir sunucu oluşturmak ve verileri [döküm ve geri yükleme](howto-migrate-dump-restore.md) kullanarak geçirmek desteklenen tek seçenektir.

### <a name="backup-storage-cost"></a>Yedekleme depolama maliyeti

MariaDB için Azure veritabanı, sağlanan sunucu depolama alanınızı ek bir ücret ödemeden yedekleme depolama alanı olarak %100 ' e kadar sağlar. Kullanılan ek yedekleme depolama alanı aylık GB olarak ücretlendirilir. Örneğin, 250 GB depolama alanı olan bir sunucu sağladıysanız, ek ücret ödemeden sunucu yedeklemeleri için kullanılabilir 250 GB ek depolama alanı vardır. 250 GB 'tan fazla yedeklemeler için tüketilen depolama, [fiyatlandırma modeline](https://azure.microsoft.com/pricing/details/mariadb/)göre ücretlendirilir. 

Bir sunucu tarafından tüketilen yedekleme depolama alanını izlemek için Azure portal aracılığıyla Azure Izleyici 'de [kullanılan yedekleme depolama alanı](concepts-monitoring.md) ölçüsünü kullanabilirsiniz. Kullanılan yedekleme depolama ölçümü, tüm tam veritabanı yedeklemeleri, fark yedeklemeleri ve sunucu için ayarlanan yedekleme Bekletme dönemi temel alınarak korunan depolama alanının toplamını temsil eder. Yedeklemelerin sıklığı Service tarafından yönetilmektedir ve daha önce açıklanmıştır. Sunucu üzerindeki yoğun işlem etkinliği yedekleme depolama alanı kullanımının toplam veritabanı boyutundan bağımsız olarak artmasına neden olabilir. Coğrafi olarak yedekli depolama için, yedekleme depolama alanı kullanımı yerel olarak yedekli depolama alanının iki katından oluşur. 

Yedekleme depolama maliyetini denetlemenin birincil yolu, uygun yedekleme saklama süresini ayarlayarak ve istediğiniz kurtarma hedeflerinizi karşılamak için doğru yedekleme artıklığı seçeneklerini belirleyerek yapılır. 7 ile 35 gün arasında bir bekletme dönemi seçebilirsiniz. Genel Amaçlı ve bellek için Iyileştirilmiş sunucular, yedeklemeler için coğrafi olarak yedekli depolamaya sahip olmak için seçim yapabilir.

## <a name="restore"></a>Geri Yükleme

MariaDB için Azure veritabanı 'nda geri yükleme gerçekleştirmek, özgün sunucunun yedeklemelerinden yeni bir sunucu oluşturur ve sunucuda bulunan tüm veritabanlarını geri yükler.

İki adet geri yükleme türü mevcuttur:

- Bir **noktaya geri yükleme** , yedekleme artıklığı seçeneğiyle kullanılabilir ve tam ve işlem günlüğü yedeklemelerinin birleşimini kullanan özgün sunucunuz ile aynı bölgede yeni bir sunucu oluşturur.
- **Coğrafi geri yükleme** yalnızca, sunucunuzu coğrafi olarak yedekli depolama için yapılandırdıysanız kullanılabilir ve sunucunuzu en son yedeklemenin kullanıldığı farklı bir bölgeye geri yüklemenize olanak tanır.

Tahmini kurtarma süresi, veritabanı boyutları, işlem günlüğü boyutu, ağ bant genişliği ve aynı bölgedeki aynı bölgede Kurtarılan toplam veritabanı sayısı gibi çeşitli faktörlere bağlıdır. Kurtarma zamanı genellikle 12 saatten düşüktür.

> [!IMPORTANT]
> Silinen sunucular yalnızca, yedeklemelerin silinmesinden sonra **beş gün** içinde geri yüklenebilir. Veritabanı yedeklemesine yalnızca sunucuyu barındıran Azure aboneliğinden erişilebilir ve geri yüklenebilir. Bırakılan bir sunucuyu geri yüklemek için [belgelenen adımlara](howto-restore-dropped-server.md)bakın. Sunucu kaynaklarını korumak için dağıtım sonrası, yanlışlıkla silme veya beklenmeyen değişikliklerden, Yöneticiler [Yönetim kilitlerinin](../azure-resource-manager/management/lock-resources.md)faydalanabilir.

### <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme

Yedekleme yedeklemenizin seçeneğinden bağımsız olarak, yedekleme saklama döneminizin içinde herhangi bir noktaya geri yükleme gerçekleştirebilirsiniz. Özgün sunucu ile aynı Azure bölgesinde yeni bir sunucu oluşturulur. Fiyatlandırma Katmanı, işlem oluşturma, sanal çekirdek sayısı, depolama boyutu, yedekleme saklama süresi ve yedek artıklık seçeneği için özgün sunucunun yapılandırmasıyla oluşturulur.

Bir noktadan noktaya geri yükleme, birden çok senaryoda faydalıdır. Örneğin, bir kullanıcı verileri yanlışlıkla sildiğinde, önemli bir tabloyu veya veritabanını bırakır veya bir uygulama hata nedeniyle yanlışlıkla hatalı verileri olan iyi verilerin üzerine yazar.

Son beş dakika içinde zaman içindeki bir noktaya geri yükleyebilmeniz için bir sonraki işlem günlüğü yedeklemesinin tamamlanmasını beklemeniz gerekebilir.

### <a name="geo-restore"></a>Coğrafi geri yükleme

Sunucunuzu coğrafi olarak yedekli yedeklemeler için yapılandırdıysanız, hizmeti hizmetin kullanılabildiği başka bir Azure bölgesine geri yükleyebilirsiniz. 4 TB 'a kadar depolamayı destekleyen sunucular, coğrafi olarak eşleştirilmiş bölgeye veya 16 TB 'a kadar depolamayı destekleyen herhangi bir bölgeye geri yüklenebilir. 16 TB 'a kadar depolamayı destekleyen sunucular için coğrafi yedeklemeler, 16 TB sunucu destekleyen her bölgede geri yüklenebilir. Desteklenen bölgelerin listesi için, [MariaDB Için Azure veritabanı fiyatlandırma katmanlarını](concepts-pricing-tiers.md) gözden geçirin.

Coğrafi geri yükleme, sunucunuzun barındırıldığı bölgedeki bir olay nedeniyle kullanılamadığında varsayılan kurtarma seçeneğidir. Bir bölgedeki büyük ölçekli bir olay veritabanı uygulamanızın kullanılamamasına neden olursa, coğrafi olarak yedekli yedeklerden bir sunucuyu başka bir bölgedeki sunucuya geri yükleyebilirsiniz. Coğrafi geri yükleme, sunucunun en son yedeklemesini kullanır. Bir yedeklemenin alınması ve farklı bölgeye çoğaltılma arasında bir gecikme vardır. Bu gecikme bir saat kadar sürebilir. bu nedenle, bir olağanüstü durum oluşursa bir saatlik veri kaybı olabilir.

> [!IMPORTANT]
>Yeni oluşturulan bir sunucu için coğrafi geri yükleme gerçekleştirilirse, ilk tam anlık görüntü yedekleme kopyalama süresi çok daha yüksek olduğundan, ilk yedekleme eşitlemesi veri boyutuna bağlı olarak 24 saatten fazla sürebilir. Sonraki anlık görüntü yedeklemeleri artımlı kopyaya sahiptir ve bu nedenle geri yüklemeler, 24 saat sonra sunucu oluşturulduktan sonra daha hızlı gerçekleşir. RTO 'nizi tanımlamak için coğrafi geri yüklemeleri değerlendiriyorsanız, coğrafi geri yüklemeyi, daha iyi tahminler için **yalnızca 24 saat sonra** sunucu oluşturulduktan sonra bekleyip değerlendirmenizi öneririz.

Coğrafi geri yükleme sırasında, değiştirilebilecek sunucu yapılandırması işlem oluşturma, sanal çekirdek, yedekleme saklama süresi ve yedekleme artıklığı seçeneklerini içerir. Coğrafi geri yükleme sırasında fiyatlandırma katmanını (temel, Genel Amaçlı veya bellek için Iyileştirilmiş) veya depolama boyutunu değiştirme desteklenmiyor.

Tahmini kurtarma süresi, veritabanı boyutları, işlem günlüğü boyutu, ağ bant genişliği ve aynı bölgedeki aynı bölgede Kurtarılan toplam veritabanı sayısı gibi çeşitli faktörlere bağlıdır. Kurtarma zamanı genellikle 12 saatten düşüktür.

### <a name="perform-post-restore-tasks"></a>Geri yükleme sonrası görevleri gerçekleştirme

Kurtarma mekanizmasından geri yükleme yapıldıktan sonra, kullanıcılarınızın ve uygulamalarınızın yedeklenmesi ve çalışması için aşağıdaki görevleri gerçekleştirmeniz gerekir:

- Yeni sunucu özgün sunucunun yerini alacak şekilde, istemcileri ve istemci uygulamalarını yeni sunucuya yeniden yönlendirin
- Kullanıcıların bağlanabilmesi için uygun VNet kurallarının yerinde olduğundan emin olun. Bu kurallar, özgün sunucudan üzerine kopyalanmaz.
- Uygun oturum açma ve veritabanı düzeyi izinlerinin yerinde olduğundan emin olun
- Uyarıları uygun şekilde yapılandırma

## <a name="next-steps"></a>Sonraki adımlar

- İş sürekliliği hakkında daha fazla bilgi edinmek için bkz. [iş sürekliliği genel bakış](concepts-business-continuity.md).
- Azure portal kullanarak bir noktaya geri yüklemek için, bkz. [Azure Portal kullanarak sunucuyu zaman noktasına geri yükleme](howto-restore-server-portal.md).
- Azure CLı 'yı kullanarak bir noktaya geri yüklemek için bkz. [CLI kullanarak sunucuyu bir zaman noktaya geri yükleme](howto-restore-server-cli.md).
