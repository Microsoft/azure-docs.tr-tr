---
title: Ana bilgisayar işlem, Azure sanal makinelerine taşıma
description: IBM Z14 uygulamalarını geçirebilmeniz için Azure işlem kaynakları favorably ile ana bilgisayar kapasitesini karşılaştırın.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 84682514d83de9d6a613581d68301efe33cd44ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954403"
---
# <a name="move-mainframe-compute-to-azure"></a>Anabilgisayar işlem hareketini Azure 'a taşıma

Ana bilgisayarlar yüksek güvenilirlik ve kullanılabilirlik için bir saygınlığa sahiptir ve birçok kurumun güvenilir omurgası olmaya devam eder. Genellikle, neredeyse sınırsız ölçeklenebilirlik ve bilgi işlem gücü de vardır. Ancak bazı kuruluşlar, kullanılabilir en büyük ana bilgisayarları yeteneklerini artmıştır. Bu şekilde bu şekilde karşılaşırsanız Azure, çeviklik, erişim ve altyapı tasarrufları sunar.

Microsoft Azure ana bilgisayar iş yüklerini çalıştırmak için, ana bilgisayarınızın işlem yeteneklerinin Azure ile nasıl karşılaştırılacağını bilmeniz gerekir. Bir IBM Z14 ana bilgisayarı (Bu yazarken en güncel model) temelinde, bu makalede Azure 'da nasıl karşılaştırılabilir sonuçlar elde edersiniz.

Başlamak için ortamları yan yana değerlendirin. Aşağıdaki şekilde, bir ana bilgisayar ortamı, uygulamaları çalıştırmak için bir Azure barındırma ortamına göre karşılaştırılmaktadır.

![Azure hizmetleri ve öykünme ortamları, karşılaştırılabilir destek sunar ve geçiş kolaylaştırır](media/mainframe-compute-azure.png)

Ana bilgisayarlar 'ın gücü genellikle binlerce kullanıcı için milyonlarca güncelleştirmeyi işleyen çevrimiçi işlem işleme (OLTP) sistemleri için kullanılır. Bu uygulamalar genellikle işlem işleme, ekran işleme ve form girişi için yazılım kullanır. Bir müşteri bilgileri denetim sistemi (CICS), bilgi yönetimi sistemi (ıMS) veya Işlem arabirimi paketi (tıp) kullanabilirler.

Şekilde gösterildiği gibi, Azure 'da bir TPM öykünücüsü CICS ve ıMS iş yüklerini işleyebilir. Azure üzerinde toplu bir sistem öykünücüsü, Iş denetimi dili (JCL) rolünü gerçekleştirir. Ana bilgisayar verileri Azure SQL veritabanı gibi Azure veritabanlarına geçirilir. Azure hizmetleri veya Azure sanal makinelerinde barındırılan diğer yazılımlar sistem yönetimi için kullanılabilir.

## <a name="mainframe-compute-at-a-glance"></a>Tek bakışta anabilgisayar işlem

Z14 ana bilgisayarı, işlemciler en fazla dört *çekmece* halinde düzenlenir. *Çekmece* yalnızca bir işlemci ve yonga kümesi kümesidir. Her çekmecede altı etkin Merkezi işlemci (CP) yongaları olabilir ve her bir CP 10 sistem denetleyicisi (SC) yongasıdır. Intel x86 terminolojisinde, çekmece başına altı yuva, yuva başına 10 çekirdek ve dört çekmece vardır. Bu mimari, Z14 için en fazla 24 yuva ve 240 çekirdek, en yüksek değer sağlar.

Fast Z14 CP 5,2 GHz saat hızına sahiptir. Genellikle, bir Z14, kutudaki tüm CPs ile birlikte dağıtılır. Gerektiğinde etkinleştirilirler. Müşteri, gerçek kullanıma rağmen ayda en az dört saatlik işlem süresi için ücretlendirilir.

Ana bilgisayar işlemcisi aşağıdaki türlerden biri olarak yapılandırılabilir:

