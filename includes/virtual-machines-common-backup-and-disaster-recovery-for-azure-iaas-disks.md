---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9332079cd77c4dcc972059071165ba0631135b5c
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012521"
---
Bu makalede, Azure 'da IaaS sanal makineleri (VM) ve disklerinin yedekleme ve olağanüstü durum kurtarma (DR) için nasıl planlanacağı açıklanmaktadır. Bu belgede hem yönetilen hem de yönetilmeyen diskler ele alınmaktadır.

İlk olarak, Azure platformunda yerel hatalara karşı koruma sağlamaya yardımcı olan yerleşik hata toleransı yeteneklerini ele aldık. Daha sonra yerleşik özellikleri tam olarak ele almayan olağanüstü durum senaryolarını tartıştık. Farklı yedekleme ve DR önemli noktaları 'nın uygulayabileceği çeşitli iş yükü senaryolarına örnek de göstereceğiz. Daha sonra IaaS disklerinin DR için olası çözümleri gözden geçiririz.

## <a name="introduction"></a>Giriş

Azure platformu, müşterilerin yerelleştirilmiş donanım arızalarına karşı korunmasına yardımcı olmak için yedekleme ve hata toleransı için çeşitli yöntemler kullanır. Yerel hatalarda, bir sanal disk için verilerin bir kısmını veya o sunucudaki SSD veya HDD başarısızlıklarını depolayan bir Azure Storage Server makinesi ile ilgili sorunlar bulunabilir. Bu tür yalıtılmış donanım bileşeni hatalarının normal işlemler sırasında meydana gelebilirler.

Azure platformu bu hatalara karşı dayanıklı olacak şekilde tasarlanmıştır. Büyük felaketler hatalara veya çok sayıda depolama sunucusuna veya hatta tüm veri merkezine erişilememesine neden olabilir. Sanal makinelerinizin ve disklerinizin normal olarak yerelleştirilmiş hatalardan korunmasını sağlar, ancak iş yükünüzü büyük bir olağanüstü durum gibi çok zararlı hatalardan (VM ve disklerinizi etkileyebilecek) korumak için ek adımlar gereklidir.

Platform hataları olasılığa ek olarak, müşteri uygulaması veya verileriyle ilgili sorunlar meydana gelebilir. Örneğin, uygulamanızın yeni bir sürümü yanlışlıkla kesintiye neden olan verilerde değişiklik yapabilir. Bu durumda, uygulamayı ve verileri, bilinen son iyi durumu içeren önceki bir sürüme dönüştürmek isteyebilirsiniz. Bu, düzenli yedeklemelerin sürdürülmesi gerektirir.

Bölgesel olağanüstü durum kurtarma için IaaS VM disklerinizi farklı bir bölgeye yedeklemeniz gerekir.

Yedekleme ve DR seçeneklerine bakmadan önce, yerelleştirilmiş hataların işlenmesi için kullanılabilecek birkaç yöntemi yeniden inceleyelim.

### <a name="azure-iaas-resiliency"></a>Azure IaaS dayanıklılığı

*Dayanıklılık* , donanım bileşenlerinde oluşan normal hatalara yönelik toleransı ifade eder. Dayanıklılık, hatalardan kurtulmakta ve çalışmaya devam edebilmesidir. Bu, hatalardan kaçınma ve kapalı kalma süresi veya veri kaybını önleyen bir şekilde hatalara yanıt vermemeye yönelik değildir. Dayanıklılığın hedefi, bir hatanın ardından uygulamayı tam çalışır duruma geri döndürmektir. Azure sanal makineleri ve diskleri, yaygın donanım hatalarına karşı dayanıklı olacak şekilde tasarlanmıştır. Azure IaaS platformunun bu esnekliği nasıl sağladığını inceleyelim.

Bir sanal makine, genellikle iki bölümden oluşur: bir işlem sunucusu ve kalıcı diskler. Her ikisi de bir sanal makinenin hata toleransını etkiler.

SANAL makinenizin barındırıldığı Azure işlem Ana Bilgisayarı sunucusu, nadir bir donanım hatası yaşıyorsa, Azure VM 'yi başka bir sunucuda otomatik olarak geri yüklemek için tasarlanmıştır. Bu senaryo, bilgisayarınız yeniden başlatılır ve VM bir süre sonra geri gönderilir. Azure bu tür donanım başarısızlıklarını otomatik olarak algılar ve müşteri VM 'sinin mümkün olan en kısa sürede kullanılabilmesini sağlamaya yardımcı olmak için kurtarma işlemlerini yürütür.

IaaS disklerinde ilgili olarak, verilerin dayanıklılığı kalıcı bir depolama platformu için kritik öneme sahiptir. Azure müşterilerinin IaaS üzerinde çalışan önemli iş uygulamaları vardır ve verilerin kalıcılığına bağlıdır. Azure, bu IaaS diskleri için, yerel olarak depolanan verilerin üç yedekli kopyası ile korumayı tasarlar. Bu kopyalar yerel hatalara karşı yüksek dayanıklılık sağlar. Diskinizin bulunduğu donanım bileşenlerinden biri başarısız olursa, disk isteklerini desteklemek için iki ek kopya olduğundan VM 'niz etkilenmez. Bir diski destekleyen iki farklı donanım bileşeni aynı anda başarısız olsa bile sorunsuz bir şekilde gerçekleşir (nadir bir durumdur). 

