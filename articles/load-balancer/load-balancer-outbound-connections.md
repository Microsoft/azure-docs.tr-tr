---
title: Azure 'da giden bağlantılar
titlesuffix: Azure Load Balancer
description: Bu makalede, Azure 'un ortak internet hizmetleriyle iletişim kurması için VM 'nin nasıl çalıştığı açıklanır.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 9dcc5fa201c08ca4b1e65b8aae88118731eba427
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881064"
---
# <a name="outbound-connections-in-azure"></a>Azure 'da giden bağlantılar

Azure birçok farklı mekanizma aracılığıyla müşteri dağıtımları için giden bağlantı sağlar. Bu makalede senaryoların ne olduğu, ne zaman uygulandığı, nasıl çalıştığı ve nasıl yönetileceği açıklanmaktadır.

>[!NOTE] 
>Bu makalede yalnızca Kaynak Yöneticisi dağıtımları ele alınmaktadır. Azure 'daki tüm klasik dağıtım senaryoları için [giden bağlantıları (klasik)](load-balancer-outbound-connections-classic.md) gözden geçirin.

Azure 'daki bir dağıtım, genel IP adresi alanında Azure dışındaki uç noktalarla iletişim kurabilir. Bir örnek, genel IP adresi alanındaki bir hedefe giden akışı başlattığında, Azure özel IP adresini dinamik olarak genel bir IP adresiyle eşleştirir. Bu eşleme oluşturulduktan sonra, bu çıkış kaynaklı akış için geri dönüş trafiği akışın kaynaklandığı özel IP adresine de ulaşabilir.

