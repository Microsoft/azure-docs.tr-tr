---
title: BGP ve Azure VPN ağ geçitlerine genel bakış | Microsoft Docs
description: Bu makale Azure VPN Gateways ile BGP’ye genel bakış sağlar.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/25/2019
ms.author: yushwang
ms.openlocfilehash: e85eb756e416f1170a0cb2b19ab68888b5a9422f
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516432"
---
# <a name="about-bgp-with-azure-vpn-gateway"></a>Azure VPN Gateway ile BGP hakkında
Bu makalede, Azure VPN Gateway 'da BGP (Sınır Ağ Geçidi Protokolü) desteğine genel bir bakış sunulmaktadır.

BGP iki veya daha fazla ağ arasında yönlendirme ve ulaşılabilirlik bilgilerini takas etmek üzere İnternet’te yaygın olarak kullanılan standart yönlendirme protokolüdür. BGP, Azure Virtual Networks bağlamında kullanıldığında her iki ağ geçidini ön eklerin ilgili ağ geçitlerinden veya yönlendiricilerden geçmeye yönelik kullanılabilirliği ve ulaşılabilirliği konusunda bilgilendiren “yolları” değiştirmek amacıyla, Azure VPN Gateways’i ve BGP eşlikleri veya komşuları olarak adlandırılan şirket içi VPN cihazlarınızı etkinleştirir. BGP ayrıca bir BGP ağ geçidinin öğrendiği yolları bir BGP eşliğinden diğer tüm BGP eşliklerine yayarak birden fazla ağ arasında geçiş yönlendirmesi sağlayabilir. 

## <a name="why"></a>BGP neden kullanılmalıdır?
BGP, Azure Yol Tabanlı VPN ağ geçitleri ile birlikte kullanabileceğiniz isteğe bağlı bir özelliktir. Özelliği etkinleştirmeden önce şirket içi VPN cihazlarınızın da BGP’yi desteklediğinden emin olmanız gerekir. Azure VPN ağ geçitlerini ve şirket içi VPN cihazlarınızı BGP olmadan kullanmaya devam edebilirsiniz. BGP ile ağlarınız ve Azure arasında statik yollar kullanmaya (BGP olmadan) *karşılık* BGP ile dinamik yönlendirme kullanmanın eşdeğeridir.

BGP’nin çeşitli avantajları ve yeni özellikleri vardır:

### <a name="prefix"></a>Otomatik ve esnek ön ek güncelleştirmelerini destekleme
BGP ile yalnızca belirli bir BGP eşliğine IPSec S2S VPN tüneli üzerinden en küçük ön eki bildirmeniz gerekir. Şirket içi VPN cihazınızın BGP eşliği IP adresinin ana bilgisayar ön eki (/32) kadar küçük olabilir. Azure Virtual Network’un erişmesine izin vermek üzere hangi şirket içi ağ ön eklerinin Azure’a tanıtılacağını denetleyebilirsiniz.

Ayrıca, büyük bir özel IP adres alanı (örneğin, 10.0.0.0/8) gibi VNet adres öneklerinizden bazılarını içerebilen daha büyük ön ekler tanıtabilirsiniz. Ön ekler, VNet öneklerinizden herhangi biriyle aynı olamaz. VNet ön eklerinizle aynı olan yollar reddedilir.

### <a name="multitunnel"></a>Sanal ağ ile şirket içi site arasında BGP 'ye dayalı otomatik yük devretme ile birden çok tüneli destekleme
Azure VNet ile şirket içi VPN cihazlarınız arasında aynı konumda birden fazla bağlantı kurabilirsiniz. Bu özellik aktif-aktif bir yapılandırmadaki iki ağ arasında birden fazla tünel (yol) sağlar. Tünellerden birinin bağlantısı kesilirse, ilgili yollar BGP üzerinden geri alınacaktır ve trafik otomatik olarak kalan tünellere kayar.

Aşağıdaki diyagramda yüksek oranda kullanılabilen bu kurulumun basit bir örneği gösterilmiştir:

![Birden fazla etkin yol](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>Şirket içi ağlarınız ile birden çok Azure sanal ağı arasında geçiş yönlendirme desteği
BGP, doğrudan veya dolaylı olarak bağlı olmasına bakılmaksızın birden fazla ağ geçidinin farklı ağlardaki ön ekleri öğrenmesini ve yaymasını sağlar. Bu özellik şirket içi siteleriniz arasında veya birden fazla Azure Sanal Ağında Azure VPN ağ geçitleri ile geçiş yönlendirmesi sağlayabilir.

Aşağıdaki diyagramda Microsoft Networks içindeki Azure VPN ağ geçitleri üzerinden iki şirket içi ağ arasındaki trafiği geçebilen birden fazla yola sahip çoklu atlamalı bir topolojinin örneği gösterilmektedir:

![Çoklu atlamalı geçiş](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>BGP SSS
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
BGP’yi şirketler arası ve VNet’ten VNet’te bağlantılar için yapılandırma adımları için bkz. [Azure VPN ağ geçitlerinde BGP kullanmaya başlama](vpn-gateway-bgp-resource-manager-ps.md).

