---
title: Azure ağ erişim denetimi listesi nedir?
description: Azure 'da erişim denetim listeleri hakkında bilgi edinin
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 38655a9da103d1d669f87c6195be7f17702f9348
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056684"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Uç nokta erişim denetim listesi nedir?

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı [dağıtım modeli](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vardır: Kaynak Yöneticisi ve klasik. Bu makale klasik dağıtım modelini incelemektedir. Microsoft, en yeni dağıtımların Kaynak Yöneticisi dağıtım modelini kullanmasını önerir. 

Bir uç nokta erişim denetim listesi (ACL), Azure dağıtımınız için kullanılabilen bir güvenlik geliştirmedir. ACL, bir sanal makine uç noktası için isteğe bağlı olarak izin verme veya reddetme yeteneği sağlar. Bu paket filtreleme özelliği, ek bir güvenlik katmanı sağlar. Yalnızca uç noktalar için ağ ACL 'Leri belirtebilirsiniz. Bir sanal ağ veya bir sanal ağda yer alan belirli bir alt ağ için bir ACL belirtemezsiniz. Mümkün olduğunda, ACL 'Ler yerine ağ güvenlik grupları (NSG) kullanılması önerilir. NSG 'leri kullanırken, uç nokta erişim denetim listesi değiştirilirler ve artık uygulanmaz. NSG 'ler hakkında daha fazla bilgi için bkz. [ağ güvenlik grubuna genel bakış](security-overview.md)

ACL 'Ler, PowerShell veya Azure portal kullanılarak yapılandırılabilir. PowerShell kullanarak bir ağ ACL 'SI yapılandırmak için bkz. [PowerShell kullanarak uç noktalar için erişim denetim listelerini yönetme](virtual-networks-acl-powershell.md). Bir ağ ACL 'sini Azure portal kullanarak yapılandırmak için bkz. [sanal makineye uç noktaları ayarlama](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Ağ ACL 'Lerini kullanarak şunları yapabilirsiniz:

* Bir sanal makine giriş uç noktasına uzak alt ağ IPv4 adresi aralığına dayalı gelen trafiğe seçmeli olarak izin ver veya Reddet.
* Kara liste IP adresleri
* Sanal makine uç noktası başına birden çok kural oluşturma
* Belirli bir sanal makine uç noktasında doğru kural kümesinin uygulandığından emin olmak için kural sıralamasını kullanın (en küçükten en büyüğe)
* Belirli bir uzak alt ağ IPv4 adresi için bir ACL belirtin.

ACL sınırları için [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) makalesine bakın.

## <a name="how-acls-work"></a>ACL 'Ler nasıl çalışır?
ACL, kuralların listesini içeren bir nesnedir. Bir ACL oluşturup bir sanal makine uç noktasına uyguladığınızda, VM 'nizin ana bilgisayar düğümünde paket filtrelemesi gerçekleşir. Bu, uzak IP adreslerinden gelen trafiğin, VM 'niz yerine eşleşen ACL kuralları için ana bilgisayar düğümü tarafından filtrelenmiştir anlamına gelir. Bu, sanal makinenizin, paket filtrelemede değerli CPU döngülerini harcamasını önler.

Bir sanal makine oluşturulduğunda, tüm gelen trafiği engellemek için varsayılan bir ACL konur. Ancak, için bir uç nokta oluşturulduysa (bağlantı noktası 3389), bu uç nokta için tüm gelen trafiğe izin vermek üzere varsayılan ACL değiştirilir. Daha sonra herhangi bir uzak alt ağdan gelen trafiğe izin verilir ve bu uç noktaya güvenlik duvarı sağlanması gerekmez. Diğer tüm bağlantı noktaları, bu bağlantı noktaları için uç noktalar oluşturulmadığı takdirde gelen trafik için engellenir. Varsayılan olarak giden trafiğe izin verilir.

**Örnek varsayılan ACL tablosu**

| **Kurallar #** | **Uzak alt ağ** | **Uç noktası** | **İzin verme/reddetme** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |İzin ver |

## <a name="permit-and-deny"></a>İzin ver ve Reddet
"İzin ver" veya "Reddet" i belirten kurallar oluşturarak bir sanal makine giriş uç noktası için ağ trafiğine seçmeli olarak izin verebilir veya vermeyebilirsiniz. Varsayılan olarak, bir uç nokta oluşturulduğunda tüm trafiğe uç noktaya izin verildiğini unutmayın. Bu nedenle, sanal makine uç noktasına ulaşmaya izin vermeyi seçtiğiniz ağ trafiği üzerinde ayrıntılı denetim sağlamak istiyorsanız, izin verme/reddetme kuralları oluşturma ve bunları uygun önceliğe göre yerleştirme hakkında bilgi sahibi olmak önemlidir.

Dikkate alınması gereken noktaları:

1. **ACL yok –** Bir uç nokta oluşturulduğunda, varsayılan olarak tüm uç noktalara izin veririz.
2. **Izin ver-** Bir veya daha fazla "izin ver" aralığı eklediğinizde varsayılan olarak tüm diğer aralıkları reddetmekten olursunuz. Yalnızca izin verilen IP aralığından gelen paketler, sanal makine uç noktası ile iletişim kurabilecektir.
3. **Reddet-** Bir veya daha fazla "reddetme" aralığı eklediğinizde, varsayılan olarak tüm diğer trafik aralıklarına izin verilir.
4. **Izin verme ve reddetme-** Belirli bir IP aralığını kabul etmek veya reddetmek için, "izin ver" ve "Reddet" birleşimini kullanabilirsiniz.

## <a name="rules-and-rule-precedence"></a>Kurallar ve kural önceliği
Ağ ACL 'Leri, belirli sanal makine uç noktalarında ayarlanabilir. Örneğin, belirli IP adreslerine erişimi kilitleyen bir sanal makinede oluşturulan RDP uç noktası için bir ağ ACL 'SI belirtebilirsiniz. Aşağıdaki tabloda, RDP erişimine izin vermek için belirli bir aralığın ortak sanal IP 'lerine (VIP 'Ler) erişim izni vermek için bir yol gösterilmektedir. Diğer tüm uzak IP 'Ler reddedilir. *En düşük önceliğe sahip* bir kural sırası izliyoruz.

