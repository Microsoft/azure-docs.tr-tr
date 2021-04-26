---
title: "Azure üzerinde SAP: Azure VM 'Leri ile desteklenen senaryolar"
description: SAP iş yüküyle desteklenen Azure sanal makineler senaryoları
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 274db058f9f291d720fc350bb23f7bfdde2791e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670936"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure sanal makinesi üzerinde SAP iş yüküne yönelik desteklenen senaryolar
Azure 'da SAP NetWeaver, Business One `Hybris` veya S/4HANA sistemleri mimarisi tasarlamak, ölçeklenebilir, verimli ve yüksek oranda kullanılabilir bir dağıtıma ulaşmak için kullanabileceğiniz çeşitli mimariler ve araçlar için çok sayıda farklı fırsat açar. Kullanılan işletim sistemine veya DBMS 'ye bağımlı olsa da kısıtlamalar vardır. Ayrıca, şirket içinde desteklenen tüm senaryolar Azure 'da aynı şekilde desteklenmez. Bu belge, Azure VM 'Leri kullanan desteklenen yüksek kullanılabilirliğe sahip olmayan yapılandırmaların ve yüksek kullanılabilirliğe sahip yapılandırmaların ve mimarilerin oluşmasına yol açacaktır. [Hana büyük örneklerle](./hana-overview-architecture.md)desteklenen senaryolar IÇIN, [Hana büyük örnekler için desteklenen senaryolar](./hana-supported-scenario.md)makalesine bakın. 


## <a name="2-tier-configuration"></a>2 katmanlı yapılandırma
SAP 2 katmanlı yapılandırmanın, SAP DBMS ve aynı sunucu veya VM birimi üzerinde çalışan uygulama katmanının birleştirilmiş bir katmanından oluşturulması kabul edilir. İkinci katman, Kullanıcı arabirimi katmanı olarak kabul edilir. 2 katmanlı bir yapılandırma söz konusu olduğunda, DBMS ve SAP uygulama katmanı, Azure VM 'nin kaynaklarını paylaşır. Sonuç olarak, farklı bileşenleri kaynaklar için rekabet altına almak için bir şekilde yapılandırmanız gerekir. Ayrıca, VM kaynaklarının aşırı abone olunmamasına dikkat etmeniz gerekir. Bu tür bir yapılandırma, dahil edilen farklı Azure bileşenlerinin [Azure hizmet düzeyi anlaşmalarının](https://azure.microsoft.com/support/legal/sla/) ötesinde yüksek kullanılabilirlik sağlamaz.

Böyle bir yapılandırmanın grafik gösterimi şöyle görünebilir:

![Basit 2 katmanlı yapılandırma](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Bu tür yapılandırmalarda Windows, Red hat, SUSE ve Oracle Linux, üretim ve üretim dışı durumlar için SQL Server, Oracle, DB2, maxDB ve SAP Ao 'un DBMS sistemleri için desteklenir. DBMS olarak SAP HANA için, bu tür bir yapılandırma yalnızca üretim dışı durumlar için desteklenir. Bu, [Azure Hana büyük örneklerinin](./hana-overview-architecture.md) dağıtım durumunu da içerir.
Azure 'da desteklenen tüm işletim sistemi/DBMS birleşimleri için, bu tür yapılandırma desteklenir. Ancak, DBMS ve SAP bileşenlerinin yapılandırılmasını, DBMS ve SAP bileşenlerinin bellek ve CPU kaynakları için rekabet etme ve böylece fiziksel olarak kullanılabilir kaynakları aşmayacak şekilde ayarlamanız zorunludur. Bu, DBMS 'nin ayrılmasına izin verilen belleği kısıtlayarak yapılmalıdır. Ayrıca, uygulama örneklerinde SAP Genişletilmiş belleğini de sınırlamanız gerekir. Ayrıca, bileşenlerin CPU kaynaklarını en üst düzeye çıkarmadığından emin olmak için VM 'nin CPU tüketimini de izlemeniz gerekir. 

> [!NOTE]
> Üretim SAP sistemlerinde, bu belgenin ilerleyen kısımlarında açıklandığı gibi daha yüksek kullanılabilirlik ve nihai olağanüstü durum kurtarma yapılandırmalarının kullanılması önerilir


## <a name="3-tier-configuration"></a>3 katmanlı yapılandırma
Bu tür yapılandırmalarda SAP uygulama katmanını ve DBMS katmanını farklı VM 'Lere ayırabilirsiniz. Genellikle daha büyük sistemler ve SAP uygulama katmanının kaynakları üzerinde daha esnek olma nedenleri vardır. En basit kurulumda, dahil edilen farklı Azure bileşenlerinin [Azure hizmet düzeyi anlaşmalarının](https://azure.microsoft.com/support/legal/sla/) ötesinde yüksek kullanılabilirlik yoktur. 

Grafik gösterimi şöyle görünür:

![Basit 3 katmanlı bir yapılandırma gösteren diyagram.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Bu tür bir yapılandırma Windows, Red hat, SUSE ve Oracle Linux ' de desteklenir ve üretim ve üretim dışı durumlar için SQL Server, Oracle, DB2, SAP HANA, maxDB ve SAP Ao 'un DBMS sistemleri için de kullanılır. Bu, [Azure Hana büyük örnekleri](./hana-overview-architecture.md)için varsayılan dağıtım yapılandırmadır. Basitme için SAP uygulama katmanındaki SAP Merkezi Hizmetleri ve SAP iletişim örnekleri arasında ayrım yapmadınız. Bu basit 3 katmanlı yapılandırmada SAP Merkezi Hizmetleri için yüksek kullanılabilirlik koruması yoktur.

> [!NOTE]
> Üretim SAP sistemlerinde, bu belgenin ilerleyen kısımlarında açıklandığı gibi daha yüksek kullanılabilirlik ve nihai olağanüstü durum kurtarma yapılandırmalarının kullanılması önerilir


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>VM veya HANA büyük örnek birimi başına birden çok DBMS örneği
Bu yapılandırma türünde, Azure VM veya HANA büyük örnek birimi başına birden çok DBMS örneği barındırabilirsiniz. Mosyon, bakım yapmak için daha az işletim sistemine sahip olabilir ve bu da düşük maliyetleri azaltır. Daha büyük bir VM veya HANA büyük örnek birimi kaynaklarını birden çok DBMS örneği arasında paylaşarak daha fazla esneklik ve daha verimlilik elde edebilirsiniz. Bu yapılandırmaların şimdiye kadar, üretim dışı sistemler için büyük ölçüde gösterilmıştı.

Benzer bir yapılandırma şöyle görünebilir:

![Bir birimde birden çok DBMS örneği](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Bu tür DBMS dağıtımı için desteklenir:

- Windows üzerinde SQL Server
- IBM DB2. Makalede [birden çok örnek (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html) ayrıntılarını bulma
- Oracle için. Ayrıntılar için bkz. [sap destek notu #1778431](https://launchpad.support.sap.com/#/notes/1778431) ve ilgili SAP notları
- SAP HANA için, bir VM 'de birden çok örnek, SAP bu dağıtım yöntemini (MCOS) çağırır, desteklenir. Ayrıntılar için bkz. SAP makalesi [bir konaktaki birden çok SAP HANA sistemi (MCOS)] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Birden çok veritabanı örneğini bir konakta çalıştırırken, farklı örneklerin kaynaklar için rekabet olmadığından ve bu nedenle VM 'nin fiziksel kaynak sınırlarını aşmadığından emin olmanız gerekir. Bu durum özellikle, VM 'yi paylaşan örnek sayısını ayırabilmeniz gereken bellek için geçerlidir. Bu Ayrıca, farklı veritabanı örneklerinin faydalanma CPU kaynakları için de doğru olabilir. Bahsedilen tüm DBMS 'nin, bir örnek düzeyinde bellek ayırmayı ve CPU kaynaklarını sınırlandırmaya izin veren yapılandırmalara sahip olması gerekir.
Azure VM 'Leri için bu tür bir yapılandırmaya yönelik destek sağlamak üzere, veriler için kullanılan disklerin veya birimlerin, farklı örnekler tarafından yönetilen veritabanlarının günlük/yineleme günlüğü dosyaları birbirinden ayrı olması beklenmektedir. Diğer bir deyişle, farklı DBMS örneği tarafından yönetilen veritabanlarının veri veya günlük/yineleme günlüğü dosyalarını aynı disklerin veya birimlerin paylaşılması gerekmez. 

HANA büyük örneklerine yönelik disk yapılandırması, yapılandırılmış ve [Hana büyük örnekleri Için desteklenen senaryolarda](./hana-supported-scenario.md#single-node-mcos)ayrıntılıdır. 

> [!NOTE]
> Üretim SAP sistemlerinde, bu belgenin ilerleyen kısımlarında açıklandığı gibi, daha yüksek kullanılabilirlik ve nihai olağanüstü durum kurtarma yapılandırmalarının kullanılması önerilir. Birden çok DBMS örneğine sahip VM 'Ler, bu belgede daha sonra açıklanan yüksek kullanılabilirlik yapılandırmalarında desteklenmez.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Bir VM 'de birden çok SAP Iletişim kutusu örneği
Birçok durumda, çıplak sunucularda veya özel bulutlarda çalışan VM 'lerde bile birden çok iletişim kutusu örneği dağıtılır. Bu tür yapılandırmaların nedeni belirli SAP iletişim örneklerinin belirli iş yüküne, iş işlevlerine veya iş yükü türlerine uyarlamalarıdır. Bu örneklerin ayrı sanal makinelere yalılamamasının nedeni, işletim sistemi bakım ve işlemlerinin çabasıyla karşılaşmaktır. Ya da birçok durumda VM 'nin barındırıcı ya da operatörü, sanal makine işletilerek yönetici başına aylık bir ücret ister. Azure 'da, Windows, Red hat, SUSE ve Oracle Linux işletim sistemlerinde üretim ve üretim dışı amaçlar için desteklenen tek bir VM 'de birden çok SAP iletişim kutusu örneği barındırma senaryosu. Tek bir VM 'de birden çok SAP uygulama sunucusu örneği çalışıyorsa, Windows ve modern Linux kernels ' de bulunan PHYS_MEMSIZE SAP Kernel parametresi ayarlanmalıdır. Ayrıca, SAP Genişletilmiş belleğinin otomatik büyümesinin uygulandığı Windows gibi işletim sistemlerinde SAP Genişletilmiş bellek genişletmesinin sınırlandırgetirilmesi önerilir. Bu, SAP profili parametresiyle yapılabilir `em/max_size_MB` .

Azure VM 'lerinde birden çok SAP iletişim örneğinin çalıştırıldığı 3 katmanlı bir yapılandırmada şöyle görünebilirler:

![Azure VM 'lerinde birden çok SAP iletişim örneğinin çalıştırıldığı 3 katmanlı bir yapılandırmayı gösteren diyagram.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Basitme için SAP uygulama katmanındaki SAP Merkezi Hizmetleri ve SAP iletişim örnekleri arasında ayrım yapmadınız. Bu basit 3 katmanlı yapılandırmada SAP Merkezi Hizmetleri için yüksek kullanılabilirlik koruması yoktur. Üretim sistemleri için SAP Merkezi Hizmetleri korumasız olarak ayrılmamanız önerilmez. Daha fazla bilgi için, SAP merkezi örnekleri etrafında çok düzeyli yapılandırmaların ve bu tür çok düzeyli yapılandırmaların yüksek kullanılabilirliğine yönelik bilgiler için, bu belgenin sonraki bölümlerine bakın.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>SAP DBMS katmanı için yüksek kullanılabilirlik koruması
SAP üretim sistemlerini dağıttığımızda, yüksek kullanılabilirlik yapılandırmalarının etkin bekleme türünü göz önünde bulundurmanız gerekir. Özellikle, verilerin tam performans ve ölçeklenebilirlik sağlayabilmesi için belleğe yüklenmesi gereken SAP HANA, Azure hizmeti düzeltme, yüksek kullanılabilirlik için ideal bir ölçüdür. 

Genel olarak, docs.microsoft.com ' deki SAP iş yükü bölümünde belirtilen yüksek kullanılabilirlik yapılandırmalarının ve yazılım paketlerinin genel olarak kullanılmasını destekler. SAP Note [#1928533](https://launchpad.support.sap.com/#/notes/1928533)aynı ifadeyi okuyabilirsiniz. Microsoft, SAP iş yüküyle birlikte Microsoft tarafından belgelenmeyen diğer yüksek kullanılabilirliğe sahip üçüncü taraf yazılım çerçeveleri için destek sağlamaz. Bu gibi durumlarda, yüksek kullanılabilirlik çerçevesinin üçüncü taraf tedarikçisidir, destek sürecine bir müşteri olarak sizin için ihtiyaç duyan yüksek kullanılabilirliğe sahip yapılandırmaya yönelik destek tarafdır. Özel durumlara bu makalede değineceğiz. 

Genel olarak, Azure VM 'lerinde veya HANA büyük örnekler birimlerinde sınırlı bir yüksek kullanılabilirlik yapılandırması kümesini destekler. HANA büyük örneklerinin desteklenen senaryolarında, [Hana büyük örnekler Için desteklenen belge senaryolarını](./hana-supported-scenario.md)okuyun.

Azure VM 'Ler için, aşağıdaki yüksek kullanılabilirlik yapılandırması DBMS düzeyinde desteklenir:

- SUSE ve Red hat üzerinde Linux pacemaker tabanlı sistem çoğaltmasını SAP HANA. Ayrıntılı makalelere göz atın:
    - [SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği](./sap-hana-high-availability.md)
    - [Red Hat Enterprise Linux üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği](./sap-hana-high-availability-rhel.md)
- SUSE ve Red hat üzerinde [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) kullanarak genişleme n + b yapılandırma SAP HANA. Ayrıntılar aşağıdaki makalelerde listelenmiştir:
    - [SUSE Linux Enterprise Server} üzerinde Azure NetApp Files kullanarak Azure VM 'lerinde bekleme düğümüne sahip bir SAP HANA genişleme sistemi dağıtma](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Red Hat Enterprise Linux Azure NetApp Files kullanarak Azure VM 'lerinde bekleme düğümüne sahip bir SAP HANA genişleme sistemi dağıtma](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- Windows Scale-Out Dosya Hizmetleri tabanlı yük devretme kümesi SQL Server. Üretim sistemlerine yönelik öneri, kümeleme yerine SQL Server her zaman açık olarak kullanmaktır. SQL Server her zaman açık, ayrı depolama kullanarak daha iyi kullanılabilirlik sağlar. Ayrıntılar Bu makalede açıklanmıştır: 
    - [Azure sanal makinelerinde SQL Server yük devretme kümesi örneği yapılandırma](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server her zaman açık, Azure 'da SQL Server için Windows işletim sistemi ile desteklenir. Bu, Azure 'daki üretim SQL Server örnekleri için varsayılan önerimiz olur. Ayrıntılar aşağıdaki makalelerde açıklanmıştır:
    - [Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grupları Ile tanışın](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
    - [Azure sanal makinelerinde farklı bölgelerde her zaman açık kullanılabilirlik grubu yapılandırın](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
    - [Azure 'Da Always on kullanılabilirlik grubu için bir yük dengeleyici yapılandırın](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).
- Windows için Oracle Data Guard ve Oracle Linux. Oracle Linux ayrıntılarını bu makalede bulabilirsiniz:
    - [Azure Linux sanal makinesinde Oracle Data Guard 'ı uygulama](../oracle/configure-oracle-dataguard.md)
- SUSE ve RHEL için SUSE ve RHEL ayrıntılı belgelerindeki IBM DB2 HADR, burada pacemaker kullanılarak sunulmaktadır:
    - [Pacemaker ile SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde IBM DB2 LUW 'ın yüksek kullanılabilirliği](./dbms-guide-ha-ibm.md)
    - [Red Hat Enterprise Linux Server üzerinde Azure VM’lerindeki IBM Db2 LUW’a yönelik yüksek kullanılabilirlik](./high-availability-guide-rhel-ibm-db2-luw.md)
- SAP Ao ve SAP maxDB yapılandırması, bu belgelerde ayrıntılı olarak:
    - [SAP iş yükü için SAP ASE Azure Sanal Makineler DBMS dağıtımı](./dbms_guide_sapase.md)
    - [Azure VM 'lerinde SAP MaxDB, liveCache ve Content Server dağıtımı](./dbms_guide_maxdb.md)
- HANA büyük örnek yüksek kullanılabilirlik senaryoları şu şekilde ayrıntılıdır:
    - [HANA büyük örnekler için desteklenen senaryolar-yüksek kullanılabilirlik için STONITH ile HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [HANA büyük örnekler için desteklenen senaryolar-konak otomatik yük devretme (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> Yukarıda açıklanan senaryolardan hiçbiri için, bir VM 'de birden çok DBMS örneğinin yapılandırmasını destekliyoruz. Her durumda, sanal makine başına yalnızca bir veritabanı örneği dağıtılabilir ve açıklanan yüksek kullanılabilirlik yöntemleriyle korunabilir. Aynı Windows veya pacemaker yük devretme kümesi altında birden çok DBMS örneğinin korunması bu **noktada desteklenmez.** Ayrıca, Oracle Data Guard yalnızca VM dağıtım durumları başına tek bir örnek için desteklenir. 

Çeşitli veritabanı sistemleri, tek bir DBMS örneği altında birden çok veritabanını barındırmanıza olanak tanır. SAP HANA durumda olduğu gibi, birden çok veritabanı birden çok veritabanı kapsayıcısında (MDC) barındırılabilir. Bu çok veritabanı yapılandırmalarının tek bir yük devretme kümesi kaynağı içinde çalıştığı durumlarda, bu konfigürasyonlar desteklenir. Desteklenmeyen yapılandırmalarda birden çok küme kaynağı olması gereken durumlar vardır. Birden çok SQL Server kullanılabilirlik grubu tanımladığınız yapılandırmalarda olduğu gibi, bir SQL Server örneği altında.


![DBMS HA yapılandırması](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

DBMS bir/veya işletim sistemine bağlı olarak, Azure yük dengeleyici gibi bileşenler çözüm mimarisinin bir parçası olarak gerekli olabilir veya olmayabilir. 

Özellikle maxDB için depolama yapılandırmasının farklı olması gerekir. MaxDB 'de, yüksek kullanılabilirliğe sahip yapılandırmalara yönelik verilerin ve günlük dosyalarının paylaşılan depolamada bulunması gerekir. Yalnızca maxDB söz konusu olduğunda, paylaşılan depolama yüksek kullanılabilirlik için desteklenir. Diğer tüm DBMS için, düğüm başına ayrı depolama yığınları yalnızca desteklenen disk yapılandırmalarıdır.

Diğer yüksek kullanılabilirlik çerçevelerinin mevcut olduğu bilinmektedir ve Microsoft Azure de çalıştırılmak üzere bilinmektedir. Ancak, Microsoft bu çerçeveleri test etmedi. Yüksek kullanılabilirlik yapılandırmanızı bu çerçevelerle derlemek istiyorsanız, bu yazılımın sağlayıcısıyla birlikte çalışmanız gerekir:

- Dağıtım mimarisi geliştirme
- Mimarinin dağıtımı
- Mimari desteği

> [!IMPORTANT]
> Microsoft Azure Market, Azure yerel depolama 'nın en üstünde depolama çözümleri sağlayan çeşitli yazılım gereçlerini sunmaktadır. Bu yazılım gereçleri, NFS paylaşımları oluşturmak için kullanılabilir ve ayrıca, bir bekleme düğümünün gerekli olduğu genişleme dağıtımlarında SAP HANA teorik olarak kullanılabilir. Çeşitli nedenlerden dolayı, Azure 'da Microsoft ve SAP tarafından gerçekleştirilen hiçbir DBMS dağıtımı için bu depolama yazılım gereçlerinden hiçbiri desteklenmez. Bu noktada, SMB paylaşımlarında DBMS dağıtımları desteklenmez. NFS paylaşımlarındaki DBMS dağıtımları [Azure NetApp FILES](https://azure.microsoft.com/services/netapp/)NFS 4,1 paylaşımlarıyla sınırlıdır.


## <a name="high-availability-for-sap-central-service"></a>SAP Merkezi hizmeti için yüksek kullanılabilirlik
SAP Merkezi Hizmetleri, SAP yapılandırmanızın ikinci bir tek hata noktasıdır. Sonuç olarak, bu merkezi hizmet süreçlerini de korumanız gerekir. SAP iş yükü için desteklenen ve belgelenen teklif şöyle okur:

- Sapmnt ve küresel aktarım dizini için Windows genişleme dosya hizmetleri 'ni kullanan Windows Yük devretme kümesi sunucusu. Ayrıntılar makalesinde açıklanmaktadır:
    - [Azure 'da bir dosya paylaşma kullanarak bir Windows Yük devretme kümesinde SAP yoks/SCS örneği oluşturma](./sap-high-availability-guide-wsfc-file-share.md)
    - [SAP Ass/SCS örnekleri için bir Windows Yük devretme kümesi ve dosya paylaşma kullanarak SAP yüksek kullanılabilirlik için Azure altyapısını hazırlama](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Sapmnt ve genel aktarım dizini için [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) tabanlı SMB paylaşımının kullanıldığı Windows Yük devretme kümesi sunucusu. Ayrıntılar makalesinde listelenmiştir:
    - [SAP uygulamaları için Azure NetApp Files (SMB) ile Windows üzerinde Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](./high-availability-guide-windows-netapp-files-smb.md)
- Windows Yük devretme kümesi sunucusu, SIOS 'yi temel alır `Datakeeper` . Microsoft tarafından belgelense de, bu çözüm kullanılırken SIOS desteğiyle birlikte çalışmak için, SIOS ile bir destek ilişkisine ihtiyacınız vardır. Ayrıntılar makalesinde açıklanmaktadır:
    - [Azure 'da küme paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [SAP Ass/SCS için bir Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını hazırlama](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- SUSE işletim sisteminde, iki SUSE VM kullanarak ve dosya çoğaltma için yüksek düzeyde kullanılabilir bir NFS paylaşımıyla oluşturma ile Pacemaker `drdb` . Ayrıntılar makalede belgelenmiştir
    - [SAP uygulamaları için SUSE Linux Enterprise Server Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](./high-availability-guide-suse.md)
    - [SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde NFS için yüksek kullanılabilirlik](./high-availability-guide-suse-nfs.md)
- Pacemaker SUSE işletim sistemi [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)tarafından sunulan NFS paylaşımlarına sahiptir. Ayrıntılar şu şekilde belgelenmiştir
    - [SAP uygulamaları için Azure NetApp Files SUSE Linux Enterprise Server üzerindeki Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](./high-availability-guide-suse-netapp-files.md)
- Bir kümede barındırılan NFS paylaşımıyla Red Hat işletim sisteminde pacemaker `glusterfs` . Makalelerde Ayrıntılar bulunabilir
    - [Red Hat Enterprise Linux SAP NetWeaver için Azure sanal makineleri yüksek kullanılabilirliği](./high-availability-guide-rhel.md)
    - [`GlusterFS` Azure VM 'lerde Red Hat Enterprise Linux for SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)barındırılan NFS paylaşımıyla Red Hat işletim sisteminde pacemaker. Ayrıntılar makalesinde açıklanmaktadır
    - [SAP NetWeaver için Azure sanal makineler yüksek kullanılabilirlik, SAP uygulamaları için Azure NetApp Files Red Hat Enterprise Linux](./high-availability-guide-rhel-netapp-files.md)

Listelenen çözümlerde, `Datakeeper` ürünü desteklemek ve doğrudan sorunlar durumunda Sios ile iletişim kurmak için, Sios ile bir destek ilişkisine ihtiyacınız vardır. Windows, Red Hat ve/veya SUSE işletim sisteminin lisanslanma yöntemine bağlı olarak, listelenen yüksek kullanılabilirliğe sahip yapılandırmalara tam destek sağlamak için işletim sistemi sağlayıcınıza sahip bir destek sözleşmeniz de gerekebilir.

Yapılandırma şöyle de gösterilebilir:

![DBMS ve ASCS HA yapılandırması](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Grafiklerin sağ tarafında, yüksek oranda kullanılabilir SAP Merkezi Hizmetleri gösterilir. Bir sorun olması durumunda yük devretme kümesi çerçevesiyle korunan SAP merkezi hizmetlerinin yanı sıra, yüksek oranda kullanılabilir bir NFS veya SMB paylaşımında bir zorunludur, sapmnt ve genel aktarım dizininin tek bir VM 'nin varlığına bağımsız olarak kullanılabilir olduğundan emin olmak için bir Windows paylaşılan diski vardır. Windows Yük devretme kümesi sunucusu ve Paceyapıcısı gibi bazı çözümler, trafiği sağlıklı bir düğüme yönlendirmek veya yeniden yönlendirmek için bir Azure Yük Dengeleyici gerektirir.

Gösterilen listede, Oracle Linux işletim sisteminin bir açıklaması yoktur. Oracle Linux, bir küme çerçevesi olarak pacemaker 'ı desteklemez. SAP sisteminizi Oracle Linux dağıtmak istiyorsanız ve Oracle Linux için yüksek kullanılabilirlik çerçevesine ihtiyacınız varsa, üçüncü taraf tedarikçilerle çalışmanız gerekir. Tedarikçilerden biri, Azure 'da SAP tarafından desteklenen Linux için koruma paketiyle birlikte SIOS 'dir. Daha fazla bilgi için bkz. SAP Note #1662610-daha fazla bilgi için bkz. [Linux IÇIN SIOS koruma paketi destek ayrıntıları](https://launchpad.support.sap.com/#/notes/1662610) .



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Yukarıda listelenen SAP Merkezi Hizmetleri senaryolarında desteklenen depolama alanı
Azure Depolama türlerinin yalnızca bir alt kümesi yüksek oranda kullanılabilir NFS veya SAP Merkezi Hizmetleri küme senaryolarımızda kullanım için kalite sağlayan SMB paylaşımları sağladığından desteklenen depolama türlerinin bir listesi olduğundan

- Windows genişleme dosya sunucusu ile Windows Yük devretme kümesi sunucusu, Azure NetApp Files hariç olmak üzere tüm yerel Azure depolama türlerine dağıtılabilir. Bununla birlikte, verimlilik ve ıOPS 'nin üstün hizmet düzeyi anlaşmaları nedeniyle Premium depolamanın yararlanması önerilir.
- Azure NetApp Files SMB içeren Windows Yük devretme kümesi sunucusu Azure NetApp Files destekleniyor. Azure dosya hizmetlerindeki SMB paylaşımları bu noktada **desteklenmez.**
- SIOS tabanlı Windows paylaşılan diski olan Windows Yük devretme kümesi sunucusu `Datakeeper` , Azure NetApp Files hariç tüm yerel Azure depolama türlerine dağıtılabilir. Bununla birlikte, verimlilik ve ıOPS 'nin üstün hizmet düzeyi anlaşmaları nedeniyle Premium depolamanın yararlanması önerilir.
- Azure NetApp Files üzerindeki NFS paylaşımlarını kullanan SUSE veya Red Hat Paceyapıcısı Azure NetApp Files desteklenir. 
- İki VM arasındaki yapılandırmayı kullanan SUSE pacemaker `drdb` , Azure NetApp Files hariç yerel Azure Depolama türleri kullanılarak desteklenir. Bununla birlikte, verimlilik ve ıOPS 'nin üstün hizmet düzeyi anlaşmaları nedeniyle Premium depolamanın yararlanması önerilir.
- NFS paylaşımının sağlanması için kullanılan Red Hat pacemaker `glusterfs` , Azure NetApp Files hariç yerel Azure Depolama türleri kullanılarak desteklenir. Bununla birlikte, verimlilik ve ıOPS 'nin üstün hizmet düzeyi anlaşmaları nedeniyle Premium depolamanın yararlanması önerilir.

> [!IMPORTANT]
> Microsoft Azure Market, Azure yerel depolama 'nın en üstünde depolama çözümleri sağlayan çeşitli yazılım gereçlerini sunmaktadır. Bu yazılım araçları, NFS veya SMB paylaşımları oluşturmak için kullanılabilir ve ayrıca teorik olarak yük devretme kümelenmiş SAP merkezi hizmetlerinde de kullanılabilir. Bu çözümler, Microsoft tarafından SAP iş yükü için doğrudan desteklenmez. NFS veya SMB paylaşımınızı oluşturmak için böyle bir çözüm kullanmaya karar verirseniz, SAP Merkezi hizmeti yapılandırma desteğinin, depolama yumuşak gereci 'nda yazılımın sahibi olan üçüncü taraf tarafından sağlanması gerekir.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Çoklu SID SAP Merkezi Hizmetler yük devretme kümeleri
Yük devretme kümesi yapılandırmasında, SAP 'nin büyük SAP lanlarında gereken sanal makine sayısını azaltmak için SAP Merkezi Hizmetleri örneklerini birden çok farklı SAP sisteminin çalıştırmasına izin verir. 30 veya daha fazla NetWeaver veya S/4HANA üretim sistemine sahip olduğunuz durumları düşünün. Birden çok SID Kümelemesi olmadan, bu yapılandırmaların 30 veya daha fazla Windows veya pacemaker yük devretme kümesi yapılandırmasında 60 veya daha fazla VM olması gerekir. DBMS yük devretme kümelerinin yanı sıra, gereklidir. Yük devretme kümesi yapılandırmasındaki iki düğüm arasında birden fazla SAP Merkezi hizmeti dağıtmak, VM 'lerin sayısını önemli ölçüde azaltabilir. Ancak, tek bir iki düğümlü küme yapılandırmasında birden fazla SAP merkezi hizmet örneğinin dağıtımı da bazı dezavantajlara sahiptir. Küme yapılandırmasındaki tek bir VM 'nin etrafındaki sorunlar birden çok SAP sistemine uygulanır. Küme yapılandırmasında çalışan konuk IŞLETIM sistemindeki bakım, birden çok üretim SAP sistemi etkilendiğinden daha fazla koordinasyon gerektirir. SAP 'yi işleme gibi araçlar, sistem kopyalama sürecinde çok SID Kümelemesi desteklememektedir.

Azure 'da, Windows işletim sistemi için ENSA1 ve ENSA2 ile bir çoklu SID küme yapılandırması desteklenir. Öneri, eski sıraya alma çoğaltma hizmeti mimarisini (ENSA1) bir çoklu SID kümesinde yeni mimari (ENSA2) ile birleştirmemelidir. Bu tür bir mimari hakkındaki ayrıntılar makalelerde belgelenmiştir

- [SAP ASCS/SCS örneği Windows Server Yük Devretme Kümelemesi ve paylaşılan disk ile Azure üzerinde çok düzeyli yüksek kullanılabilirlik](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [SAP ASCS/SCS örneği Windows Server Yük Devretme Kümelemesi ve dosya paylaşımıyla Azure 'da yüksek oranda kullanılabilirlik](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

SUSE için, pacemaker tabanlı çok düzeyli bir küme de desteklenir. Şu ana kadar yapılandırma desteklenir:

- En fazla beş SAP ASCS/SCS örneği
- Eski sıraya alma çoğaltma sunucusu buz mimarisi (ENSA1)
- İki düğümlü Paceyapıcısı küme yapılandırması

Yapılandırma, [SAP uygulamaları için SUSE Linux Enterprise Server Çoklu SID Kılavuzu ' nda Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirlik](./high-availability-guide-suse-multi-sid.md) olarak belgelenmiştir

Sıraya alma çoğaltması sunucusu olan bir çoklu SID kümesi, şema gibi

![Sıraya alma çoğaltması sunucusuyla çok düzeyli bir kümeyi gösteren diyagram.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA genişleme senaryoları
SAP HANA ölçek genişletme senaryoları, [SAP HANA donanım dizininde](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)listelenen Hana sertifikalı Azure VM 'lerinin bir alt kümesi için desteklenir. ' Kümeleme ' sütununda ' Yes ' ile işaretlenen tüm VM 'Ler OLAP veya S/4HANA ölçeği için kullanılabilir. Bekleme olmadan yapılandırma, Azure Depolama türleri ile desteklenir: 

- /Hana/log birimi için Azure Write Hızlandırıcısı dahil olmak üzere Azure Premium Depolama
- [Ultra disk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Bekleyen düğüm (ler) ile OLAP veya S/4HANA için genişleme yapılandırmalarının SAP HANA, yalnızca Azure NetApp Files üzerinde barındırılan NFS ile desteklenir.

Bekleyen düğümü olan veya olmadan tam depolama yapılandırması hakkında daha fazla bilgi için, makalelere göz atın:

- [SAP HANA Azure sanal makine depolama alanı yapılandırmaları](./hana-vm-operations-storage.md) 
- [SUSE Linux Enterprise Server Azure NetApp Files kullanarak Azure VM 'lerinde bekleme düğümüne sahip bir SAP HANA genişleme sistemi dağıtma](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Red Hat Enterprise Linux Azure NetApp Files kullanarak Azure VM 'lerinde bekleme düğümüne sahip bir SAP HANA genişleme sistemi dağıtma](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP destek notunun #2080991](https://launchpad.support.sap.com/#/notes/2080991)

HANA büyük örnekleri desteklenen HANA genişleme yapılandırmalarının ayrıntıları için aşağıdaki belgeler geçerlidir:

- [Hazırda bekleme ile büyük örnek ölçeği genişletme için desteklenen senaryolar](./hana-supported-scenario.md#scale-out-with-standby)
- [Hazır olmayan HANA büyük örnekler için desteklenen senaryolar](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Olağanüstü durum kurtarma senaryosu
Desteklenen çeşitli olağanüstü durum kurtarma senaryoları vardır. Tüm Azure bölgelerinin kılavuzdan sonra dengeleyecekse, olağanüstü durum mimarilerini mimarilere göre tanımladık. Bu, olağanüstü durum kurtarma hedefi 'nin SAP manzara 'yi çalıştırmak için hedef olarak farklı bir Azure bölgesi olması gerektiği anlamına gelir. DBMS katmanındaki ve DBMS olmayan katmandaki yöntemleri ve konfigürasyonları ayırdık. 

### <a name="dbms-layer"></a>DBMS katmanı
DBMS katmanı için, her zaman açık, Oracle Data Guard, DB2 HADR, SAP ASE her zaman açık veya HANA sistem çoğaltması gibi DBMS yerel çoğaltma mekanizmalarını kullanan yapılandırmalarda yapılandırma desteklenir. Tek bir Azure bölgesi içinde dağıtılan tipik yüksek kullanılabilirlik senaryolarında olduğu gibi, bu gibi durumlarda çoğaltma akışının zaman uyumsuz olması zorunludur. Bu tür desteklenen bir DBMS olağanüstü durum kurtarma yapılandırmasına örnek olarak, [Azure bölgelerindeki SAP HANA kullanılabilirliği](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions)makalesinde açıklanmaktadır. Bu bölümdeki ikinci grafik, örnek olarak HANA ile bir senaryoyu açıklar. SAP uygulamaları için desteklenen ana veritabanlarının hepsi böyle bir senaryoya dağıtılabilir.

VM 'nin tam iş yükü trafiğine karşılaşmadığından, olağanüstü durum kurtarma bölgesinde daha küçük bir VM 'yi hedef örnek olarak kullanmak desteklenir. Bunu yaptığınızda, aşağıdaki noktaları göz önünde bulundurmanız gerekir:

- Daha küçük VM türleri, daha küçük VM 'Lere bağlı olan çok sayıda diske izin vermez
- Daha küçük VM 'Ler daha az ağ ve depolama aktarım hızına sahiptir
- VM aileleri arasında yeniden boyutlandırma, farklı VM 'Ler tek bir Azure kullanılabilirlik kümesinde toplandığında veya M serisi ailesi ile Mv2 ailesi sanal makineleri arasında yeniden boyutlandırma gerçekleşiyorsa bir sorun olabilir
- Veritabanı örneği için CPU ve bellek tüketimi, bu değişiklikleri verilere en az gecikmeyle uygulamak üzere en az gecikmeyle ve yeterli CPU ve bellek kaynağına sahip değişiklikler akışını alabiliyor  

Farklı VM boyutlarının sınırlamaları hakkında daha fazla ayrıntı için [burada](../../sizes.md) bulunabilir 

Bir DR hedefini dağıtmaya yönelik başka bir desteklenen yöntem, bir üretim dışı SAP örneğinin üretim dışı bir DBMS örneğini çalıştıran bir VM 'de yüklü ikinci bir DBMS örneğidir. Bu, DR senaryosunda ana örnek olarak çalışması gereken belirli hedef örnekler için bellek, CPU kaynakları, ağ bant genişliği ve depolama bant genişliği hakkında bilgi almanız gerektiğinden bu biraz daha zor olabilir. Özellikle de HANA 'da, verilerin DR hedefi örneğine önceden yüklenmemiş olması için paylaşılan bir konakta DR hedefi olarak işlev gören örneği yapılandırmanız önemle önerilir.

HANA büyük örnek DR senaryolarında şu belgeleri kontrol edin:

- [Depolama çoğaltması kullanan DR ile tek düğüm](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Depolama çoğaltması kullanarak DR (çok amaçlı) ile tek düğüm](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Depolama çoğaltması kullanarak DR (çok amaçlı) ile tek düğüm](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Depolama çoğaltması ile HSR ve DR ile yüksek kullanılabilirlik](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Depolama çoğaltması kullanarak DR ile genişleme](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [HSR kullanarak DR ile tek düğüm](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [Tek düğümlü HSR-DR (maliyet için iyileştirilmiş)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [HSR ile yüksek kullanılabilirlik ve olağanüstü durum kurtarma](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [HSR ile yüksek kullanılabilirlik ve olağanüstü durum kurtarma (maliyet için iyileştirilmiş)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [HSR kullanarak DR ile ölçeklendirme](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) kullanımı, SAP iş yükü altındaki DBMS dağıtımları için sınanmamıştır. Sonuç olarak, bu zaman noktasındaki SAP sistemlerinin DBMS katmanı için desteklenmez. Microsoft ve SAP tarafından listelenmeyen diğer çoğaltma yöntemleri desteklenmez. Farklı Azure bölgeleri arasında SAP sistemlerinin DBMS katmanını çoğaltmak için üçüncü taraf yazılım kullanılması, yazılımın satıcısı tarafından desteklenilmesi gerekir ve Microsoft ve SAP destek kanalları aracılığıyla desteklenmez. 
 
## <a name="non-dbms-layer"></a>DBMS olmayan katman
SAP uygulama katmanı ve gereken son paylaşımlar veya depolama konumları için, iki ana senaryo müşteriler tarafından yararlanılabilir:

- İkinci Azure bölgesindeki olağanüstü durum kurtarma hedefleri, herhangi bir üretim veya üretim dışı amaçla kullanılmıyor. Bu senaryoda, olağanüstü durum kurtarma hedefi olarak işlev gösteren VM 'Ler ideal şekilde dağıtılır ve görüntü ve üretim SAP uygulama katmanının görüntülerinde yapılan değişiklikler olağanüstü durum kurtarma bölgesine çoğaltılır. Böyle bir görevi gerçekleştirebilen bir işlevsellik [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md). Azure Site Recovery bunun gibi Azure 'dan Azure 'a çoğaltma senaryolarını destekler. 
- Olağanüstü durum kurtarma hedefleri, gerçekte üretim dışı sistemler tarafından kullanılan sanal makinelerlerdir. Tüm SAP yatay, üretim sistemlerine genellikle bir bölgede ve üretim dışı sistemlere sahip olan iki farklı Azure bölgesine yayılır. Birçok müşteri dağıtımında, müşterinin bir üretim sistemine eşdeğer bir üretim dışı sistemi vardır. Müşterinin uygulama katmanında üretim dışı sistemlere önceden yüklenmiş üretim uygulaması örnekleri vardır. Yük devretme durumunda, üretim dışı örnekler kapatılırlar, üretim VM 'lerinin sanal adları, üretim dışı VM 'lere taşınır (DNS 'de yeni IP adresleri atandıktan sonra) ve önceden yüklenmiş üretim örnekleri çalışmaya başlama

### <a name="sap-central-services-clusters"></a>SAP Merkezi Hizmetler kümeleri
Paylaşılan diskler (Windows), SMB paylaşımları (Windows) veya NFS paylaşımlarını kullanan SAP Merkezi Hizmetler kümeleri, çoğaltma biraz daha zordur. Windows tarafında Windows Storage çoğaltma olası bir çözümdür. Linux rsync, önemli bir çözümdür.



## <a name="non-supported-scenario"></a>Desteklenmeyen senaryo
Azure mimarilerinde SAP iş yükü için desteklenmeyen bir senaryo listesi var. **Desteklenmez** , SAP ve Microsoft bu konfigürasyonları destekleyeceği ve bu tür mimariler oluşturmak için yazılım tarafından sunulan son ilgili üçüncü tarafa ertelenmesi gereken anlamına gelir. Kategorilerin ikisi şunlardır:

- Depolama Soft gereçler: Azure Marketi 'nde sunulan bir dizi depolama yazılım aygıtı vardır. Satıcıların bazıları, Azure 'da SAP yazılımıyla ilgili olarak bu depolama yazılım gereçlerinin nasıl kullanılacağına ilişkin bir belge sunar. Bu depolama yazılım gereçlerinin satıcısı tarafından bu depolama alanı yazılım gereçlerine yönelik olan yapılandırmaların veya dağıtımların sağlanması gerekir. Bu olgu Ayrıca [sap desteği notuna](https://launchpad.support.sap.com/#/notes/2015553) de göre belirlenir #2015553
- Yüksek kullanılabilirlik çerçeveleri: yalnızca Paceyapıcısı ve Windows Server yük devretme kümesi, Azure 'da SAP iş yükü için yüksek kullanılabilirlik çerçeveleri destekliyordu. Daha önce belirtildiği gibi, SIOS 'nin çözümü `Datakeeper` Microsoft tarafından açıklanmış ve belgelenmiştir. Bununla birlikte, SIOS bileşenlerinin `Datakeeper` Bu bileşenleri sağlayan satıcı olarak Sios aracılığıyla desteklenmesi gerekir. SAP ayrıca çeşitli SAP notlarındaki diğer sertifikalı yüksek kullanılabilirlik çerçeveleri de listelenmiştir. Bazıları Azure için üçüncü taraf satıcı tarafından da sertifikalandırilmiştir. Bununla birlikte, bu ürünleri kullanan yapılandırma desteğinin ürün satıcısı tarafından sağlanması gerekir. Farklı satıcıların SAP destek işlemlerine farklı entegrasyonu vardır. Azure 'da dağıtılan SAP yapılandırmalarında ürünü kullanmaya karar vermeden önce, belirli bir satıcı için en uygun destek sürecini Açıklığa kavuşturmanız gerekir.
- Veritabanı dosyalarının paylaşılan disklerde bulunduğu paylaşılan disk kümeleri, maxDB dışında desteklenmez. Diğer tüm veritabanları için desteklenen çözüm, yüksek kullanılabilirlik senaryolarını yapılandırmak için bir SMB veya NFS paylaşım veya paylaşılan disk yerine ayrı depolama konumları içermelidir

Desteklenmeyen diğer senaryolar şunlardır:

- SAP uygulama katmanı ve SAP DBMS katmanı arasında NetWeaver, S/4HANA 'da gösterildiği gibi daha büyük bir ağ gecikmesi sunan dağıtım senaryoları ve `Hybris` örn. Şunları içerir:
    - Şirket içi katmanlardan birini dağıtma, diğer katman Azure 'da dağıtılır
    - Farklı bir Azure bölgesindeki sistemin SAP uygulama katmanını DBMS katmanından dağıtma
    - Azure 'da ve bu tür bir mimari desenlerinin Azure yerel hizmeti tarafından sağlandığı durumlar dışında, Azure 'da birlikte bulunan veri merkezlerinde bir katmanı ve Azure 'daki diğer katmanı dağıtma
    - SAP uygulama katmanı ve DBMS katmanı arasında ağ sanal gereçlerini dağıtma
    - SAP DBMS katmanı veya SAP Genel aktarım dizini için Azure veri merkezlerinde birlikte bulunan veri merkezlerinde barındırılan depolamayı kullanma
    - İki katmanı iki farklı bulut satıcısı ile dağıtma. Örneğin, DBMS katmanını Oracle bulut altyapısında ve Azure 'da uygulama katmanında dağıtma
- Çok örnekli HANA Paceyapıcısı küme yapılandırması
- Windows 'da desteklenen SAP veritabanları için ANF 'de SOFS veya SMB aracılığıyla paylaşılan disklere sahip Windows küme yapılandırması. Bunun yerine, belirli veritabanlarının yerel yüksek kullanılabilirlik çoğaltmasının kullanımını ve ayrı depolama yığınları kullanmayı öneririz.
- Linux üzerinde desteklenen SAP veritabanlarının, ANF 'nin üzerinde bulunan veritabanı dosyalarıyla, SAP HANA hariç dağıtımı.
- Oracle DBMS 'nin Windows ve Oracle Linux dışındaki tüm konuk IŞLETIM sistemlerinde dağıtımı. Ayrıca bkz. [sap destek notuna #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Test etmediğiniz senaryolar ve bu nedenle şu şekilde liste ile ilgili hiçbir deneyim yok:

- DBMS katmanı VM 'lerini çoğaltma Azure Site Recovery. Sonuç olarak, olası olağanüstü durum kurtarma yapılandırması için veritabanı yerel zaman uyumsuz çoğaltma işlevini kullanmanızı öneririz
 

## <a name="next-steps"></a>Sonraki Adımlar
[SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama](./planning-guide.md) 'da sonraki adımları okuyun




  