---
title: NAT ağ geçidi kaynaklarıyla sanal ağlar tasarlama
titleSuffix: Azure Virtual Network NAT
description: NAT ağ geçidi kaynaklarıyla sanal ağların nasıl tasarlanacağını öğrenin.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: 68f6d28612d0ce57901ded89c4ae5aecd43a33e4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055935"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>NAT ağ geçidi kaynaklarıyla sanal ağlar tasarlama

NAT ağ geçidi kaynakları, [sanal ağ NAT](nat-overview.md) 'nin bir parçasıdır ve bir sanal ağın bir veya daha fazla alt ağı Için giden Internet bağlantısı sağlar. Sanal ağ alt ağı hangi NAT ağ geçidinin kullanılacağını belirtir. NAT, bir alt ağ için kaynak ağ adresi çevirisi (SNAT) sağlar.  NAT ağ geçidi kaynakları, dış akışlar oluştururken sanal makinelerin hangi statik IP adresleri tarafından kullanılacağını belirtir. Statik IP adresleri, genel IP adresi kaynaklarından (PıP), genel IP öneki kaynaklarından veya her ikisiyle gelir. Genel IP öneki kaynağı kullanılıyorsa, tüm genel IP öneki kaynağının tüm IP adresleri bir NAT ağ geçidi kaynağı tarafından kullanılır. Bir NAT ağ geçidi kaynağı, her birinden en fazla 16 statik IP adresi kullanabilir.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Internet 'e giden sanal ağ NAT">
</p>

*Şekil: Internet 'e giden sanal ağ NAT*

## <a name="how-to-deploy-nat"></a>NAT dağıtma

NAT ağ geçidini yapılandırma ve kullanma özellikle basit hale getirilir:  

