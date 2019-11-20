---
title: Azure DNS Özel Bölgeleri için senaryolar
description: Azure DNS Özel Bölgeleri kullanmaya yönelik genel senaryolara genel bakış.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: 3ac4db3a2d98e761183360c268d23efcc313cf09
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048492"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS özel bölgeler senaryoları

Bir sanal ağ içinde ve sanal ağlar arasında ad çözümlemesi sağlamak Azure DNS Özel Bölgeleri. Bu makalede, bu özellik kullanılarak gerçekleştirilen bazı yaygın senaryolara bakacağız.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Senaryo: tek bir sanal ağın kapsamına alınmış ad çözümlemesi
Bu senaryoda, Azure 'da sanal makineler (VM 'Ler) dahil olmak üzere çok sayıda Azure kaynağına sahip bir sanal ağınız vardır. Belirli bir etki alanı adı (DNS bölgesi) ile sanal ağ içinden kaynakları çözümlemek istiyorsunuz ve ad çözümlemesinin özel olması ve internet 'ten erişilebilir olmaması gerekir. Ayrıca, VNET içindeki VM 'Ler için Azure 'un otomatik olarak DNS bölgesine kaydolmaları gerekir. 

Bu senaryo aşağıda gösterilmiştir. "A" adlı sanal ağ iki VM içerir (VNETA-VM1 ve VNETA-VM2). Bunların her birinin ilişkili özel IP 'Ler vardır. Contoso.com adlı bir özel bölge oluşturduktan ve bu sanal ağı bir kayıt sanal ağı olarak bağladığınızda Azure DNS, otomatik olarak bölgede gösterildiği gibi iki bir kayıt oluşturur. Şimdi, VNETA-VM1 ile VNETA-VM2.contoso.com çözümlemek için DNS sorguları VNETA-VM2 özel IP 'sini içeren bir DNS yanıtı alacaktır. Ayrıca, vneta-VM2 ' den çıkarılan VNETA-VM1 (10.0.0.1) özel IP 'si için ters DNS sorgusu (PTR), beklenen şekilde VNETA-VM1 adını içeren bir DNS yanıtı alacaktır. 

![Tek sanal ağ çözünürlüğü](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Senaryo: sanal ağlar arasında ad çözümlemesi

Bu senaryo, özel bir bölgeyi birden çok sanal ağla ilişkilendirmeniz gereken daha yaygın bir durumdur. Bu senaryo, hub ve bağlı bileşen modeli gibi mimarilerin, birden fazla başka bağlı sanal ağın bağlandığı bir merkezi hub sanal ağı olduğu mimarilere uyum sağlayabilir. Merkezi hub sanal ağı, kayıt sanal ağı olarak bir özel bölgeye bağlanabilir ve bağlı sanal ağlar, çözümleme sanal ağları olarak bağlanabilir. 

Aşağıdaki diyagramda, bu senaryonun yalnızca iki sanal ağı (A ve B) bulunduğu basit bir sürümü gösterilmektedir. Bir kayıt sanal ağı olarak atanır ve B bir çözümleme sanal ağı olarak atanır. Amaç, her iki sanal ağın da ortak bir bölge contoso.com paylaşmasına yöneliktir. Bölge oluşturulduğunda ve çözümleme ve kayıt sanal ağları bölgeye bağlandığında, Azure VM 'Ler (VNETA-VM1 ve VNETA-VM2) için DNS kayıtlarını A sanal ağından otomatik olarak kaydeder. Ayrıca, çözümleme sanal ağı B 'deki VM 'Ler için DNS kayıtlarını bölgeye el ile ekleyebilirsiniz. Bu kurulumla, ileri ve ters DNS sorguları için aşağıdaki davranışı gözlemleyeceksiniz:
* VNETA-VM1.contoso.com için çözümleme sanal ağı 'ndaki VNETB-VM1 ' t a bir DNS sorgusu, VNETA-VM1 özel IP 'sini içeren bir DNS yanıtı alacaktır.
* 10.1.0.1 için sanal ağ B 'deki VNETB-VM2 ' y a ters DNS (PTR) sorgusu, için, FQDN VNETB-VM1.contoso.com içeren bir DNS yanıtı alacaktır.  
* Sanal ağ B 'deki VNETB-VM3 öğesinden bir ters DNS (PTR) sorgusu, 10.0.0.1 için, NXDOMAIN alır. Bunun nedeni, ters DNS sorgularının yalnızca aynı sanal ağın kapsamına alınır. 


![Birden çok sanal ağ çözünürlüğü](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Senaryo: bölünmüş ufuk işlevselliği

Bu senaryoda, aynı DNS bölgesi için istemcinin bulunduğu yere (Azure 'un içinden veya Internet 'te) bağlı olarak farklı DNS çözümleme davranışı sağlamak istediğiniz bir kullanım örneğine sahipsiniz. Örneğin, uygulamanızın farklı işlevselliği veya davranışı olan özel ve genel bir sürümüne sahip olabilirsiniz, ancak her iki sürüm için aynı etki alanı adını kullanmak istersiniz. Bu senaryo, aynı ada sahip bir genel DNS bölgesi ve bir özel bölge oluşturularak Azure DNS ile gerçekleştirilebilir.

Aşağıdaki diyagramda bu senaryo gösterilmektedir. Hem özel IP 'ler hem de genel IP 'leri ayrılmış iki VM 'ye (VNETA-VM1 ve VNETA-VM2) sahip bir sanal ağınız vardır. Contoso.com adlı bir genel DNS bölgesi oluşturur ve bu VM 'Ler için genel IP 'Leri bölge içinde DNS kayıtları olarak kaydedersiniz. Ayrıca, kayıt sanal ağı olarak bir olarak belirterek contoso.com adlı bir Özel DNS bölgesi de oluşturursunuz. Azure, VM 'Leri otomatik olarak özel bir bölgeye bir kayıt olarak kaydeder ve bunlara özel IP 'Leri işaret eder.

Artık bir İnternet istemcisi VNETA-VM1.contoso.com aramak için bir DNS sorgusu yaparken, Azure genel IP kaydını ortak bölgeden döndürür. Aynı DNS sorgusu aynı sanal ağdaki başka bir VM 'den (örneğin: VNETA-VM2) verildiyse, Azure özel IP kaydını özel bölgeden döndürür. 

![Brian çözümlemeyi Böl](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Sonraki adımlar
Özel DNS bölgeleri hakkında daha fazla bilgi için bkz. [Özel etki alanları için Azure DNS'i kullanma](private-dns-overview.md).

Azure DNS [Özel BIR DNS bölgesi oluşturmayı](./private-dns-getstarted-powershell.md) öğrenin.

Ziyaret ederek DNS bölgeleri ve kayıtları hakkında bilgi edinin: [DNS bölgeleri ve kayıtlarına genel bakış](dns-zones-records.md).

Azure'un diğer önemli [ağ özelliklerinden](../networking/networking-overview.md) bazıları hakkında bilgi edinin.

