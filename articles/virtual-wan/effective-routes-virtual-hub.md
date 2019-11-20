---
title: "Sanal hub 'ın geçerli yollarını görüntüleme: Azure sanal WAN | Microsoft Docs"
description: Azure sanal WAN 'da bir sanal hub için geçerli yolları görüntüle
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515856"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Bir sanal hub 'ın geçerli yollarını görüntüleme

Sanal WAN hub 'ınızın tüm yollarını Azure portal görüntüleyebilirsiniz. Rotaları görüntülemek için sanal hub 'a gidin ve ardından **yönlendirme-> geçerli yolları görüntüle**' yi seçin.

## <a name="understand"></a>Rotaları anlama

Aşağıdaki örnek sanal WAN yönlendirmesinin nasıl göründüğünü daha iyi anlamanıza yardımcı olabilir.

Bu örnekte, üç hub ile sanal bir WAN sunuyoruz. İlk hub Doğu ABD bölgedeyse, ikinci hub Batı Avrupa bölgedeyse ve üçüncü hub Batı ABD bölgesidir. Sanal bir WAN 'da tüm Hub 'lar birbirine bağlı. Bu örnekte, Doğu ABD ve Batı Avrupa hub 'larının şirket içi dallardan (bağlı bileşenler) ve Azure sanal ağlarının (bağlı bileşenler) bağlantılarının olduğunu varsayacağız.

Ağ sanal gereci (10.4.0.6) ile bir Azure VNet bağlı alanı (10.4.0.0/16) VNet 'e daha fazla eşlenmez (10.5.0.0/16). Merkez yol tablosu hakkında daha fazla bilgi için bu makalenin ilerleyen kısımlarında bulunan [ek bilgilere](#abouthubroute) bakın.

Bu örnekte, Batı Avrupa dalı 1 ' in Doğu ABD hub 'a ve Batı Avrupa hub 'ına bağlı olduğunu da varsaydık. Doğu ABD bir ExpressRoute bağlantı hattı, dalı 2 ' yi Doğu ABD hub 'ına bağlar.

![çizimindeki](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view"></a>Geçerli yolları görüntüle

Portalda ' etkin yolları görüntüle ' seçeneğini belirlediğinizde, Doğu ABD hub ['ına ait merkez yolu tablosunda](#routetable) gösterilen çıktıyı üretir.

Bunu perspektife koymak için, ilk satır Doğu ABD hub 'ın, VPN *sonraki atlama türü* bağlantısı (' sonraki atlama ' VPN Gateway örnek0 IP 10.1.0.6, Instance1 IP 10.1.0.7) nedeniyle 10.20.1.0/24 (dal 1) yolunu öğrenmiş olduğunu gösterir. *Yol* kaynağı kaynak kimliğine işaret eder. *As yolu* , Branch 1 Için as yolunu gösterir.

### <a name="routetable"></a>Merkez yol tablosu

"Yol olarak" görüntülemek için tablonun altındaki kaydırma çubuğunu kullanın.

| **Koy** |  **Sonraki atlama türü** | **Sonraki atlama** |  **Yol kaynağı** |**AS yolu** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Sanal ağ bağlantısı| Bağlantı üzerine |  |  |
|10.5.0.0/16| IP Adresi| 10.4.0.6|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| IP Adresi| `<Azure Firewall IP>` |/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Uzak Merkez|10.8.0.6, 10.8.0.7|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Uzak Merkez|  Bağlantı üzerine |/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Doğu ABD ve Batı Avrupa hub 'ları örnek topolojide birbirleriyle iletişim kurmadığında, öğrenilen yol (10.9.0.0/16) mevcut olmaz. Hub 'lar yalnızca doğrudan kendisine bağlı olan ağları duyurur.
>

## <a name="additional"></a>Ek bilgi

### <a name="abouthubroute"></a>Merkez yol tablosu hakkında

Sanal hub yolu oluşturabilir ve yolu sanal hub yolu tablosuna uygulayabilirsiniz. Sanal hub rota tablosuna birden fazla rota uygulayabilirsiniz. Bu, hedef VNet için bir IP adresi (genellikle bir bağlı olan VNet 'teki ağ sanal gereci (NVA)) yoluyla bir yol ayarlamanıza olanak sağlar. NVA 'lar hakkında daha fazla bilgi için bkz. [bir sanal hub 'dan NVA 'ya trafiği yönlendirme](virtual-wan-route-table-portal.md).

### <a name="aboutdefaultroute"></a>Varsayılan yol hakkında (0.0.0.0/0)

Bir sanal hub, bağlantıda ' Enabled ' olduğunda, bir sanal ağa, siteden siteye VPN 'e ve bir ExpressRoute bağlantısına, öğrenilen bir varsayılan yolu yaymaya olanak tanır. Bu bayrak, bir sanal ağ bağlantısını, bir VPN bağlantısını veya bir ExpressRoute bağlantısını düzenlediğinizde görülebilir. Hub VNet, ExpressRoute ve VPN bağlantılarında varsayılan olarak ' Enableınternetsecurity ' her zaman false 'tur.

Varsayılan yol, sanal WAN hub 'ında değil. Hub 'da bir güvenlik duvarı dağıtımının bir sonucu olarak veya başka bir bağlı sitede Zorlamalı tünel etkinse, varsayılan yol sanal WAN hub tarafından zaten öğrenilmiş ise yayılır.

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için bkz. [Sanal WAN'a Genel Bakış](virtual-wan-about.md).