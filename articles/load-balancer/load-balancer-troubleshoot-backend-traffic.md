---
title: Azure Load Balancer sorunlarını giderme
description: Azure Load Balancer ile ilgili bilinen sorunları nasıl giderebileceğinizi öğrenin.
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
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98029225"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Arka uç trafiği yanıtlarının Azure Load Balancer sorunlarını giderme

Bu sayfa Azure Load Balancer sorularıyla ilgili sorun giderme bilgileri sağlar.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Load Balancer arkasındaki VM 'Ler yapılandırılan veri bağlantı noktasındaki trafiğe yanıt vermiyor

Bir arka uç havuzu VM 'si sağlıklı olarak listeleniyorsa ve sistem durumu araştırmasına yanıt verirse, ancak hala yük dengelemeye katılmadığında veya veri trafiğine yanıt vermiyorsa, bunun nedeni aşağıdakilerden biri olabilir:
* Load Balancer arka uç havuzu VM 'si, veri bağlantı noktasında dinlemiyor
* Ağ güvenlik grubu Load Balancer arka uç havuzu VM 'sinin bağlantı noktasını engelliyor  
* Aynı VM ve NIC 'den Load Balancer erişme
* Katılan Load Balancer arka uç havuzu VM 'sinden Internet Load Balancer ön ucuna erişme

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Neden 1: Load Balancer arka uç havuzu VM 'si veri bağlantı noktasında dinlemiyor

Bir VM veri trafiğine yanıt vermezse, bunun nedeni hedef bağlantı noktasının katılan VM 'de açık olmaması veya VM 'nin Bu bağlantı noktasını dinlemiyor olması olabilir. 

**Doğrulama ve çözümleme**

1. Arka uç VM 'de oturum açın. 
2. Bir komut istemi açın ve veri bağlantı noktasında dinleme yapan bir uygulama olduğunu doğrulamak için aşağıdaki komutu çalıştırın:  
            netstat-a 
3. Bağlantı noktası "dınleme" durumu ile listelenmiyorsa, doğru dinleyici bağlantı noktasını yapılandırın 
4. Bağlantı noktası dinleme olarak işaretlenmişse, olası sorunlar için bu bağlantı noktasındaki hedef uygulamayı kontrol edin.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Neden 2: ağ güvenlik grubu Load Balancer arka uç havuzu sanal makinesi üzerindeki bağlantı noktasını engelliyor  

Alt ağda veya VM 'de yapılandırılmış bir veya daha fazla ağ güvenlik grubu kaynak IP veya bağlantı noktasını engelliyorsa, VM yanıt veremez.

Ortak yük dengeleyici için, Internet istemcilerinin IP adresi, istemcilerle yük dengeleyici arka uç VM 'Leri arasındaki iletişim için kullanılacaktır. İstemcilerin IP adresine arka uç VM 'sinin ağ güvenlik grubunda izin verildiğinden emin olun.

1. Arka uç VM üzerinde yapılandırılan ağ güvenlik gruplarını listeleyin. Daha fazla bilgi için bkz. [ağ güvenlik gruplarını yönetme](../virtual-network/manage-network-security-group.md)
1. Ağ güvenlik grupları listesinden şunları denetleyin:
    - veri bağlantı noktasındaki gelen veya giden trafiğin girişimi vardır. 
    - sanal makinenin NIC 'inde veya Load Balancer araştırmasını ve trafiğe izin veren varsayılan kuralın (ağ güvenlik grupları, araştırma bağlantı noktası olan 168.63.129.16 IP 'si Load Balancer izin vermelidir) daha yüksek önceliğe sahip bir ağ güvenlik grubu kuralını **Engelle**
1. Kurallardan herhangi biri trafiği engelliyorsa, veri trafiğine izin vermek için bu kuralları kaldırın ve yeniden yapılandırın.  
1. VM şimdi durum araştırmalara yanıt vermeye başlamışsa test edin.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Neden 3: aynı VM ve ağ arabiriminden Load Balancer erişme 

