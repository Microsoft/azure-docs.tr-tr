---
title: Azure VM 'lerinde SAP kullanmaya başlama | Microsoft Docs
description: Microsoft Azure sanal makinelerde (VM) çalışan SAP Çözümleri hakkında bilgi edinin
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 777fe1a62002a765460617cd69f3d518d78ed331
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150876"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>SAP iş yükü senaryolarını barındırmak ve çalıştırmak için Azure kullanın

Microsoft Azure kullandığınızda, görev açısından kritik SAP iş yüklerinizi ve senaryolarını ölçeklenebilir, uyumlu ve kurumsal olarak kanıtlanmış bir platformda güvenilir bir şekilde çalıştırabilirsiniz. Azure 'un ölçeklenebilirlik, esneklik ve maliyet tasarrufu olanakları elde edersiniz. Microsoft ve SAP arasındaki genişletilmiş iş ortaklığı sayesinde, SAP uygulamalarını Azure 'daki geliştirme ve test ve üretim senaryolarında çalıştırabilir ve tam olarak desteklenmektedir. SAP NetWeaver ile SAP S/4HANA, Linux 'ta SAP BI ve Windows 'a SAP HANA, bu konu başlığı altında yer aldık.

Azure üzerinde farklı DBMS ile SAP NetWeaver senaryolarını barındırmanın yanı sıra Azure 'da SAP BI gibi diğer SAP iş yükü senaryolarını de barındırabilirsiniz. 

SAP HANA için Azure 'un benzersizliği, Azure 'ı ayrı ayarlayan bir tekliftir. Azure, SAP HANA içeren daha fazla bellek ve CPU kaynak gerektiren SAP senaryolarına olanak tanımak için, müşterinin adanmış çıplak donanımlarının kullanımını sağlar. S/4HANA veya diğer SAP HANA iş yükü için 24 TB 'a kadar (120-TB genişleme) bellek gerektiren SAP HANA dağıtımlarını çalıştırmak için bu çözümü kullanın. 

Azure 'da SAP iş yükü senaryolarını barındırmak, kimlik tümleştirmesi ve çoklu oturum açma gereksinimleri de oluşturabilir. Bu durum, farklı SAP bileşenleri ve hizmet olarak yazılım (SaaS) veya hizmet olarak platform (PaaS) tekliflerini bağlamak için Azure Active Directory (Azure AD) kullandığınızda ortaya çıkabilir. Azure AD ve SAP varlıklarıyla ilgili tümleştirme ve çoklu oturum açma senaryolarından oluşan bir liste, "AAD SAP kimlik tümleştirmesi ve çoklu oturum açma" bölümünde açıklanmaktadır ve belgelenmiştir.

## <a name="changes-to-the-sap-workload-section"></a>SAP iş yükü bölümünde yapılan değişiklikler
Azure iş yükünde SAP bölümündeki belgelerde yapılan değişiklikler bu makalenin sonunda listelenmiştir.


## <a name="sap-hana-on-azure-large-instances"></a>Azure’da SAP HANA (Büyük Örnekler)

Bir dizi belge, Azure 'daki SAP HANA (büyük örnekler) veya kısa, HANA büyük örnekleri için size yol gösterir. Aşağıdaki HANA büyük örneklerinin alanlarıyla ilgili bilgi için bkz.:

