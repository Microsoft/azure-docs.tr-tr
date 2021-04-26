---
title: Azure 'da yüksek kullanılabilirliğe sahip bağlantı noktalarına genel bakış
titleSuffix: Azure Load Balancer
description: İç yük dengeleyicide yüksek kullanılabilirlik bağlantı noktaları Yük Dengelemesi hakkında bilgi edinin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 6f089af71e4d32023e9cebd6613872f7db0eed7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94694968"
---
# <a name="high-availability-ports-overview"></a>Yüksek kullanılabilirlik bağlantı noktalarına genel bakış

Azure Standart Load Balancer, HA bağlantı noktaları aracılığıyla iç Load Balancer kullanırken **Tüm** bağlantı noktalarında tüm protokol akışlarını aynı anda **Yük dengelemenize** yardımcı olur.

Yüksek kullanılabilirlik (HA) bağlantı noktaları, bir iç Standart Load Balancer **Tüm** bağlantı noktalarına ulaşan **Tüm** akışların yük dengelenmesi için kolay bir yol sağlayan bir yük dengeleme kuralı türüdür. Yük Dengeleme kararı akış başına yapılır. Bu eylem, şu beş demet bağlantısına dayanır: kaynak IP adresi, kaynak bağlantı noktası, hedef IP adresi, hedef bağlantı noktası ve protokol

