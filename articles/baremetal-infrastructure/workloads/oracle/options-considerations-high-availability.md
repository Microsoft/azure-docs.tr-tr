---
title: Seçenekler veya Oracle BareMetal altyapı sunucuları
description: Oracle BareMetal altyapı sunucuları için seçenekler ve konular hakkında bilgi edinin.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590346"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Oracle BareMetal altyapı sunucuları için seçenekler

Bu makalede, BareMetal altyapı sunucularında Oracle çalıştıran en yüksek koruma ve performans düzeyini almak için seçenekleri ve önerileri göz önünde bulunduracağız. 

## <a name="data-guard-protection-modes"></a>Data Guard Koruma modları

Data Guard, yalnızca Oracle gerçek uygulamaları kümesi (RAC), mantıksal çoğaltma (GoldenGate gibi) ve depolama tabanlı çoğaltma aracılığıyla kullanılabilir koruma sunar. 

| Koruma modu | Description |
| --- | --- |
| **En yüksek performans** | Varsayılan koruma modu. Birincil veritabanının performansını etkilemeden en yüksek koruma düzeyini sağlar. Veriler, birincil veritabanı yineleme akışına yazıldığı anda taahhüt edilir. Daha sonra bekleme veritabanına zaman uyumsuz olarak çoğaltılır. Genellikle, bekleme veritabanı bu dosyayı saniye içinde alır, ancak bu etkiye hiçbir garanti verilmez. Bu mod, birincil ve bekleme siteleri arasında düşük gecikme süresine gerek kalmadan genellikle iş gereksinimlerini (gecikme izlemenin yanı sıra) karşılar.<br /><br />En iyi işletimsel kalıcılığı sağlar; Ancak, sıfır veri kaybını garanti etmez.   |
| **Maksimum kullanılabilirlik** | Birincil veritabanının kullanılabilirliğini etkilemeden en yüksek koruma düzeyini sağlar. Veriler, en az bir hazır bekleyen veritabanına işlenene kadar hiçbir zaman birincil veritabanı tarafından kabul edilmez. Birincil veritabanı yineleme değişikliklerini en az bir hazır bekleyen veritabanına yazamıyorum, kullanılamaz duruma gelmesi yerine en yüksek performans moduna geri döner. <br /><br />Bekleme sitesi kullanılamaz durumdaysa hizmetin devam etmesine izin verir. Yalnızca bir site çalışıyorsa, ikinci site çevrimiçi olana ve eşitleme yeniden kuruluncaya kadar verilerin yalnızca bir kopyası korunur. |
| **Maksimum koruma** | En yüksek kullanılabilirliğe benzer bir koruma düzeyi sağlar. Birincil veritabanı, yineleme değişikliklerini en az bir hazır bekleyen veritabanına yazamıyorum eklenen özellik ile birlikte kapanır. Bu, veri kaybı oluşmasını sağlar, ancak daha kırılabilirliğine daha fazla kullanılabilirlik harcamasını sağlar. |

>[!IMPORTANT]
>Sıfır kurtarma noktası hedefine (RPO) ihtiyacınız varsa, en yüksek kullanılabilirlik yapılandırmasını öneririz. Daha sonra RPO, birden çok başarısızlık oluştuğunda bile garanti edilebilir. Örneğin, birincil veritabanından bir ağ kesintisi olması durumunda bile, daha sonra ağ kesintisi devam ederken birincil veritabanının kaybedilmesi durumunda bile olur.

### <a name="data-guard-deployment-patterns"></a>Data Guard dağıtım desenleri

Oracle, yeniden oluşturma için birden çok hedef yapılandırmanıza olanak tanır ve birden çok bekleme veritabanına izin verir. En yaygın yapılandırma, farklı bir bölgedeki tek bir hazır bekleyen veritabanı olan aşağıdaki şekilde gösterilmiştir.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Oracle 'ın varsayılan Data Guard dağıtımını gösteren diyagram.":::

