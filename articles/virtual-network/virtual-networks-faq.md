---
title: Azure sanal ağ hakkında SSS
titlesuffix: Azure Virtual Network
description: Microsoft Azure sanal ağlarla ilgili en sık sorulan soruların yanıtları.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: 30398b5f81ac1893129ba222c5f1a2d762ad1e7f
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595057"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure sanal ağ hakkında sık sorulan sorular (SSS)

## <a name="virtual-network-basics"></a>Sanal Ağ temelleri

### <a name="what-is-an-azure-virtual-network-vnet"></a>Azure sanal ağı (VNet) nedir?
Bir Azure sanal ağı (VNet) buluttaki kendi ağınızın bir gösterimidir. Azure bulutunun aboneliğinize adanmış mantıksal bir yalıtımıdır. VNET 'leri Azure 'da sanal özel ağlar (VPN) sağlamak ve yönetmek için ve isteğe bağlı olarak, Azure 'daki diğer sanal ağlarla veya şirket içi BT altyapınızla, karma veya şirket içi çözümler oluşturmak için, VNET 'Leri kullanabilirsiniz. Oluşturduğunuz her VNet kendi CıDR bloğuna sahiptir ve CıDR blokları çakışmadığından, diğer sanal ağlara ve şirket içi ağlarla bağlantı kurulabilir. Ayrıca sanal ağlar için DNS sunucusu ayarlarının denetimine sahip olursunuz ve VNet 'in alt ağlara segmentlemesini sağlayabilirsiniz.

Sanal ağları şu şekilde kullanın:

* Ayrılmış bir özel bulut sanal ağı oluşturun. Bazen çözümünüz için şirket içi bir yapılandırmaya gerek kalmaz. Bir sanal ağ oluşturduğunuzda, VNet 'iniz içindeki hizmetleriniz ve VM 'Ler bulutta birbirleriyle doğrudan ve güvenli bir şekilde iletişim kurabilir. Çözümünüzün bir parçası olarak Internet iletişimi gerektiren VM 'Ler ve hizmetler için uç nokta bağlantıları yapılandırabilirsiniz.

* Veri merkezinizi güvenli bir şekilde genişletin. Sanal ağlar ile, veri merkezi kapasitenizi güvenli bir şekilde ölçeklendirmek için geleneksel siteden siteye (S2S) VPN 'Ler oluşturabilirsiniz. S2S VPN 'Leri, kurumsal VPN Gateway ve Azure arasında güvenli bir bağlantı sağlamak için ıPSEC kullanır.

* Karma bulut senaryolarını etkinleştirin. VNET 'ler, bir dizi karma bulut senaryosunu destekleme esnekliği sağlar. Bulut tabanlı uygulamaları, ana bilgisayarlar ve UNIX sistemleri gibi herhangi bir şirket içi sistem türüne güvenli bir şekilde bağlayabilirsiniz.

