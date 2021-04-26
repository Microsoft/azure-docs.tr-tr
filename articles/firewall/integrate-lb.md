---
title: Azure Güvenlik Duvarı’nı Azure Standart Load Balancer ile tümleştirme
description: Bir Azure Güvenlik duvarını bir Azure Standart Load Balancer (genel veya dahili) ile bir sanal ağ ile tümleştirebilirsiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: e14a8afe27fc9dd9ca40730dd7e681c3093e0b50
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505913"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Azure Güvenlik Duvarı’nı Azure Standart Load Balancer ile tümleştirme

Bir Azure Güvenlik duvarını bir Azure Standart Load Balancer (genel veya dahili) ile bir sanal ağ ile tümleştirebilirsiniz. 

Tercih edilen ve çok daha basit olan tasarım, Azure güvenlik duvarınızla bir iç yük dengeleyiciyi tümleştirmektir. Önceden dağıttığınız bir genel yük dengeleyici varsa ve bunu kullanmaya devam etmek istiyorsanız bundan faydalanabilirsiniz. Ancak genel yük dengeleyici senaryosunda işlevi etkileyen asimetrik yönlendirme sorununun farkında olmanız gerekir.

Azure Load Balancer hakkında daha fazla bilgi için bkz. [Azure Load Balancer nedir?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Genel yük dengeleyici

Ortak yük dengeleyici ile yük dengeleyici, genel ön uç IP adresi ile dağıtılır.

### <a name="asymmetric-routing"></a>Asimetrik yönlendirme

Asimetrik yönlendirme, bir paketin hedefe bir yol aldığı ve kaynağa dönme sırasında başka bir yol aldığı yerdir. Bu sorun, bir alt ağın güvenlik duvarının özel IP adresine giderken bir varsayılan yolu olduğunda ve bir ortak yük dengeleyici kullanıyorsanız oluşur. Bu durumda, gelen yük dengeleyici trafiği genel IP adresi aracılığıyla alınır, ancak döndürülen yol güvenlik duvarının özel IP adresinden geçer. Güvenlik duvarı durum bilgisi olduğundan, güvenlik duvarı, bu tür bir oturumun farkında olmadığından döndürülen paketi bırakır.

### <a name="fix-the-routing-issue"></a>Yönlendirme sorununu çözme

Bir Azure Güvenlik duvarını bir alt ağa dağıttığınızda, tek bir adım, paketleri güvenlik duvarının AzureFirewallSubnet konumundaki özel IP adresi aracılığıyla yönlendiren alt ağ için varsayılan bir yol oluşturmaktır. Daha fazla bilgi için bkz. [öğretici: Azure Güvenlik duvarını Azure Portal kullanarak dağıtma ve yapılandırma](tutorial-firewall-deploy-portal.md#create-a-default-route).

Yük dengeleyici senaryonuza güvenlik duvarını tanıtdığınızda, Internet trafiğinizin güvenlik duvarınızın genel IP adresi aracılığıyla gelmesini istersiniz. Buradan güvenlik duvarı, güvenlik duvarı kurallarını ve NAT 'ları yük dengeleyicinin genel IP adresine uygular. Bu, sorunun oluştuğu yerdir. Paketler, güvenlik duvarının genel IP adresine ulaşır, ancak özel IP adresi (varsayılan yol kullanılarak) aracılığıyla güvenlik duvarına geri döner.
Bu sorundan kaçınmak için, güvenlik duvarının genel IP adresi için ek bir ana bilgisayar yolu oluşturun. Güvenlik duvarının genel IP adresine giden paketler Internet üzerinden yönlendirilir. Bu, güvenlik duvarının özel IP adresine varsayılan yolu almayı önler.

![Asimetrik yönlendirme](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Rota tablosu örneği

Örneğin, aşağıdaki rotalar genel IP adresi 20.185.97.136 ve 10.0.1.4 özel IP adresi için bir güvenlik duvarına yöneliktir.

> [!div class="mx-imgBorder"]
> ![Yol tablosu](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>NAT kuralı örneği

Aşağıdaki örnekte, bir NAT kuralı, 20.185.97.136 adresindeki yük dengeleyiciye 20.42.98.220 adresindeki güvenlik duvarındaki RDP trafiğini çevirir:

> [!div class="mx-imgBorder"]
> ![NAT kuralı](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Durum araştırmaları

Bağlantı noktası 80 veya HTTP/HTTPS araştırmaları için TCP sistem durumu araştırmaları kullanıyorsanız yük dengeleyici havuzundaki konaklarda çalışan bir Web hizmetiniz olması gerektiğini unutmayın.

## <a name="internal-load-balancer"></a>İç yük dengeleyici

İç yük dengeleyici ile, yük dengeleyici özel bir ön uç IP adresi ile dağıtılır.

Bu senaryoyla ilgili bir asimetrik yönlendirme sorunu yok. Gelen paketler, güvenlik duvarının genel IP adresine ulaşır, yük dengeleyicinin özel IP adresine çevrilir ve ardından aynı dönüş yolunu kullanarak güvenlik duvarının özel IP adresine geri döner.

Dolayısıyla, bu senaryoyu ortak yük dengeleyici senaryosuna benzer ancak güvenlik duvarı genel IP adresi ana bilgisayar yoluna gerek kalmadan dağıtabilirsiniz.

Arka uç havuzundaki sanal makinelerin, Azure Güvenlik Duvarı aracılığıyla giden Internet bağlantısı olabilir. Bir sonraki atlama olarak güvenlik duvarıyla sanal makine alt ağında Kullanıcı tanımlı bir yol yapılandırın.


## <a name="additional-security"></a>Ek güvenlik

Yük dengeli senaryonuzun güvenliği artırmak için ağ güvenlik grupları (NSG 'ler) kullanabilirsiniz.

Örneğin, yük dengeli sanal makinelerin bulunduğu arka uç alt ağında bir NSG oluşturabilirsiniz. Güvenlik Duvarı IP adresinden/bağlantı noktasından gelen trafiğe izin ver.

![Ağ güvenlik grubu](media/integrate-lb/nsg-01.png)

NSG 'ler hakkında daha fazla bilgi için bkz. [güvenlik grupları](../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik duvarını dağıtmayı ve yapılandırmayı](tutorial-firewall-deploy-portal.md)öğrenin.