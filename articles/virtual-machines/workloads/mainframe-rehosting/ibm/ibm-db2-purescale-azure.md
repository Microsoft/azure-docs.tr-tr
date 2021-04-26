---
title: Azure 'da IBM DB2 pureScale
description: Bu makalede, Azure 'da bir IBM DB2 pureScale ortamı çalıştırmaya yönelik bir mimari gösterilmektedir.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 4ab0dfabc5fc4773413e7009bb8a16e6b77d5c09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950204"
---
# <a name="ibm-db2-purescale-on-azure"></a>Azure 'da IBM DB2 pureScale

IBM DB2 pureScale ortamı, Linux işletim sistemlerinde yüksek kullanılabilirlik ve ölçeklenebilirlik sunan Azure için bir veritabanı kümesi sağlar. Bu makalede, Azure 'da DB2 pureScale 'yi çalıştırmaya yönelik bir mimari gösterilmektedir.

## <a name="overview"></a>Genel Bakış

Kuruluşlar, çevrimiçi işlem işleme (OLTP) ihtiyaçlarına göre daha fazla kullanan geleneksel ilişkisel veritabanı yönetim sistemi (RDBMS) platformlarını kullanır. Bu günler, çoğu, ana bilgisayar tabanlı veritabanı ortamlarını kapasiteyi genişletmek, maliyetleri azaltmak ve sabit bir operasyonel maliyet yapısını sürdürmek için bir yol olarak Azure 'a geçirmektedir. Geçiş, genellikle eski bir platformu modernleştirmede ilk adımdır. 

Son olarak, bir kurumsal müşteri, Azure 'da z/ç 'de IBM DB2 Puresreskisini çalıştıran IBM DB2 ortamını yeniden barındırılmaktadır. DB2 pureScale veritabanı kümesi çözümü, Linux işletim sistemleri üzerinde yüksek kullanılabilirlik ve ölçeklenebilirlik sağlar. Müşteri, DB2 Porescale 'yi yüklemeden önce Azure 'daki büyük ölçekli bir sistemde tek bir sanal makinede (VM) tek bir sanal makinede (VM) tek başına, ölçek örneği olarak başarıyla çalıştı. 

Başlangıçtaki ortamla aynı olmasa da, Linux 'ta IBM DB2 pureScale, ana bilgisayar üzerindeki bir Paralel Sysplex yapılandırmasında çalışan z/OS için IBM DB2 olarak benzer yüksek kullanılabilirlik ve ölçeklenebilirlik özellikleri sunar. Bu senaryoda, küme Iscsı aracılığıyla paylaşılan bir depolama kümesine bağlanır. Bulut depolama için özel olarak iyileştirilmiş, ücretsiz, ölçeklenebilir, açık kaynaklı bir dosya sistemi olan GlusterFS dosya sistemini kullandık. Ancak, IBM artık bu çözümü desteklememektedir. IBM 'nizin desteğini sürdürmek için desteklenen bir Iscsı uyumlu dosya sistemi kullanmanız gerekir. Microsoft, bir seçenek olarak Depolama Alanları Doğrudan (S2D) sunar

Bu makalede, bu Azure geçişi için kullanılan mimari açıklanmaktadır. Müşteri, yapılandırmayı sınamak için Red Hat Linux 7,4 kullandı. Bu sürüm, Azure Marketi 'nden edinilebilir. Linux dağıtımını seçmeden önce, desteklenen sürümleri doğrulayın. Ayrıntılar için bkz. [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) ve [GlusterFS](https://docs.gluster.org/en/latest/)belgeleri.

Bu makale, DB2 uygulama planınız için bir başlangıç noktasıdır. İş gereksinimleriniz farklılık gösterir, ancak aynı temel model geçerli olur. Azure 'da çevrimiçi analitik işleme (OLAP) uygulamaları için bu mimari modelini de kullanabilirsiniz.

Bu makalede, Linux üzerinde çalışan bir Linux DB2 için IBM DB2 pureScale 'ye bir Linux DB2 ile taşıma farklılıkları ve olası geçiş görevleri ele alınmaktadır. DB2 z/ç 'den DB2 Puresresküyle gezinmek için boyutlandırma tahminleri ve iş yükü analizleri sağlamaz. 

Ortamınız için en iyi DB2 pureScale mimarisine karar vermenize yardımcı olmak üzere boyutlandırmayı tam olarak tahmin etmenizi ve bir varsayım yapmanızı öneririz. Kaynak sistemde, DB2 z/OS Parallel sysplex veri paylaşımı mimarisi, Kupın yapılandırması ve dağıtılmış veri tesis (DDF) kullanım istatistikleri ile göz önünde bulundurduğunuzdan emin olun.