### <a name="how-do-i-get-started"></a>Nasıl kullanmaya başlarım?
Başlamak için [sanal ağ belgelerini](https://docs.microsoft.com/azure/virtual-network/) ziyaret edin. Bu içerik, tüm VNet özellikleri için genel bakış ve dağıtım bilgileri sağlar.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Şirket içi bağlantı olmadan VNET 'i kullanabilir miyim?
Evet. Bir sanal ağı, şirket içine bağlanmadan kullanabilirsiniz. Örneğin, yalnızca bir Azure VNet 'te Microsoft Windows Server Active Directory etki alanı denetleyicileri ve SharePoint grupları çalıştırabilirsiniz.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Sanal ağlar veya VNet ile şirket içi veri Merkezim arasında WAN iyileştirmesi yapabilir miyim?
Evet. Azure Marketi aracılığıyla çeşitli satıcıların [WAN optimizasyon ağ sanal](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) gerecini dağıtabilirsiniz.

## <a name="configuration"></a>Yapılandırma

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>VNet oluşturmak için hangi araçları kullanabilirim?
VNet oluşturmak veya yapılandırmak için aşağıdaki araçları kullanabilirsiniz:

* Azure portalı
* PowerShell
* Azure CLI
* Ağ yapılandırma dosyası (yalnızca klasik sanal ağlar için netcfg). [Ağ yapılandırma dosyası kullanarak VNET yapılandırma](virtual-networks-using-network-configuration-file.md) makalesini inceleyin.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>VNET 'imde hangi adres aralıklarını kullanabilirim?
[RFC 1918](https://tools.ietf.org/html/rfc1918)' de tanımlanan HERHANGI bir IP adresi aralığı. Örneğin, 10.0.0.0/16. Aşağıdaki adres aralıklarını ekleyemezsiniz:
* 224.0.0.0/4 (çok noktaya yayın)
* 255.255.255.255/32 (yayın)
* 127.0.0.0/8 (geri döngü)
* 169.254.0.0/16 (bağlantı-yerel)
* 168.63.129.16/32 (iç DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>VNET 'imde genel IP adresleri olabilir miyim?
Evet. Genel IP adresi aralıkları hakkında daha fazla bilgi için bkz. [sanal ağ oluşturma](manage-virtual-network.md#create-a-virtual-network). Genel IP adreslerine internet 'ten doğrudan erişilemez.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>VNet 'imde alt ağ sayısı sınırı var mı?
Evet. Ayrıntılar için bkz. [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) . Alt ağ adres alanları bir diğerinden çakışamaz.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Bu alt ağlar içindeki IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?
Evet. Azure, her alt ağ içindeki 5 IP adresini ayırır. Bunlar x. x. x. 0-x. x. x. 3 ve alt ağın son adresidir. x. x. x. 1-x. x. x. 3, Azure hizmetleri için her alt ağda ayrılmıştır.   
- x. x. x. 0: ağ adresi
- x. x. x. 1: varsayılan ağ geçidi için Azure tarafından ayrılmış
- x. x. x. 2, x. x. x. 3: Azure tarafından Azure DNS IP 'Leri VNet alanı ile eşlemek için ayrılmış
- x. x. x. 255: ağ yayını adresi

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Ne kadar küçük, sanal ağlar ve alt ağlar olabilir?
En küçük desteklenen alt ağ/29 ve en büyük değer/8 ' dir (CıDR alt ağ tanımları kullanılarak).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Sanal ağları kullanarak VLAN 'larımı Azure 'a getirebilir miyim?
Hayır. VNET 'ler katman 3 Yerpaylaşımları. Azure, katman 2 semantiğini desteklemez.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>VNET ve alt ağlarım üzerinde özel yönlendirme ilkeleri belirtebilir miyim?
Evet. Bir yol tablosu oluşturup bir alt ağla ilişkilendirebilirsiniz. Azure 'da yönlendirme hakkında daha fazla bilgi için bkz. [yönlendirmeye genel bakış](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Sanal ağlar çok noktaya yayın veya yayın destekliyor mu?
Hayır. Çok noktaya yayın ve yayın desteklenmez.

### <a name="what-protocols-can-i-use-within-vnets"></a>VNET 'ler içinde hangi protokolleri kullanabilirim?
Sanal ağlar içindeki TCP, UDP ve ıCMP TCP/IP protokollerini kullanabilirsiniz. Tek noktaya yayın, sanal ağlar içinde dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) dışında tek noktaya yayın aracılığıyla desteklenir (kaynak bağlantı noktası UDP/68/hedef bağlantı noktası UDP/67). Çok noktaya yayın, yayın, IP-IP kapsüllenmiş paketleri ve Genel Yönlendirme Kapsüllemesi (GRE) paketleri VNET 'ler içinde engellenir. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>VNet içindeki varsayılan yönlendiricileriyle ping yapabilir miyim?
Hayır.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Bağlantıyı tanılamak için tracert 'i kullanabilir miyim?
Hayır.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>VNet oluşturulduktan sonra alt ağları ekleyebilir miyim?
Evet. Alt ağ adres aralığı başka bir alt ağın parçası olmadığı ve sanal ağın adres aralığında kalan kullanılabilir alan olduğu sürece, alt ağlar VNET 'lere eklenebilir.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Oluşturduktan sonra alt ağımın boyutunu değiştirebilir miyim?
Evet. İçinde dağıtılan bir VM veya hizmet yoksa alt ağ ekleyebilir, kaldırabilir, genişletebilir veya küçültebilirsiniz.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Alt ağları oluşturduktan sonra değiştirebilir miyim?
Evet. VNet tarafından kullanılan CıDR bloklarını ekleyebilir, kaldırabilir ve değiştirebilirsiniz.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Hizmetlerimi bir VNet 'te çalıştırdım, internet 'e bağlanabilir miyim?
Evet. Bir sanal ağ içinde dağıtılan tüm hizmetler Internet 'e bağlanabilir. Azure 'da giden internet bağlantıları hakkında daha fazla bilgi edinmek için bkz. [giden bağlantılar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Kaynak Yöneticisi aracılığıyla dağıtılan bir kaynağa gelen bağlantı kurmak istiyorsanız, kaynağa atanmış bir genel IP adresi olmalıdır. Genel IP adresleri hakkında daha fazla bilgi için bkz. [genel IP adresleri](virtual-network-public-ip-address.md). Azure 'da dağıtılan her Azure bulut hizmetine, kendisine atanmış genel olarak adreslenebilir bir VIP vardır. Bu hizmetlerin İnternet 'ten gelen bağlantıları kabul etmesine olanak tanımak için sanal makinelerin PaaS rolleri ve uç noktaları için giriş uç noktaları tanımlarsınız.

### <a name="do-vnets-support-ipv6"></a>VNET 'ler IPv6 'Yı destekliyor mu?
Hayır. Şu anda IPv6 'Yı VNET 'ler ile kullanamazsınız. Ancak, sanal makinelerin yük dengelenmesi için Azure Yük dengeleyicilerine IPv6 adresleri atayabilirsiniz. Ayrıntılar için bkz. [Azure Load Balancer Için IPv6 'Ya genel bakış](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Bir VNet span bölgesi olabilir mi?
Hayır. VNet, tek bir bölgeyle sınırlıdır. Ancak, bir sanal ağ, kullanım alanı kullanılabilirlik bölgelerini de kapsar. Kullanılabilirlik alanları hakkında daha fazla bilgi için bkz. [Kullanılabilirlik alanlarına genel bakış](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Sanal ağ eşlemesi ile farklı bölgelerdeki sanal ağları bağlayabilirsiniz. Ayrıntılar için bkz. [sanal ağ eşlemesine genel bakış](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Azure 'da bir sanal ağı başka bir VNet 'e bağlayabilirim miyim?
Evet. Aşağıdakilerden birini kullanarak bir sanal ağı başka bir VNet 'e bağlayabilirsiniz:
- **Sanal ağ eşlemesi**: Ayrıntılar için bkz. [VNET eşlemesi genel bakış](virtual-network-peering-overview.md)
- **Azure VPN Gateway**: Ayrıntılar için bkz. [VNET 'ten VNET 'e bağlantı yapılandırma](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Ad çözümlemesi (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Sanal ağlar için DNS seçeneklerim nelerdir?
Kullanılabilir tüm DNS seçeneklerinde size rehberlik etmek için [VM 'ler ve rol örnekleri Için ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md) sayfasında karar tablosunu kullanın.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>VNet için DNS sunucularını belirtebilir miyim?
Evet. Sanal ağ ayarlarında DNS sunucusu IP adresleri belirtebilirsiniz. Ayar, VNet 'teki tüm VM 'Ler için varsayılan DNS sunucuları olarak uygulanır.

### <a name="how-many-dns-servers-can-i-specify"></a>Kaç DNS sunucusu belirtmem gerekiyor?
Başvuru [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Ağı oluşturduktan sonra DNS sunucularımı değiştirebilir miyim?
Evet. Sanal ağınız için DNS sunucusu listesini dilediğiniz zaman değiştirebilirsiniz. DNS sunucusu listenizi değiştirirseniz, yeni DNS ayarlarının etkili olabilmesi için, VNet 'teki tüm etkilenen VM 'lerde bir DHCP kira yenilemesi gerçekleştirmeniz gerekir. Windows işletim sistemi çalıştıran VM 'Ler için `ipconfig /renew` doğrudan VM 'ye yazarak bunu yapabilirsiniz. Diğer işletim sistemi türleri için, belirli işletim sistemi türü için DHCP kira yenileme belgelerine bakın. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Azure tarafından sunulan DNS nedir ve VNET ile birlikte çalışır mı?
Azure tarafından sağlanan DNS, Microsoft tarafından sunulan çok kiracılı bir DNS hizmetidir. Azure, tüm VM 'lerinizi ve bulut hizmeti rol örneklerinizi bu hizmette kaydeder. Bu hizmet, aynı bulut hizmetinde yer alan VM 'Ler ve rol örnekleri için ana bilgisayar adına ve aynı VNet 'teki VM 'Ler ve rol örnekleri için FQDN ile ad çözümlemesi sağlar. DNS hakkında daha fazla bilgi edinmek için bkz. [VM 'ler Için ad çözümleme ve rol örnekleri Cloud Services](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Azure tarafından sunulan DNS kullanılarak platformlar arası ad çözümlemesi için bir VNet 'te ilk 100 bulut hizmeti sınırlaması vardır. Kendi DNS sunucunuzu kullanıyorsanız, bu sınırlama uygulanmaz.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>DNS ayarlarımı VM başına veya bulut hizmeti temelinde geçersiz kılabilir miyim?
Evet. Varsayılan ağ ayarlarını geçersiz kılmak için, VM veya bulut hizmeti başına DNS sunucuları ayarlayabilirsiniz. Ancak, ağ genelindeki DNS 'yi mümkün olduğunca çok kullanmanız önerilir.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kendi DNS son ekini getirebilir miyim?
Hayır. Sanal ağlarınız için özel bir DNS soneki belirtemezsiniz.

## <a name="connecting-virtual-machines"></a>Sanal makineleri bağlama

### <a name="can-i-deploy-vms-to-a-vnet"></a>VM 'Leri VNet 'e dağıtabilir miyim?
Evet. Kaynak Yöneticisi dağıtım modeliyle dağıtılan bir VM 'ye bağlı tüm ağ arabirimleri (NIC) bir sanal ağa bağlı olmalıdır. Klasik dağıtım modeli aracılığıyla dağıtılan VM 'Ler, isteğe bağlı olarak bir VNet 'e bağlanabilir.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>VM 'lere atayabildiğim farklı IP adresi türleri nelerdir?
* **Özel:** Her VM 'deki her NIC 'ye atanır. Adres static ya da Dynamic yöntemi kullanılarak atanır. Özel IP adresleri, sanal ağınızın alt ağ ayarlarında belirttiğiniz aralıktan atanır. Klasik dağıtım modeliyle dağıtılan kaynaklara, sanal ağa bağlı olmasalar bile özel IP adresleri atanır. Ayırma yönteminin davranışı, bir kaynağın Kaynak Yöneticisi veya klasik dağıtım modeliyle dağıtıldığına bağlı olarak farklılık belirtir: 

  - **Kaynak Yöneticisi**: dinamik veya statik yöntemle atanan özel IP adresi, kaynak silinene kadar bir sanal makineye (Kaynak Yöneticisi) atanmış olarak kalır. Fark, statik kullanırken atanacak adresi seçtiğinizde Azure 'un dinamik kullanılırken seçtiği bir adrestir. 
  - **Klasik**: sanal makine (klasık) VM durdurulmuş (serbest bırakıldı) durumda olduktan sonra yeniden başlatıldığında dinamik yöntemle atanan özel IP adresi değişebilir. Klasik dağıtım modeliyle dağıtılan bir kaynağın özel IP adresinin hiçbir şekilde değişmeyeceğinden emin olmanız gerekiyorsa, statik metoda sahip bir özel IP adresi atayın.

* **Ortak:** İsteğe bağlı olarak, Azure Resource Manager dağıtım modeliyle dağıtılan VM 'lere bağlı NIC 'lere atanır. Adres, statik veya dinamik ayırma yöntemiyle atanabilir. Klasik dağıtım modeli aracılığıyla dağıtılan tüm VM 'Ler ve Cloud Services rol örnekleri, *dinamik*, genel sanal IP (VIP) adresi atanmış bir bulut hizmeti içinde bulunur. [Ayrılmış IP adresi](virtual-networks-reserved-public-ip.md)olarak adlandırılan genel *statik* IP adresı isteğe bağlı olarak VIP olarak atanabilir. Genel IP adreslerini tek tek VM 'lere atayabilir veya klasik dağıtım modeliyle dağıtılan rol örneklerine Cloud Services. Bu adreslere [örnek düzeyi genel IP (ıLPıP](virtual-networks-instance-level-public-ip.md) adresleri denir ve dinamik olarak atanabilir.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Daha sonra oluşturacağınız bir VM için özel IP adresini ayırabilir miyim?
Hayır. Özel bir IP adresi ayıramazsınız. Özel bir IP adresi varsa, DHCP sunucusu tarafından bir VM veya rol örneğine atanır. VM, özel IP adresinin atanmasını istediğiniz bir tane olabilir veya olmayabilir. Bununla birlikte, zaten oluşturulmuş bir VM 'nin özel IP adresini kullanılabilir bir özel IP adresine değiştirebilirsiniz.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>VNet 'teki VM 'Ler için özel IP adresleri değişikliği yapılsın mı?
Duruma göre değişir. VM Kaynak Yöneticisi üzerinden dağıtılmışsa, IP adresinin statik veya dinamik ayırma yöntemiyle atanıp atanmadığına bakılmaksızın, hayır. VM, klasik dağıtım modeli üzerinden dağıtılmışsa, bir VM durdurulmuş (serbest bırakıldı) durumda olduktan sonra başlatıldığında dinamik IP adresleri değişebilir. Adres, VM silindiğinde dağıtım modeli aracılığıyla dağıtılan bir VM 'den serbest bırakılır.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>IP adreslerini VM işletim sistemi içindeki NIC 'lere el ile atayabilir miyim?
Evet, ancak bir sanal makineye birden çok IP adresi atarken olduğu gibi gerekmedikçe bu önerilmez. Ayrıntılar için bkz. [sanal makineye birden çok IP adresi ekleme](virtual-network-multiple-ip-addresses-portal.md#os-config). Bir sanal makineye bağlı bir Azure NIC 'sine atanan IP adresi değişirse ve VM işletim sistemi içindeki IP adresi farklıysa, VM bağlantısı kaybedilir.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Bir bulut hizmeti dağıtım yuvasını durdurur veya bir VM 'yi işletim sistemi içinden kapatırsanız, IP Adreslerime ne olur?
Yapma. IP adresleri (genel VIP, genel ve özel), bulut hizmeti dağıtım yuvasına veya VM 'ye atanmaya devam eder.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>VM 'Leri yeniden dağıtmaya gerek kalmadan bir alt ağdan başka bir alt ağa taşıyabilir miyim?
Evet. [BIR VM veya rol örneğini farklı bir alt ağa taşıma hakkında](virtual-networks-move-vm-role-to-subnet.md) daha fazla bilgi edinebilirsiniz.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>VM 'im için statik bir MAC adresi yapılandırabilir miyim?
Hayır. MAC adresi statik olarak yapılandırılamaz.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>MAC adresi, VM 'imde oluşturulduktan sonra aynı kalır mi?
Evet, MAC adresi, silinene kadar hem Kaynak Yöneticisi hem de klasik dağıtım modelleriyle dağıtılan bir VM için aynı kalır. Daha önce, VM durdurulmuşsa (serbest bırakıldı), MAC adresi serbest bırakılır, ancak artık VM serbest bırakılmış durumdaysa bile MAC adresi korunur. Ağ arabirimi silininceye veya birincil ağ arabiriminin birincil IP yapılandırmasına atanan özel IP adresi değiştirilinceye kadar MAC adresi ağ arabirimine atanmış olarak kalır. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>VNet 'teki bir VM 'den internet 'e bağlanabilir miyim?
Evet. Bir sanal ağ içinde dağıtılan tüm VM 'Ler ve Cloud Services rol örnekleri Internet 'e bağlanabilir.

## <a name="azure-services-that-connect-to-vnets"></a>VNET 'lere bağlanan Azure hizmetleri

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>VNet ile Azure App Service Web Apps kullanabilir miyim?
Evet. Bir ASE (App Service Ortamı) kullanarak bir VNet içinde Web Apps dağıtabilir, uygulamalarınızın arka ucunu VNet tümleştirmeyle sanal ağlarınıza bağlayabilirsiniz ve gelen trafiği hizmet uç noktalarıyla uygulamanıza taşıyabilirsiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [App Service ağ özellikleri](../app-service/networking-features.md)
* [App Service Ortamı Web Apps oluşturma](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Uygulamanızı bir Azure sanal ağıyla tümleştirin](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [App Service erişim kısıtlamaları](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Web ve çalışan rolleri (PaaS) ile bir VNet 'te Cloud Services dağıtabilir miyim?
Evet. Sanal ağlar içinde Cloud Services rol örnekleri dağıtabilirsiniz (isteğe bağlı olarak). Bunu yapmak için, hizmet yapılandırmanızın ağ yapılandırması bölümünde VNet adı ve rol/alt ağ eşlemelerini belirtirsiniz. İkili dosyalarınızı güncelleştirmeniz gerekmez.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Sanal makine ölçek kümesini VNet 'e bağlayabilirim miyim?
Evet. Sanal makine ölçek kümesini VNet 'e bağlamanız gerekir.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Kaynakları VNet 'e dağıtabilecek Azure hizmetlerinin tamamen bir listesi var mı?
Evet, Ayrıntılar Için bkz. [Azure hizmetleri Için sanal ağ tümleştirmesi](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>VNet 'ten Azure PaaS kaynaklarına erişimi nasıl kısıtlayabilirim?

Azure PaaS hizmetleri (Azure depolama ve Azure SQL veritabanı gibi) aracılığıyla dağıtılan kaynaklar, sanal ağ hizmeti uç noktaları veya Azure özel bağlantısı aracılığıyla VNet 'e ağ erişimini kısıtlayabilir. Ayrıntılar için bkz. [sanal ağ hizmeti uç noktalarına genel bakış](virtual-network-service-endpoints-overview.md), [Azure özel bağlantısına genel bakış](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Hizmetlerimi VNET 'lere ve dışına taşıyabilir miyim?
Hayır. Hizmetleri VNET 'lere ve dışına taşıyamazsınız. Kaynağı başka bir sanal ağa taşımak için, kaynağı silip yeniden dağıtmanız gerekir.

## <a name="security"></a>Güvenlik

### <a name="what-is-the-security-model-for-vnets"></a>VNET 'ler için güvenlik modeli nedir?
Sanal ağlar birbirleriyle ve Azure altyapısında barındırılan diğer hizmetlerden yalıtılmıştır. VNet bir güven sınırındaki.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Gelen veya giden trafik akışını VNet 'e bağlı kaynaklarla kısıtlayabilir miyim?
Evet. [Ağ güvenlik gruplarını](security-overview.md) bir sanal ağ içindeki ayrı alt ağlara, VNET 'e bağlı NIC 'lere veya her ikisine birden uygulayabilirsiniz.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>VNet bağlantılı kaynaklar arasında bir güvenlik duvarı uygulayabilir miyim?
Evet. Azure Marketi aracılığıyla birkaç satıcının [güvenlik duvarı ağ sanal](https://azure.microsoft.com/marketplace/?term=firewall) gerecini dağıtabilirsiniz.

### <a name="is-there-information-available-about-securing-vnets"></a>VNET 'leri güvenli hale getirmek için kullanılabilir bilgi var mı?
Evet. Ayrıntılar için bkz. [Azure ağ güvenliğine genel bakış](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API 'Ler, şemalar ve araçlar

### <a name="can-i-manage-vnets-from-code"></a>Koddan VNET 'leri yönetebilir miyim?
Evet. [Azure Resource Manager](/rest/api/virtual-network) ve [Klasik](https://go.microsoft.com/fwlink/?LinkId=296833) dağıtım MODELLERINDE sanal ağlar için REST API 'lerini kullanabilirsiniz.

### <a name="is-there-tooling-support-for-vnets"></a>VNET 'ler için araç desteği var mı?
Evet. Kullanma hakkında daha fazla bilgi edinin:
- [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) ve [Klasik](virtual-networks-create-vnet-classic-pportal.md) dağıtım modelleriyle vnet 'leri dağıtmak için Azure Portal.
- [Kaynak Yöneticisi](/powershell/module/az.network) ve [Klasik](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) dağıtım modelleriyle dağıtılan sanal ağları yönetmek için PowerShell.
- [Kaynak Yöneticisi](/cli/azure/network/vnet) ve [Klasik](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) dağıtım modelleri aracılığıyla dağıtılan VNET 'leri dağıtmak ve yönetmek için Azure komut satırı arabirimi (CLI).  

## <a name="vnet-peering"></a>Sanal ağ eşleme

### <a name="what-is-vnet-peering"></a>VNet eşlemesi nedir?
VNet eşlemesi (veya sanal ağ eşlemesi), sanal ağlara bağlanmanızı sağlar. Sanal ağlar arasındaki VNet eşleme bağlantısı, trafiği özel olarak IPv4 adresleriyle yönlendirmenizi sağlar. Eşlenen VNET 'lerdeki sanal makineler, aynı ağ içinde olup olmadıkları gibi birbirleriyle iletişim kurabilir. Bu sanal ağlar aynı bölgede veya farklı bölgelerde (genel VNet eşlemesi olarak da bilinir) olabilir. VNet eşleme bağlantıları, Azure abonelikleri arasında da oluşturulabilir.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Farklı bir bölgedeki VNet 'e yönelik eşleme bağlantısı oluşturabilir miyim?
Evet. Küresel VNet eşlemesi, farklı bölgelerdeki VNET 'leri eşlemenize olanak sağlar. Küresel VNet eşlemesi, tüm Azure genel bölgelerinde, Çin bulut bölgelerinde ve kamu bulut bölgelerinde kullanılabilir. Azure genel bölgelerinden Ulusal bulut bölgelerine genel olarak ihtiyacınız yoktur.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Küresel VNet eşlemesi ve yük dengeleyiciler ile ilgili kısıtlamalar nelerdir?
İki sanal ağ farklı bölgelerde (küresel VNet eşlemesi), temel Load Balancer kullanan kaynaklara bağlanamazsınız. Standart Load Balancer kullanan kaynaklara bağlanabilirsiniz.
Aşağıdaki kaynaklar temel yük dengeleyiciler kullanır, bu da genel VNet eşlemesi genelinde onlarla iletişim kuramayabileceği anlamına gelir:
- Temel yük dengeleyiciler arkasındaki VM 'Ler
- Temel yük dengeleyicileri olan sanal makine ölçek kümeleri 
- Redis Cache 
- Application Gateway (v1) SKU 'SU
- Service Fabric
- SQL MI
- API Management
- Active Directory Etki Alanı Hizmeti (ekler)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service Ortamı

Sanal ağ geçitleri aracılığıyla ExpressRoute veya VNet-VNet aracılığıyla bu kaynaklara bağlanabilirsiniz.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Sanal ağlarım farklı Azure Active Directory kiracıların içindeki aboneliklere aitse VNet eşlemesini etkinleştirebilir miyim?
Evet. Abonelikleriniz farklı Azure Active Directory kiracılara aitse VNet eşlemesi (yerel veya genel) oluşturmak mümkündür. Bunu PowerShell veya CLı aracılığıyla yapabilirsiniz. Portal henüz desteklenmiyor.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>VNet eşleme bağlantısı *başlatılmış* durumda, neden bağlanamıyorum?
Eşleme bağlantınız *başlatılmış* durumdaysa, bu, yalnızca bir bağlantı oluşturmuş olmanız anlamına gelir. Başarılı bir bağlantı kurmak için çift yönlü bir bağlantı oluşturulmalıdır. Örneğin, A VNet to VNet 'e eşler arası, VNetA 'dan VNetB 'ye ve VNetB 'den VNetA 'ya bir bağlantı oluşturulmalıdır. Her iki bağlantı da oluşturulduğunda durumu *bağlı*olarak değişir.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>VNet eşleme bağlantımın bağlantısı *kesik* durumda, neden eşleme bağlantısı oluşturamıyorum?
VNet eşleme bağlantınızın *bağlantısı kesik* durumdaysa, oluşturulan bağlantılardan biri silinmiş demektir. Bir eşleme bağlantısını yeniden kurmak için bağlantıyı silip yeniden oluşturmanız gerekir.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>VNET 'umu farklı bir abonelikte VNet 'e eşleyebilir miyim?
Evet. Abonelikler arasında ve bölgeler arasında sanal ağlar oluşturabilirsiniz.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Eşleşen veya çakışan adres aralıkları ile iki sanal ağı eşleyebilir miyim?
Hayır. VNet eşlemesini etkinleştirmek için adres alanları çakışmamalıdır.

### <a name="how-much-do-vnet-peering-links-cost"></a>VNet eşleme bağlantı maliyeti ne kadar?
VNet eşleme bağlantısı oluşturmak için ücret alınmaz. Eşleme bağlantıları arasında veri aktarımı ücretlendirilir. [Buraya bakın](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>VNet eşleme trafiği şifrelendi mı?
Hayır. Eşlenen VNET 'lerdeki kaynaklar arasındaki trafik özeldir ve yalıtılmıştır. Microsoft omurga üzerinde tamamen kalır.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Neden eşleme bağlantıdır bağlantısı *kesik* durumda?
VNET eşleme bağlantıları, bir VNet eşleme bağlantısı silindiğinde *bağlantısı kesik* duruma geçer. Başarılı bir eşleme bağlantısını yeniden kurmak için her iki bağlantıyı da silmelisiniz.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>VNetB ve I eşi vnetb 'ye eş ağlarım varsa, bu, VNetA ve VNetC 'nin eşlenme anlamına gelir.
Hayır. Geçişli eşleme desteklenmiyor. Bunun gerçekleşmesi için VNET ve VNetC eşdüzey olmalıdır.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Eşleme bağlantıları için herhangi bir bant genişliği sınırlaması var mı?
Hayır. Yerel veya genel olan VNet eşlemesi, herhangi bir bant genişliği kısıtlaması uygulamaz. Bant genişliği yalnızca VM veya işlem kaynağı tarafından sınırlandırılır.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>VNet eşleme sorunlarını nasıl giderebilirim?
Deneyebileceğiniz bir [sorun giderici Kılavuzu](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) aşağıda verilmiştir.

## <a name="virtual-network-tap"></a>Sanal ağ TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Sanal ağ dokunarak hangi Azure bölgeleri kullanılabilir?
Sanal ağ dokunma önizleme tüm Azure bölgelerinde kullanılabilir. İzlenen ağ arabirimleri, sanal ağ kaynağı ' na dokunun ve toplayıcı ya da analiz çözümünün aynı bölgede dağıtılması gerekir.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Sanal ağ dokunma, yansıtılmış paketlerde herhangi bir filtreleme özelliğini destekliyor mu?
Filtre özellikleri sanal ağ Önizleme ' ye dokunarak desteklenmez. Bir dokunma yapılandırması ağ arabirimine eklendiğinde, ağ arabirimindeki tüm giriş ve çıkış trafiğinin ayrıntılı bir kopyası, dokunma hedefine akışla kaydedilir.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>İzlenen bir ağ arabirimine birden çok dokunma yapılandırmasına izin eklenebilir mi?
İzlenen bir ağ arabirimi yalnızca bir dokunma yapılandırmasına sahip olabilir. DOKUNMA trafiğinin birden çok kopyasını istediğiniz analiz araçlarına akışını sağlamak için bağımsız bir [iş ortağı çözümüne](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) danışın.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Aynı sanal ağ, birden fazla sanal ağdaki izlenen ağ arabirimlerinden kaynak toplu trafiğine DOKUNABILIR mi?
Evet. Aynı abonelikte veya farklı bir abonelikte eşlenmiş sanal ağlardaki izlenen ağ arabirimlerinden yansıtılmış trafiği toplamak için aynı sanal ağ dokunma kaynağı kullanılabilir. Sanal ağ dokunma kaynağı ve hedef yük dengeleyici veya hedef ağ arabirimi aynı abonelikte olmalıdır. Tüm abonelikler aynı Azure Active Directory kiracısında olmalıdır.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Bir ağ arabirimindeki bir sanal ağ yapılandırmasına izin etkinleştirdiğimde üretim trafiği üzerinde herhangi bir performans konusunda dikkate alınması gereken noktalar var mı?

Sanal ağ dokunma önizlemededir. Önizleme süresince hizmet düzeyi sözleşmesi yoktur. Yetenek, üretim iş yükleri için kullanılmamalıdır. Bir sanal makine ağ arabirimi bir dokunma yapılandırması ile etkinleştirildiğinde, Azure ana bilgisayarında üretim trafiğini göndermek için ayrılan kaynaklar, yansıtma işlevini gerçekleştirmek ve yansıtılan paketleri göndermek için kullanılır. Sanal makinenin üretim trafiğini ve yansıtmalı trafiği gönderebilmesi için yeterli kaynakların kullanılabilir olduğundan emin olmak için doğru [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makine boyutunu seçin.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>[Linux](create-vm-accelerated-networking-cli.md) veya [Windows](create-vm-accelerated-networking-powershell.md) IÇIN, sanal ağ dokunarak desteklenen hızlandırılmış ağ mı?

Hızlandırılmış ağ ile etkinleştirilen bir sanal makineye bağlı bir ağ arabirimine bir dokunma yapılandırması ekleyebileceksiniz. Ancak, yansıtma trafiği için yük boşaltma Şu anda Azure hızlandırılmış ağ tarafından desteklenmediğinden, sanal makinedeki performans ve gecikme süresi, bir dokunma yapılandırması eklenerek etkilenecektir.

## <a name="virtual-network-service-endpoints"></a>Sanal ağ hizmet uç noktaları

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Hizmet uç noktalarını bir Azure hizmetine ayarlamaya yönelik işlemlerin doğru sırası nedir?
Hizmet uç noktaları aracılığıyla bir Azure hizmet kaynağını güvenli hale getirmek için iki adım vardır:
1. Azure hizmeti için hizmet uç noktalarını açın.
2. Azure hizmetinde VNet ACL 'Lerini ayarlayın.

İlk adım bir ağ tarafı işlemidir ve ikinci adım bir hizmet kaynak tarafı işlemidir. Her iki adım da yönetici rolüne verilen RBAC izinlerine bağlı olarak aynı yönetici veya farklı yöneticiler tarafından gerçekleştirilebilir. Azure hizmet tarafında VNet ACL 'Lerini ayarlamadan önce sanal ağınızın hizmet uç noktalarını etkinleştirmenizi öneririz. Bu nedenle, adımların VNet hizmet uç noktalarını ayarlamak için yukarıda listelenen sırayla gerçekleştirilmesi gerekir.

>[!NOTE]
> Azure hizmeti erişimini izin verilen VNet ve alt ağa sınırlayabilmeniz için yukarıda açıklanan işlemlerin her ikisi de tamamlanmalıdır. Yalnızca ağ tarafında Azure hizmeti için hizmet uç noktalarını açmak, size sınırlı erişim sağlamaz. Ayrıca, Azure hizmet tarafında VNet ACL 'Leri de ayarlamanız gerekir.

Bazı hizmetler (SQL ve CosmosDB gibi), **ıgnoremissingvnetserviceendpoint** bayrağıyla yukarıdaki sırada özel durumlara izin verir. Flag **değeri true**olarak ayarlandığında, ağ tarafında hizmet uç noktaları ayarlamadan önce Azure hizmet tarafında VNET ACL 'leri ayarlanabilir. Azure Hizmetleri, belirli IP güvenlik duvarlarının Azure hizmetlerinde yapılandırıldığı ve ağ tarafında hizmet uç noktalarının açık olduğu durumlarda bir bağlantı bırakmaya neden olabileceği ve kaynak IP genel bir IPv4 adresinden özel bir adres. Ağ tarafında hizmet uç noktaları ayarlamadan önce Azure hizmet tarafında VNet ACL 'Lerinin kurulması bağlantı kesmesinden kaçınılmasını sağlayabilir.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Tüm Azure hizmetleri müşteri tarafından sunulan Azure sanal ağında mi var? VNet hizmeti uç noktası Azure hizmetleriyle nasıl çalışır?

Hayır, tüm Azure hizmetleri müşterinin sanal ağında yer alır. Azure depolama, Azure SQL ve Azure Cosmos DB gibi Azure veri hizmetlerinin çoğu, genel IP adreslerinden erişilebilen çok kiracılı hizmetlerdir. Azure hizmetleri için sanal ağ tümleştirmesi hakkında [buradan](virtual-network-for-azure-services.md)daha fazla bilgi edinebilirsiniz. 

VNet hizmet uç noktaları özelliğini kullandığınızda (ağ tarafında VNet hizmet uç noktasını açıp Azure hizmet tarafında uygun VNet ACL 'Lerini ayarlarken), bir Azure hizmetine erişim izin verilen VNet ve alt ağ ile kısıtlanır.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>VNet hizmeti uç noktası güvenlik nasıl sağlanır?

VNet hizmeti uç noktası özelliği (ağ tarafında VNet hizmet uç noktasını açma ve Azure hizmet tarafında uygun VNet ACL 'Lerini ayarlama), Azure hizmeti erişimini izin verilen VNet ve alt ağa göre sınırlandırır ve böylece bir ağ düzeyi güvenliği sağlar ve Azure hizmet trafiğinin yalıtımı. VNet hizmet uç noktalarını kullanan tüm trafik Microsoft omurga üzerinden akar ve bu sayede genel İnternet 'ten başka bir yalıtım katmanı sağlar. Ayrıca, müşteriler, Azure hizmet kaynaklarına genel Internet erişimini tamamen kaldırmayı ve IP güvenlik duvarı ve VNet ACL 'Leri aracılığıyla yalnızca sanal ağınızdan gelen trafiğe izin vermeyi seçebilir ve bu sayede Azure hizmet kaynaklarını yetkisiz bir şekilde koruma altına alabilir erişmesini.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>VNet hizmeti uç noktası koruma-VNet kaynakları veya Azure hizmeti nedir?
VNet hizmet uç noktaları, Azure hizmet kaynaklarını korumanıza yardımcı olur. VNet kaynakları ağ güvenlik grupları (NSG 'ler) ile korunur.

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>VNet hizmet uç noktalarını kullanmak için herhangi bir maliyet var mı?

Hayır, VNet hizmet uç noktalarının kullanılmasına yönelik ek bir maliyet yoktur.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Sanal ağ ve Azure hizmet kaynakları farklı aboneliklere aitse VNet hizmet uç noktalarını açabilir ve VNet ACL 'Lerini ayarlayabilirim miyim?

Evet, olabilir. Sanal ağlar ve Azure hizmet kaynakları aynı ya da farklı aboneliklerde olabilir. Tek gereksinim, hem sanal ağ hem de Azure hizmet kaynaklarının aynı Active Directory (AD) kiracısı altında olması gerekir.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Sanal ağ ve Azure hizmet kaynakları farklı AD kiracılarına aitse VNet hizmet uç noktalarını açıp VNet ACL 'Lerini ayarlayabilirim miyim?
Hayır, VNet hizmet uç noktaları ve VNet ACL 'Leri AD kiracılar arasında desteklenmez.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Azure sanal ağ geçidi (VPN) veya ExpressRoute ağ geçidi üzerinden bağlı olan bir şirket içi cihazın IP adresi, VNet hizmet uç noktaları üzerinden Azure PaaS hizmeti 'ne erişebilir mi?
Varsayılan olarak sanal ağlara ayrılmış olan Azure hizmeti kaynaklarına şirket içi ağlardan erişmek mümkün değildir. Şirket içinden gelen trafiğe izin vermek istiyorsanız şirket içi veya ExpressRoute 'un genel (genellikle NAT) IP adreslerine de izin vermeniz gerekir. Bu IP adresleri, Azure hizmet kaynakları için IP güvenlik duvarı yapılandırması üzerinden eklenebilir.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Azure hizmetini bir sanal ağ içindeki veya birden çok sanal ağ arasında birden çok alt ağa güvenli hale getirmek için VNet hizmeti uç noktası özelliğini kullanabilir miyim?
Azure hizmetlerinin bir sanal ağ içindeki veya birden çok sanal ağ arasında birden çok alt ağa güvenliğini sağlamak için, alt ağların her birinde ağ tarafında bulunan hizmet uç noktalarını bağımsız olarak etkinleştirin ve sonra Azure hizmet kaynaklarını Azure hizmet tarafında uygun VNet ACL 'Leri.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Bir sanal ağdan Azure hizmetlerine giden trafiği filtreleyip hizmet uç noktalarını kullanmaya devam edebilir miyim?
Bir sanal ağdan bir Azure hizmetine giden trafiği incelemek veya filtrelemek istiyorsanız, sanal ağ içinde bir ağ sanal gereci dağıtabilirsiniz. Daha sonra, ağ sanal gerecinin dağıtıldığı alt ağa hizmet uç noktaları uygulayabilir ve Azure hizmet kaynaklarını yalnızca VNet ACL 'Leri üzerinden bu alt ağ için güvenli hale getirebilirsiniz. Bu senaryo Ayrıca, sanal ağınızdan gelen Azure hizmeti erişimini yalnızca ağ sanal gereç filtresi kullanarak belirli Azure kaynaklarına kısıtlamak istiyorsanız yararlı olabilir. Daha fazla bilgi için bkz. [Ağ sanal gereçleri ile çıkış](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Sanal ağ erişim denetimi listesi (ACL) VNet dışından etkin olan bir Azure hizmet hesabına eriştiğinizde ne olur?
HTTP 403 veya HTTP 404 hatası döndürüldü.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Farklı bölgelerde oluşturulan bir sanal ağın alt ağları, başka bir bölgedeki Azure hizmet hesabına erişmelerine izin verildi mi? 
Evet, Azure hizmetlerinin çoğu için, farklı bölgelerde oluşturulan sanal ağlar VNet hizmet uç noktaları aracılığıyla başka bir bölgedeki Azure hizmetlerine erişebilir. Örneğin, bir Azure Cosmos DB hesabı Batı ABD veya Doğu ABD ve sanal ağlar birden çok bölgede yer alıyorsa, sanal ağ Azure Cosmos DB erişebilir. Depolama ve SQL özel durumlardır ve hem sanal ağın hem de Azure hizmetinin aynı bölgede olması gerekir.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Bir Azure hizmeti hem VNet ACL 'sine hem de bir IP güvenlik duvarına sahip olabilir mi?
Evet, VNet ACL ve IP güvenlik duvarı birlikte bulunabilir. Yalıtım ve güvenlik sağlamak için her iki özellik de birbirini tamamlmasını sağlar.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Azure hizmeti için hizmet uç noktası açık olan bir sanal ağı veya alt ağı silerseniz ne olur?
VNET ve alt ağların silinmesi bağımsız işlemlerdir ve Azure hizmetleri için hizmet uç noktaları açık olduğunda bile desteklenir. Azure hizmetlerinin VNet ACL 'Leri ayarlandığı durumlarda, bu sanal ağlar ve alt ağlar için, VNet hizmeti uç noktası açık olan VNet veya alt ağ silindiğinde, bu Azure hizmeti ile ilişkili VNet ACL bilgileri devre dışı bırakılır.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>VNet hizmet uç noktası etkin olan bir Azure hizmet hesabı silinirse ne olur?
Bir Azure hizmet hesabının silinmesi bağımsız bir işlemdir ve hizmet uç noktası ağ tarafında etkin olduğunda bile desteklenir ve sanal ağ ACL 'Leri Azure hizmet tarafında ayarlanır. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>VNet hizmet uç noktası etkin olan bir kaynağın kaynak IP adresine (alt ağdaki bir VM gibi) ne olur?
Sanal ağ hizmeti uç noktaları etkinleştirildiğinde, sanal ağınızın alt ağındaki kaynakların kaynak IP adresleri, Azure hizmetine giden trafik için genel ıPV4 adreslerini Azure sanal ağının özel IP adreslerine kullanarak değiştirir. Bunun, Azure hizmetlerinde daha önce genel ıPV4 adresi olarak ayarlanan belirli IP güvenlik duvarlarının başarısız olmasına neden olabileceğini unutmayın. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Hizmet uç noktası rotası her zaman önceliklidir mi?
Hizmet uç noktaları, BGP rotalarında önceliğe sahip olan ve hizmet uç noktası trafiği için en iyi yönlendirmeyi sağlayan bir sistem yolu ekler. Hizmet uç noktaları, her zaman hizmet trafiğini doğrudan sanal ağınızdan Microsoft Azure omurga ağındaki hizmete götürür. Azure 'un bir yolu nasıl seçtiği hakkında daha fazla bilgi için bkz. [Azure sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Alt ağdaki NSG, hizmet uç noktaları ile nasıl çalışır?
Azure hizmetine ulaşmak için NSG 'lerin giden bağlantılara izin verihtiyacı vardır. NSG 'larınız tüm Internet giden trafiği için açılırsa, hizmet uç noktası trafiğinin çalışması gerekir. Ayrıca, hizmet etiketlerini kullanarak giden trafiği yalnızca hizmet IP 'Leri ile sınırlayabilirsiniz.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Hizmet uç noktalarını ayarlamak için hangi izinlere ihtiyacım var?
Hizmet uç noktaları, sanal ağa yazma erişimi olan bir kullanıcı tarafından bağımsız bir sanal ağ üzerinde yapılandırılabilir. Azure hizmet kaynaklarını bir sanal ağa güvenli hale getirmek için kullanıcının eklenmekte olan alt ağlar için **Microsoft. Network/virtualNetworks/alt ağları/Jodavetli Aserviceendpoint/Action** iznine sahip olması gerekir. Bu izin, varsayılan olarak yerleşik hizmet yöneticisi rolüne dahildir ve özel roller oluşturularak değiştirilebilir. Yerleşik roller hakkında daha fazla bilgi edinin ve [özel rollere](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json)özel izinler atama.
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Sanal ağ trafiğini Azure hizmetlerine filtreleyip, VNet hizmet uç noktaları üzerinden yalnızca belirli Azure hizmet kaynaklarına izin verebilir miyim? 

Sanal ağ (VNet) hizmet uç noktası ilkeleri, Azure hizmetlerine sanal ağ trafiğini filtrelemenizi sağlar ve hizmet uç noktaları üzerinde yalnızca belirli Azure hizmet kaynaklarına izin verir. Uç nokta ilkeleri, Azure hizmetlerine yönelik sanal ağ trafiğinden ayrıntılı erişim denetimi sağlar. Hizmet uç noktası ilkeleri hakkında [buradan](virtual-network-service-endpoint-policies-overview.md)daha fazla bilgi edinebilirsiniz.

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Azure Active Directory (Azure AD) VNet hizmet uç noktalarını destekliyor mu?

Azure Active Directory (Azure AD) yerel olarak hizmet uç noktalarını desteklemez. VNet hizmet uç noktalarını destekleyen Azure hizmetlerinin tüm listesi [burada](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)görülebilir. Hizmet uç noktalarını destekleme hizmetleri altında listelenen "Microsoft. AzureActiveDirectory" etiketinin, ADLS Gen 1 ' e hizmet uç noktalarını desteklemek için kullanıldığını unutmayın. ADLS Gen 1 için Azure Data Lake Storage 1. sanal ağ tümleştirmesi, sanal ağınız ile Azure Active Directory (Azure AD) arasındaki sanal ağ hizmeti uç noktası güvenliğini kullanarak erişim belirtecinde ek güvenlik talepleri oluşturur. Ardından bu talepler sanal ağınız için Data Lake Storage 1. Nesil hesabınızda kimlik doğrulaması gerçekleştirme ve erişim izni verme amacıyla kullanılır. [Azure Data Lake Store Gen 1 VNET tümleştirmesi](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hakkında daha fazla bilgi edinin

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>VNet 'imde kaç VNet hizmeti uç noktasına Ayarlanabileceğim üzerinde herhangi bir sınırlama var mı?
Bir sanal ağdaki toplam VNet hizmeti uç noktası sayısı için bir sınır yoktur. Azure hizmet kaynağı (örneğin, Azure depolama hesabı) için hizmetler, kaynağın güvenliğini sağlamak için kullanılan alt ağ sayısı sınırlarını zorlayabilir. Aşağıdaki tabloda bazı örnek sınırları gösterilmektedir: 

|||
|---|---|
|Azure hizmeti| VNet kuralları sınırları|
|Azure Depolama| 100|
|Azure SQL| 128|
|Azure SQL Veri Ambarı|  128|
|Azure Keykasası|    127|
|Azure Cosmos DB|   64|
|Azure Olay Hub’ı|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store v1|  100|
 
>[!NOTE]
> Sınırlar, Azure hizmeti 'nin tabi değişikliklere karşı değişir. Hizmet ayrıntıları için ilgili hizmet belgelerine bakın. 




  



