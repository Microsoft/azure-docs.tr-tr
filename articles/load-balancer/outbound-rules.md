---
title: Giden kuralları Azure Load Balancer
description: Bu makalede, Azure Load Balancer ile internet trafiğinin çıkışı denetlemek için giden kuralların nasıl yapılandırılacağı açıklanır.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 339bbd7edf48737113de360812165dc8148c5b93
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375874"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Giden kuralları Azure Load Balancer

Giden kuralları, genel bir standart yük dengeleyici için SNAT 'yi (kaynak ağ adresi çevirisi) açıkça tanımlamanızı sağlar. Bu yapılandırma, arka uç örnekleri için giden internet bağlantısı sağlamak üzere yük dengeleyicinizin genel IP 'leri kullanmanıza olanak sağlar.

Bu yapılandırma şunları sunar:

* IP kendini gizleyen
* İzin verilenler listelerinizi basitleştirin.
* Dağıtım için genel IP kaynakları sayısını azaltır.

Giden kurallarla, giden internet bağlantısı üzerinde tam bildirime dayalı denetiminiz vardır. Giden kuralları, bu özelliği özel gereksinimlerinize göre ölçeklendirmenize ve ayarlamanıza olanak sağlar. 

Giden kuralları yalnızca arka uç VM 'sinin örnek düzeyi genel IP adresi (ıLPıP) yoksa izlenir.

