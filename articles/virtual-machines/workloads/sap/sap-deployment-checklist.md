---
title: SAP iş yükü planlama ve dağıtım denetim listesi | Microsoft Docs
description: Azure 'da SAP iş yükü Dağıtımlarını Planlama ve iş yüklerini dağıtma denetim listesi
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51bfd632e854132be27a7b971cf03e9fe5b00138
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504312"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure 'da SAP iş yükleri: planlama ve dağıtım denetim listesi

Bu denetim listesi, SAP NetWeaver, S/4HANA ve Hybrsıs uygulamalarını bir hizmet olarak Azure altyapısına taşıyan müşteriler için tasarlanmıştır. Projenin süresi boyunca müşteri ve/veya SAP iş ortağı denetim listesini gözden geçirmelidir. Denetimlerin çoğunun projenin başlangıcında ve planlama aşamasında tamamlandığını aklınızda bulundurulmanız önemlidir. Dağıtım gerçekleştirildikten sonra, dağıtılan Azure altyapısına veya SAP yazılım yayınlarına yönelik doğrudan değişiklikler karmaşık hale gelebilir.

Projeniz sırasında anahtar kilometre taşları içindeki denetim listesini gözden geçirin. Bunun yapılması, küçük sorunları büyük sorunlar yapmadan önce algılamanıza olanak tanır. Ayrıca, gerekli değişiklikleri yeniden mühendisle ve test etmek için yeterli zaman vardır. Bu denetim listesini tamamlamayı düşünün. Durumunuza bağlı olarak, birçok daha fazla denetim gerçekleştirmeniz gerekebilir.

Denetim listesi, Azure 'dan bağımsız görevler içermez. Örneğin, SAP uygulama arabirimleri, Azure platformuna veya bir barındırma sağlayıcısına geçiş sırasında değişir.

Bu denetim listesi, zaten dağıtılmış sistemler için de kullanılabilir. Yazma Hızlandırıcısı ve Kullanılabilirlik Alanları gibi yeni özellikler ve yeni VM türleri, dağıtıldıktan sonra eklenmiş olabilir. Bu nedenle, Azure platformunda yeni özelliklerden haberdar olduğunuzdan emin olmak için denetim listesini düzenli aralıklarla gözden geçirmeniz yararlı olabilir.

## <a name="project-preparation-and-planning-phase"></a>Proje hazırlama ve planlama aşaması
Bu aşamada, SAP iş yükünüzün geçişini Azure platformuna planlarsınız. En azından, bu aşamada aşağıdaki belgeleri oluşturmanız ve geçişin aşağıdaki öğelerini tanımlamanız ve tartışmanız gerekir:

