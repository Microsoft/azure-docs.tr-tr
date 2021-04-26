---
title: Yaygın sorunları giderme Azure Load Balancer
description: Azure Load Balancer ile ilgili sık karşılaşılan sorunları nasıl giderebileceğinizi öğrenin.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028820"
---
# <a name="troubleshoot-azure-load-balancer"></a>Azure Load Balancer sorunlarını giderme

Bu sayfa temel ve standart ortak Azure Load Balancer soruları için sorun giderme bilgileri sağlar. Standart Load Balancer hakkında daha fazla bilgi için bkz. [Standart Load Balancer genel bakış](load-balancer-standard-diagnostics.md).

Load Balancer bağlantısı kullanılamadığında en yaygın belirtiler aşağıdaki gibidir:

- Load Balancer arkasındaki VM 'Ler sistem durumu araştırmalara yanıt vermiyor 
- Load Balancer arkasındaki VM 'Ler yapılandırılmış bağlantı noktasındaki trafiğe yanıt vermiyor

Arka uç VM 'lerine dış istemciler yük dengeleyiciye geldiğinde, istemcilerin IP adresi iletişim için kullanılacaktır. İstemcilerin IP adresinin NSG izin verilenler listesine eklendiğinden emin olun.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Standart iç yük dengeleyiciler (ıLB) ile giden bağlantı yok

**Doğrulama ve çözümleme**

Standart ılbs 'ler **Varsayılan olarak güvenlidir**. *Gizli* genel IP adresi aracılığıyla internet 'e bağlanmasına izin verilen temel ılbs. Bu, IP adresi statik olmadığı veya sahip olduğunuz NSG 'ler aracılığıyla kilitlendiği için üretim iş yükleri için önerilmez. Kısa bir süre önce temel ıLB 'den standart bir ıLB 'ye taşındıysanız IP 'yi NSG 'ler aracılığıyla kilitleyen [yalnızca giden](egress-only.md) yapılandırma yoluyla açık bir IP oluşturmalısınız. Ayrıca, alt ağınızda bir [NAT ağ geçidi](../virtual-network/nat-overview.md) de kullanabilirsiniz.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Arka uç havuzunda sanal makine ölçek kümesi dağıtılan bir yük dengeleyicinin mevcut LB kuralı için arka uç bağlantı noktası değiştirilemiyor.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Neden: arka uç bağlantı noktası, sanal makine ölçek kümesi tarafından başvurulan yük dengeleyici için bir sistem durumu araştırması tarafından kullanılan bir yük dengeleme kuralı için değiştirilemez

**Çözüm** Bağlantı noktasını değiştirmek için, sanal makine ölçek kümesini güncelleştirerek sistem durumu araştırmasını kaldırabilir, bağlantı noktasını güncelleştirebilir ve ardından sistem durumu araştırmasını yeniden yapılandırabilirsiniz.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Yük dengeleyicinin arka uç havuzundan VM 'Ler kaldırıldıktan sonra küçük trafik hala yük dengeleyiciye devam ediyor.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Neden: arka uç havuzundan kaldırılan VM 'Ler artık trafik almamalıdır. Küçük miktarda ağ trafiği, Azure 'daki depolama, DNS ve diğer işlevlerle ilgili olabilir.

Doğrulamak için bir ağ izlemesi gerçekleştirebilirsiniz. BLOB depolama hesaplarınız için kullanılan FQDN, her depolama hesabının özellikleri içinde listelenir.  Azure aboneliğinizdeki bir sanal makineden, bu depolama hesabına atanan Azure IP 'sini tespit etmek için nslookup gerçekleştirebilirsiniz.

## <a name="additional-network-captures"></a>Ek ağ yakalamaları

Bir destek durumu açmaya karar verirseniz daha hızlı bir çözüm için aşağıdaki bilgileri toplayın. Aşağıdaki testleri gerçekleştirmek için tek bir arka uç VM seçin:

- Araştırma bağlantı noktası yanıtını sınamak için VNet 'teki arka uç VM 'lerinden birinden PS ping komutunu kullanın (örneğin: PS Ping 10.0.0.4:3389) ve sonuçları kaydedin. 
- Bu ping testlerinde bir yanıt alınmıyorsa, PsPing komutunu çalıştırırken, arka uç VM 'sinde ve VNet test VM 'de eşzamanlı bir Netsh izlemesi çalıştırın ve ardından Netsh izlemesini durdurun.

## <a name="load-balancer-in-failed-state"></a>Başarısız durumda Load Balancer

**Çözünürlük**

- Hatalı durumda olan kaynağı tanımladıktan sonra, [Azure Kaynak Gezgini](https://resources.azure.com/) ' a gidin ve kaynağı bu durumda TANIIN.
- Sağ üst köşedeki geçişi oku/yaz ' a güncelleştirin.
- Kaynak başarısız durumunda Düzenle ' ye tıklayın.
- Sağlama durumunun başarılı olarak güncelleştirildiğinden emin olmak için Al ' a ve ardından GET ' e tıklayın.
- Daha sonra kaynak başarısız durumunda diğer eylemlerle devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki adımlar sorunu çözmezse, bir [destek bileti](https://azure.microsoft.com/support/options/)açın.
