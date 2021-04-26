---
title: Ağ güvenlik grubu-nasıl çalıştığı
titlesuffix: Azure Virtual Network
description: Ağ güvenlik gruplarının Azure kaynakları arasında ağ trafiğini filtrelemenize nasıl yardımcı olduğunu öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 9510c4b0940a0a03ae9a232c3329817468aaf358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537907"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Ağ güvenlik grupları ağ trafiğini filtreleyerek
<a name="network-security-groups"></a>

Bir Azure sanal ağındaki Azure kaynaklarından gelen ve giden ağ trafiğini filtrelemek için bir Azure ağ güvenlik grubu kullanabilirsiniz. Bir ağ güvenlik grubu, Azure kaynaklarından oluşan çeşitli türlerden gelen ağ trafiğine veya giden ağ trafiğine izin veren veya reddeden [güvenlik kuralları](./network-security-groups-overview.md#security-rules) içerir. Her kural için kaynak, hedef, bağlantı noktası ve protokol belirtebilirsiniz.

Birden fazla Azure hizmetinde bulunan kaynakları bir Azure sanal ağına dağıtabilirsiniz. Hizmetlerin tam listesi için bkz. [Sanal ağa dağıtılabilecek hizmetler](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Bir sanal makine içindeki her bir sanal ağ [alt ağına](virtual-network-manage-subnet.md#change-subnet-settings) ve [ağ arabirimine](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) sıfır veya bir ağ güvenlik grubu atayabilirsiniz. Bir ağ güvenlik grubunu istediğiniz sayıda alt ağ ve ağ arabirimi ile ilişkilendirebilirsiniz.

Aşağıdaki resimde, ağ güvenlik gruplarının 80 numaralı TCP bağlantı noktası üzerinden gelen ve giden internet trafiğine izin vermek için dağıtılabilecek ağ güvenlik grupları için farklı senaryolar gösterilmektedir:

![NSG-processing](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Azure'ın ağ güvenlik grupları için gelen ve giden kuralları nasıl işlediğini anlamak için yukarıdaki resmi ve aşağıdaki metni inceleyin:

## <a name="inbound-traffic"></a>Gelen trafik

Azure, gelen trafik için ilk olarak varsa bir alt ağ ile ilişkilendirilmiş ağ güvenlik grubu içindeki kuralları ve ardından varsa ağ arabirimi ile ilişkilendirilmiş ağ güvenlik grubundaki kuralları işler.

- **VM1**: *Subnet1* ile ilişkilendirilmiş olduğundan ve *VM1*, *Subnet1* içinde olduğundan *NSG1* içindeki güvenlik kuralları işlenir. Gelen trafik için 80 numaralı bağlantı noktasına izin veren bir kural oluşturmadığınız sürece trafik [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) varsayılan güvenlik kuralı tarafından reddedilir ve *NSG2*, ağ arabirimi ile ilişkilendirilmiş olduğundan *NSG2* tarafından değerlendirilmez. *NSG1*, 80 numaralı bağlantı noktasına izin veren bir güvenlik kuralına sahipse trafik *NSG2* tarafından işlenir. 80 numaralı bağlantı noktasından gelen trafiğin sanal makineye iletilmesine izin vermek için hem *NSG1* hem de *NSG2* içinde 80 numaralı bağlantı noktası üzerinden gelen internet bağlantılarına izin veren bir kural olması gerekir.
- **VM2**: *VM2* de *Subnet1* içinde olduğundan *NSG1* içindeki kurallar işlenir. *VM2* ağ arabirimi ile ilişkilendirilmiş bir ağ güvenlik grubu olmadığından *NSG1* üzerinden izin verilen tüm trafiği alır veya *NSG1* tarafından reddedilen tüm trafiği reddeder. Ağ güvenlik grubu bir alt ağ ile ilişkilendirilmiş olduğunda bu alt ağ içindeki tüm kaynaklar için trafiğe izin verilir veya trafik reddedilir.
- **VM3**: *Subnet2* ile ilişkilendirilmiş ağ güvenlik grubu olmadığından ve *NSG2*, *VM3* üzerindeki ağ arabirimi ile ilişkilendirilmiş olduğundan alt ağa gelen trafiğe izin verilir ve bu trafik *NSG2* tarafından işlenir.
- **VM4**: *Subnet3* ile ilişkilendirilmiş ağ güvenlik grubu veya sanal makinede ağ arabirimi olmadığından *VM4,* sanal makinesine gelen trafiğe izin verilir. Ağ güvenlik grubu ile ilişkilendirilmiş olmayan alt ağ ve ağ arabirimleri üzerinden gelen tüm ağ trafiğine izin verilir.

## <a name="outbound-traffic"></a>Giden trafik

Azure, giden trafik için ilk olarak varsa bir ağ arabirimi ile ilişkilendirilmiş ağ güvenlik grubu içindeki kuralları ve ardından varsa alt ağ ile ilişkilendirilmiş ağ güvenlik grubundaki kuralları işler.

- **VM1**: *NSG2* içindeki güvenlik kuralları işlenir. 80 numaralı bağlantı noktası üzerinden internete giden trafiği reddeden bir güvenlik kuralı oluşturmadığınız sürece *NSG1* ve *NSG2* içindeki [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) varsayılan güvenlik kuralı trafiğe izin verir. *NSG2* içinde 80 numaralı bağlantı noktasından giden trafiği reddeden bir güvenlik kuralı varsa trafik reddedilir ve *NSG1* tarafından değerlendirilmez. Sanal makinenin 80 numaralı bağlantı noktasından giden trafiği reddetmek için ağ güvenlik gruplarından birinde veya her ikisinde 80 numaralı bağlantı noktasından internete giden trafiği reddeden bir kural olması gerekir.
- **VM2**: *VM2* ile ilişkilendirilmiş ağ arabiriminde bir ağ güvenlik kuralı bulunmadığından tüm trafik ağ arabiriminden alt ağa gönderilir. *NSG1* içindeki kurallar işlenir.
- **VM3**: *NSG2* içinde 80 numaralı bağlantı noktasından giden trafiği reddeden bir güvenlik kuralı varsa trafik reddedilir. *NSG2* içinde 80 numaralı bağlantı noktasından giden trafiğe izin veren bir güvenlik kuralı varsa *Subnet2* ile ilişkilendirilmiş bir ağ güvenlik grubu bulunmadığından 80 numaralı bağlantı noktasından internete giden trafiğe izin verilir.
- **VM4**: *VM4* adlı sanal makineye bağlı olan ağ arabirimi veya *Subnet3* ile ilişkilendirilmiş bir ağ güvenlik grubu olmadığından bu sanal makineden gelen tüm ağ trafiğine izin verilir.


## <a name="intra-subnet-traffic"></a>Intra-Subnet trafiği

Bir alt ağla ilişkili bir NSG 'deki güvenlik kurallarının, sanal makine arasındaki bağlantıyı etkileyebileceğini unutmayın. Örneğin, *NSG1* 'e tüm gelen ve giden trafiği reddeden bir kural eklenirse, *VM1* ve *VM2* artık birbirleriyle iletişim kuramaz. Buna izin vermek için başka bir kural özellikle eklenmelidir. 

Bir ağ arabirimi için [geçerli güvenlik kurallarını](virtual-network-network-interface.md#view-effective-security-rules) görüntüleyerek bir ağ arabirimine uygulanmış olan toplu kuralları kolayca görüntüleyebilirsiniz. Azure Ağ İzleyicisi'ndeki [IP akışı doğrulama](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) özelliğini kullanarak da bir ağ arabirimine gelen veya dışarı giden iletişime izin verilip verilmediğini belirleyebilirsiniz. IP akışı doğrulama, bir iletişimin izin verildiğini veya reddedildiğini, hangi ağ güvenlik kuralının trafiğe izin verdiğini veya reddetmediğini söyler.

> [!NOTE]
> Ağ güvenlik grupları, klasik dağıtım modelinde dağıtılan alt ağlar veya sanal makineler ve bulut Hizmetleri ile ve Kaynak Yöneticisi dağıtım modelindeki alt ağlar veya ağ arabirimleri ile ilişkilendirilir. Azure dağıtım modelleri hakkında daha fazla bilgi edinmek için bkz. [Azure dağıtım modellerini kavrama](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> İçin belirli bir nedeniniz yoksa, bir ağ güvenlik grubunu bir alt ağ veya ağ arabirimiyle ilişkilendirmenizi öneririz, ancak ikisini birden kullanamazsınız. Bir alt ağ ile ilişkilendirilmiş olan ağ güvenlik grubundaki kurallar bir ağ arabirimi ile ilişkilendirilmiş olan ağ güvenlik grubundaki kurallarla çakışabileceğinden çözmeniz gereken beklenmeyen iletişim sorunlarıyla karşılaşabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Sanal ağ dağıtımı ve ağ güvenlik grubu ataması için uygun olan Azure kaynakları hakkında bilgi için bkz. [Azure hizmetleri için sanal ağ tümleştirmesi](virtual-network-for-azure-services.md).
* Daha önce bir ağ güvenlik grubu oluşturmadıysanız deneyim edinmek için hızlı [öğreticiyi](tutorial-filter-network-traffic.md) tamamlayabilirsiniz.
* Ağ güvenlik grupları ve yönetimi hakkında bilginiz varsa bkz. [Ağ güvenlik gruplarını yönetme](manage-network-security-group.md). 
* İletişim sorunları yaşıyorsanız ve ağ güvenlik gruplarıyla ilgili sorunları gidermeniz gerekiyorsa bkz. [Sanal makine ağ trafiği filtresi sorunlarını tanılama](diagnose-network-traffic-filter-problem.md). 
* Ağ [güvenlik grubu akış günlüklerinin](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , ilişkili bir ağ güvenlik grubu olan kaynaklardan gelen ve giden ağ trafiğini analiz etmek üzere nasıl etkinleştirileceğini öğrenin.