![Giden kuralları Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Giden kurallarla, giden **SNAT** davranışını açık bir şekilde tanımlayabilirsiniz.

Giden kuralları denetlemenize izin verir:

* **Hangi sanal makinelerin hangi ortak IP adreslerine çevrildiği.**
     * İki kural arka uç havuzu 1, mavi IP adresi 1 ve 2 kullanır, arka uç Havuzu 2 sarı IP önekini kullanır.
* **Giden SNAT bağlantı noktaları nasıl ayrılır.**
     * Giden bağlantılar yapan tek havuz 2 arka uç havuzu ise, tüm SNAT bağlantı noktalarına arka uç Havuzu 2 ' ye ve hiçbiri ' ni arka uç havuzu 1 ' e vermeyin
* **İçin giden çeviri sağlayacak olan protokoller.**
     * Arka uç Havuzu 2 giden için UDP bağlantı noktalarına ihtiyaç duyuyorsa ve arka uç havuzu 1 için TCP gerekiyorsa, TCP bağlantı noktalarını 1 ve UDP bağlantı noktalarına 2 ' ye verin.
* **Giden bağlantı boşta kalma zaman aşımı (4-120 dakika) için kullanılacak süre.**
     * Keepsıt ile uzun süre çalışan bağlantılar varsa, uzun süre çalışan bağlantılar için boşta kalma bağlantı noktalarını 120 dakikaya kadar ayırın. Yeni bağlantılar için eski bağlantıların terk olduğunu ve sürüm bağlantı noktalarını 4 dakika içinde kabul edin 
* **Boşta kalma zaman aşımı durumunda TCP sıfırlaması gönderileceğini belirtir.**
     * Boşta bağlantıları zaman aşımına uğradıklarında, akışın terk olduğunu bilmesi için istemciye ve sunucuya bir TCP RST gönderiyoruz.

## <a name="outbound-rule-definition"></a>Giden kuralı tanımı

Giden kuralları yük dengeleme ve gelen NAT kuralları ile aynı tanıdık sözdizimini izler: **ön uç**  +  **parametreleri**  +  **arka uç havuzu**. 

Giden bir kural _, arka uç havuzu tarafından belirtilen tüm sanal makineler_ IÇIN giden NAT 'yi _ön_ uca çevrilecek şekilde yapılandırır.  

_Parametreler_ , giden NAT algoritması üzerinde ek ayrıntılı denetim sağlar.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Birden çok IP adresiyle giden NAT 'yi ölçeklendirme

Bir ön uç tarafından sağlanan her ek IP adresi, yük dengeleyici için SNAT bağlantı noktası olarak kullanılacak ek 64.000 kısa ömürlü bağlantı noktaları sağlar. 

Büyük ölçekli senaryolar planlamak için birden çok IP adresi kullanın. [SNAT tükenmesi](troubleshoot-outbound-connection.md#snatexhaust)azaltmak için giden kurallarını kullanın. 

[Genel BIR IP önekini](./load-balancer-outbound-connections.md#outboundrules) doğrudan giden bir kuralla de kullanabilirsiniz. 

Genel bir IP öneki, dağıtımınızın ölçeklendirilmesini arttırır. Ön ek, Azure kaynaklarınızdan kaynaklanan akışların izin verilenler listesine eklenebilir. Yük dengeleyicide bir ön uç IP yapılandırması genel IP adresi ön ekine başvuracak şekilde yapılandırılabilir.  

Yük dengeleyicinin genel IP öneki üzerinde denetimi vardır. Giden kuralı, giden bağlantılar için genel IP ön eki içinde bulunan tüm genel IP adreslerini otomatik olarak kullanacaktır. 

Genel IP ön eki içindeki IP adreslerinden her biri, yük dengeleyicinin SNAT bağlantı noktası olarak kullanması için IP adresi başına ek 64.000 kısa ömürlü bağlantı noktaları sağlar.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Giden akış boşta kalma zaman aşımı ve TCP sıfırlaması

Giden kuralları, giden akış boşta kalma zaman aşımını denetlemek ve uygulamanızın gereksinimleriyle eşleştirmek için bir yapılandırma parametresi sağlar. Çıkış boşta kalma zaman aşımları varsayılan olarak 4 dakikadır. Daha fazla bilgi için bkz. [boştaki zaman aşımlarını yapılandırma](load-balancer-tcp-idle-timeout.md). 

Yük dengeleyicinin varsayılan davranışı, giden boşta kalma zaman aşımına ulaşıldığında akışı sessizce düşürülemiyor. `enableTCPReset`Parametresi, öngörülebilir bir uygulama davranışı ve denetimi sunar. Parametresi, giden boşta kalma zaman aşımı süresi dolduğunda çift yönlü TCP sıfırlaması (TCP RST) gönderilmesini belirler. 

Bölge kullanılabilirliği dahil Ayrıntılar için [boşta kalma zaman aşımı durumunda TCP sıfırlamayı](./load-balancer-tcp-reset.md) gözden geçirin.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Giden bağlantıyı açıkça güvenli hale getirme ve denetleme

Yük Dengeleme kuralları, giden NAT 'nin otomatik olarak programlamayı sağlar. Bazı senaryolar, giden NAT 'nin otomatik programlamayı Yük Dengeleme kuralıyla devre dışı bırakmanızı veya gerektirmesini gerektirir. Kural aracılığıyla devre dışı bırakmak, davranışı denetlemenize veya iyileştirmenize olanak tanır.  

Bu parametreyi iki şekilde kullanabilirsiniz:

1. Giden SNAT için gelen IP adresini önleme. Yük Dengeleme kuralında giden SNAT 'yi devre dışı bırakın.
  
2. Gelen ve giden için aynı anda kullanılan bir IP adresinin giden **SNAT** parametrelerini ayarlayın. Giden bir kuralın denetim geçirmesine izin vermek için otomatik giden NAT 'nin devre dışı bırakılması gerekir. Gelen için de kullanılan bir adresin SNAT bağlantı noktası ayırmasını değiştirmek için, `disableOutboundSnat` parametrenin true olarak ayarlanması gerekir. 

Gelen için kullanılan bir IP adresini yeniden tanımlamayı denerseniz, giden kuralı yapılandırma işlemi başarısız olur.  Önce yük dengeleme kuralının giden NAT 'ı devre dışı bırakın.

>[!IMPORTANT]
> Bu parametreyi true olarak ayarlarsanız ve giden bağlantıyı tanımlamak için bir giden kuralı yoksa, sanal makinenizin giden bağlantısı olmayacaktır.  VM 'nizin veya uygulamanızın bazı işlemleri giden bağlantıya sahip olmaya bağlı olabilir. Senaryonuzun bağımlılıklarını anladığınızdan ve bu değişikliği yapmanın etkileri olduğuna emin olun.

Bazen bir VM 'nin giden akış oluşturması istenmeyen bir şekilde yapılır. Hangi hedeflerin giden akışları alacağını veya hangi hedeflerin gelen akışlara başlayacağını yönetmek için bir gereksinim olabilir. VM 'nin ulaştığı hedefleri yönetmek için [ağ güvenlik gruplarını](../virtual-network/network-security-groups-overview.md) kullanın. Hangi ortak hedeflerin gelen akışları başlattığını yönetmek için NSG 'leri kullanın.

Yük dengeli bir VM 'ye NSG uyguladığınızda, [hizmet etiketlerine](../virtual-network/network-security-groups-overview.md#service-tags) ve [varsayılan güvenlik kurallarına](../virtual-network/network-security-groups-overview.md#default-security-rules)dikkat edin. 

VM 'nin Azure Load Balancer durum araştırma isteklerini almasına emin olun.

Bir NSG AZURE_LOADBALANCER varsayılan etiketten durum araştırma isteklerini engelliyorsa, sanal makine sistem durumu araştırmanız başarısız olur ve VM kullanılamaz olarak işaretlenir. Yük dengeleyici bu VM 'ye yeni akış göndermeyi durduruyor.

## <a name="scenarios-with-outbound-rules"></a>Giden kuralları olan senaryolar
        

### <a name="outbound-rules-scenarios"></a>Giden kuralları senaryoları


* Belirli bir genel IP veya ön ek kümesine giden bağlantılar yapılandırın.
* [SNAT](load-balancer-outbound-connections.md) bağlantı noktası ayırmasını değiştirin.
* Yalnızca gideni etkinleştirin.
* Yalnızca VM 'Ler için giden NAT (gelen yok).
* İç standart yük dengeleyici için giden NAT.
* Genel bir standart yük dengeleyici ile giden NAT için TCP & UDP protokollerini etkinleştirin.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Senaryo 1: belirli bir genel IP veya ön ek kümesine giden bağlantıları yapılandırma


#### <a name="details"></a>Ayrıntılar


Giden bağlantıların bir genel IP adresi kümesinden kaynaklanacak şekilde uyarlamak için bu senaryoyu kullanın. Genel IP 'Leri veya önekleri, alma temelli bir izin verme veya reddetme listesine ekleyin.


Bu genel IP veya ön ek, bir yük dengeleme kuralı tarafından kullanılan ile aynı olabilir. 


Yük Dengeleme kuralı tarafından kullanılan farklı bir genel IP veya ön ek kullanmak için: 


1. Genel IP öneki veya genel IP adresi oluşturun.
2. Ortak standart yük dengeleyici oluşturma 
3. Kullanmak istediğiniz genel IP ön ekine veya genel IP adresine başvuran bir ön uç oluşturun. 
4. Arka uç havuzunu yeniden kullanma veya arka uç havuzu oluşturma ve VM 'Leri ortak yük dengeleyicinin arka uç havuzuna yerleştirme
5. Ön uç kullanan VM 'Ler için giden NAT 'yi etkinleştirmek üzere ortak yük dengeleyicide giden bir kural yapılandırın. Giden için bir yük dengeleme kuralı kullanılması önerilmez, Yük Dengeleme kuralında giden SNAT 'yi devre dışı bırakın.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>Senaryo 2: [SNAT](load-balancer-outbound-connections.md) bağlantı noktası ayırmayı değiştirme


#### <a name="details"></a>Ayrıntılar


[Arka uç havuzu boyutuna göre OTOMATIK SNAT bağlantı noktası ayırmayı](load-balancer-outbound-connections.md#preallocatedports)ayarlamak için giden kurallarını kullanabilirsiniz. 


SNAT tükenmesi yaşarsanız, varsayılan 1024 olan [SNAT](load-balancer-outbound-connections.md) bağlantı noktalarının sayısını artırın. 


Her genel IP adresi 64.000 kısa ömürlü bağlantı noktasına katkıda bulunur. Arka uç havuzundaki sanal makinelerin sayısı, her sanal makineye dağıtılan bağlantı noktası sayısını belirler. Arka uç havuzundaki bir sanal makinenin en fazla 64.000 bağlantı noktasına erişimi vardır. İki VM için, bir giden kuralla en fazla 32.000 [SNAT](load-balancer-outbound-connections.md) bağlantı noktasına (2x 32.000 = 64.000) verilebilir. 


Varsayılan olarak verilen SNAT bağlantı noktalarını ayarlamak için giden kurallarını kullanabilirsiniz. Varsayılan [SNAT](load-balancer-outbound-connections.md) bağlantı noktası ayırmasının sağladığı daha fazlasını veya daha fazlasını verirsiniz. Giden kuralının ön noktasındaki her genel IP adresi, [SNAT](load-balancer-outbound-connections.md) bağlantı noktaları olarak kullanılmak üzere 64.000 kısa ömürlü bağlantı noktasına kadar katkıda bulunur. 


Yük dengeleyici, [SNAT](load-balancer-outbound-connections.md) bağlantı noktalarını 8 ' in katları halinde verir. 8 ' den bölünemez bir değer sağlarsanız, yapılandırma işlemi reddedilir. Her yük dengeleme kuralı ve gelen NAT kuralı, 8 bağlantı noktası aralığını kullanır. Bir yük dengeleme veya gelen NAT kuralı aynı 8 aralığını başka bir şekilde paylaşıyorsa, ek bağlantı noktası tüketilmeyecektir.


Genel IP adresi sayısına göre kullanılabilir olandan daha fazla [SNAT](load-balancer-outbound-connections.md) bağlantı noktası vermenizi denerseniz, yapılandırma işlemi reddedilir. Örneğin, VM başına 10.000 bağlantı noktası verirseniz ve arka uç havuzundaki yedi VM tek bir genel IP paylaşıyorsa, yapılandırma reddedilir. 10.000 ile çarpılan yedi, 64.000 bağlantı noktası sınırını aşıyor. Senaryoyu etkinleştirmek için giden kuralının ön ucunda daha fazla genel IP adresi ekleyin. 


Bağlantı noktası sayısı için 0 belirterek [varsayılan bağlantı noktası ayırmaya](load-balancer-outbound-connections.md#preallocatedports) dönün. İlk 50 VM örnekleri 1024 bağlantı noktalarını alacak, 51-100 sanal makine örnekleri en fazla örneğe kadar 512. Varsayılan SNAT bağlantı noktası ayırma hakkında daha fazla bilgi için bkz. [SNAT bağlantı noktaları ayırma tablosu](./load-balancer-outbound-connections.md#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Senaryo 3: yalnızca giden trafiği etkinleştir


#### <a name="details"></a>Ayrıntılar


Bir VM grubu için giden NAT sağlamak üzere genel bir standart yük dengeleyici kullanın. Bu senaryoda, herhangi bir ek kural yapılandırılmadığı bir giden kuralı kendisi kullanın.


> [!NOTE]
> **Azure sanal ağ NAT** , yük dengeleyiciye gerek duymadan sanal makineler için giden bağlantı sağlayabilir. Daha fazla bilgi için bkz. [Azure sanal ağ NAT nedir?](../virtual-network/nat-overview.md) .

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Senaryo 4: yalnızca VM 'Ler için giden NAT (gelen)


> [!NOTE]
> **Azure sanal ağ NAT** , yük dengeleyiciye gerek duymadan sanal makineler için giden bağlantı sağlayabilir. Daha fazla bilgi için bkz. [Azure sanal ağ NAT nedir?](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Ayrıntılar


Bu senaryo için: Azure Load Balancer giden kuralları ve sanal ağ NAT, bir sanal ağdan çıkış için kullanılabilir seçeneklerdir.


1. Genel IP veya ön ek oluşturun.
2. Genel bir standart yük dengeleyici oluşturun. 
3. Giden için ayrılmış genel IP veya ön ek ile ilişkili bir ön uç oluşturun.
4. VM 'Ler için bir arka uç havuzu oluşturun.
5. VM 'Leri arka uç havuzuna yerleştirin.
6. Giden NAT 'yi etkinleştirmek için bir giden kuralı yapılandırın.



[SNAT](load-balancer-outbound-connections.md) bağlantı noktalarını ölçeklendirmek için bir ön ek veya genel IP kullanın. Giden bağlantıların kaynağını bir izin verme veya reddetme listesine ekleyin.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Senaryo 5: iç standart yük dengeleyici için giden NAT


> [!NOTE]
> **Azure sanal ağ NAT** , iç standart yük dengeleyiciyi kullanan sanal makineler için giden bağlantı sağlayabilir. Daha fazla bilgi için bkz. [Azure sanal ağ NAT nedir?](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Ayrıntılar


İç standart yük dengeleyici, örnek düzeyi genel IP 'Ler veya sanal ağ NAT aracılığıyla açıkça bildirildiği veya arka uç havuz üyelerini yalnızca giden bir yük dengeleyici yapılandırmasıyla ilişkilendirerek, giden bağlantı kullanılamıyor. 


Daha fazla bilgi için bkz. [yalnızca giden yük dengeleyici yapılandırması](./egress-only.md).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Senaryo 6: genel bir standart yük dengeleyici ile giden NAT için hem TCP & UDP protokollerini etkinleştirin


#### <a name="details"></a>Ayrıntılar


Ortak standart yük dengeleyici kullanıldığında, belirtilen otomatik giden NAT, Yük Dengeleme kuralının Aktarım Protokolü ile eşleşir. 


1. Yük Dengeleme kuralında giden [SNAT](load-balancer-outbound-connections.md) 'yi devre dışı bırakın. 
2. Aynı yük dengeleyicide giden bir kural yapılandırın.
3. VM 'niz tarafından zaten kullanılan arka uç havuzunu yeniden kullanın. 
4. Giden kuralının bir parçası olarak "protokol": "tümü" ni belirtin. 


Yalnızca gelen NAT kuralları kullanıldığında, giden NAT sağlanmaz. 


1. VM 'Leri bir arka uç havuzuna yerleştirin.
2. Genel IP adresleri veya genel IP önekiyle bir veya daha fazla ön uç IP yapılandırması tanımlayın 
3. Aynı yük dengeleyicide giden bir kural yapılandırın. 
4. Giden kuralının bir parçası olarak "Protocol": "All" belirtin


## <a name="limitations"></a>Sınırlamalar

- Ön uç IP adresi başına kullanılabilir en fazla kısa ömürlü bağlantı noktası sayısı 64.000 ' dir.
- Yapılandırılabilir giden boşta kalma zaman aşımı aralığı 4 ila 120 dakikadır (240 ila 7200 saniye).
- Yük dengeleyici giden NAT için ıCMP 'yi desteklemez.
- Giden kuralları, yalnızca bir NIC 'in birincil IP yapılandırmasına uygulanabilir.  VM veya NVA 'nın ikincil IP 'si için giden bir kural oluşturamazsınız. Birden çok NIC desteklenir.
- Bir **kullanılabilirlik kümesindeki** tüm sanal makinelerin, giden bağlantı için arka uç havuzuna eklenmesi gerekir. 
- Bir **sanal makine ölçek kümesindeki** tüm sanal makinelerin giden bağlantı için arka uç havuzuna eklenmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Standart Load Balancer](load-balancer-overview.md) hakkında daha fazla bilgi
- [Azure Load Balancer hakkında sık sorulan sorularımızı](load-balancer-faqs.md) inceleyin
