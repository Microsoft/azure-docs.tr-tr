---
title: Azure VM 'lerinde SAP kullanmaya başlama | Microsoft Docs
description: Microsoft Azure sanal makinelerde (VM) çalışan SAP Çözümleri hakkında bilgi edinin
services: virtual-machines-sap
ms.service: virtual-machines-sap
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/13/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9f4a1f5823b67cc55a4e038bae911ec583608336
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483104"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>SAP iş yükü senaryolarını barındırmak ve çalıştırmak için Azure kullanın

Microsoft Azure kullandığınızda, görev açısından kritik SAP iş yüklerinizi ve senaryolarını ölçeklenebilir, uyumlu ve kurumsal olarak kanıtlanmış bir platformda güvenilir bir şekilde çalıştırabilirsiniz. Azure 'un ölçeklenebilirlik, esneklik ve maliyet tasarrufu olanakları elde edersiniz. Microsoft ve SAP arasındaki genişletilmiş iş ortaklığı sayesinde, SAP uygulamalarını Azure 'daki geliştirme ve test ve üretim senaryolarında çalıştırabilir ve tam olarak desteklenmektedir. SAP NetWeaver ile SAP S/4HANA, Linux 'ta SAP BI ve Windows 'a SAP HANA, bu konu başlığı altında yer aldık.

Azure üzerinde farklı DBMS ile SAP NetWeaver senaryolarını barındırmanın yanı sıra Azure 'da SAP BI gibi diğer SAP iş yükü senaryolarını de barındırabilirsiniz. 

SAP HANA için Azure 'un benzersizliği, Azure 'ı ayrı ayarlayan bir tekliftir. Azure, SAP HANA içeren daha fazla bellek ve CPU kaynak gerektiren SAP senaryolarına olanak tanımak için, müşterinin adanmış çıplak donanımlarının kullanımını sağlar. S/4HANA veya diğer SAP HANA iş yükü için 24 TB 'a kadar (120 TB genişleme) bellek gerektiren SAP HANA dağıtımlarını çalıştırmak için bu çözümü kullanın. 

Azure 'da SAP iş yükü senaryolarını barındırmak, kimlik tümleştirmesi ve çoklu oturum açma gereksinimleri de oluşturabilir. Bu durum, farklı SAP bileşenleri ve hizmet olarak yazılım (SaaS) veya hizmet olarak platform (PaaS) tekliflerini bağlamak için Azure Active Directory (Azure AD) kullandığınızda ortaya çıkabilir. Azure AD ve SAP varlıklarıyla ilgili tümleştirme ve çoklu oturum açma senaryolarından oluşan bir liste, "Azure AD SAP kimlik tümleştirmesi ve çoklu oturum açma" bölümünde açıklanmaktadır ve belgelenmiştir.

## <a name="changes-to-the-sap-workload-section"></a>SAP iş yükü bölümünde yapılan değişiklikler
Azure iş yükünde SAP bölümündeki belgelerde yapılan değişiklikler bu makalenin sonunda listelenmiştir. Değişiklik günlüğündeki girişler 180 gün boyunca tutulur.

## <a name="you-want-to-know"></a>Bilmeniz istediğiniz
Belirli sorularınız varsa, başlangıç sayfasının bu bölümündeki belirli belgelere veya akışlara yöneltireceğiz. Şunları bildirmek istiyorsunuz:

- Hangi SAP yazılım sürümleri ve işletim sistemi sürümleri için desteklenen Azure VM 'Leri ve HANA büyük örnek birimleri. Yanıtlar için [Azure dağıtımı Için HANGI SAP yazılımlarının desteklendiğini](./sap-supported-product-on-azure.md) ve bilgileri bulma işlemini okuyun
- Azure VM 'Leri ve HANA büyük örneklerle desteklenen SAP dağıtım senaryoları. Desteklenen senaryolar hakkında daha fazla bilgi belgelerde bulunabilir:
    - [Azure sanal makinesi üzerinde SAP iş yüküne yönelik desteklenen senaryolar](./sap-planning-supported-configurations.md)
    - [HANA büyük örneği için desteklenen senaryolar](./hana-supported-scenario.md)
