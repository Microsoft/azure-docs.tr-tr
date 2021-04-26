---
title: Azure BareMetal 'da Oracle için yüksek kullanılabilirlik özellikleri
description: Bir Oracle veritabanı için BareMetal 'da kullanılabilen özellikler hakkında bilgi edinin.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/16/2021
ms.openlocfilehash: b27dc4b857d553be791528cbd91aee70b2294a92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600225"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Azure BareMetal 'da Oracle için yüksek kullanılabilirlik özellikleri

Bu makalede, Oracle 'ın temel yüksek kullanılabilirlik ve olağanüstü durum kurtarma özelliklerine bakacağız.

Oracle, Oracle veritabanlarını çalıştırmaya yönelik dayanıklı bir platform oluşturmak için birçok özellik sunar. Tek bir özellik her başarısızlık türü için kapsam sağlarken, teknolojileri katmanlı bir biçimde birleştirmek, yüksek oranda kullanılabilir bir sistem oluşturur. Kullanılabilirliği sürdürmek için her özellik gerekli değildir. Ancak, Stratejileri birleştiren oluşan arızaların en iyi şekilde korunmasını sağlar. 

## <a name="flashback-database"></a>Flashback veritabanı

[Flashback veritabanı](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) özelliği Oracle Database Enterprise Edition gelir. Flashback veritabanı, veritabanını zaman içinde belirli bir noktaya geri sarar. Bu özellik bir [Kurtarma Yöneticisi (RMAN)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) noktadan sonra, geri yükleme sonrasında ileri-WINDS yerine geçerli zamandan sonra yeniden WINDS kurtarma işleminden farklıdır. Bunun sonucunda, Flashback veritabanının çok daha hızlı tamamlama süreleri vardır.
 
Bu özelliği, [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)ile birlikte kullanabilirsiniz. Flashback veritabanı, bir veritabanı yöneticisinin başarısız bir veritabanını tam RMAN geri yükleme ve kurtarma olmadan bir Data Guard yapılandırmasına geri yüklemesine olanak tanır. Bu özellik çok daha hızlı bir şekilde olağanüstü durum kurtarma özelliğini (ve etkin veri koruma ile tüm boşaltılan raporlama ve yedekleme avantajlarını) geri yüklemenize olanak tanır.
 
Bu özelliği, bekleme veritabanında bir zaman gecikmeli yineleme yerine kullanabilirsiniz. Bekleme bir veritabanı, sorundan önce bir noktaya doğru bir şekilde geri alınabilir.
 
Oracle Database, hızlı kurtarma alanında (FRA) Flashback günlüklerini tutar. Bu Günlükler, yineleme günlüklerinden ayrıdır ve FRA içinde daha fazla alan gerektirir. Varsayılan olarak, 24 saatlik Flashback günlüğü tutulur, ancak bu ayarı gereksinimlerinize göre değiştirebilirsiniz.

## <a name="oracle-real-application-clusters"></a>Oracle gerçek uygulama kümeleri