- Genel Amaçlı (GP) işlemcisi
- Sistem z tümleşik bilgi Işlemcisi (Ziıp)
- Linux (ıTıL) işlemcisi için tümleşik tesis
- Sistem yardımı Işlemcisi (SAP)
- Tümleşik Kuptesis (ICF) işlemcisi

## <a name="scaling-mainframe-compute-up-and-out"></a>Ana bilgisayar işlem ölçeğini genişletme

IBM ana bilgisayarları 240 çekirdeğe kadar ölçeklendirme (tek bir sistem için geçerli Z14 boyutu) olanağı sunar. Ayrıca IBM ana bilgisayarları, Kupı (CF) adlı bir özellik ile ölçeklenebilir. CF birden çok anabilgisayar sisteminin aynı anda aynı verilere erişmesine izin verir. Ana bilgisayar Paralel Sysplex teknolojisi, CF kullanarak ana bilgisayar işlemcilerini kümeler halinde gruplandırır. Bu kılavuz yazıldığında, Paralel Sysplex özelliği, her biri 64 işlemcili 32 gruplandırmaları destekler. İşlem kapasitesini ölçeklendirmek için, en fazla 2.048 işlemci bu şekilde gruplandırılabilir.

Bir CF, işlem kümelerinin doğrudan erişimli veri paylaşmasına izin verir. Bilgi, önbellek bilgileri ve paylaşılan veri kaynakları listesini kilitlemek için kullanılır. Bir veya daha fazla CFs kullanan Paralel Sysplex, "paylaşılan her şey" genişleme işlem kümesi olarak düşünülebilir. Bu özellikler hakkında daha fazla bilgi için IBM Web sitesindeki [IBM Z üzerinde Parallel sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) konusuna bakın.

Uygulamalar, bu özellikleri hem genişleme performansı hem de yüksek kullanılabilirlik sağlamak için kullanabilir. CICS with CF ile Parallel sysplex kullanma hakkında daha fazla bilgi için, [IBM CICS ve Kupı hizmetini indirin: temel bilgiler](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) .

## <a name="azure-compute-at-a-glance"></a>Tek bakışta Azure işlem

Bazı kullanıcılar, Intel tabanlı sunucuların ana bilgisayarlar kadar güçlü olduğunu yanlışlıkla düşündüler. Ancak, yeni çekirdek yoğun, Intel tabanlı sistemlerin, ana bilgisayar ile çok fazla işlem kapasitesi vardır. Bu bölümde, bilgi işlem ve depolama için Azure hizmet olarak altyapı (IaaS) seçenekleri açıklanmaktadır. Azure hizmet olarak platform (PaaS) seçenekleri de sunar, ancak bu makale, benzer ana bilgisayar kapasitesini sağlayan IaaS seçeneklerine odaklanır.

Azure sanal makineleri, bir dizi boyut ve türde işlem gücü sağlar. Azure 'da bir sanal CPU (vCPU) kabaca ana bilgisayar üzerindeki bir çekirdeğe karşılık gelir.

Şu anda Azure sanal makine boyutlarının aralığı, 1 ile 128 vCPU arasında yer almaktadır. Sanal makine (VM) türleri, belirli iş yükleri için iyileştirilmiştir. Örneğin, aşağıdaki listede sanal makine türleri (Bu yazma itibariyle geçerli) ve önerilen kullanımları gösterilmektedir:

| Boyut     | Tür ve açıklama                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D serisi | 64 vCPU ile genel amaçlı ve en fazla 3,5 GHz saat hızı                           |
| E Serisi | 64 vCPU ile iyileştirilmiş bellek                                                 |
| F serisi | 64 vCPU ve 3.7 GHz saat hızında en iyi duruma getirilmiş işlem                       |
| H serisi | Yüksek performanslı bilgi işlem (HPC) uygulamaları için iyileştirildi                          |
| L serisi | NoSQL gibi veritabanları tarafından desteklenen yüksek performanslı uygulamalar için en iyi duruma getirilmiş depolama |
| A serisi | 128 vCPU 'ya kadar en büyük işlem ve bellek için iyileştirilmiş VM 'Ler                        |

