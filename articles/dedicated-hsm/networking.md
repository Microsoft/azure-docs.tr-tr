---
title: İlgili ağ konuları - Azure ayrılmış HSM | Microsoft Docs
description: Ağ konuları Azure ayrılmış HSM dağıtımlar için geçerli genel bakış
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881276"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure ayrılmış HSM ağ

Azure ayrılmış HSM, yüksek oranda güvenli bir ağ ortamını gerektirir. Azure'dan olup böyle müşterinin geri buluta dağıtılmış uygulamalar kullanarak BT ortamı (şirket içi), ya da yüksek kullanılabilirlik senaryolarına. Azure ağı bu sağlar ve ele alınması gereken dört ayrı alanları vardır.

- Azure'da sanal ağınız (VNet) içindeki HSM cihazlarına oluşturma
- Bulut tabanlı kaynaklar yapılandırma ve HSM cihazların yönetimi için şirket içi bağlanma
- Oluşturma ve sanal ağlar arası bağlantı uygulama kaynakları ve HSM cihazlarına bağlanma
- Bölgeler arası iletişim için ve aynı zamanda yüksek kullanılabilirlik senaryolarını etkinleştirmek için sanal ağları bağlama

## <a name="virtual-network-for-your-dedicated-hsms"></a>Sanal ağ, ayrılmış HSM'ler için

Ayrılmış Hsm'lerin tümleşik bir sanal ağa ve müşterilerin kendi özel ağında azure'da yerleştirilir. Bu erişim cihazlara sanal makineler veya sanal ağda işlem kaynakları sağlar.  
Sanal ağ ve yetenekleri sağladığı Azure Hizmetleri tümleştirme hakkında daha fazla bilgi için bkz: [Azure Hizmetleri için sanal ağ](../virtual-network/virtual-network-for-azure-services.md) belgeleri.

### <a name="virtual-networks"></a>Sanal ağlar

Ayrılmış HSM cihaz sağlamadan önce müşterilerin, Azure'da bir sanal ağ oluşturma veya müşterilerin abonelikte zaten var olan bir ilk gerekir. Sanal ağ için ayrılmış HSM cihazını güvenlik çevresi tanımlar. Sanal ağlar oluşturma hakkında daha fazla bilgi için bkz. [sanal ağ belgeleri](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Alt ağlar

