---
title: Azure sanal makinelerinde SAP Business One | Microsoft Docs
description: Azure 'da SAP Business One.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18409f93ab50f7d031ec78a55b9eaf8ad1b85a49
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101406"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>Azure Sanal Makineler'de SAP Business One
Bu belge, Azure sanal makinelerinde SAP Business One dağıtımı için rehberlik sağlar. Belgeler, SAP için Iş 'nin yükleme belgelerinin yerini almaz. Belgeler, Azure altyapısına ilişkin Iş tek uygulamaları çalıştırmak için temel planlama ve dağıtım yönergelerini kapsamalıdır.

İş biri iki farklı veritabanını destekler:
- SQL Server-Bkz. [SAP Note #928839-Microsoft SQL Server Için yayın planlaması](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA-SAP HANA için tam SAP Business One destek matrisi için [SAP ürün kullanılabilirliği matrisini](https://support.sap.com/pam) kullanıma alın

SQL Server, [SAP NetWeaver Için Azure sanal MAKINELER DBMS dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) 'nda belgelendiği gibi temel dağıtım konuları geçerlidir. SAP HANA için bu belgede dikkat edilecek noktalar bahsedilir.

## <a name="prerequisites"></a>Önkoşullar
Bu kılavuzu kullanmak için aşağıdaki Azure bileşenleriyle temel bilgilere ihtiyacınız vardır:

- [Windows üzerinde Azure sanal makineleri](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Linux üzerinde Azure sanal makineleri](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [PowerShell ile Azure ağ iletişimi ve sanal ağlar yönetimi](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [CLı ile Azure ağı ve sanal ağlar](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure CLı ile Azure disklerini yönetme](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Yalnızca iş ile ilgileniyor olsanız bile, [SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) belgeleri iyi bir bilgi kaynağı olabilir.

Bu varsayımı, SAP Işletmasının dağıtıldığı bir örnek olarak kullanılır:

- VM gibi belirli bir altyapıya SAP HANA yükleme hakkında bilgi sahibi
- SAP Business One uygulamasını Azure VM 'Leri gibi bir altyapıya yükleme
- İşletim SAP Business One ve seçilen DBMS sistemi hakkında bilgi sahibi
- Azure 'da altyapı dağıtımı hakkında bilgi edinin

Tüm bu alanların bu belgede kapsanmayacak.

Azure belgelerinin yanı sıra, Iş için SAP 'den bir veya iş için merkezi notlar olan ana SAP notlarını bilmeniz gerekir:

- [528296-genel bakış, SAP Business One yayınları ve Ilgili ürünler için](https://launchpad.support.sap.com/#/notes/528296)
- [2216195-sürüm güncelleştirmeleri için SAP Business One 9,2, sürüm SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583-SAP Business One 9,3 için merkezi](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615-yayın güncelleştirmeleri için SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595-SAP Business One 9,3 genel sorunlar için toplu Not](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458-SAP HANA Ilgili SAP Business One konuları için toplu danışmanlık, SAP HANA sürümü](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>İş One mimarisi
İş, iki katmana sahip bir uygulamadır:

- ' FAT ' istemcisiyle istemci katmanı
- Bir kiracının veritabanı şemasını içeren bir veritabanı katmanı

İstemci bölümünde hangi bileşenlerin çalıştığını ve sunucu bölümünde çalışmakta olan parçaların [SAP Business One Yönetici Kılavuzu](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 'nda belgelendiği daha iyi bir genel bakış 

İstemci katmanı ve DBMS katmanı arasında ağır gecikme süresi açısından kritik bir etkileşim olduğundan, her iki katmanın Azure 'da dağıtma sırasında Azure 'da bulunması gerekir. Kullanıcıların, Iş tek istemci bileşenleri için bir RDS hizmetini çalıştıran bir veya birden çok VM 'de RDS.

### <a name="sizing-vms-for-sap-business-one"></a>SAP Iş için VM 'Leri boyutlandırma

İstemci VM 'leri boyutlandırmasıyla ilgili olarak, kaynak gereksinimleri, belge [SAP Iş bir donanım gereksinimleri Kılavuzu](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)'nda SAP tarafından belgelenmiştir. Azure için, belge Bölüm 2,4 ' de belirtilen gereksinimlere odaklanmanız ve hesaplamanız gerekir.

Iş tek istemci bileşenlerini ve DBMS konağını barındırmak için Azure sanal makineleri olarak yalnızca SAP NetWeaver destekleniyor olan VM 'Lere izin verilir. SAP NetWeaver desteklenen Azure VM 'lerinin listesini bulmak için [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533)makalesini okuyun.

Iş için DBMS arka ucu olarak SAP HANA çalıştırmak, yalnızca Hana [sertifikası](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) 'nda Hana 'da iş Için listelenen VM 'ler için desteklenir. Iş One istemci bileşenleri, SAP HANA DBMS sistemi olarak bu daha güçlü kısıtlamadan etkilenmez.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>SAP Business One için kullanılacak işletim sistemi sürümleri

İlke ' de en son işletim sistemi sürümlerini kullanmak her zaman en iyisidir. Özellikle Linux alanında yeni Azure işlevselliği, SUSE ve Red Hat 'in farklı, son küçük sürümleriyle birlikte sunulmuştur. Windows tarafında, Windows Server 2016 kullanılması önemle önerilir.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Azure 'da SAP Business One için altyapı dağıtma
Sonraki birkaç bölümde SAP dağıtmaya yönelik altyapı parçaları.

### <a name="azure-network-infrastructure"></a>Azure ağ altyapısı
Azure 'da dağıtmanız gereken ağ altyapısı, kendiniz için tek bir Iş sistemi dağıtdığınıza bağlıdır. Ya da müşteriler için düzinelerce Iş için bir sistem barındıran bir barındırıcı olup olmadığı. Ayrıca, Azure 'a nasıl bağlanabileceğini tasarımda küçük değişiklikler de olabilir. Azure 'a VPN bağlantınız olan ve Active Directory [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) veya [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) aracılığıyla Azure 'a genişleten bir tasarıma sahip farklı olasılıklardan ilerleyeceğiz.

![Iş ile basit ağ yapılandırması](./media/business-one-azure/simple-network-with-VPN.PNG)

Sunulan Basitleştirilmiş yapılandırma, yönlendirmeyi denetlemeye ve sınırlamaya izin veren çeşitli güvenlik örnekleri sunar. İle başlar 

- Müşterinin Şirket içi tarafında yönlendirici/güvenlik duvarı.
- Sonraki örnek, içinde SAP Business One yapılandırmanızı çalıştırdığınız Azure VNet 'in yönlendirme ve güvenlik kurallarını tanıtmak için kullanabileceğiniz [Azure ağ güvenlik grubudur](https://docs.microsoft.com/azure/virtual-network/security-overview) .
- Iş kolu kullanıcılarının, veritabanını çalıştıran Iş tek bir sunucuyu çalıştıran sunucuyu görebilmesi için, iş bir istemciyi ve iş tek sunucusunu barındıran VM 'yi VNet 'in içindeki iki farklı alt ağda ayırmanız gerekir.
- Iş tek sunucuya erişimi sınırlandırmak için, Azure NSG 'yi iki farklı alt ağa yeniden atandı.

Azure ağ yapılandırması 'nın daha karmaşık bir sürümü, [hub ve bağlı bileşen mimarisinin Azure tarafından belgelenen en iyi yöntemlerini](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)temel alır. Hub ve bağlı bileşen mimari mimarisi, ilk Basitleştirilmiş yapılandırmayı şöyle bir şekilde değiştirir:


![Iş ile hub ve bağlı bileşen yapılandırması](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Kullanıcıların Azure 'a özel bağlantı olmadan Internet üzerinden bağlandığı durumlar için, Azure 'da ağ tasarımı, Azure ile DMZ arasında Azure başvuru mimarisinde belgelenen ilkelere göre hizalanmalıdır [. Internet](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>İş tek veritabanı sunucusu
Veritabanı türü için SQL Server ve SAP HANA kullanılabilir. DBMS 'den bağımsız olarak, Azure VM 'Leri [Için Azure sanal MAKINELER DBMS dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) ve ilgili ağ ve depolama konuları hakkında genel bilgi edinmek IÇIN, SAP iş yüküne yönelik belge konularını okumalısınız.

Yalnızca belirli ve genel veritabanı belgelerinde vurgulandı olsa da şunları öğrenmelisiniz:

- Azure ['Da Windows sanal makinelerinin kullanılabilirliğini yönetme](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) ve [Linux sanal makinelerinin Azure 'da kullanılabilirliğini yönetme](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [Sanal makineler için SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Bu belgeler, depolama türleri ve yüksek kullanılabilirlik yapılandırması seçimine karar vermenize yardımcı olmalıdır.

İlke ' de şunları yapmalısınız:

- Standart HDD 'ler üzerinden Premium SSD 'leri kullanın. Kullanılabilir disk türleri hakkında daha fazla bilgi edinmek için bkz. makalemiz [bir disk türü seçin](../../windows/disks-types.md)
- Yönetilmeyen diskler üzerinde Azure yönetilen disklerini kullanma
- Disk yapılandırmanızla yapılandırılmış yeterli ıOPS ve g/ç aktarım hızına sahip olduğunuzdan emin olun
- Maliyetli bir depolama yapılandırması sağlamak için/Hana/Data ve/Hana/log birimini birleştirin


#### <a name="sql-server-as-dbms"></a>DBMS olarak SQL Server
Iş için DBMS olarak SQL Server dağıtmak için, [SAP NetWeaver Için Azure sanal MAKINELER DBMS dağıtımı SQL Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)belgeye gidin. 

SQL Server için DBMS tarafı için kaba boyutlandırma tahminleri şunlardır:

| Kullanıcı sayısı | vCPU sayısı | Bellek | Örnek VM türleri |
| --- | --- | --- | --- |
| en fazla 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| 40 kadar | 8 | 32 GB | D8s_v3, E8s_v3 |
| 80 kadar | 16 | 64 GB | D16s_v3, E16s_v3 |
| 150 kadar | 32 | 128 GB | D32s_v3, E32s_v3 |

Yukarıda listelenen boyut, ile başlamak için bir fikir vermelidir. Daha az veya daha fazla kaynağa ihtiyacınız olabilir, bu durumda Azure üzerinde bir uyumluluk daha kolay olur. VM türleri arasında bir değişiklik, yalnızca VM 'nin yeniden başlatılmasına olanak tanır.

#### <a name="sap-hana-as-dbms"></a>DBMS olarak SAP HANA
SAP HANA DBMS olarak kullanarak aşağıdaki bölümlerde, [Azure işlemler Kılavuzu 'ndaki belge SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)ilgili hususları izlemeniz gerekir.

Azure 'da tek bir Iş için veritabanı olarak SAP HANA bir yüksek kullanılabilirlik ve olağanüstü durum kurtarma yapılandırması için, [Azure sanal makineler için yüksek kullanılabilirlik SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) ve bu belgeden ulaşılan belgeler için belgeleri okumanız gerekir .

SAP HANA yedekleme ve geri yükleme stratejileri için, [Azure sanal makinelerinde SAP HANA belge yedekleme kılavuzunu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) ve bu belgeden işaret edilen belgeleri okumanız gerekir.

 
### <a name="business-one-client-server"></a>İş tek istemci sunucusu
Bu bileşenler için depolama konuları birincil sorun değildir. Bununla birlikte, güvenilir bir platforma sahip olmak istiyorsunuz. Bu nedenle, temel VHD için bile bu sanal makine için Azure Premium Depolama kullanmanız gerekir. VM 'yi, [SAP Business One donanım gereksinimleri kılavuzunda](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)verilen verilerle boyutlandırma. Azure için, belge Bölüm 2,4 ' de belirtilen gereksinimlere odaklanmanız ve hesaplamanız gerekir. Gereksinimleri hesaplarken, sizin için ideal VM 'yi bulmak için bunları aşağıdaki belgelere göre karşılaştırmanız gerekir:

- [Azure'daki Windows sanal makinesi boyutları](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Microsoft tarafından belgelenme için gereken CPU ve bellek sayısını karşılaştırın. Ayrıca, VM 'Leri seçerken ağ aktarım hızını göz önünde bulundurun.