### <a name="multiple-rules"></a>Birden çok kural
Aşağıdaki örnekte, RDP uç noktasına yalnızca iki ortak IPv4 adres aralığından (65.0.0.0/8 ve 159.0.0.0/8) erişime izin vermek istiyorsanız, iki *izin* kuralını belirterek bunu elde edebilirsiniz. Bu durumda, RDP, bir sanal makine için varsayılan olarak oluşturulduğundan, uzak bir alt ağa bağlı olarak RDP bağlantı noktasına erişimi kilitlemek isteyebilirsiniz. Aşağıdaki örnekte, RDP erişimine izin vermek için belirli bir aralığın ortak sanal IP 'lerine (VIP 'Ler) erişim verme yöntemi gösterilmektedir. Diğer tüm uzak IP 'Ler reddedilir. Bu, ağ ACL 'Leri belirli bir sanal makine uç noktası için ayarlanabileceğinden ve varsayılan olarak erişim engellendiğinden çalışır.

**Örnek – birden çok kural**

| **Kurallar #** | **Uzak alt ağ** | **Uç noktası** | **İzin verme/reddetme** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |İzin ver |
| 200 |159.0.0.0/8 |3389 |İzin ver |

### <a name="rule-order"></a>Kural sırası
Uç nokta için birden çok kural belirtilebildiğinden, hangi kuralın öncelikli olduğunu belirleyebilmek için kuralları düzenlemenin bir yolu olmalıdır. Kural sırası önceliği belirtir. Ağ ACL 'Leri *En düşük öncelik* kuralı sırasını izler. Aşağıdaki örnekte, 80 numaralı bağlantı noktası için yalnızca belirli IP adresi aralıklarına erişim izni verilir. Bunu yapılandırmak için, 175.1.0.1/24 alanındaki adresler için bir \# reddetme kuralı (kural 100) vardır. Daha sonra 175.0.0.0/8 altındaki diğer tüm adreslere erişime izin veren öncelik 200 ile ikinci bir kural belirtilir.

**Örnek – kural önceliği**

| **Kurallar #** | **Uzak alt ağ** | **Uç noktası** | **İzin verme/reddetme** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Reddet |
| 200 |175.0.0.0/8 |80 |İzin ver |

## <a name="network-acls-and-load-balanced-sets"></a>Ağ ACL 'Leri ve yük dengeli kümeler
Ağ ACL 'Leri, yük dengeli bir küme uç noktasında belirtilebilir. Yük dengeli bir küme için bir ACL belirtilmişse, ağ ACL 'SI bu yük dengeli küme içindeki tüm sanal makinelere uygulanır. Örneğin, "bağlantı noktası 80" ile bir yük dengeli küme oluşturulduysa ve yük dengeli küme 3 VM içeriyorsa, bir VM 'nin "bağlantı noktası 80" uç noktasında oluşturulan ağ ACL 'SI otomatik olarak diğer VM 'lere uygulanır.

![Ağ ACL 'Leri ve yük dengeli kümeler](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Sonraki Adımlar
[PowerShell kullanarak uç noktalar için erişim denetim listelerini yönetme](virtual-networks-acl-powershell.md)