> [!NOTE]
> Bu makalede, DB2 geçişine yönelik bir yaklaşım açıklanmaktadır, ancak diğerleri vardır. Örneğin, DB2 pureScale, sanallaştırılmış şirket içi ortamlarda da çalıştırılabilir. IBM, çeşitli yapılandırmalarda Microsoft Hyper-V DB2 'yi destekler. Daha fazla bilgi için bkz. IBM bilgi merkezindeki [DB2 Porescale sanallaştırma mimarisi](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) .

## <a name="architecture"></a>Mimari

Azure 'da yüksek kullanılabilirlik ve ölçeklenebilirlik desteği sağlamak için DB2 Porescale için genişleme, paylaşılan veri mimarisi kullanabilirsiniz. Müşteri geçişi aşağıdaki örnek mimariyi kullandı.

![Depolama ve ağı gösteren Azure sanal makinelerinde DB2 pureScale](media/pureScaleArchitecture.png "Depolama ve ağı gösteren Azure sanal makinelerinde DB2 pureScale")


Diyagramda, bir DB2 pureScale kümesi için gereken mantıksal katmanlar gösterilmektedir. Bunlar bir istemciye yönelik sanal makineler, yönetim için, veritabanı altyapısı ve paylaşılan depolama için sanal makineler içerir. 

Veritabanı altyapısı düğümlerine ek olarak, diyagram, küme önbelleğe alma olanakları (CFs) için kullanılan iki düğüm içerir. Veritabanı altyapısının kendisi için en az iki düğüm kullanılır. PureScale kümesine ait bir DB2 sunucusuna üye denir. 

Küme, genişleme depolama ve yüksek kullanılabilirlik sağlamak için Iscsı aracılığıyla üç düğümlü bir paylaşılan depolama kümesine bağlanır. DB2 pureScale, Linux çalıştıran Azure sanal makinelerinde yüklüdür.

Bu yaklaşım, kuruluşunuzun boyutu ve ölçeği için değiştirebileceğiniz bir şablondur. Bu, aşağıdakilere dayalıdır:

-   İki veya daha fazla veritabanı üyesi en az iki CF düğümüyle birleştirilir. Düğümler, paylaşılan bellek ve genel kilit Yöneticisi (GLM) Hizmetleri için bir genel arabellek havuzunu (GBP) yönetir ve bu da paylaşılan erişimi denetlemek ve etkin üyelerden kilit çakışması. Bir CF düğümü, ikincil, yük devretme CF düğümü olarak birincil ve diğeri olarak davranır. Ortamdaki tek bir başarısızlık noktasını önlemek için, bir DB2 pureScale kümesi en az dört düğüm gerektirir.

-   Yüksek performanslı paylaşılan depolama alanı (diyagramda P30 boyutunda gösterilir). Her düğüm bu depolamayı kullanır.

-   Veri üyeleri ve paylaşılan depolama için yüksek performanslı ağ.

### <a name="compute-considerations"></a>İşlem değerlendirmeleri