1. Üst düzey tasarım belgesi. Bu belge şunları içermelidir:
    - SAP bileşenlerinin ve uygulamalarının geçerli envanteri ve Azure için bir hedef uygulama envanteri.
    - İlgili tarafların sorumluluklarını ve atamalarını tanımlayan bir sorumluluk atama matrisi (KCI). Yüksek düzeyde başlatın ve planlama ve ilk dağıtımlar genelinde daha ayrıntılı düzeyler için çalışın.
    - Üst düzey bir çözüm mimarisi.
    - Hangi Azure bölgelerinin dağıtılacağı konusunda bir karar. [Azure bölgelerinin listesini](https://azure.microsoft.com/global-infrastructure/regions/)görüntüleyin. Her bölgede hangi hizmetlerin kullanılabilir olduğunu öğrenmek için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/).
    - Şirket içinden Azure 'a bağlanmak için bir ağ mimarisi. [Azure Için sanal veri merkezi şeması](/azure/architecture/vdc/)hakkında bilgi edinmek için başlatın.
    - Azure 'da yüksek etkili iş verilerini çalıştırmaya yönelik güvenlik ilkeleri. Veri güvenliği hakkında bilgi edinmek için [Azure Güvenlik belgeleriyle](../../../security/index.yml)başlayın.
2.  Teknik tasarım belgesi. Bu belge şunları içermelidir:
    - Çözüm için bir blok diyagramı.
    - Azure 'daki işlem, depolama ve ağ bileşenlerinin boyutu. Azure VM 'lerinin SAP boyutlandırması için bkz. [SAP 
    -  Note #1928533] ( https://launchpad.support.sap.com/#/notes/1928533) .
    - İş sürekliliği ve olağanüstü durum kurtarma mimarisi.
    - İşletim sistemi, DB, çekirdek ve SAP destek paketi sürümleri hakkında ayrıntılı bilgi. SAP NetWeaver veya S/4HANA tarafından desteklenen her işletim sistemi sürümünün Azure VM 'lerinde desteklendiğinden true olması gerekmez. Aynı, DBMS yayınları için de geçerlidir. SAP ve Azure desteğinin sağlanması için gerekli olan SAP sürümlerini, DBMS sürümlerini ve işletim sistemi sürümlerini hizalamak ve gerekirse, aşağıdaki kaynakları kontrol edin. SAP ve Microsoft 'tan tam destek almak için SAP ve Azure tarafından desteklenen yayın birleşimlerinin olması gerekir. Gerekirse, bazı yazılım bileşenlerini yükseltmeyi planlamanız gerekir. Desteklenen SAP, OS ve DBMS yazılımları hakkında daha fazla ayrıntı aşağıda belirtilmiştir:
        - [Sap destek notunun #1928533](https://launchpad.support.sap.com/#/notes/1928533). Bu notta, Azure VM 'lerinde desteklenen en düşük işletim sistemi sürümleri tanımlanmaktadır. Ayrıca, HANA olmayan çoğu veritabanı için gereken en düşük veritabanı sürümlerini tanımlar. Son olarak, SAP tarafından desteklenen Azure VM türleri için SAP boyutlandırma sağlar.
        - [Sap destek notunun #2015553](https://launchpad.support.sap.com/#/notes/2015553). Bu notta, Azure depolama ve Microsoft ile ilgili destek ilişkisinin yanı sıra destek ilkeleri tanımlanmaktadır.
        - [Sap destek notunun #2039619](https://launchpad.support.sap.com/#/notes/2039619). Bu notta Azure için Oracle destek matrisi tanımlanmaktadır. Oracle, SAP iş yükleri için Azure 'da yalnızca Windows ve Oracle Linux 'yi konuk işletim sistemleri olarak destekler. Bu destek beyanı, SAP örnekleri çalıştıran SAP uygulama katmanı için de geçerlidir. Ancak, Oracle, pacemaker üzerinden Oracle Linux SAP Merkezi Hizmetleri için yüksek kullanılabilirliği desteklemez. Oracle Linux yoks için yüksek kullanılabilirliğe ihtiyacınız varsa, Linux için SIOS koruma paketini kullanmanız gerekir. Ayrıntılı SAP sertifika verileri için bkz. SAP destek notunun [#1662610-Linux IÇIN SIOS koruma paketine yönelik destek ayrıntıları](https://launchpad.support.sap.com/#/notes/1662610). Windows için SAP tarafından desteklenen Windows Server Yük Devretme Kümelemesi çözümü, SAP Merkezi Hizmetleri için, DBMS katmanı olarak Oracle ile birlikte desteklenir.
        - [Sap destek notunun #2235581](https://launchpad.support.sap.com/#/notes/2235581). Bu notta, farklı işletim sistemi sürümlerindeki SAP HANA için destek matrisi sağlanmaktadır.
        - SAP HANA desteklenen Azure VM 'Leri ve [Hana büyük örnekleri](./hana-overview-architecture.md) [SAP web sitesinde](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)listelenmiştir.
        - [SAP ürün kullanılabilirliği matrisi](https://support.sap.com/en/).
        - [SAP destek notunun #2555629-SAP HANA 2,0 dinamik katmanlama – hiper yönetici ve bulut desteği](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP destek notunun #1662610-Linux için SIOS koruma paketine yönelik destek ayrıntıları](https://launchpad.support.sap.com/#/notes/1662610)
        - SAP 'ye özgü diğer ürünler için SAP notları.     
    - SAP Merkezi Hizmetleri için çok SID küme yapılandırmalarının kullanılması, Azure 'da Windows, SLES ve RHEL Konuk işletim sistemlerinde desteklenir. Bir çok SID kümesine yerleştirdiğiniz daha fazla ASCS/SCS 'yi, elde eden yarıçapın artırabileceğini aklınızda bulundurun. İlgili Konuk işletim sistemi senaryosuna ait belgeleri şu makalelerde bulabilirsiniz:
        - [SAP ASCS/SCS örneği Windows Server Yük Devretme Kümelemesi ve paylaşılan disk ile Azure üzerinde çok düzeyli yüksek kullanılabilirlik](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [SAP ASCS/SCS örneği Windows Server Yük Devretme Kümelemesi ve dosya paylaşımıyla Azure 'da yüksek oranda kullanılabilirlik](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [SAP NetWeaver için SUSE Linux Enterprise Server Azure VM 'lerde yüksek kullanılabilirlik çoklu SID Kılavuzu](./high-availability-guide-suse-multi-sid.md)
        - [SAP NetWeaver için Red Hat Enterprise Linux Azure VM 'lerde yüksek kullanılabilirlik çoklu SID Kılavuzu](./high-availability-guide-rhel-multi-sid.md)
    - Yüksek kullanılabilirlik ve olağanüstü durum kurtarma mimarisi.
        - RTO ve RPO temelinde, yüksek kullanılabilirlik ve olağanüstü durum kurtarma mimarisinin nasıl görünmesi gerektiğini tanımlayın.
        - Bir bölge içinde yüksek kullanılabilirlik için, istenen DBMS 'nin Azure 'da sunmasına ne olduğunu denetleyin. Çoğu DBMS paketi, üretim sistemleri için önerdiğimiz, zaman uyumlu bir etkin bekleme, zaman uyumlu yöntemler sunar. Ayrıca, [SAP iş yükleri ve ilgili belgeler Için Azure sanal MAKINELER DBMS dağıtımına ilişkin bazı hususlar](./dbms_guide_general.md) ile başlayan farklı VERITABANLARı için SAP ile ilgili belgelere bakın.
           Örneğin [SQL Server açıklananlar](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)gıbı, DBMS katmanının paylaşılan disk yapılandırmasıyla Windows Server Yük Devretme Kümelemesi kullanmak desteklenmez. Bunun yerine, şunun gibi çözümler kullanın:
           - [SQL Server Always On](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
           - [HANA sistem çoğaltması](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Azure bölgeleri arasında olağanüstü durum kurtarma için farklı DBMS satıcıları tarafından sunulan çözümleri gözden geçirin. Çoğu zaman uyumsuz çoğaltmayı veya günlük dağıtımını destekler.
        - SAP uygulama katmanı için, aynı Azure bölgesindeki veya DR bölgesindeki üretim dağıtımlarınızın çoğaltmaları ideal olan iş gerileme test sistemlerinizi çalıştırıp çalıştıramayacağını saptayın. İkinci durumda, bu iş gerileme sistemini üretim dağıtımlarınız için DR hedefi olarak hedefleyebilirsiniz.
        - Üretim dışı sistemleri DR sitesine yerleştirmemeye karar verirseniz, SAP uygulama katmanını Azure DR bölgesine çoğaltmak için bir yöntem olarak Azure Site Recovery bakın. Daha fazla bilgi için bkz. [çok KATMANLı SAP NetWeaver uygulama dağıtımı için olağanüstü durum kurtarmayı ayarlama](../../../site-recovery/site-recovery-sap.md).
        - [Azure kullanılabilirlik alanları](../../../availability-zones/az-overview.md)kullanarak birleştirilmiş bir HADR yapılandırması kullanmaya karar verirseniz kullanılabilirlik alanları kullanılabilir olan Azure bölgelerini tanıyın. Ayrıca iki Kullanılabilirlik Alanları arasındaki artan ağ gecikmeleri ile ortaya çıkan hesap kısıtlamalarını da dikkate alın.  
3.  Tüm SAP arabirimlerinin (SAP ve SAP olmayan) bir stoku.
4.  Foundation hizmetlerinin tasarımı. Bu tasarım aşağıdaki öğeleri içermelidir:
    - Active Directory ve DNS tasarımı.
    - Azure içindeki ağ topolojisi ve farklı SAP sistemleri atama.
    - Azure 'da altyapı ve SAP uygulamalarını yöneten takımlar için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../../role-based-access-control/overview.md) yapısı.
    - Kaynak grubu topolojisi.
    - [Etiketleme stratejisi](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - VM 'Ler ve diğer altyapı bileşenleri ve/veya mantıksal adlar için adlandırma kuralları.
5.  Microsoft Professional veya Premier Destek sözleşmesi. Microsoft 'un sahip olduğu bir Premier destek sözleşmeniz varsa, Microsoft Teknik Hesap Yöneticisi 'Ni (TAM) tanımlamak. SAP destek gereksinimleri için bkz. [sap destek notunun #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Abonelikler için Azure abonelikleri ve çekirdek kota sayısı. Gerektiğinde [Azure aboneliklerinin kotalarını artırmak için destek Istekleri açın](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) .
7.  SAP verilerini Azure 'a geçirmek için veri azaltma ve veri geçişi planı. SAP NetWeaver sistemlerinde SAP, büyük miktarlarda veri hacminin nasıl sınırlandırılmasıyla ilgili yönergeler sağlar. SAP ERP sistemlerinde veri yönetimiyle ilgili [Bu SAP kılavuzuna](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) bakın. İçeriğin bazıları Ayrıca genel olarak NetWeaver ve S/4HANA sistemleri için de geçerlidir.
8.  Otomatikleştirilmiş bir dağıtım yaklaşımı. Azure üzerinde altyapı dağıtımları Otomasyonu hedefi, belirleyici bir şekilde dağıtılır ve belirleyici sonuçlar elde etmek için kullanılır. Birçok müşteri PowerShell veya CLı tabanlı betikleri kullanır. Ancak SAP için Azure altyapısını dağıtmak ve hatta SAP yazılımı yüklemek için kullanabileceğiniz çeşitli açık kaynaklı teknolojiler vardır. GitHub 'da örnekleri bulabilirsiniz:
    - [Azure bulutta otomatik SAP dağıtımları](https://github.com/Azure/sap-hana)
    - [SAP HANA yükleme](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Müşteri, Sistem Tümleştirici, Microsoft ve diğer ilgili taraflar arasında bir normal tasarım ve dağıtım incelemesi temposunda tanımlayın.


## <a name="pilot-phase-strongly-recommended"></a>Pilot aşaması (önemle önerilir)
 
Pilot uygulamayı, proje planlama ve hazırlık sırasında veya sırasında çalıştırabilirsiniz. Ayrıca, planlama ve hazırlık aşamasında yapılan yaklaşımları ve tasarımları test etmek için pilot aşamasını da kullanabilirsiniz. Ayrıca pilot aşamasını genişleterek gerçek bir kavram kanıtı oluşturabilirsiniz.

Bir pilot dağıtımı sırasında tam bir HADR çözümü ve güvenlik tasarımını ayarlamanızı ve doğrulamanızı öneririz. Bazı müşteriler bu aşamada ölçeklenebilirlik testi gerçekleştirir. Diğer müşteriler, bir pilot aşaması olarak SAP Sandbox sistemlerinin dağıtımlarını kullanır. Pilot için Azure 'a geçirmek istediğiniz bir sistemi zaten tanımladığınızı varsayalım.

1. Veri aktarımını Azure 'a iyileştirin. En iyi seçenek, belirli senaryoya son derece bağımlıdır. ExpressRoute bağlantı hattının yeterli bant genişliğine sahip olması durumunda [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) üzerinden şirket içinden aktarım daha hızlı olur. Diğer senaryolarda Internet üzerinden aktarım daha hızlıdır.
2. Verilerin dışarı ve içeri aktarılmasını içeren heterojen bir SAP platformu geçişi için, dışa aktarma ve içeri aktarma aşamalarını test edin ve iyileştirin. Hedef platformu SQL Server olan büyük geçişler için [öneriler](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)bulabilirsiniz. Birleşik bir yayın yükseltmesine gerek duymuyorsanız geçiş Izleyicisi/SWPM kullanabilirsiniz. Geçişi bir SAP sürüm yükseltmesi ile birleştirdiğinizde [SAP dmo](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) işlemini kullanabilirsiniz. Bunu yapmak için, kaynak ve Hedef DBMS platformu birleşiminin belirli gereksinimlerini karşılamanız gerekir. Bu işlem, [2,0 SP03 toplamının veritabanı geçiş seçeneği (dmo)](https://launchpad.support.sap.com/#/notes/2631152)bölümünde belgelenmiştir.
   1.  Kaynağa aktarın, dosya yükleme işlemini Azure 'a aktarın ve performansı içeri aktarın. Dışarı aktarma ve içeri aktarma arasındaki üst düzeye çıkarın.
   2.  Altyapı boyutlandırma amacıyla hedef ve hedef platformlarda veritabanının hacmini değerlendirin.
   3.  Zamanlamayı doğrulayın ve iyileştirin.
1. Teknik doğrulama.
   1. VM türleri.
        - SAP destek notlarındaki kaynakları, SAP HANA donanım dizinindeki ve SAP PAM ' de tekrar gözden geçirin. Azure için desteklenen VM 'Lerde, bu VM türleri için desteklenen işletim sistemi yayınlarına ve desteklenen SAP ve DBMS sürümlerinden herhangi bir değişiklik olmadığından emin olun.
        - Uygulamanızın ve Azure 'da dağıttığınız altyapının boyutunu yeniden doğrulayın. Mevcut uygulamaları taşıyorsanız, genellikle kullandığınız altyapıdan ve [SAP kıyaslama Web sayfasından](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) gerekli SAPS 'leri türetebilir ve bunu [sap destek Not#1928533](https://launchpad.support.sap.com/#/notes/1928533)listelenen SAPS numaralarıyla karşılaştırabilirsiniz. Ayrıca, [Bu makaleye SAPS derecelendirmeleri](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) göz önünde bulundurun.
        - Azure VM 'lerinizin boyutunu, planlama aşamasında seçtiğiniz VM türlerinin maksimum depolama alanı işleme ve ağ aktarım hızına göre değerlendirin ve test edin. Verileri buradan bulabilirsiniz:
           -  [Azure 'Da Windows sanal makineleri Için boyutlar](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Boyut için *önbelleğe alınmamış maksimum disk aktarım hızını* göz önünde bulundurmanız önemlidir.
           -  [Azure 'Da Linux sanal makineleri Için boyutlar](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Boyut için *önbelleğe alınmamış maksimum disk aktarım hızını* göz önünde bulundurmanız önemlidir.
   2. Depolama’yı seçin.
        - [SAP iş yükü için belge Azure depolama türlerini](./planning-guide-storage.md) denetleyin
        - En azından, SAP uygulama katmanlarını temsil eden VM 'Ler için ve performans duyarlı olmayan DBMS 'lerin dağıtımı için [Azure Standart SSD depolama](../../disks-types.md#standard-ssd) kullanın.
        - Genel olarak, [Azure Standart HDD disklerinin](../../disks-types.md#standard-hdd)kullanımını önermiyoruz.
        - Uzaktan performansa duyarlı tüm DBMS VM 'Leri için [Azure Premium Depolama](../../disks-types.md#premium-ssd) kullanın.
        - [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/)kullanın.
        - D serisi olan DBMS günlük sürücüleri için Azure Yazma Hızlandırıcısı kullanın. [Yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md)' de belgelendiği gibi yazma Hızlandırıcısı limitlerinin ve kullanımlardan haberdar olun.
        - Farklı DBMS türleri için [SAP ile ilgili genel DBMS belgelerini](./dbms_guide_general.md) ve genel belgenin Işaret ettiği DBMS 'ye özgü belgeleri denetleyin.
        - SAP HANA hakkında daha fazla bilgi için bkz. [Azure 'da altyapı yapılandırmalarına ve işlemlerine SAP HANA](./hana-vm-operations.md).
        - Azure veri disklerini, cihaz KIMLIĞI kullanarak bir Azure Linux sanal makinesine hiçbir şekilde bağlama. Bunun yerine, evrensel benzersiz tanımlayıcıyı (UUID) kullanın. Örneğin, Azure veri disklerini bağlamak için grafiksel araçlar kullanırken dikkatli olun. UUID 'nin diskleri bağlamak için kullanıldığından emin olmak için/etc/fstab içindeki girdileri çift kontrol edin. Daha fazla ayrıntı için [Bu makaleyi](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)bulabilirsiniz.
   3. İşlemleri.
        - Sanal ağ altyapınızı ve farklı Azure sanal ağları üzerinde veya içinde SAP uygulamalarınızın dağıtımını test edin ve değerlendirin.
        -  Tek bir Azure sanal ağı içindeki hub ve bağlı bileşen sanal ağ mimarisi yaklaşımını veya mikro segmentleme yaklaşımını değerlendirin. Bu değerlendirmeyi temel alan:
               1. Eşlenen [Azure sanal ağları](../../../virtual-network/virtual-network-peering-overview.md)arasındaki veri değişim ücretleri. Maliyetler hakkında daha fazla bilgi için bkz. [sanal ağ fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Bir sanal ağ içinde bir alt ağı yalıtmak üzere ağ güvenlik grubunu değiştirmenin aksine, Azure sanal ağları arasındaki eşlemenin hızlı bir şekilde kesilmesi avantajları. Bu değerlendirme, sanal ağın alt ağında barındırılan uygulamalar veya VM 'Lerin bir güvenlik riski haline geldiğini durumlar içindir.
                3. Şirket içi, dış dünya ve Azure 'da oluşturduğunuz sanal veri merkezi arasındaki ağ trafiğini merkezi olarak günlüğe kaydetme ve denetleme.
        - SAP uygulama katmanı ve SAP DBMS katmanı arasındaki veri yolunu değerlendirin ve test edin.
            -  SAP NetWeaver, Hybru veya S/4HANA 'ya dayalı sap sistemleri için SAP uygulaması ve DBMS katmanı arasındaki iletişim yoluna [Azure ağ sanal gereçlerinin](https://azure.microsoft.com/solutions/network-appliances/) yerleştirilmesi desteklenmez.
            -  SAP uygulama katmanının ve SAP DBMS 'nin eşlenmeyen farklı Azure sanal ağlarında yerleştirilmesi desteklenmez.
            -  SAP uygulama katmanı ve SAP DBMS katmanı arasındaki yolları tanımlamak için [uygulama güvenlik grubu ve ağ güvenlik grubu kurallarını](../../../virtual-network/network-security-groups-overview.md) kullanabilirsiniz.
        - SAP uygulama katmanında ve SAP DBMS katmanında kullanılan VM 'lerde [Azure hızlandırılmış ağ](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 'ın etkinleştirildiğinden emin olun. Azure 'da hızlandırılmış ağı desteklemek için farklı işletim sistemi düzeylerinin gerekli olduğunu aklınızda bulundurun:
            - Windows Server 2012 R2 veya üzeri.
            - SUSE Linux 12 SP3 veya üzeri.
            - RHEL 7,4 veya üzeri.
            - Oracle Linux 7,5. RHCKL çekirdeğini kullanıyorsanız, 3.10.0-862.13.1. EL7 sürümü gerekir. Oracle UEK çekirdeğini kullanıyorsanız sürüm 5 gerekir.
        - Sap destek notlarına [#500235](https://launchpad.support.sap.com/#/notes/500235) ve [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)göre SAP uygulama KATMANı VM 'leri ve DBMS VM 'ler arasındaki ağ gecikmesini test edin ve değerlendirin. [Sap desteği not#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)ağ gecikmesi yönergelerine karşı sonuçları değerlendirin. Ağ gecikmesi orta veya iyi bir aralıkta olmalıdır. [Bu makalede](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)belgelendiği gibi, VM 'ler ve Hana büyük örnek birimleri arasındaki trafik için özel durumlar geçerlidir.
        - ILB dağıtımlarının doğrudan sunucu dönüşü kullanacak şekilde ayarlandığından emin olun. Bu ayar, DBMS katmanında yüksek kullanılabilirlik yapılandırmalarında Azure ılbs 'ler kullanıldığında gecikme süresini azaltır.
        - Linux konuk işletim sistemleriyle birlikte Azure Load Balancer kullanıyorsanız, Linux ağ parametresinin **net.ipv4.tcp_timestamps** **0** olarak ayarlandığından emin olun. Bu öneri, daha eski [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421)sürümlerindeki önerilerle çakışıyor. SAP Note artık bu parametrenin Azure yük dengeleyiciler ile çalışması için **0** olarak ayarlanması gerektiğini belirten bir durum olarak güncelleştirilir.
        - En iyi ağ gecikmesini sağlamak için [Azure yakınlık yerleştirme gruplarını](../../co-location.md) kullanmayı düşünün. Daha fazla bilgi için bkz. [SAP uygulamalarıyla en iyi ağ gecikmesi Için Azure yakınlık yerleşimi grupları](sap-proximity-placement-scenarios.md).
   4. Yüksek kullanılabilirlik ve olağanüstü durum kurtarma dağıtımları.
        - SAP uygulama katmanını belirli bir Azure kullanılabilirlik bölgesi belirtmeden dağıtırsanız, SAP iletişim kutusu örnekleri veya tek bir SAP sisteminin ara yazılım örneklerini çalıştıran tüm VM 'Lerin bir [kullanılabilirlik kümesinde](../../availability-set-overview.md)dağıtıldığından emin olun.
        - SAP Merkezi Hizmetleri ve DBMS için yüksek kullanılabilirliğe ihtiyacınız yoksa, bu VM 'Leri SAP uygulama katmanıyla aynı Kullanılabilirlik kümesine dağıtabilirsiniz.
        - Pasif çoğaltma kullanarak yüksek kullanılabilirlik için SAP merkezi hizmetlerini ve DBMS katmanını koruduğunuzda, SAP Merkezi Hizmetleri için iki düğümü ayrı bir kullanılabilirlik kümesine ve farklı bir kullanılabilirlik kümesindeki iki DBMS düğümüne yerleştirin.
        - Azure Kullanılabilirlik Alanları dağıtırsanız, kullanılabilirlik kümelerini kullanamazsınız. Ancak, etkin ve pasif Merkezi Hizmetler düğümlerini iki farklı Kullanılabilirlik Alanları dağıttığınızdan emin olmanız gerekir. Aralarında en düşük gecikme süresine sahip Kullanılabilirlik Alanları kullanın.
          DBMS ve SAP Merkezi Hizmetler katmanı için Kullanılabilirlik Alanları üzerinde Windows veya Paceyapıcısı yük devretme kümeleri oluşturma durumu için [Azure Standart Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) kullanmanız gerektiğini unutmayın. Bölgesel dağıtımları için [temel Load Balancer](../../../load-balancer/load-balancer-overview.md) kullanamazsınız.
   5. Zaman aşımı ayarları.
        - Sıraya alma sunucusu ve SAP iş işlemleriyle bağlantı kesmesi olmadığından emin olmak için SAP örneklerinin SAP NetWeaver geliştirici izlemelerini denetleyin. Bu iki kayıt defteri parametresini ayarlayarak bu bağlantı kesilmesinden kaçınabilirsiniz:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Daha fazla bilgi için bkz. [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - Hklm\system\currentcontrolset\services\tcpip\parameters\keepaliveınterval = 120000. Daha fazla bilgi için bkz. [Keepaliveınterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Azure 'da dağıtılan şirket içi SAP GUI arabirimleri ve SAP uygulama katmanları arasındaki GUI zaman aşımlarını önlemek için, bu parametrelerin varsayılan. PFL veya örnek profilinde ayarlanmış olup olmadığını denetleyin:
            - rdisp/keepalive_timeout = 3600
            - rdisp/KeepAlive = 20
        - SAP sıraya alma işlemi ve SAP çalışma işlemleri arasında kurulan bağlantıların kesintiye uğramasını önlemek için **EnQue/encnı/set_so_keepalive** parametresini **true** olarak ayarlamanız gerekir. Ayrıca bkz. [SAP note #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Windows Yük devretme kümesi yapılandırması kullanıyorsanız, yanıt vermeyen düğümlerde tepki vermeye yönelik sürenin Azure için doğru ayarlandığından emin olun. [Yük devretme kümesi ağ eşiklerini ayarlama](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) makalesi, parametreleri ve yük devretme sensitivities nasıl etkilediğini listeler. Küme düğümlerinin aynı alt ağda olduğu varsayıldığında, bu parametreleri değiştirmelisiniz:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - Routinggeçmişini = 30
    6. İşletim sistemi ayarları veya düzeltme ekleri
        - SAP üzerinde HANA 'yı çalıştırmak için bu notları ve belgeleri okuyun:
            -   [SAP destek notuna #2814271 SAP HANA yedekleme, sağlama hatası ile Azure 'da başarısız oluyor](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP destek notunun #2753418-süreölçer geri dönüşü nedeniyle olası performans düşüşü](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP destek notuna #2791572-Azure 'da Hyper-V Için eksik VDSO desteği nedeniyle performans düşüşü](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP destek notunun #2382421-ağ yapılandırmasını HANA ve işletim sistemi düzeyinde Iyileştirme](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP destek notuna #2694118-Red Hat Enterprise Linux HA Add-On Azure 'da](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP destek notu #1984787-SUSE LINUX Enterprise Server 12: yükleme notları](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP destek notunun #2002167-Red Hat Enterprise Linux 7. x: yükleme ve yükseltme](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP destek notunun #2292690-SAP HANA DB: RHEL 7 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP destek notunun #2772999-Red Hat Enterprise Linux 8. x: yükleme ve yapılandırma](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP destek notunun #2777782-SAP HANA DB: RHEL 8 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP destek notunun #2578899-SUSE Linux Enterprise Server 15: yükleme notunun](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP destek notunun #2455582-Linux: GCC 6. x ile derlenen SAP uygulamaları çalıştırma](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP destek Not #2729475-HWCCT, SAP HANA için sertifikalı Azure VM 'lerinde "hiper yönetici desteklenmez" hatasıyla başarısız oldu](https://launchpad.support.sap.com/#/notes/2729475)
1. Yüksek kullanılabilirlik ve olağanüstü durum kurtarma yordamlarınızı test edin.
   1. VM 'leri (Windows Konuk işletim sistemleri) kapatarak veya işletim sistemlerini panik moduna (Linux konuk işletim sistemleri) yerleştirerek yük devretme durumlarının benzetimini yapın. Bu adım, yük devretme yapılandırmalarınızın tasarlandıkları şekilde çalışıp çalışmadığını bulmanıza yardımcı olur.
   1. Yük devretmenin yürütülmesi için geçen süreyi ölçer. Süreler çok uzunsa şunları göz önünde bulundurun:
        - SUSE Linux için, yük devretmeyi hızlandırmak üzere Azure çit Aracısı yerine SBD cihazlarını kullanın.
        - SAP HANA için, verilerin yeniden yüklenmesi çok uzun sürerse daha fazla depolama bant genişliği sağlamayı düşünün.
   3. Yedekleme/geri yükleme sırasını ve zamanlamasını test edin ve gerekirse düzeltmeler yapın. Yedekleme sürelerinin yeterli olduğundan emin olun. Geri yükleme ve zaman geri yükleme etkinliklerini de test etmeniz gerekir. RTO SLA 'larınızın bir veritabanı veya VM geri yükleme işlemini temel aldığından emin olun.
   4. Bölgeler arası DR işlevlerini ve mimarisini test edin.
1. Güvenlik denetimleri.
   1. Azure rol tabanlı erişim denetimi (Azure RBAC) mimarinizin geçerliliğini test edin. Amaç, farklı takımların erişimini ve izinlerini ayırmak ve sınırlamak. Örneğin, SAP tabanlı ekip üyeleri VM 'Leri dağıtıp Azure depolama 'dan belirli bir Azure sanal ağına disk atayabilmelidir. Ancak SAP tabanlı ekibin kendi sanal ağlarını oluşturamayacak veya mevcut sanal ağların ayarlarını değiştirememesi gerekir. Ağ ekibinin üyeleri, SAP uygulaması ve DBMS VM 'lerinin çalıştığı sanal ağlarda VM 'Leri dağıtabilmelidir. Ya da bu ekibin üyeleri sanal makinelerin özniteliklerini değiştirebilir veya hatta VM 'Leri ya da diskleri silebilir.  
   1.  [Ağ güvenlik grubu ve ASC](../../../virtual-network/network-security-groups-overview.md) kurallarının beklenen şekilde çalıştığını ve korunan kaynakları koruduğuna emin olun.
   1.  Şifrelenmesi gereken tüm kaynakların şifrelendiğinden emin olun. Sertifikaları yedeklemek, bu sertifikaları depolamak ve erişmek ve şifrelenmiş varlıkları geri yüklemek için süreçler tanımlayın ve uygulayın.
   1.  Bir işletim sistemi destek noktasından mümkün olduğunda, işletim sistemi diskleri için [Azure disk şifrelemesi](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) kullanın.
   1.  Çok fazla şifreleme katmanı kullandığınızdan emin olun. Bazı durumlarda, aynı sunucudaki farklı diskleri veya bileşenleri korumak için, DBMS Saydam Veri Şifrelemesi yöntemlerinden biriyle birlikte Azure disk şifrelemesi 'ni kullanmak mantıklı değildir.  Örneğin, bir SAP DBMS sunucusunda Azure disk şifrelemesi (ADE) işletim sistemi önyükleme diskinde etkinleştirilebilir (IŞLETIM sistemi ADE 'yi destekliyorsa) ve bu veri diskleri DBMS veri Kalıcılık dosyaları tarafından kullanılmıyor olabilir.  Örnek, DBMS TDE şifreleme anahtarlarını tutan diskte ADE 'yi kullanmaktır.
1. Performans testi. SAP, SAP izleme ve ölçümlerine bağlı olarak şu karşılaştırmaları yapın:
   - Uygun olduğunda, ilk 10 çevrimiçi raporu geçerli uygulamanız ile karşılaştırın.
   - Uygun olduğunda, ilk 10 toplu işi geçerli uygulamanız ile karşılaştırın.
   - Arabirimler aracılığıyla SAP sistemine veri aktarımlarını karşılaştırın. Aktarımın Şirket içinden Azure 'a benzer farklı konumlar arasında gittiğini bildiğiniz arabirimlere odaklanın.


## <a name="non-production-phase"></a>Üretim dışı aşaması 
Bu aşamada, başarılı bir pilot veya kavram kanıtı (POC) sonrasında, üretim dışı SAP sistemlerini Azure 'a dağıtmaya başladığınızı varsayalım. POC sırasında öğrendiğiniz ve karşılaştığınız her şeyi bu dağıtıma ekleyin. POCs için listelenen tüm ölçüt ve adımlar bu dağıtım için de geçerlidir.

Bu aşamada, genellikle geliştirme sistemlerini, birim testi sistemlerini ve iş gerileme test sistemlerini Azure 'a dağıtırsınız. Bir SAP uygulama satırındaki en az bir üretim dışı sisteminin, gelecekteki üretim sisteminin sahip olacağı tam yüksek kullanılabilirlik yapılandırmasına sahip olmasını öneririz. Bu aşamada gerçekleştirmeniz gereken bazı ek adımlar aşağıda verilmiştir:  

1.  Sistemleri eski platformdan Azure 'a taşımadan önce, CPU kullanımı, depolama alanı işleme ve ıOPS verileri gibi kaynak tüketim verilerini toplayın. Özellikle bu verileri DBMS katman birimlerinden toplayın, ancak aynı zamanda uygulama katmanı birimlerinden da toplayın. Ayrıca ağ ve depolama gecikmesini ölçer.
2.  Sistemlerinizin kullanılabilirlik kullanım süresi desenlerini kaydedin. Amaç, üretim dışı sistemlerin her gün, her gün veya bir haftanın veya ayın belirli aşamaları sırasında kapatılabilen üretim dışı sistemler olup olmadığını anlamak için geçerlidir.
3.  Test edin ve Azure 'da VM 'niz için kendi işletim sistemi görüntülerini oluşturmak isteyip istemediğinizi veya Azure Paylaşılan görüntü galerisinden bir görüntü kullanmak isteyip istemediğinizi öğrenin. Paylaşılan görüntü galerisinden bir görüntü kullanıyorsanız, işletim sistemi satıcınızla destek sözleşmesini yansıtan bir görüntü kullandığınızdan emin olun. Bazı işletim sistemi satıcıları için, paylaşılan görüntü Galerisi kendi lisans görüntülerinizi almanızı sağlar. Diğer işletim sistemi görüntüleri için destek, Azure tarafından teklif edilen fiyata dahildir. Kendi işletim sistemi görüntülerinizi oluşturmaya karar verirseniz, bu makalelerdeki belgeleri bulabilirsiniz:
    -   [Azure 'da dağıtılan bir Windows sanal makinesinin genelleştirilmiş bir görüntüsünü oluşturma](../../windows/capture-image-resource.md)
    -   [Azure 'da dağıtılan bir Linux VM 'sinin genelleştirilmiş bir görüntüsünü oluşturma](../../linux/capture-image.md)
3.  Paylaşılan görüntü galerisindeki SUSE ve Red Hat Linux görüntülerini kullanıyorsanız, paylaşılan görüntü galerisinde Linux satıcıları tarafından sunulan SAP görüntülerini kullanmanız gerekir.
4.  Microsoft destek sözleşmeleri için SAP destek gereksinimlerini karşıladığınızdan emin olun. Bkz. [sap destek notuna #2015553](https://launchpad.support.sap.com/#/notes/2015553). HANA büyük örnekleri için bkz. [ekleme gereksinimleri](./hana-onboarding-requirements.md).
4.  Doğru kişilerin [Planlı bakım bildirimleri](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) almasını sağlayıp en iyi şekilde seçim yapabilirsiniz.
5.  Dağıtımlarınız için uygulanabilecek yeni işlevsellik için [Kanal 9](https://channel9.msdn.com/) gibi kanallarda bulunan Azure sunularını sık sık kontrol edin.
6.  Yeni VM SKU 'Ları ve yeni desteklenen işletim sistemi ve DBMS sürümleri için [destek not #1928533](https://launchpad.support.sap.com/#/notes/1928533)gibi Azure Ile ilgili SAP notlarını kontrol edin. Yeni VM türlerinin fiyatlandırmasını daha eski VM türleriyle karşılaştırın, bu sayede VM 'Leri en iyi fiyat/performans oranıyla dağıtabilirsiniz.
7.  SAP destek notlarını, SAP HANA donanım dizinini ve SAP PAM 'yi yeniden denetleyin. Azure için desteklenen VM 'lerde, bu VM 'lerde desteklenen işletim sistemi yayınlarına ve desteklenen SAP ve DBMS yayınlarına hiçbir değişiklik olmadığından emin olun.
8.  Azure 'da yeni HANA sertifikalı SKU 'Lar için [SAP Web sitesini](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) denetleyin. Yeni SKU 'ların fiyatlandırmasını, kullanmak üzere planladığınız olanlarla karşılaştırın. Sonuç olarak, en iyi fiyat/performans oranına sahip olanları kullanmak için gerekli değişiklikleri yapın.
9.  Dağıtım betiklerinizi yeni VM türlerini kullanmak ve kullanmak istediğiniz yeni Azure özelliklerini birleştirmek için uyarlayın.
10. Altyapı dağıtımından sonra, sap destek notlarına [#500235](https://launchpad.support.sap.com/#/notes/500235) ve [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)göre SAP uygulama KATMANı VM 'Leri ve DBMS VM 'ler arasındaki ağ gecikmesini test edin ve değerlendirin. [Sap desteği not#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)ağ gecikmesi yönergelerine karşı sonuçları değerlendirin. Ağ gecikmesi orta veya iyi bir aralıkta olmalıdır. [Bu makalede](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)belgelendiği gibi, VM 'ler ve Hana büyük örnek birimleri arasındaki trafik için özel durumlar geçerlidir. [SAP iş yükleri Için Azure sanal MAKINELERI DBMS dağıtımı](./dbms_guide_general.md#azure-network-considerations) ve [Azure 'da altyapı yapılandırmalarının ve SAP HANA altyapı yapılandırmalarının ve işlemlerin](./hana-vm-operations.md) hiçbir şekilde belirtilmediği konusunda bahsedilen kısıtlamaların olmadığından emin olun.
11. [SAP uygulamalarıyla en iyi ağ gecikmesi için](sap-proximity-placement-scenarios.md), sanal makinelerinizin doğru [Azure yakınlık yerleşimi grubuna](../../co-location.md)dağıtıldığından emin olun.
11. İş yükünü uygulamadan önce kavram kanıtı aşaması için listelenen diğer tüm denetimleri gerçekleştirin.
12. İş yükü geçerli olduğundan, sistemlerin kaynak tüketimini Azure 'da kaydedin. Bu tüketimi eski platformunuzun kayıtlarıyla karşılaştırın. Büyük farklarınızın olduğunu görürseniz gelecek dağıtımların sanal makine boyutunu ayarlayın. Sanal makinelerin boyutunu, depolamayı ve ağ bant genişliğini azaltdığınızda da azaltabileceğinizi aklınızda bulundurun.
    - [Azure'daki Windows sanal makinesi boyutları](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure’da Linux sanal makine boyutları](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Sistem kopyalama işlevselliği ve işlemleriyle denemeler yapın. Amaç, bir geliştirme sistemini veya test sistemini kopyalamanızı kolaylaştırırken, proje ekiplerinin hızla yeni sistemler alabilir. 
14. Görevlerin ayrıldığınızdan emin olmak için takımınızın Azure rol tabanlı erişimini, izinlerini ve süreçlerini iyileştirin ve odaklanmak. Aynı zamanda, tüm takımların Azure altyapısında görevlerini gerçekleştirmesini sağlayın.
15. Personelinizin bu görevleri yürütmesini sağlamak için yüksek kullanılabilirlik ve olağanüstü durum kurtarma yordamlarını uygulayın, test edin ve belgeleyin. Eksiklikleri belirleyip dağıtımlarınızla Tümleştirdiğiniz yeni Azure işlevlerini uyarlayın.


## <a name="production-preparation-phase"></a>Üretim hazırlığı aşaması 
Bu aşamada, üretim dışı dağıtımlarınız sırasında karşılaştığınız ve öğrendiklerinizi toplayın ve gelecekteki üretim dağıtımlarına uygulayın. Ayrıca, geçerli barındırma konumunuz ile Azure arasında veri aktarımı işini hazırlamanız gerekir.

1.  Azure 'a geçmeden önce üretim sistemlerinizi gereken SAP sürüm yükseltmelerini doldurun.
1.  Üretim sisteminin geçişten sonra gerçekleştirilmesi gereken işlevsel ve iş testlerinde iş sahiplerini kabul edin.
1.  Bu testlerin geçerli barındırma konumundaki kaynak sistemlerle tamamlandığından emin olun. Sistem Azure 'a taşındıktan sonra testleri ilk kez yürütmemeye özen gösterin.
1.  Üretim sistemlerini Azure 'a geçirme sürecini test edin. Aynı zaman çerçevesinde tüm üretim sistemlerini Azure 'a taşıyorsanız, aynı barındırma konumunda olması gereken üretim sistemleri gruplarını oluşturun. Veri geçişini test edin. Yaygın olarak kullanılan bazı yöntemler şunlardır:
    - Yedekleme/geri yükleme gibi DBMS yöntemlerini SQL Server Always on, HANA sistem çoğaltması veya çekirdek hale getirmek ve Azure 'daki veritabanı içeriğini eşitleyecek şekilde kullanın.
    - Daha küçük veritabanları için yedekleme/geri yükleme kullanın.
    - Heterojen geçişler gerçekleştirmek için SAP SWPM ile tümleştirilmiş SAP geçiş Izleyicisini kullanın.
    - Geçişinizi bir SAP sürümü yükseltmesinde birleştirmeniz gerekiyorsa [SAP dmo](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) işlemini kullanın. Kaynak DBMS ve Hedef DBMS 'nin tüm birleşimlerinin desteklenmediğini göz önünde bulundurun. Farklı DMO sürümleri için belirli SAP destek notlarında daha fazla bilgi edinebilirsiniz. Örneğin, [2,0 SP04 Sum veritabanı geçiş seçeneği (dmo)](https://launchpad.support.sap.com/#/notes/2644872).
    - Yedeklemeleri veya SAP dışa aktarma dosyalarını taşımanız gerekebilmeniz halinde veri aktarımı aktarım hızının Internet üzerinden mi yoksa ExpressRoute aracılığıyla mı daha iyi olduğunu test edin. Verileri Internet üzerinden taşıyorsanız, gelecekteki üretim sistemlerinde yer almanız gereken bazı ağ güvenlik grubu/uygulama güvenlik grubu kurallarınızı değiştirmeniz gerekebilir.
1.  Sistemleri eski platformundan Azure 'a taşımadan önce kaynak tüketim verilerini toplayın. Faydalı veriler CPU kullanımı, depolama verimi ve ıOPS verileri içerir. Özellikle bu verileri DBMS katman birimlerinden toplayın, ancak aynı zamanda uygulama katmanı birimlerinden da toplayın. Ayrıca ağ ve depolama gecikmesini ölçer.
1.  SAP destek notlarını ve gereken işletim sistemi ayarlarını, SAP HANA donanım dizinini ve SAP PAM 'yi yeniden denetleyin. Azure için desteklenen VM 'lerde, bu VM 'lerdeki desteklenen işletim sistemi yayınlarına ve desteklenen SAP ve DBMS yayınlarına hiçbir değişiklik olmadığından emin olun.
1.  Dağıtım betiklerini, VM türlerinde ve Azure işlevselliğinde yaptığınız en son kararları hesaba katalacak şekilde güncelleştirin.
1.  Altyapı ve uygulamaları dağıttıktan sonra aşağıdakileri doğrulayın:
    - Doğru sanal makine türleri, doğru öznitelikler ve depolama boyutları ile dağıtıldı.
    - VM 'Ler doğru ve istenen işletim sistemi sürümleri ve düzeltme ekleriyle ve birdir.
    - VM 'Ler gerektiğinde ve tek bir şekilde sağlamlaştırılmış.
    - Doğru uygulama sürümleri ve düzeltme ekleri yüklendi ve dağıtıldı.
    - VM 'Ler Azure kullanılabilirlik kümelerine planlandı olarak dağıtıldı.
    - Azure Premium Depolama, gecikme süresine duyarlı diskler veya [% 99,9 tek VM SLA 'sinin](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) gerekli olduğu durumlarda kullanılır.
    - Azure Yazma Hızlandırıcısı doğru şekilde dağıtılır.
        - VM 'Ler, depolama alanları veya dizili kümeler içinde Yazma Hızlandırıcısı gereken diskler arasında doğru şekilde oluşturulduğundan emin olun.
        - [Linux 'ta yazılım RAID yapılandırmasını](/previous-versions/azure/virtual-machines/linux/configure-raid)denetleyin.
        - [Azure 'Da Linux VM 'lerinde LVM yapılandırmasını](/previous-versions/azure/virtual-machines/linux/configure-lvm)denetleyin.
    - [Azure yönetilen diskler](https://azure.microsoft.com/services/managed-disks/) yalnızca kullanılır.
    - VM 'Ler doğru kullanılabilirlik kümelerine ve Kullanılabilirlik Alanları dağıtıldı.
    - [Azure hızlandırılmış ağ](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) , SAP uygulama KATMANıNDA ve SAP DBMS katmanında kullanılan VM 'lerde etkindir.
    - SAP NetWeaver, Hybru veya S/4HANA 'ya dayalı sap sistemleri ve SAP sistemlerinin DBMS katmanı arasındaki iletişim yolunda hiçbir [Azure ağ sanal aygıtı](https://azure.microsoft.com/solutions/network-appliances/) yok.
    - Uygulama güvenlik grubu ve ağ güvenlik grubu kuralları, istenen ve planlanan şekilde iletişime izin verir ve gerektiğinde iletişimi engeller.
    - Zaman aşımı ayarları, daha önce açıklandığı gibi doğru şekilde ayarlanır.
    - VM 'Ler, [SAP uygulamalarıyla en iyi ağ gecikmesi Için Azure yakınlık yerleşimi gruplarında](sap-proximity-placement-scenarios.md)açıklandığı gibi doğru [Azure yakınlık yerleşimi grubuna](../../co-location.md)dağıtılır.
    - SAP uygulama katmanı VM 'Leri ve DBMS VM 'ler arasındaki ağ gecikmesi, SAP destek notları [#500235](https://launchpad.support.sap.com/#/notes/500235) ve [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)açıklandığı şekilde test edilir ve onaylanır. [Sap desteği not#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)ağ gecikmesi yönergelerine karşı sonuçları değerlendirin. Ağ gecikmesi orta veya iyi bir aralıkta olmalıdır. [Bu makalede](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)belgelendiği gibi, VM 'ler ve Hana büyük örnek birimleri arasındaki trafik için özel durumlar geçerlidir.
    - Şifreleme gerektiğinde ve uygun şifreleme yöntemiyle uygulandı.
    - Arabirimler ve diğer uygulamalar, yeni dağıtılan altyapıyı birbirine bağlayabilirler.
1.  Planlı Azure bakım için yeniden işlem yapmaya yönelik bir PlayBook oluşturun. Planlı bakım için sistemlerin ve VM 'Lerin yeniden başlatılması gereken sırayı belirleme.
    

## <a name="go-live-phase"></a>Go-Live aşaması
Go-Live aşamasında, önceki aşamalar sırasında geliştirmiş olduğunuz PlayBook 'ları izlediğinizden emin olun. Test ettiğiniz ve alıştırdığınız adımları yürütün. Yapılandırmalarda ve işlemlerde geçen dakikalık değişiklikleri kabul etmeyin. Ayrıca şu adımları uygulayın:

1. Azure portal izlemenin ve diğer izleme araçlarının çalıştığını doğrulayın. Windows için Windows performans Izleyicisi (PerfMon) ve Linux için çın ÖIB önerilir.
    - CPU sayaçları.
        - Ortalama CPU süresi, toplam (tüm CPU 'Lar)
        - Ortalama CPU süresi, her bir işlemci (M128 VM 'lerde 128 işlemci)
        - CPU çekirdek süresi, her ayrı işlemci
        - CPU Kullanıcı saati, her ayrı işlemci
    - Bellek.
        - Boş bellek
        - Bellek sayfası/saniye
        - Bellek sayfa çıkış/saniye
    - Dis.
        - Tek tek disk başına KB/sn cinsinden disk okuma
        - Disk okuma/saniye, ayrı disk başına
        - Tek tek disk başına mikrosaniye/okuma olarak disk okuma
        - Her disk için KB/sn cinsinden disk yazma
        - Disk yazma/saniye, ayrı disk başına
        - Disk başına mikrosaniye/okuma, tek bir diske yazma
    - Ağ.
        - /Saniye içinde ağ paketleri
        - Giden ağ paketi/saniye
        - Ağ KB/saniye
        - Ağ KB/sn
1.  Veri geçişten sonra, iş sahipleri ile üzerinde anlaştığınız tüm doğrulama testlerini gerçekleştirin. Doğrulama test sonuçlarını yalnızca özgün kaynak sistemleri için sonuçlara sahip olduğunuzda kabul edin.
1.  Arabirimlerin çalışıp çalışmadığını ve diğer uygulamaların yeni dağıtılan üretim sistemleriyle iletişim kurup kuramayacağını denetleyin.
1.  SAP işlem STMS aracılığıyla taşıma ve düzeltme sistemini denetleyin.
1.  Sistem üretime sunulduktan sonra veritabanı yedeklemeleri gerçekleştirin.
1.  Sistem üretime sunulduktan sonra SAP uygulama katmanı VM 'Leri için VM yedeklemeleri gerçekleştirin.
1.  Geçerli Go-Live aşamasının parçası olmayan ancak bu Go-Live aşamasında Azure 'a taşıdığınız SAP sistemleriyle iletişim kuran SAP sistemleri için SM51 içindeki konak adı arabelleğini sıfırlamanız gerekir. Bunun yapılması, Azure 'a taşıdığınız uygulama örneklerinin adlarıyla ilişkili eski önbelleğe alınmış IP adreslerini kaldırır.  


## <a name="post-production"></a>Üretim sonrası
Bu aşamada sistemin izlenmesi, çalışıyor ve yönetimi hakkında bilgi sağlanır. Bir SAP bakış noktasından, eski barındırma konumunuzda tamamlaması gereken olağan görevler geçerlidir. Bu Azure 'a özgü görevleri de doldurun:

1. Yüksek ücretlendirme sistemleri için Azure faturalarını gözden geçirin.
2. VM tarafında ve depolama tarafında fiyat/performans verimliliğini iyileştirin.
3. Sistemleri kapatmak için kullandığınız zamanları en uygun hale getirin.  


## <a name="next-steps"></a>Sonraki adımlar
Şu makalelere bakın:

- [SAP NetWeaver için Azure sanal makineleri planlama ve uygulama](./planning-guide.md)
- [SAP NetWeaver için Azure sanal makineler dağıtımı](./deployment-guide.md)
- [SAP iş yükleri için Azure sanal makineler DBMS dağıtımına yönelik konular](./dbms_guide_general.md)