- Azure Hizmetleri, Azure VM türleri ve Azure Depolama Hizmetleri farklı Azure bölgelerinde kullanılabilir, [bölgeye göre kullanılabilir site ürünlerini](https://azure.microsoft.com/global-infrastructure/services/) kontrol edin 
- Windows ve Paceyapıcısı 'nin desteklenme yanı sıra üçüncü taraf HA çerçevesi çalışıyor mu? [Sap destek notunun](https://launchpad.support.sap.com/#/notes/1928533) alt kısmına bakın #1928533
- Senaryoum için en iyi Azure depolama alanı nedir? [SAP iş yükü Için Azure depolama türlerini](./planning-guide-storage.md) okuyun
- , SAP tarafından desteklenen Oracle Enterprise Linux 'taki Red Hat çekirdeği midir? SAP [sap destek dekontunu okuyun #1565179](https://launchpad.support.sap.com/#/notes/1565179)
- Azure [da (s) v4](../../dav4-dasv4-series.md) / [EA](../../eav4-easv4-series.md) 'lar sanal makine aileleri neden SAP HANA için sertifikalı değil? Azure das/EAS VM aileleri, AMD işlemci tabanlı donanımı temel alır. SAP HANA, sanallaştırılmış senaryolarda bile olmayan AMD işlemcileri desteklemez
- Şu iletiyi almaya devam ediyorum: ' RDTSCP yönergesi için CPU bayrakları veya constant_tsc ya da nonstop_tsc için CPU bayrakları ayarlı değil veya current_clocksource ve available_clocksource doğru şekilde SAP HANA yapılandırılmamış, ancak en son Linux kernels 'i çalıştırdığımı belirten bir olay. Yanıt için [sap destek notuna bakın #2791572](https://launchpad.support.sap.com/#/notes/2791572)
- Azure 'da SAP Fiori dağıtmak için mimarileri nereden bulabilirim? [Internet 'e YÖNELIK SAP Fiori uygulamaları için Application Gateway Web uygulaması güvenlik duvarı (WAF) v2 kurulumu Azure 'da blog SAP 'ye](https://blogs.sap.com/2020/12/03/sap-on-azure-application-gateway-web-application-firewall-waf-v2-setup-for-internet-facing-sap-fiori-apps/) göz atın. 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure’da SAP HANA (Büyük Örnekler)

Bir dizi belge, Azure 'daki SAP HANA (büyük örnekler) veya kısa, HANA büyük örnekleri için size yol gösterir. HANA büyük örnekleri hakkında daha fazla bilgi için, [Azure 'da SAP HANA belge genel bakış ve mimarisine (büyük örnekler)](./hana-overview-architecture.md) BAŞLAYıN ve Hana büyük örnek bölümündeki ilgili belgelere gidin



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure sanal makineler 'de SAP HANA
Belgelerinin bu bölümü SAP HANA farklı yönlerini kapsamaktadır. Bir önkoşul olarak, Azure IaaS 'in temel hizmetlerini sağlayan Azure 'un asıl hizmetleri hakkında bilgi sahibi olmanız gerekir. Bu nedenle, Azure işlem, depolama ve ağ hakkında bilgi sahibi olmanız gerekir. Bu konuların birçoğu SAP NetWeaver ile ilgili [Azure planlama kılavuzunda](./planning-guide.md)ele alınır. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure sanal makinelerinde dağıtılan SAP NetWeaver
Bu bölümde, Azure 'da SAP NetWeaver, SAP ve Iş için planlama ve dağıtım belgeleri listelenmiştir. Belgeler, Azure 'da SAP iş yükünün temel alınarak ve HANA olmayan veritabanlarının kullanımına odaklanmaktadır. Yüksek kullanılabilirlik için belgeler ve makaleler, Azure 'da yüksek kullanılabilirlik SAP HANA için de temel sahiptir

## <a name="sap-netweaver-and-s4hana-high-availability"></a>SAP NetWeaver ve S/4HANA yüksek kullanılabilirlik
SAP uygulama katmanının ve DBMS 'nin yüksek kullanılabilirliği, belge [Azure sanal makineleri için yüksek kullanılabilirliğe sahıp SAP NetWeaver](./sap-high-availability-guide-start.md) ile başlayan ayrıntılarla birlikte belgelenmiştir



## <a name="integrate-azure-ad-with-sap-services"></a>Azure AD 'yi SAP hizmetleriyle tümleştirme
Bu bölümde, hem SAP SaaS hem de PaaS hizmetleri, NetWeaver ve Fiori ile SSO 'yu yapılandırma hakkında bilgi edinebilirsiniz. 



## <a name="documentation-on-integration-of-azure-services-into-sap-components"></a>Azure hizmetlerinin SAP bileşenleriyle tümleştirilmesine yönelik belgeler
Bu bölümde, Microsoft Power BI tümleştirme hakkındaki belgeleri SAP veri kaynaklarına ve SAP BW Azure Data Factory tümleştirmesini bulabilirsiniz.



## <a name="change-log"></a>Değişiklik Günlüğü
- 04/12/2021: [SLES 'Deki Azure VM 'lerinde SAP HANA Için ha](./sap-hana-high-availability.md)'de değiştirin SAP HANA, SAP HANA sistem çoğaltması Python kancası için yapılandırma yönergeleri eklemek üzere RHEL ve ha üzerindeki [azure sanal](./sap-hana-high-availability-rhel.md) makinelerinde [SAP HANA ölçeği](./sap-hana-high-availability-netapp-files-red-hat.md)  
- 04/12/2021: [Azure Backup hizmeti ile SAP HANA yedekleme/geri yükleme](../../../backup/sap-hana-db-about.md) belgeleri tarafından SAP HANA için yedekleme belgeleri değiştirilmiştir 
- 04/12/2021: [SLES yapılandırma kılavuzunda Azure VM 'Lerinde Paceyapıcısı ile SAP HANA genişleme HSR](./sap-hana-high-availability-scale-out-hsr-suse.md) sürümü
- 04/07/2021: [SAP NetWeaver Için Azure sanal MAKINELER DBMS dağıtımında](./dbms_guide_sqlserver.md) SQL Server çok örnekli ve çoklu veritabanı SQL Server desteğinin açıklığa kavuşturuldu desteği
- 04/07/2021: [SAP NetWeaver Için Azure sanal makineler planlama ve uygulama](./planning-guide.md) 'DA ikincil IP adresleriyle ilgili bilgiler eklendi
- 04/07/2021: [SAP iş yükü Için Azure depolama türlerinde](./planning-guide-storage.md) ANF 'de Oracle DBMS desteği desteği eklendi
- 03/17/2021: [SLES 'Deki Azure VM 'lerinde SAP HANA Için ha](./sap-hana-high-availability.md)değiştirme, RHEL ve ha üzerinde Azure [VM 'lerinde SAP HANA için, Khel 'de azure sanal](./sap-hana-high-availability-rhel.md) makinelerinde [SAP HANA ölçeği ile](./sap-hana-high-availability-netapp-files-red-hat.md)
- 03/15/2021: SAP ascs [/SCS ÖRNEĞINDE wsfc ve dosya paylaşımıyla](./sap-high-availability-guide-wsfc-file-share.md)değiştirin, SAP Ass/SCS örneği [ve hem](./sap-ascs-ha-multi-sid-wsfc-file-share.md) wsfc hem de dosya paylaşımıyla SAP Ass/SCS örneği, SAP ascs/SCS[örneği](./sap-high-availability-installation-wsfc-file-share.md) ve SOFS paylaşımının ayrı kümelerde dağıtılması gerektiğini açıklığa kavuşturun
- 03/03/2021: SAP sistem yüklemesi sırasında SWPM çalıştıran kullanıcı için yükseltilmiş ayrıcalıkların gerekli olduğunu belirten bir uyarı eklemek için, [wsfc ve Azure NetApp Files (SMB) Ile SAP yoks/SCS Için ha kılavuzunda](./high-availability-guide-windows-netapp-files-smb.md) değişiklik yapın
- 02/11/2021: RHEL 8. x için [Red Hat Enterprise Linux sunucusundaki Azure VM 'LERINDE IBM DB2 LUW 'ın yüksek kullanılabilirliğe sahip](./high-availability-guide-rhel-ibm-db2-luw.md) değişiklikler
- 02/03/2021: [Azure 'da RHEL 'de paceyapıcısı ayarlama](./high-availability-guide-rhel-pacemaker.md) , stonıth Create komutunda pcmk_host_map güncelleştirmek için değiştirin
- 02/03/2021: stonıth Create komutunda pcmk_host_map eklemek için [Azure 'da SLES 'de paceyapıcısı ayarlama](./high-availability-guide-suse-pacemaker.md) sırasında değişiklik yapın 
- 02/03/2021: [Azure sanal makine depolama konfigürasyonları SAP HANA](./hana-vm-operations-storage.md) makalesinde SuSE için g/ç Zamanlayıcı ayarları hakkında daha fazla bilgi
- 02/01/2021: SAP HANA ha 'de değiştirin ve [RHEL üzerinde ANF ile ölçeği büyütme](./sap-hana-high-availability-netapp-files-red-hat.md), [RHEL üzerindeki Azure VM 'lerinde genişleme ile](./sap-hana-high-availability-scale-out-hsr-rhel.md)azure vm 'lerinde genişleme SAP HANA [, SLES üzerinde ANF ile](./sap-hana-scale-out-standby-netapp-files-suse.md) azure vm 'lerinde yer alan ve [Azure VM](./hana-vm-operations-netapp.md) 'lerinde bir SAP HANA bağlantı eklemek için RHEL [üzerinde bir ve](./sap-hana-scale-out-standby-netapp-files-rhel.md) daha fazla SAP HANA Azure NetApp Files SAP HANA
- 01/23/2021: [Azure sanal makine depolama yapılandırması](./hana-vm-operations-storage.md) ve [NFS v 4.1 birimleri](./hana-vm-operations-netapp.md) SAP HANA makalelerinde bir disk birimi Yöneticisi kullanmadan, farklı Azure disklerinde veya NFS paylaşımlarında bir disk birimi Yöneticisi kullanmadan, Hana veri birimi bölümlendirme işlevlerini işlevsellik olarak ekleyin SAP HANA Azure NetApp Files
- 01/18/2021: Azure sanal makineler 'de Oracle için Azure net Apps dosya tabanlı NFS desteği eklendi, [SAP iş yükü Için Oracle DBMS dağıtımı](./dbms_guide_oracle.md) ve [SAP HANA Için Azure NetApp Files belge NFS v 4.1 birimlerindeki](./hana-vm-operations-netapp.md) tabloda Ondalıklar ayarlanıyor
- 01/11/2021: [RHEL for SAP Applications üzerinde Azure sanal](./high-availability-guide-rhel.md)makinelerinde SAP NW için ha 'da küçük değişiklikler, RHEL [çok düzeyli](./high-availability-guide-rhel-multi-sid.md) Azure VM ['lerinde RHEL](./high-availability-guide-rhel-netapp-files.md) 'de SAP NW IÇIN ve RHEL8 ve RHEL7, ENSA1 ve ENSA2 için çalışır.
- 01/05/2021: [SLES 'de ANF Ile Azure VM](./sap-hana-scale-out-standby-netapp-files-suse.md) 'lerinde yer alan ve Azure VM 'lerinde yer alan SAP HANA ve [RHEL 'de ANF üzerinde](./sap-hana-scale-out-standby-netapp-files-rhel.md)yer alan Azure sanal makinelerinde bekleyen bir düğüm ile ölçek GENIŞLETME SAP HANA değişiklik, SAP konak aracısının yerel bağlantı noktası aralığını yönetmesine izin vermek için önerilen yapılandırmayı yeniden gözden geçirme  
- 01/04/2021: Azure 'da SAP HANA, HLI tarafından desteklenen yeni Azure bölgelerini ekleme [(büyük örnekler)](./hana-overview-architecture.md)
- 12/29/2020: [Azure kullanılabilirlik alanları Ile SAP iş yükü yapılandırmalarında](./sap-ha-availability-zones.md) belirli Azure bölgeleri için mimari önerileri ekleyin
- 12/21/2020: [HLI Için kullanılabilir SKU](./hana-available-skus.md) 'Larda Hana büyük örnek SKU 'larına yeni sertifikalar ekleyin
- 12/12/2020: SAP [, Azure dağıtımları için desteklenen SAP YAZıLıMıNı](./sap-supported-product-on-azure.md#oracle-dbms-support) SAP 'ye göre Oracle Enterprise Linux desteğiyle ilgili ayrıntıları açıklığa kavuşturan SAP notuna yönelik işaretçi eklendi
- 11/26/2020: [SAP iş yükü için](./planning-guide-storage.md) Azure [sanal makine depolama yapılandırmalarının](./hana-vm-operations-storage.md) ve Azure depolama türlerini tek [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) 'larını değiştirdi SAP HANA uyarlayın
- 11/05/2020: [Azure sanal makine depolama yapılandırmalarında SAP HANA](./hana-vm-operations-storage.md) desteklenen dosya sistemi türleri hakkında yeni SAP notuna yönelik bağlantıyı değiştirme 
- 10/26/2020: Azure Premium Depolama yapılandırması için bazı tabloları değiştirme [SAP HANA Azure sanal makine depolama yapılandırmalarında](./hana-vm-operations-storage.md) sağlanan ve veri bloğu aktarım hızını açıklığa kavuşturacak
- 10/22/2020: [SLES for SAP uygulamaları üzerindeki Azure VM](./high-availability-guide-suse.md)'lerinde SAP NW için ha 'DA, [ANF ile SLES](./high-availability-guide-suse-netapp-files.md)'deki Azure VM 'lerinde SAP NW 'de [sap NW için ha, net.IPv4.TCP_KEEPALIVE_TIME](./high-availability-guide-rhel.md) [Khel ile RHEL](./high-availability-guide-rhel-netapp-files.md) 'nin Azure VM 'lerinde  
- 10/16/2020: [SLES 'In pacemaker Ile Azure VM 'LERINDE IBM DB2 LUW 'ın ha](./dbms-guide-ha-ibm.md)'de değiştirilmesi [Azure VM 'lerinde SAP NW için, RHEL 'nin SAP uygulamaları için](./high-availability-guide-rhel.md)ha, RHEL ÜZERINDEKI Azure VM 'lerinde [IBM DB2 LUW](./high-availability-guide-rhel-ibm-db2-luw.md), RHEL 'de Azure VM 'lerinde SAP NW [için ha](./high-availability-guide-rhel-multi-sid.md), [ANF Ile RHEL üzerindeki Azure VM 'lerinde SAP NW](./high-availability-guide-rhel-netapp-files.md)için ha [SLES for SAP UYGULAMALARı üzerinde SAP NW için ha](./high-availability-guide-suse.md), SLES [Multi-SID kılavuzundaki Azure VM 'LERINDE SAP](./high-availability-guide-suse-multi-sid.md)'ye yönelik ha, [SLES 'de Azure VM 'Lerinde SAP NW için ha SAP uygulamaları için ANF](./high-availability-guide-suse-netapp-files.md), [SLES 'de Azure VM 'lerinde NFS için ha](./high-availability-guide-suse-nfs.md), [SLES üzerinde Azure VM 'lerinde SAP HANA ha](./sap-hana-high-availability.md), [RHEL üzerinde ANF ile SAP HANA ölçeği artırma](./sap-hana-high-availability-netapp-files-red-hat.md) , [RHEL üzerinde Azure VM 'lerinde SAP HANA ha](./sap-hana-high-availability-rhel.md), [RHEL üzerindeki Azure VM 'Lerinde pacemaker Ile genişleme HSR SAP HANA](./sap-hana-high-availability-scale-out-hsr-rhel.md)wsfc ve [paylaşılan disk Ile](./sap-high-availability-infrastructure-wsfc-shared-disk.md)SAP yoks/SCS, wsfc ve [Azure Paylaşılan](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md) disk ile SAP yoks/SCS için çok düzeyli ha Kılavuzu ve IKINCIL IP 'lerde Yük Dengeleme senaryolarında, kayan IP 'nin desteklenmeyen bir Ifade eklemek için çok [düzeyli ha Kılavuzu](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) için Azure altyapısını hazırlama 
- 10/16/2020: HANA büyük örneklerde [SAP HANA yedekleme ve geri yükleme](./hana-backup-restore.md) sırasında Hana büyük örneklerinin depolama anlık görüntülerini denetlemek için belgeler ekleme
- 10/15/2020: Azure 'da SAP BusinessObjects bı platformu, Azure 'da SAP BusinessObjects [bı platformu planlama ve uygulama kılavuzu](businessobjects-deployment-guide.md) , Azure 'da [Linux Için Azure ve SAP BusinessObjects bı platform dağıtım kılavuzu](businessobjects-deployment-guide-linux.md) ' na bakın
- 10/05/2020: [RHEL yapılandırma kılavuzunda Azure VM 'Lerinde pacemaker ile SAP HANA genişleme HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) sürümü
- 09/30/2020: [RHEL üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirlik](./sap-hana-high-availability-rhel.md), ha, RHEL [üzerinde anf ile SAP HANA ölçeği artırma](./sap-hana-high-availability-netapp-files-red-hat.md) ve RHEL 8,1 yönergelerini uyarlamak için [Azure 'Da RHEL üzerinde paceyapıcısı ayarlama](./high-availability-guide-rhel-pacemaker.md)
- 09/29/2020: [SAP uygulamalarıyla en iyi ağ gecikme süresi Için Azure yakınlık yerleşimi grupları](./sap-proximity-placement-scenarios.md) makalesindeki PPG kullanımı etrafında kısıtlamalar ve öneriler yapılıyor 
- 09/28/2020: [SAP HANA için Azure NetApp Files belge NFS v 4.1 birimleri](./hana-vm-operations-netapp.md) ile Azure NetApp Files kullanarak SAP HANA için yeni bir depolama işlemi Kılavuzu ekleniyor
- 09/23/2020: [hLi Için kullanılabilir SKU 'larda](./hana-available-skus.md) hLi için yeni sertifikalı SKU 'lar ekleyin 
- 09/20/2020: belgelerde yapılan değişiklikler [SAP iş yükü](./dbms_guide_general.md)için Azure sanal makineleri dbms dağıtımı [SQL Server](./dbms_guide_sqlserver.md), SAP NetWeaver IÇIN Azure sanal makineler DBMS dağıtımı, [Azure sanal makineler for SAP Iş yükü için Azure sanal makineleri, Azure sanal makineler, SAP Iş yüküne](./dbms_guide_oracle.md)yönelik [IBM DB2 Azure sanal makineler](./dbms_guide_ibm.md) ve SAP ikililerini farklı Azure disklerine ayırmayı öneren yeni yapılandırma önerisine uyum sağlar. Ayrıca, farklı kılavuzlara Ultra disk önerileri de ekleniyor.
- 09/08/2020: stonıth tanımlarını netleştirmek için [SLES 'teki Azure VM 'lerinde SAP HANA yüksek kullanılabilirlik](./sap-hana-high-availability.md) olarak değiştirin
- 09/03/2020: [SAP HANA Azure sanal makine depolama yapılandırmalarında](./hana-vm-operations-storage.md) , Ultra disk Ile 1 GB 'lik kapasite başına en az 2 IOPS olacak şekilde değiştirin
- 09/02/2020: [HLI Için kullanılabilir SKU](./hana-available-skus.md) 'LARDA, Hana sertifikalı SKU 'lar daha fazla saydam olacak şekilde değiştirin
- 25 Ağustos 2020: mımpo 'yı onarmak için [ANF Ile SLES 'Teki Azure VM 'LERINDE SAP NW Için ha](./high-availability-guide-suse-netapp-files.md) 'de değiştirin
- 25 Ağustos 2020: Azure Paylaşılan disk ve belge SAP ERS2 mimarisini kullanma seçeneğini sağlamak için, [Azure altyapısını](./sap-high-availability-infrastructure-wsfc-shared-disk.md) wsfc ve paylaşılan disk ile SAP ascs/ [SCS](./sap-high-availability-guide-wsfc-shared-disk.md)'ler IÇIN hazırlayın ve SAP NW 'yi wsfc ve paylaşılan diskle birlikte [yükler](./sap-high-availability-guide-wsfc-shared-disk.md) .
- 25 Ağustos 2020: [wsfc ve Azure Paylaşılan disk Ile SAP yoks/SCS için çok DÜZEYLI ha kılavuzunun](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md) yayını
- 25 Ağustos 2020: [wsfc ve Azure NetApp Files (SMB) Ile SAP ASCS/SCS Için ha kılavuzunda](./high-availability-guide-windows-netapp-files-smb.md)değiştirin, wsfc ve [dosya paylaşımıyla](./sap-high-availability-infrastructure-wsfc-file-share.md)SAP yoks/SCS, wsfc ve [paylaşılan disk](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) ile SAP yoks/SCS için çok düzeyli ha Kılavuzu, wsfc ve paylaşılan disk ile SAP yoks/SCS IÇIN çok [düzeyli ha Kılavuzu ve WFC](./sap-ascs-ha-multi-sid-wsfc-file-share.md) ve paylaşılan disk ile SAP 
- 21 Ağustos 2020: I ve II türünde HLı birimler için kullanılabilir işletim sistemi olarak [Hana büyük örnekler Için uyumlu Işletim sistemlerine](./os-compatibility-matrix-hana-large-instance.md) yeni işletim sistemi yayını ekleme
- 18 Ağustos 2020: [RHEL üzerinde ANF ile SAP HANA ölçeği Için ha](./sap-hana-high-availability-netapp-files-red-hat.md) sürümü
- 17 Ağustos 2020: SAP NetWeaver sistemlerini Şirket içinden Azure 'a taşımak için Azure Site Recovery kullanma hakkında bilgi ekleme [Azure sanal makineler planlama ve uygulama IÇIN SAP NetWeaver](./planning-guide.md)
- 08/14/2020: [IBM DB2 Azure sanal makineleri için disk yapılandırma önerisi, SAP Için Azure sanal MAKINELER DBMS dağıtımı](./dbms_guide_ibm.md)
- 11 Ağustos 2020: RHEL 7,6, [Hana büyük örnekleri Için uyumlu Işletim sistemlerine](./os-compatibility-matrix-hana-large-instance.md) , ı türünde hLi birimler için kullanılabilir işletim sistemi olarak ekleniyor
- 10 Ağustos 2020: [Azure sanal makine depolama yapılandırmalarında SAP HANA](./hana-vm-operations-storage.md) SAP HANA depolama yapılandırması ve [Azure 'da SAP iş yükleri için bazı güncelleştirmeler yapma: planlama ve dağıtım denetim listesi](./sap-deployment-checklist.md)
- 04 Ağustos 2020: [Azure 'DA SLES 'de pacemaker 'ı ayarlama](./high-availability-guide-suse-pacemaker.md) ve [Azure 'daki RHEL üzerinde](./high-availability-guide-rhel-pacemaker.md) paceyapıcısı ayarlama ve pacemaker kümeleri için güvenilir ad çözümlemesinin önemini vurgulama
- 04 Ağustos 2020: [SAP NW ha Ile WFCS, dosya paylaşımıyla](./sap-high-availability-installation-wsfc-file-share.md)birlikte, [paylaşılan disk ile wfcs ÜZERINDE SAP NW ha](./sap-high-availability-installation-wsfc-shared-disk.md), [Azure VM](./high-availability-guide.md)'lerinde SAP NW için ha, for SAP NW, [SLES](./high-availability-guide-suse.md)üzerinde SAP NW için ha, SLES 'deki [Azure VM 'Lerinde](./high-availability-guide-suse-netapp-files.md)SAP NW [SLES Multi-SID KıLAVUZUNDAKI Azure VM 'lerinde SAP NW Için ha](./high-availability-guide-suse-multi-sid.md), RHEL 'de Azure VM 'lerinde SAP [NetWeaver için yüksek kullanılabilirlik](./high-availability-guide-rhel.md), Azure VM 'lerinde RHEL 'de SAP [için,](./high-availability-guide-rhel-netapp-files.md) RHEL [Çoklu SID](./high-availability-guide-rhel-multi-sid.md) 'de Azure VM 'lerinde for SAP NW `enque/encni/set_so_keepalive`
- 23 Temmuz 2020: SAP HANA Büyük Örnekleri ayırmayı satın almadan önce bilmeniz gerekenleri açıklayan [bir Azure rezervasyon ile SAP HANA büyük örnekleri kaydetme](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) ve satın alma işlemi yapma
- 16 Temmuz 2020: [dağıtım KıLAVUZUNDA](deployment-guide.md) SAP IÇIN yeni VM uzantısı yüklemek üzere Azure PowerShell nasıl kullanacağınızı açıkla
- 04 Temmuz 2020:  [SAP Çözümleri Için Azure Izleyici sürümü (Önizleme)](./azure-monitor-overview.md)
- 01 Temmuz 2020: Azure [sanal makine depolama yapılandırmalarını SAP HANA](./hana-vm-operations-storage.md) belgedeki Azure Premium Depolama patlaması işlevine göre daha ucuz depolama yapılandırması önerme 
- 24 Haziran 2020: Azure 'da yeni geliştirilmiş Azure sınır Aracısı ve cihazların Azure sınır aracısına göre daha dayanıklı STONITH yapılandırmasını serbest bırakmak için [Azure 'daki SLES 'de Paceyapıcısı ayarlama](./high-availability-guide-suse-pacemaker.md) sırasında değişiklik yapın 
- 24 Haziran 2020: [Azure 'daki RHEL 'nin](./high-availability-guide-rhel-pacemaker.md) daha dayanıklı STONITH yapılandırması yayınlamaması Için pacemaker ayarlama sırasında değişiklik
- 23 Haziran 2020: [SAP NetWeaver Kılavuzu planlama ve uygulama Için Azure sanal makinelerinde](./planning-guide.md) yapılan DEĞIŞIKLIKLER ve [SAP iş yükü Kılavuzu için Azure depolama türlerine](./planning-guide-storage.md) giriş
- 06/22/2020: [dağıtım KıLAVUZUNA](deployment-guide.md) SAP IÇIN yeni VM uzantısı için yükleme adımları ekleyin
- 16 Haziran 2020: SUSE genel bulut altyapısı 101 belgelerine bir bağlantı eklemek için [SAP ha senaryolarında Azure Standard ILB kullanarak VM 'ler Için genel uç nokta bağlantısında](./high-availability-guide-standard-load-balancer-outbound-connections.md) değişiklik yapın 
- 10 Haziran 2020: yeni HLI SKU 'ları HLI ve [SAP HANA (büyük örnekler) depolama mimarisi](./hana-storage-architecture.md) [Için kullanılabilir SKU 'lara](./hana-available-skus.md) ekleme
- 21 Mayıs 2020: [Azure 'DA SLES üzerinde Paceyapıcısı ayarlama](./high-availability-guide-suse-pacemaker.md) ve Azure ['da RHEL 'de PACEYAPıCıSı](./high-availability-guide-rhel-pacemaker.md) ayarlama, [SAP ha senaryolarında Azure Standard ILB kullanarak VM 'ler için genel uç nokta bağlantısına](./high-availability-guide-standard-load-balancer-outbound-connections.md) bağlantı ekleme  
- 19 Mayıs 2020: [SAP HANA Azure sanal makine depolama yapılandırmalarında](./hana-vm-operations-storage.md) , Hana ile ilgili birimler için LVM kullanırken kök birim grubunu kullanmak için önemli bir ileti ekleyin
- 19 Mayıs 2020: Hana büyük [örnekler Için uyumlu Işletim SISTEMLERINDE](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) Hana büyük örnek türü II için yeni desteklenen Işletim sistemi ekleme
- 12 Mayıs 2020: bağlantıları güncelleştirmek ve üçüncü taraf güvenlik duvarı yapılandırması için bilgi eklemek üzere [SAP ha senaryolarında Azure Standard ILB kullanarak VM 'ler Için genel uç nokta bağlantısında](./high-availability-guide-standard-load-balancer-outbound-connections.md) değişiklik yapın
- 11 Mayıs 2020: [SLES 'Teki Azure VM 'lerinde SAP HANA yüksek kullanılabilirlik](./sap-hana-high-availability.md) olarak değişiklik, kaynak için kaynak listesini 0 olarak ayarlama `netcat` ve bu da daha kolay yük devretmeye yol açar 
- 05 Mayıs 2020: [Azure sanal makinelerindeki DEĞIŞIKLIKLER SAP NetWeaver için planlama ve uygulama;](./planning-guide.md) bu Gen2 DAĞıTıMLARıNıN Mv1 VM ailesi için kullanılabilir olduğunu ifade edebilir
- 24 Nisan 2020: [SLES 'de ANF Ile Azure VM 'lerinde bekleme düğümü ile SAP HANA genişleme](./sap-hana-scale-out-standby-netapp-files-suse.md)değişiklikleri Azure VM 'lerinde NetWeaver on [RHEL ile Azure](./sap-hana-scale-out-standby-netapp-files-rhel.md)VM 'lerinde, [SLES](./high-availability-guide-suse-netapp-files.md) 'deki Azure VM 'LERINDE, ANF birimleri için IP adreslerinin otomatik olarak atandığını açıklama eklemek için ANF ile Azure VM 'lerinde yüksek kullanılabilirlik ve [yüksek](./high-availability-guide-rhel-netapp-files.md) kullanılabilirlik içeren SAP HANA.
- 22 Nisan 2020: [SLES 'Deki Azure VM 'lerinde SAP HANA yüksek kullanılabilirlik](./sap-hana-high-availability.md) olarak değişiklik `is-managed` , kümeyi bakım moduna alma ile çakıştığından, yönergelerden meta özniteliği kaldırma
- 21 Nisan 2020: SQL Azure DB, SAP için desteklenen DBMS (Hybrsıs) Commerce platform 1811 ve üzeri sürümlerde Azure dağıtımları ve [SAP Sertifikaları ve Microsoft Azure üzerinde çalışan yapılandırma](./sap-certifications.md) [için SAP yazılımlarının desteklendiği](./sap-supported-product-on-azure.md) makalelerde eklendi
- 16 Nisan 2020: [Azure dağıtımları için](./sap-supported-product-on-azure.md) desteklenen SAP (hybru) ticaret platformu olarak SAP HANA eklendi ve [Microsoft Azure üzerinde çalışan SAP Sertifikaları ve yapılandırma](./sap-certifications.md)
- 13 Nisan 2020: [SAP asa Azure sanal makineler için](./dbms_guide_sapase.md) tam SAP asa sürüm NUMARALARıNA, SAP iş yükü için DBMS dağıtımına doğru
- 07 Nisan 2020: Cloud-netconfig-Azure yönergelerini açıklığa kavuşturacak [Azure 'DA SLES 'de Paceyapıcısı ayarlama](./high-availability-guide-suse-pacemaker.md) sırasında değişiklik
- 06 Nisan 2020: SAP HANA, Azure VM 'lerinde yer alan, [SLES ve Azure NetApp Files ile](./sap-hana-scale-out-standby-netapp-files-suse.md) Azure VM 'lerinde bekleme düğümü Ile SAP HANA değişiklik, NetApp [tr-](https://www.netapp.com/us/media/tr-4746.pdf) [4746](https://www.netapp.com/us/media/tr-4746.pdf)4435 başvurularını kaldırmak için [RHEL 'daki Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- 31 Mart 2020: [Azure VM 'Lerde yüksek düzeyde SAP HANA](./sap-hana-high-availability.md) [SAP HANA ve RHEL üzerindeki Azure](./sap-hana-high-availability-rhel.md) VM 'lerinde, Şeritli birimleri oluştururken Stripe boyutunu belirtme hakkında yönergeler eklemek için
- 27 Mart 2020: dosya sistemi bağlama seçeneklerini NetApp TR-4746 (eşitleme bağlama seçeneğini kaldır) olarak hizalamak için [ANF Ile SLES 'Deki Azure VM 'LERINDE SAP NW Için yüksek kullanılabilirlik](./high-availability-guide-suse-netapp-files.md) olarak değiştirin
- 26 Mart 2020: [SLES çok düzeyli kılavuzdaki Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirlik](./high-availability-guide-suse-multi-sid.md) değişikliği yapın NetApp TR-4746 başvurusu ekleme
- 26 Mart 2020: [SLES for SAP Applications üzerinde SAP NetWeaver Için yüksek kullanılabilirlik](./high-availability-guide-suse.md)olarak değişiklik yapın ve SAP uygulamaları için [Azure NetApp Files, SLES 'teki Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](./high-availability-guide-suse-netapp-files.md)yapın [SLES](./high-availability-guide-suse-nfs.md)üzerinde Azure VM 'lerde yüksek KULLANıLABILIRLIK, [rhel çoklu SID kılavuzundaki](./high-availability-guide-suse-multi-sid.md)Azure [VM](./high-availability-guide-rhel-netapp-files.md) 'lerinde SAP NetWeaver için yüksek kullanılabilirlik, SAP uygulamaları için [RHEL 'de](./high-availability-guide-rhel.md) Azure VM 'lerde yüksek kullanılabilirlik ve güvenlik arka uç havuzu oluşturma yönergelerini açıklığa kavuşturma ve Azure VM 'lerde yüksek kullanılabilirlik Azure NetApp Files Azure Load Balancer
- 19 Mart 2020: belge hızlı başlangıcı 'nın ana düzeltmesi: Azure sanal makinelerine [SAP HANA yüklemek](./hana-get-started.md) Için [Azure sanal makinelerinde tek örnekli SAP HANA el ile yükleme](./hana-get-started.md)
- 17 Mart 2020: artık gerekli olmayan SBD yapılandırma ayarını kaldırmak için [Azure 'daki SUSE Linux Enterprise Server Paceyapıcısı ayarlama](./high-availability-guide-suse-pacemaker.md) sırasında değiştirin
- 16 2020 Mart: [Azure dağıtımları için desteklenen SAP yazılımının](./sap-supported-product-on-azure.md) SAP HANA IaaS sertifikalı platformunda sütun sertifikasyon senaryosunun açıklaması
- 03/11/2020: DBMS örnek desteği başına birden çok veritabanını netleştirmek için [Azure sanal makinesi desteklenen senaryolarda SAP iş](./sap-planning-supported-configurations.md) yükünde değişiklik yapın
- 11 Mart 2020:1. nesil ve 2. nesil VM 'Lerin yanı sıra [SAP NetWeaver için planlama ve uygulama Için Azure sanal makinelerinde](./planning-guide.md) değişiklik yapma
- 10 Mart 2020: [SAP HANA Azure sanal makine depolama yapılandırmalarında](./hana-vm-operations-storage.md) , ANF 'nin gerçek mevcut aktarım hızı sınırlarını açıklığa kavuşturacak şekilde değiştirin
- 09 Mart 2020: SAP [uygulamaları için SUSE Linux Enterprise Server Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirlik](./high-availability-guide-suse.md)değişikliği, SAP uygulamaları için [Azure NetApp Files Ile SUSE Linux Enterprise Server Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](./high-availability-guide-suse-netapp-files.md) [SUSE LINUX ENTERPRISE Server üzerindeki Azure VM 'lerinde NFS için yüksek kullanılabilirlik](./high-availability-guide-suse-nfs.md), [azure 'Da SUSE Linux Enterprise Server üzerinde paceyapıcısı ayarlama, Azure](./high-availability-guide-suse-pacemaker.md)VM 'lerinde yüksek kullanılabilirliğe sahip Azure sanal makinelerinde SUSE Linux Enterprise Server, [Azure](./dbms-guide-ha-ibm.md)VM 'lerinde yüksek kullanılabilirlik ve kaynak Aracısı Azure-lb ile [](./sap-hana-high-availability.md) küme kaynaklarını güncelleştirmek için [yüksek kullanılabilirlik SUSE Linux Enterprise Server](./high-availability-guide-suse-multi-sid.md) SAP HANA 
- 05 Mart 2020: Azure bölgeleri ve Azure sanal makineleri için Azure sanal makineler 'de yapılan değişiklikleri ve içerik değişikliklerini, [SAP NetWeaver planlama ve uygulama Için](./planning-guide.md) yapılandırma
- 03/03/2020: SAP için [SLES 'Teki Azure VM 'LERINDE SAP NW 'yi](./high-availability-guide-suse-netapp-files.md) daha verimli ANF birimi düzenine göre değiştirmek için yüksek kullanılabilirlik olarak değiştirin
- 01 Mart 2020: [Azure sanal makinelerinde SAP HANA için](./sap-hana-backup-guide.md) Azure Backup hizmetini içerecek yedekleme Kılavuzu yeniden çalıştı. [SAP HANA Azure Backup dosya düzeyinde](./sap-hana-backup-file-level.md) daha az ve dar içerik ve disk anlık görüntüsü aracılığıyla yedekleme ile ilgili üçüncü bir belgeyi sildi. İçerik, Azure sanal makinelerinde SAP HANA için yedekleme kılavuzunda ele alınır 