Her zaman üç kopyayı korutığınızdan emin olmak için, üç kopyadan biri kullanılamaz hale gelirse Azure Storage verilerin arka planda yeni bir kopyasını otomatik olarak oluşturur. Bu nedenle, hata toleransı için Azure diskleriyle RAID kullanılması gerekli değildir. Basit bir RAID 0 yapılandırması, gerekirse daha büyük birimler oluşturmak için diskleri şeridi için yeterli olmalıdır.

Bu mimaride, Azure, IaaS diskleri için sürekli olarak kurumsal düzeyde dayanıklılık dağıtmıştır ve sektörde önde gelen sıfır yıllık bir [hata oranı](https://en.wikipedia.org/wiki/Annualized_failure_rate)vardır.

İşlem konağında veya depolama platformunda yerelleştirilmiş donanım hataları bazen, VM kullanılabilirliği için [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) kapsamındaki VM 'nin geçici kullanım dışı kalması ile sonuçlanabilir. Azure ayrıca Azure Premium SSD 'leri kullanan tek VM örnekleri için sektör lideri bir SLA sağlar.

Bir disk veya VM 'nin geçici olarak KULLANILAMAMASINDAN dolayı uygulama iş yüklerinin kapalı kalma süresini korumak için, müşteriler [kullanılabilirlik kümelerini](../articles/virtual-machines/windows/manage-availability.md)kullanabilir. Bir kullanılabilirlik kümesindeki iki veya daha fazla sanal makine uygulama için artıklık sağlar. Azure daha sonra bu VM 'Leri ve diskleri farklı güç, ağ ve sunucu bileşenleriyle ayrı hata etki alanlarında oluşturur.

Bu ayrı hata etki alanları nedeniyle, yerelleştirilmiş donanım arızaları genellikle aynı anda küme içinde birden çok VM 'yi etkilemez. Ayrı hata etki alanlarının olması, uygulamanız için yüksek kullanılabilirlik sağlar. Yüksek kullanılabilirlik gerektiğinde kullanılabilirlik kümelerini kullanmak iyi bir uygulama olarak kabul edilir. Sonraki bölümde olağanüstü durum kurtarma yönü ele alınmaktadır.

### <a name="backup-and-disaster-recovery"></a>Yedekleme ve olağanüstü durum kurtarma

Olağanüstü durum kurtarma, nadir, ancak büyük olayları kurtarma olanağıdır. Bu olaylar, tüm bölgeyi etkileyen hizmet kesintisi gibi geçici olmayan, geniş ölçekli arızalar içerir. Olağanüstü durum kurtarma, veri yedekleme ve arşivleme içerir ve bir veritabanını yedekten geri yükleme gibi el ile müdahale içerebilir.

Birincil bir olağanüstü durum büyük ölçekli kesintiler oluşmasına neden olursa, Azure platformunun yerelleştirilmiş hatalara karşı yerleşik koruması VM 'Leri/diskleri tamamen koruyamayabilir. Bu büyük ölçekli kesintiler, bir veri merkezinin acericane, deprem, Fire veya büyük ölçekli bir donanım birimi arızası olması gibi çok zararlı olaylar içerir. Ayrıca, uygulama veya veri sorunları nedeniyle hatalarla karşılaşabilirsiniz.

IaaS iş yüklerinizin kesintiden korunmasına yardımcı olmak için yedeklilik planlaması yapmanız ve kurtarmayı etkinleştirmek için yedeklemeler olması gerekir. Olağanüstü durum kurtarma için, birincil siteden farklı bir coğrafi konumda yedeklemeniz gerekir. Bu yaklaşım, yedeklemenin VM veya diskleri ilk olarak etkilediği aynı olaydan etkilenmemesini sağlamaya yardımcı olur. Daha fazla bilgi için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma](/azure/architecture/resiliency/disaster-recovery-azure-applications).

DR önemli noktalara aşağıdaki noktaları dahil edebilirsiniz:

- Yüksek kullanılabilirlik: uygulamanın, önemli kapalı kalma süresi olmadan sağlıklı durumda çalışmaya devam etmesine olanak tanır. *Sağlıklı durumda*, bu durum uygulamanın yanıt verdiğini ve kullanıcıların uygulamaya bağlanıp etkileşime girebileceği anlamına gelir. Bazı görev açısından kritik uygulamalar ve veritabanlarının, platformda oluşan sorunlar olduğunda bile her zaman kullanılabilir olması gerekebilir. Bu iş yükleri için, uygulamanın yedekliliği ve verileri planlamanız gerekebilir.

- Veri dayanıklılığı: bazı durumlarda, önemli bir durum oluşursa verilerin korunmasını sağlamaktır. Bu nedenle, verilerinizin farklı bir sitede yedeğinin olması gerekebilir. Bu tür iş yükleri için, uygulama için tam yedekliliğe gerek kalmaz, ancak disklerin yalnızca normal bir yedeklemesi de gerekebilir.

