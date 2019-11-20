---
title: IP adresi 168.63.129.16 nedir? | Microsoft Docs
description: IP adresi 168.63.129.16 ve kaynaklarınızın nasıl çalıştığı hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 0ea8a8ec1a92a7dbc01dddc175f7116825ba00f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067775"
---
# <a name="what-is-ip-address-1686312916"></a>IP adresi 168.63.129.16 nedir?

168.63.129.16 IP adresi, Azure platform kaynaklarına yönelik bir iletişim kanalını kolaylaştırmak için kullanılan bir sanal genel IP adresidir. Müşteriler, Azure 'da özel sanal ağı için herhangi bir adres alanı tanımlayabilir. Bu nedenle, Azure platform kaynakları benzersiz bir genel IP adresi olarak sunulmalıdır. Bu sanal genel IP adresi aşağıdaki şeyleri kolaylaştırır:

- VM aracısının, "Ready" durumunda olduğunu bildirmek üzere Azure platformuyla iletişim kurmasını sağlar.
- , Özel bir DNS sunucusuna sahip olmayan kaynaklara (VM gibi) filtrelenmiş ad çözümlemesi sağlamak için DNS sanal sunucusuyla iletişim sağlar. Bu filtreleme, müşterilerin yalnızca kaynaklarının ana bilgisayar adlarını çözümleyebilmeleri için de emin olur.
- VM 'lerin sistem durumunu belirlemede [Azure Yük dengeleyicisinden sistem durumu araştırmalarını](../load-balancer/load-balancer-custom-probe-overview.md) sağlar.
- VM 'nin Azure 'daki DHCP hizmetinden dinamik bir IP adresi almasını sağlar.
- PaaS rolü için konuk Aracısı sinyal iletilerini etkinleştirilir.

## <a name="scope-of-ip-address-1686312916"></a>IP adresi 168.63.129.16 kapsamı

168.63.129.16 genel IP adresi tüm bölgelerde ve tüm ulusal bulutlarda kullanılır. Bu özel genel IP adresi Microsoft 'a aittir ve değişmeyecektir. Varsayılan ağ güvenlik grubu kuralı tarafından izin verilir. Tüm yerel güvenlik duvarı ilkelerinde bu IP adresine hem gelen hem de giden yönlere izin vermeniz önerilir. Yalnızca iç Azure platformu bu IP adresinden bir ileti kaynağı olabileceğinden, bu özel IP adresi ve kaynakları arasındaki iletişim güvenlidir. Bu adres engellenirse, çeşitli senaryolarda beklenmeyen davranış oluşabilir.

[Azure Load Balancer sistem durumu araştırmaları](../load-balancer/load-balancer-custom-probe-overview.md) bu IP adresinden kaynaklanır. Bu IP adresini engellerseniz, araştırmalar başarısız olur.

Sanal olmayan bir ağ senaryosunda (klasik), sistem durumu araştırması özel bir IP 'den kaynaklanmıştır ve 168.63.129.16 kullanılmaz.

## <a name="next-steps"></a>Sonraki adımlar

- [Güvenlik grupları](security-overview.md)
- [Ağ güvenlik grubu oluşturma, değiştirme veya silme](manage-network-security-group.md)