- [Azure 'daki SAP HANA genel bakış (büyük örnekler)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 'da SAP HANA mimarisi (büyük örnekler)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Azure 'da SAP HANA altyapı ve bağlantı (büyük örnekler)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Azure 'da SAP HANA 'yi (büyük örnekler)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [SAP HANA Azure 'da yüksek kullanılabilirlik ve olağanüstü durum kurtarma (büyük örnekler)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Azure 'da SAP HANA sorunlarını giderme ve izleme (büyük örnekler)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Sonraki adımlar:

- [Azure 'da SAP HANA genel bakış ve mimarisini okuyun (büyük örnekler)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure sanal makineler 'de SAP HANA
Belgelerinin bu bölümü SAP HANA farklı yönlerini kapsamaktadır. Bir önkoşul olarak, Azure IaaS 'in temel hizmetlerini sağlayan Azure 'un asıl hizmetleri hakkında bilgi sahibi olmanız gerekir. Bu nedenle, Azure işlem, depolama ve ağ hakkında bilgi sahibi olmanız gerekir. Bu konuların birçoğu SAP NetWeaver ile ilgili [Azure planlama kılavuzunda](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)ele alınır. 

Azure 'da HANA hakkında daha fazla bilgi için aşağıdaki makalelere ve bunların alt makalelerine bakın:

- [Hızlı başlangıç: Azure VM 'lerinde tek örnekli SAP HANA El Ile yükleme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure 'da SAP S/4HANA veya siyah beyaz/4HANA dağıtma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure'da SAP HANA altyapısı yapılandırmaları ve işlemleri](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure sanal makineleri için yüksek kullanılabilirlik SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Tek bir Azure bölgesinde kullanılabilirliği SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure bölgeleri arasında kullanılabilirlik SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure sanal makinelerinde SAP HANA yüksek kullanılabilirliği](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure sanal makinelerinde SAP HANA için yedekleme Kılavuzu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Dosya düzeyinde Azure Backup SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Depolama anlık görüntülerine göre SAP HANA yedekleme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure sanal makinelerinde dağıtılan SAP NetWeaver
Bu bölümde, Azure 'da SAP NetWeaver ve Iş için planlama ve dağıtım belgeleri listelenmiştir. Belgeler, Azure 'da SAP iş yükünün temel alınarak ve HANA olmayan veritabanlarının kullanımına odaklanmaktadır. Yüksek kullanılabilirlik için belgeler ve makaleler Ayrıca, Azure 'da HANA yüksek kullanılabilirliğe yönelik temel bir temelidir; örneğin:

- [Azure sanal makinelerde SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Azure 'da SAP ERP 6,0 için SAP ıDES EHP7 SP3 'Ü dağıtma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Microsoft Azure SUSE Linux VM 'lerinde SAP NetWeaver çalıştırma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver için Azure sanal makineleri planlama ve uygulama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver için Azure sanal makineler dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Site Recovery kullanarak çok katmanlı SAP NetWeaver uygulama dağıtımını koruma](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure için SAP LaMa bağlayıcısı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure 'da bir SAP iş yükü altında HANA olmayan veritabanları hakkında bilgi için bkz.:

- [SAP iş yükü için Azure sanal makineler DBMS dağıtımına yönelik konular](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver için Azure sanal makineler DBMS dağıtımı SQL Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP iş yükü için Oracle Azure Sanal Makineler DBMS dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP iş yükü için IBM DB2 Azure Sanal Makineler DBMS dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP iş yükü için SAP ASE Azure Sanal Makineler DBMS dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM 'lerinde SAP MaxDB, canlı önbellek ve Içerik sunucusu dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure 'daki SAP HANA veritabanları hakkında daha fazla bilgi için, "Azure sanal makinelerinde SAP HANA" bölümüne bakın.

Azure 'da SAP iş yükünün yüksek kullanılabilirliği hakkında daha fazla bilgi için bkz.:

- [Azure sanal makineler SAP NetWeaver için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Bu belge, diğer çeşitli mimari ve senaryo belgelerini gösterir. Sonraki senaryo belgelerinde, farklı yüksek kullanılabilirliğe sahip yöntemlerin dağıtımını ve yapılandırmasını açıklayan ayrıntılı teknik belgelerin bağlantıları verilmiştir. SAP NetWeaver iş yükü için Linux ve Windows işletim sistemleri için yüksek kullanılabilirlik oluşturmayı ve yapılandırmayı gösteren farklı belgeler.


Azure Active Directory (Azure AD) ile SAP Hizmetleri ve çoklu oturum açma arasında tümleştirme hakkında daha fazla bilgi için bkz.:

- [Öğretici: müşteri için SAP bulutu ile Azure Active Directory tümleştirme](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Öğretici: SAP Cloud Platform Identity Authentication ile tümleştirme Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Öğretici: SAP Cloud Platform ile tümleştirme Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Öğretici: SAP NetWeaver ile tümleştirme Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Öğretici: SAP Business ByDesign ile Azure Active Directory tümleştirme](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Öğretici: SAP HANA ile tümleştirme Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA ortamınız: Fiori Launchpad SAML çoklu oturum açma Azure AD ile](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Azure hizmetlerini SAP bileşenleriyle tümleştirme hakkında daha fazla bilgi için bkz.:

- [Power BI Desktop’ta SAP HANA kullanma](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery ve SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [SAP BW Connector’ı Power BI Desktop’ta kullanma](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory, SAP HANA ve Business Warehouse veri tümleştirmesi sunuyor](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Değişiklik günlüğü
- 11/15/2019: SAP NetWeaver için [yüksek kullanılabilirliğe sahip SUSE Linux Enterprise Server, SAP uygulamaları için Azure NetApp Files](high-availability-guide-suse-netapp-files.md) ve SAP [NetWeaver için yüksek kullanılabilirliğe sahip Red Hat Enterprise Linux](high-availability-guide-rhel-netapp-files.md) SAP uygulamaları Için Azure NetApp Files ve yalnızca NFSv3 sürümünün desteklendiği ve kaldırma ifadesini açıklığa kavuşturacak şekilde destekler.
- 11/12/2019: [Azure NetApp Files (SMB) Ile Windows ÜZERINDE SAP NetWeaver Için yüksek kullanılabilirlik](high-availability-guide-windows-netapp-files-smb.md) yayını
- 11/08/2019: [SUSE Linux Enterprise Server üzerindeki Azure VM 'lerinde SAP HANA yüksek kullanılabilirlik](sap-hana-high-availability.md), [Azure sanal makinelerinde (VM) SAP HANA sistem çoğaltmasını ayarlama](sap-hana-high-availability-rhel.md), SAP [uygulamaları için SUSE Linux Enterprise Server üzerinde SAP NetWeaver için yüksek kullanılabilirlik](high-availability-guide-suse.md), Azure sanal makineler for the SUSE Linux Enterprise Server [on](high-availability-guide-suse-netapp-files.md)The SAP NetWeaver for the Azure NetApp Files [ ](high-availability-guide-rhel.md), Azure [sanal makineler Azure NetApp Files Red Hat ENTERPRISE LINUX on SAP NetWeaver için yüksek kullanılabilirlik](high-availability-guide-rhel-netapp-files.md), Azure [VM](high-availability-guide-suse-nfs.md)'lerinde Azure VM 'lerde yüksek kullanılabilirlik, Azure Standart yük dengeleyiciyi önermek için [SAP NetWeaver için Red Hat Enterprise Linux on](high-availability-guide-rhel-glusterfs.md) SUSE Linux Enterprise Server, Azure VM 'lerinde GlusterFS  
- 11/08/2019: şifreleme önerisini netleştirmek için [SAP iş yükü planlama ve dağıtım denetim listesi](sap-deployment-checklist.md) değişiklikleri  
- 11/04/2019: [Azure 'daki SUSE Linux Enterprise Server Paceyapıcısı ayarlama](high-availability-guide-suse-pacemaker.md) içindeki değişiklikler, tek noktaya yayın yapılandırmasıyla doğrudan küme oluşturmak için  
- 10/29/2019: [Azure Standart Load Balancer 'yi kullanarak sanal makineler Için genel uç nokta bağlantısının YAYıMLANMASı SAP yüksek kullanılabilirlik senaryolarında](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: [Azure sanal makine depolama yapılandırmalarının SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) değişiklikler ve Azure VM 'lerinde,/Hana/Shared BIRIMI için NFS protokolünü açıklığa kavuşturmak için [SUSE Linux Enterprise Server Azure NetApp Files ile SAP HANA birlikte bekleme düğümüyle genişleme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/22/2019: SAP [uygulamaları için SUSE Linux Enterprise Server Azure VM 'Lerde yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)değişikliği, SAP uygulamaları için [Azure NetApp Files SUSE Linux Enterprise Server Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [SUSE Linux Enterprise Server üzerindeki Azure VM 'lerinde NFS için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Azure 'Daki SUSE Linux Enterprise Server pacemaker 'ı ayarlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), Azure VM 'Lerinde [yüksek kullanılabilirliğe sahip IBM DB2 LUW, pacemaker ile SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) Azure yük dengeleyici algılama sağlamlaştırma için [SUSE Linux Enterprise Server Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure sanal makine depolama yapılandırmalarında SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) ANF bölümü ve üst bilgi bölümünü değiştirir
- 10/21/2019: [SLES 'de Azure NetApp Files Ile Azure VM 'lerinde bekleme düğümüyle SAP HANA genişleme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) sürümü
- 10/16/2019: [yedekleme ve geri yükleme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore) sırasında kopuk bağlantıları çözme
- 10/16/2019: WLES 12 SP3 'ün en düşük önerilen işletim sistemini, [SUSE Linux Enterprise Server pacemaker Ile Azure VM 'Lerinde yüksek kullanılabilirlik Ile](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) SLES 12 SP4 olarak değiştirin
- 10/11/2019: [SAP HANA Azure sanal makine depolama yapılandırmalarında](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , Ultra disk depolama yapılandırmalarının ve ANF 'ye giriş değişiklikleri
- 10/01/2019: daha fazla açıklık elde etmek için [SAP uygulamalarıyla en iyi ağ gecikmesi Için Azure yakınlık yerleştirme gruplarının](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) grafiklerde değişiklik yapın
- 10/01/2019: [Azure 'daki SAP HANA altyapı yapılandırmalarında ve işlemlerinde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) ,/Hana/sharediçin yüksek düzeyde kullanılabilir NFS paylaşımındaki deyimleri düzeltmek üzere değiştirin. 
- 09/28/2019: RHEL kümelerinde, bir Chel kümelerinde SBD 'yi açıklığa kavuşturmaya yönelik olarak [Azure 'daki Red Hat Enterprise Linux Paceyapıcısı ayarlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) sırasında değişiklik  
- 09/17/2019: SAP için VM uzantısının etrafındaki terimleri bütünleştirme NetWeaver planlama ve dağıtım kılavuzunda değiştirin  
- 08/22/2019: özel rol oluşturmaya yönelik URL 'Leri güncelleştirmek için [Azure 'daki SUSE Linux Enterprise Server pacemaker ayarlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) değişiklikleri  
- 08/16/2019: Azure ['da Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) , Azure sınır aracısının yeni sürümüne güncelleştirilüyorsam, müşterilere özel roldeki eylemleri güncelleştirme  
- 08/15/2019: [SAP HANA Azure sanal makine depolama yapılandırmalarında](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , Ultra diskin genel kullanılabilirliğini yansıtacak değişiklikler (eski adıyla Ultra SSD)
- 08/01/2019: [Azure 'daki SUSE Linux Enterprise Server pacemaker 'ı ayarlamaya](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) yönelik değişiklikler, SLES 15 için değişiklikleri özellikle tümleştirin 
- 07/23/2019: [bir Windows Yük devretme kümesindeki BIR SAP ASCS/SCS örneği kümesindeki](sap-high-availability-guide-wsfc-file-share.md) değişiklikler, Azure Site Recovery Hizmetleri tarafından doğrudan depolama alanı desteğini yansıtmak için Azure 'da bir dosya paylaşımının kullanılması
- 07/14/2019: [SAP uygulamalarıyla en iyi ağ gecikmesi Için Azure yakınlık yerleştirme gruplarının](sap-proximity-placement-scenarios.md) yayını
- 07/11/2019: Hana büyük örneklerinin 3. düzeltmesini kapsayan çeşitli belgelerdeki değişiklikler
- 07/09/2019: [Red Hat Enterprise Server 'Da IBM DB2 HADR](high-availability-guide-rhel-ibm-db2-luw.md) için yeni kılavuz sürümü
- 06/13/2019: SAP [NetWeaver için Red Hat Enterprise Linux yüksek kullanılabilirlik sürümü SAP uygulamaları için Azure NetApp Files](high-availability-guide-rhel-netapp-files.md)


