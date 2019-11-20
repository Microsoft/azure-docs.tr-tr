---
title: MySQL için Azure veritabanı 'nda yedekleme ve geri yükleme
description: Otomatik yedeklemeler ve MySQL için Azure veritabanı sunucunuzu geri yükleme hakkında bilgi edinin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fbd595c7de0bde4e8ba8b7aaa9a65aa5880c1165
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151921"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda yedekleme ve geri yükleme

MySQL için Azure veritabanı otomatik olarak sunucu yedeklemeleri oluşturur ve bunları Kullanıcı tarafından yerel olarak yedekli veya coğrafi olarak yedekli depolama olarak yapılandırılmış şekilde depolar. Sunucunuzu belirli bir noktaya geri yüklemek için yedeklemeler kullanılabilir. Yedekleme ve geri yükleme, verilerinizi yanlışlıkla bozulmasından veya silmekten koruyan bir iş sürekliliği stratejisinin önemli bir parçasıdır.

## <a name="backups"></a>Yedeklemeler

MySQL için Azure veritabanı, veri dosyalarının ve işlem günlüğünün yedeklerini alır. Desteklenen en fazla depolama boyutuna bağlı olarak, tam ve fark yedeklemeleri (4 TB maksimum depolama sunucusu) veya anlık görüntü yedeklemeleri (en fazla 16 TB depolama sunucusuna kadar) sunuyoruz. Bu yedeklemeler, yapılandırılmış yedekleme saklama döneminizin içindeki herhangi bir zamanda bir sunucuyu geri yüklemenize olanak tanır. Varsayılan yedekleme saklama süresi yedi gündür. [İsteğe bağlı olarak](howto-restore-server-portal.md#set-backup-configuration) 35 güne kadar yapılandırma yapabilirsiniz. Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir.

### <a name="backup-frequency"></a>Yedekleme sıklığı

Genellikle tam yedeklemeler haftalık olarak gerçekleşir, fark yedeklemeleri en fazla 4 TB depolama alanı olan sunucular için günde iki kez gerçekleşir. Anlık görüntü yedeklemeleri, 16 TB depolama alanını destekleyen sunucular için günde en az bir kez gerçekleşir. Her iki durumda da işlem günlüğü yedeklemeleri beş dakikada bir gerçekleşir. Tam yedeklemenin ilk anlık görüntüsü bir sunucu oluşturulduktan sonra hemen zamanlanır. İlk tam yedekleme, büyük bir geri yüklenen sunucuda daha uzun sürebilir. Yeni bir sunucunun geri yüklenebileceği en erken süre, ilk tam yedeklemenin tamamlandığı zamandır. Anlık görüntüler instantanious olduğundan, 16 TB 'a kadar depolamayı destekleyen sunucular, oluşturma saatine geri geri yüklenebilir.

### <a name="backup-redundancy-options"></a>Yedekleme artıklığı seçenekleri

MySQL için Azure veritabanı, Genel Amaçlı ve bellek için Iyileştirilmiş katmanlardaki yerel olarak yedekli veya coğrafi olarak yedekli yedekleme depolaması arasında seçim yapmak için esneklik sağlar. Yedeklemeler coğrafi olarak yedekli yedekleme depolaması 'nda depolandığında, yalnızca sunucunuzun barındırıldığı bölge içinde depolanmaz, ancak aynı zamanda [eşleştirilmiş bir veri merkezine](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)çoğaltılır. Bu, daha iyi koruma ve olağanüstü durum durumunda sunucunuzu farklı bir bölgede geri yükleme yeteneği sağlar. Temel katman yalnızca yerel olarak yedekli yedekleme depolama alanı sunar.

> [!IMPORTANT]
> Yedekleme için yerel olarak yedekli veya coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. Sunucu sağlandıktan sonra yedekleme depolama artıklığı seçeneğini değiştiremezsiniz.

### <a name="backup-storage-cost"></a>Yedekleme depolama maliyeti

MySQL için Azure veritabanı, sağlanan sunucu depolama alanınızı ek bir ücret ödemeden yedekleme depolama alanı olarak %100 ' e kadar sağlar. Genellikle, bu, yedi günlük bir yedekleme bekletme için uygundur. Kullanılan ek yedekleme depolama birimi GB aylık olarak ücretlendirilir.

Örneğin, 250 GB ile bir sunucu sağladıysanız, ek ücret ödemeden 250 GB yedekleme depolama alanı vardır. 250 GB 'tan fazla depolama alanı ücretlendirilir.

## <a name="restore"></a>Geri Yükleme

MySQL için Azure veritabanı 'nda, geri yükleme gerçekleştirmek özgün sunucunun yedeklemelerinden yeni bir sunucu oluşturur.

İki adet geri yükleme türü mevcuttur:

- Tek **noktaya geri yükleme** , yedekleme artıklığı seçeneğiyle birlikte kullanılabilir ve özgün sunucunuz ile aynı bölgede yeni bir sunucu oluşturur.
- **Coğrafi geri yükleme** yalnızca, sunucunuzu coğrafi olarak yedekli depolama için yapılandırdıysanız kullanılabilir ve sunucunuzu farklı bir bölgeye geri yüklemenize olanak tanır.

Tahmini kurtarma süresi, veritabanı boyutları, işlem günlüğü boyutu, ağ bant genişliği ve aynı bölgedeki aynı bölgede Kurtarılan toplam veritabanı sayısı gibi çeşitli faktörlere bağlıdır. Kurtarma zamanı genellikle 12 saatten düşüktür.

> [!IMPORTANT]
> Silinen sunucular **geri yüklenemez.** Sunucuyu silerseniz, sunucuya ait olan tüm veritabanları da silinir ve kurtarılamaz. Sunucu kaynaklarını korumak için dağıtım sonrası, yanlışlıkla silme veya beklenmeyen değişikliklerden, Yöneticiler [Yönetim kilitlerinin](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)faydalanabilir.

### <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme

Yedekleme yedeklemenizin seçeneğinden bağımsız olarak, yedekleme saklama döneminizin içinde herhangi bir noktaya geri yükleme gerçekleştirebilirsiniz. Özgün sunucu ile aynı Azure bölgesinde yeni bir sunucu oluşturulur. Fiyatlandırma Katmanı, işlem oluşturma, sanal çekirdek sayısı, depolama boyutu, yedekleme saklama süresi ve yedek artıklık seçeneği için özgün sunucunun yapılandırmasıyla oluşturulur.

Bir noktadan noktaya geri yükleme, birden çok senaryoda faydalıdır. Örneğin, bir kullanıcı verileri yanlışlıkla sildiğinde, önemli bir tabloyu veya veritabanını bırakır veya bir uygulama hata nedeniyle yanlışlıkla hatalı verileri olan iyi verilerin üzerine yazar.

Son beş dakika içinde zaman içindeki bir noktaya geri yükleyebilmeniz için bir sonraki işlem günlüğü yedeklemesinin tamamlanmasını beklemeniz gerekebilir.

### <a name="geo-restore"></a>Coğrafi Geri Yükleme

Sunucunuzu coğrafi olarak yedekli yedeklemeler için yapılandırdıysanız, hizmeti hizmetin kullanılabildiği başka bir Azure bölgesine geri yükleyebilirsiniz. 16 TB 'a kadar depolamayı destekleyen sunucular için, coğrafi yedeklemeler yalnızca 16 TB sunucu destekleyen bölgelerde geri yüklenebilir. Desteklenen bölgelerin listesi için [MySQL Için Azure veritabanı fiyatlandırma katmanlarını](concepts-pricing-tiers.md) gözden geçirin. 

Coğrafi geri yükleme, sunucunuzun barındırıldığı bölgedeki bir olay nedeniyle kullanılamadığında varsayılan kurtarma seçeneğidir. Bir bölgedeki büyük ölçekli bir olay veritabanı uygulamanızın kullanılamamasına neden olursa, coğrafi olarak yedekli yedeklerden bir sunucuyu başka bir bölgedeki sunucuya geri yükleyebilirsiniz. Bir yedeklemenin alınması ve farklı bölgeye çoğaltılma arasında bir gecikme vardır. Bu gecikme bir saat kadar sürebilir. bu nedenle, bir olağanüstü durum oluşursa bir saatlik veri kaybı olabilir.

Coğrafi geri yükleme sırasında, değiştirilebilecek sunucu yapılandırması işlem oluşturma, sanal çekirdek, yedekleme saklama süresi ve yedekleme artıklığı seçeneklerini içerir. Coğrafi geri yükleme sırasında fiyatlandırma katmanını (temel, Genel Amaçlı veya bellek için Iyileştirilmiş) veya depolama boyutunu değiştirme desteklenmiyor.

### <a name="perform-post-restore-tasks"></a>Geri yükleme sonrası görevleri gerçekleştirme

Kurtarma mekanizmasından geri yükleme yapıldıktan sonra, kullanıcılarınızın ve uygulamalarınızın yedeklenmesi ve çalışması için aşağıdaki görevleri gerçekleştirmeniz gerekir:

- Yeni sunucu özgün sunucunun yerini alacak şekilde, istemcileri ve istemci uygulamalarını yeni sunucuya yeniden yönlendirin
- Kullanıcıların bağlanabilmesi için uygun sunucu düzeyi güvenlik duvarı kurallarının yerinde olduğundan emin olun
- Uygun oturum açma ve veritabanı düzeyi izinlerinin yerinde olduğundan emin olun
- Uyarıları uygun şekilde yapılandırma

## <a name="next-steps"></a>Sonraki adımlar

- İş sürekliliği hakkında daha fazla bilgi edinmek için bkz. [iş sürekliliği genel bakış](concepts-business-continuity.md).
- Azure portal kullanarak bir noktaya geri yüklemek için, bkz. [Azure Portal kullanarak veritabanını zaman noktasına geri yükleme](howto-restore-server-portal.md).
- Azure CLı kullanarak zaman noktasına geri yüklemek için bkz. [CLI kullanarak veritabanını zaman içinde bir noktaya geri yükleme](howto-restore-server-cli.md).
