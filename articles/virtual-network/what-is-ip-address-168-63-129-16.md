---
title: IP adresi 168.63.129.16 nedir? | Microsoft Belgeleri
description: Özellikle Azure platform kaynaklarına yönelik bir iletişim kanalını kolaylaştırmak için kullanılan IP adresi 168.63.129.16 hakkında bilgi edinin.
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
ms.openlocfilehash: 1e304bc30a48c92fdff576723dae6af1e26ef3da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222590"
---
# <a name="what-is-ip-address-1686312916"></a>IP adresi 168.63.129.16 nedir?

168.63.129.16 IP adresi, Azure platform kaynaklarına yönelik bir iletişim kanalını kolaylaştırmak için kullanılan bir sanal genel IP adresidir. Müşteriler, Azure 'da özel sanal ağı için herhangi bir adres alanı tanımlayabilir. Bu nedenle, Azure platform kaynakları benzersiz bir genel IP adresi olarak sunulmalıdır. Bu sanal genel IP adresi aşağıdaki şeyleri kolaylaştırır:

- VM aracısının, "Ready" durumunda olduğunu bildirmek üzere Azure platformuyla iletişim kurmasını sağlar.
- , Özel bir DNS sunucusuna sahip olmayan kaynaklara (VM gibi) filtrelenmiş ad çözümlemesi sağlamak için DNS sanal sunucusuyla iletişim sağlar. Bu filtreleme, müşterilerin yalnızca kaynaklarının ana bilgisayar adlarını çözümleyebilmeleri için de emin olur.
- VM 'lerin sistem durumunu belirlemede [Azure Yük dengeleyicisinden sistem durumu araştırmalarını](../load-balancer/load-balancer-custom-probe-overview.md) sağlar.
- VM 'nin Azure 'daki DHCP hizmetinden dinamik bir IP adresi almasını sağlar.
- PaaS rolü için konuk Aracısı sinyal iletilerini etkinleştirilir.

> [!NOTE]
> Sanal olmayan bir ağ senaryosunda (klasik), 168.63.129.16 yerine özel bir IP adresi kullanılır. Bu özel IP adresi dinamik olarak DHCP üzerinden bulunur. 168.63.129.16 'e özgü güvenlik duvarı kurallarının uygun şekilde ayarlanması gerekir.

## <a name="scope-of-ip-address-1686312916"></a>IP adresi 168.63.129.16 kapsamı

168.63.129.16 genel IP adresi tüm bölgelerde ve tüm ulusal bulutlarda kullanılır. Bu özel genel IP adresi Microsoft 'a aittir ve değişmeyecektir. Bu IP adresine herhangi bir yerel (VM) güvenlik duvarı ilkelerinde (giden yönü) izin vermeniz önerilir. Yalnızca iç Azure platformu bu IP adresinden bir ileti kaynağı olabileceğinden, bu özel IP adresi ve kaynakları arasındaki iletişim güvenlidir. Bu adres engellenirse, çeşitli senaryolarda beklenmeyen davranış oluşabilir. 168.63.129.16, [ana bilgisayar düğümünün sanal BIR IP](./network-security-groups-overview.md#azure-platform-considerations) 'si ve bu nedenle Kullanıcı tanımlı yollara tabi değildir.

- VM Aracısı, bağlantı noktaları 80/TCP ve 32526/TCP ile kablolu sunucu (168.63.129.16) üzerinden giden iletişim gerektirir. Bunlar VM 'deki yerel güvenlik duvarında açık olmalıdır. 168.63.129.16 ile bu bağlantı noktalarıyla iletişim, yapılandırılan ağ güvenlik gruplarına tabi değildir.

- 168.63.129.16, VM 'ye DNS hizmetleri sağlayabilir. Bu istenmiyorsa, VM 'deki yerel güvenlik duvarında 53/UDP ve 53/TCP 168.63.129.16 bağlantı noktalarına giden trafik engellenebilir.

  Varsayılan olarak, [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) hizmeti etiketini kullanarak özel olarak HEDEFLENMEDIKÇE DNS iletişimi yapılandırılmış ağ güvenlik gruplarına tabi değildir. NSG aracılığıyla Azure DNS DNS trafiğini engellemek için, [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)trafiğini reddetmek üzere bir giden kuralı oluşturun ve "*" öğesini "hedef bağlantı noktası aralıkları" ve "Any" olarak belirtin.

- VM, yük dengeleyici arka uç havuzunun bir parçası olduğunda, [durum araştırma](../load-balancer/load-balancer-custom-probe-overview.md) iletişiminin 168.63.129.16 ' den kaynaklanmasına izin verilmelidir. Varsayılan ağ güvenlik grubu yapılandırmasında bu iletişime izin veren bir kural bulunur. Bu kural [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) hizmeti etiketiyle yararlanır. İstenirse bu trafik ağ güvenlik grubu yapılandırılarak engelleniyorsa, bu, başarısız olan yoklamalara neden olur.

## <a name="next-steps"></a>Sonraki adımlar

- [Güvenlik grupları](./network-security-groups-overview.md)
- [Ağ güvenlik grubu oluşturma, değiştirme veya silme](manage-network-security-group.md)