Alt ağlar, sanal ağ yerleştirebilirsiniz, Azure kaynaklarını kullanılabilir ayrı adres alanları ölçütü. Ayrılmış Hsm'lerin sanal ağdaki bir alt ağa dağıtılır. Müşterinin alt ağında dağıtılan her bir ayrılmış HSM cihaz bu alt ağdan özel IP adresi alır. HSM cihazının dağıtıldığı alt ağın hizmete açıkça atanmış olması gerekir: Microsoft. HardwareSecurityModules/ayrılmış Atedhsms. Bu alt ağa dağıtım için HSM hizmeti belirli izinleri verir. Ayrılmış HSM'ler için temsilci seçme, alt ağdaki belirli ilke kısıtlamalarını uygular. Şu anda temsilci alt ağlarda ağ güvenlik grupları (Nsg'ler) ve kullanıcı tanımlı yollar (Udr) desteklenmez. Bir alt ağ için ayrılmış Hsm'lerin temsilci sonra sonuç olarak, yalnızca HSM kaynakları dağıtmak için kullanılabilir. Alt ağa herhangi bir müşteri kaynakları dağıtımı başarısız olur.


### <a name="expressroute-gateway"></a>ExpressRoute ağ geçidi

Bir HSM cihazını HSM cihazını bir tümleştirme azure'a yerleştirilmesi gereken yere müşteriler alt ağdaki bir ER ağ geçidi yapılandırmasını geçerli mimariye gereksinimi var. Bu ER ağ geçidi, Azure müşterilerine HSM cihazlarına şirket içi konumlara bağlanmak için kullanılan olamaz.

## <a name="connecting-your-on-premises-it-to-azure"></a>Şirket içi bağlanma BT azure'a

Bulut tabanlı kaynaklar oluşturulurken özel bir bağlantı şirket içi geri için tipik bir gereksinim olduğu BT kaynakları. Ayrılmış HSM söz konusu olduğunda, bu genellikle HSM cihazları yapılandırmak HSM istemci yazılımının ve yedeklemeleri ve günlükleri, analiz için HSM'ler alınan gibi etkinlikler için de olacaktır. Seçenekleri gibi bir anahtar karar noktası bağlantısının doğasını ' dir.  Büyük olasılıkla olacaktır (HSM'ler dahil) kaynaklarla Azure bulutunda güvenli iletişim gerektiren birden çok şirket içi kaynaklara en esnek seçeneği siteden siteye VPN aynıdır. Bu bağlantıyı kolaylaştırmak için bir VPN cihazı için bir müşteri kuruluş gerektirir. Noktadan siteye VPN bağlantısı, şirket içi bir tek yönetim iş istasyonu gibi tek bir uç nokta varsa kullanılabilir.
Bağlantı seçenekleri hakkında daha fazla bilgi için bkz. [VPN Gateway planlama seçenekleri](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Şu anda ExpressRoute, şirket içi kaynaklara bağlantı için bir seçenek değil. Bu da unutulmamalıdır ExpressRoute ağ geçidini yukarıda açıklanan şekilde kullanılan, bağlantıları şirket içi altyapı için uygun değildir.

### <a name="point-to-site-vpn"></a>Noktadan siteye VPN

Noktadan siteye sanal özel ağ tek bir uç nokta güvenli bağlantı en basit biçimidir şirket içi. Bu, yalnızca tek bir yönetim iş istasyonu için ayrılmış Hsm'lerin Azure tabanlı olmasını istiyorsanız uygun olabilir.

### <a name="site-to-site-vpn"></a>Siteden siteye VPN

Ayrılmış HSM'ler Azure tabanlı ve şirket içi arasında güvenli iletişim için bir siteden siteye sanal özel ağ sağlayan BT. Bunu yapmak için bir neden, HSM 'nin şirket içi olarak bir yedekleme tesisi olmasını ve yedeklemeyi çalıştırmak için iki ile arasında bağlantı olmasını gerektirir.

## <a name="connecting-virtual-networks"></a>Sanal ağları bağlama

Ayrılmış HSM için bir normal dağıtım mimarisi, bir tek bir sanal ağ ve HSM cihazlarına oluşturulur ve sağlanan karşılık gelen bir alt ağ ile başlar. Bu aynı bölge içinde de olabilir ek sanal ağlar ve alt ağlar ayrılmış HSM kullanma yapacağı uygulama bileşenleri için. Bu ağlar arasında iletişim sağlamak için sanal ağ eşlemesi kullanın.

### <a name="virtual-network-peering"></a>Sanal ağ eşleme

Diğer kişilerin kaynaklara erişmesi gereken bir bölgede birden fazla sanal ağ olduğunda, sanal ağ eşlemesi bunlar arasında güvenli iletişim kanalları oluşturmak için kullanılabilir.  Sanal Ağ eşlemesi yalnızca güvenli iletişimi sağlar, ancak ayrıca Azure içinde kaynaklar arasında düşük gecikme süreli ve yüksek bant genişlikli bağlantı sağlar.

![Ağ eşlemesi](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Azure bölgeleri arasında bağlanma

HSM cihazlarına, alternatif bir HSM'ye trafiği yönlendirmek için yazılım kitaplıkları aracılığıyla sahipsiniz. Trafik yeniden yönlendirmesi, cihazlar veya erişim bir cihaz kaybolur yararlıdır. Bölgesel düzeyinde hata senaryoları HSM'ler de başka bölgelerde dağıtarak ve farklı bölgelerdeki sanal ağlar arasındaki iletişimin etkinleştirilmesi azaltılabilir.

### <a name="cross-region-ha-using-vpn-gateway"></a>Çapraz bölge HA VPN ağ geçidini kullanma

Global olarak dağıtılmış uygulamaları veya yüksek kullanılabilirlik bölgesel yük devretme senaryolarını bölgelerdeki sanal ağları bağlamak için gereklidir. Azure ayrılmış HSM ile iki sanal ağ arasında güvenli bir tünel sağlayan VPN ağ geçidi kullanarak yüksek kullanılabilirlik sağlanabilir. Vnet-Vnet bağlantıları hakkında daha fazla bilgi için VPN ağ geçidini kullanarak başlıklı makaleye bakın [VPN ağ geçidi nedir?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Küresel Vnet eşlemesi, ayrılmış HSM'ler senaryolarıyla bu zamanda ve VPN ağ geçidi bunun yerine kullanılması gereken bölgeler arası bağlantısı kullanılamıyor. 

![Genel sanal ağ](media/networking/global-vnet.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Sık sorulan sorular](faq.md)
- [Desteklenebilirliği](supportability.md)
- [Yüksek kullanılabilirlik](high-availability.md)
- [Fiziksel güvenlik](physical-security.md)
- [İzleme](monitoring.md)
- [Dağıtım mimarisi](deployment-architecture.md)