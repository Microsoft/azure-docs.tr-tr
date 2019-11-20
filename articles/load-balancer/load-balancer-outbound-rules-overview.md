---
title: Giden kuralları-Azure Load Balancer
description: Bu öğrenme yoluyla giden ağ adresi çevirileri tanımlamak için giden kurallarını kullanmaya başlayın.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: 316b28faa458b03431cb48f02a8087116415b061
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075899"
---
# <a name="load-balancer-outbound-rules"></a>Yük Dengeleyici giden kuralları

Azure yük dengeleyici, gelen ek bir sanal ağdan giden bağlantı sağlar.  Giden kuralları, yapılandırma basit genel hale [Standard Load Balancer](load-balancer-standard-overview.md)'s giden ağ adresi çevirisi.  Ölçeklendirme ve kendi özel gereksinimlerinize göre bu özelliği ayarlamak için giden bağlantı üzerinde tam bildirim temelli denetiminiz vardır.

![Yük Dengeleyici giden kuralları](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Giden kuralları ile yük dengeleyiciye kullanabilirsiniz: 
- Giden NAT sıfırdan tanımlayın.
- ölçeklendirme ve giden NAT'ın mevcut davranışını ayarlama 

Giden kuralları denetimine izin ver:
- genel IP adresleri, hangi sanal makinelerin çevrilmesi gerekir. 
- nasıl [giden SNAT bağlantı noktaları](load-balancer-outbound-connections.md#snat) ayrılmalıdır.
- Giden çeviri sağlamak için hangi protokollerin.
- giden bağlantı boşta kalma zaman aşımı (4-120 dakika) için kullanılacak süre.
- boşta kalma zaman aşımı (genel Önizleme) üzerinde bir TCP Sıfırla'ı gönderilip gönderilmeyeceğini belirtir. 

Giden kuralları genişletin [Senaryo 2](load-balancer-outbound-connections.md#lb) içinde açıklanan [giden bağlantılar](load-balancer-outbound-connections.md) olarak kalır makale ve senaryo öncelik-olduğu.

## <a name="outbound-rule"></a>Giden kuralı

Tüm yük dengeleyici kuralları gibi giden kuralları, Yük Dengeleme ve gelen NAT kuralları aynı tanıdık söz izleyin:

**ön uç** + **parametreleri** + **arka uç havuzu**

Giden NAT için bir giden kuralı yapılandırır _arka uç havuzu tarafından tanımlanan tüm sanal makineler_ için çevrilemeyen _ön uç_.  Ve _parametreleri_ giden NAT algoritması üzerinde ek ayrıntılı denetim sağlar.

API sürümü "2018-07-01", şu şekilde yapılandırılmış bir giden kuralı tanımı izin verir:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>Etkin giden NAT bileşik tüm giden kuralları ve Yük Dengeleme kuralları bir yapılandırmadır. Giden kuralları, Yük Dengeleme kuralları artımlı. Gözden geçirme [giden NAT için Yük Dengeleme kuralı devre dışı bırakma](#disablesnat) bir VM'ye birden çok kural geçerli olduğunda, etkin giden NAT çeviri yönetmek için. Yapmanız gerekenler [giden SNAT devre dışı](#disablesnat) aynı genel IP adresini bir Yük Dengeleme kuralı kullanan bir giden kuralı tanımlarken.

### <a name="scale"></a> Birden çok IP adresi ile ölçek giden NAT

Yalnızca tek bir genel IP adresi ile bir giden kuralı kullanılabilse giden kuralları için giden NAT'ın ölçeklendirme yapılandırma yükünü Büyük ölçekli senaryolar için plan için birden çok IP adresi kullanabilirsiniz ve giden kuralları azaltmak için kullanabileceğiniz [SNAT tükenmesi](load-balancer-outbound-connections.md#snatexhaust) saldırıya desenleri.  

Ön uç tarafından sağlanan her ek IP adresi, Load Balancer SNAT bağlantı noktası olarak kullanılacak olan 64.000 kısa ömürlü bağlantı noktaları sağlar. Gelen NAT kuralları Yük Dengeleme veya tek bir ön uç olsa da, giden kuralı ön uç kavramımız genişletir ve Kural başına birden çok ön uç sağlar.  Kural başına birden çok ön uç, kullanılabilir SNAT bağlantı noktaları miktarı her genel IP adresi ile çarpılır ve büyük senaryolar desteklenir.

Ayrıca, kullanabileceğiniz bir [genel IP öneki](https://aka.ms/lbpublicipprefix) bir giden kuralı ile doğrudan.  Genel IP'si kullanan, kolay ölçeklendirme ve Azure dağıtımınızı kaynaklanan akış Basitleştirilmiş güvenilir listeye için ön ek sağlar. Genel bir IP adresi ön eki doğrudan başvurmak için yük dengeleyici kaynak içinde bir ön uç IP yapılandırması yapılandırabilirsiniz.  Bu yük dengeleyicinin genel IP öneki özel denetime sağlar ve giden kuralı, giden bağlantılar için genel IP öneki içinde yer alan tüm genel IP adresleri otomatik olarak kullanacak.  Genel IP öneki aralığı içindeki IP adreslerinden her biri, Load Balancer SNAT bağlantı noktası olarak kullanılacak IP adresi başına 64.000 kısa ömürlü bağlantı noktaları sağlar.   

Genel IP önekten giden kuralı genel IP öneki tam denetimi olmalıdır gibi bu seçeneği tercih edildiğinde oluşturulan genel IP adresi kaynakların sahip olamaz.  Daha iyi tanecikli denetim gerekiyorsa, genel IP önekten ayrı genel IP adresi kaynağı oluşturma ve birden çok genel IP adresleri ayrı ayrı bir giden kuralı ön uç için atayın.

### <a name="snatports"></a> Bağlantı noktası ayırma SNAT ayarlama

Giden kuralları ayarlamak için kullanılabileceğiniz [otomatik SNAT bağlantı noktası ayırma arka uç havuz boyutunu temel alarak](load-balancer-outbound-connections.md#preallocatedports) ve otomatik SNAT bağlantı noktası ayırma sağladığından daha fazla veya az ayırın.

Bağlantı noktalarını VM (NIC IP yapılandırması) başına 10.000 SNAT ayırmak için aşağıdaki parametreyi kullanın.
 

          "allocatedOutboundPorts": 10000

Giden bir kuralın tüm ön uçlarından gelen her genel IP adresi, SNAT bağlantı noktaları olarak kullanılmak üzere 64.000 kısa ömürlü bağlantı noktasına kadar katkıda bulunur.  Yük Dengeleyici SNAT katları 8 bağlantı noktası ayırır. 8 katı olmayan bir değer sağlarsanız, yapılandırma işlemi reddedilir.  Genel IP adresleri sayısına göre bulunandan daha fazla SNAT bağlantı noktaları ayırmak çalışırsanız, yapılandırma işlemi reddedilir.  Örneğin, VM başına 10.000 bağlantı noktası ve arka uç havuzunda 7 VM 'Ler ayırırsanız tek bir genel IP adresi paylaşabilir, yapılandırma reddedilir (7 x 10.000 SNAT bağlantı noktaları > 64.000 SNAT bağlantı noktaları).  Daha fazla genel IP adresleri ön uç senaryoyu etkinleştirmek için bir giden kuralı ekleyebilirsiniz.

Geri döndürebilirsiniz [otomatik SNAT bağlantı noktası ayırma arka uç havuz boyutunu temel alarak](load-balancer-outbound-connections.md#preallocatedports) 0 bağlantı noktası numarası belirterek. Bu durumda, ilk 50 sanal makine örnekleri 1024 bağlantı noktalarını alacak şekilde, 51-100 sanal makine örnekleri tabloya göre 512 ve bu şekilde devam eder.

### <a name="idletimeout"></a> Denetim giden akış boşta kalma zaman aşımı

Giden kuralları giden akış boşta kalma zaman aşımını denetleme ve uygulamanızın ihtiyaçlarını eşleştirmek için bir yapılandırma parametresi sağlayın.  4 dakikalık varsayılan giden boşta kalma zaman aşımı.  Parametresi, belirli bir kuralla eşleşen akışlar için boşta kalma zaman aşımı için dakika sayısı kadar olan 4 ila 120 arasında bir değer kabul eder.

Giden boşta kalma zaman aşımı 1 saate ayarlamak için aşağıdaki parametresini kullanın:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Sıfırlama TCP boşta kalma zaman aşımı (Önizleme) etkinleştirme

Load Balancer'ın varsayılan davranışı, giden boşta kalma zaman aşımı ulaşıldığında akışı sessizce bırak sağlamaktır.  EnableTCPReset parametresiyle daha öngörülebilir bir uygulama davranışını etkinleştirmek ve gönderilip gönderilmeyeceğini denetleyen çift TCP Reset (TCP k) yönlü, zaman aşımı giden boşta kalma zaman aşımı. 

TCP sıfırlama bir giden kuralı etkinleştirmek için aşağıdaki parametresini kullanın:

           "enableTcpReset": true

Gözden geçirme [sıfırlama TCP boşta kalma zaman aşımı (Önizleme) üzerinde](https://aka.ms/lbtcpreset) bölge kullanılabilirliği gibi ayrıntıları.

### <a name="proto"></a> Tek bir kural ile TCP ve UDP aktarım protokolünü destekler

Büyük olasılıkla "All" giden kuralı Aktarım Protokolü için kullanmak istersiniz, ancak bunu yapmak için bir gereksinimi varsa giden kuralı da belirli bir aktarım protokolü için de uygulayabilirsiniz.

TCP ve UDP protokolünü ayarlamak için aşağıdaki parametresini kullanın:

          "protocol": "All"

### <a name="disablesnat"></a> Giden NAT için Yük Dengeleme kuralı devre dışı bırak

Daha önce belirtildiği gibi Yük Dengeleme kuralları, giden NAT'ın otomatik programlamasını sağlayın. Ancak, bazı senaryolarda avantaj ya da Yük Dengeleme davranışını denetlemek için izin veya kuralı tarafından otomatik programlama giden NAT'ın devre dışı bırakmak ihtiyacınız.  Giden kuralları, otomatik giden NAT programlama durdurmak önemli olduğu senaryolar vardır.

Bu parametre iki şekilde kullanabilir:
- Giden NAT için gelen bir IP adresi kullanarak isteğe bağlı gizleme  Giden kuralları, Yük Dengeleme kuralları artımlı ve bu parametre kümesi ile giden denetiminde kuralıdır.
  
- Gelen ve giden için eşzamanlı olarak kullanılan bir IP adresine giden NAT parametrelerini ayarlayın.  Otomatik giden NAT programlama denetimini almak bir giden kuralı izin vermek için devre dışı bırakılmalıdır.  Örneğin, bir adresin SNAT bağlantı noktası ayırma da değiştirmek için kullanılan için gelen, bu parametre ayarlanmalıdır true.  Yeniden tanımlanacak bir giden kuralı kullanmayı denerseniz, bir IP adresi için de kullanılan parametreleri gelen ve giden NAT programlamasını Yük Dengeleme kuralını yayımlamadık, bir giden kuralı yapılandırma işlemi başarısız olur.

>[!IMPORTANT]
> Bu parametre true olarak ayarlayın ve bir giden kuralına sahip olmaması durumunda, sanal makinenizin giden bağlantı olmaz (veya [örnek düzeyi genel IP senaryo](load-balancer-outbound-connections.md#ilpip) giden bağlantıyı tanımlamak için.  Sanal makinenizin veya uygulamanızı bazı işlemler, giden bağlantı kullanılabilir olması bağlı olabilir. Senaryonuz bağımlılıkları anlamak ve bu değişiklik etkisini göz önünde bulundurduğunuzdan emin olun.

Yük Dengeleme kuralı bu yapılandırma parametresi ile giden SNAT devre dışı bırakabilirsiniz:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

DisableOutboundSNAT parametresi varsayılan olarak, Yük Dengeleme kuralını gösterir. false olarak **mu** otomatik giden NAT kuralı yapılandırması yük dengelemeyi bir Ayna görüntüsünü sağlayın.  

Yük Dengeleme kuralını disableOutboundSnat Yük Dengeleme kuralı true olarak ayarlayın, aksi takdirde otomatik giden NAT programlama denetimin serbest bırakır.  Yük Dengeleme kuralı sonucunda giden SNAT devre dışı bırakıldı.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Varolan yeniden kullanabilir veya yeni bir arka uç havuzlarını tanımlayın

Giden kuralları, sanal makinelerin bir kuralın uygulanacağı grup tanımlamak için yeni bir kavramın İstemediğimiz.  Bunun yerine, aynı zamanda Yük Dengeleme kuralları için kullanılan bir arka uç havuzu kavramını yeniden. Bu, var olan bir arka uç havuzu tanımı yeniden veya özellikle bir giden kuralı için oluşturma yapılandırmasını basitleştirmek için kullanabilirsiniz.

## <a name="scenarios"></a>Senaryolar

### <a name="groom"></a> Giden bağlantılar belirli bir genel IP adresleri kümesini Temizle

Bilgisayar bir genel IP adreslerini beyaz listeye ekleme senaryoları kolaylaştırmak için belirli kümesinden kaynaklanacak şekilde görünmesini giden bağlantılar bakımını yapmak için bir giden kuralı kullanabilirsiniz.  Yük Dengeleme kuralı tarafından eskisinden farklı bir genel IP adresleri veya bu kaynak genel IP adresi Yük Dengeleme kuralı tarafından kullanılanla aynı olabilir.  

1. Oluşturma [genel IP öneki](https://aka.ms/lbpublicipprefix) (veya ortak IP adreslerini genel IP ön ek)
2. Genel bir Standart Yük Dengeleyici oluşturma
3. Ön uçlar genel başvuran oluşturma IP önekini (veya genel IP adresleri) kullanmak istediğiniz
4. Arka uç havuzu yeniden veya bir arka uç havuzu oluşturun ve Vm'leri genel Load Balancer arka uç havuzu yerleştirin
5. Ön uçlar kullanarak bu VM'ler için giden NAT programlamak için genel yük dengeleyici üzerindeki bir giden kuralı yapılandırma
   
Kullanılacak Yük Dengeleme kuralı için istemiyorsanız için gereken giden [giden SNAT devre dışı](#disablesnat) Yük Dengeleme kuralı.

### <a name="modifysnat"></a> SNAT bağlantı noktası ayırmayı Değiştir

Giden kuralları ayarlamak için kullanılabileceğiniz [otomatik SNAT bağlantı noktası ayırma arka uç havuz boyutunu temel alarak](load-balancer-outbound-connections.md#preallocatedports).

Örneğin, tek bir genel IP adresi için giden NAT paylaşımı iki sanal makine varsa, varsayılan 1024 bağlantı noktalarından SNAT tükenmesi yaşıyorsanız ayrılan SNAT bağlantı noktası sayısını artırmak isteyebilirsiniz. Her genel IP adresi en fazla 64.000 kısa ömürlü bağlantı noktasına katkıda bulunabilir.  Tek bir genel IP adresi ön ucuna sahip bir giden kuralı yapılandırırsanız, arka uç havuzundaki sanal makinelere Toplam 64.000 SNAT bağlantı noktası dağıtabilirsiniz.  İki VM için, en fazla 32.000 SNAT bağlantı noktası bir giden kuralla ayrılabilir (2x 32.000 = 64.000).

Gözden geçirme [giden bağlantılar](load-balancer-outbound-connections.md) hakkında bilgi [SNAT](load-balancer-outbound-connections.md#snat) bağlantı noktalarını ayrılan ve kullanılır.

### <a name="outboundonly"></a> Yalnızca giden etkinleştir

Giden NAT VM için bir grubu sağlamak için genel bir Standard Load Balancer'ı kullanabilirsiniz. Bu senaryoda, bir giden kuralı kendisi tarafından herhangi bir ek kuralın gerek kalmadan kullanabilirsiniz.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Yalnızca VM'ler için giden NAT (Giriş yok)

Genel bir Standard Load Balancer tanımlamak, VM'ler arka uç havuzuna yerleştirin ve giden NAT program ve giden bağlantılar belirli bir genel IP adresi kaynağı için Temizleme için bir giden kuralı yapılandırın. Bir genel IP öneki de kullanabilirsiniz güvenilir listeye giden bağlantılar kaynağını basitleştirin.

1. Genel bir Standard Load Balancer oluşturun.
2. Arka uç havuzu oluşturabilir ve Vm'leri genel Load Balancer arka uç havuzu halinde yerleştirin.
3. Bu VM'ler için giden NAT programlamak için genel yük dengeleyici üzerindeki bir giden kuralı yapılandırın.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Giden NAT için iç Load Balancer standart senaryoları

Giden bağlantı açıkça bildirilen kadar iç bir Standard Load Balancer kullanırken, giden NAT kullanılamaz. Bu adımlarla iç standart yük dengeleyici arkasındaki VM'ler için giden bağlantı oluşturmak için bir giden kuralı kullanarak giden bağlantıyı tanımlayabilirsiniz:

1. Genel bir Standard Load Balancer oluşturun.
2. Arka uç havuzu oluşturabilir ve Vm'leri iç Load Balancer yanı sıra genel Load Balancer arka uç havuzu yerleştirin.
3. Bu VM'ler için giden NAT programlamak için genel yük dengeleyici üzerindeki bir giden kuralı yapılandırın.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Genel bir Standard Load Balancer ile giden NAT için hem TCP ve UDP protokolleri etkinleştir

- Genel standart yük dengeleyici kullanıldığında, sağlanan otomatik giden NAT programlama Yük Dengeleme kuralını Aktarım Protokolü ile eşleşir.  

   1. Yük Dengeleme kuralı giden SNAT devre dışı bırakın.
   2. Aynı yük Dengeleyicide bir giden kuralı yapılandırın.
   3. Sanal makineleriniz tarafından zaten kullanılan arka uç havuzu yeniden kullanın.
   4. Belirtin "protokol": "All" giden kuralı bir parçası olarak.

- Yalnızca gelen NAT kuralları kullanıldığında, giden NAT sağlanır.

   1. Vm'leri bir arka uç havuzuna yerleştirin.
   2. Genel IP adresleri veya ortak IP öneki ile bir veya daha fazla ön uç IP yapılandırmaları tanımlar.
   3. Aynı yük Dengeleyicide bir giden kuralı yapılandırın.
   4. "Protokol" belirtin: giden kuralı bir parçası olarak "Tüm"

## <a name="limitations"></a>Sınırlamalar

- Ön uç IP adresi başına kullanılabilir en fazla kısa ömürlü bağlantı noktası sayısı 64.000 ' dir.
- Yapılandırılabilir giden boşta kalma zaman aşımı aralığı 4 ila 120 dakikadır (240 ila 7200 saniye).
- Yük Dengeleyici giden NAT için ICMP desteklemiyor
- Portal, yapılandırma veya giden kuralları görüntülemek için kullanılamaz.  Bunun yerine şablonları, REST API, Az CLI 2. 0'ı veya PowerShell kullanın.
- Giden kuralları, yalnızca bir NIC 'in birincil IP yapılandırmasına uygulanabilir.  Birden çok NIC desteklenir.

## <a name="next-steps"></a>Sonraki adımlar

- Kullanma hakkında bilgi edinin [giden bağlantılar için yük dengeleyici](load-balancer-outbound-connections.md).
- Hakkında bilgi edinin [standart Load Balancer](load-balancer-standard-overview.md).
- Hakkında bilgi edinin [çift TCP sıfırlama yönlü boşta kalma zaman aşımı üzerinde](load-balancer-tcp-reset.md).
- [Azure CLI 2.0 ile giden kuralları yapılandırma](configure-load-balancer-outbound-cli.md).
