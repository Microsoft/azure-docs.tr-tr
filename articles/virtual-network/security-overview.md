---
title: Azure güvenlik gruplarına genel bakış
titlesuffix: Azure Virtual Network
description: Ağ ve uygulama güvenlik grupları hakkında bilgi edinin. Güvenlik grupları Azure kaynakları arasındaki ağ trafiğini filtrelemenize yardımcı olur.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 6046ab98e657cd14a2ac883cd32709c9a1b5da57
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721489"
---
# <a name="security-groups"></a>Güvenlik grupları
<a name="network-security-groups"></a>

Ağ güvenlik grupları ile bir Azure [sanal ağı](virtual-networks-overview.md) içindeki Azure kaynaklarına gelen ve bu kaynaklardan giden ağ trafiğini filtreleyebilirsiniz. Ağ güvenlik grupları, farklı Azure kaynaklarına gelen ya da bu kaynaklardan dışarı giden ağ trafiğine izin veren veya bu trafiği reddeden [güvenlik kuralları](#security-rules) içerir. Sanal ağ dağıtımı ve ağ güvenlik grubu ataması için uygun olan Azure kaynakları hakkında bilgi için bkz. [Azure hizmetleri için sanal ağ tümleştirmesi](virtual-network-for-azure-services.md). Her kural için kaynak, hedef, bağlantı noktası ve protokol belirtebilirsiniz.

Bu makalede, ağ güvenlik gruplarını daha etkili bir şekilde kullanmanıza yardımcı olmak için gereken kavramlar açıklanır. Daha önce bir ağ güvenlik grubu oluşturmadıysanız deneyim edinmek için hızlı [öğreticiyi](tutorial-filter-network-traffic.md) tamamlayabilirsiniz. Ağ güvenlik grupları ve yönetimi hakkında bilginiz varsa bkz. [Ağ güvenlik gruplarını yönetme](manage-network-security-group.md). İletişim sorunları yaşıyorsanız ve ağ güvenlik gruplarıyla ilgili sorunları gidermeniz gerekiyorsa bkz. [Sanal makine ağ trafiği filtresi sorunlarını tanılama](diagnose-network-traffic-filter-problem.md). [Ağ güvenlik grubu akış günlüklerini](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) etkinleştirerek bir ağ güvenlik grubu ile ilişkilendirilmiş kaynaklara gelen ve bu kaynaklardan giden [ağ trafiğini analiz edebilirsiniz](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="security-rules"></a>Güvenlik kuralları

Bir ağ güvenlik grubunda Azure abonelik [limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) dahilinde sıfır veya istenen sayıda kural bulunabilir. Her bir kural aşağıdaki özellikleri belirtir:

|Özellik  |Açıklama  |
|---------|---------|
|Ad|Ağ güvenlik grubu içinde benzersiz bir ad.|
|Öncelik | 100 ile 4096 arasında bir rakam. Kurallar öncelik sırasına göre işleme alınır ve düşük rakamlı kurallar daha yüksek önceliğe sahip olduğundan yüksek rakamlı kurallardan önce uygulanır. Trafik bir kuralla eşleştiğinde işlem durur. Bunun sonucunda yüksek önceliğe sahip olan kurallarla aynı özniteliklere sahip olan önceliği daha düşük olan (yüksek rakamlı) kurallar işleme alınmaz.|
|Kaynak veya hedef| Herhangi bir IP adresi, sınıfsız etki alanları arası yönlendirme (CIDR) bloğu (10.0.0.0/24 gibi), [hizmet etiketi](service-tags-overview.md) veya [uygulama güvenlik grubu](#application-security-groups). Bir Azure kaynağı için adres belirtirken kaynağa atanmış olan özel IP adresini belirtmeniz gerekir. Ağ güvenlik grupları, Azure gelen trafik için genel IP adresini özel IP adresine çevirdikten sonra ve giden trafik için özel IP adresini genel IP adreslerine çevirmeden önce işleme alınır. Azure [IP adresleri](virtual-network-ip-addresses-overview-arm.md) hakkında daha fazla bilgi edinin. Aralık, hizmet etiketi veya uygulama güvenlik grubu belirterek daha az sayıda güvenlik kuralı oluşturabilirsiniz. Bir kuralda birden fazla IP adresi veya aralığı belirtme özelliği (birden fazla hizmet etiketi veya uygulama grubu belirtemezsiniz) [genişletilmiş güvenlik kuralı](#augmented-security-rules) olarak adlandırılır. Genişletilmiş güvenlik kuralları yalnızca Resource Manager dağıtım modeliyle oluşturulmuş olan ağ güvenlik gruplarında oluşturulabilir. Klasik dağıtım modeliyle oluşturulmuş olan ağ güvenlik gruplarında birden fazla IP adresi ve IP adresi aralığı belirtemezsiniz. [Azure dağıtım modelleri](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hakkında daha fazla bilgi edinin.|
|Protokol     | TCP, UDP, ıCMP veya any.|
|Yön| Kuralın gelen veya giden trafiğe uygulanma seçeneği.|
|Bağlantı noktası aralığı     |Tek bir bağlantı noktası veya aralık belirtebilirsiniz. Örneğin 80 veya 10000-10005 değerini kullanabilirsiniz. Aralık belirterek oluşturmanız gereken güvenlik kuralı sayısını azaltabilirsiniz. Genişletilmiş güvenlik kuralları yalnızca Resource Manager dağıtım modeliyle oluşturulmuş olan ağ güvenlik gruplarında oluşturulabilir. Klasik dağıtım modeliyle oluşturulmuş olan ağ güvenlik gruplarında aynı güvenlik kuralı içinde birden fazla bağlantı noktası ve bağlantı noktası aralığı belirtemezsiniz.   |
|Eylem     | İzin ver veya reddet        |

Ağ güvenlik grubu güvenlik kuralları, trafiğe izin verilmesi veya trafiğin reddedilmesi için 5 tanımlama grubu bilgisi (kaynak, kaynak bağlantı noktası, hedef, hedef bağlantı noktası ve protokol) ile önceliğe göre değerlendirilir. Var olan bağlantılar için bir akış kaydı oluşturulur. Akış kaydının bağlantı durumuna göre iletişime izin verilir veya iletişim reddedilir. Akış kaydı bir ağ güvenlik grubunun durum bilgisine sahip olmasını sağlar. Örneğin 80 numaralı bağlantı noktasından tüm adreslere doğru giden bir güvenlik kuralı belirtirseniz giden trafiğe yanıt olarak bir gelen güvenlik kuralı belirtmeniz gerekli değildir. Yalnızca iletişimin dışarıdan başlatılması halinde bir gelen güvenlik kuralı belirtmeniz gerekir. Bunun tersi de geçerlidir. Gelen trafiğe bir bağlantı noktası üzerinden izin verilmesi halinde bağlantı noktasından geçen trafiğe yanıt olarak bir giden güvenlik belirtmeniz gerekli değildir.
Akışı etkinleştiren bir güvenlik kuralını kaldırdığınızda mevcut bağlantılar kesintiye uğramayabilir. Bağlantılar durdurulduğunda trafik akışları kesintiye uğrar ve en azından birkaç dakika boyunca hiçbir yönde trafik akışı gerçekleşmez.

Bir ağ güvenlik grubu içinde sınırlı sayıda güvenlik kuralı oluşturabilirsiniz. Ayrıntılar için [Azure limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini inceleyin.

## <a name="augmented-security-rules"></a>Genişletilmiş güvenlik kuralları

Genişletilmiş güvenlik kuralları, sanal ağlar için güvenlik tanımını daha basit hale getirerek daha az sayıda kuralla daha geniş çaplı ve karmaşık ağ güvenlik ilkesi tanımlamanızı sağlar. Birden fazla bağlantı noktası ile birden fazla açık IP adresini ve aralığını bir araya getirerek tek ve anlaşılması kolay bir güvenlik kuralı oluşturabilirsiniz. Genişletilmiş kuralları bir kuralın kaynak, hedef ve bağlantı noktası alanlarında kullanabilirsiniz. Güvenlik kuralı tanımınızın bakımını kolaylaştırmak için genişletilmiş güvenlik kurallarını [hizmet etiketleri](service-tags-overview.md) veya [uygulama güvenlik gruplarıyla](#application-security-groups) bir arada kullanabilirsiniz. Bir kuralda belirtebileceğiniz adres, Aralık ve bağlantı noktası sayısı için sınırlar vardır. Ayrıntılar için [Azure limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini inceleyin.

## <a name="service-tags"></a>Hizmet etiketleri

Hizmet etiketi, belirli bir Azure hizmetinden bir IP adresi önekleri grubunu temsil eder. Ağ güvenlik kurallarında sık sık güncelleştirmelerin karmaşıklığını en aza indirmenize yardımcı olur.

Daha fazla bilgi için bkz. [Azure hizmet etiketleri](service-tags-overview.md). 

## <a name="default-security-rules"></a>Varsayılan güvenlik kuralları

Azure, oluşturduğunuz tüm ağ güvenlik gruplarına aşağıdaki varsayılan kuralları ekler:

### <a name="inbound"></a>Gelen

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları|Hedef|Hedef bağlantı noktaları|Protokol|Erişim|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Herhangi biri|Allow|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları|Hedef|Hedef bağlantı noktaları|Protokol|Erişim|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Herhangi biri|Allow|

#### <a name="denyallinbound"></a>DenyAllInbound

|Öncelik|Kaynak|Kaynak bağlantı noktaları|Hedef|Hedef bağlantı noktaları|Protokol|Erişim|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Herhangi biri|Deny|

### <a name="outbound"></a>Giden

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Herhangi biri | Allow |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Herhangi biri | Allow |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Herhangi biri | Deny |

**Kaynak** ve **Hedef** sütunlarında *VirtualNetwork*, *AzureLoadBalancer* ve *Internet*, için IP adresi yerine [hizmet etiketi](service-tags-overview.md) belirtilir. Protokol sütununda, TCP, UDP ve ıCMP **'yi kapsar.** Bir kural oluştururken TCP, UDP, ıCMP veya any belirtebilirsiniz. *Kaynak* ve **Hedef** sütunlarında yer alan **0.0.0.0/0** ifadesi tüm adresleri temsil eder. Azure portal, Azure CLı veya PowerShell gibi istemciler bu ifade için * veya herhangi birini kullanabilir.
 
Varsayılan kuralları kaldıramazsınız ancak daha yüksek önceliğe sahip kurallar oluşturarak onları geçersiz kılabilirsiniz.

## <a name="application-security-groups"></a>Uygulama güvenliği grupları

Uygulama güvenlik grupları ağ güvenliğini uygulamanın yapısının doğal bir uzantısı olarak yapılandırmanıza imkan vererek sanal makineleri gruplamanızı ve ağ güvenlik ilkelerini bu gruplara göre tanımlamanızı sağlar. Açık IP adreslerinin bakımını el ile yapmanıza gerek kalmadan güvenlik ilkesini farklı ölçeklerde yeniden kullanabilirsiniz. Platform açık IP adreslerinin ve birden fazla kural kümesinin karmaşık süreçlerini üstlenerek iş mantığınıza odaklanmanızı sağlar. Uygulama güvenlik gruplarını daha iyi anlamak için aşağıdaki örneği inceleyin:

![Uygulama güvenliği grupları](./media/security-groups/application-security-groups.png)

Yukarıdaki resimde *NIC1* ve *NIC2*, *AsgWeb* uygulama güvenlik grubunun üyeleridir. *NIC3*, *AsgLogic* uygulama güvenlik grubunun üyesidir. *NIC4*, *AsgDb* uygulama güvenlik grubunun üyesidir. Bu örnekteki tüm ağ arabirimleri tek bir uygulama güvenlik grubuna üye olsa da bir ağ arabirimi [Azure sınırları](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) dahilinde birden fazla uygulama güvenlik grubuna üye olabilir. Ağ arabirimlerinin hiçbiri bir ağ güvenlik grubuyla ilişkilendirilmemiştir. *NSG1*, iki alt ağ ile de ilişkilendirilmiştir ve aşağıdaki kuralları içerir:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Bu kural, internetten Web sunucularına gelen trafiğe izin vermek için kullanılır. İnternetten gelen trafik, [DenyAllInbound](#denyallinbound) varsayılan güvenlik grubu tarafından reddedildiğinden *AsgLogic* veya *AsgDb* uygulama güvenlik grupları için ek kurala ihtiyaç duyulmaz.

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

### <a name="deny-database-all"></a>Deny-Database-All

[AllowVNetInBound](#allowvnetinbound) varsayılan güvenlik kuralı aynı sanal ağ içinde bulunan kaynaklar arasındaki tüm iletişime izin verdiğinden, tüm kaynaklardan gelen trafiği reddetmek için bu kurala ihtiyaç duyulur.

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Herhangi biri | Deny |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Bu kural *AsgLogic* uygulama güvenlik grubundan *AsgDb* uygulama güvenlik grubuna gelen trafiğe izin verir. Bu kuralın önceliği, *Deny-Database-All* kuralının önceliğinden daha yüksektir. Sonuç olarak bu kural, *Deny-Database-All* kuralından önce işlenir ve böylece *AsgLogic* uygulama güvenlik grubundan gelen trafiğe izin veriler ve diğer tüm trafik engellenir.

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

Bir uygulama güvenlik grubunu kaynak veya hedef olarak belirten kurallar yalnızca uygulama güvenlik grubuna üye olan ağ arabirimlerine uygulanır. Ağ arabirimi bir uygulama güvenlik grubuna üye değilse, ağ güvenlik grubu alt ağ ile ilişkilendirilmiş olsa dahi kural ağ arabirimine uygulanmaz.

Uygulama güvenlik grupları aşağıdaki sınırlamalara sahiptir:

-   Bir abonelik içinde bulunabilecek uygulama güvenlik grubu sayısı sınırlıdır ve uygulama güvenlik gruplarıyla ilgili başka sınırlar da vardır. Ayrıntılar için [Azure limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini inceleyin.
- Bir uygulama güvenlik grubunu bir güvenlik kuralında kaynak ve hedef olarak belirtebilirsiniz. Kaynak veya hedefte birden çok uygulama güvenlik grubu belirtemezsiniz.
- Bir uygulama güvenlik grubuna atanan tüm ağ arabirimleri, uygulama güvenlik grubuna atanmış ilk ağ arabirimiyle aynı sanal ağda olmalıdır. Örneğin, ilk ağ arabirimi *VNet1* adlı sanal ağdaki *AsgWeb* adlı bir uygulama güvenlik grubuna atanmışsa *ASGWeb*’e atanan sonraki tüm ağ arabirimleri *VNet1*’de olmalıdır. Bir uygulama güvenlik grubuna farklı ağlarda bulunan ağ arabirimlerini ekleyemezsiniz.
- Uygulama güvenlik grubunu bir güvenlik kuralında kaynak ve hedef olarak belirtirseniz iki uygulama güvenlik grubundaki ağ arabirimlerinin de aynı sanal ağda bulunması gerekir. Örneğin *AsgLogic* üzerinde *VNet1* içinde bulunan ağ arabirimleri, *AsgDb* üzerinde de *VNet2* içinde bulunan ağ arabirimleri varsa, bir kural içinde *AsgLogic* grubunu kaynak olarak ve *AsgDb* grubunu da hedef olarak belirleyemezsiniz. Hem kaynak hem de hedef uygulama güvenlik gruplarının tüm ağ arabirimlerinin aynı sanal ağ içinde bulunması gerekir.

> [!TIP]
> İhtiyacınız olan güvenlik kuralı sayısını ve kural değiştirme gereksinimini en aza indirmek için, gereken uygulama güvenlik gruplarını planlarken ve kuralları oluştururken tek IP adreslerini veya IP adresi aralıklarını kullanmak yerine hizmet etiketlerini ya da uygulama güvenlik gruplarını kullanın.

## <a name="how-traffic-is-evaluated"></a>Trafik nasıl değerlendirilir?

Birden fazla Azure hizmetinde bulunan kaynakları bir Azure sanal ağına dağıtabilirsiniz. Hizmetlerin tam listesi için bkz. [Sanal ağa dağıtılabilecek hizmetler](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Bir sanal makine içindeki her bir sanal ağ [alt ağına](virtual-network-manage-subnet.md#change-subnet-settings) ve [ağ arabirimine](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) sıfır veya bir ağ güvenlik grubu atayabilirsiniz. Bir ağ güvenlik grubunu istediğiniz sayıda alt ağ ve ağ arabirimi ile ilişkilendirebilirsiniz.

Aşağıdaki resimde, ağ güvenlik gruplarının 80 numaralı TCP bağlantı noktası üzerinden gelen ve giden internet trafiğine izin vermek için dağıtılabilecek ağ güvenlik grupları için farklı senaryolar gösterilmektedir:

![NSG-processing](./media/security-groups/nsg-interaction.png)

Azure'ın ağ güvenlik grupları için gelen ve giden kuralları nasıl işlediğini anlamak için yukarıdaki resmi ve aşağıdaki metni inceleyin:

### <a name="inbound-traffic"></a>Gelen trafik

Azure, gelen trafik için ilk olarak varsa bir alt ağ ile ilişkilendirilmiş ağ güvenlik grubu içindeki kuralları ve ardından varsa ağ arabirimi ile ilişkilendirilmiş ağ güvenlik grubundaki kuralları işler.

- **VM1**: *Subnet1* ile ilişkilendirilmiş olduğundan ve *VM1*, *Subnet1* içinde olduğundan *NSG1* içindeki güvenlik kuralları işlenir. Gelen trafik için 80 numaralı bağlantı noktasına izin veren bir kural oluşturmadığınız sürece trafik [DenyAllInbound](#denyallinbound) varsayılan güvenlik kuralı tarafından reddedilir ve *NSG2*, ağ arabirimi ile ilişkilendirilmiş olduğundan *NSG2* tarafından değerlendirilmez. *NSG1*, 80 numaralı bağlantı noktasına izin veren bir güvenlik kuralına sahipse trafik *NSG2* tarafından işlenir. 80 numaralı bağlantı noktasından gelen trafiğin sanal makineye iletilmesine izin vermek için hem *NSG1* hem de *NSG2* içinde 80 numaralı bağlantı noktası üzerinden gelen internet bağlantılarına izin veren bir kural olması gerekir.
- **VM2**: *VM2* de *Subnet1* içinde olduğundan *NSG1* içindeki kurallar işlenir. *VM2* ağ arabirimi ile ilişkilendirilmiş bir ağ güvenlik grubu olmadığından *NSG1* üzerinden izin verilen tüm trafiği alır veya *NSG1* tarafından reddedilen tüm trafiği reddeder. Ağ güvenlik grubu bir alt ağ ile ilişkilendirilmiş olduğunda bu alt ağ içindeki tüm kaynaklar için trafiğe izin verilir veya trafik reddedilir.
- **VM3**: *Subnet2* ile ilişkilendirilmiş ağ güvenlik grubu olmadığından ve *NSG2*, *VM3* üzerindeki ağ arabirimi ile ilişkilendirilmiş olduğundan alt ağa gelen trafiğe izin verilir ve bu trafik *NSG2* tarafından işlenir.
- **VM4**: *Subnet3* ile ilişkilendirilmiş ağ güvenlik grubu veya sanal makinede ağ arabirimi olmadığından *VM4,* sanal makinesine gelen trafiğe izin verilir. Ağ güvenlik grubu ile ilişkilendirilmiş olmayan alt ağ ve ağ arabirimleri üzerinden gelen tüm ağ trafiğine izin verilir.

### <a name="outbound-traffic"></a>Giden trafik

Azure, giden trafik için ilk olarak varsa bir ağ arabirimi ile ilişkilendirilmiş ağ güvenlik grubu içindeki kuralları ve ardından varsa alt ağ ile ilişkilendirilmiş ağ güvenlik grubundaki kuralları işler.

- **VM1**: *NSG2* içindeki güvenlik kuralları işlenir. 80 numaralı bağlantı noktası üzerinden internete giden trafiği reddeden bir güvenlik kuralı oluşturmadığınız sürece [NSG1](#allowinternetoutbound) ve *NSG2* içindeki *AllowInternetOutbound* varsayılan güvenlik kuralı trafiğe izin verir. *NSG2* içinde 80 numaralı bağlantı noktasından giden trafiği reddeden bir güvenlik kuralı varsa trafik reddedilir ve *NSG1* tarafından değerlendirilmez. Sanal makinenin 80 numaralı bağlantı noktasından giden trafiği reddetmek için ağ güvenlik gruplarından birinde veya her ikisinde 80 numaralı bağlantı noktasından internete giden trafiği reddeden bir kural olması gerekir.
- **VM2**: *VM2* ile ilişkilendirilmiş ağ arabiriminde bir ağ güvenlik kuralı bulunmadığından tüm trafik ağ arabiriminden alt ağa gönderilir. *NSG1* içindeki kurallar işlenir.
- **VM3**: *NSG2* içinde 80 numaralı bağlantı noktasından giden trafiği reddeden bir güvenlik kuralı varsa trafik reddedilir. *NSG2* içinde 80 numaralı bağlantı noktasından giden trafiğe izin veren bir güvenlik kuralı varsa *Subnet2* ile ilişkilendirilmiş bir ağ güvenlik grubu bulunmadığından 80 numaralı bağlantı noktasından internete giden trafiğe izin verilir.
- **VM4**: *VM4* adlı sanal makineye bağlı olan ağ arabirimi veya *Subnet3* ile ilişkilendirilmiş bir ağ güvenlik grubu olmadığından bu sanal makineden gelen tüm ağ trafiğine izin verilir.


### <a name="intra-subnet-traffic"></a>Alt ağ trafiği

Bir alt ağla ilişkili bir NSG 'deki güvenlik kurallarının, sanal makine arasındaki bağlantıyı etkileyebileceğini unutmayın. Örneğin, *NSG1* 'e tüm gelen ve giden trafiği reddeden bir kural eklenirse, *VM1* ve *VM2* artık birbirleriyle iletişim kuramaz. Buna izin vermek için başka bir kural özellikle eklenmelidir. 



Bir ağ arabirimi için [geçerli güvenlik kurallarını](virtual-network-network-interface.md#view-effective-security-rules) görüntüleyerek bir ağ arabirimine uygulanmış olan toplu kuralları kolayca görüntüleyebilirsiniz. Azure Ağ İzleyicisi'ndeki [IP akışı doğrulama](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) özelliğini kullanarak da bir ağ arabirimine gelen veya dışarı giden iletişime izin verilip verilmediğini belirleyebilirsiniz. IP akışı doğrulama, iletişime izin verme veya reddetme durumunu ve trafiğe izin veren veya onu reddeden ağ güvenlik kuralının hangisi olduğunu belirler.

> [!NOTE]
> Ağ güvenlik grupları, klasik dağıtım modelinde dağıtılan alt ağlar veya sanal makineler ve bulut Hizmetleri ile ve Kaynak Yöneticisi dağıtım modelindeki alt ağlar veya ağ arabirimleri ile ilişkilendirilir. Azure dağıtım modelleri hakkında daha fazla bilgi edinmek için bkz. [Azure dağıtım modellerini kavrama](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Belirli bir nedeniniz yoksa bir ağ güvenlik grubunu bir alt ağ veya ağ arabirimi ile ilişkilendirmenizi ancak ikisiyle birden ilişkilendirmemenizi öneririz. Bir alt ağ ile ilişkilendirilmiş olan ağ güvenlik grubundaki kurallar bir ağ arabirimi ile ilişkilendirilmiş olan ağ güvenlik grubundaki kurallarla çakışabileceğinden çözmeniz gereken beklenmeyen iletişim sorunlarıyla karşılaşabilirsiniz.

## <a name="azure-platform-considerations"></a>Azure platformunda dikkat edilmesi gerekenler

- **Konak düğümünün sanal IP 'si**: DHCP, DNS, imds ve sistem durumu izleme gibi temel altyapı hizmetleri, 168.63.129.16 ve 169.254.169.254 sanallaştırılmış ana bilgisayar IP adresleri aracılığıyla sağlanır. Bu IP adresleri Microsoft 'a aittir ve tüm bölgelerde bu amaç için kullanılan tek sanallaştırılmış IP adresleridir.
- **Lisanslama (Anahtar Yönetimi Hizmeti):** Sanal makinelerde çalışan Windows görüntülerinin lisanslanması gerekir. Lisanslama için, lisans isteği sorgularını işleyen Anahtar Yönetimi Hizmeti ana bilgisayar sunucularına bir lisans isteği gönderilir. İstek, bağlantı noktası 1688 üzerinden gönderilir. [default route 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) yapılandırması kullanan dağıtmalar için bu platform kuralı devre dışı bırakılır.
- **Yük dengelenmiş havuzlardaki sanal makineler**: Uygulanan kaynak bağlantı noktası ve adres aralığı, yük dengeleyiciye değil kaynak bilgisayara aittir. Hedef bağlantı noktası ve adres aralığı, yük dengeleyici değil, hedef bilgisayar içindir.
- **Azure hizmet örnekleri**: HDInsight, Uygulama Servisi Ortamları ve Sanal Makine Ölçek Kümeleri gibi sanal ağ alt ağlarına dağıtılmış olan farklı Azure hizmeti örnekleri. Sanal ağlara dağıtabileceğiniz hizmetlerin tam listesi için bkz. [Azure hizmetleri için sanal ağ](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Bir ağ güvenlik grubunu kaynağın dağıtılmış olduğu alt ağa uygulamadan önce hizmetlerle ilgili olan bağlantı noktası gereksinimlerini öğrendiğinizden emin olun. Gerekli bağlantı noktalarını reddetmeniz halinde hizmet düzgün çalışmaz.
- **Giden e-posta gönderme**: Microsoft, Azure Sanal Makineler'den e-posta göndermek için kimliği doğrulanmış SMTP geçiş hizmetlerini (normalde TCP bağlantı noktası 587 üzerinden bağlanır, ama sıklıkla başkalarını da kullanır) kullanmanızı önerir. SMTP geçiş hizmetleri, üçüncü taraf e-posta sağlayıcılarının iletileri reddetme olasılığını en aza indirmek için gönderen saygınlığı konusunda uzmanlaşmıştır. Bu tür SMTP geçiş hizmetleri Exchange Online Protection ve SendGrid'i içerir ancak bunlarla sınırlı değildir. Abonelik türünüz ne olursa olsun, Azure'da SMTP geçiş hizmetlerinin kullanımına hiçbir kısıtlama getirilmez. 

  Azure aboneliğinizi 15 Kasım 2017'den önce oluşturduysanız, SMTP geçiş hizmetlerini kullanabileceğiniz gibi, doğrudan TCP bağlantı noktası 25 üzerinden de e-posta gönderebilirsiniz. Aboneliğinizi 15 Kasım 2017'den sonra oluşturduysanız, doğrudan bağlantı noktası 25 üzerinden e-posta gönderemeyebilirsiniz. Bağlantı noktası 25 üzerinden giden iletişimin davranışı, sahip olduğunuz aboneliğe bağlıdır:

     - **Kurumsal Anlaşma**: Giden bağlantı noktası 25 iletişimine izin verilir. Azure platformundan hiçbir kısıtlama uygulanmadan, giden e-postaları doğrudan sanal makinelerden dış e-posta sağlayıcılarına gönderebilirsiniz. 
     - **Kullandıkça öde:** Tüm kaynaklardan giden bağlantı noktası 25 iletişimi engellenir. Sanal makineden doğrudan dış e-posta sağlayıcılarına (kimliği doğrulanmış SMTP geçişi kullanmadan) e-posta göndermeniz gerekirse, kısıtlamanın kaldırılması için istekte bulunabilirsiniz. İstekler gözden geçirilip Microsoft'un takdirine bağlı olarak onaylanır ve yalnızca dolandırıcılık önleme denetimleri yapıldıktan sonra kabul edilir. İstekte bulunmak için, sorun türü *Teknik*, *Sanal Ağ Bağlantısı*, *E-posta gönderilemiyor (SMTP/Bağlantı Noktası 25)* olan bir destek olayı açın. Destek olayınıza, aboneliğinizin neden kimliği doğrulanmış SMTP geçişi üzerinden değil de doğrudan posta sağlayıcılarına e-posta göndermesi gerektiği konusundaki ayrıntıları da ekleyin. Aboneliğiniz muaf tutulursa, yalnızca muafiyet tarihinden sonra oluşturulmuş sanal makineler bağlantı noktası 25 üzerinden giden iletişimi kurabilir.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark ve Ücretsiz deneme**: Tüm kaynaklardan giden bağlantı noktası 25 iletişimi engellenir. Kısıtlamayı kaldırmaya yönelik istekte bulunulamaz çünkü istekler kabul edilmez. Sanal makinenizden e-posta göndermeniz gerekirse, SMTP geçiş hizmetini kullanmanız gerekir.
     - **Bulut hizmeti sağlayıcısı**: Bulut hizmeti sağlayıcısı aracılığıyla Azure kaynakları kullanan müşteriler bulut hizmeti sağlayıcılarıyla bir destek talebi oluşturarak, güvenli SMTP geçişi kullanılamıyorsa sağlayıcıların onlar adına bir engelleme kaldırma talebi oluşturmalarını isteyebilir.

  Azure bağlantı noktası 25 üzerinde e-posta göndermenize izin verirse, Microsoft e-posta sağlayıcılarının sanal makinenizden gelen e-postayı kabul edeceğini garanti edemez. Belirli bir sağlayıcı sanal makinenizden gelen postayı reddederse, tüm ileti teslimi veya istenmeyen posta filtreleme sorunlarını çözmek için doğrudan sağlayıcıyla çalışmanız veya kimliği doğrulanmış SMTP geçiş hizmeti kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Ağ güvenlik grubu oluşturmayı](tutorial-filter-network-traffic.md) öğrenin.
