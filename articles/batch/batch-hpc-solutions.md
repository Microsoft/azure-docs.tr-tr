---
title: "Bulutta Batch ve HPC çözümleri - Azure | Microsoft Docs"
description: "Azure’de toplu işlem ve yüksek performanslı bilgi işlem (HPC ve Big Compute) senaryoları ve çözüm seçenekleri hakkında bilgi alın"
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0bab12648a31b416bd7ceca75f0f83f5da226bad
ms.lasthandoff: 03/31/2017


---
# <a name="batch-and-hpc-solutions-for-large-scale-computing-workloads"></a>Büyük ölçekli bilgi işlem iş yükleri için Batch ve HPC çözümleri

Azure toplu işlem ve *Big Compute* olarak da bilinen yüksek performanslı bilgi işlem (HPC) için verimli, ölçeklenebilir bulut çözümleri sunar. Burada, desteklemek için Big Compute iş yükleri ve Azure hizmetleri hakkında bilgi edinin veya doğrudan bu makalenin ilerisinde yer alan [çözüm senaryolarına](#scenarios) gidin. Temel olarak teknik karar verenler, BT yöneticileri ve bağımsız yazılım satıcıları için olan bu makaleyi başka BT profesyonelleri ve geliştiricileri de bu çözümlerle tanışmak amacıyla kullanabilir.

Kuruluşlarda büyük ölçekli bilgi işlem sorunları vardır: mühendislik tasarımı ve analizi, görüntü işleme, karmaşık modelleme, Monte Carlo benzetimleri, finansal risk hesaplamaları ve diğerleri. Azure gerekli kaynaklar, ölçek ve zamanlama ile ilgili sorunları çözmeleri için kuruluşlara yardımcı olur. Azure’le kuruluşların yapabildikleri:

* Şirket içi HPC kümesinden tepe iş yüklerini boşaltmaya, buradan da buluta uzanan karma çözümler oluşturma
* HPC küme araçlarını ve iş yüklerini tamamen Azure'da çalıştırma
* İşlem altyapısını dağıtmak ve yönetmek gerekmeden işlem yoğunluklu iş yüklerini çalıştırmak için [Batch](https://azure.microsoft.com/documentation/services/batch/) gibi yönetilen ve ölçeklenebilir Azure hizmetlerini çalıştırma

Bu makalenin kapsamı dışında olsa da, büyük ölçekli, özel Big Compute iş akışlarını derlemek için, Azure geliştiricilere ve ortaklara bir dizi özellik, mimari seçim ve geliştirme aracı da sağlar. Bunun yanı sıra, gittikçe büyüyen bir ortak ekosistemi Big Compute iş yüklerinizi Azure bulutta verimli bir hale getirmek için yardıma hazırdır.

## <a name="batch-and-hpc-applications"></a>Batch ve HPC uygulamaları
Web uygulamalarından ve birçok iş kolu uygulamalarından farklı olarak, toplu işlem ve HPC uygulamalarında tanımlı bir başlangıç ve bitiş vardır;zamanlamayla veya istek üzerine bazen saatlerce ve hatta daha uzun çalışabilirler. Çoğu iki ana kategoriye ayrılır: *doğası gereği paralel* (birden çok bilgisayarda veya işlemcide paralel çalışmak amacıyla kendilerini hazırlayarak sorunları çözdüklerinden bazen bunlara "utandırıcı derecede paralel" de denir) ve *sıkı şekilde bağlı*. Bu uygulama türleri hakkında daha fazla bilgi için aşağıdaki tabloya bakın. Bazı Azure çözüm yaklaşımları tek bir türle veya başka bir türle daha iyi çalışır.

> [!NOTE]
> Batch ve HPC çözümlerinde uygulamanın çalışan bir örneği olan ve yaygın adıyla *iş* ya da her iş *görevlere* ayrılır. Uygulamayla ilgili kümelenmiş işlem kaynakları da çoğunlukla *işlem düğümleri* olarak bilinir.
> 
> 

| Tür | Özellikler | Örnekler |
| --- | --- | --- |
| **Doğası gereği paralel**<br/><br/>![Doğası gereği paralel][parallel] |• Tek tek bilgisayarlar uygulama mantığını bağımsız çalıştırır<br/><br/> • Bilgisayar eklenmesi uygulamanın bilgi işlem süresini ölçeklendirir ve azaltır<br/><br/>• Uygulama ayrı yürütülebilir öğelerden oluşur veya istemcinin başlattığı hizmet grubuna bölünür (hizmet odaklı mimari veya SOA uygulaması) |• Finansal risk modelleme<br/><br/>• Görüntü işleme <br/><br/>• Medya kodlama ve kodlama dönüştürme<br/><br/>• Monte Carlo benzetimleri<br/><br/>• Yazılım testi |
| **Sıkı şekilde bağlı**<br/><br/>![Sıkı şekilde bağlı][coupled] |• Ara sonuçlarla etkileşim ve değişim için uygulamaya işlem düğümleri gerekir<br/><br/>• İşlem düğümleri, paralel bilgi işlem için yaygın bir iletişim protokolü olan İleti Geçirme Arabirimi (MPI) kullanılarak iletişime geçebilirler<br/><br/>• Uygulama ağ gecikme süresi ve bant genişliği için duyarlıdır<br/><br/>• Uygulama performansı, InfiniBand ve doğrudan uzak bellek erişimi (RDMA) gibi yüksek hızlı ağ teknolojileri kullanılarak geliştirilebilir. |• Petrol ve gaz rezervuarı modelleme<br/><br/>• Mühendislik tasarımı ve hesaplama sıvı dinamiği gibi analizi<br/><br/>• Araba kazaları ve nükleer tepkiler gibi fiziksel benzetimler<br/><br/>• Hava durumu tahmini |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Toplu işlem ve HPC uygulamalarını bulutta çalıştırmak için değerlendirmeler
Şirket içi HPC kümelerinde çalışmak üzere tasarlanmış birçok uygulamayı Azure’e ya da karma (şirket içi ve dışı karışık) ortama geçirmeye hazırdır. Ancak, bazı sınırlamalar veya değerlendirmeler olabilir; örneğin şunlar:

* **Bulut kaynaklarının kullanılabilirliği** - Kullandığınız bulut işlem kaynaklarının türüne bağlı olarak bir iş çalışırken sürekli makine kullanılabilirliğine bağlanamayabilirsiniz. Durum işleme ve ilerleme denetim noktalarını oluşturma olası geçici hataları işlemek için ortak tekniklerdir ve bulut kaynakları kullanılırken daha da gereklidir.
* **Veri erişimi** - NFS gibi kurumsal kümelerde yaygın olarak kullanılabilen veri erişimi teknikleri bulutta özel bir yapılandırma gerektirebilir. Veya bulut için farklı veri erişimi uygulamaları ve desenleri benimsemeniz gerekebilir.
* **Veri taşıma** - Büyük miktarda veri işleyen uygulamalar için stratejilerin verileri bulut depolamaya ve işlem kaynaklarına taşıması gerekir. [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) gibi yüksek hızda şirket içi ve dışı karışık ağa gerek duyabilirsiniz. Ayrıca, bu verileri depolama ve erişme hakkında yasal, düzenleme ve ilke kısıtlamalarını dikkate alın.
* **Lisans** - Bulutta çalışmayla ilgili lisans ya da başka kısıtlamalar için satıcıyla birlikte ticari uygulamaları denetleyin. Satıcıların tümü kullandıkça öde lisansı sunmaz. Çözümünüz için bulutta bir lisans sunucusu planlamanız gerekebilir, bu olmuyorsa şirket içi bir lisans sunucusuna bağlanın.

### <a name="big-compute-or-big-data"></a>Big Compute mü, yoksa Büyük Veri mi?
Big Compute ile Büyük Veri uygulamaları arasındaki çizgi her zaman net değildir ve bazı uygulamalar her ikisinde de aynı özelliklere sahip olabilirler. Her ikisi de, çoğunlukla da bilgisayar kümelerinde çalışan büyük ölçekli hesaplamalardan oluşur. Ancak çözüm yaklaşımları ve destek araçları farklı olabilir.

• **Big Compute**, CPU gücüne ve belleğe bağlı uygulamalardan oluşmaya yatkındır; örneğin, mühendislik benzetimleri, finansal risk modelleme ve dijital işleme. Big Compute çözümünün altyapısı kaba hesaplama gerçekleştirecek özelleştirilmiş çok çekirdekli işlemcilere sahip bilgisayarlar ve bilgisayarları bağlamak için özelleştirilmiş, yüksek hızda ağ donanımlarına sahip olabilir.

• **Büyük Veri**, tek bir bilgisayarla veya veritabanı yönetim sistemiyle yönetilemeyen yüksek miktarda verilerden oluşan veri analizi sorunlarını çözümler. büyük hacimli web günlükleri veya diğer iş zekası verileri bunun örneklerindendir. Büyük Veri, CPU gücünden daha fazla disk kapasitesi ve G/Ç performansı kullanma eğilimindedir. Ayrıca Apache Hadoop gibi küme yönetmeye ve verileri bölümlemeye yönelik özel Büyük Veri araçları vardır. (Azure HDInsight ve diğer Azure Hadoop çözümleri hakkında bilgi için bkz. [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## <a name="compute-management-and-job-scheduling"></a>İşlem yönetimi ve iş zamanlama
Kümelenmiş işlem kaynaklarının yönetilmesine ve bunları işleri çalıştıran uygulamalara ayırmasına yardımcı olmak için Batch ve HPC uygulamalarının çalıştırılmasında çoğunlukla bir *küme yöneticisi* ve bir *iş zamanlayıcısı* vardır. Bu işlevler ayrı araçlarla, tümleştirilmiş araçla veya hizmetle elde edilebilir.

* **Küme yöneticisi** - Sağlamalar, yayımlar ve yönetici işlem kaynakları (veya işlem düğümleri). Küme yöneticisi, işletim sistemi görüntülerinin ve uygulamaların işlem düğümlerine yüklenmesini otomatikleştirebilir, işlem kaynaklarını istek üzerine zamanlayabilir ve düğümlerin performansını izleyebilir.
* **İş zamanlayıcı** - Bir uygulamanın gereksinim duyduğu kaynakları (örneğin, işlemci veya bellek) ve çalışacağı koşulları belirtir. İş zamanlayıcı bir iş kuyruğu tutar ve atanan öncelik veya başka özelliklere göre kaynakları ayırır.

Windows tabanlı ve Linux tabanlı kümeler için kümeleme ve iş zamanlama araçları Azure’e de geçirilebilir. Örneğin, Windows ve Linux HPC iş yükleri için Microsoft'un ücretsiz işlem kümesi çözümü olan [Microsoft HPC Paketi](https://technet.microsoft.com/library/cc514029), Azure'da çalışma için çeşitli seçenekler sunmaktadır. Ayrıca Torque ve SLURM gibi açık kaynaklı araçlar çalıştırmak için Linux kümeleri oluşturabilirsiniz. Ek olarak [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/), [IBM Spectrum Symphony ve Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/) ve [Univa Grid Engine](http://www.univa.com/products/grid-engine) gibi ticari kılavuz çözümlerini Azure’a getirebilirsiniz.

Aşağıdaki bölümlerde gösterildiği gibi, geleneksel küme yönetim araçları olmadan (ya da buna ek olarak) işlem kaynaklarını ve zamanlama işlerini yönetmek için de Azure hizmetlerinden yararlanabilirsiniz.

## <a name="scenarios"></a>Senaryolar
Mevcut HPC küme çözümlerini, Azure hizmetlerini veya ikisinin birleşimini kullanarak Big Compute iş yüklerini çalıştırmaya yönelik üç yaygın senaryo aşağıda verilmiştir. Her senaryoyu seçmeyle ilgili önemli kararlar listelenmiş olsa da bunlar pek kapsamlı değildir. Çözümünüzde kullanabildiğiniz Azure hizmetleri hakkında daha fazla bilgi makalenin ilerideki bölümlerindedir.

| Senaryo | Neden seçiliyor? |
| --- | --- | --- |
| **HPC kümesinin Azure’de ortaya çıkması**<br/><br/>[![Cluster burst][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Daha fazla bilgi edinin:<br/>• [HPC Pack ile Azure çalışan örneklerine patlama](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [HPC Paketi ile karma işlem kümesi ayarlama](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [HPC Pack ile Azure Batch’e patlama](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/> |• [Microsoft HPC Paketini](https://technet.microsoft.com/library/cc514029) veya başka şirket içi kümesini karma bir çözümde ek Azure kaynaklarıyla birleştirin.<br/><br/>• Platformda çalışacak Big Compute iş yüklerinizi Hizmet (PaaS) sanal makine örnekleri (şu anda yalnızca Windows Server) olarak uzatın.<br/><br/>• İsteğe bağlı bir Azure sanal ağını kullanarak şirket içi lisans sunucusuna veya veri deposuna erişin |
| **HPC kümesini Azure’da tamamen oluşturma**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Daha fazla bilgi edinin:<br/>• [Azure’de HPC kümesi çözümleri](big-compute-resources.md)<br/><br/> |• Uygulamalarınızı ve küme araçlarınızı standart veya özel Windows ya da Linux hizmet olarak altyapı (IaaS) sanal makinelerine hızlı ve tutarlı bir şekilde dağıtın.<br/><br/>• Tercih ettiğiniz iş zamanlaması çözümünü kullanarak çeşitli Big Compute iş yüklerini çalıştırın.<br/><br/>• Tam bulut tabanlı çözümler oluşturmak için ağ ve depolama da dahil olmak üzere ek Azure Hizmetleri kullanın. |
| **Azure’de paralel uygulama ölçeğini genişletme**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Daha fazla bilgi edinin:<br/>• [Azure Batch temel bilgileri](batch-technical-overview.md)<br/><br/>• [.NET için Azure Batch kitaplığını kullanmaya başlama](batch-dotnet-get-started.md) |• Windows veya Linux sanal makinelerinin havuzlarında çalışacak çeşitli Big Compute iş yüklerinin ölçeğini genişletmek için [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) ile geliştirin.<br/><br/>• Sanal makinelerin dağıtımı ve otomatik ölçeklendirilmesi, iş zamanlaması, olağanüstü durum kurtarma, veri taşıma, bağımlılık yönetimi ve uygulama dağıtımını yönetmek için Azure platform hizmetini kullanın. |

## <a name="azure-services-for-big-compute"></a>Big Compute için Azure Hizmetleri
Big Compute çözümleri ve iş akışları için birleştirebildiğiniz hesaplama, veriler, ağ ve ilgili hizmetler hakkında daha fazla bilgi aşağıda verilmiştir. Azure hizmetleri hakkında ayrıntılı yönergeler için Azure hizmetleri [belgelerine](https://azure.microsoft.com/documentation/) bakın. Bu makalede yukarıda söz edilen [senaryolar](#scenarios) tam da bu hizmetleri kullanmanın bazı yollarını gösterir.

> [!NOTE]
> Azure düzenli olarak senaryonuz için yararlı olabilecek yeni hizmetler sunar. Sorularınız varsa, [Azure iş ortağı](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ile görüşün veya *bigcompute@microsoft.com* adresine e-posta gönderin.
> 
> 

### <a name="compute-services"></a>İşlem hizmetleri
Azure işlem hizmetleri Big Compute çözümünün çekirdeğidir ve farklı işlem hizmetleri farklı senaryolar için avantaj sunarlar. Temel düzeyde, bu hizmetler, Azure’ün Windows Server Hyper-V teknolojisini kullanarak sağladığı sanal makine tabanlı işlem örneklerini çalıştırmak için uygulamaların farklı modlarını sağlar. Bu örnekler standart ve özel Linux ve Windows işletim sistemlerinin ve araçlarını çalıştırabilir. Azure, CPU çekirdekleri, bellek, disk kapasitesi ve diğer özelliklerin farklı yapılandırmalarıyla size [örnek boyutları](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) seçimi sağlar. Gereksinimlerinize bağlı olarak örnekleri binlerce çekirdeğe ölçeklendirip, daha az kaynak gerektiğinde de ölçeklendirmeyi azaltın.

> [!NOTE]
> HPC iş yüklerinin performansını ve ölçeklenebilirliğini geliştirmek için [H serisi gibi yoğun işlem gücü kullanımlı Azure örneklerinden](../virtual-machines/windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) yararlanın. Bu örnekler, düşük gecikme ve yüksek üretimli bir uygulama ağı gerektiren paralel MPI uygulamalarını da destekler. Ayrıca, Azure’daki işlem ve görselleştirme senaryolarını genişletmek için NVIDIA GPU içeren [N serisi](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/) VM’ler de kullanılabilir.  
> 
> 

| Hizmet | Açıklama |
| --- | --- |
| **[Sanal makineler](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Microsoft Hyper-V teknolojisi kullanarak işlem hizmet olarak altyapısı (IaaS) sağlarlar<br/><br/>• [Azure Market](https://azure.microsoft.com/marketplace/)’teki Standart Windows Server veya Linux görüntülerinden ya da kendinizin sağlanan görüntülerden ve veri disklerinden kalıcı bulut bilgisayarlarını esnek bir biçimde hazırlamanızı ve yönetmenizi sağlarlar<br/><br/>• Kapasiteyi otomatik olarak artıracak veya azaltacak otomatik ölçeklendirmeyle, aynı sanal makinelerden büyük ölçekli hizmetler derlemek için [VM Ölçek Kümeleri](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) olarak dağıtılabilir ve yönetilebilirler<br/><br/>• Bulutun tamamında şirket içi işlem kümesi araçlarını ve uygulamalarını çalıştırırlar<br/><br/> |
| **[Bulut hizmetleri](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Çalışan rolü örneklerinde, Windows Server’ın bir sürümünü çalıştıran ve tamamen Azure tarafından yönetilen sanal makineler olan Big Compute uygulamalarını çalıştırabilirler<br/><br/>• Düşük yönetici desteğiyle hizmet olarak platform (PaaS) modelinde çalışan ölçeklenebilir, güvenli uygulamaları etkinleştirirler<br/><br/>• Şirket içi HPC kümesi çözümleriyle tümleştirmek için ek araçlar ve geliştirme gerekebilir. |
| **[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Tam olarak yönetilen bir hizmette büyük ölçekli paralel ve toplu işlem iş yüklerini çalıştırır<br/><br/>• Sanal makinelerin iş zamanlamasını ve yönetilen havuzun otomatik ölçeklendirilmesini sağlar<br/><br/>• Geliştiricilerin mevcut uygulamaları hizmet olarak ya da bulut etkin olarak derlemelerin ve çalıştırmalarını sağlar<br/> |

### <a name="storage-services"></a>Storage hizmetleri
Big Compute çözümü tipik olarak bir dizi girdi verisi üzerinde çalışır ve sonuçları için veri üretir. Big Compute çözümlerinde kullanılan bazı Azure depolama hizmetleri:

* [Blob, tablo ve kuyruk depolama](https://azure.microsoft.com/documentation/services/storage/) - Büyük miktarda yapılandırılmamış verileri, SQL dışı verileri, iş akışı ve iletişim iletilerini bu sırayla yönetin. Örneğin, büyük teknik veri kümelerinde blob depolamayı veya uygulamanızın işlediği girdi görüntülerini ya da medya dosyalarını kullanabilirsiniz. Çözümdeki uyumsuz iletişim için kuyrukları kullanabilirsiniz. Bkz. [Microsoft Azure Storage’a Giriş](../storage/storage-introduction.md).
* [Azure File storage](https://azure.microsoft.com/services/storage/files/) - Bazı HPC küme çözümleri için gerekli olan standart SMB protokolü kullanarak Azure’de ortak dosyaları ve verileri paylaşır.
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) - Bulut için üst ölçekte bir Apache Hadoop Dağıtılmış Dosya Sistemi sağlar; toplu işlem, gerçek zamanlı ve etkileşimli analizler için yararlıdır.

### <a name="data-and-analysis-services"></a>Veri ve analiz hizmetleri
Bazı Big Compute senaryoları büyük ölçekli veri akışlarından oluşur ya da daha fazla işleme veya analiz gereken veriler üretir. Azure aşağıdakiler dahil birkaç veri ve analiz hizmeti sunar:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - Şirket içi, bulut tabanlı ve İnternet veri depolarına ait verilerle birleşen, yığılan ve dönüştüren veri temelli iş akışlarını (işlem hattı) derler.
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) - Yönetilen bir hizmette Microsoft SQL Server ilgili veritabanı yönetim sisteminin önemli özelliklerini sağlar.
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - Büyük verileri yönetmek, analiz etmek ve raporlamak için Windows Server veya Linux tabanlı Apache Hadoop kümelerini dağıtır ve hazırlar.
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - Tam yönetilen bir hizmette tahmine dayalı analitik çözümleri bulutta oluşturmanıza, test etmenize, çalıştırmanıza ve yönetmenize yardımcı olur.

### <a name="additional-services"></a>Ek hizmetler
Big Compute çözümünüze, şirket içi veya başka ortamlardaki kaynaklara bağlanmak için başka Azure hizmetleri gerekebilir. Örneklere şunlar dahildir:

* [Sanal Ağ](https://azure.microsoft.com/documentation/services/virtual-network/) - Azure’da Azure kaynaklarını birbirine veya şirket içi veri merkezinize bağlamak için mantıksal olarak yalıtılmış bir bölüm oluşturur. Şirket içi ve dışı karışık bir sanal ağ ile Big Compute uygulamaları şirket içi verilere, Active Directory hizmetlerine ve lisans sunucularına erişebilir
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - Microsoft veri merkezleri ile şirketinizde veya bir birlikte bulundurma ortamında bulunan altyapı arasında özel bir bağlantı oluşturur. ExpressRoute İnternet üzerinden kurulan tipik bağlantılardan daha fazla güvenilirlik, daha yüksek hızlar ve daha düşük gecikme sağlar.
* [Hizmet Veri Yolu](https://azure.microsoft.com/documentation/services/service-bus/) - İster Azure’de, ister başka bir bulut platformunda, isterse de bir veri merkezinde olsun, uygulamaların iletişim kurması ve veri değişimi yapması için birçok mekanizma sağlar.

## <a name="next-steps"></a>Sonraki adımlar
* Çözümünüzü derlemek için teknik kılavuz arıyorsanız bkz. [Batch ve HPC için Teknik Kaynaklar](big-compute-resources.md).
* Azure seçeneklerinizi Cycle Computing, Rescale ve UberCloud dahil olmak üzere iş ortaklarınızla tartışın.
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) ve [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088) tarafından sağlanan Azure Big Compute çözümleri hakkında okuyun.
* Son duyurular için bkz. [Microsoft HPC ve Batch ekip blogu](http://blogs.technet.com/b/windowshpc/) ve [Azure blogu](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