HA bağlantı noktaları Yük Dengeleme kuralları, sanal ağların içindeki ağ sanal gereçleri (NVA 'lar) için yüksek kullanılabilirlik ve ölçek gibi kritik senaryolarda size yardımcı olur. Bu özellik, çok sayıda bağlantı noktasının yük dengeli olması gerektiğinde da yardımcı olabilir. 

HA bağlantı noktaları Yük Dengeleme kuralları, ön uç ve arka uç bağlantı noktalarını **0** ' a ve Protokolü **tümüne** ayarladığınızda yapılandırılır. İç yük dengeleyici kaynağı, bağlantı noktası numarasından bağımsız olarak tüm TCP ve UDP akışlarını dengeler

## <a name="why-use-ha-ports"></a>HA bağlantı noktaları neden kullanılmalıdır?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Ağ sanal cihazları

Azure iş yükünüzü birden çok güvenlik tehditlerine karşı korumaya yardımcı olmak için NVA 'lar kullanabilirsiniz. Bu senaryolarda NVA 'lar kullandığınızda, güvenilir ve yüksek oranda kullanılabilir olmaları ve isteğe bağlı olarak ölçeklendirilmesi gerekir.

Bu hedeflere, iç yük dengeleyicinizin arka uç havuzuna NVA örnekleri ekleyerek ve bir HA bağlantı noktaları yük dengeleyici kuralını yapılandırarak elde edebilirsiniz.

NVA HA senaryolarında, HA bağlantı noktaları aşağıdaki avantajları sunar:
- Sağlıklı örneklere hızlı yük devretme, örnek başına sistem durumu araştırmalarını sağlama
- *N* etkin örneklere genişleme ile daha yüksek performans sağlayın
- *N*-aktif ve aktif-pasif senaryolar sağlama
- İzleme gereçlerine yönelik Apache ZooKeeper düğümleri gibi karmaşık çözümlere gereksinimi ortadan kaldırın

Aşağıdaki diyagramda bir hub ve bağlı bileşen sanal ağ dağıtımı sunulmaktadır. Bağlı bileşenler, güvenilir alandan çıkmadan önce, trafiği hub sanal ağına ve NVA üzerinden tünele zorlar. NVA 'lar, bir HA bağlantı noktası yapılandırması olan bir iç Standart Load Balancer arkasında bulunur. Tüm trafik, uygun şekilde işlenebilir ve iletilebilir. Aşağıdaki diyagramda göster olarak yapılandırıldığında, bir HA bağlantı noktaları Yük Dengeleme kuralı ek olarak giriş ve çıkış trafiği için Flow simetri sağlar.

<a node="diagram"></a>
![NVA 'lar, HA modunda dağıtılan merkez ve bağlı sanal ağın diyagramı](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> NVA 'lar kullanıyorsanız, sağlayıcıları ile ilgili en iyi şekilde HA bağlantı noktalarını kullanma ve hangi senaryoların desteklendiğini öğrenme hakkında bilgi edinin.

### <a name="load-balancing-large-numbers-of-ports"></a>Yük Dengelemesi çok fazla sayıda bağlantı noktası

Ayrıca, çok sayıda bağlantı noktasının yük dengelenmesini gerektiren uygulamalar için HA bağlantı noktalarını kullanabilirsiniz. Bu senaryoları, HA bağlantı noktalarıyla bir iç [Standart Load Balancer](./load-balancer-overview.md) kullanarak basitleştirebilirsiniz. Tek bir yük dengeleme kuralı, her bağlantı noktası için bir tane olmak üzere birden çok ayrı Yük Dengeleme kuralını değiştirir.

## <a name="region-availability"></a>Bölge kullanılabilirliği

HA bağlantı noktaları özelliği tüm genel Azure bölgelerinde kullanılabilir.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Bir iç Standart Load Balancer, tek, kayan olmayan IP (doğrudan sunucu dönüşü) HA bağlantı noktaları yapılandırması

Bu yapılandırma, temel bir HA bağlantı noktaları yapılandırması. Aşağıdaki işlemleri gerçekleştirerek tek bir ön uç IP adresi üzerinde bir HA bağlantı noktaları Yük Dengeleme kuralı yapılandırabilirsiniz:
1. Standart Load Balancer yapılandırılırken, Load Balancer kuralı yapılandırmasındaki **ha bağlantı noktaları** onay kutusunu seçin.
2. **Kayan IP** Için **devre dışı**' yı seçin.

Bu yapılandırma, geçerli yük dengeleyici kaynağında başka bir yük dengeleme kuralı yapılandırmasına izin vermiyor. Ayrıca, belirtilen arka uç örnekleri kümesi için başka bir iç yük dengeleyici kaynak yapılandırmasına izin vermez.

Ancak, bu HA bağlantı noktaları kuralına ek olarak, arka uç örnekleri için genel Standart Load Balancer yapılandırabilirsiniz.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Bir iç Standart Load Balancer, tek bir kayan IP (doğrudan sunucu dönüşü) HA bağlantı noktaları yapılandırması

Benzer şekilde, **kayan IP** 'yi **etkin** olarak ayarlayarak, yük dengeleyicinizi tek bir ön uç ile **ha bağlantı noktasıyla** bir yük dengeleme kuralı kullanacak şekilde yapılandırabilirsiniz. 

Bu yapılandırmayı kullanarak, daha fazla kayan IP yük dengeleme kuralı ve/veya bir genel yük dengeleyici ekleyebilirsiniz. Ancak, bu yapılandırmanın en üstünde kayan olmayan IP, HA-Ports yük dengeleme yapılandırması kullanamazsınız.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>İç Standart Load Balancer birden çok HA-Port yapılandırması

Senaryonuz aynı arka uç havuzu için birden fazla HA bağlantı noktası ön ucu yapılandırmanızı gerektiriyorsa, şunları yapabilirsiniz: 
- Tek bir iç Standart Load Balancer kaynağı için birden fazla ön uç özel IP adresi yapılandırın.
- Her kuralın seçili tek bir ön uç IP adresi olduğu çoklu Yük Dengeleme kurallarını yapılandırın.
- **Ha bağlantı noktaları** seçeneğini belirleyin ve ardından tüm yük dengeleme kuralları IÇIN **kayan IP** 'yi **etkin** olarak ayarlayın.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Aynı arka uç örneği üzerinde HA bağlantı noktaları ve ortak yük dengeleyici olan bir iç yük dengeleyici

Arka uç kaynakları için *bir* genel standart Load Balancer KAYNAĞıNı, ha bağlantı noktalarıyla tek bir iç standart Load Balancer birlikte yapılandırabilirsiniz.

## <a name="limitations"></a>Sınırlamalar

- HA bağlantı noktaları Yük Dengeleme kuralları yalnızca iç Standart Load Balancer için kullanılabilir.
- Aynı arka uç IP yapılandırmasında bir HA bağlantı noktaları Yük Dengeleme kuralı ve HA olmayan bir bağlantı noktası yük dengeleme kuralı, her ikisinde de kayan IP etkin olmadığı **müddetçe, tek** bir ön uç IP yapılandırmasında desteklenmez.
- Mevcut IP parçaları, ilk paket ile aynı hedefe HA bağlantı noktaları Yük Dengeleme kuralları tarafından iletilir.  IP fragmenting bir UDP veya TCP paketi desteklenmez.
- Akış simetrileri (öncelikli olarak NVA senaryolarında), yalnızca Yukarıdaki diyagramda gösterildiği gibi kullanıldığında ve HA bağlantı noktaları Yük Dengeleme kuralları kullanıldığında, arka uç örneği ve tek bir NIC (ve tek IP yapılandırması) ile desteklenir. Başka hiçbir senaryoda sağlanmaz. Bu, iki veya daha fazla Load Balancer kaynağı ve ilgili kuralları bağımsız kararlar verirken ve hiçbir şekilde koordine etmediği anlamına gelir. Bkz. [ağ sanal cihazları](#nva)için açıklama ve diyagram. Birden çok NIC kullandığınızda veya bir ortak ve dahili Load Balancer arasında NVA 'yı kullanarak, Flow simetri kullanılamıyor.  Yanıtların aynı NVA 'ya ulaşmasına izin vermek için, giriş akışını gereç IP 'si ile çözmek için kaynak tarafından geçici bir çözüm bulabilirsiniz.  Ancak, tek bir NIC kullanmanız ve Yukarıdaki diyagramda gösterilen başvuru mimarisini kullanmanız önemle önerilir.


## <a name="next-steps"></a>Sonraki adımlar

- [Standart Load Balancer hakkında bilgi edinin](load-balancer-overview.md)