Kullanılabilir VM 'Ler hakkında daha fazla bilgi için bkz. [sanal makine serisi](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Bir Z14 ana bilgisayar en fazla 240 çekirdeğe sahip olabilir. Ancak, Z14 ana bilgisayarları neredeyse her türlü çekirdeği tek bir uygulama veya iş yükü için kullanmaz. Bunun yerine, bir ana bilgisayar iş yüklerini mantıksal bölümlere (LPARs) ayırır ve LPARs derecelendirmelere sahiptir: MIPS (saniyede milyonlarca yönerge) veya MSU (milyon hizmet birimi). Azure 'da bir anabilgisayar iş yükünü çalıştırmak için gereken sanal makine boyutu belirlenirken, MIPS (veya MSU) derecelendirmesinde etken yapın.

Genel tahminler aşağıda verilmiştir:

-   vCPU başına 150 MIPS

-   işlemci başına 1.000 MIPS

Bir LNOMINAL içindeki belirli bir iş yükü için doğru VM boyutunu anlamak için, önce iş yükünün sanal makinesini iyileştirin. Ardından gereken vCPU sayısını saptayın. Bir koruyucu tahmin, vCPU başına 150 MIPS 'dir. Bu tahmine göre, örneğin, 16 vCPU içeren bir F serisi VM, 2.400 MIPS ile bir LNOMINAL 'ten gelen bir IBM DB2 iş yükünü kolayca destekleyebilir.

## <a name="azure-compute-scale-up"></a>Azure işlem ölçeği artırma

D serisi VM 'Ler 128 vCPU kadar ölçeklendirebilir (Bu makalenin yazıldığı sırada). VCPU başına 150 MIPS 'nin klasik tahminini kullanarak, d serisi VM yaklaşık 19.000 MIPS öğesine karşılık gelir. Bir ana bilgisayar için MIPS 'yi tahmin etmek için genel kural, işlemci başına 1.000 MIPS ' dir. Bir Z14 ana bilgisayar, en fazla 24 işlemciye sahip olabilir ve tek bir anabilgisayar sistemi için yaklaşık 24.000 MIPS sağlayabilir.

En büyük tek Z14 ana bilgisayar, Azure 'da kullanılabilir olan en büyük VM 'den yaklaşık 5.000 MIPS 'ye sahiptir. Ancak, iş yüklerinin nasıl dağıtıldığını karşılaştırmak önemlidir. Bir anabilgisayar sisteminde hem bir uygulama hem de ilişkisel veritabanı varsa, bunlar genellikle her biri kendi LPAR olan fiziksel ana bilgisayara dağıtılır. Azure 'daki aynı çözüm, genellikle uygulama için bir VM ve veritabanı için ayrı, uygun şekilde boyutlu bir VM kullanılarak dağıtılır.

Örneğin, bir M64 vCPU sistemi uygulamayı destekliyorsa ve veritabanı için bir M96 vCPU kullanılıyorsa, aşağıdaki şekilde gösterildiği gibi yaklaşık 150 vCPU gerekir — veya 24.000 MIPS hakkında.

![24.000 MIPS iş yükü dağıtımlarını karşılaştırma](media/mainframe-compute-mips.png)

Bu yaklaşım, LPARs 'Leri ayrı VM 'lere geçirkullanmaktır. Daha sonra Azure, tek bir anabilgisayar sisteminde dağıtılan çoğu uygulama için gereken boyuta kadar kolayca ölçeklendiriyor.

## <a name="azure-compute-scale-out"></a>Azure işlem ölçeğini genişletme

Azure tabanlı bir çözümün avantajlarından biri, ölçeği genişletme olanağıdır. Ölçeklendirme, bir uygulama için neredeyse sınırsız işlem kapasitesi sağlar. Azure, işlem gücünü genişletmek için birden çok yöntemi destekler:

