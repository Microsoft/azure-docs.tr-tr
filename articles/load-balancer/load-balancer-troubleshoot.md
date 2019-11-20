---
title: Azure Load Balancer sorunlarını giderme
description: Azure Load Balancer ile ilgili bilinen sorunları nasıl giderebileceğinizi öğrenin.
services: load-balancer
documentationcenter: na
author: chadmath
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: d1c10fa8267131f13d3148ace6c97218a18fd494
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076917"
---
# <a name="troubleshoot-azure-load-balancer"></a>Azure Load Balancer sorunlarını giderme

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Bu sayfa, yaygın Azure Load Balancer sorularıyla ilgili sorun giderme bilgileri sağlar. Load Balancer bağlantısı kullanılamadığında en yaygın belirtiler aşağıdaki gibidir: 
- Load Balancer arkasındaki VM 'Ler sistem durumu araştırmalara yanıt vermiyor 
- Load Balancer arkasındaki VM 'Ler yapılandırılmış bağlantı noktasındaki trafiğe yanıt vermiyor

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
2. Bir komut istemi açın ve araştırma bağlantı noktasında dinleme yapan bir uygulama olduğunu doğrulamak için aşağıdaki komutu çalıştırın:   
            netstat-a
3. Bağlantı noktası durumu **dinleyen**olarak listelenmiyorsa, doğru bağlantı noktasını yapılandırın. 
4. Alternatif olarak, **dinleme**olarak listelenen başka bir bağlantı noktasını seçin ve yük dengeleyici yapılandırmasını buna göre güncelleştirin.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Neden 3: güvenlik duvarı veya ağ güvenlik grubu yük dengeleyici arka uç havuzu VM 'lerinde bağlantı noktasını engelliyor  
VM 'deki güvenlik duvarı araştırma bağlantı noktasını engelliyorsa veya alt ağda veya VM 'de yapılandırılmış bir veya daha fazla ağ güvenlik grubu varsa, araştırmanın bağlantı noktasına erişmesine izin vermez, VM sistem durumu araştırmasına yanıt veremez.          

**Doğrulama ve çözümleme**

* Güvenlik Duvarı etkinse, araştırma bağlantı noktasına izin vermek üzere yapılandırılıp yapılandırılmadığını denetleyin. Aksi takdirde, güvenlik duvarını araştırma bağlantı noktasında trafiğe izin verecek şekilde yapılandırın ve yeniden sınayın. 
* Ağ güvenlik grupları listesinden, araştırma bağlantı noktasındaki gelen veya giden trafiğin girişim olup olmadığını denetleyin. 
* Ayrıca, sanal makinenin NIC 'inde bulunan tüm ağ güvenlik gruplarını reddetme kuralını ve LB yoklamalarını & trafiğe izin veren varsayılan kuraldan daha yüksek önceliğe sahip alt ağı **Engelle** (ağ güvenlik grupları 168.63.129.16 ıp 'sinin Load Balancer IP 'sine izin vermelidir) seçeneğini işaretleyin. 
* Bu kurallardan herhangi biri araştırma trafiğini engelliyorsa, araştırma trafiğine izin vermek için kuralları kaldırın ve yeniden yapılandırın.  
* VM şimdi sistem durumu araştırmalarının yanıt vermeyi başlatmışsa test edin. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Neden 4: Load Balancer diğer yapılandırma hataları
Yukarıdaki tüm nedenler doğru şekilde doğrulanıp çözümlenirse ve arka uç VM hala sistem durumu araştırmasına yanıt vermezse, bağlantıyı el ile test edin ve bağlantıyı anlamak için bazı izlemeler toplayın.

**Doğrulama ve çözümleme**

* Araştırma bağlantı noktası yanıtını sınamak için sanal ağ içindeki diğer VM 'lerden birinden **Psping** kullanın (örnek: .\psping.exe-t 10.0.0.4:3389) ve sonuçları kaydedin. 
* Araştırma bağlantı noktası yanıtını sınamak için VNet içindeki diğer VM 'lerden birinden **tcpın** kullanın (örnek: .\tcping.exe 10.0.0.4 3389) ve sonuçları kaydedin. 
* Bu ping testlerinde yanıt alınmıyorsa,
    - Hedef arka uç havuzu VM 'sinde ve aynı VNet 'ten başka bir test sanal makinesinde eşzamanlı bir Netsh izlemesi çalıştırın. Şimdi bir süre için bir yük testi çalıştırın, bazı ağ izlemelerini toplayın ve ardından testi durdurun. 
    - Ağ yakalamayı çözümleyin ve ping sorgusuyla ilgili hem gelen hem de giden paketlerin olup olmadığını görün. 
        - Arka uç havuzu VM 'si üzerinde hiçbir gelen paket gözlemleniyorsa, ağ güvenlik grupları veya UDR yanlış yapılandırma, trafiği engelliyor olabilir. 
        - Arka uç havuzu VM 'sinde hiçbir giden paket gözlemleniyorsa, sanal makinenin ilgisiz sorunlar için denetlenmesi gerekir (örneğin, uygulama araştırma bağlantı noktasını engelliyor). 
    - Yük dengeleyiciye ulaşmadan önce araştırma paketlerinin başka bir hedefe (muhtemelen UDR aracılığıyla) zorlandığından emin olun. Bu, trafiğin arka uç VM 'ye hiçbir şekilde ulaşmasına neden olabilir. 
* Araştırma türünü (örneğin, HTTP-TCP) değiştirin ve ağ güvenlik grupları ACL 'lerinde ve güvenlik duvarında karşılık gelen bağlantı noktasını, sorunun araştırma yanıtı yapılandırmasıyla olup olmadığını doğrulayacak şekilde yapılandırın. Durum araştırma yapılandırması hakkında daha fazla bilgi için bkz. [Endpoint Yük Dengeleme sistem durumu araştırma yapılandırması](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Belirti: Load Balancer arkasındaki VM 'Ler, yapılandırılan veri bağlantı noktasındaki trafiğe yanıt vermiyor

