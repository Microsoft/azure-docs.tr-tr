---
title: Azure Load Balancer sistem durumu araştırma durumu sorunlarını giderme
description: Azure Load Balancer sistem durumu araştırma durumuyla ilgili bilinen sorunları nasıl giderebileceğinizi öğrenin.
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
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98029231"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Azure Load Balancer sistem durumu araştırma durumu sorunlarını giderme

Bu sayfa, sorun giderme bilgilerinin ortak Azure Load Balancer durum araştırma sorularını sağlar.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Belirti: Load Balancer arkasındaki VM 'Ler sistem durumu araştırmalara yanıt vermiyor
Arka uç sunucularının yük dengeleyici kümesine katılması için, araştırma denetimini geçmesi gerekir. Sistem durumu araştırmaları hakkında daha fazla bilgi için bkz. [Load Balancer araştırmalarını anlama](load-balancer-custom-probe-overview.md). 

Load Balancer arka uç havuzu VM 'Leri aşağıdaki nedenlerden biri nedeniyle yoklamalara yanıt vermiyor olabilir: 
- Load Balancer arka uç havuzu VM 'si uygun değil 
- Load Balancer arka uç havuzu VM 'si, araştırma bağlantı noktasında dinlemiyor 
- Güvenlik duvarı veya bir ağ güvenlik grubu Load Balancer arka uç havuzu VM 'lerinde bağlantı noktasını engelliyor 
- Load Balancer diğer yapılandırma hataları

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Neden 1: Load Balancer arka uç havuzu VM 'si sağlıksız

**Doğrulama ve çözümleme**

Bu sorunu çözmek için, katılan VM 'lerde oturum açın ve VM durumunun sağlıklı olup olmadığını denetleyin ve havuzdaki başka bir VM 'den **Psping** 'e veya **TCPA** 'ya yanıt verebilir. VM sağlıksız durumdaysa veya araştırmayı yanıtlamadığında, yük dengelemeye katılabilmek için sorunu yeniden yapmanız ve VM 'yi sağlıklı bir duruma almanız gerekir.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Neden 2: Load Balancer arka uç havuzu VM 'si, araştırma bağlantı noktasında dinlemiyor
VM sağlıklı, ancak araştırmasına yanıt vermiyorsa, olası bir neden araştırma bağlantı noktasının katılan VM 'de açık olmaması veya VM 'nin Bu bağlantı noktasını dinlemiyor olması olabilir.

**Doğrulama ve çözümleme**

1. Arka uç VM 'de oturum açın.
2. Araştırma bağlantı noktasında dinleme yapan bir uygulama olduğunu doğrulamak için bir komut istemi açın ve aşağıdaki komutu çalıştırın: netstat-a
3. Bağlantı noktası durumu **dinleyen** olarak listelenmiyorsa, doğru bağlantı noktasını yapılandırın. 
4. Alternatif olarak, **dinleme** olarak listelenen başka bir bağlantı noktasını seçin ve yük dengeleyici yapılandırmasını buna göre güncelleştirin.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Neden 3: güvenlik duvarı veya ağ güvenlik grubu yük dengeleyici arka uç havuzu VM 'lerinde bağlantı noktasını engelliyor
VM 'deki güvenlik duvarı araştırma bağlantı noktasını engelliyorsa veya alt ağda veya VM 'de yapılandırılmış bir veya daha fazla ağ güvenlik grubu varsa, araştırmanın bağlantı noktasına erişmesine izin vermez, VM sistem durumu araştırmasına yanıt veremez.

**Doğrulama ve çözümleme**

1. Güvenlik Duvarı etkinse, araştırma bağlantı noktasına izin vermek üzere yapılandırılıp yapılandırılmadığını denetleyin. Aksi takdirde, güvenlik duvarını araştırma bağlantı noktasında trafiğe izin verecek şekilde yapılandırın ve yeniden sınayın.
2. Ağ güvenlik grupları listesinden, araştırma bağlantı noktasındaki gelen veya giden trafiğin girişim olup olmadığını denetleyin.
3. Ayrıca, sanal makinenin NIC 'inde bulunan tüm ağ güvenlik gruplarını reddetme kuralını ve LB yoklamalarını & trafiğe izin veren varsayılan kuraldan daha yüksek önceliğe sahip alt ağı **Engelle** (ağ güvenlik grupları 168.63.129.16 ıp 'sinin Load Balancer IP 'sine izin vermelidir) seçeneğini işaretleyin.
4. Bu kurallardan herhangi biri araştırma trafiğini engelliyorsa, araştırma trafiğine izin vermek için kuralları kaldırın ve yeniden yapılandırın.  
5. VM şimdi sistem durumu araştırmalarının yanıt vermeyi başlatmışsa test edin.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Neden 4: Load Balancer diğer yapılandırma hataları
Yukarıdaki tüm nedenler doğru şekilde doğrulanıp çözümlenirse ve arka uç VM hala sistem durumu araştırmasına yanıt vermezse, bağlantıyı el ile test edin ve bağlantıyı anlamak için bazı izlemeler toplayın.

**Doğrulama ve çözümleme**

1. Araştırma bağlantı noktası yanıtını test etmek için VNet içindeki diğer VM 'lerden birinden **Psping** kullanın (örnek: .\psping.exe-t 10.0.0.4:3389) ve sonuçları kaydedin. 
2. Araştırma bağlantı noktası yanıtını sınamak için VNet içindeki diğer VM 'lerden birinden **Tcping** kullanın (örnek: .\tcping.exe 10.0.0.4 3389) ve sonuçları kaydedin. 
3. Bu ping testlerinde yanıt alınmıyorsa,
    - Hedef arka uç havuzu VM 'sinde ve aynı VNet 'ten başka bir test sanal makinesinde eşzamanlı bir Netsh izlemesi çalıştırın. Şimdi bir süre için bir yük testi çalıştırın, bazı ağ izlemelerini toplayın ve ardından testi durdurun. 
    - Ağ yakalamayı çözümleyin ve ping sorgusuyla ilgili hem gelen hem de giden paketlerin olup olmadığını görün. 
        - Arka uç havuzu VM 'si üzerinde hiçbir gelen paket gözlemleniyorsa, ağ güvenlik grupları veya UDR yanlış yapılandırma, trafiği engelliyor olabilir. 
        - Arka uç havuzu VM 'sinde hiçbir giden paket gözlemleniyorsa, sanal makinenin ilgisiz sorunlar için denetlenmesi gerekir (örneğin, uygulama araştırma bağlantı noktasını engelliyor). 
    - Yük dengeleyiciye ulaşmadan önce araştırma paketlerinin başka bir hedefe (muhtemelen UDR aracılığıyla) zorlandığından emin olun. Bu, trafiğin arka uç VM 'ye hiçbir şekilde ulaşmasına neden olabilir. 
4. Araştırma türünü (örneğin, HTTP-TCP) değiştirin ve ağ güvenlik grupları ACL 'lerinde ve güvenlik duvarında karşılık gelen bağlantı noktasını, sorunun araştırma yanıtı yapılandırmasıyla olup olmadığını doğrulayacak şekilde yapılandırın. Durum araştırma yapılandırması hakkında daha fazla bilgi için bkz. [Endpoint Yük Dengeleme sistem durumu araştırma yapılandırması](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki adımlar sorunu çözmezse, bir [destek bileti](https://azure.microsoft.com/support/options/)açın.