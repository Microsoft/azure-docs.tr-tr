---
title: Application Gateway ve arka uç havuzları arasında güvenli trafik
titleSuffix: Azure Virtual WAN
description: Güvenli bir sanal WAN hub 'ına bağlı bir bağlı olan VNet 'te dağıtılan bir uygulama ağ geçidi üzerinden dolaşan güvenli trafiğe yönlendirme için senaryolar.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315674"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Senaryo: Application Gateway ve arka uç havuzları arasındaki güvenli trafik

Sanal WAN sanal hub 'ı yönlendirme ile çalışırken, kullanılabilecek oldukça az sayıda senaryo vardır. Bu senaryoda, bir kullanıcının trafiği, güvenli bir sanal WAN hub 'ına (Azure Güvenlik Duvarı ile sanal WAN hub 'ı) bağlı olan bir bağlı olan VNet 'te dağıtılan bir uygulama ağ geçidi üzerinden Azure 'a girer. Amaç, uygulama ağ geçidi ile arka uç havuzları arasındaki trafiği incelemek için güvenli sanal hub 'da Azure Güvenlik Duvarı 'nı kullanmaktır.

Bu senaryoda, uygulama ağ geçidi ve arka uç havuzlarının aynı VNet 'te ya da farklı VNET 'lerde olmasına bağlı olarak iki özel tasarım deseni vardır.

* **Senaryo 1:** Uygulama ağ geçidi ve arka uç havuzları, sanal bir WAN hub 'ına (ayrı alt ağlar) eşlenmiş sanal ağ içinde bulunur.
* **Senaryo 2:** Uygulama ağ geçidi ve arka uç havuzları, sanal bir WAN hub 'ına eşlenmiş farklı sanal ağlarda bulunur.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>Senaryo 1-aynı VNet

Bu senaryoda, uygulama ağ geçidi ve arka uç havuzları, sanal bir WAN hub 'ına (ayrı alt ağlar) eşlenmiş sanal ağ içinde bulunur.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Senaryo 1 için diyagram." lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>İş akışı

Şu anda, sanal WAN yol tablosundan bağlı olan sanal ağlara tanıtılan yollar, sanal ağ VNet 'in alt ağlarında değil, tüm sanal ağa uygulanır. Sonuç olarak, bu senaryoyu etkinleştirmek için Kullanıcı tanımlı yollar gereklidir. Kullanıcı tanımlı yollar (UDR) hakkında daha fazla bilgi için bkz. [sanal ağ özel yolları](../virtual-network/virtual-networks-udr-overview.md#user-defined).


1. Azure Güvenlik Duvarı Yöneticisi 'nde, uygulama ağ geçidi ve arka uç havuzlarını içeren bağlı olan sanal ağda, **güvenli Internet trafiğini etkinleştir** ' i seçin ve **güvenli özel trafiği etkinleştirin**.
1. Uygulama ağ geçidi alt ağında Kullanıcı tanımlı yolları (UDRs) yapılandırın.

   * Application Gateway 'in trafiği doğrudan Internet 'e gönderebilmesini sağlamak için aşağıdaki UDR 'yi belirtin:

     * **Adres ön eki:** 0.0.0.0.0/0
     * **Sonraki atlama:** Internet

   * Uygulama ağ geçidinin, sanal WAN hub 'ında Azure Güvenlik Duvarı aracılığıyla arka uç havuzuna trafik gönderebilmesini sağlamak için aşağıdaki UDR 'yi belirtin:

      * **Adres ön eki:** Arka uç havuzu alt ağı (10.2.0.0/24)
      * **Sonraki atlama:** Azure Güvenlik Duvarı özel IP 'si

1. Arka uç havuzu alt ağında Kullanıcı tanımlı bir yol (UDR) yapılandırın.

   * **Adres ön eki:** Application Gateway alt ağ
   * **Sonraki atlama:** Azure Güvenlik Duvarı özel IP 'si

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>Senaryo 2-farklı sanal ağlar

Bu senaryoda, uygulama ağ geçidi ve arka uç havuzları, sanal bir WAN hub 'ına eşlenmiş farklı sanal ağlarda bulunur.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="Senaryo 2 için diyagram." lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>İş akışı

Şu anda, sanal WAN yol tablosundan bağlı olan sanal ağlara tanıtılan yollar, sanal ağ VNet 'in alt ağlarında değil, tüm sanal ağa uygulanır. Sonuç olarak, bu senaryoyu etkinleştirmek için Kullanıcı tanımlı yollar gereklidir. Kullanıcı tanımlı yollar (UDR) hakkında daha fazla bilgi için bkz. [sanal ağ özel yolları](../virtual-network/virtual-networks-udr-overview.md#user-defined).

1. **Azure Güvenlik Duvarı Yöneticisi**'Nde, **güvenli Internet trafiğini etkinleştir** ' i seçin ve her iki bağlı ağ sanal ağında **güvenli özel trafiği etkinleştirin** .

1. Uygulama ağ geçidi alt ağında Kullanıcı tanımlı yolları (UDRs) yapılandırın. Application Gateway 'in trafiği doğrudan Internet 'e gönderebilmesini sağlamak için aşağıdaki UDR 'yi belirtin:

   * **Adres ön eki:** 0.0.0.0.0/0
   * **Sonraki atlama:** Internet

## <a name="next-steps"></a>Sonraki adımlar

* Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).
* Kullanıcı tanımlı rotalar hakkında daha fazla bilgi için bkz. [sanal ağ özel yolları](../virtual-network/virtual-networks-udr-overview.md#user-defined).
* Sanal WAN güvenli sanal hub 'lar hakkında daha fazla bilgi için bkz. [güvenli sanal hub 'lar](../firewall-manager/secured-virtual-hub.md).