[Oracle gerçek uygulama kümeleri (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) , birden çok bağlı sunucunun son kullanıcılara ve uygulamalara tek bir veritabanı hizmeti olarak görünmesine izin verir. Bu özellik birçok hata noktasını kaldırır ve Oracle veritabanları için tanınan yüksek kullanılabilirliğe sahip etkin/etkin çözümdür.

Oracle 'ın [yüksek kullanılabilirliğe genel bakış ve En Iyi uygulamalardan](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html)oluşan aşağıdaki şekilde gösterildiği gibi, uygulama katmanına tek bir RAC veritabanı sunulur. Uygulamalar, trafiği belirli bir veritabanı örneğine yönlendiren tarama dinleyicisine bağlanır. RAC, ayrı işlem düğümlerinde veri tutarlılığını sağlamak için birden çok örneğe erişimi denetler.

![Oracle RAC mimarisine genel bakış gösteren diyagram.](media/oracle-high-availability/oracle-real-application-clusters.png)

Bir örnek başarısız olursa, hizmet kalan diğer tüm örneklerde devam eder. Çözümde dağıtılan her bir veritabanı, n + 1 ' in RAC yapılandırmasında olur; burada n, hizmeti desteklemek için gereken en düşük işleme gücünden oluşur.

Oracle Database Hizmetleri, bir örnek saydam bir şekilde başarısız olduğunda düğümlerin arasında yük devretme yapmasına izin vermek için kullanılır. Bu tür arızalar planlanmış veya planlanmamış olabilir. Uygulamayla çalışma (hızlı uygulama bildirimi olayları), bir örnek kullanılamaz hale getirildiğinde hizmet, çalışan bir düğüme taşınır. Hizmet, tercih edilen ya da kullanılabilir olarak hizmet yapılandırmasında belirtilen bir düğüme gider.

Oracle Database hizmetlerinin başka bir temel özelliği, rolüne bağlı olarak yalnızca bir hizmeti başlatıyor. Bu özellik bir Data Guard yük devretmesi olduğunda kullanılır. Data Guard kullanılarak dağıtılan tüm desenler, bir veritabanı hizmetini Data Guard rolüne bağlamak için gereklidir.

Örneğin, iki hizmet oluşturulabilir, \_ DB \_ Uygulamam ve My \_ DB \_ . MY \_ DB \_ App Service, yalnızca VERITABANı örneği birincil veri koruma rolüyle başlatıldığında başlatılır. MY \_ DB, \_ yalnızca Data Guard rolü fiziksel bekleme olduğunda başlatılır \_ . Bu yapılandırma, uygulamaların App Service 'e işaret etmesine olanak tanır \_ . Ayrıca, etkin bekleme konumuna boşaltılabilecek ve as hizmetine işaret eden rapor \_ .

## <a name="oracle-data-guard"></a>Oracle Data Guard

Data Guard ile ayrı fiziksel donanımda bir veritabanının özdeş bir kopyasını tutabilirsiniz. İdeal olarak, bu donanımın birincil veritabanından coğrafi olarak kaldırılması gerekir. Veri koruması, mesafe koruma modlarında bir sınır olmasına rağmen mesafe üzerinde hiçbir sınır yoktur. Artan mesafe, siteler arasında gecikme ekler ve bu da bazı seçeneklerin (zaman uyumlu çoğaltma gibi) artık önemli olmasına neden olabilir.

Data Guard, depolama düzeyinde çoğaltma üzerinde avantajlar sağlar:

- Çoğaltma veritabanı duyarlı olduğu için, yalnızca ilgili trafik çoğaltılır.
- Bazı iş yükleri geçici tablosallarında yüksek giriş/çıkış oluşturabilir ve bu nedenle, bekleme durumunda gerekli değildir ve çoğaltılmamıştır.
- Çoğaltılan bloklarla ilgili doğrulama, bekleme veritabanında oluşur, bu nedenle birincil veritabanındaki fiziksel bozulmalar bekleme veritabanına çoğaltılmaz.
- Mantıksal ve blok bozulmaları ve kayıp yazma bozukluklarının yapılmasını önler. Ayrıca, depolama yöneticilerinin bekleme konumuna çoğaltılmasından oluşan hatalar riskini ortadan kaldırır.
Yineleme önceden belirlenmiş bir süre için geciktirilebilir, bu nedenle Kullanıcı hataları bekleme moduna hemen çoğaltılmaz.

## <a name="azure-netapp-files-snapshots"></a>Azure NetApp Files anlık görüntüler

BareMetal 'da kullanılan NetApp dosyaları depolama çözümü, birimlerin anlık görüntülerini oluşturmanıza olanak sağlar. Anlık görüntüler bir dosya sistemini hızla belirli bir noktaya döndürmenize olanak tanır. Anlık görüntü teknolojileri, bir veritabanı yedeklemesini geri yüklemek için gereken sürenin bir bölümü olan kurtarma süresi hedefi (RTO) sürelerine izin verir.

Oracle veritabanları için anlık görüntü işlevselliği, Azure NetApp SnapCenter aracılığıyla kullanılabilir. SnapCenter, yedekleme için anlık görüntüler sağlar, anlık görüntü Kasası size çevrimdışı bir işlem yapmanızı sağlar ve ek kopyalama, self servis geri yükleme ve diğer işlemleri sağlar.

## <a name="recovery-manager"></a>Kurtarma Yöneticisi

Kurtarma Yöneticisi (RMAN), fiziksel veritabanı yedeklemeleri almak için tercih edilen yardımcı programdır. RMAN, veritabanı denetim dosyası (veya merkezi bir kurtarma kataloğu) ile etkileşime geçtiğinde, aşağıdakiler de dahil olmak üzere veritabanının çeşitli çekirdek bileşenlerini koruyabilir:

- Veritabanı veri dosyaları
- Arşivlenmiş yineleme günlükleri
- Veritabanı denetim dosyaları
- Veritabanı başlatma dosyaları (SPFile)

RMAN, sıcak veya soğuk veritabanı yedeklemeleri almanıza olanak sağlar. Bu yedeklemeleri, bekleyen veritabanları oluşturmak veya ortamları kopyalamak üzere veritabanlarını çoğaltmak için kullanabilirsiniz. RMAN 'ın geri yükleme doğrulama işlevi de vardır. Bu işlev bir yedekleme kümesini okur ve veritabanını zaman içinde belirli bir noktaya kurtarmak için kullanıp kullanmayacağınızı belirler.

RMAN, Oracle tarafından sağlanmış bir yardımcı program olduğundan, veritabanı dosyalarının iç yapısını okur. Bu, yedekleme ve geri yükleme işlemleri sırasında fiziksel ve mantıksal bozulma denetimleri çalıştırmanızı sağlar. Ayrıca, veritabanı veri dosyalarını kurtarabilir ve tek tek veri dosyalarını ve tablo alanlarını belirli bir zaman noktasına geri yükleyebilirsiniz. Bunlar, depolama anlık görüntüleri üzerinde RMAN tekliflerinin avantajlarına sahiptir. RMAN yedeklemeleri, anlık görüntüleri kullanmadığında tam veri kaybına karşı son savunma hattı sunar.

## <a name="next-steps"></a>Sonraki adımlar

BareMetal altyapısında Oracle çalıştıran koruma ve performansı iyileştirmek için seçenekler ve öneriler hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Oracle BareMetal altyapı sunucuları için seçenekler](options-considerations-high-availability.md)
