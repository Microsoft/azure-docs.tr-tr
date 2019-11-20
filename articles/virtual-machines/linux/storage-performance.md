---
title: Azure Lsv2-Series sanal makinelerinde performansı iyileştirme-depolama
description: Lsv2 serisi sanal makinelerde çözümünüz için performansı en uygun hale getirmeyi öğrenin.
services: virtual-machines-linux
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 8d99f63ae084b4f1dae3c0125420eaecf5655e2d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034765"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Lsv2 serisi sanal makinelerde performansı iyileştirin

Lsv2 serisi sanal makineler, çok sayıda uygulama ve sektör genelinde yerel depolamada yüksek g/ç ve aktarım hızı gerektiren çeşitli iş yüklerini destekler.  Lsv2-Series, Cassandra, MongoDB, Cloudera ve Redsıs gibi büyük veri, SQL, NoSQL veritabanları, veri ambarı ve büyük işlem veritabanları için idealdir.

Lsv2 serisi sanal makinelerin (VM 'Ler) tasarımı, işlemci, bellek, NVMe cihazları ve VM 'Ler arasında en iyi performansı sağlamak için AMD EPN™ 7551 işlemcisini en üst düzeye çıkarır. Linux 'ta iş ortaklarıyla çalışırken, çeşitli derlemeler Lsv2 serisi performans için iyileştirilmiş ve şu anda şunları içerir:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8,0
- Debian 9
- Debian 10

Bu makalede, iş yüklerinizin ve uygulamalarınızın VM 'Lerde tasarlanan maksimum performansı elde etmelerini sağlamak için ipuçları ve öneriler sağlanmaktadır. Bu sayfadaki bilgiler, Azure Market 'e daha fazla Lsv2 iyileştirilmiş görüntü eklendikçe sürekli olarak güncelleştirilir.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ yonga kümesi mimarisi

Lsv2 serisi VM 'Ler, Zen mikro mimarisine bağlı olarak AMD EYPC™ sunucu işlemcileri kullanır. , Şirket içi, paket içi ve çok düzeyli iletişimler için kullanılabilen, NUMA modeli için ölçeklendirilebilir bağlantı olarak™ EYPC için AMD tarafından geliştirilen sonsuz yapı (IF). Intel modern monoparçalı-zar işlemcilerde kullanılan QPı (hızlı yol bağlantısı) ve UPı (Ultra Path Interconnect) ile karşılaştırıldığında, AMD 'nin çok-NUMA küçük-zar mimarisi, her iki performans avantajlarının yanı sıra zorlukları de getirebilir. Bellek bant genişliği ve gecikme kısıtlamalarının gerçek etkisi, çalışan iş yüklerinin türüne bağlı olarak değişebilir.

## <a name="tips-to-maximize-performance"></a>Performansı en üst düzeye çıkarmaya yönelik ipuçları

* İş yükünüz için özel bir Linux tahmin TOS yüklüyorsanız, hızlandırılmış ağın varsayılan olarak **kapalı** olacağını unutmayın. Hızlandırılmış ağı etkinleştirmek istiyorsanız, en iyi performans için VM oluşturma sırasında etkinleştirin.

* Lsv2 serisi VM 'Leri destekleyen donanım, en fazla sekiz g/ç kuyruğu çifti (QP) s ile NVMe cihazları kullanır. Her NVMe cihaz g/ç kuyruğu aslında bir çifdir: gönderim kuyruğu ve tamamlanma kuyruğu. NVMe sürücüsü, g/ç 'yi hepsini bir kez deneme zamanlamasında dağıtarak bu sekiz g/ç QPs kullanımını iyileştirmek üzere ayarlanır. En yüksek performans kazanmak için cihaz başına sekiz işi eşleştirmek üzere çalıştırın.

* Etkin iş yükleri sırasında NVMe g/ç komutlarıyla NVMe yönetici komutlarını (örneğin, NVMe akıllı bilgi sorgusu, vb.) karıştırmaktan kaçının. Lsv2 NVMe cihazları, herhangi bir NVMe yönetici komutu beklendiğinde "yavaş mod" a geçiş yaparak Hyper-V NVMe Direct teknolojisi tarafından desteklenir. Lsv2 kullanıcılar, bu durumda NVMe g/ç performansında önemli bir performans bırakabilir.

* Lsv2 kullanıcılar, uygulamalarına yönelik NUMA benzeşimini belirlemek üzere veri sürücüleri için VM içinden bildirilen cihaz NUMA bilgilerine (tümü 0) güvenmemelidir. Daha iyi performans için önerilen yol, mümkünse iş yüklerini CPU 'larda yaymasıdır.

* Lsv2 VM NVMe cihazı için g/ç sıra çifti başına desteklenen en fazla sıra derinliği 1024 (vs. Amazon i3 QD 32 limit). Lsv2 kullanıcılar, (yapay) bir değerlendirme iş yüklerini sıra derinliği 1024 veya daha düşük olarak sınırlandırmalıdır ve bu da performansı düşürebilir.

## <a name="utilizing-local-nvme-storage"></a>Yerel NVMe Storage kullanma

Tüm Lsv2 VM 'lerinde 1,92 TB NVMe diskinde yerel depolama alanı kısa ömürlü. Sanal makinenin başarılı bir standart yeniden başlatması sırasında, yerel NVMe diskindeki veriler korunur. VM yeniden dağıtılırsa, serbest bırakılır veya silinirse veriler NVMe üzerinde kalıcı olmayacaktır. Başka bir sorun VM 'nin veya üzerinde çalıştığı donanımın sağlıksız hale gelmesi durumunda veriler kalıcı olmaz. Bu durumda, eski konaktaki tüm veriler güvenli bir şekilde silinir.

Ayrıca, VM 'nin bir planlı bakım işlemi sırasında farklı bir ana makineye taşınması gereken durumlar da olacaktır. Planlı bakım işlemleri ve bazı donanım arızaları [zamanlanan olaylar](scheduled-events.md)ile tahmin edilebilir. Zamanlanan Olaylar, tüm tahmin edilen bakım ve kurtarma işlemlerinde güncel kalmak için kullanılmalıdır.

Planlı bir bakım olayının VM 'nin boş yerel disklere sahip yeni bir konakta yeniden oluşturulmasını gerektirmesi durumunda, verilerin yeniden eşitlenmesi gerekir (eski konaktaki veriler güvenli bir şekilde silinmekte). Bu durum, Lsv2 serisi VM 'Lerin yerel NVMe diskinde Şu anda dinamik geçişi desteklemediği için oluşur.

Planlı bakım için iki mod vardır.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standart VM müşteri denetimli Bakımı

- VM, 30 günlük bir pencere sırasında güncelleştirilmiş bir konağa taşınır.
- Lsv2 yerel depolama verileri kaybolabilir. bu nedenle, olaydan önce verilerin yedeklenmesi önerilir.

### <a name="automatic-maintenance"></a>Otomatik bakım

- Müşteri, müşteri denetimli Bakımı yürütmez veya güvenlik sıfır günlük olay gibi acil yordamlar durumunda oluşur.
- Müşteri verilerini korumak için tasarlanmıştır, ancak VM donması veya yeniden başlatılması için küçük bir risk vardır.
- Lsv2 yerel depolama verileri kaybolabilir. bu nedenle, olaydan önce verilerin yedeklenmesi önerilir.

Yaklaşan hizmet olayları için, güncelleştirme için en uygun zamanı seçmek üzere denetimli bakım sürecini kullanın. Olaydan önce Premium depolamada verilerinizi yedekleyebilirsiniz. Bakım olayı tamamlandıktan sonra, verilerinizi yenilenen Lsv2 VM 'Ler yerel NVMe depolama alanına döndürebilirsiniz.

Yerel NVMe disklerinde verilerin bakımını yapan senaryolar şunlardır:

- VM çalışıyor ve sağlıklı.
- VM, yerinde (siz veya Azure tarafından) yeniden başlatılır.
- VM duraklatıldı (serbest ayırma olmadan durduruldu).
- Planlı bakım hizmeti işlemlerinin çoğunluğu.

Müşteriyi korumaya yönelik verileri güvenli bir şekilde silmeyen senaryolar şunlardır:

- VM yeniden dağıtıldı, durduruldu (serbest bırakıldı) veya silindi (sizin tarafınızdan).
- VM sağlıksız hale gelir ve bir donanım sorunu nedeniyle başka bir düğüme hizmet vermek zorunda olur.
- VM 'nin bakım için başka bir konağa yeniden ayrılmasını gerektiren planlı bakım hizmeti işlemlerinin az olması.

Yerel depolamada verileri yedekleme seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Azure IaaS diskleri Için Yedekleme ve olağanüstü durum kurtarma](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

* **Lsv2 serisi VM 'Leri dağıtmaya Nasıl yaparım? misiniz?**  
   Diğer tüm VM 'ler gibi, bir VM oluşturmak için [Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md)veya [PowerShell](quick-create-powershell.md) kullanın.

* **Tek bir NVMe disk hatası, konaktaki tüm VM 'Lerin başarısız olmasına neden olur mu?**  
   Donanım düğümünde bir disk hatası algılanırsa, donanım başarısız durumunda olur. Bu gerçekleştiğinde, düğümdeki tüm VM 'Ler otomatik olarak ayrılır ve sağlıklı bir düğüme taşınır. Lsv2 serisi VM 'Ler için bu, müşterinin hatalı düğümdeki verilerin da güvenli bir şekilde silineceği ve yeni düğümdeki müşteri tarafından yeniden oluşturulması gereken anlamına gelir. Belirtildiği gibi, dinamik geçiş Lsv2 ' de kullanılabilir hale gelmeden önce, hatalı düğümdeki veriler, başka bir düğüme aktarıldıklarından, bu VM 'Ler için uygun şekilde taşınabilir.

* **Performans için rq_affinity ayarlamalar yapmam gerekiyor mu?**  
   Rq_affinity ayar, saniye başına en fazla giriş/çıkış işlemi (ıOPS) kullanılırken küçük bir ayarlamadır. Diğer her şey iyi çalışır duruma getirildikten sonra, fark olup olmadığını görmek için rq_affinity 0 olarak ayarlamayı deneyin.

* **Blk_mq ayarlarını değiştirmem gerekiyor mu?**  
   RHEL/CentOS 7. x, NVMe cihazları için otomatik olarak blk-MQ kullanır. Yapılandırma değişikliği veya ayarları gerekli değildir. Scsi_mod. use_blk_mq ayarı yalnızca SCSI içindir ve NVMe cihazları Konuk VM 'lerde SCSI cihazları olarak göründüğünden, Lsv2 Preview sırasında kullanılmıştır. Şu anda NVMe cihazları NVMe cihazları olarak görülebilir, bu nedenle SCSI blk-MQ ayarı ilgisiz olur.

* **"Fio" öğesini değiştirmem gerekiyor mu?**  
   L64v2 ve L80v2 VM boyutlarında ' Fio ' gibi bir performans ölçme aracıyla maksimum ıOPS 'yi almak için, her bir NVMe cihazında "rq_affinity" öğesini 0 olarak ayarlayın.  Örneğin, bu komut satırı, bir L80v2 VM 'deki tüm 10 NVMe cihazları için "rq_affinity" değerini sıfıra ayarlar:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Ayrıca, g/ç, bölümleme olmadan, hiçbir dosya sistemi, RAID 0 yapılandırma vb. olmayan ham NVMe cihazlarının her birine doğrudan yapıldığında en iyi performansı elde edin. Bir test oturumuna başlamadan önce, NVMe cihazlarının her birinde `blkdiscard` çalıştırarak yapılandırmanın bilinen bir yeni/temiz durumda olduğundan emin olun.
   
## <a name="next-steps"></a>Sonraki adımlar

* Azure 'da [depolama performansı için en iyi duruma getirilmiş tüm sanal makinelerin](sizes-storage.md) belirtimlerine bakın
