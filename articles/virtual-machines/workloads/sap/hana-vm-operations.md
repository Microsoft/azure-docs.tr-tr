---
title: Azure 'da altyapı yapılandırma ve işlemlerini SAP HANA | Microsoft Docs
description: Azure sanal makinelerine dağıtılan SAP HANA sistemleri için işlemler Kılavuzu.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4c27895c5163d59ca785aa15fa3739359e5be457
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666602"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Azure'da SAP HANA altyapı yapılandırmaları ve işlemleri
Bu belgede, Azure yerel sanal makinelerinde (VM 'Ler) dağıtılan Azure altyapısını ve işletim SAP HANA sistemlerini yapılandırmaya yönelik yönergeler sağlanmaktadır. Belge ayrıca, M128s VM SKU 'SU için SAP HANA genişleme için yapılandırma bilgilerini içerir. Bu belge, aşağıdaki içeriği içeren standart SAP belgelerinin yerine geçecek şekilde tasarlanmamıştır:

- [SAP Yönetim Kılavuzu](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP Yükleme Kılavuzu](https://service.sap.com/instguides)
- [SAP notları](https://service.sap.com/notes)

## <a name="prerequisites"></a>Önkoşullar
Bu kılavuzu kullanmak için aşağıdaki Azure bileşenleriyle temel bilgilere ihtiyacınız vardır:

- [Azure sanal makineleri](../../linux/tutorial-manage-vm.md)
- [Azure ağ iletişimi ve sanal ağlar](../../linux/tutorial-virtual-network.md)
- [Azure Depolama](../../linux/tutorial-manage-disks.md)

Azure 'da SAP NetWeaver ve diğer SAP bileşenleri hakkında daha fazla bilgi edinmek için [Azure belgelerinin](../../../index.yml) [Azure 'da SAP](./get-started.md) bölümüne bakın.

## <a name="basic-setup-considerations"></a>Temel kurulum konuları
Aşağıdaki bölümlerde, Azure VM 'lerinde SAP HANA sistemleri dağıtmaya yönelik temel kurulum konuları açıklanır.

### <a name="connect-into-azure-virtual-machines"></a>Azure sanal makinelerine bağlanma
[Azure sanal makineler planlama kılavuzunda](./planning-guide.md)belgelendiği gibi, Azure VM 'lerine bağlanmak için iki temel yöntem vardır:

- Bir geçiş VM 'sinde veya SAP HANA çalıştıran VM 'de internet ve genel uç noktalar üzerinden bağlanın.
- [VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md) veya Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)üzerinden bağlanın.

Üretim senaryolarında VPN veya ExpressRoute aracılığıyla siteden siteye bağlantı gereklidir. Bu bağlantı türü, SAP yazılımının kullanıldığı üretim senaryolarında akışı yapılan üretim dışı senaryolar için de gereklidir. Aşağıdaki görüntüde çapraz site bağlantısı örneği gösterilmektedir:

![Çapraz site bağlantısı](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure VM türlerini seçin
Üretim senaryolarında kullanılabilecek Azure sanal makine türleri, [ıAAS Için SAP belgelerinde](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)listelenmiştir. Üretim dışı senaryolar için, çok çeşitli yerel Azure VM türleri mevcuttur.

>[!NOTE]
> Üretim dışı senaryolar için [#1928533 SAP Note](https://launchpad.support.sap.com/#/notes/1928533)' da listelenen VM türlerini kullanın. Azure VM 'lerinin üretim senaryolarında kullanımı için, SAP yayımlanmış [sertifikalı IaaS platformları listesinde](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)SAP HANA sertifikalı VM 'ler olup olmadığını denetleyin.

Kullanarak VM 'Leri Azure 'da dağıtın:

- Azure portalı.
- Azure PowerShell cmdlet 'leri.
- Azure CLı.

Ayrıca, Azure VM Hizmetleri üzerinde, tamamlanmış bir SAP HANA platformunu [SAP bulut platformu](https://cal.sap.com/)aracılığıyla dağıtabilirsiniz. Yükleme işlemi, [Azure 'DA SAP S/4HANA veya siyah beyaz](./cal-s4h.md) [](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)

>[!IMPORTANT]
> M208xx_v2 VM 'Leri kullanabilmeniz için, Azure VM görüntüsü galerisinden Linux görüntünüzü seçerken dikkatli olmanız gerekir. Ayrıntıları okumak için [bellek için iyileştirilmiş sanal makine boyutları](../../mv2-series.md)makalesini okuyun.
> 


### <a name="storage-configuration-for-sap-hana"></a>SAP HANA için depolama yapılandırması
Azure 'da SAP HANA birlikte kullanılacak depolama yapılandırmalarının ve depolama türlerinin [Azure sanal makine depolama yapılandırması SAP HANA](./hana-vm-operations-storage.md) belgeyi okuyun


### <a name="set-up-azure-virtual-networks"></a>Azure sanal ağlarını ayarlama
VPN veya ExpressRoute aracılığıyla Azure 'da siteden siteye bağlantınız olduğunda, VPN veya ExpressRoute devresine bir sanal ağ geçidi üzerinden bağlanmış en az bir Azure sanal ağınızın olması gerekir. Basit dağıtımlarda, sanal ağ geçidi, SAP HANA örneklerini barındıran Azure sanal ağının (VNet) bir alt ağında dağıtılabilir. SAP HANA yüklemek için Azure sanal ağı 'nda iki ek alt ağ oluşturursunuz. Bir alt ağ, SAP HANA örneklerini çalıştırmak için VM 'Leri barındırır. Diğer alt ağ, SAP HANA Studio 'yu, diğer yönetim yazılımlarını veya uygulama yazılımınızı barındırmak için JumpBox veya yönetim sanal makineleri çalıştırır.

> [!IMPORTANT]
> İşlevsellik dışında, ancak performans nedenlerinden daha önemlisi, SAP NetWeaver, Hybru veya S/4HANA tabanlı SAP sisteminin DBMS katmanı arasındaki iletişim yolundaki [Azure ağ sanal gereçlerini](https://azure.microsoft.com/solutions/network-appliances/) yapılandırmak desteklenmez. SAP uygulama katmanı ve DBMS katmanı arasındaki iletişimin doğrudan bir tane olması gerekir. Bu ASG ve NSG kuralları doğrudan iletişime izin vermedikçe, kısıtlama [Azure ASG ve NSG kurallarını](../../../virtual-network/network-security-groups-overview.md) içermez. [SAP uygulamalarında SUSE Linux Enterprise Server Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirlik](./high-availability-guide-suse.md)bölümünde açıklandığı gibi, NVA 'lar ' nin desteklenmediği daha fazla senaryo, Linux Paceyapıcısı küme düğümlerini ve SBD cihazlarını temsil eden Azure VM 'ler arasında iletişim yollarıdır. Ya da Azure VM 'Ler ile Windows Server SOFS arasındaki iletişim yollarında, [Azure 'da bir dosya paylaşımının kullanıldığı bir Windows Yük devretme KÜMESINDE SAP ASCS/SCS örneği kümesinde](./sap-high-availability-guide-wsfc-file-share.md)açıklandığı şekilde ayarlanır. İletişim yollarındaki NVA 'lar iki iletişim ortağı arasındaki ağ gecikmesini kolayca çift açabilir, SAP uygulama katmanı ve DBMS katmanı arasındaki kritik yollarda üretilen işi kısıtlayabilir. Müşterilerin gözlemlediği bazı senaryolarda NVA 'lar, Linux Paceyapıcısı küme düğümleri arasındaki iletişimin bir NVA üzerinden SBD cihazlarıyla iletişim kurması gereken durumlarda pacemaker Linux kümelerinin başarısız olmasına neden olabilir.  
> 

> [!IMPORTANT]
> Desteklenmeyen başka bir tasarım, SAP uygulama katmanının ve DBMS [katmanının birbirleriyle](../../../virtual-network/virtual-network-peering-overview.md) **eşlenmez farklı** Azure sanal ağlarına ayrılabilir. Farklı Azure sanal ağları kullanmak yerine, bir Azure sanal ağı içindeki alt ağları kullanarak SAP uygulama katmanını ve DBMS katmanını ayırt etmek önerilir. Öneriyi izlemeden ve bunun yerine iki katmanı farklı bir sanal ağa [ayırmayı tercih ediyorsanız](../../../virtual-network/virtual-network-peering-overview.md), iki sanal ağın eşlenmiş olması gerekir. [İki eşlenen](../../../virtual-network/virtual-network-peering-overview.md) Azure sanal ağı arasındaki ağ trafiğinin aktarım maliyetlerinin konusu olduğunu unutmayın. SAP uygulama katmanı ve DBMS katmanı arasında çok sayıda Terabaytdaki çok büyük veri hacmi, iki eşlenmiş Azure sanal ağı arasında ayrım yapıbilirse, önemli maliyetler toplanabilir. 

SAP HANA çalıştırmak için VM 'Leri yüklediğinizde, VM 'Ler şunlar için gereklidir:

- Yüklü iki sanal NIC: Yönetim alt ağına bağlanmak için bir NIC ve şirket içi ağ veya diğer ağlardan bağlantı kurmak için bir NIC, Azure VM 'deki SAP HANA örneğine.
- Her iki sanal NIC için dağıtılan statik özel IP adresleri.

> [!NOTE]
> Ayrı sanal NIC 'ler için Azure aracılığıyla statik IP adresleri atamanız gerekir. Konuk işletim sistemi içinde bir vNIC 'e statik IP adresleri atamamalısınız. Azure Backup hizmeti gibi bazı Azure Hizmetleri, en azından birincil vNIC 'nin statik IP adreslerine değil, DHCP olarak ayarlandığı gerçeğini temel alır. Ayrıca bkz. belge [Azure sanal makine yedeklemesi sorunlarını giderme](../../../backup/backup-azure-vms-troubleshoot.md#networking). Bir VM 'ye birden çok statik IP adresi atamanız gerekiyorsa, bir VM 'ye birden fazla vNIC atamanız gerekir.
>
>

Ancak, bir yandan, Azure 'da bir sanal veri merkezi ağ mimarisi oluşturmanız gerekir. Bu mimari, şirket içinde ayrı bir Azure VNet 'e bağlanan Azure VNet ağ geçidinin ayrılmasını önerir. Bu ayrı VNet, şirket içinde veya internet 'e giden tüm trafiği barındırmalıdır. Bu yaklaşım, bu ayrı hub VNet 'e Azure 'daki sanal veri merkezini giren denetim ve günlük trafik için yazılım dağıtmanıza olanak tanır. Bu nedenle, Azure dağıtımınıza yönelik gelen ve giden trafikle ilgili tüm yazılım ve yapılandırmaların bulunduğu bir sanal ağınız vardır.

[Azure sanal veri merkezi makaleleri: bir ağ perspektifi](/azure/architecture/vdc/networking-virtual-datacenter) ve [Azure sanal veri merkezi ve kurumsal denetim düzlemi](/azure/architecture/vdc/) , sanal veri merkezi yaklaşımı ve ilgili Azure VNET tasarımı hakkında daha fazla bilgi sağlar.


>[!NOTE]
>[Azure VNET eşlemesi](../../../virtual-network/virtual-network-peering-overview.md) kullanan bir hub VNET ve bağlı ağ VNET arasında akan trafik, ek [maliyetlerin](https://azure.microsoft.com/pricing/details/virtual-network/)konusudur. Bu maliyetlere bağlı olarak, katı bir hub ve bağlı bileşen ağı tasarımı çalıştırıp VNet eşlemesini atlamak için ' bağlı bileşenleri ' ile bağlandığınız birden çok [Azure ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md) Gateway 'i çalıştırmayı göz önünde bulundurmanız gerekebilir. Ancak, Azure ExpressRoute ağ geçitleri ek [maliyetler](https://azure.microsoft.com/pricing/details/vpn-gateway/) de sunar. Ayrıca, ağ trafiği günlüğü, denetim ve izleme için kullandığınız üçüncü taraf yazılımlar için ek maliyetlerle karşılaşabilirsiniz. Bir taraftaki VNet eşlemesi ve ek Azure ExpressRoute ağ geçitleri ve ek yazılım lisansları tarafından oluşturulan maliyetler ile veri değişimi maliyetlerine bağlı olarak, alt ağları sanal ağlar yerine yalıtım birimi olarak kullanarak bir VNet içinde mikro kesimlemeye karar verebilirsiniz.


IP adreslerini atamaya yönelik farklı yöntemlere genel bir bakış için bkz. [Azure 'Da IP adresi türleri ve ayırma yöntemleri](../../../virtual-network/public-ip-addresses.md). 

SAP HANA çalıştıran VM 'Ler için, atanan statik IP adresleriyle çalışmanız gerekir. Nedeni, HANA başvuru IP adreslerine yönelik bazı yapılandırma öznitelikleridir.

[Azure ağ güvenlik grupları (NSG 'ler)](../../../virtual-network/virtual-network-vnet-plan-design-arm.md) , SAP HANA örneğine veya atlama kutusuna yönlendirilen trafiği yönlendirmek için kullanılır. NSG 'ler ve sonuç olarak [uygulama güvenlik grupları](../../../virtual-network/network-security-groups-overview.md#application-security-groups) , SAP HANA alt ağı ve yönetim alt ağıyla ilişkilendirilir.

Aşağıdaki görüntüde, hub ve bağlı ağ VNet mimarisine göre SAP HANA için kaba bir dağıtım şemasına genel bakış gösterilmektedir:

![SAP HANA için kaba dağıtım şeması](media/hana-vm-operations/hana-simple-networking-dmz.png)

Siteden siteye bağlantı olmadan Azure 'da SAP HANA dağıtmak için, SAP HANA örneğini genel İnternet 'ten korumak ve ileri bir proxy 'nin arkasında gizlemek istersiniz. Bu temel senaryoda dağıtım, ana bilgisayar adlarını çözümlemek için Azure yerleşik DNS hizmetlerini kullanır. Genel kullanıma yönelik IP adreslerinin kullanıldığı daha karmaşık bir dağıtımda, Azure yerleşik DNS hizmetleri özellikle önemlidir. Azure NSG 'leri ve [Azure NVA 'lar](https://azure.microsoft.com/solutions/network-appliances/) 'i kullanarak Azure 'Daki Azure VNET mimarinize internet 'ten yönlendirmeyi izleyin. Aşağıdaki görüntüde, hub ve bağlı ağ VNet mimarisinde siteden siteye bağlantı olmadan SAP HANA dağıtmaya yönelik kaba bir şema gösterilmektedir:
  
![Siteden siteye bağlantı olmadan SAP HANA için kaba dağıtım şeması](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Hub ve bağlı ağ VNet mimarisi olmadan Internet 'ten erişimi denetlemek ve izlemek için Azure NVA 'lar 'ın nasıl kullanılacağına ilişkin başka bir açıklama, [yüksek oranda kullanılabilir ağ sanal gereçlerini dağıtma](/azure/architecture/reference-architectures/dmz/nva-ha)makalesinde bulunabilir.


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Azure altyapısını SAP HANA genişleme için yapılandırma
OLAP genişleme veya S/4HANA genişleme için sertifikalı Azure VM türlerini bulmak için [SAP HANA donanım dizinini](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)denetleyin. ' Kümeleme ' sütunundaki onay işareti, genişleme desteğini gösterir. Uygulama türü, OLAP ölçeği genişletme veya S/4HANA ölçeklendirmesinin desteklenip desteklenmediğini gösterir. VM 'lerin her biri için ölçek genişletme konusunda sertifikalı düğümlerle ilgili ayrıntılar için SAP HANA donanım dizininde listelenen belirli VM SKU 'sunda girişlerin ayrıntılarını kontrol edin.

Azure VM 'lerinde genişleme yapılandırması dağıtmak için en düşük işletim sistemi sürümleri, SAP HANA donanım dizininde listelenen belirli VM SKU 'sunda girişlerin ayrıntılarını kontrol edin. N düğümlü bir OLAP genişleme yapılandırması olan bir düğüm, ana düğüm olarak işlev görür. Sertifika sınırına kadar olan diğer düğümler, çalışan düğümü olarak davranır. Ek bekleme düğümleri, sertifikalı düğüm sayısına göre sayılmaz

>[!NOTE]
> Bekleme düğümüyle SAP HANA Azure VM genişleme dağıtımları yalnızca [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) depolama alanı kullanılarak mümkündür. Diğer SAP HANA sertifikalı Azure depolama, SAP HANA bekleme düğümlerinin yapılandırılmasına izin vermez
>

/Hana/Shared için [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)kullanımını da öneririz. 

Bir genişleme yapılandırmasındaki tek bir düğüm için tipik bir temel tasarım şöyle görünecektir:

![Genişleme yapılandırmasındaki tek bir düğüm için tipik bir temel tasarımı gösteren diyagram.](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

SAP HANA ölçeği genişletme için bir VM düğümünün temel yapılandırması şöyle görünür:

- **/Hana/Shared** için, Azure NetApp Files aracılığıyla SAĞLANMıŞ yerel NFS hizmetini kullanırsınız. 
- Diğer tüm disk birimleri farklı düğümler arasında paylaşılmaz ve NFS tabanlı değildir. Bu belgede daha sonra paylaşılmayan **/Hana/Data** ve **/Hana/log** ile genişleme Hana yüklemelerine yönelik yükleme yapılandırma ve adımları daha sonra sunulmaktadır. Kullanılabilecek HANA sertifikalı depolama için [Azure sanal makine depolama yapılandırmalarının SAP HANA](./hana-vm-operations-storage.md)makalesine bakın.


Birimleri veya diskleri boyutlandırıyorsanız, gereken boyut için çalışan düğüm sayısına bağlı olarak belge [SAP HANA TDI depolama gereksinimleri](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)' ni denetlemeniz gerekir. Belge, birimin gerekli kapasitesini almak için uygulamanız gereken bir formülü serbest bırakır

Bir genişleme SAP HANA sanal makinesi için tek düğümlü bir VM 'nin grafiklerde görüntülenen diğer tasarım ölçütleri VNet 'dir veya alt ağ yapılandırması daha iyidir. SAP, HANA düğümleri arasındaki iletişimlerden istemci/uygulamayla bağlantılı trafiğin ayrılmasını çok önerir. Grafiklerde gösterildiği gibi bu hedef, VM 'ye bağlı iki farklı vNIC varsa elde edilir. Her iki vNIC de farklı alt ağlardaki iki farklı IP adresine sahiptir. Daha sonra NSG 'ler veya Kullanıcı tanımlı yollar kullanarak yönlendirme kurallarıyla trafik akışını kontrol edersiniz.

Özellikle Azure 'da, belirli sanal NIC 'lerde hizmet ve kota kalitesi sağlamak için bir yol ve yöntem yoktur. Sonuç olarak, istemci/uygulama ile karşılıklı ve düğüm içi iletişimin ayrımı, bir trafik akışının diğer üzerinden önceliklerini belirlemek için herhangi bir fırsat açmaz. Bunun yerine ayrım, genişleme yapılandırmalarının düğüm içi iletişimleriyle koruma için güvenlik ölçüsü olarak kalır.  

>[!NOTE]
>SAP, bu belgede açıklandığı gibi, ağ trafiğini istemci/uygulama tarafına ve düğüm içi trafiğe ayırmayı önerir. Bu nedenle, son grafiklerde gösterildiği gibi bir mimarinin yerinde yerleştirilmesi önerilir. Ayrıca, öneriden sapmanız gereken gereklilikler için güvenlik ve uyumluluk ekibinize başvurun 
>

Bir ağ noktasından, gereken en düşük ağ mimarisi şöyle görünür:

![Tek bir düğümün ölçek genişletme temelleri](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>SAP HANA genişleme 'yi yükleme n Azure
Genişleme SAP yapılandırması yükleme, şu adımları gerçekleştirmeniz gerekir:

- Mevcut bir Azure VNet altyapısını yeni veya uygun hale dağıtma
- Azure yönetilen Premium Depolama, Ultra disk birimleri ve/veya NFS birimlerini ANF tabanlı kullanarak yeni VM 'Leri dağıtma
- - Ağ yönlendirmeyi, örneğin VM 'Ler arasındaki düğüm içi iletişimin bir [NVA](https://azure.microsoft.com/solutions/network-appliances/)üzerinden yönlendirilmediğinden emin olmak için uyarlayın. 
- SAP HANA ana düğümünü yükler.
- SAP HANA ana düğümünün yapılandırma parametrelerini uyarlayın
- SAP HANA çalışan düğümlerinin yüklenmesine devam edin

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Genişleme yapılandırmasında SAP HANA yüklemesi
Azure VM altyapınız dağıtıldığında ve diğer tüm hazırlıklar yapıldıktan sonra, aşağıdaki adımlarda SAP HANA genişleme yapılandırmasını yüklemeniz gerekir:

- SAP HANA ana düğümünü SAP belgelerine göre yükler
- Azure Premium Storage veya/Hana/Data ve/Hana/log olmayan diskler ile ultra disk depolaması kullanmak durumunda global.ini dosyasını değiştirmeniz ve ' basepath_shared = No ' parametresini global.ini dosyasına eklemeniz gerekir. Bu parametre SAP HANA, düğümler arasında ' Shared ' **/Hana/Data** ve **/Hana/log** birimleri olmadan ölçek genişletme içinde çalışmasına olanak sağlar. Ayrıntılar [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991)bölümünde belgelenmiştir. /Hana/Data ve/Hana/log için ANF tabanlı NFS birimleri kullanıyorsanız, bu değişikliği yapmanız gerekmez
- global.ini parametresindeki nihai değişiklikten sonra, SAP HANA örneğini yeniden başlatın
- Ek çalışan düğümleri ekleyin. Ayrıca bkz <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html> .. Yükleme sırasında veya daha sonra (örneğin, yerel hdblcm) düğümler arası iletişim SAP HANA iç ağı belirtin. Daha ayrıntılı belgeler için Ayrıca bkz. [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

SUSE Linux 'ta bekleme moduna sahip bir SAP HANA genişleme sistemi ayarlama ayrıntıları, [SUSE Linux Enterprise Server üzerinde Azure NetApp Files kullanarak Azure VM 'lerinde bekleme moduna sahip bir SAP HANA genişleme sistemi dağıtma](./sap-hana-scale-out-standby-netapp-files-suse.md)konusunda ayrıntılı olarak açıklanmıştır. Red hat için eşdeğer belgeler, [Red Hat Enterprise Linux Azure NetApp Files kullanarak Azure VM 'lerinde bekleme moduna sahip bir SAP HANA genişleme sistemi dağıtma](./sap-hana-scale-out-standby-netapp-files-rhel.md)makalesinde bulunabilir. 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Azure sanal makineler için dinamik katmanlama 2,0 SAP HANA

Azure d serisi VM 'lerde SAP HANA sertifikalarına ek olarak SAP HANA, dinamik katmanlama 2,0 Microsoft Azure de desteklenir (bkz. SAP HANA dinamik katmanlama belgeleri bağlantıları daha sonra). Ürünü yükleme veya çalıştırma konusunda herhangi bir farklılık olmasa da, örneğin Azure sanal makinesi içinde SAP HANA kokpiti aracılığıyla Azure 'da resmi destek için zorunlu olan birkaç önemli öğe vardır. Bu anahtar noktaları aşağıda açıklanmıştır. Makalenin tamamında, "DT 2,0" kısaltması, dinamik katmanlama 2,0 tam adı yerine kullanılacaktır.

SAP HANA dinamik katmanlama 2,0 SAP BW veya S4HANA tarafından desteklenmez. Ana kullanım örnekleri artık yerel HANA uygulamalardır.


### <a name="overview"></a>Genel Bakış

Aşağıdaki resimde Microsoft Azure üzerindeki DT 2,0 desteğiyle ilgili bir genel bakış sunulmaktadır. Resmi sertifikaya uymak için izlenmesi gereken zorunlu gereksinimlerin bir kümesi vardır:

- DT 2,0, adanmış bir Azure VM üzerinde yüklü olmalıdır. SAP HANA çalıştığı sanal makinede çalışmayabilir
- SAP HANA ve DT 2,0 VM 'Lerin aynı Azure VNET içinde dağıtılması gerekir
- SAP HANA ve DT 2,0 VM 'lerinin Azure hızlandırılmış ağ etkinleştirilmiş olarak dağıtılması gerekir
- DT 2,0 VM 'Leri için depolama türü Azure Premium Depolama olmalıdır
- DT 2,0 VM 'ye birden çok Azure diski eklenmelidir
- Azure disklerinde şeritleme kullanarak bir yazılım RAID/şeritli birim (LVM veya mdaddm aracılığıyla) oluşturmak için gereklidir

Daha fazla ayrıntı aşağıdaki bölümlerde açıklanacaktır.

![SAP HANA DT 2,0 mimarisine genel bakış](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>SAP HANA DT 2,0 için adanmış Azure VM

Azure IaaS 'de, DT 2,0 yalnızca adanmış bir VM 'de desteklenir. HANA örneğinin çalıştığı Azure sanal makinesinde DT 2,0 ' i çalıştırmasına izin verilmez. Başlangıçta iki VM türü, SAP HANA DT 2,0 çalıştırmak için kullanılabilir:

- M64-32ms 
- E32sv3 

VM türü açıklamasına [buraya](../../sizes-memory.md) bakın

, Maliyetleri kaydetmek için "ısınma" verilerinin boşaltımı olduğu ve bunlara karşılık gelen VM boyutlarının kullanılması mantıklı olduğu için DT 2,0 temel fikri verilme aşamasında. Olası birleşimlerle ilgili katı bir kural yoktur. Bu, belirli müşteri iş yüküne bağlıdır.

Önerilen yapılandırma şöyle olacaktır:

| SAP HANA VM türü | DT 2,0 VM türü |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Desteklenen DT 2,0 VM 'Leri (M64-32ms ve E32sv3) olan SAP HANA sertifikalı M serisi sanal makinelerin tüm birleşimleri olasıdır.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure ağ iletişimi ve SAP HANA DT 2,0

Özel bir VM 'ye DT 2,0 ' nin yüklenmesi için, DT 2,0 VM ve en az 10 GB SAP HANA VM arasında ağ işleme gerekir. Bu nedenle, tüm VM 'Leri aynı Azure sanal ağı içinde yerleştirmeniz ve Azure hızlandırılmış ağını etkinleştirmeniz zorunludur.

Azure hızlandırılmış ağ hakkında daha fazla bilgiyi [burada bulabilirsiniz](../../../virtual-network/create-vm-accelerated-networking-cli.md)

### <a name="vm-storage-for-sap-hana-dt-20"></a>SAP HANA DT 2,0 için VM depolaması

DT 2,0 en iyi uygulama yönergelerine göre disk GÇ verimlilik, fiziksel çekirdek başına en az 50 MB/sn olmalıdır. DT 2,0 için desteklenen iki Azure VM türünün belirtimine bakarak, sanal makine için en fazla disk GÇ verimlilik limiti şöyle görünür:

- E32sv3:768 MB/sn (önbelleğe alınmamış) Bu, fiziksel çekirdek başına 48 MB/sn oranını gösterir
- M64-32ms: 1000 MB/sn (önbelleğe alınmamış), fiziksel çekirdek başına 62,5 MB/sn oranını gösterir

Birden çok Azure diskini DT 2,0 sanal makinesine iliştirmek ve VM başına disk aktarım hızı üst sınırına ulaşmak için işletim sistemi düzeyinde bir yazılım RAID (dizme) oluşturmak gerekir. Tek bir Azure diski, bu şekilde en fazla VM sınırına ulaşmak için üretilen iş miktarını sağlayamaz. Azure Premium Storage, DT 2,0 ' i çalıştırmak için zorunludur. 

- Kullanılabilir Azure disk türleriyle ilgili ayrıntılar [burada](../../disks-types.md) bulunabilir
- Mdaddm aracılığıyla yazılım RAID oluşturma hakkında ayrıntılı bilgi için [burada](/previous-versions/azure/virtual-machines/linux/configure-raid) bulunabilir
- En fazla üretilen iş için bir şeritli birim oluşturmak üzere LVM yapılandırma ayrıntıları [burada](/previous-versions/azure/virtual-machines/linux/configure-lvm) bulunabilir

Boyut gereksinimlerine bağlı olarak, bir VM 'nin en fazla üretilen iş hızına ulaşmak için farklı seçenekler vardır. Her DT 2,0 sanal makine türü için, üst VM aktarım hızı sınırına ulaşmak üzere olası veri hacmi disk konfigürasyonları aşağıda verilmiştir. E32sv3 VM, daha küçük iş yükleri için bir giriş düzeyi olarak değerlendirilmelidir. Bu durumda yeterince hızlı olmadığından, VM 'nin M64-32ms olarak yeniden boyutlandırılması gerekebilir.
M64-32ms VM 'nin belleği çok fazlaysa, GÇ yükü özellikle okuma yoğunluklu iş yükleri için sınıra ulaşmayabilir. Bu nedenle, Stripe kümesindeki daha az disk, müşterinin özel iş yüküne bağlı olarak yeterli olabilir. Ancak, aşağıdaki Disk yapılandırmalarının en yüksek aktarım hızını garanti etmek için aşağıda verilmiştir:


| VM SKU | Disk yapılandırması 1 | Disk yapılandırması 2 | Disk yapılandırması 3 | Disk yapılandırması 4 | Disk yapılandırması 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50-> 16 TB | 4 x P40-> 8 TB | 5 x P30-> 5 TB | 7 x P20-> 3,5 TB | 8 x P15-> 2 TB | 
| E32sv3 | 3 x P50-> 12 TB | 3 x P40-> 6 TB | 4 x P30-> 4 TB | 5 x P20-> 2,5 TB | 6 x P15-> 1,5 TB | 


Özellikle iş yükünün okuma açısından yoğun olması durumunda, veritabanı yazılımının veri birimleri için önerilen "salt okunurdur" Azure ana bilgisayar önbelleğini açmak üzere GÇ performansını artırabilir. İşlem günlüğü için Azure konak disk önbelleğinin "none" olması gerekir. 

Günlük biriminin boyutuna ilişkin önerilen bir başlangıç noktası, veri boyutunun %15 ' i için buluşsal bir değer. Günlük biriminin oluşturulması, maliyet ve verimlilik gereksinimlerine bağlı olarak farklı Azure disk türleri kullanılarak gerçekleştirilebilir. Günlük birimi için yüksek g/ç verimlilik gereklidir.  M64-32ms VM türünün kullanılması durumunda [yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md)etkinleştirilmesi zorunludur. Azure Yazma Hızlandırıcısı, işlem günlüğü için en iyi disk yazma gecikmesi sağlar (yalnızca M serisi için kullanılabilir). VM türü başına en fazla disk sayısı gibi göz önünde bulundurmanız gereken bazı öğeler vardır. Yazma Hızlandırıcısı ayrıntılarını [burada](../../how-to-enable-write-accelerator.md) bulabilirsiniz


Günlük birimini boyutlandırma hakkında birkaç örnek aşağıda verilmiştir:

| veri birimi boyutu ve disk türü | günlük birimi ve disk türü yapılandırması 1 | günlük birimi ve disk türü yapılandırması 2 |
| --- | --- | --- |
| 4 x P50-> 16 TB | 5 x P20-> 2,5 TB | 3 x P30-> 3 TB |
| 6 x P15-> 1,5 TB | 4 x P6-> 256 GB | 1 x P15-> 256 GB |


SAP HANA ölçeği genişletme gibi,/Hana/Shared Directory SAP HANA VM ile DT 2,0 VM arasında paylaşılmalıdır. Yüksek oranda kullanılabilir bir NFS sunucusu işlevi gören adanmış VM 'Ler kullanılarak SAP HANA ölçek genişletme için aynı mimari önerilir. Paylaşılan yedekleme birimi sağlamak için, aynı tasarım kullanılabilir. Ancak, HA gerekli olacaksa veya bir yedekleme sunucusu işlevi görecek şekilde yeterli depolama kapasitesine sahip ayrılmış bir VM kullanmak için yeterli olup olmadığı müşteriye ait olur.



### <a name="links-to-dt-20-documentation"></a>DT 2,0 belgelerinin bağlantıları 

- [SAP HANA dinamik katmanlama yükleme ve güncelleştirme Kılavuzu](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Dinamik katmanlama öğreticileri ve kaynakları SAP HANA](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinamik katmanlama PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2,0 SPS 02 dinamik katmanlama iyileştirmeleri](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Azure VM 'lerinde SAP HANA dağıtmaya yönelik işlemler
Aşağıdaki bölümlerde, Azure VM 'lerinde SAP HANA sistemlerini dağıtmayla ilgili bazı işlemler açıklanır.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM 'lerinde yedekleme ve geri yükleme işlemleri
Aşağıdaki belgelerde SAP HANA dağıtımınızın nasıl yedekleneceği ve geri yükleneceği açıklanır:

- [SAP HANA yedeklemesine genel bakış](./sap-hana-backup-guide.md)
- [SAP HANA dosya düzeyinde yedekleme](./sap-hana-backup-file-level.md)
- [SAP HANA depolama anlık görüntü kıyaslama](./sap-hana-backup-guide.md)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANA içeren VM 'Leri başlatma ve yeniden başlatma
Azure genel bulutu 'nın belirgin bir özelliği yalnızca işlem tutanakanız için ücretlendirilirsiniz. Örneğin, SAP HANA çalıştıran bir VM 'yi kapattığınızda yalnızca bu süre boyunca depolama maliyetleri için faturalandırılırsınız. İlk dağıtımınızdaki sanal makinelerinize yönelik statik IP adresleri belirttiğinizde, başka bir özellik de kullanılabilir. SAP HANA olan bir VM 'yi yeniden başlattığınızda, VM önceki IP adresleriyle yeniden başlatılır. 


### <a name="use-saprouter-for-sap-remote-support"></a>SAP uzaktan desteği için SAProuter kullanın
Şirket içi konumlarınız ile Azure arasında siteden siteye bağlantı varsa ve SAP bileşenlerini çalıştırıyorsanız büyük olasılıkla zaten SAProuter çalıştırıyorsunuz demektir. Bu durumda, uzaktan destek için aşağıdaki öğeleri doldurun:

- SAProuter yapılandırmasında SAP HANA barındıran sanal makinenin özel ve statik IP adresini koruyun.
- 3299 numaralı TCP/IP bağlantı noktası üzerinden trafiğe izin vermek için HANA VM 'yi barındıran alt ağın NSG 'sini yapılandırın.

Internet üzerinden Azure 'a bağlanıyorsanız ve SAP HANA VM için bir SAP yönlendiriciniz yoksa, bileşeni yüklemeniz gerekir. Yönetim alt ağındaki ayrı bir sanal makineye SAProuter 'yi yükler. Aşağıdaki görüntüde, bir siteden siteye bağlantı olmadan ve SAProuter ile SAP HANA dağıtmaya yönelik kaba bir şema gösterilmektedir:

![Siteden siteye bağlantı olmadan SAP HANA için kaba dağıtım şeması ve SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

SAProuter 'ı, JumpBox sanal makinenizde değil ayrı bir sanal makineye yüklediğinizden emin olun. Ayrı VM 'nin statik bir IP adresi olmalıdır. SAProuter 'nizi SAP tarafından barındırılan SAProuter 'a bağlamak için bir IP adresi için SAP ile iletişim kurun. (SAP tarafından barındırılan SAProuter, sanal makinenize yüklediğiniz SAProuter örneğinin karşılığından oluşur.) SAProuter örneğinizi yapılandırmak için SAP IP adresini kullanın. Yapılandırma ayarlarında, yalnızca TCP bağlantı noktası 3299 olan bağlantı noktası gereklidir.

SAProuter aracılığıyla uzaktan destek bağlantıları ayarlama ve sürdürme hakkında daha fazla bilgi için bkz. [SAP belgeleri](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure yerel VM 'lerinde SAP HANA yüksek kullanılabilirlik
SUSE Linux Enterprise Server veya Red Hat çalıştırıyorsanız, STONITH cihazlarıyla bir Paceoluşturucu kümesi kurabilirsiniz. Cihazları, HANA sistem çoğaltması ve otomatik yük devretme ile zaman uyumlu çoğaltma kullanan SAP HANA bir yapılandırma ayarlamak için kullanabilirsiniz. ' Sonraki adımlar ' bölümünde listelenen daha fazla bilgi için.

## <a name="next-steps"></a>Sonraki Adımlar
Listelenen makaleleri hakkında bilgi edinin
- [SAP HANA Azure sanal makine depolama alanı yapılandırmaları](./hana-vm-operations-storage.md)
- [SUSE Linux Enterprise Server Azure NetApp Files kullanarak Azure VM 'lerinde bekleme düğümüne sahip bir SAP HANA genişleme sistemi dağıtma](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Red Hat Enterprise Linux Azure NetApp Files kullanarak Azure VM 'lerinde bekleme düğümüne sahip bir SAP HANA genişleme sistemi dağıtma](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği](./sap-hana-high-availability.md)
- [Red Hat Enterprise Linux üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği](./sap-hana-high-availability-rhel.md)