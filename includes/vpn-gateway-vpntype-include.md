---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010886"
---
* **Policybased:** PolicyBased VPN 'Ler, daha önce klasik dağıtım modelinde statik yönlendirme ağ geçitleri olarak adlandırılmıştı. İlke tabanlı VPN 'Ler, şirket içi ağınız ve Azure VNet arasındaki adres ön ekleri birleşimleriyle yapılandırılmış IPSec ilkelerine bağlı olarak paketleri IPSec tünellerine göre şifreler ve yönlendirir. İlke (veya trafik seçici) çoğunlukla VPN cihazı yapılandırmasında bir erişim listesi olarak tanımlanır. PolicyBased VPN türü için değer *Policybased*' dir. PolicyBased VPN kullanırken aşağıdaki sınırlamaları aklınızda bulundurun:
  
  * PolicyBased VPN 'Ler **yalnızca** temel ağ geçidi SKU 'sunda kullanılabilir. Bu VPN türü, diğer ağ geçidi SKU 'Ları ile uyumlu değil.
  * PolicyBased VPN kullanırken yalnızca 1 tünele sahip olabilirsiniz.
  * Yalnızca S2S bağlantıları için PolicyBased VPN ve yalnızca belirli yapılandırmalarda kullanabilirsiniz. Çoğu VPN Gateway yapılandırması, RouteBased VPN gerektirir.
* **Routebased**: Routebased VPN 'ler, daha önce klasik dağıtım modelinde dinamik yönlendirme ağ geçitleri olarak adlandırılmıştı. RouteBased VPN 'Ler, paketleri karşılık gelen Tünel arabirimlerine yönlendirmek için IP iletme veya yönlendirme tablosunda "rotalar" kullanır. Bundan sonra tünel arabirimleri, paketleri tünellerin içinde veya dışında şifreler veya şifrelerini çözer. RouteBased VPN 'Ler için ilke (veya trafik seçici), herhangi bir (veya joker karakter) olarak yapılandırılır. RouteBased VPN türü için değer *Routebased*' dir.
