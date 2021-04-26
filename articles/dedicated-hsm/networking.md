---
title: Ağ değerlendirmeleri-Azure ayrılmış HSM | Microsoft Docs
description: Azure ayrılmış HSM dağıtımları için geçerli olan ağ konularına genel bakış
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 3370389027805cfb5a68b5b0551d14dc31154804
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611846"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure ayrılmış HSM ağı

Azure ayrılmış HSM, yüksek güvenlikli bir ağ ortamı gerektirir. Bu, Azure bulutunun şirket BT ortamına (Şirket içi), dağıtılmış uygulamalar kullanılarak veya yüksek kullanılabilirlik senaryolarında olup olmadığı doğru bir şekilde yapılır. Azure ağ bunu sağlar ve değinilmesi gereken dört farklı alan vardır.

- Azure 'da sanal ağınız (VNet) içinde HSM cihazları oluşturma
- HSM cihazlarının yapılandırması ve yönetimi için şirket içinde bulut tabanlı kaynaklara bağlanma
- Bağlama ve bağlantı temelli uygulama kaynakları ve HSM cihazları için sanal ağlar oluşturma ve bağlama
- Ağlarda iletişim kurmak için sanal ağları bölgeler arasında bağlama ve ayrıca yüksek kullanılabilirlik senaryolarını etkinleştirme

## <a name="virtual-network-for-your-dedicated-hsms"></a>Adanmış HSM 'niz için sanal ağ

Adanmış HSM 'ler bir sanal ağla tümleşiktir ve müşterilere Azure 'da özel ağa yerleştirilir. Bu, sanal makinelerden cihazlara veya sanal ağdaki işlem kaynaklarına erişim sağlar.  
Azure hizmetlerini sanal ağla ve sağladığı yeteneklere tümleştirme hakkında daha fazla bilgi için bkz. [Azure hizmetleri Için sanal ağ](../virtual-network/virtual-network-for-azure-services.md) belgeleri.

### <a name="virtual-networks"></a>Sanal ağlar

