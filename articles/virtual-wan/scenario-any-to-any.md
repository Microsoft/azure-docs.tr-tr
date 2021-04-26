---
title: 'Senaryo: any-any'
titleSuffix: Azure Virtual WAN
description: Any ile herhangi bir yönlendirme için senaryolar
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 3b5002873160490dfb7b8d3ad9790f9c6f1e8ae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99525797"
---
# <a name="scenario-any-to-any"></a>Senaryo: any-any

Sanal WAN sanal hub 'ı yönlendirme ile çalışırken, kullanılabilecek oldukça az sayıda senaryo vardır. Dilediğiniz bir senaryoda, herhangi bir bağlı bileşen başka bir bağlı bileşene erişebilir. Birden çok hub mevcut olduğunda, standart sanal WAN 'da hub-hub arası yönlendirme (hub 'lar olarak da bilinir) varsayılan olarak etkindir. Bu yapılandırmayı Azure portal veya [Azure hızlı başlangıç şablonu](quickstart-any-to-any-template.md)gibi çeşitli farklı yöntemler kullanarak oluşturabilirsiniz. Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md). 

## <a name="design"></a><a name="design"></a>Tasarım

Bir sanal WAN senaryosunda kaç yol tablosunun gerekli olacağını anlamak için, her hücrenin bir kaynağın (satır) bir hedefle (sütun) iletişim kurup kuramayacağını temsil ettiği bir bağlantı matrisi oluşturabilirsiniz.

| Kaynak |   Amaç |  *Sanal ağlar* | *Dallar* |
| -------------- | -------- | ---------- | ---|
| Sanal ağlar     | &#8594;| Direct | Direct |
| Dallar   | &#8594;| Direct  | Direct |

Önceki tablodaki hücrelerden her biri, bir sanal WAN bağlantısının (akışın "Kimden" tarafı, satır başlıkları) bir hedef önekiyle (akışın "to" tarafı, italik olan sütun başlıkları) iletişim kuracağını açıklar. Bu senaryoda, güvenlik duvarı veya ağ sanal gereçleri yoktur, bu nedenle iletişim doğrudan sanal WAN üzerinden akar (Bu nedenle tablodaki "doğrudan" sözcüğü).

Sanal ağların ve dalların (VPN, ExpressRoute ve kullanıcı VPN) tüm bağlantıları aynı bağlantı gereksinimlerine sahip olduğundan, tek bir yol tablosu gereklidir. Sonuç olarak, tüm bağlantılar ilişkilendirilir ve varsayılan yol tablosuna aynı rota tablosuna yayılır:

* Sanal ağlar:
  * İlişkili yol tablosu: **varsayılan**
  * Yol tablolarına yayma: **varsayılan**
* Dallar
  * İlişkili yol tablosu: **varsayılan**
  * Yol tablolarına yayma: **varsayılan**

Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Mimari

**Şekil 1**' de tüm sanal ağlar ve dallar (VPN, ExpressRoute, P2S) birbirine ulaşabilir. Bir sanal hub 'da bağlantılar aşağıdaki gibi çalışır:

* VPN bağlantısı VPN sitesini VPN ağ geçidine bağlar.
* Sanal ağ bağlantısı, sanal bir ağı bir sanal hub 'a bağlar. Sanal hub 'ın yönlendiricisi, sanal ağlar arasında aktarım işlevi sağlar.
* ExpressRoute bağlantısı, bir ExpressRoute bağlantı hattını ExpressRoute bağlantı hattına bağlar.

Bu bağlantılar (varsayılan olarak, oluşturma sırasında), bağlantının yönlendirme yapılandırmasını **hiçbiri** veya özel bir yol tablosu olarak ayarlamadığınız takdirde varsayılan yol tablosuyla ilişkilendirilir. Bu bağlantılar, varsayılan olarak varsayılan yol tablosuna aynı zamanda yolları da yayar. Bu, herhangi bir bağlı bileşen (VNet, VPN, ER, P2S) birbirlerine ulaşabulabildiği herhangi bir senaryoya olanak sağlar.

**Şekil 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Şekil 1":::

## <a name="workflow"></a><a name="workflow"></a>İş akışı

Bu senaryo, standart sanal WAN için varsayılan olarak etkinleştirilmiştir. Daldan dala yönelik ayarlar WAN yapılandırmasında devre dışıysa, bu da dal arasında bağlantı yapılmasına izin vermez. VPN/ExpressRoute/Kullanıcı VPN, sanal WAN 'da dal olarak kabul edilir

## <a name="next-steps"></a>Sonraki adımlar

* Sanal WAN hakkında daha fazla bilgi için bkz. [SSS](virtual-wan-faq.md).
* Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).