Bir Load Balancer arka uç VM 'sinde barındırılan uygulamanız aynı ağ arabirimi üzerinden aynı arka uç VM 'sinde barındırılan başka bir uygulamaya erişmeye çalışıyorsa, desteklenmeyen bir senaryodur ve başarısız olur. 

**Çözüm** Aşağıdaki yöntemlerden birini kullanarak bu sorunu çözebilirsiniz:
* Uygulama başına ayrı arka uç havuzu VM 'lerini yapılandırın. 
* Her uygulamanın kendi ağ arabirimini ve IP adresini kullanmasını sağlamak için, iki NIC sanal makinelerinde uygulamayı yapılandırın. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Neden 4: katılan Load Balancer arka uç havuzu VM 'sinden iç Load Balancer ön uca erişme

Bir sanal ağ içinde bir iç Load Balancer yapılandırıldıysa ve katılımcı arka uç VM 'lerinden biri iç Load Balancer ön uca erişmeye çalışıyorsa, akış kaynak VM 'ye eşlendiğinde sorunlar oluşabilir. Bu senaryo desteklenmez.

**Çözüm** Bu senaryonun engelini kaldırmak için proxy kullanma gibi çeşitli yollar vardır. Application Gateway veya diğer üçüncü taraf proxy 'leri değerlendirin (örneğin, NGINX veya HAProxy). Application Gateway hakkında daha fazla bilgi için bkz. [Application Gateway genel bakış](../application-gateway/overview.md)

**Ayrıntılar** İç yük dengeleyiciler, giden kaynaklı bağlantıları iç Load Balancer ön ucuna çevirmez çünkü her ikisi de özel IP adresi alanında bulunur. Ortak yük dengeleyiciler, sanal ağ içindeki özel IP adreslerinden genel IP adreslerine [giden bağlantılar](load-balancer-outbound-connections.md) sağlar. Bu yaklaşım, iç yük dengeleyiciler için, çeviri gerekli olmadığı, benzersiz bir iç IP adresi alanı içinde olası SNAT bağlantı noktası tükenmesi önler.

Bir yan etkisi, arka uç havuzundaki bir VM 'den giden bir akış, havuzundaki iç Load Balancer ön uca bir akışı denerse _ve_ kendisine geri eşleniyorsa, akışın iki sanal makinesi eşleşmez. Eşleşmediği için akış başarısız olur. Akış, akışı ön uca oluşturan arka uç havuzundaki aynı VM 'ye geri eşlenmediyse akış başarılı olur.

Akış kendisiyle eşleniyorsa, giden akış VM 'den ön uca, buna karşılık gelen gelen akış ise VM 'den kendisine doğru şekilde görünür. Konuk işletim sisteminin görünüm noktasından, aynı akışın gelen ve giden parçaları sanal makine içinde eşleşmez. TCP yığını aynı akışın parçası olarak aynı akışın bu halcüleri tanımıyor. Kaynak ve hedef eşleşmiyor. Akış, arka uç havuzundaki diğer herhangi bir sanal makineye eşleniyorsa, akışın kilitlenme miktarı eşleşir ve VM akışa yanıt verebilir.

Bu senaryonun belirtisi, akış kaynaklı aynı arka uca döndüğünde zaman aralıklı bağlantı zaman aşımları olur. Yaygın geçici çözümler, iç Load Balancer arkasında bir proxy katmanının eklenmesini ve doğrudan sunucu dönüşü (DSR) stil kurallarını kullanmayı içerir. Daha fazla bilgi için bkz. [Azure Load Balancer Için birden fazla ön uçlar](load-balancer-multivip-overview.md).

Bir iç Load Balancer herhangi bir üçüncü taraf proxy ile birleştirebilir veya HTTP/HTTPS ile proxy senaryolarında iç [Application Gateway](../application-gateway/overview.md) kullanabilirsiniz. Bu sorunu gidermek için genel Load Balancer kullanabilmeniz sırasında, sonuçta elde edilen senaryo [SNAT tükenmesine](load-balancer-outbound-connections.md)açıktır. Dikkatle yönetilene kadar bu ikinci yaklaşımdan kaçının.

## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki adımlar sorunu çözmezse, bir [destek bileti](https://azure.microsoft.com/support/options/)açın.