---
title: Azure Route Server (Önizleme) hakkında ExpressRoute ve Azure VPN desteği
description: Azure Route sunucusunun ExpressRoute ve Azure VPN ağ geçitleri ile nasıl etkileşime gireceğini öğrenin.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101680530"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>ExpressRoute ve Azure VPN için Azure Route Server (Önizleme) desteği hakkında

Azure Route sunucusu yalnızca Azure 'da çalışan üçüncü taraf ağ sanal gereçlerini (NVA) değil, ExpressRoute ve Azure VPN ağ geçitleriyle sorunsuz bir şekilde tümleşir. Ağ geçidi ve Azure rota sunucusu arasında BGP eşlemesini yapılandırmanız veya yönetmeniz gerekmez. Ağ geçidi ve Azure rota sunucusu arasında bir basit [yapılandırma değişikliği](quickstart-configure-route-server-powershell.md#route-exchange)ile yönlendirme değişimini etkinleştirebilirsiniz.

> [!IMPORTANT]
> Azure yol sunucusu (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Nasıl çalışır?

Bir Azure yol sunucusunu bir ExpressRoute ağ geçidi ve bir sanal ağda bir NVA ile birlikte dağıtırken, varsayılan olarak Azure yol sunucusu, aldığı yolları NVA ve ExpressRoute ağ geçidinden birbirleriyle yayılmaz. Yönlendirme değişimini etkinleştirdikten sonra, ExpressRoute ve NVA her birinin yollarını öğrentirecektir.

Örneğin, aşağıdaki diyagramda:

* SDWAN gereci, ExpressRoute 'a bağlanan ve sanal ağ rotası ile birlikte "Şirket içi 2" yolu üzerinden Azure Route Server 'dan alınır.

* ExpressRoute ağ geçidi, Azure Route Server 'daki sanal ağ yoluyla birlikte SDWAN gerecine bağlı olan "Şirket içi 1" öğesinden yolu alır.

    ![Yönlendirme sunucusu ile yapılandırılmış ExpressRoute gösteren diyagram.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

SDWAN gerecini Azure VPN Gateway ile de değiştirebilirsiniz. Azure VPN Gateway ve ExpressRoute tam olarak yönetildiğinden, yalnızca iki şirket içi ağ için yönlendirme değişimini, birbirleriyle konuşmak üzere etkinleştirmeniz gerekir.

> [!IMPORTANT] 
> Azure VPN ağ geçidi, [**etkin-etkin**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) modda yapılandırılmalıdır.
>

![Yönlendirme sunucusu ile yapılandırılmış ExpressRoute ve VPN Gateway 'i gösteren diyagram.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Route sunucusu](route-server-faq.md)hakkında daha fazla bilgi edinin.
- [Azure Route Server 'ı yapılandırmayı](quickstart-configure-route-server-powershell.md)öğrenin.
- [Azure ExpressRoute ve Azure VPN birlikte bulunma](../expressroute/expressroute-howto-coexist-resource-manager.md)hakkında daha fazla bilgi edinin.
