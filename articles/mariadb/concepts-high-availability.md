---
title: Yüksek kullanılabilirlik-MariaDB için Azure veritabanı
description: Bu makalede, MariaDB için Azure veritabanı 'nda yüksek kullanılabilirlik hakkında bilgi sağlanır
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 4dcb1ac7ce4b468374993c11578bce553f766a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664342"
---
# <a name="high-availability-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda yüksek kullanılabilirlik
MariaDB için Azure veritabanı hizmeti, [% 99,99](https://azure.microsoft.com/support/legal/sla/MariaDB) çalışma süresi için mali olarak desteklenen hizmet düzeyi SÖZLEŞMESI (SLA) ile garantili yüksek düzeyde kullanılabilirlik sağlar. MariaDB için Azure veritabanı, Kullanıcı tarafından sağlanan ölçek işlem işlemi gibi planlı olaylar sırasında ve ayrıca temel alınan donanım, yazılım veya ağ başarısızlığı gibi planlanmamış olaylar gerçekleştiğinde yüksek kullanılabilirlik sağlar. MariaDB için Azure veritabanı en kritik durumlardan hızla kurturabilir ve bu hizmeti kullanırken neredeyse hiçbir uygulama süresi olmamasını sağlar.

MariaDB için Azure veritabanı, yüksek çalışma süresi gerektiren görev açısından kritik veritabanlarını çalıştırmaya uygundur. Azure mimarisi üzerinde geliştirilen hizmette, ek bileşenleri yapılandırmanıza gerek kalmadan planlanmış ve planlanmamış kesintilerden oluşan veritabanı kapalı kalma süresini azaltmak için yüksek kullanılabilirlik, artıklık ve dayanıklılık özelliklerine sahip olursunuz. 

## <a name="components-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı bileşenleri

| **Bileşen** | **Açıklama**|
| ------------ | ----------- |
| <b>MariaDB veritabanı sunucusu | MariaDB için Azure veritabanı, veritabanı sunucuları için güvenlik, yalıtım, kaynak korumaları ve hızlı yeniden başlatma özelliği sağlar. Bu yetenekler, saniye cinsinden bir kesinti olduktan sonra ölçekleme ve veritabanı sunucusu kurtarma işlemi gibi işlemleri kolaylaştırır. <br/> Veritabanı sunucusundaki veri değişiklikleri genellikle bir veritabanı işlemi bağlamında meydana gelir. Tüm veritabanı değişiklikleri zaman uyumlu olarak, veritabanı sunucusuna eklenen Azure Storage 'da yazma öncesi Günlükler (ib_log) biçiminde kaydedilir. Veritabanı [denetim noktası](https://mariadb.com/kb/innodb-redo-log/#checkpoints) işlemi sırasında, veritabanı sunucusu belleğinden veri sayfaları da depolamaya silinir. |
| <b>Uzak depolama | Tüm MariaDB fiziksel veri dosyaları ve günlük dosyaları, veri yedekliliği, kullanılabilirliği ve güvenilirliği sağlamak için bir bölge içinde verilerin üç kopyasını depolamak üzere tasarlanmış Azure Storage 'da depolanır. Depolama katmanı, veritabanı sunucusundan de bağımsızdır. Bu, başarısız bir veritabanı sunucusundan ayrılabilir ve birkaç saniye içinde yeni bir veritabanı sunucusuna yeniden iliştirilebilir. Ayrıca, Azure Storage her türlü depolama hatalarını sürekli izler. Bir blok bozulması algılanırsa, yeni bir depolama kopyası örnekleyerek otomatik olarak düzeltilir. |
| <b>Ağ geçidi | Ağ geçidi bir veritabanı proxy 'si görevi görür ve tüm istemci bağlantılarını veritabanı sunucusuna yönlendirir. |

## <a name="planned-downtime-mitigation"></a>Planlanmış kapalı kalma süresi hafifletme
MariaDB için Azure veritabanı, planlanan kapalı kalma işlemleri sırasında yüksek kullanılabilirlik sağlamak üzere tasarlanmıştır. 

![Azure MariaDB 'de elastik ölçeklendirmeyi görüntüleme](./media/concepts-high-availability/elastic-scaling-mariadb-server.png)

Aşağıda bazı planlı bakım senaryoları verilmiştir:

| **Senaryo** | **Açıklama**|
| ------------ | ----------- |
| <b>İşlem ölçeği artırma/azaltma | Kullanıcı işlem ölçeğini artırma/azaltma işlemi gerçekleştirdiğinde, ölçeklenmiş işlem yapılandırması kullanılarak yeni bir veritabanı sunucusu sağlanır. Eski veritabanı sunucusunda, etkin kontrol noktalarının tamamlanabilmesi için, istemci bağlantıları boşaltılır, kaydedilmeyen tüm işlemler iptal edilir ve sonra kapatılır. Depolama daha sonra eski veritabanı sunucusundan ayrılır ve yeni veritabanı sunucusuna eklenir. İstemci uygulaması bağlantıyı yeniden dener veya yeni bir bağlantı kurmaya çalıştığında, ağ geçidi bağlantı isteğini yeni veritabanı sunucusuna yönlendirir.|
| <b>Depolama alanını ölçeklendirme | Depolama alanının ölçeği, çevrimiçi bir işlemdir ve veritabanı sunucusunu kesintiye uğratmaz.|
| <b>Yeni yazılım dağıtımı (Azure) | Yeni özellik dağıtımı veya hata düzeltmeleri, hizmetin planlı bakımının bir parçası olarak otomatik olarak gerçekleşir. Daha fazla bilgi için [belgelere](concepts-monitoring.md#planned-maintenance-notification)bakın ve ayrıca [portalınızı](https://aka.ms/servicehealthpm)kontrol edin.|
| <b>İkincil sürüm yükseltmeleri | MariaDB için Azure veritabanı, veritabanı sunucularını otomatik olarak Azure tarafından belirlenen alt sürüme ekler. Hizmetin planlı bakım kapsamında olur. Bu, saniye cinsinden kısa bir kapalı kalma süresine neden olur ve veritabanı sunucusu yeni ikincil sürümle otomatik olarak yeniden başlatılır. Daha fazla bilgi için [belgelere](concepts-monitoring.md#planned-maintenance-notification)bakın ve ayrıca [portalınızı](https://aka.ms/servicehealthpm)kontrol edin.|


##  <a name="unplanned-downtime-mitigation"></a>Planlanmamış kapalı kalma süresi azaltma

Planlanmamış kapalı kalma süresi, temel alınan donanım hatası, ağ sorunları ve yazılım hataları da dahil olmak üzere öngörülemeyen hataların sonucu olarak gerçekleşebilir. Veritabanı sunucusu beklenmedik şekilde kapanıyorsa, saniye cinsinden yeni bir veritabanı sunucusu otomatik olarak sağlanır. Uzak depolama, yeni veritabanı sunucusuna otomatik olarak eklenir. MariaDB altyapısı, WAL ve veritabanı dosyalarını kullanarak kurtarma işlemini gerçekleştirir ve istemcilerin bağlanmasına izin vermek için veritabanı sunucusunu açar. İşlenmemiş işlemler kaybolur ve uygulama tarafından yeniden denenmeleri gerekir. Planlanmamış kapalı kalma süresini önlemeden, MariaDB için Azure veritabanı, insan müdahalesine gerek kalmadan hem veritabanı sunucusunda hem de depolama katmanlarında kurtarma işlemlerini otomatik olarak gerçekleştirerek kapalı kalma süresini azaltır. 


![Azure MariaDB 'de yüksek kullanılabilirlik görünümü](./media/concepts-high-availability/availability-mariadb-server.png)

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Planlanmamış kapalı kalma süresi: hata senaryoları ve hizmet kurtarma
İşte birkaç hata senaryosu ve MariaDB için Azure veritabanı 'nın otomatik olarak nasıl kurtaracağını öğrenin:

| **Senaryo** | **Otomatik kurtarma** |
| ---------- | ---------- |
| <B>Veritabanı sunucusu hatası | Temel alınan bazı donanım hatası nedeniyle veritabanı sunucusu kapalıysa, etkin bağlantılar bırakılır ve tüm esnek işlemler iptal edilir. Yeni bir veritabanı sunucusu otomatik olarak dağıtılır ve uzak veri depolama yeni veritabanı sunucusuna eklenir. Veritabanı kurtarma işlemi tamamlandıktan sonra istemcileri, ağ geçidi aracılığıyla yeni veritabanı sunucusuna bağlanabilir. <br /> <br /> MariaDB veritabanlarını kullanan uygulamaların, kesilen bağlantıları ve başarısız işlemleri algılayıp yeniden denedikleri bir şekilde oluşturulması gerekir.  Uygulama yeniden denenirse, ağ geçidi, bağlantıyı yeni oluşturulan veritabanı sunucusuna şeffaf olarak yeniden yönlendirir. |
| <B>Depolama hatası | Uygulamalar, disk arızası veya fiziksel blok bozulması gibi depolama ile ilgili herhangi bir sorun için herhangi bir etkisi görmez. Veriler 3 kopyada depolandığından, verilerin kopyası, çalışan depolama alanı tarafından sunulur. Blok bozulmaları otomatik olarak düzeltilir. Verilerin bir kopyası kaybolursa, verilerin yeni bir kopyası otomatik olarak oluşturulur. |

Kurtarmak için Kullanıcı eylemi gerektiren bazı hata senaryoları şunlardır:

| **Senaryo** | **Kurtarma planı** |
| ---------- | ---------- |
| <b> Bölge hatası | Bölge arızası nadir bir olaydır. Ancak, bir bölge hatasından korumaya ihtiyacınız varsa, diğer bölgelerde olağanüstü durum kurtarma (DR) için bir veya daha fazla okuma çoğaltması yapılandırabilirsiniz. (Ayrıntılar için okuma çoğaltmaları oluşturma ve yönetme ile ilgili [Bu makaleye](howto-read-replicas-portal.md) bakın). Bölge düzeyinde bir hata olması durumunda, diğer bölgede yapılandırılan okuma çoğaltmasını üretim veritabanı sunucunuz olacak şekilde el ile yükseltebilirsiniz. |
| <b> Mantıksal/Kullanıcı hataları | Yanlışlıkla bırakılan tablolar veya yanlış güncelleştirilmiş veriler gibi Kullanıcı hatalarından kurtarma, hata oluşmadan hemen önce geçen zamana kadar verileri geri yükleyerek ve kurtararak bir [zaman içinde kurtarma](concepts-backup.md) (sür) gerçekleştirmeyi içerir.<br> <br>  Veritabanı sunucusundaki tüm veritabanları yerine veritabanlarının yalnızca bir alt kümesini veya belirli tabloları geri yüklemek isterseniz, veritabanı sunucusunu yeni bir örneğe geri yükleyebilir, tabloları [mysqldump](howto-migrate-dump-restore.md)aracılığıyla dışarı aktarabilir ve ardından [geri yükleme](howto-migrate-dump-restore.md#restore-your-mariadb-database) kullanarak bu tabloları veritabanınıza geri yükleyebilirsiniz. |



## <a name="summary"></a>Özet

MariaDB için Azure veritabanı, veritabanı sunucularının, yedekli depolamanın ve ağ geçidinden etkin yönlendirmenin hızlı bir şekilde yeniden başlatma özelliğini sağlar. Ek veri koruma için, yedeklemeleri coğrafi olarak çoğaltılacak şekilde yapılandırabilir ve ayrıca diğer bölgelerde bir veya daha fazla okuma çoğaltması dağıtabilirsiniz. Devralınan yüksek kullanılabilirlik özellikleri sayesinde, MariaDB için Azure veritabanı veritabanlarınızı en yaygın kesintilerden korur ve sektör lideri, finans-desteklenen [% 99,99 çalışma süresi SLA 'sı](https://azure.microsoft.com/support/legal/sla/MariaDB)sağlar. Tüm bu kullanılabilirlik ve güvenilirlik özellikleri, Azure 'un görev açısından kritik uygulamalarınızı çalıştırmak için ideal platform olmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure bölgeleri](../availability-zones/az-overview.md) hakkında bilgi edinin
- [Geçici bağlantı hatalarını işleme](concepts-connectivity.md) hakkında bilgi edinin
- [Okuma çoğaltmalarıyla verilerinizi çoğaltmayı](howto-read-replicas-portal.md) öğrenin