Data Guard varsayılan bir dağıtım için en yüksek performans modunda yapılandırılır. Bu yapılandırma, zaman uyumsuz yineleme taşıması aracılığıyla neredeyse gerçek zamanlı veri çoğaltma sağlar. Bekleme veritabanının bir RAC dağıtımının içinde çalıştırılması gerekmez, ancak birincil sitenin performans taleplerini karşıladığından önerilir.

Katı çalışma süresi veya sıfır RPO gerektiren ortamlar için aşağıdaki şekilde gösterildiği gibi bir dağıtım önerilir. En yüksek kullanılabilirlik yapılandırması, zaman uyumlu modda yeniden yineleme uygulayan yerel bir bekleme veritabanından ve uzak bölgede çalışan ikinci bir bekleme veritabanıyla oluşur.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Maksimum kullanılabilirlik Data Guard dağıtımını gösteren diyagram.":::

Veritabanı ve uygulama sunucularını ayrı bölgelerde çalıştırarak, uygulama performansı ne zaman, yerel bir bekleme veritabanı oluşturabilirsiniz. Bu yapılandırmada, birincil kümede planlı veya planlanmamış bakım gerektiğinde yerel bir bekleme veritabanı kullanılır. Bu veritabanlarını aynı bölgede olduklarından, aralarında veri kaybolmamasını sağlayarak zaman uyumlu çoğaltma ile çalıştırabilirsiniz.

### <a name="data-guard-configuration-considerations"></a>Data Guard yapılandırma konuları

Data Guard yapılandırmasını uygulamayı basitleştirir ve en iyi uygulamaların ' a uygunluğunu sağlar. Performans izleme işlevselliği sağlar ve geçiş, yük devretme ve yeniden örnekleme yordamlarını büyük ölçüde basitleştirir.

Data Guard, veritabanı kullanılabilirliğini tespit etmek için bir Data Guard yapılandırmasındaki tüm veritabanlarını izleyen bir gözlemci işlemi çalıştırmanızı sağlar. Birincil bir veritabanı başarısız olursa, Data Guard gözlemci otomatik olarak yapılandırmadaki bir bekleyen veritabanına yük devretmeyi başlatabilir. Data Guard gözlemcisini, fiziksel site sayısına (en fazla üç) göre birden çok gözlemcilerin ile uygulayabilirsiniz. 

Bu gözlemci, uygulama katmanını destekleyecek altyapıda bulunmalıdır. Birincil gözlemci, birincil veritabanının yer aldığı fiziksel sitede her zaman olmalıdır. Data Guard gözlemci tarafından tetiklenen yük devretme işlemlerini otomatikleştirmede dikkatli olmanız önerilir. İlk olarak, veritabanı ayrı bir konumda çalıştığında, kabul edilebilir hizmet sağlamak için uygulamalarınızın tasarlandığından ve test edilmiş olduğundan emin olun.

Uygulama yalnızca yerel olarak çalışabilebiliyor ise, ikincil siteye yük devretme el ile olmalıdır. Yüksek kullanılabilirlik düzeyleri (% 99,99 veya 99,999% çalışma süresi) gerektiren ortamlar, yukarıdaki şekilde gösterildiği gibi hem yerel hem de uzak bekleme veritabanını kullanmalıdır. Bu durumlarda, FastStartFailoverTarget parametresi yalnızca yerel bekleme veritabanına ayarlanır.

Siteler arası uygulama/veritabanı erişimini destekleyen tüm uygulamalarda, FastStartFailoverTarget, Data Guard yapılandırmasındaki tüm bekleme veritabanlarına ayarlanır.

### <a name="active-data-guard"></a>Etkin Data Guard

Oracle Active Data Guard (ADG), Oracle Database Enterprise Edition eklenen temel veri koruma özellikleri üst kümesidir. Oracle Sınavveri dağıtımı genelinde kullanılacak eklenen aşağıdaki özellikleri sağlar:

- Benzersiz bozulma algılama ve otomatik onarım.
- Eşitlenmiş üretim çoğaltmasına hızlı yük devretme – el ile veya otomatik.
- Üretim iş yükünü yük devretme eşitlenmiş bir bekleme konumuna boşaltma salt okunurdur.
- Veritabanı toplama yükseltmeleri ve bekleme. Fiziksel bekleme kullanılarak ilk düzeltme eki uygulama.
- Artımlı yedeklemeleri bekleme konumuna boşaltma.
- Performansı etkilemeden her türlü mesafede sıfır veri kaybı veri kurtarma koruması.

' De kullanılabilen Teknik İnceleme, [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) aşağıdaki şekilde gösterildiği gibi önceki özelliklere yönelik iyi bir genel bakış sağlar.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Oracle 'ın Active Data Guard özelliklerine genel bakış gösteren diyagram.":::

## <a name="backup-recommendations"></a>Yedekleme önerileri

Veritabanlarınızı yedeklediğinizden emin olun. Bir veritabanını aynı veya başka bir sisteme geri yüklemek ya da veritabanı dosyalarını kurtarmak için geri yükleme ve kurtarma özelliklerini kullanın.

Oracle Database gereç veritabanlarını veri kaybına karşı korumak için bir yedekleme kurtarma stratejisi oluşturulması önemlidir. Bu tür bir kayıp, veritabanını çalıştırmak için gerekli bir disk dosyasında okuma veya yazma hatasına neden olan bir disk ile fiziksel bir sorundan kaynaklanabilir. Kullanıcı hatası da veri kaybına neden olabilir. Yedekleme özelliği, **zaman içinde geri yükleme (ıNR) veritabanını, sistem değişiklik numarasını (SCN) kurtarmayı ve en son kurtarmayı işaret** etme yeteneği sağlar. Tarayıcı Kullanıcı arabiriminde veya komut satırı arabiriminden bir yedekleme ilkesi oluşturabilirsiniz.

Aşağıdaki yedekleme seçenekleri mevcuttur:

- NFS depolama birimine (hızlı kurtarma alanı-FRA-/U98) yedekleyin.
- Azure NetApp Files SnapCenter – Snapshot 'ı kullanma.

Göz önünde bulundurulması gereken işlem:

- El ile veya otomatik yedeklemeler.
- Otomatik yedeklemeler, NFS depolama birimlerine (örneğin,/U98) yazılır.
- Yedeklemeler, veritabanı sisteminin saat diliminde 12:00 ile – 6:00 arasında çalışır.
- Bekletme dönemleri Sun: 7, 15, 30, 45 ve 60 gün.

- Veritabanı, nesne depolamada depolanan bir yedekten kurtarılır:
  - En az olası veri kaybı ile bilinen son iyi duruma.
  - Belirtilen zaman damgası kullanılıyor.
  - Belirtilen SCN 'yi kullanma.
  - BackupReport: _BELIRTILEN SCN yerine yedekleme RAPORUNDAN SCN 'yi kullanır_.

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="FRA (/U98) ve FRA olmayan (/u95) müşteri veritabanı yedeklemesini gösteren diyagram.":::

### <a name="backup-policy"></a>Yedekleme ilkesi

Yedekleme ilkesi, yedekleme ayrıntılarını tanımlar. Bir yedekleme ilkesi oluşturduğunuzda, veritabanı yedeklemeleri FRA (NFS konumu) hedefini tanımlar ve kurtarma penceresini tanımlarsınız.

Varsayılan olarak, temel sıkıştırma algoritması kullanılır. Disk veya NFS yedekleme ilkesi için düşük, orta veya yüksek sıkıştırma algoritmaları kullanırken, lisans konuları vardır.

### <a name="backup-levels"></a>Yedekleme düzeyleri

Yedekleme alırken yedekleme düzeyini belirtin.

- Düzey 0-tam
- Düzey 1 – artımlı
- LongTerm/Archievelog-yedekleme bekletme ilkesi hariç, FRA olmayan konum kullanın (örneğin,/u95).

## <a name="next-steps"></a>Sonraki adımlar

Bir hata oluştuğunda Oracle veritabanınızı nasıl kurtaracağınızı öğrenin:

> [!div class="nextstepaction"]
> [Oracle veritabanınızı Azure BareMetal altyapısında kurtarma](oracle-high-availability-recovery.md)