NAT ağ geçidi kaynağı:
- Bölgesel veya bölgesel (bölge yalıtılmış) NAT ağ geçidi kaynağı oluşturma,
- IP adresleri ata,
- Gerekirse, TCP boşta kalma zaman aşımını değiştirin (isteğe bağlı).  Varsayılan değişikliği <ins>yapmadan önce</ins> [zamanlayıcıları](#timers) gözden geçirin.

Sanal ağ:
- Sanal ağ alt ağını bir NAT ağ geçidi kullanacak şekilde yapılandırın.

Kullanıcı tanımlı yollar gerekli değildir.

## <a name="resource"></a>Kaynak

Kaynak, şablon benzeri bir biçimde aşağıdaki Azure Resource Manager örneğinde görebileceğiniz kadar basit olacak şekilde tasarlanmıştır.  Bu şablon benzeri biçim, kavramları ve yapıyı göstermek için burada gösterilmiştir.  Gereksinimlerinize göre örneği değiştirin.  Bu belge bir öğretici olarak tasarlanmamıştır.

Aşağıdaki diyagramda farklı Azure Resource Manager kaynakları arasındaki yazılabilir başvurular gösterilmektedir.  Ok, başvurunun yazılabilir olduğu yerden kaynaklanan yönünün yönünü gösterir. Gözden geçirme 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP and an IP prefix." width="256" title="Sanal ağ NAT nesne modeli">
</p>

*Şekil: sanal ağ NAT nesne modeli*

[Havuz tabanlı Load Balancer giden bağlantı](../load-balancer/load-balancer-outbound-connections.md)üzerinde belirli bir bağımlılığı yoksa, NAT çoğu iş yükü için önerilir.  

[Giden kuralları](../load-balancer/load-balancer-outbound-connections.md#outboundrules)da dahil olmak üzere standart yük dengeleyici SENARYOLARıNDAN NAT ağ geçidine geçiş yapabilirsiniz. Geçirmek için genel IP ve genel IP öneki kaynaklarını yük dengeleyici ön uçları ' ndan NAT ağ geçidine taşıyın. NAT Gateway için yeni IP adresleri gerekli değildir. Standart genel IP adresi kaynakları ve genel IP öneki kaynağı, toplam 16 IP adresini aşmadığı sürece yeniden kullanılabilir. Geçiş sırasında göz önünde bulundurarak hizmet kesintisini içeren geçiş planlayın.  İşlemi otomatikleştirerek kesintiye uğramasını küçültebilirsiniz. Geçişi önce hazırlama ortamında test edin.  Geçiş sırasında gelen kaynaklı akışlar etkilenmez.


Aşağıdaki örnek, bir Azure Resource Manager şablonundan bir kod parçacığı örneğidir.  Bu şablon, bir NAT ağ geçidi dahil olmak üzere birkaç kaynak dağıtır.  Şablon Bu örnekte aşağıdaki parametrelere sahiptir:

- **natgatewayname** -NAT ağ geçidinin adı.
- **konum** -kaynağın bulunduğu Azure bölgesi.
- **publicıpname** -NAT ağ geçidiyle ilişkili gıden genel IP 'nin adı.
- **vağadı** -sanal ağın adı.
- **subnetName** -NAT ağ geçidiyle ilişkili alt ağın adı.

Tüm IP adresleri ve önek kaynakları tarafından belirtilen IP adreslerinin toplam sayısı 16 IP adresi toplamı aşamaz. 1 ve 16 arasında herhangi bir sayıda IP adresine izin verilir.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

NAT ağ geçidi kaynağı oluşturulduğunda, sanal ağın bir veya daha fazla alt ağında kullanılabilir. Hangi alt ağların bu NAT ağ geçidi kaynağını kullanmasını belirtin. Bir NAT ağ geçidi birden fazla sanal ağa yayılamaz. Aynı NAT ağ geçidini bir sanal ağın tüm alt ağlarına atamak gerekli değildir. Ayrı alt ağlar, farklı NAT ağ geçidi kaynaklarıyla yapılandırılabilir.

Kullanılabilirlik bölgelerini kullanmayan senaryolar bölgesel (bölge belirtilmemiş) olacaktır. Kullanılabilirlik alanları kullanıyorsanız, NAT 'yi belirli bir bölgeye yalıtmak için bir bölge belirtebilirsiniz. Bölge yedekliliği desteklenmez. NAT [kullanılabilirlik bölgelerini](#availability-zones)gözden geçirin.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

NAT ağ geçitleri, bir sanal ağ içindeki bir alt ağda bulunan bir özellik ile tanımlanır. Sanal makineler tarafından oluşturulan akışların alt ağ **subnetName** sanal ağ sanal ağ adı, NAT ağ **geçidini kullanacaktır.** Tüm giden bağlantılar, kaynak IP adresi olarak **natgatewayname** Ile ilişkili IP adreslerini kullanır.

Bu örnekte kullanılan Azure Resource Manager şablonu hakkında daha fazla bilgi için bkz.:

- [Hızlı başlangıç: NAT Gateway-Kaynak Yöneticisi şablonu oluşturma](quickstart-create-nat-gateway-template.md)
- [Sanal ağ NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Tasarım Kılavuzu

NAT ile sanal ağların tasarlanmasıyla ilgili hususları öğrenmek için bu bölümü gözden geçirin.  

1. [Maliyet iyileştirmesi](#cost-optimization)
1. [Gelen ve giden birlikte bulunma](#coexistence-of-inbound-and-outbound)
2. [Temel kaynakları yönetme](#managing-basic-resources)
3. [Kullanılabilirlik Alanları](#availability-zones)

### <a name="cost-optimization"></a>Maliyet iyileştirmesi

[Hizmet uç noktaları](virtual-network-service-endpoints-overview.md) ve [özel bağlantı](../private-link/private-link-overview.md) , maliyeti iyileştirmek için göz önünde bulundurulması gereken seçeneklerdir. Bu hizmetler için NAT gerekli değildir. Hizmet uç noktalarına veya özel bağlantıya yöneltilen trafik sanal ağın NAT tarafından işlenmez.  

Hizmet uç noktaları, Azure hizmet kaynaklarını sanal ağınıza bağlayabilir ve Azure hizmet kaynaklarınıza erişimi denetler. Örneğin, Azure depolama 'ya eriştiğinizde, veri işleme NAT ücretlerinden kaçınmak için depolama için bir hizmet uç noktası kullanın. Hizmet uç noktaları ücretsizdir.

Özel bağlantı, Azure PaaS hizmetini (veya özel bağlantı ile barındırılan diğer hizmetleri) bir sanal ağ içindeki özel bir uç nokta olarak kullanıma sunar.  Özel bağlantı, işlenen süreye ve verilere göre faturalandırılır.

Bu yaklaşımlardan birinin veya her ikisinin senaryonuz için uygun olup olmadığını değerlendirin ve gerektiğinde kullanın.

### <a name="coexistence-of-inbound-and-outbound"></a>Gelen ve giden birlikte bulunma

NAT ağ geçidi ile uyumludur:

 - Standart yük dengeleyici
 - Standart genel IP
 - Standart genel IP öneki

Yeni bir dağıtım geliştirilirken standart SKU 'Lar ile başlayın.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network." width="256" title="Internet 'e giden sanal ağ NAT">
</p>

*Şekil: Internet 'e giden sanal ağ NAT*

NAT ağ geçidi tarafından sunulan yalnızca Internet giden senaryosu Internet 'ten gelen bir işlevle genişletilebilir. Her kaynak, akışın kaynaklandığı yönü algılar. NAT ağ geçidine sahip bir alt ağda, Internet 'Ten gelen tüm senaryolar, NAT ağ geçidi tarafından yenisiyle değiştirilmiştir. Internet senaryolarından gelen giriş, ilgili kaynak tarafından sağlanır.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>Örnek düzeyi genel IP ile NAT ve VM

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Örnek düzeyi genel IP 'si olan sanal ağ NAT ve VM">
</p>

*Şekil: sanal ağ NAT ve örnek düzeyi genel IP 'si olan VM*

| Yön | Kaynak |
|:---:|:---:|
| Gelen | Örnek düzeyi genel IP 'si olan VM |
| Giden | NAT Gateway |

VM, giden için NAT Gateway kullanır.  Gelen kaynaklı etkilenmemektedir.

#### <a name="nat-and-vm-with-public-load-balancer"></a>Ortak Load Balancer sahip NAT ve VM

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Sanal ağ NAT ve ortak Load Balancer VM">
</p>

*Şekil: ortak Load Balancer sanal ağ NAT ve VM*

| Yön | Kaynak |
|:---:|:---:|
| Gelen | ortak Load Balancer |
| Giden | NAT Gateway |

Yük Dengeleme kuralı veya giden kuralların herhangi bir giden yapılandırmasının yerini NAT ağ geçidi almıştır.  Gelen kaynaklı etkilenmemektedir.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>Örnek düzeyi genel IP ve ortak Load Balancer sahip NAT ve VM

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Örnek düzeyi genel IP ve ortak Load Balancer sanal ağ NAT ve VM">
</p>

*Şekil: örnek düzeyinde genel IP ve genel Load Balancer sanal ağ NAT ve VM*

| Yön | Kaynak |
|:---:|:---:|
| Gelen | Örnek düzeyi genel IP ve genel Load Balancer VM |
| Giden | NAT Gateway |

Yük Dengeleme kuralı veya giden kuralların herhangi bir giden yapılandırmasının yerini NAT ağ geçidi almıştır.  VM, giden için NAT ağ geçidini de kullanacaktır.  Gelen kaynaklı etkilenmemektedir.

### <a name="managing-basic-resources"></a>Temel kaynakları yönetme

Standart yük dengeleyici, genel IP ve genel IP ön eki, NAT ağ geçidiyle uyumludur. NAT ağ geçitleri bir alt ağın kapsamında çalışır. Bu hizmetlerin temel SKU 'SU bir NAT ağ geçidi olmadan bir alt ağda dağıtılmalıdır. Bu ayrım, her iki SKU çeşitlemesinin de aynı sanal ağda birlikte kullanılmasına izin verir.

NAT ağ geçitleri, alt ağın giden senaryolarından önceliklidir. Temel yük dengeleyici veya genel IP (ve ile derlenen herhangi bir yönetilen hizmet) doğru çeviriler ile ayarlanamaz. NAT ağ geçidi, bir alt ağdaki Internet trafiğine giden denetimi alır. Temel yük dengeleyiciye ve genel IP 'ye gelen trafik kullanılamıyor. Temel yük dengeleyiciye gelen trafik ve veya bir VM 'de yapılandırılmış genel IP kullanılamaz.

### <a name="availability-zones"></a>Kullanılabilirlik Alanları

#### <a name="zone-isolation-with-zonal-stacks"></a>Bölgesel yığınları ile bölge yalıtımı

<p align="center">
  <img src="media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="425" title="Bölge yalıtımıyla sanal ağ NAT, birden çok oluşturma "zonal stacks"">
</p>

*Şekil: bölge yalıtımına sahip sanal ağ NAT, birden çok "zonal yığınları" oluşturma*

Kullanılabilirlik alanları olmadan bile NAT esnektir ve birden çok altyapı bileşeni hatasını devam edebilir.  Kullanılabilirlik alanları, NAT için bölge yalıtımı senaryolarıyla bu esnekliği oluşturur.

Sanal ağlar ve alt ağları bölgesel yapılardır.  Alt ağlar bir bölgeyle sınırlı değildir.

Bir NAT ağ geçidi kaynağı kullanan bir sanal makine örneği, NAT ağ geçidi kaynağı ve genel IP adresleriyle aynı bölgede olduğunda bölge yalıtımı için bir bölgesel Promise vardır. Bölge yalıtımı için kullanmak istediğiniz model, kullanılabilirlik alanı başına bir "zonal Stack" oluşturuyor.  Bu "zeli Stack" sanal makine örneklerinden, NAT ağ geçidi kaynaklarından, genel IP adresi ve/veya yalnızca aynı bölgeye hizmet veren bir alt ağda yer alan önek kaynaklarından oluşur.   Denetim düzlemi işlemleri ve veri düzlemi, belirtilen bölge ile hizalanır ve kısıtlanır. 

Senaryonuzun olması dışında bir bölgede hata olması beklenen, NAT 'nin etkisi olmadan olması beklenir. Aynı bölgedeki sanal makinelerden giden trafik, bölge yalıtımı nedeniyle başarısız olur.  

#### <a name="integrating-inbound-endpoints"></a>Gelen uç noktaları tümleştirme

Senaryonuz gelen uç noktalar gerektiriyorsa iki seçeneğiniz vardır:

| Seçenek | Desen | Örnek | Pro | Con |
|---|---|---|---|---|
| (1) | Gelen uç noktaları, giden için oluşturmakta olduğunuz ilgili **Bölgesel yığınlarıyla** **hizalayın** . | Bölgesel ön ucu ile standart yük dengeleyici oluşturun. | Gelen ve giden için aynı durum modeli ve hata modu. Daha basit çalışır. | Her bölge için ayrı IP adreslerinin ortak bir DNS adıyla maskelenme gerekebilir. |
| (2) | Bir **çapraz bölge** gelen bitiş noktasıyla bölgesel yığınlarını birlikte **kaplaın** . | Bölgesel olarak yedekli ön uç ile standart yük dengeleyici oluşturun. | Gelen uç noktası için tek IP adresi. | Gelen ve giden için farklı durum modeli ve hata modları.  Çalışması daha karmaşıktır. |

>[!NOTE]
> Bölge yalıtılmış bir NAT ağ geçidi, IP adreslerinin NAT ağ geçidinin bölgesiyle eşleşmesini gerektirir. Farklı bir bölgeden veya bölge olmadan IP adreslerine sahip NAT ağ geçidi kaynaklarına izin verilmez.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Bölgeler arası giden senaryolar desteklenmez

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet, with the connections between to of the gateways and their subnets broken." width="425" title="Sanal ağ NAT, bölge ile yayılan alt ağ ile uyumlu değil">
</p>

*Şekil: sanal ağ NAT, bölge ile yayılan alt ağ ile uyumlu değil*

Sanal makine örnekleri aynı alt ağ içindeki birden çok bölgeye dağıtıldığında, NAT ağ geçidi kaynaklarıyla bir bölgesel taahhüdüne ulaşamıyoruz.   Bir alt ağa eklenmiş birden çok sayıda NAT ağ geçidi bulunsa bile, sanal makine örneği hangi NAT ağ geçidi kaynağını seçeceğimizi bilmez.

Bir sanal makine örneği ve bir IP 'nin bir NAT ağ geçidinin bölgesi hizalanmamış veya b), bir bölgesel NAT ağ geçidi kaynağı, zeli sanal makine örnekleri ile birlikte kullanılır.

Senaryo çalışır durumda gözükirken, sistem durumu modeli ve hata modu, bir kullanılabilirlik bölgesi bakış noktasından tanımlanmamıştır. Bunun yerine bölgesel yığınları veya tüm bölge ile çalışmaya devam edin.

>[!NOTE]
>Bir NAT ağ geçidi kaynağının Zones özelliği değişebilir değildir.  NAT ağ geçidi kaynağını amaçlanan bölge veya bölge tercihi ile yeniden dağıtın.

>[!NOTE] 
>Hiçbir bölge belirtilmemişse, kendilerine göre IP adresleri bölge yedekli değildir.  Belirli bir bölgede bir IP adresi oluşturulmadıysa, bir Standart Load Balancer ön ucu [bölge yedekli olur](../load-balancer/load-balancer-standard-availability-zones.md) .  Bu, NAT için uygulanmaz.  Yalnızca bölge veya bölge yalıtımı desteklenir.

## <a name="performance"></a>Performans

Her NAT ağ geçidi kaynağı 50 GB 'a kadar aktarım hızı sağlayabilir. Dağıtımlarınızı birden çok alt ağa bölebilir ve genişleme için bir NAT ağ geçidi olan her bir alt ağı veya alt ağ grubunu atayabilirsiniz.

Her NAT ağ geçidi, atanan giden IP adresi başına sırasıyla TCP ve UDP için 64.000 akışı destekleyebilir.  Ayrıntılar ve belirli sorun çözümleme Kılavuzu için [sorun giderme makalesi](./troubleshoot-nat.md) Için kaynak ağ adresi ÇEVIRISI (SNAT) üzerinde aşağıdaki bölümü gözden geçirin.

## <a name="source-network-address-translation"></a>Kaynak ağ adresi çevirisi

Kaynak ağ adresi çevirisi (SNAT), bir akışın kaynağını farklı bir IP adresinden kaynaklanan bir şekilde yeniden yazar.  NAT ağ geçidi kaynakları, bağlantı noktası adresi çevirisi (PAT) ile yaygın olarak anılan bir, SNAT değişkenini kullanır. PAT kaynak adresi ve kaynak bağlantı noktasını yeniden yazar. SNAT ile, özel adres sayısı ve bunların çevrilmiş ortak adresleri arasında sabit bir ilişki yoktur.  

### <a name="fundamentals"></a>Temel Bilgiler

Temel kavramı açıklamak için dört akışla bir örneğe bakalım.  NAT ağ geçidi, genel IP adresi kaynağı 65.52.1.1 kullanıyor ve VM, 65.52.0.1 ile bağlantı yapıyor.

| Akış | Kaynak kayıt düzeni | Hedef kayıt düzeni |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Bu akışlar, PAT olduktan sonra şöyle görünebilir:

| Akış | Kaynak kayıt düzeni | Snattoed kaynak kayıt düzeni | Hedef kayıt düzeni | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

Hedef, belirtilen atanmış bağlantı noktası ile Flow kaynağını 65.52.0.1 (SNAT kaynak kayıt düzeni) olarak görür.  Yukarıdaki tabloda gösterildiği gibi PAT, bağlantı noktası aşağı olarak da adlandırılır.  Birden çok özel kaynak bir IP ve bağlantı noktasının arkasında bir şekilde kendini temel alınır.  

#### <a name="source-snat-port-reuse"></a>Kaynak (SNAT) bağlantı noktası yeniden kullanımı

NAT ağ geçitleri mümkün olduğunda yeniden kullanım kaynağı (SNAT) bağlantı noktaları.  Aşağıda, bu kavram önceki akış kümesi için ek bir akış olarak gösterilmiştir.  Örnekteki VM, 65.52.0.2 ' e bir akışdır.

| Akış | Kaynak kayıt düzeni | Hedef kayıt düzeni |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Bir NAT ağ geçidi muhtemelen Flow 4 ' ü diğer hedefler için de kullanılabilecek bir bağlantı noktasına çevireceği anlamına gelebilir.  IP adresi hazırlığını doğru şekilde boyutlandırma hakkında daha fazla tartışma için bkz. [ölçeklendirme](#scaling) .

| Akış | Kaynak kayıt düzeni | Snattoed kaynak kayıt düzeni | Hedef kayıt düzeni | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

Yukarıdaki örnekte kaynak bağlantı noktalarının atanması için bir bağımlılık kullanmayın.  Yukarıdaki temel kavramın bir çizimidir.

NAT tarafından sunulan SNAT, çeşitli açılardan [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) farklıdır.

### <a name="on-demand"></a>İsteğe bağlı

NAT, yeni giden trafik akışları için isteğe bağlı SNAT bağlantı noktaları sağlar. Envanterdeki tüm kullanılabilir SNAT bağlantı noktaları, NAT ile yapılandırılmış alt ağlardaki tüm sanal makineler tarafından kullanılır. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Sanal ağ NAT istek üzerine giden SNAT">
</p>

*Şekil: sanal ağ NAT istek üzerine giden SNAT*

Bir sanal makinenin herhangi bir IP yapılandırması, gerektiğinde istek üzerine giden akışlar oluşturabilir.  Örnek başına en kötü büyük/küçük harf sağlama de dahil olmak üzere ön tahsisi, gerekli değildir.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Tükenme senaryolarında farklılıklar">
</p>

*Şekil: tükenme senaryolarında farklılıklar*

Bir SNAT bağlantı noktası yayımlandıktan sonra, NAT ile yapılandırılmış alt ağlardaki tüm sanal makineler tarafından kullanılabilir.  İsteğe bağlı ayırma, alt ağdaki dinamik ve daha fazla iş yükünün, gereken SNAT bağlantı noktalarını kullanmasına izin verir.  SNAT bağlantı noktası envanteri kullanılabilir olduğu sürece, SNAT akışları başarılı olur. SNAT bağlantı noktası etkin noktaları, bunun yerine daha büyük envanterden faydalanır. SNAT bağlantı noktaları, etkin bir şekilde ihtiyaç duymayan sanal makineler için kullanılmıyor olarak kalmadı.

### <a name="scaling"></a>Ölçeklendirme

NAT ölçeklendirme, birincil olarak paylaşılan, kullanılabilir SNAT bağlantı noktası envanterini yönetme işlevinden oluşur. NAT, bir NAT ağ geçidi kaynağına bağlı tüm alt ağlar için beklenen yoğun çıkış akışları için yeterli SNAT bağlantı noktası envanterini istiyor.  SNAT bağlantı noktası envanterini oluşturmak için genel IP adresi kaynaklarını, genel IP öneki kaynaklarını veya her ikisini birden kullanabilirsiniz.  

>[!NOTE]
>Genel IP öneki kaynağı atadıysanız, genel IP ön ekinin tamamı kullanılacaktır.  Genel bir IP öneki kaynağı atayamazsınız ve sonra diğer kaynaklara atanacak ayrı IP adreslerini kesebilirsiniz.  Genel bir IP önekinden birden çok kaynağa tek tek IP adresleri atamak istiyorsanız, genel IP öneki kaynağından tek tek genel IP adresleri oluşturmanız ve bunları genel IP öneki kaynağının yerine gerektiği gibi atamanız gerekir.

SNAT, özel adresleri bir veya daha fazla genel IP adresi, süreçteki kaynak adresi ve kaynak bağlantı noktasını yeniden yazarak eşleştirir. Bir NAT ağ geçidi kaynağı, bu çeviri için yapılandırılmış genel IP adresi başına 64.000 bağlantı noktası (SNAT bağlantı noktaları) kullanır. NAT ağ geçidi kaynakları, 16 IP adresi ve 1M SNAT bağlantı noktası kadar ölçeklenebilir. Genel bir IP ön eki kaynağı sağlanmışsa, ön ek içindeki her IP adresi, SNAT bağlantı noktası envanterini sağlar. Ve daha fazla genel IP adresi eklemek, kullanılabilir envanter SNAT bağlantı noktalarını artırır. TCP ve UDP ayrı SNAT bağlantı noktası envanteridir ve ilgisiz değildir.

NAT ağ geçidi kaynakları mümkün olduğunda yeniden kullanım kaynağı (SNAT) bağlantı noktaları. Ölçeklendirme amaçlarıyla tasarım kılavuzu olarak, her akışın yeni bir SNAT bağlantı noktası gerektirdiğini ve giden trafik için kullanılabilir IP adreslerinin toplam sayısını ölçeklendirmelisiniz.  Tasarlamakta olduğunuz ölçeği dikkatle düşünmeniz ve IP adresleri miktarlarını uygun şekilde sağlamanız gerekir.

Farklı hedeflere yönelik SNAT bağlantı noktaları mümkün olduğunda yeniden kullanılabilir. SNAT bağlantı noktası tükenmesi yaklaşımının yanı sıra akışlar başarısız olabilir.  

Örneğin bkz. [SNAT temelleri](#source-network-address-translation) .


### <a name="protocols"></a>Protokoller

NAT ağ geçidi kaynakları, UDP ve TCP akışlarının IP ve IP taşıma üstbilgileri ile etkileşim kurar ve uygulama katmanı yükleri için belirsizdir.  Diğer IP protokolleri desteklenmez.

### <a name="timers"></a>Zamanlayıcılar

>[!IMPORTANT]
>Uzun boşta kalma süreölçeri, SNAT tükenmesi olasılığını gereksiz şekilde artırabilir. Belirttiğiniz bir zamanlayıcının daha uzun olması durumunda, daha uzun bir NAT, zaman aşımına uğraıncaya kadar SNAT bağlantı noktalarında oturum açacaktır. Akışlarınız boşta kalırsa, yine de başarısız olur ve SNAT bağlantı noktası envanterini gereksiz şekilde kullanır.  2 saat içinde başarısız olan akışlar da varsayılan 4 dakika içinde başarısız olur. Boşta kalma zaman aşımını artırmak, gelişigüzel bir şekilde kullanılması gereken son çare bir seçenektir. Bir akış hiçbir süre boşta kalırsa, boşta zamanlayıcının bundan etkilenmeyecektir.

TCP boşta kalma zaman aşımı, tüm akışlar için 4 dakikadan (varsayılan) 120 dakikaya (2 saat) ayarlanabilir.  Ayrıca, boşta kalma zamanlayıcısını akıştaki trafikle sıfırlayabilirsiniz.  Uzun boşta bağlantıları yenilemek için önerilen bir model ve uç nokta sahtekarlığı algılama, TCP keepcanlı hale getirilir.  TCP keepetkin noktalar, uç noktalara yinelenen bir ACK olarak görünür, düşük yüklerdir ve uygulama katmanında görünmez.

Aşağıdaki zamanlayıcılar, SNAT bağlantı noktası sürümü için kullanılır:

| Zamanlayıcı | Değer |
|---|---|
| TCP FIN | 60 saniye |
| TCP RST | 10 saniye |
| TCP yarı açık | 30 saniye |

Bir SNAT bağlantı noktası, 5 saniye sonra aynı hedef IP adresi ve hedef bağlantı noktası için yeniden kullanılabilir.

>[!NOTE] 
>Bu süreölçer ayarları değişebilir. Bu değerler, sorun gidermeye yardımcı olmak için sağlanır ve şu anda belirli zamanlayıcılara bağımlılık yapmanız gerekmez.

## <a name="limitations"></a>Sınırlamalar

- NAT, standart SKU genel IP 'si, genel IP öneki ve yük dengeleyici kaynaklarıyla uyumludur.   Temel kaynaklar (örneğin, temel yük dengeleyici) ve bunlardan türetilmiş tüm ürünler NAT ile uyumlu değildir.  Temel kaynakların NAT ile yapılandırılmamış bir alt ağa yerleştirilmesi gerekir.
- IPv4 adres ailesi destekleniyor.  NAT, IPv6 adres ailesi ile etkileşime girmez.  NAT, IPv6 ön ekine sahip bir alt ağa dağıtılamaz.
- NAT birden çok sanal ağa yayılamaz.
- IP parçalanması desteklenmiyor.

## <a name="suggestions"></a>Öneriler

Hizmeti nasıl geliştirebileceğimizi öğrenmek istiyoruz. Bir yetenek eksik mı? [NAT Için UserVoice](https://aka.ms/natuservoice)'ta daha sonra derleme yaptığımız bir durum oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

* [Sanal ağ NAT](nat-overview.md)hakkında bilgi edinin.
* [NAT ağ geçidi kaynakları için ölçümler ve uyarılar](nat-metrics.md)hakkında bilgi edinin.
* [NAT ağ geçidi kaynakları sorunlarını giderme](troubleshoot-nat.md)hakkında bilgi edinin.