- **Bir küme genelinde yük dengeleme.** Bu senaryoda, bir uygulama bir kümedeki birden çok VM arasında iş yükünü yaymak için bir [yük dengeleyici](../../../../load-balancer/load-balancer-overview.md) veya Resource Manager kullanabilir. Daha fazla işlem kapasitesi gerekiyorsa, kümeye ek VM 'Ler eklenir.

- **Sanal Makine Ölçek Kümeleri.** Bu patlama senaryosunda, bir uygulama VM kullanımına göre ek [işlem kaynaklarına](../../../../virtual-machine-scale-sets/overview.md) ölçeklendirebilir. Talep geldiğinde, bir ölçek kümesindeki sanal makinelerin sayısı da, işlem gücünün verimli bir şekilde kullanılması için de kullanılabilir.

- **PaaS Ölçeklendirmesi.** Azure PaaS teklifleri, işlem kaynaklarını ölçeklendirir. Örneğin, [Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) , işlem kaynaklarını, istek hacminde artımlardan buluşmak üzere ayırır.

- **Kubernetes kümeleri.** Azure 'daki uygulamalar, belirtilen kaynaklar için işlem hizmetleri için [Kubernetes kümelerini](../../../../aks/concepts-clusters-workloads.md) kullanabilir. Azure Kubernetes hizmeti (AKS), Azure 'da Kubernetes düğümlerini, havuzlarını ve kümelerini düzenleyen bir yönetilen hizmettir.

İşlem kaynaklarını ölçeklendirmeye yönelik doğru yöntemi seçmek için, Azure ve ana bilgisayarlar 'ın nasıl farklı olduğunu anlamak önemlidir. Bu anahtar, verileri işlem kaynakları tarafından paylaşılır (veya ise). Azure 'da veriler (varsayılan olarak) genellikle birden çok VM tarafından paylaşılmaz. Genişleme işlem kümesinde birden çok VM için veri paylaşımı gerekliyse, paylaşılan verilerin bu işlevselliği destekleyen bir kaynakta bulunması gerekir. Azure 'da, veri paylaşımı aşağıdaki bölümde açıklandığı gibi depolama ile ilgilidir.

## <a name="azure-compute-optimization"></a>Azure işlem iyileştirmesi

Her bir Azure mimarisinde işlem katmanını iyileştirebilirsiniz. Her ortam için en uygun sanal makine ve özellik türlerini kullanın. Aşağıdaki şekilde, DB2 kullanan bir CICS uygulamasını desteklemek üzere VM 'Leri Azure 'da dağıtmaya yönelik olası bir model gösterilmektedir. Birincil sitede, üretim, üretim öncesi ve test sanal makineleri yüksek kullanılabilirliğe sahip olarak dağıtılır. İkincil site yedekleme ve olağanüstü durum kurtarma içindir.

Her katman, uygun olağanüstü durum kurtarma hizmetleri de sağlayabilir. Örneğin, üretim ve veritabanı VM 'Leri, geliştirme ve test VM 'Leri soğuk kurtarmayı destekleirken, dinamik veya sıcak bir kurtarma gerektirebilir.

![Olağanüstü durum kurtarmayı destekleyen yüksek oranda kullanılabilir dağıtım](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Ana bilgisayar geçişi](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure sanal makinelerinde Ana bilgisayar yeniden barındırma](../overview.md)
- [Ana bilgisayar depolama alanını Azure 'a taşıma](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM kaynakları

- [IBM Z üzerinde Parallel Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS ve kuponu: temel bilgilerin ötesinde](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [DB2 pureScale özelliği yüklemesi için gerekli kullanıcıları oluşturma](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-örnek Oluştur komutu](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 Porescale kümelenmiş veritabanı çözümü](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Kamu

- [Anabilgisayar uygulamaları için Microsoft Azure Kamu Bulutu](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft ve Fedrampa](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Daha fazla geçiş kaynağı

- [Azure sanal veri merkezi yükselt ve kaydırma Kılavuzu](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS Iscsı](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