Bu mimaride, Azure sanal makinelerinde uygulama, depolama ve veri katmanları çalıştırılır. [Dağıtım Kurulum betikleri](https://aka.ms/db2onazure) aşağıdakileri oluşturur:

-   DB2 pureScale kümesi. Azure 'da ihtiyacınız olan işlem kaynaklarının türü kuruluma göre değişir. Genel olarak, iki yaklaşımdan yararlanabilirsiniz:

    -   Küçük ve orta ölçekli örneklere paylaşılan depolama alanı erişimi olan çok düğümlü, yüksek performanslı bilgi işlem (HPC) stilindeki bir ağ kullanın. Bu HPC yapılandırma türü için, Azure bellek için iyileştirilmiş E-serisi veya depolama ile iyileştirilmiş L serisi [sanal makineler](../../../sizes.md) gereken işlem gücünü sağlar.

    -   Veri altyapıları için daha az büyük sanal makine örneği kullanın. Büyük örnekler için bellek için iyileştirilmiş en büyük [d serisi](https://azure.microsoft.com/pricing/details/virtual-machines/series/) sanal makineler, ağır bellek içi iş yükleri için idealdir. DB2 çalıştırmak için kullanılan mantıksal bölümün (LPAR) boyutuna bağlı olarak, adanmış bir örneğe ihtiyacınız vardır.

-   DB2 CF, E serisi veya L serisi gibi bellek için iyileştirilmiş sanal makineler kullanır.

-   \_ \_ Linux çalıştıran standart DS4 v2 sanal makinelerini kullanan paylaşılan bir depolama kümesi.

-   Yönetim atlama kutusu, \_ Linux çalıştıran standart bir DS2 \_ v2 sanal makinedir.  Sanal ağınızdaki tüm VM 'Ler için güvenli bir RDP/SSH deneyimi sağlayan bir hizmet olan Azure savunma bir alternatiftir.

-   İstemci, \_ Windows çalıştıran standart bir DS3 \_ v2 sanal makinedir (test için kullanılır).

-   *Isteğe bağlı*. Tanık sunucu. Bu yalnızca daha eski DB2 pureScale sürümleriyle gereklidir. Bu örnek, \_ \_ Linux çalıştıran standart DS3 v2 sanal makinesini KULLANıR (DB2 pureScale için kullanılır).

> [!NOTE]
> DB2 pureScale kümesi, en az iki DB2 örneği gerektirir. Ayrıca bir önbellek örneği ve bir kilit Yöneticisi örneği gerektirir.

### <a name="storage-considerations"></a>Depolama hakkında dikkat edilmesi gerekenler

Oracle RAC gibi DB2 pureScale, yüksek performanslı bir blok g/ç, genişleme veritabanıdır. Gereksinimlerinize uygun en büyük [Azure PREMIUM SSD](../../../disks-types.md) seçeneğini kullanmanızı öneririz. Üretim ortamları genellikle daha fazla depolama kapasitesine ihtiyaç duyurken, daha küçük depolama seçenekleri geliştirme ve test ortamları için uygun olabilir. Örnek mimari, ıOPS 'nin boyut ve fiyata oranı nedeniyle [P30](https://azure.microsoft.com/pricing/details/managed-disks/) kullanır. Boyut ne olursa olsun, en iyi performans için Premium depolama kullanın.

DB2 pureScale, tüm verilere tüm küme düğümlerinden erişilebilen bir paylaşılan her şey mimarisi kullanır. Premium Depolama, isteğe bağlı veya adanmış örneklerde, birden çok örnek arasında paylaşılmalıdır.

Büyük bir DB2 pureScale kümesi, 200 terabayt (TB) veya daha fazla Premium paylaşılan depolama alanı gerektirebilir, bu da 100.000 ıOPS ile kullanılabilir. DB2 pureScale, Azure 'da kullanabileceğiniz bir Iscsı blok arabirimini destekler. Iscsı arabirimi S2D veya başka bir araçla uygulayabileceğiniz bir paylaşılan depolama kümesi gerektirir. Bu tür bir çözüm, Azure 'da bir sanal depolama alanı ağı (vSAN) cihazı oluşturur. DB2 pureScale, sanal makineler arasında veri paylaşımında kullanılan kümelenmiş dosya sistemini yüklemek için vSAN 'ı kullanır.

### <a name="networking-considerations"></a>Ağ konusunda dikkat edilmesi gerekenler

IBM, bir DB2 pureScale kümesindeki tüm Üyeler için InfiniBand ağı önerir. DB2 pureScale Ayrıca, CFs için kullanılabilir yerlerde uzak doğrudan bellek erişimi (RDMA) kullanır.

Kurulum sırasında, tüm sanal makineleri içeren bir Azure [kaynak grubu](../../../../azure-resource-manager/management/overview.md) oluşturursunuz. Genel olarak, kaynakları kendi yaşam süresine göre gruplayan ve bunları yöneteceksiniz. Bu mimarideki sanal makineler [hızlandırılmış ağ](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)gerektirir. Tek köklü g/ç Sanallaştırması (SR-ıOV) ile bir sanal makineye tutarlı, ultra düşük ağ gecikmesi sağlayan bir Azure özelliğidir.

Her Azure sanal makinesi, alt ağları olan bir sanal ağa dağıtılır: Main, Gluster FS ön ucu (gfsfe), Gluster FS arka ucu (bfsin), DB2 pureScale (db2be) ve DB2 pureScale ön ucu (db2fe). Yükleme betiği, ana alt ağdaki sanal makinelerde birincil [NIC 'leri](../../../windows/multiple-nics.md) de oluşturur.

Sanal ağ içindeki ağ trafiğini kısıtlamak ve alt ağları yalıtmak için [ağ güvenlik grupları](../../../../virtual-network/virtual-network-vnet-plan-design-arm.md) 'nı kullanın.

Azure 'da DB2 pureScale 'in depolama için ağ bağlantısı olarak TCP/IP kullanması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

-   [Bu mimariyi Azure 'da dağıtın](deploy-ibm-db2-purescale-azure.md)