## <a name="backup-and-dr-scenarios"></a>Yedekleme ve DR senaryoları

Uygulama iş yükü senaryolarına ilişkin bazı tipik örneklere ve olağanüstü durum kurtarma planlaması konularına göz atalım.

### <a name="scenario-1-major-database-solutions"></a>Senaryo 1: ana veritabanı çözümleri

Yüksek kullanılabilirliği destekleyebilen SQL Server veya Oracle gibi bir üretim veritabanı sunucusunu göz önünde bulundurun. Kritik üretim uygulamaları ve kullanıcılar bu veritabanına bağımlıdır. Bu sistemin olağanüstü durum kurtarma planının aşağıdaki gereksinimleri desteklemesi gerekebilir:

- Verilerin korunması ve kurtarılabilir olması gerekir.
- Sunucu kullanıma hazır olmalıdır.

Olağanüstü durum kurtarma planı, farklı bir bölgedeki veritabanının bir çoğaltmasını yedekleme olarak sürdürmek isteyebilir. Sunucu kullanılabilirliği ve veri kurtarma gereksinimlerine bağlı olarak, çözüm etkin-etkin veya etkin-Pasif bir çoğaltma sitesinden verilerin düzenli aralıklarla çevrimdışı yedeklemelere göre değişebilir. SQL Server ve Oracle gibi ilişkisel veritabanları, çoğaltma için çeşitli seçenekler sağlar. SQL Server için, yüksek kullanılabilirlik için [SQL Server AlwaysOn kullanılabilirlik grupları](https://msdn.microsoft.com/library/hh510230.aspx) kullanın.

MongoDB gibi NoSQL veritabanları da yedeklilik için [çoğaltmaları](https://docs.mongodb.com/manual/replication/) destekler. Yüksek kullanılabilirlik için çoğaltmalar kullanılır.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Senaryo 2: yedekli VM 'Ler kümesi

Artıklık ve yük dengeleme sağlayan bir VM kümesi tarafından işlenen bir iş yükünü göz önünde bulundurun. Bir örnek, bir bölgede dağıtılan Cassandra kümesidir. Bu tür bir mimari zaten bu bölge içinde yüksek düzeyde artıklık sağlar. Ancak, iş yükünü bölgesel düzeyde bir hatadan korumak için, kümeyi iki bölgede yayılmasını veya başka bir bölgeye düzenli yedeklemeler yapmayı düşünmelisiniz.

### <a name="scenario-3-iaas-application-workload"></a>Senaryo 3: IaaS uygulaması iş yükü

IaaS uygulama iş yüküne göz atalım. Örneğin, bu uygulama bir Azure VM üzerinde çalışan tipik bir üretim iş yükü olabilir. Bir Web sunucusu veya bir sitenin içeriğini ve diğer kaynaklarını tutan dosya sunucusu olabilir. Ayrıca, VM disklerinde verileri, kaynakları ve uygulama durumunu depolanan bir VM üzerinde çalışan özel oluşturulmuş bir iş uygulaması da olabilir. Bu durumda, yedeklemeleri düzenli olarak aldığınızdan emin olmak önemlidir. Yedekleme sıklığı, VM iş yükünün yapısını temel almalıdır. Örneğin, uygulama her gün çalışır ve verileri değiştirirse, her saat yedeklemenin alınması gerekir.

Başka bir örnek, diğer kaynaklardan veri alıp toplanmış raporlar üreten bir raporlama sunucusudur. Bu VM veya disklerin kaybı, raporların kaybına neden olabilir. Ancak, raporlama işlemini yeniden çalıştırmak ve çıktıyı yeniden oluşturmak mümkün olabilir. Bu durumda, raporlama sunucusu bir olağanüstü durum ile isabet ediyor olsa bile gerçekten veri kaybı olmaz. Sonuç olarak, raporlama sunucusundaki verilerin bir kısmını kaybetmek için daha yüksek bir tolerans düzeyine sahip olabilirsiniz. Bu durumda, daha az sık yedeklemeler maliyetleri azaltmak için bir seçenektir.

### <a name="scenario-4-iaas-application-data-issues"></a>Senaryo 4: IaaS uygulama verileri sorunları

IaaS uygulama verileri sorunları başka bir olasılık. Fiyatlandırma bilgileri gibi kritik ticari verileri hesaplayan, tutan ve sunan bir uygulama düşünün. Uygulamanızın yeni bir sürümünde, fiyatlandırmayı yanlış bir şekilde hesaplamış ve platform tarafından sunulan mevcut ticari verileri bozmakta olan bir yazılım hatası vardı. Burada, en iyi eylem, uygulamanın ve verilerin önceki sürümüne dönmemek olur. Bunu etkinleştirmek için sisteminizin düzenli yedeklemelerini alın.

## <a name="disaster-recovery-solution-azure-backup"></a>Olağanüstü durum kurtarma çözümü: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) YEDEKLEMELER ve Dr için kullanılır ve yönetilmeyen disklerin yanı sıra [yönetilen disklerle](../articles/virtual-machines/windows/managed-disks-overview.md) de birlikte kullanılabilir. Zaman tabanlı yedeklemeler, kolay VM geri yükleme ve yedekleme bekletme ilkeleriyle bir yedekleme işi oluşturabilirsiniz.

[Premium SSD](../articles/virtual-machines/windows/disks-types.md)'leri, [yönetilen diskleri](../articles/virtual-machines/windows/managed-disks-overview.md)veya [yerel olarak yedekli depolama](../articles/storage/common/storage-redundancy-lrs.md) seçeneğiyle diğer disk TÜRLERINI kullanırsanız, düzenli olarak gerçekleştirilen Dr yedeklemeleri yapmak özellikle önemlidir. Azure Backup, verileri kurtarma hizmetleri kasasında uzun süreli saklama için depolar. Yedekleme kurtarma hizmetleri Kasası için [coğrafi olarak yedekli depolama](../articles/storage/common/storage-redundancy-grs.md) seçeneğini belirleyin. Bu seçenek, yedeklemelerin bölgesel felaketlerden korunması için farklı bir Azure bölgesine çoğaltılmasını sağlar.

Yönetilmeyen diskler için, IaaS diskleri için yerel olarak yedekli depolama türünü kullanabilir, ancak kurtarma hizmetleri Kasası için coğrafi olarak yedekli depolama seçeneğiyle Azure Backup etkinleştirildiğinden emin olabilirsiniz.

> [!NOTE]
> Yönetilmeyen diskleriniz için [coğrafi olarak yedekli depolama](../articles/storage/common/storage-redundancy-grs.md) veya [Okuma Erişimli Coğrafi olarak yedekli depolama](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) seçeneğini KULLANıRSANıZ, yedekleme ve Dr için yine de tutarlı anlık görüntüler gerekir. [Azure Backup](https://azure.microsoft.com/services/backup/) veya [tutarlı anlık görüntüleri](#alternative-solution-consistent-snapshots)kullanın.

 Aşağıdaki tablo, DR için kullanılabilen çözümlerin bir özetidir.

| Senaryo | Otomatik çoğaltma | DR çözümü |
| --- | --- | --- |
| Premium SSD diskler | Yerel ([yerel olarak yedekli depolama](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Yönetilen diskler | Yerel ([yerel olarak yedekli depolama](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Yönetilmeyen yerel olarak yedekli depolama diskleri | Yerel ([yerel olarak yedekli depolama](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Yönetilmeyen coğrafi olarak yedekli depolama diskleri | Çapraz bölge ([coğrafi olarak yedekli depolama](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Tutarlı anlık görüntüler](#alternative-solution-consistent-snapshots) |
| Yönetilmeyen Okuma Erişimli Coğrafi olarak yedekli depolama diskleri | Çapraz bölge ([Okuma Erişimli Coğrafi olarak yedekli depolama](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Tutarlı anlık görüntüler](#alternative-solution-consistent-snapshots) |

Yüksek kullanılabilirlik, Azure Backup birlikte bir kullanılabilirlik kümesinde yönetilen diskler kullanılarak en iyi şekilde karşılanır. Yönetilmeyen diskler kullanıyorsanız, DR için Azure Backup kullanmaya devam edebilirsiniz. Azure Backup kullandıysanız ve daha sonraki bir bölümde açıklandığı gibi [tutarlı anlık görüntüler](#alternative-solution-consistent-snapshots)almak, yedekleme ve Dr için alternatif bir çözümdür.

Uygulama veya altyapı seviyeleri için yüksek kullanılabilirlik, yedekleme ve DR seçimleriniz aşağıdaki gibi gösterilebilir:

| Düzey |   Yüksek kullanılabilirlik   | Yedekleme veya DR |
| --- | --- | --- |
| Uygulama | SQL Server AlwaysOn | Azure Backup |
| Altyapı    | Kullanılabilirlik kümesi  | Tutarlı anlık görüntülerle coğrafi olarak yedekli depolama |

### <a name="using-azure-backup"></a>Azure Backup kullanma 

[Azure Backup](../articles/backup/backup-azure-vms-introduction.md) , Windows veya Linux çalıştıran sanal makinelerinizi Azure kurtarma hizmetleri kasasına yedekleyebilir. İş açısından kritik verilerin yedeklenmesi ve geri yüklenmesi, verileri üreten uygulamalar çalışırken iş açısından kritik verilerin yedeklenmesi gereken bir olgusuna göre karmaşıktır. 

Bu sorunu gidermek için, Azure Backup Microsoft iş yükleri için uygulamayla tutarlı yedeklemeler sağlar. Verilerin depolamaya doğru yazıldığından emin olmak için birim gölge hizmetini kullanır. Linux sanal makinelerinde, Linux 'un Windows durumunda olduğu gibi birim gölge hizmeti ile eşdeğer olmadığından, varsayılan yedekleme tutarlılığı modu dosya bakımından tutarlı yedeklemededir. Linux makineleri için bkz. [Azure Linux VM 'Lerinin uygulamayla tutarlı yedeklemesi](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

![Azure Backup akışı][1]

Azure Backup zamanlanan saatte bir yedekleme işi başlattığında, sanal makinede yüklü olan yedekleme uzantısının bir zaman noktaya anlık görüntü almak için bu uzantıyı tetikler. Bir anlık görüntü, sanal makinedeki disklerin kapatılmasını gerektirmeden tutarlı bir anlık görüntüsünü almak için birim gölge hizmeti ile eşgüdümle birlikte getirilir. VM 'deki yedekleme uzantısı tüm disklerin tutarlı bir anlık görüntüsünü almadan önce tüm yazmaları temizler. Anlık görüntüyü aldıktan sonra veriler, yedekleme kasasına Azure Backup tarafından aktarılır. Yedekleme işlemini daha verimli hale getirmek için hizmet, son yedeklemeden sonra yalnızca değiştirilen veri bloklarını tanımlar ve aktarır.

Geri yüklemek için, kullanılabilir yedeklemeleri Azure Backup aracılığıyla görüntüleyebilir ve sonra geri yükleme başlatabilirsiniz. [Azure Portal](https://portal.azure.com/)aracılığıyla, [PowerShell kullanarak](../articles/backup/backup-azure-vms-automation.md)veya [Azure CLI](/cli/azure/)kullanarak Azure yedeklemeleri oluşturabilir ve geri yükleyebilirsiniz.

### <a name="steps-to-enable-a-backup"></a>Yedeklemeyi etkinleştirme adımları

[Azure Portal](https://portal.azure.com/)kullanarak sanal makinelerinizin yedeklerini etkinleştirmek için aşağıdaki adımları kullanın. Tam senaryonuza bağlı olarak bazı farklılıklar vardır. Tüm ayrıntılar için [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) belgelerine bakın. Azure Backup Ayrıca [yönetilen disklere sahip VM 'leri destekler](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Bir VM için kurtarma hizmetleri Kasası oluşturma:

    a. [Azure Portal](https://portal.azure.com/), **tüm kaynaklara** gözatıp **Kurtarma Hizmetleri kasalarını**bulun.

    b. **Kurtarma Hizmetleri kasaları** menüsünde, **Ekle** ' ye tıklayın ve VM ile aynı bölgede yeni bir kasa oluşturmak için adımları izleyin. Örneğin, VM 'niz Batı ABD bölgedeyse, kasa için Batı ABD seçin.

1.  Yeni oluşturulan kasa için depolama çoğaltmasını doğrulayın. **Kurtarma Hizmetleri kasaları** bölümünde kasaya erişin ve **Özellikler** > **yedekleme yapılandırması** > **Güncelleştir**' e gidin. Coğrafi olarak **yedekli depolama** seçeneğinin varsayılan olarak seçildiğinden emin olun. Bu seçenek, kasalarınızın otomatik olarak ikincil bir veri merkezine çoğaltılmasını sağlar. Örneğin, Batı ABD içinde kasanızın otomatik olarak Doğu ABD olarak çoğaltılır.

1.  Yedekleme ilkesini yapılandırın ve aynı kullanıcı arabiriminden VM 'yi seçin.

1.  Yedekleme aracısının VM 'de yüklü olduğundan emin olun. VM 'niz bir Azure Galeri görüntüsü kullanılarak oluşturulduysa, yedekleme Aracısı zaten yüklüdür. Aksi halde (özel bir görüntü kullanıyorsanız), [sanal MAKINEYE VM aracısını yüklemek](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent)için yönergeleri kullanın.

1.  VM 'nin yedekleme hizmetinin çalışması için ağ bağlantısına izin verdiğinden emin olun. [Ağ bağlantısı](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity)yönergelerini izleyin.

1.  Önceki adımlar tamamlandıktan sonra yedekleme, yedekleme ilkesinde belirtildiği şekilde düzenli aralıklarla çalışır. Gerekirse, ilk yedeklemeyi Azure portal kasa panosundan el ile tetikleyebilirsiniz.

Betikleri kullanarak Azure Backup otomatikleştirmek için [VM yedeklemesi Için PowerShell cmdlet 'leri](../articles/backup/backup-azure-vms-automation.md)bölümüne bakın.

### <a name="steps-for-recovery"></a>Kurtarma adımları

Bir VM 'yi onarmanız veya yeniden oluşturmanız gerekiyorsa, sanal makineyi kasadaki herhangi bir yedekleme kurtarma noktasından geri yükleyebilirsiniz. Kurtarmayı gerçekleştirmek için birkaç farklı seçenek vardır:

-   Yedeklenen sanal makinenizin bir zaman noktası gösterimi olarak yeni bir VM oluşturabilirsiniz.

-   Diskleri geri yükleyebilir ve ardından geri yüklenen VM 'yi özelleştirmek ve yeniden oluşturmak için sanal makine şablonunu kullanabilirsiniz.

Daha fazla bilgi için, [sanal makineleri geri yüklemek üzere Azure Portal kullanma](../articles/backup/backup-azure-arm-restore-vms.md)yönergelerine bakın. Bu belge, birincil veri merkezinde olağanüstü bir durum oluşursa, coğrafi olarak yedekli yedekleme kasasını kullanarak, yedeklenen VM 'Leri eşleştirilmiş bir veri merkezine geri yüklemeye yönelik belirli adımları açıklamaktadır. Bu durumda Azure Backup, geri yüklenen sanal makineyi oluşturmak için ikincil bölgeden Işlem hizmeti 'ni kullanır.

Ayrıca, [geri yüklenen disklerden yeni bir sanal makine oluşturmak](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)için PowerShell kullanabilirsiniz.

## <a name="alternative-solution-consistent-snapshots"></a>Alternatif çözüm: tutarlı anlık görüntüler

Azure Backup kullandıysanız, anlık görüntüler kullanarak kendi yedekleme mekanizmanızı uygulayabilirsiniz. Bir VM tarafından kullanılan tüm diskler için tutarlı anlık görüntüler oluşturma ve ardından bu anlık görüntüleri başka bir bölgeye çoğaltma karmaşıktır. Bu nedenle Azure, yedekleme hizmetini özel bir çözüm derlemeden daha iyi bir seçenek olarak kullanmayı kabul eder.

Diskler için Okuma Erişimli Coğrafi olarak yedekli depolama/coğrafi olarak yedekli depolama kullanırsanız, anlık görüntüler otomatik olarak ikincil bir veri merkezine çoğaltılır. Diskler için yerel olarak yedekli depolama kullanırsanız, verileri kendiniz çoğaltmanız gerekir. Daha fazla bilgi için bkz. [Artımlı anlık görüntülerle Azure YÖNETILMEYEN VM disklerini yedekleme](../articles/virtual-machines/windows/incremental-snapshots.md).

Anlık görüntü, zaman içinde belirli bir noktadaki bir nesnenin gösterimidir. Bir anlık görüntü, tuttuğu verilerin artımlı boyutu için faturalandırılır. Daha fazla bilgi için bkz. [BLOB anlık görüntüsü oluşturma](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>VM çalışırken anlık görüntü oluşturma

Herhangi bir zamanda anlık görüntü götürebilseniz de sanal makine çalışıyorsa disklere akışa alınan veriler de vardır. Anlık görüntüler uçuş durumunda olan kısmi işlemler içerebilir. Ayrıca, birden çok disk söz konusu olduğunda, farklı diskler için anlık görüntüler farklı zamanlarda oluşmuş olabilir. Bu senaryolar anlık görüntülerin koordine edilmemiş olmasına neden olabilir. Bu ortak ordinalıma olmaması, özellikle, yedekleme sırasında değişiklik yapılmamakta olan şeritli birimlerde, dosyaları bozulmuş olabilir.

Bu durumdan kaçınmak için, yedekleme işleminin aşağıdaki adımları uygulaması gerekir:

1.  Tüm diskleri dondurma.

1.  Bekleyen tüm yazmaları temizler.

1.  Tüm diskler için [bir blob anlık görüntüsü oluşturun](../articles/storage/blobs/storage-blob-snapshots.md) .

SQL Server gibi bazı Windows uygulamaları, uygulamayla tutarlı yedeklemeler oluşturmak için bir birim gölge hizmeti aracılığıyla eşgüdümlü bir yedekleme mekanizması sağlar. Linux 'ta, diskleri koordine etmek için *fsfreeze* gibi bir araç kullanabilirsiniz. Bu araç, uygulamayla tutarlı anlık görüntüler olmadan dosya ile tutarlı yedeklemeler sağlar. Bu işlem karmaşıksa, bu yordamı zaten uygulayan [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) veya üçüncü taraf bir yedekleme çözümünü kullanmayı düşünmelisiniz.

Önceki işlem, sanal makinenin belirli bir zaman noktası görünümünü temsil eden tüm VM diskleri için eşgüdümlü bir anlık görüntü koleksiyonu ile sonuçlanır. Bu, VM için bir yedekleme geri yükleme noktasıdır. Düzenli yedeklemeler oluşturmak için işlemi zamanlanan aralıklarda yineleyebilirsiniz. Anlık görüntüleri DR için başka bir bölgeye kopyalama adımları için bkz. [yedeklemeleri başka bir bölgeye kopyalama](#copy-the-snapshots-to-another-region) .

### <a name="create-snapshots-while-the-vm-is-offline"></a>VM çevrimdışıyken anlık görüntü oluşturma

Tutarlı yedeklemeler oluşturmaya yönelik başka bir seçenek de sanal makineyi kapatıp her diskin blob anlık görüntülerini alır. Blob anlık görüntülerinin alınması, çalışan bir sanal makinenin anlık görüntülerini koordine kullanmaktan daha kolaydır, ancak birkaç dakika kapalı kalma süresi gerektirir.

1. VM 'yi kapatın.

1. Yalnızca birkaç saniye süren her bir sanal sabit sürücü blobunun anlık görüntüsünü oluşturun.

    Anlık görüntü oluşturmak için, [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure depolama REST API](https://msdn.microsoft.com/library/azure/ee691971.aspx), [Azure CLI](/cli/azure/)veya [.NET için depolama istemci kitaplığı](https://msdn.microsoft.com/library/azure/hh488361.aspx)gibi Azure depolama istemci kitaplıklarından birini kullanabilirsiniz.

1. Sanal makineyi başlatın ve kapalı kalma süresini sonlandırır. Genellikle tüm işlem birkaç dakika içinde tamamlanır.

Bu işlem, sanal makine için bir yedekleme geri yükleme noktası sağlayan tüm diskler için tutarlı bir anlık görüntü koleksiyonu oluşturur.

### <a name="copy-the-snapshots-to-another-region"></a>Anlık görüntüleri başka bir bölgeye Kopyala

Anlık görüntülerin oluşturulması yalnızca DR için yeterli olmayabilir. Anlık görüntü yedeklemelerini başka bir bölgeye de çoğaltmanız gerekir.

Diskleriniz için coğrafi olarak yedekli depolama veya Okuma Erişimli Coğrafi olarak yedekli depolama kullanırsanız, anlık görüntüler otomatik olarak ikincil bölgeye çoğaltılır. Çoğaltmadan önce birkaç dakikalık gecikme olabilir. Birincil veri merkezi, anlık görüntülerin çoğaltılmasından önce kapanıyorsa, ikincil veri merkezindeki anlık görüntülere erişemezsiniz. Bunun olasılığı küçüktür.

> [!NOTE]
> Yalnızca coğrafi olarak yedekli depolama veya Okuma Erişimli Coğrafi olarak yedekli depolama hesabındaki disklere sahip olmak, VM 'yi felaketlerden korumaz. Ayrıca, koordine edilen anlık görüntüler oluşturmanız veya Azure Backup kullanmanız gerekir. Bu, bir VM 'yi tutarlı bir duruma kurtarmak için gereklidir.

Yerel olarak yedekli depolama kullanırsanız, anlık görüntüyü oluşturduktan hemen sonra anlık görüntüleri farklı bir depolama hesabına kopyalamanız gerekir. Kopyalama hedefi, farklı bir bölgedeki yerel olarak yedekli bir depolama hesabı olabilir ve bu, kopyanın uzak bir bölgeye yerleştirilmesiyle sonuçlanır. Ayrıca, anlık görüntüyü aynı bölgedeki bir Okuma Erişimli Coğrafi olarak yedekli depolama hesabına kopyalayabilirsiniz. Bu durumda, anlık görüntü geç uzak ikincil bölgeye çoğaltılır. Kopyalama ve çoğaltma işlemi tamamlandıktan sonra yedeklemeniz birincil sitede olağanüstü bir şekilde korunur.

Artımlı anlık görüntülerinizi DR için verimli bir şekilde kopyalamak için [Artımlı anlık görüntülerle Azure YÖNETILMEYEN VM disklerini yedekleme](../articles/virtual-machines/windows/incremental-snapshots.md)bölümündeki yönergeleri gözden geçirin.

![Artımlı anlık görüntülerle Azure yönetilmeyen VM disklerini yedekleme][2]

### <a name="recovery-from-snapshots"></a>Anlık görüntülerden kurtarma

Anlık görüntü almak için yeni bir blob oluşturmak üzere kopyalayın. Anlık görüntüyü birincil hesaptan kopyalıyorsanız, anlık görüntüyü anlık görüntünün temel blobuna kopyalayabilirsiniz. Bu işlem, diski anlık görüntüye geri döndürür. Bu işlem, anlık görüntüyü yükseltme olarak bilinir. Anlık görüntü yedeklemesini ikincil bir hesaptan kopyalıyorsanız, Okuma Erişimli Coğrafi olarak yedekli depolama hesabı söz konusu olduğunda, bir birincil hesaba kopyalamanız gerekir. [PowerShell kullanarak](../articles/storage/common/storage-powershell-guide-full.md) veya AZCopy yardımcı programını kullanarak bir anlık görüntüyü kopyalayabilirsiniz. Daha fazla bilgi için bkz. [AzCopy komut satırı yardımcı programıyla veri aktarma](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Birden çok diski olan VM 'Ler için, aynı Eşgüdümlü geri yükleme noktasının parçası olan tüm anlık görüntüleri kopyalamanız gerekir. Anlık görüntüleri yazılabilir VHD bloblarına kopyaladıktan sonra, sanal makine şablonunu kullanarak VM 'nizi yeniden oluşturmak için Blobları kullanabilirsiniz.

## <a name="other-options"></a>Diğer seçenekler

### <a name="sql-server"></a>SQL Server

Bir VM 'de çalışan SQL Server, SQL Server veritabanınızı Azure Blob depolama alanına veya bir dosya paylaşımında yedeklemek için kendi yerleşik özelliklerine sahiptir. Depolama hesabı coğrafi olarak yedekli depolama veya Okuma Erişimli Coğrafi olarak yedekli depolama ise, bu yedeklemelere, daha önce açıklanan kısıtlamalara sahip bir olağanüstü durum durumunda depolama hesabının ikincil veri merkezinde erişebilirsiniz. Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). [SQL Server AlwaysOn kullanılabilirlik grupları](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) , yedekleme ve geri yükleme özelliklerine ek olarak veritabanlarının ikincil çoğaltmalarını de koruyabilir. Bu özellik olağanüstü durum kurtarma süresini önemli ölçüde azaltır.

## <a name="other-considerations"></a>Diğer konular

Bu makalede, olağanüstü durum kurtarmayı desteklemek ve verilerinizi kurtarmak için bu yedeklemeleri veya anlık görüntüleri kullanmak üzere sanal makinelerinizin ve disklerinin anlık görüntülerini nasıl yedekleyeceğiniz veya bunların anlık görüntülerinin ele alındığı ele alınmıştır. Azure Resource Manager modeliyle birçok kişi, Azure 'daki sanal makinelerini ve diğer altyapıları oluşturmak için şablonlar kullanır. Her seferinde aynı yapılandırmaya sahip bir VM oluşturmak için bir şablon kullanabilirsiniz. VM 'lerinizi oluşturmak için özel görüntüler kullanıyorsanız, görüntülerinizi depolamak için bir Okuma Erişimli Coğrafi olarak yedekli depolama hesabı kullanarak görüntülerinizin korunduğundan da emin olmanız gerekir.

Sonuç olarak, Yedekleme işleminiz iki şeyin bir birleşimi olabilir:

- Verileri yedekleyin (diskler).
- Yapılandırmayı (Şablonlar ve özel görüntüler) yedekleyin.

Seçtiğiniz yedekleme seçeneğine bağlı olarak, hem verilerin hem de yapılandırmanın yedeklemesini işlemeniz gerekebilir ya da yedekleme hizmeti sizin için tüm bunları işleyebilir.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Ek: veri yedekliliinin etkilerini anlama

Azure 'daki depolama hesapları için olağanüstü durum kurtarma ile ilgili göz önünde bulundurmanız gereken üç tür veri artıklığı vardır: yerel olarak yedekli, coğrafi olarak yedekli veya Okuma Erişimli Coğrafi olarak yedekli. 

Yerel olarak yedekli depolama, aynı veri merkezinde verilerin üç kopyasını tutar. VM, verileri yazdığında, her üç kopya, çağrı yapana döndürülmeden önce güncelleştirilir ve bu sayede özdeş olduklarını bilirsiniz. Her üç kopyanın de aynı anda etkilenmesi olası olduğundan, diskiniz yerel hatalardan korunuyor. Yerel olarak yedekli depolama söz konusu olduğunda, coğrafi artıklık yoktur, bu nedenle disk, tüm veri merkezini veya depolama birimini etkileyebilecek çok zararlı hatalardan korunmaz.

Coğrafi olarak yedekli depolama ve Okuma Erişimli Coğrafi olarak yedekli depolama ile verilerinizin üç kopyası sizin tarafınızdan seçilen birincil bölgede tutulur. Verilerinizin üç kopyası, Azure tarafından ayarlanan karşılık gelen bir ikincil bölgede tutulur. Örneğin, verileri Batı ABD depolulırsa, veriler Doğu ABD çoğaltılır. Kopya bekletme zaman uyumsuz olarak gerçekleştirilir ve birincil ve ikincil sitelerdeki güncelleştirmeler arasında kısa bir gecikme vardır. İkincil sitedeki disklerin çoğaltmaları disk başına temelinde (gecikmeyle) tutarlıdır, ancak birden çok etkin diskin çoğaltmaları birbirleriyle eşitlenmiş olmayabilir. Birden çok diskte tutarlı çoğaltmalar olması için tutarlı anlık görüntüler gereklidir.

Coğrafi olarak yedekli depolama ve Okuma Erişimli Coğrafi olarak yedekli depolama arasındaki temel fark, Okuma Erişimli Coğrafi olarak yedekli depolama, ikincil kopyayı dilediğiniz zaman okuyabilirler. Birincil bölgedeki verileri erişilemez hale getiren bir sorun varsa, Azure ekibi, erişimi geri yükleme çabalarının her yerinde olur. Birincil devre dışı olsa da, Okuma Erişimli Coğrafi olarak yedekli depolama etkinse, ikincil veri merkezindeki verilere erişebilirsiniz. Bu nedenle, birincil erişilemeyen sürece çoğaltmadan okumayı planlıyorsanız, Okuma Erişimli Coğrafi olarak yedekli depolama göz önünde bulundurulmalıdır.

Önemli bir kesinti olması için Azure ekibi, coğrafi Yük devretme tetikleyip birincil DNS girdilerini ikincil depolamaya işaret etmek üzere değiştirebilir. Bu noktada, coğrafi olarak yedekli depolama veya Okuma Erişimli Coğrafi olarak yedekli depolama etkinse, ikincil olarak kullanılan bölgedeki verilere erişebilirsiniz. Diğer bir deyişle, depolama hesabınız coğrafi olarak yedekli depolama ise ve bir sorun varsa ikincil depolamaya yalnızca coğrafi Yük devretme varsa erişebilirsiniz.

Daha fazla bilgi için bkz. [Azure depolama kesintisi oluşursa Yapılacaklar](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>Microsoft, yük devretme yapılıp yapılmayacağını denetler. Yük devretme, depolama hesabı başına denetlenemez, bu nedenle bireysel müşteriler tarafından karar vermez. Belirli depolama hesapları veya sanal makine disklerinde olağanüstü durum kurtarma uygulamak için, bu makalede daha önce açıklanan teknikleri kullanmanız gerekir.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