Azure, bu işlevi gerçekleştirmek için kaynak ağ adresi çevirisini (SNAT) kullanır. Birden çok özel IP adresi tek bir genel IP adresinin arkasında olduğunda, Azure özel IP adreslerine maske eklemek için [bağlantı noktası adres çevirisini (Pat)](#pat) kullanır. Kısa ömürlü bağlantı noktaları, PAT için kullanılır ve havuz boyutuna bağlı olarak [önceden ayrılır](#preallocatedports) .

Birden çok [giden senaryo](#scenarios)vardır. Bu senaryoları gerektiğinde birleştirebilirsiniz. Özellikleri, kısıtlamaları ve desenleri, dağıtım modeliniz ve Uygulama senaryonuz için uygun olarak anlamak üzere dikkatlice gözden geçirin. [Bu senaryoları yönetme](#snatexhaust)kılavuzunu gözden geçirin.

>[!IMPORTANT] 
>Standart Load Balancer ve standart genel IP, giden bağlantıya yeni yetenekler ve farklı davranışlar getirir.  Bunlar temel SKU 'Lar ile aynı değildir.  Standart SKU 'Lar ile çalışırken giden bağlantı isterseniz, standart genel IP adresleriyle veya standart ortak Load Balancer açıkça tanımlamanız gerekir.  Bu, dahili bir Standart Load Balancer kullanırken giden bağlantı oluşturmayı içerir.  Her zaman standart bir genel Load Balancer giden kuralları kullanmanızı öneririz.  [3. senaryo](#defaultsnat) standart SKU ile kullanılamaz.  Bu, bir iç Standart Load Balancer kullanıldığında, giden bağlantı istenirse arka uç havuzundaki VM 'Ler için giden bağlantı oluşturmak üzere gerekli adımları uygulamanız gerekir.  Giden bağlantı bağlamında, tek bir tek başına VM, tüm VM 'ler bir kullanılabilirlik kümesinde, bir VMSS içindeki tüm örnekler bir grup olarak davranır. Bu, bir kullanılabilirlik kümesindeki tek bir VM standart SKU ile ilişkili ise, bu kullanılabilirlik kümesindeki tüm sanal makine örnekleri, tek bir örnek doğrudan ilişkili olmasa bile standart SKU ile ilişkilendirildikleri kurallarla aynı kurallara göre davranır.  Genel kavramları anlamak için tüm belgeyi dikkatle gözden geçirin, SKU 'Lar arasındaki farklılıklar için [Standart Load Balancer](load-balancer-standard-overview.md) gözden geçirin ve [giden kuralları](load-balancer-outbound-rules-overview.md)gözden geçirin.  Giden kuralları kullanmak, giden bağlantıların tüm yönleri üzerinde ayrıntılı denetim sağlar.

## <a name="scenarios"></a>Senaryoya genel bakış

Azure Load Balancer ve ilgili kaynaklar [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)kullanırken açıkça tanımlanır.  Azure Şu anda Azure Resource Manager kaynaklarına giden bağlantı sağlamak için üç farklı yöntem sunmaktadır. 

| SKU'lar | Senaryo | Yöntem | IP protokolleri | Açıklama |
| --- | --- | --- | --- | --- |
| Standart, temel | [1. Ortak IP adresine sahip VM (Load Balancer ile veya olmayan)](#ilpip) | SNAT, bağlantı noktası aşağı olarak kullanılmıyor | TCP, UDP, ICMP, ESP | Azure, örneğin NIC 'in IP yapılandırmasına atanan genel IP 'yi kullanır. Örnekte, tüm kısa ömürlü bağlantı noktaları kullanılabilir. Standart Load Balancer kullanırken, giden bağlantıyı açıkça tanımlamak için [giden kurallarını](load-balancer-outbound-rules-overview.md) kullanmanız gerekir |
| Standart, temel | [2. Bir VM ile ilişkili ortak Load Balancer (örnekte genel IP adresi yok)](#lb) | Load Balancer ön uçları kullanarak bağlantı noktası geçici olarak (PAT) SNAT | TCP, UDP |Azure, genel Load Balancer ön uçlarının genel IP adresini birden çok özel IP adresi ile paylaşır. Azure, ön uçların kısa ömürlü bağlantı noktalarını kullanır. |
| hiçbiri veya temel | [3. Tek başına VM (Load Balancer yok, genel IP adresi yok)](#defaultsnat) | Bağlantı noktası geçici olarak SNAT (PAT) | TCP, UDP | Azure, SNAT için bir genel IP adresi otomatik olarak atar, bu genel IP adresini kullanılabilirlik kümesinin birden çok özel IP adresi ile paylaşır ve bu genel IP adresinin kısa ömürlü bağlantı noktalarını kullanır. Bu senaryo, önceki senaryolar için bir geri dönüş olur. Görünürlük ve denetime ihtiyacınız varsa bunu önermiyoruz. |

Bir VM 'nin genel IP adresi alanında Azure dışındaki uç noktalarla iletişim kurmasını istemiyorsanız, erişimi gerektiği şekilde engellemek için ağ güvenlik grupları (NSG 'ler) kullanabilirsiniz. [Giden bağlantının önlenmesi](#preventoutbound) bölümünde NSG 'ler daha ayrıntılı şekilde anlatılmaktadır. Bir sanal ağın herhangi bir giden erişimi olmadan tasarlanması, uygulanması ve yönetilmesi ile ilgili yönergeler bu makalenin kapsamı dışındadır.

### <a name="ilpip"></a>Senaryo 1: Ortak IP adresine sahip VM

Bu senaryoda, sanal makineye atanmış bir genel IP vardır. Giden bağlantılar söz konusu olduğunda, VM 'nin yük dengeli olup olmadığı önemi yoktur. Bu senaryo diğerlerine göre önceliklidir. Ortak bir IP adresi kullanıldığında, VM tüm giden akışlar için genel IP adresini kullanır.  

Bir VM 'ye atanan genel IP, 1:1 ilişkidir (1: çok) ve durum bilgisiz 1:1 NAT olarak uygulanır.  Bağlantı noktası (PAT) kullanılmaz ve VM 'nin kullanılabilir tüm kısa ömürlü bağlantı noktaları kullanılabilir.

Uygulamanız birçok giden akışı başlatırsa ve SNAT bağlantı noktası tükenmesi ile karşılaşırsanız, [SNAT kısıtlamalarını azaltmak Için genel BIR IP adresi](#assignilpip)atamayı göz önünde bulundurun. [SNAT tükenmesi yönetimini](#snatexhaust) tamamen inceleyin.

### <a name="lb"></a>Senaryo 2: Genel IP adresi olmayan yük dengeli VM

Bu senaryoda, VM ortak Load Balancer arka uç havuzunun bir parçasıdır. SANAL makineye atanmış bir genel IP adresi yok. Load Balancer kaynak, arka uç havuzuyla genel IP ön ucu arasında bağlantı oluşturmak için bir yük dengeleyici kuralıyla yapılandırılmalıdır.

Bu kural yapılandırmasını tamamlamayın, davranış [genel IP olmadan tek BAŞıNA VM](#defaultsnat)için senaryoda açıklanacaktır. Durum araştırmasının başarılı olması için kuralın arka uç havuzunda çalışma dinleyicisi olması gerekmez.

Yük dengeli VM bir giden akış oluşturduğunda, Azure giden akışın özel kaynak IP adresini genel Load Balancer ön ucu genel IP adresine çevirir. Azure, bu işlevi gerçekleştirmek için SNAT 'yi kullanır. Azure, genel bir IP adresinin arkasında birden çok özel IP adresini geçici olarak çözmek için [Pat](#pat) 'yi de kullanır. 

Yük dengeleyicinin genel IP adresi ön ucu için kısa ömürlü bağlantı noktaları, VM 'nin kaynaklandığı bireysel akışları ayırt etmek için kullanılır. SNAT, giden akışlar oluşturulduğunda, [önceden ayrılmış kısa ömürlü bağlantı noktalarını](#preallocatedports) dinamik olarak kullanır. Bu bağlamda, SNAT için kullanılan kısa ömürlü bağlantı noktaları SNAT bağlantı noktaları olarak adlandırılır.

SNAT bağlantı noktaları, [SNAT ve Pat 'ı anlama](#snat) bölümünde açıklandığı gibi önceden ayrılır. Bu, tükenebilir sınırlı bir kaynaktır. Bunların nasıl kullanıldığını anlamak önemlidir. [](#pat) Bu tüketim için nasıl tasarlanacağını ve gerekirse etkisini anlamak için, [SNAT tükenmesi yönetimini](#snatexhaust)gözden geçirin.

[Birden çok genel IP adresi Load Balancer temel ile ilişkilendirildiğinde](load-balancer-multivip-overview.md), bu genel IP adreslerinden herhangi biri giden akışlar için aday olur ve rastgele bir seçilir.  

Load Balancer temel ile giden bağlantıların sistem durumunu izlemek için, Load Balancer ve [Uyarı olay günlüklerinin](load-balancer-monitor-log.md#alert-event-log) [Azure IZLEYICI günlüklerini](load-balancer-monitor-log.md) kullanarak SNAT bağlantı noktası Tükenme iletilerini izleyebilirsiniz.

### <a name="defaultsnat"></a>Senaryo 3: Genel IP adresi olmayan tek başına VM

Bu senaryoda, VM ortak bir Load Balancer havuzunun parçası değildir (bir iç Standart Load Balancer havuzunun parçası değildir) ve kendisine atanmış bir genel IP adresi yoktur. VM bir giden akış oluşturduğunda, Azure giden akışın özel kaynak IP adresini ortak kaynak IP adresine çevirir. Bu giden akış için kullanılan genel IP adresi yapılandırılamaz ve aboneliğin genel IP kaynak sınırına göre sayılmaz. Bu genel IP adresi size ait değil ve ayrılamaz. VM 'yi veya kullanılabilirlik kümesini veya sanal makine ölçek kümesini yeniden dağıtıyorsanız, bu genel IP adresi yayımlanır ve yeni bir genel IP adresi istenir. IP adreslerini beyaz listeye almak için bu senaryoyu kullanmayın. Bunun yerine, çıkış senaryosunu ve giden bağlantı için kullanılacak genel IP adresini açıkça bildirdiğiniz diğer iki senaryonun birini kullanın.

>[!IMPORTANT] 
>Bu senaryo __yalnızca__ bir iç temel Load Balancer eklendiğinde de geçerlidir. Bir VM 'ye dahili Standart Load Balancer eklendiğinde Senaryo 3 __kullanılamaz__ .  Dahili bir Standart Load Balancer kullanmaya ek olarak [Senaryo 1](#ilpip) veya [Senaryo 2](#lb) ' i açıkça oluşturmanız gerekir.

Azure, bu işlevi gerçekleştirmek için bağlantı noktası geçici ([Pat](#pat)) ile SNAT 'yi kullanır. Bu senaryo, kullanılan IP adresi üzerinde denetim olmaması dışında [Senaryo 2](#lb)' ye benzer. Bu senaryo 1 ve 2 ' nin bulunmadığı durumlarda için bir geri dönüş senaryosudur. Giden adres üzerinde denetim istiyorsanız bu senaryoyu önermiyoruz. Giden bağlantılar uygulamanızın önemli bir parçasıysa, başka bir senaryo seçmeniz gerekir.

SNAT bağlantı noktaları, [SNAT ve Pat 'ı anlama](#snat) bölümünde açıklandığı gibi önceden ayrılır.  Bir kullanılabilirlik kümesini paylaşan VM 'lerin sayısı, hangi ön ayırma katmanının uygulanacağını belirler.  Kullanılabilirlik kümesi olmayan tek başına VM, ön ayırmayı belirleme (1024 SNAT bağlantı noktası) amaçları için etkin bir şekilde 1 havuzu olur. SNAT bağlantı noktaları, tükenebilir sınırlı bir kaynaktır. Bunların nasıl kullanıldığını anlamak önemlidir. [](#pat) Bu tüketim için nasıl tasarlanacağını ve gerekirse etkisini anlamak için, [SNAT tükenmesi yönetimini](#snatexhaust)gözden geçirin.

### <a name="combinations"></a>Çoklu, Birleşik senaryolar

Belirli bir sonuca ulaşmak için yukarıdaki bölümlerde açıklanan senaryoları birleştirebilirsiniz. Birden çok senaryo mevcut olduğunda, bir öncelik sırası geçerlidir: [Senaryo 1](#ilpip) [Senaryo 2](#lb) ve [3](#defaultsnat)' ten önceliklidir. [Senaryo 2](#lb) geçersiz kılmaları [Senaryo 3](#defaultsnat).

Bir örnek, uygulamanın sınırlı sayıda hedefe giden bağlantıları yoğun bir şekilde kullandığı ancak aynı zamanda gelen akışları bir Load Balancer ön uç üzerinden aldığından Azure Resource Manager bir dağıtımdır. Bu durumda, yardım için 1 ve 2 senaryolarını birleştirebilirsiniz. Ek desenler için, [SNAT tükenmesi yönetimini](#snatexhaust)gözden geçirin.

### <a name="multife"></a>Giden akışlar için birden çok ön uç

#### <a name="standard-load-balancer"></a>Standart Load Balancer

Standart Load Balancer, [birden çok (genel) IP ön uçları](load-balancer-multivip-overview.md) mevcut olduğunda giden akışlar için tüm adayları aynı anda kullanır. Giden bağlantılar için bir yük dengeleme kuralı etkinleştirildiyse, her ön uç, kullanılabilir önceden ayrılmış SNAT bağlantı noktalarının sayısını çarpar.

Yeni bir yük dengeleme kuralı seçeneği ile giden bağlantılar için bir ön uç IP adresinin kullanılmasını Önle seçeneğini belirleyebilirsiniz:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalde, `disableOutboundSnat` seçeneği _false_ olur ve bu kuralın, Yük Dengeleme kuralının arka uç havuzundaki ilişkili VM 'ler için giden SNAT programları olduğunu belirtir. Load Balancer `disableOutboundSnat` , bu yük dengeleme kuralının arka uç havuzundaki VM 'ler için giden bağlantılar için ilgili ön uç IP adresini kullanmasını engellemek üzere _true_ olarak değiştirilebilir.  Ayrıca, dış akışlar için de belirli bir IP adresini aynı zamanda [birden çok, Birleşik senaryolarda](#combinations) da açıklandığı gibi atayabilirsiniz.

#### <a name="load-balancer-basic"></a>Load Balancer temel

Load Balancer temel, dış akışlar için [birden çok (genel) IP ön uçları](load-balancer-multivip-overview.md) aday olduğunda giden akışlar için kullanılacak tek bir ön uç seçer. Bu seçim yapılandırılamaz ve seçim algoritmasını rastgele olacak şekilde göz önünde bulundurmanız gerekir. Giden akışlar için belirli bir IP adresini [birden çok, Birleşik senaryolar](#combinations)bölümünde açıklandığı gibi belirtebilirsiniz.

### <a name="az"></a>Kullanılabilirlik Alanları

[Kullanılabilirlik alanları ile standart Load Balancer](load-balancer-standard-availability-zones.md)kullanırken, bölgesel olarak yedekli ön uçlar, bölgesel olarak YEDEKLI giden SNAT BAĞLANTıLARı ve SNAT programlama içi bölge hatası verebilir.  ZGen ön uçları kullanıldığında, giden SNAT bağlantıları Fate, ait oldukları bölge ile paylaşılır.

## <a name="snat"></a>SNAT ve PAT 'yi anlama

### <a name="pat"></a>Bağlantı noktası, SNAT (PAT)

Ortak bir Load Balancer kaynağı VM örnekleriyle ilişkilendirildiğinde, her giden bağlantı kaynağı yeniden yazılır. Kaynak, sanal ağ özel IP adresi alanından yük dengeleyicinin ön uç genel IP adresine yeniden yazılır. Genel IP adresi alanında, akışın 5 demet (kaynak IP adresi, kaynak bağlantı noktası, IP Aktarım Protokolü, hedef IP adresi, hedef bağlantı noktası) benzersiz olmalıdır.  Bağlantı noktası, TCP veya UDP IP protokolleriyle birlikte kullanılabilir.

Çok sayıda akış tek bir genel IP adresinden kaynaklandığından, bu, özel kaynak IP adresini yeniden yazdıktan sonra bunu gerçekleştirmek için kısa ömürlü bağlantı noktaları (SNAT bağlantı noktaları) kullanılır. Geçici bağlantı noktası, SNAT bağlantı noktalarını UDP ve TCP 'ye karşı farklı şekilde ayırır.

#### <a name="tcp"></a>TCP SNAT bağlantı noktaları

Tek bir hedef IP adresine, bağlantı noktasına akış başına bir SNAT bağlantı noktası kullanılır. Aynı hedef IP adresine, bağlantı noktasına ve protokolüne birden çok TCP akışı için, her TCP akışı tek bir SNAT bağlantı noktası kullanır. Bu, akışların aynı ortak IP adresinden kaynaklandıklarında benzersiz olmasını sağlar ve aynı hedef IP adresine, bağlantı noktasına ve protokolüne gider. 

Her biri farklı bir hedef IP adresi, bağlantı noktası ve protokole birden çok akış, tek bir SNAT bağlantı noktasını paylaşır. Hedef IP adresi, bağlantı noktası ve protokol, genel IP adres alanındaki akışları ayırt etmek için ek kaynak bağlantı noktalarına gerek olmadan akış yapar.

#### <a name="udp"></a>UDP SNAT bağlantı noktaları

UDP SNAT bağlantı noktaları, TCP SNAT bağlantı noktalarından farklı bir algoritma tarafından yönetilir.  Load Balancer, UDP için "bağlantı noktası kısıtlanmış koni NAT" olarak bilinen bir algoritma kullanır.  Hedef IP adresi, bağlantı noktasından bağımsız olarak her akış için bir SNAT bağlantı noktası kullanılır.

#### <a name="snat-port-reuse"></a>SNAT bağlantı noktası yeniden kullanımı

Bağlantı noktası yayımlandıktan sonra, bağlantı noktası gerektiğinde yeniden kullanılabilir.  SNAT bağlantı noktalarını, belirli bir senaryo için en düşük ve en yüksek olan kullanılabilir bir sıra olarak düşünebilirsiniz ve yeni bağlantılar için kullanılabilir ilk SNAT bağlantı noktası kullanılır. 
 
#### <a name="exhaustion"></a>Tüken

SNAT bağlantı noktası kaynakları tükendiğinde, mevcut akışlar SNAT bağlantı noktalarını yayınlana kadar giden akışlar başarısız olur. Flow kapandığında geri kazanır SNAT bağlantı noktalarını Load Balancer ve boştaki akışlardan geri kazanma SNAT bağlantı noktaları için [4 dakikalık bir boşta kalma zaman aşımı süresi](#idletimeout) kullanır.

UDP SNAT bağlantı noktaları, kullanılan algoritmadaki fark nedeniyle TCP SNAT bağlantı noktalarından genellikle çok daha hızlı tükeiyor. Testi bu fark göz önüne alarak tasarlamanızı ve ölçeklendirmeniz gerekir.

Genellikle SNAT bağlantı noktası tükenmesi ' ne yol açabilecek koşulları azaltmak için, SNAT 'yi [yönetme](#snatexhaust) bölümünü gözden geçirin.

### <a name="preallocatedports"></a>Bağlantı noktası için geçici bağlantı noktası önayırması (PAT)

Azure, bağlantı noktası geçici olarak ([Pat](#pat)) kullanılırken arka uç havuzunun boyutuna bağlı olarak kullanılabilir önceden ayrılmış SNAT bağlantı noktalarının sayısını belirlemede bir algoritma kullanır. SNAT bağlantı noktaları, belirli bir genel IP kaynak adresi için kullanılabilir kısa ömürlü bağlantı noktalarıdır.

Aynı sayıda SNAT bağlantı noktası, sırasıyla UDP ve TCP için önceden ayrılır ve IP Aktarım Protokolü başına bağımsız olarak kullanılır.  Ancak, SNAT bağlantı noktası kullanımı, akışın UDP veya TCP olmasına bağlı olarak farklılık belirtir.

>[!IMPORTANT]
>Standart SKU SNAT programlama, IP Aktarım Protokolü başına ve yük dengeleme kuralından türetilir.  Yalnızca bir TCP Yük Dengeleme kuralı varsa, SNAT yalnızca TCP için kullanılabilir. Yalnızca bir TCP Yük Dengeleme kuralınız varsa ve UDP için giden SNAT gerekiyorsa, aynı ön uçta aynı arka uç havuzuna bir UDP Yük Dengeleme kuralı oluşturun.  Bu, UDP için SNAT programlamayı tetikler.  Çalışma kuralı veya sistem durumu araştırması gerekli değildir.  Yük Dengeleme kuralında belirtilen Aktarım Protokolü ne olursa olsun, temel SKU SNAT her zaman her iki IP Aktarım Protokolü için SNAT.

Azure, SNAT bağlantı noktalarını her VM 'nin NIC 'sinin IP yapılandırmasına önceden ayırır. Havuza bir IP yapılandırması eklendiğinde, bu IP yapılandırması için, arka uç havuz boyutuna bağlı olarak SNAT bağlantı noktaları önceden ayrılır. Giden akışlar oluşturulduğunda, [Pat](#pat) dinamik olarak (önceden ayrılan sınıra kadar) kullanır ve akış kapandığında veya [boşta kalma zaman aşımları](#idletimeout) olduğunda bu bağlantı noktalarını yayınlar.

Aşağıdaki tabloda, arka uç havuz boyutlarının katmanları için SNAT bağlantı noktası ön ayırmaları gösterilmektedir:

| Havuz boyutu (VM örnekleri) | IP yapılandırması başına önceden ayrılmış SNAT bağlantı noktaları|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

>[!NOTE]
> [Birden çok](load-balancer-multivip-overview.md)ön uç içeren standart Load Balancer kullanırken, her bir ön uç IP adresi önceki tabloda kullanılabilir SNAT bağlantı noktalarının sayısını çarpar. Örneğin, her biri ayrı bir ön uç IP adresi olan 2 Yük Dengeleme kuralıyla 50 VM 'nin bir arka uç havuzu, IP yapılandırması başına 2048 (2x 1024) SNAT bağlantı noktasını kullanır. Bkz. [birden çok ön uç](#multife)için ayrıntılar.

Kullanılabilir SNAT bağlantı noktası sayısının, akış sayısına doğrudan çevrilemez olduğunu unutmayın. Tek bir SNAT bağlantı noktası, birden çok benzersiz hedef için yeniden kullanılabilir. Bağlantı noktaları yalnızca akışları benzersiz hale getirmek için gerekliyse kullanılır. Tasarım ve azaltma Kılavuzu için, [Bu tüketilme kaynağını yönetme](#snatexhaust) ve [Pat](#pat)'yi açıklayan bölüm hakkında bölümüne bakın.

Arka uç havuzunuzun boyutunu değiştirmek, sağlanan akışlarınızdan bazılarını etkileyebilir. Arka uç havuzu boyutu bir sonraki katmana artıyorsa ve geçiş yaptığında, önceden ayrılan SNAT bağlantı noktalarınızın yarısı sonraki daha büyük arka uç havuz katmanına geçiş sırasında geri kazanılır. Geri kazanılan bir SNAT bağlantı noktasıyla ilişkili akışlar zaman aşımına uğrar ve yeniden oluşturulmalıdır. Yeni bir akış deneniyorsa, önceden ayrılan bağlantı noktaları kullanılabildiği sürece akış hemen başarılı olur.

Arka uç havuzu boyutu düşürüyorsa ve daha düşük bir katmana geçerse, kullanılabilir SNAT bağlantı noktalarının sayısı artar. Bu durumda, ayrılmış olan SNAT bağlantı noktaları ve ilgili akışları etkilenmez.

SNAT bağlantı noktaları ayırmaları, IP aktarım protokolüne özgüdür (TCP ve UDP ayrı tutulur) ve aşağıdaki koşullarda yayımlanır:

### <a name="tcp-snat-port-release"></a>TCP SNAT bağlantı noktası sürümü

- Sunucu/istemci FINACK gönderirse, SNAT bağlantı noktası 240 saniye sonra serbest bırakılır.
- Bir RST görülüyorsa, SNAT bağlantı noktası 15 saniye sonra serbest bırakılır.
- Boşta kalma zaman aşımına ulaşıldığında, bağlantı noktası serbest bırakılır.

### <a name="udp-snat-port-release"></a>UDP SNAT bağlantı noktası sürümü

- Boşta kalma zaman aşımına ulaşıldığında, bağlantı noktası serbest bırakılır.

## <a name="problemsolving"></a>Sorun çözme 

Bu bölüm, SNAT tükenmesi azaltmaya ve Azure 'daki giden bağlantılarla meydana getirmenize yardımcı olmaya yöneliktir.

### <a name="snatexhaust"></a>SNAT (PAT) bağlantı noktası tükenmesi yönetimi
[Pat](#pat) Için kullanılan [kısa ömürlü bağlantı noktaları](#preallocatedports) , genel IP [adresi olmayan tek BAŞıNA VM](#defaultsnat) 'de ve [genel IP adresi olmayan yük dengeli VM](#lb)'de açıklandığı gibi, tüketilmeyen bir kaynaktır.

Aynı hedef IP adresine ve bağlantı noktasına giden çok sayıda giden TCP veya UDP bağlantısı başlattığın ve başarısız olmuş bağlantıları gözlemlerseniz veya SNAT bağlantı noktalarını (önceden ayrılan [kısa ömürlü bağlantı noktaları](#preallocatedports) ) tahmin ettiğiniz destekle karşılaşdığınızı bilirsiniz [Pat](#pat)tarafından kullanılan), çeşitli genel risk azaltma seçenekleriniz vardır. Bu seçenekleri gözden geçirin ve senaryonuz için nelerin kullanılabilir ve en iyisi olduğuna karar verin. Bir veya daha fazla bu senaryonun yönetilmesine yardımcı olabilir.

Giden bağlantı davranışını anlamakta sorun yaşıyorsanız, IP yığın istatistiklerini (netstat) kullanabilirsiniz. Ya da paket yakalamaları kullanılarak bağlantı davranışlarını gözlemlemek faydalı olabilir. Bu paket yakalamalarını örneğinizin Konuk işletim sisteminde gerçekleştirebilir veya [paket yakalama Için Ağ İzleyicisi](../network-watcher/network-watcher-packet-capture-manage-portal.md)kullanabilirsiniz.

#### <a name="connectionreuse"></a>Bağlantıyı yeniden kullanmak için uygulamayı değiştirme 
Uygulamanızdaki bağlantıları yeniden kullandığınızda, SNAT için kullanılan kısa ömürlü bağlantı noktaları için talebi azaltabilirsiniz. Bu, özellikle HTTP/1.1 gibi protokoller için geçerlidir; burada bağlantı yeniden kullanım varsayılandır. Ve taşıma olarak HTTP kullanan diğer protokoller (örneğin, REST) sırasıyla faydalanabilir. 

Her istek için bireysel, atomik TCP bağlantılarından yeniden kullanım her zaman daha iyidir. Sonuçları daha performanslı, çok verimli bir TCP işlemi ile yeniden kullanın.

#### <a name="connection pooling"></a>Uygulamayı bağlantı havuzunu kullanacak şekilde değiştirme
Uygulamanızda, isteklerin dahili bir bağlantı kümesi arasında dahili olarak dağıtıldığı (mümkün olduğunda yeniden kullanıldığı) bir bağlantı havuzu oluşturabilirsiniz. Bu düzen, kullanımda olan kısa ömürlü bağlantı noktalarının sayısını kısıtlar ve daha öngörülebilir bir ortam oluşturur. Bu düzen, bir işlem yanıtında tek bir bağlantı yapıldığında birden çok eş zamanlı işleme izin vererek isteklerin verimini de artırabilir.  

Bağlantı havuzu, uygulamanızı geliştirmek için kullandığınız çerçeve içinde veya uygulamanızın yapılandırma ayarlarını zaten içerebilir. Bağlantı havuzunu bağlantı yeniden kullanımı ile birleştirebilirsiniz. Birden çok istekleriniz daha sonra aynı hedef IP adresi ve bağlantı noktası için sabit, öngörülebilir bir bağlantı noktası sayısı kullanır. İstekler, gecikme ve kaynak kullanımını azaltan TCP işlemlerinin verimli kullanımından de yararlanır. UDP işlemleri de yararlı olabilir, çünkü UDP akışlarının sayısını yönetmek, koşulları tüketmekten kaçınmak ve SNAT bağlantı noktası kullanımını yönetmektir.

#### <a name="retry logic"></a>Uygulamayı daha az agresif yeniden deneme mantığını kullanacak şekilde değiştirin
[Pat](#pat) için kullanılan [kısa ömürlü bağlantı noktaları](#preallocatedports) tükendiğinde ya da uygulama hatalarından oluşması durumunda, Decay ve geri dönüş mantığı olmadan, agresif veya deneme yanılma denemesi kesintileri meydana gelir veya kalıcı hale getirme. Daha az bir agresif yeniden deneme mantığı kullanarak, kısa ömürlü bağlantı noktaları için talebi azaltabilirsiniz. 

Kısa ömürlü bağlantı noktalarında 4 dakikalık boşta kalma zaman aşımı (ayarlanamaz) vardır. Yeniden denemeler çok ısrarlı ise, tükenmenin kendi kendine temizleme olanağı yoktur. Bu nedenle, uygulamanızın yeniden deneme işlemleri, tasarımın kritik bir parçasıdır.

#### <a name="assignilpip"></a>Her VM 'ye genel IP atama
Genel IP adresi atamak, senaryonuzu [BIR VM 'ye genel IP](#ilpip)'ye dönüştürür. Her VM için kullanılan genel IP 'nin tüm kısa ömürlü bağlantı noktaları, sanal makine için kullanılabilir. (Genel bir IP 'nin kısa ömürlü bağlantı noktaları, ilgili arka uç havuzuyla ilişkili tüm VM 'Ler ile paylaşıldığından senaryolar aksine.) Genel IP adreslerinin ek maliyeti ve çok sayıda ayrı IP adresini daha beyaz listeleyen olası etkileri gibi göz önünde bulundurmanız gereken bir denge vardır.

>[!NOTE] 
>Bu seçenek Web çalışanı rolleri için kullanılamaz.

#### <a name="multifesnat"></a>Birden çok ön uç kullan

Ortak Standart Load Balancer kullanırken, [giden bağlantılar için birden çok ön uç IP adresi](#multife) atar ve [mevcut SNAT bağlantı noktası sayısını çarpın](#preallocatedports).  SNAT 'nin, ön uç 'nin genel IP 'si için bir ön uç IP yapılandırması, kuralı ve arka uç havuzu oluşturun.  Kuralın çalışması gerekmez ve sistem durumu araştırmasının başarılı olması gerekmez.  Gelen (yalnızca giden) için birden çok ön uç kullanırsanız, güvenilirliği sağlamak için özel sistem durumu araştırmalarını de kullanmanız gerekir.

>[!NOTE]
>Çoğu durumda, SNAT bağlantı noktalarının tükenmesi hatalı tasarımın bir imzadır.  SNAT bağlantı noktalarını eklemek için daha fazla ön ek kullanarak bağlantı noktalarını neden tüketdiğinizi anladığınızdan emin olun.  Daha sonra hataya neden olabilecek bir sorunu maskeleyerek bir sorun olabilir.

#### <a name="scaleout"></a>Ölçeği genişletme

[Önceden ayrılmış bağlantı noktaları](#preallocatedports) , arka uç havuzu boyutu temel alınarak atanır ve bir sonraki daha büyük arka uç havuzu boyut katmanına uyum sağlamak için bağlantı noktalarından bazılarının yeniden ayrılması gerektiğinde kesintiyi en aza indirmek için katmanlara gruplanırlar.  Arka uç havuzunuzu belirli bir katman için en büyük boyuta ölçeklendirerek, belirli bir ön uç için SNAT bağlantı noktası kullanımının yoğunluğunu artırma seçeneğiniz olabilir.  Bu, uygulamanın etkili bir şekilde ölçeğini gerektirir.

Örneğin, arka uç havuzundaki iki sanal makinede, IP yapılandırması başına 1024 SNAT bağlantı noktası bulunabilir ve dağıtım için toplam 2048 SNAT bağlantı noktasına izin verilir.  Dağıtımın 50 sanal makinelere yükselymiş olması halinde, önceden ayrılan bağlantı noktalarının sayısı sanal makine başına sabit kalırsa bile, dağıtım tarafından toplam 51.200 (50 x 1024) SNAT bağlantı noktası kullanılabilir.  Dağıtımınızın ölçeğini genişletmek istiyorsanız katman başına [önceden ayrılmış bağlantı noktası](#preallocatedports) sayısını denetleyerek ilgili katman için en yüksek ölçeğe göre şekillerinizi şekillendirdiğinizden emin olun.  Yukarıdaki örnekte, 50 örnek yerine 51 ' e ölçeklendirmeye seçtiyseniz, bir sonraki katmana ilerleyerek VM başına daha az SNAT bağlantı noktası ve toplam olarak da sona erdir olur.

Bir sonraki daha büyük arka uç havuzu boyut katmanına ölçeklendirirseniz, ayrılan bağlantı noktalarının yeniden ayrılması durumunda giden bağlantılarınızın bazılarının zaman aşımına uğrar.  Yalnızca SNAT bağlantı noktalarından bazılarını kullanıyorsanız, sonraki daha büyük arka uç havuzu boyutunun ölçeğini ölçeklendirilmeye hazır değildir.  Sonraki arka uç havuz katmanına her geçtiğinizde, mevcut bağlantı noktalarının yarısı yeniden tahsis edilir.  Bunun gerçekleşmesini istemiyorsanız, dağıtımınızı katman boyutuna şekillendirmeniz gerekir.  Ya da uygulamanızın gerektiği şekilde algılayıp yeniden denenebildiğini doğrulayın.  TCP keepcanlı tutma, SNAT bağlantı noktalarının yeniden ayrılması nedeniyle artık işlev olmadığını algılamaya yardımcı olabilir.

### <a name="idletimeout"></a>Giden boşta kalma zaman aşımını sıfırlamak için keepcanlı tutmayı kullanın

Giden bağlantılarda 4 dakikalık bir boşta kalma zaman aşımı vardır. Bu zaman aşımı ayarlanamaz. Bununla birlikte, boş bir akışı yenilemek ve gerekirse bu boşta kalma zaman aşımını sıfırlamak için taşıma (örneğin, TCP keepcanlı tutma) veya uygulama katmanı keepcanlı tutma kullanabilirsiniz.  

TCP keepcanlı hale geldiğinde, bağlantının tek tarafında etkinleştirilmesi yeterlidir. Örneğin, bunları sunucu tarafında etkinleştirmek yeterlidir ve akışın boşta kalma zamanlayıcısını sıfırlayın ve her iki taraf da her iki tarafın da TCP keepcanlı hale getirilir olması gerekmez.  Veritabanı istemci-sunucu yapılandırmalarına dahil olmak üzere uygulama katmanı için benzer kavramlar vardır.  Uygulamaya özgü keepcanlı tutma için hangi seçeneklerin mevcut olduğunu kontrol edin.

## <a name="discoveroutbound"></a>Bir VM 'nin kullandığı genel IP 'yi keşfetme
Giden bir bağlantının genel kaynak IP adresini belirlemenin birçok yolu vardır. OpenDNS, sanal makinenizin genel IP adresini gösterebilmeniz için bir hizmet sağlar. 

Nslookup komutunu kullanarak, myip.opendns.com adı için OpenDNS çözümleyiciye bir DNS sorgusu gönderebilirsiniz. Hizmet, sorguyu göndermek için kullanılan kaynak IP adresini döndürür. Aşağıdaki sorguyu sanal makinenizde çalıştırdığınızda, yanıt o VM için kullanılan genel IP 'dir:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Giden bağlantıyı önlemek
Bazen bir VM 'nin giden akış oluşturmasına izin verilmesi istenmeyen bir hale gelir. Ya da giden akışlarla hangi hedeflere ulaşılamayabileceğini veya hangi hedeflerin gelen akışlara başlayabileceğini yönetmek için bir gereksinim olabilir. Bu durumda, VM 'nin ulaşabileceği hedefleri yönetmek için [ağ güvenlik gruplarını](../virtual-network/security-overview.md) kullanabilirsiniz. Ayrıca, NSG 'leri kullanarak hangi ortak hedefin gelen akışları başlatabileceğini yönetebilirsiniz.

Yük dengeli bir VM 'ye NSG uyguladığınızda, [hizmet etiketlerine](../virtual-network/security-overview.md#service-tags) ve [varsayılan güvenlik kurallarına](../virtual-network/security-overview.md#default-security-rules)dikkat edin. VM 'nin Azure Load Balancer durum araştırma isteklerini alabilmeniz gerekir. 

Bir NSG, AZURE_LOADBALANCER varsayılan etiketinden durum araştırma isteklerini engelliyorsa, sanal makine sistem durumu araştırmanız başarısız olur ve VM aşağı işaretlenir. Load Balancer, bu VM 'ye yeni akış göndermeyi durduruyor.

## <a name="limitations"></a>Sınırlamalar
- DisableOutboundSnat, portalda bir yük dengeleme kuralı yapılandırılırken bir seçenek olarak kullanılamaz.  Bunun yerine REST, şablon veya istemci araçları kullanın.
- VNet ve diğer Microsoft Platformu Hizmetleri olmayan Web çalışanı rolleri, ön VNet Hizmetleri ve diğer platform hizmetleri işlevinin yan etkisi nedeniyle yalnızca bir iç Standart Load Balancer kullanıldığında erişilebilir. İlgili hizmetin kendisi veya temel alınan platform hiçbir bildirimde bulunmaksızın değişmeksizin, bu yan etkiye güvenmeyin. Yalnızca dahili Standart Load Balancer kullandığınızda istenirse, açıkça giden bağlantı oluşturmanız gerektiğini varsaymanız gerekir. Bu makalede açıklanan [varsayılan SNAT](#defaultsnat) senaryosu 3 kullanılabilir değildir.

## <a name="next-steps"></a>Sonraki adımlar

- [Standart Yük Dengeleyici](load-balancer-standard-overview.md) hakkında daha fazla bilgi edinin.
- Standart genel Load Balancer [giden kuralları](load-balancer-outbound-rules-overview.md) hakkında daha fazla bilgi edinin.
- [Load Balancer](load-balancer-overview.md)hakkında daha fazla bilgi edinin.
- [Ağ güvenlik grupları](../virtual-network/security-overview.md)hakkında daha fazla bilgi edinin.
- Azure 'daki diğer bazı anahtar [ağ özellikleri](../networking/networking-overview.md) hakkında bilgi edinin.
