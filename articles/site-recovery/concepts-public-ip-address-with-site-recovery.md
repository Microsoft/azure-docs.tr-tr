---
title: Azure Site Recovery ile yük devretmeden sonra genel IP adresleri atama
description: Olağanüstü durum kurtarma ve geçiş için Azure Site Recovery ve Azure Traffic Manager ile genel IP adreslerini ayarlamayı açıklar
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: b1f3ffa6fc90fc0cab0217d1b71907342f2dbd0d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084244"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Yük devretmeden sonra genel IP adreslerini ayarlama

Genel IP adresleri, İnternet kaynaklarının Azure kaynakları ile gelen iletişimi kurmasına izin verir. Genel IP adresleri ayrıca Azure kaynaklarının İnternet ve kaynağa atanmış bir IP adresi ile genel kullanıma yönelik Azure hizmetleri ile giden iletişimi kurmasını sağlar.
- Azure sanal makineler (VM), Azure uygulama ağ geçitleri, Azure yük dengeleyiciler, Azure VPN ağ geçitleri ve diğerleri gibi Internet 'ten kaynağa gelen iletişim. VM, bir yük dengeleyici arka uç havuzunun parçası olduğu ve Yük Dengeleyiciye genel bir IP adresi atandığında, bir VM 'nin kendisine atanmış bir genel IP adresi yoksa, Internet 'ten de sanal makineler gibi bazı kaynaklarla iletişim kurabilirsiniz.
- Öngörülebilir bir IP adresi kullanarak Internet 'e giden bağlantı. Örneğin, bir sanal makine, kendisine atanmış bir genel IP adresi olmadan giden Internet ile iletişim kurabilir, ancak adresi varsayılan olarak Azure tarafından öngörülemeyen bir genel adrese çevrilmiş ağ adresidir. Bir kaynağa genel IP adresi atamak, giden bağlantı için hangi IP adresinin kullanıldığını bilmenizi sağlar. Tahmin edilebilir olsa da, seçilen atama yöntemine bağlı olarak adres değişebilir. Daha fazla bilgi için bkz. [genel IP adresi oluşturma](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Azure kaynaklarından giden bağlantılar hakkında daha fazla bilgi edinmek için bkz. [giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Azure Resource Manager, genel IP adresi kendi özelliklerine sahip bir kaynaktır. Genel bir IP adresini ilişkilendirebileceğiniz kaynakların bazıları şunlardır:

* Sanal makine ağ arabirimleri
* İnternet'e yönelik yük dengeleyiciler
* VPN ağ geçitleri
* Uygulama ağ geçitleri

Bu makalede, Site Recovery ile genel IP adreslerini nasıl kullanabileceğiniz açıklanır.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Kurtarma planı kullanılarak genel IP adresi ataması

Üretim uygulamasının genel IP adresi, **Yük devretmede bekletilemez**. Yük devretme işleminin bir parçası olarak getirilen iş yükleri, hedef bölgede kullanılabilir bir Azure genel IP kaynağı atanmalıdır. Bu adım el ile yapılabilir veya kurtarma planlarıyla otomatik hale getirilebilir. Kurtarma planı, makineleri kurtarma gruplarına toplar. Sistematik bir kurtarma işlemi tanımlamanıza yardımcı olur. Azure 'da yük devretme için Azure Otomasyonu runbook 'larını kullanarak her adımda sipariş getirmek ve gereken eylemleri otomatikleştirmek için bir kurtarma planı kullanabilirsiniz.

Kurulum aşağıdaki gibidir:
- Bir [kurtarma planı](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) oluşturun ve iş yüklerinizi plana gereken şekilde gruplayın.
- Yük devredilen VM 'ye [Azure Otomasyonu runbook 'ları](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) kullanarak genel IP adresi ekleme adımı ekleyerek planı özelleştirin.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>DNS düzeyinde yönlendirme ile genel uç nokta değiştirme

Azure Traffic Manager, uç noktalar arasında DNS düzeyinde yönlendirmeyi mümkün ve bir DR senaryosunda [RTOs](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) 'ı yönlendirmeye yardımcı olabilir. 

Traffic Manager ile yük devretme senaryoları hakkında daha fazla bilgi edinin:
1. Traffic Manager ile [Şirket Içinde Azure yük devretmesi](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) 
2. [Azure 'dan Azure 'a yük devretme](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) Traffic Manager 

Kurulum aşağıdaki gibidir:
- [Traffic Manager profili](../traffic-manager/traffic-manager-create-profile.md)oluşturun.
- **Öncelik** yönlendirme yönteminden yararlanarak, Azure için kaynak ve **Yük devretme** için **birincil** olan iki uç nokta oluşturun. **Birincil** öncelik 1 olarak atanır ve **yük devretmeye** öncelik 2 atanır.
- **Birincil** uç nokta, kaynak ortamınızın Azure içinde veya dışında olmasına bağlı olarak [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) veya [Harici](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) olabilir.
- **Yük devretme** uç noktası bir **Azure** uç noktası olarak oluşturulur. Bir **statik genel IP adresi** kullanın çünkü bu, olağanüstü durum olayında Traffic Manager için dış uç nokta olacaktır.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Site Recovery Traffic Manager](../site-recovery/concepts-traffic-manager-with-site-recovery.md) hakkında daha fazla bilgi edinin
- Traffic Manager [yönlendirme yöntemleri](../traffic-manager/traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.
- Uygulama yük devretmesini otomatikleştirmek için [kurtarma planları](site-recovery-create-recovery-plans.md) hakkında daha fazla bilgi edinin.