Bir arka uç havuzu VM 'si sağlıklı olarak listeleniyorsa ve sistem durumu araştırmasına yanıt verirse, ancak hala yük dengelemeye katılmadığında veya veri trafiğine yanıt vermiyorsa, bunun nedeni aşağıdakilerden biri olabilir: 
* Load Balancer arka uç havuzu VM 'si, veri bağlantı noktasında dinlemiyor 
* Ağ güvenlik grubu Load Balancer arka uç havuzu VM 'sinin bağlantı noktasını engelliyor  
* Aynı VM ve NIC 'den Load Balancer erişme 
* Katılan Load Balancer arka uç havuzu VM 'sinden Internet Load Balancer ön ucuna erişme 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Neden 1: Load Balancer arka uç havuzu VM 'si veri bağlantı noktasında dinlemiyor 
Bir VM veri trafiğine yanıt vermezse, bunun nedeni hedef bağlantı noktasının katılan VM 'de açık olmaması veya VM 'nin Bu bağlantı noktasını dinlemiyor olması olabilir. 

**Doğrulama ve çözümleme**

1. Arka uç VM 'de oturum açın. 
2. Bir komut istemi açın ve veri bağlantı noktasında dinleme yapan bir uygulama olduğunu doğrulamak için şu komutu çalıştırın:  netstat-a 
3. Bağlantı noktası "dınleme" durumu ile listelenmiyorsa, doğru dinleyici bağlantı noktasını yapılandırın 
4. Bağlantı noktası dinleme olarak işaretlenmişse, olası sorunlar için bu bağlantı noktasındaki hedef uygulamayı kontrol edin. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Neden 2: ağ güvenlik grubu Load Balancer arka uç havuzu sanal makinesi üzerindeki bağlantı noktasını engelliyor  

Alt ağda veya VM 'de yapılandırılmış bir veya daha fazla ağ güvenlik grubu kaynak IP veya bağlantı noktasını engelliyorsa, VM yanıt veremez.

* Arka uç VM üzerinde yapılandırılan ağ güvenlik gruplarını listeleyin. Daha fazla bilgi için bkz. [ağ güvenlik gruplarını yönetme](../virtual-network/manage-network-security-group.md).
* Ağ güvenlik grupları listesinden şunları denetleyin:
    - veri bağlantı noktasındaki gelen veya giden trafiğin girişimi vardır. 
    - sanal makinenin NIC 'inde veya Load Balancer araştırmasını ve trafiğe izin veren varsayılan kuralın (ağ güvenlik grupları, araştırma bağlantı noktası olan 168.63.129.16 IP 'si Load Balancer izin vermelidir) daha yüksek önceliğe sahip bir ağ güvenlik grubu kuralını **Engelle** 
* Kurallardan herhangi biri trafiği engelliyorsa, veri trafiğine izin vermek için bu kuralları kaldırın ve yeniden yapılandırın.  
* VM şimdi durum araştırmalara yanıt vermeye başlamışsa test edin.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Neden 3: aynı VM ve ağ arabiriminden Load Balancer erişme 

Bir Load Balancer arka uç VM 'sinde barındırılan uygulamanız aynı ağ arabirimi üzerinden aynı arka uç VM 'sinde barındırılan başka bir uygulamaya erişmeye çalışıyorsa, desteklenmeyen bir senaryodur ve başarısız olur. 

**Çözüm** Aşağıdaki yöntemlerden birini kullanarak bu sorunu çözebilirsiniz:
* Uygulama başına ayrı arka uç havuzu VM 'lerini yapılandırın. 
* Her uygulamanın kendi ağ arabirimini ve IP adresini kullanmasını sağlamak için, iki NIC sanal makinelerinde uygulamayı yapılandırın. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Neden 4: katılan Load Balancer arka uç havuzu VM 'sinden iç Load Balancer ön uca erişme

Bir sanal ağ içinde bir iç Load Balancer yapılandırıldıysa ve katılımcı arka uç VM 'lerinden biri iç Load Balancer ön uca erişmeye çalışıyorsa, akış kaynak VM 'ye eşlendiğinde sorunlar oluşabilir. Bu senaryo desteklenmez. Ayrıntılı bir tartışma için [sınırlamaları](load-balancer-overview.md#limitations) gözden geçirin.

**Çözüm** Bu senaryonun engelini kaldırmak için proxy kullanma gibi çeşitli yollar vardır. Application Gateway veya diğer üçüncü taraf proxy 'leri değerlendirin (örneğin, NGINX veya HAProxy). Application Gateway hakkında daha fazla bilgi için bkz. [Application Gateway genel bakış](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Ek ağ yakalamaları
Bir destek durumu açmaya karar verirseniz daha hızlı bir çözüm için aşağıdaki bilgileri toplayın. Aşağıdaki testleri gerçekleştirmek için tek bir arka uç VM seçin:
- Araştırma bağlantı noktası yanıtını test etmek için VNet içindeki arka uç VM 'lerinden birini kullanın (örnek: Psping 10.0.0.4:3389) ve sonuçları kaydedin. 
- Bu ping testlerinde bir yanıt alınmıyorsa, PsPing komutunu çalıştırırken, arka uç VM 'sinde ve VNet test VM 'de eşzamanlı bir Netsh izlemesi çalıştırın ve ardından Netsh izlemesini durdurun. 
  
## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki adımlar sorunu çözmezse, bir [destek bileti](https://azure.microsoft.com/support/options/)açın.