Özel bir HSM cihazını sağlamadan önce, müşterilerin ilk olarak bir Azure Ağa gelen oluşturması veya müşteriler aboneliğinde zaten mevcut olan bir tane kullanması gerekir. Sanal ağ, adanmış HSM cihazı için güvenlik çevre birimi tanımlar. Sanal ağlar oluşturma hakkında daha fazla bilgi için bkz. [sanal ağ belgeleri](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Alt ağlar

Alt ağlar sanal ağı, yerleştirdiğiniz Azure kaynakları tarafından kullanılabilen ayrı adres alanlarına bölüler. Adanmış HSM 'ler, sanal ağdaki bir alt ağa dağıtılır. Müşterinin alt ağına dağıtılan her bir adanmış HSM aygıtı, bu alt ağdan özel bir IP adresi alır. HSM cihazının dağıtıldığı alt ağın hizmete açıkça atanmış olması gerekir: Microsoft. HardwareSecurityModules/ayrılmış Atedhsms. Bu, alt ağa dağıtım için HSM hizmetine bazı izinler verir. Adanmış HSM 'lerin temsilciliğini, alt ağda belirli ilke kısıtlamalarını uygular. Ağ güvenlik grupları (NSG 'ler) ve User-Defined yolları (UDRs) Şu anda temsilci alt ağlarda desteklenmez. Sonuç olarak, bir alt ağ adanmış HSM 'ler için temsilci seçildikten sonra yalnızca HSM kaynaklarını dağıtmak için kullanılabilir. Diğer müşteri kaynaklarının alt ağa dağıtılması başarısız olur.


### <a name="expressroute-gateway"></a>ExpressRoute ağ geçidi

Geçerli mimarinin gereksinimi, HSM cihazının Azure ile tümleştirilmesini sağlamak için bir HSM cihazının yerleştirilmesi gereken müşteriler alt ağındaki bir ER ağ geçidinin yapılandırılmasına neden olur. Bu ER Gateway, şirket içi konumları Azure 'daki müşterilerin HSM cihazlarına bağlamak için kullanılamaz.

## <a name="connecting-your-on-premises-it-to-azure"></a>Şirket içi BT hesabınızı Azure 'a bağlama

Bulut tabanlı kaynaklar oluştururken, şirket içi BT kaynaklarına geri bir özel bağlantı için tipik bir gereksinimdir. Bu özel HSM söz konusu olduğunda, HSM istemci yazılımının HSM cihazlarını yapılandırması ve ayrıca yedekleme ve günlükleri gibi etkinlikler ve analiz için HSMs 'tan günlük çekme işlemleri için ağırlıklı olacaktır. Burada önemli bir karar noktası, seçenekler olduğu gibi bağlantının doğasına alınır.  Azure bulutundaki kaynaklarla (HSM 'ler dahil) güvenli iletişim gerektiren birden çok şirket içi kaynak olacağı için, en esnek seçenek siteden siteye VPN 'dir. Bu, bir müşteri kuruluşunun bağlantıyı kolaylaştırmak için bir VPN cihazına sahip olmasını gerektirir. Bir noktadan siteye VPN bağlantısı, tek bir yönetim iş istasyonu gibi yalnızca şirket içi tek bir uç nokta varsa kullanılabilir.
Bağlantı seçenekleri hakkında daha fazla bilgi için bkz. [VPN Gateway planlama seçenekleri](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Şu anda ExpressRoute, şirket içi kaynaklarla bağlantı için bir seçenek değildir. Ayrıca, yukarıda açıklanan ExpressRoute ağ geçidinin şirket içi altyapıyla bağlantı için değil, aynı zamanda kullanılması da not edilmelidir.

### <a name="point-to-site-vpn"></a>Noktadan siteye VPN

Noktadan siteye sanal özel ağ, şirket içi tek bir uç noktaya yönelik güvenli bağlantının en basit biçimidir. Bu, yalnızca Azure tabanlı adanmış HSM 'ler için tek bir yönetim iş istasyonuna sahip olmak istiyorsanız ilgili olabilir.

### <a name="site-to-site-vpn"></a>Siteler arası VPN

Siteden siteye sanal özel ağ, Azure tabanlı adanmış HSM 'ler ve şirket içi BT 'niz arasında güvenli iletişim sağlar. Bunu yapmak için bir neden, HSM 'nin şirket içi olarak bir yedekleme tesisi olmasını ve yedeklemeyi çalıştırmak için iki ile arasında bağlantı olmasını gerektirir.

## <a name="connecting-virtual-networks"></a>Sanal ağları bağlama

Adanmış HSM için tipik bir dağıtım mimarisi, tek bir sanal ağ ve HSM cihazlarının oluşturulduğu ve sağlandığı ilgili alt ağ ile başlar. Aynı bölge içinde, özel HSM 'yi kullanacak uygulama bileşenleri için ek sanal ağlar ve alt ağlar da olabilir. Bu ağlar genelinde iletişimi etkinleştirmek için sanal ağ eşlemesi kullanırız.

### <a name="virtual-network-peering"></a>Sanal ağ eşleme

Her birinin kaynağına erişmesi gereken bir bölgede birden çok sanal ağ olduğunda, aralarında güvenli iletişim kanalları oluşturmak için sanal ağ eşlemesi kullanılabilir.  Sanal ağ eşlemesi yalnızca güvenli iletişim sağlamaz, ancak Azure 'daki kaynaklar arasında düşük gecikmeli ve yüksek bant genişliğine sahip bağlantılar sağlar.

![ağ eşlemesi](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Azure bölgeleri arasında bağlantı kurma

HSM cihazlarının, trafiği alternatif bir HSM 'ye yeniden yönlendirmek için yazılım kitaplıkları aracılığıyla yeteneği vardır. Cihazların başarısız olması veya bir cihaza erişimin kaybolması durumunda trafik yeniden yönlendirme yararlı olur. Bölgesel düzey hata senaryoları, diğer bölgelerde HSM 'ler dağıtarak ve bölgeler arasında sanal ağlar arasındaki iletişimi etkinleştirerek azaltılabilir.

### <a name="cross-region-ha-using-vpn-gateway"></a>Çapraz bölge HA VPN Gateway kullanma

Küresel olarak dağıtılan uygulamalarda veya yüksek kullanılabilirlik bölgesel yük devretme senaryolarında, sanal ağların bölgeler arasında bağlanması gerekir. Azure ayrılmış HSM ile, yüksek kullanılabilirlik, iki sanal ağ arasında güvenli bir tünel sağlayan bir VPN Gateway kullanılarak elde edilebilir. VPN Gateway kullanarak VNET-VNET bağlantıları hakkında daha fazla bilgi için [VPN Gateway nedir?](../vpn-gateway/design.md#V2V) başlıklı makaleye bakın.

> [!NOTE]
> Genel VNET eşlemesi Şu anda adanmış HSM 'ler ile bölgeler arası bağlantı senaryolarında kullanılamaz ve bunun yerine VPN Gateway kullanılmalıdır. 

![Diyagramda iki sanal P N Ağ Geçidi ile bağlanmış iki bölge gösterilmektedir. Her bölge eşlenmiş sanal ağlar içerir.](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>Ağ kısıtlamaları
> [!NOTE]
> Alt ağ temsili kullanan adanmış HSM hizmeti kısıtlaması, bir HSM dağıtımı için hedef ağ mimarisi tasarlarken göz önünde bulundurmanız gereken kısıtlamalardır. Alt ağ temsilcisinin kullanımı, NSG 'ler, UDRs ve küresel VNet eşlemesi adanmış HSM için desteklenmez. Aşağıdaki bölümler, bu yetenekler için aynı veya benzer bir sonuca ulaşmak için alternatif teknikler sağlar. 

Adanmış HSM VNet 'te bulunan HSM NIC 'i ağ güvenlik gruplarını veya Kullanıcı tanımlı yolları kullanamaz. Bu, varsayılan reddetme ilkelerinin adanmış HSM VNet açısından ayarlanmayacağı ve ayrılmış HSM hizmetine erişim kazanmak için diğer ağ kesimlerinin allowlistelendiğinden olması gerektiği anlamına gelir. 

Ağ sanal gereçleri (NVA) proxy çözümü, Ayrıca, transit/DMZ hub 'ındaki bir NVA güvenlik duvarının, HSM NIC 'nin önüne mantıksal olarak yerleştirilmesi ve bu sayede NSG 'ler ve UDRs için gerekli alternatifi sağlaması de sağlar.

### <a name="solution-architecture"></a>Çözüm Mimarisi
Bu ağ tasarımı aşağıdaki öğeleri gerektirir:
1.  NVA proxy katmanı içeren bir transit veya DMZ hub VNet. İdeal olarak iki veya daha fazla NVA 'lar vardır. 
2.  Özel eşleme etkin olan bir ExpressRoute bağlantı hattı ve transit hub VNet bağlantısı.
3.  Transit hub VNet ve adanmış HSM VNet arasındaki VNet eşlemesi.
4.  Bir NVA güvenlik duvarı veya Azure Güvenlik Duvarı, bir seçenek olarak hub 'da DMZ hizmetleri sunmaktadır. 
5.  Ek iş yükü sanal ağları hub VNet 'e eşlenebilir. Gemalto istemcisi, hub VNet üzerinden adanmış HSM hizmetine erişebilir.

![Diyagram, NSG ve UDR geçici çözümü için NVA proxy katmanı içeren bir DMZ hub VNet gösterir](media/networking/network-architecture.png)

NVA proxy çözümünü eklemek Ayrıca, transit/DMZ hub 'ındaki NVA güvenlik duvarının, HSM NIC 'nin önüne mantıksal olarak yerleştirilmesi ve bu sayede gerekli varsayılan reddetme ilkelerini sağlaması de sağlar. Bizim örneğimizde, bu amaçla Azure Güvenlik duvarını kullanacağız ve aşağıdaki öğelere sahip olacak:
1. DMZ hub VNet 'te "AzureFirewallSubnet" alt ağına dağıtılan bir Azure Güvenlik Duvarı
2. Azure Güvenlik Duvarı 'na trafik uçlu Azure ıDB özel uç noktasına yönlendiren bir UDR içeren bir yönlendirme tablosu. Bu yönlendirme tablosu, Customer ExpressRoute sanal ağ geçidinin bulunduğu GatewaySubnet öğesine uygulanır
3. AzureFirewall içindeki ağ güvenlik kuralları, TCP bağlantı noktası 1792 ' te dinleme yapan bir güvenilen kaynak aralığı ve Azure ıTıL özel uç noktası arasında iletmeyi sağlar. Bu güvenlik mantığı, adanmış HSM hizmetine yönelik gerekli "varsayılan reddetme" ilkesini ekler. Yani, adanmış HSM hizmetine yalnızca güvenilen kaynak IP aralıklarına izin verilir. Diğer tüm aralıklar bırakılacak.  
4. UDR ile trafik yoğunluğunu Azure Güvenlik Duvarı 'na şirket içi olarak yönlendiren bir yönlendirme tablosu. Bu yönlendirme tablosu NVA proxy alt ağına uygulanır. 
5. Kaynak olarak yalnızca Azure Güvenlik duvarının alt ağ aralığına güvenmek ve yalnızca TCP bağlantı noktası 1792 üzerinden HSM NIC IP adresine iletme izni vermek için proxy NVA alt ağına uygulanan bir NSG. 

> [!NOTE]
> NVA proxy katmanı, HSM NIC 'sine ilettiğinden istemci IP adresini SNAT olarak alacak olduğundan, HSM VNet ve DMZ hub VNet arasında UDRs gerekmez.  

### <a name="alternative-to-udrs"></a>UDRs 'ye alternatif
Yukarıda bahsedilen NVA katmanı çözümü, UDRs 'ye alternatif olarak işe yarar. Dikkat edilmesi için bazı önemli noktaları vardır.
1.  Dönüş trafiğinin doğru şekilde yönlendirilmesine izin vermek için NVA 'da ağ adresi çevirisi yapılandırılmalıdır.
2. Müşteriler, bir NAT için VNA kullanmak üzere Luna HSM yapılandırmasında istemci IP 'sini iade etmelidir. Aşağıdaki komutlar örnek olarak servce.
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  NVA katmanına giriş trafiği için UDRs dağıtın. 
4. Tasarıma göre, HSM alt ağları platform katmanına giden bir bağlantı isteği başlatmaz.

### <a name="alternative-to-using-global-vnet-peering"></a>Küresel Sanal Ağ Eşleme kullanımı için alternatif
Küresel VNet eşlemesi için alternatif olarak kullanabileceğiniz birkaç mimarinin vardır.
1.  [VNET-vnet VPN Gateway bağlantısını](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) kullanma 
2.  HSM VNET 'i bir ER devresi ile başka bir VNET ile bağlayın. Bu, doğrudan şirket içi bir yol gerektiğinde veya VPN VNET olduğunda en iyi şekilde geçerlidir. 

#### <a name="hsm-with-direct-express-route-connectivity"></a>Doğrudan Express Route bağlantısı ile HSM
![Diyagramda doğrudan Express Route bağlantısı ile HSM gösterilmektedir](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Sık sorulan sorular](faq.md)
- [Desteklenebilirlik](supportability.md)
- [Yüksek kullanılabilirlik](high-availability.md)
- [Fiziksel Güvenlik](physical-security.md)
- [İzleme](monitoring.md)
- [Dağıtım mimarisi](deployment-architecture.md)
