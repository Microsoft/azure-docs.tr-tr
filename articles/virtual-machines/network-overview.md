---
title: Sanal ağlar
description: Azure 'da Linux sanal makineleri oluşturmayla ilgili olarak ağ oluşturma hakkında bilgi edinin.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 20cc67d5c6436d7c0f44071509e13af324a88eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578861"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Azure 'da sanal ağlar ve sanal makineler 

Azure sanal makinesi (VM) oluştururken bir [sanal ağ](../virtual-network/virtual-networks-overview.md) (VNet) oluşturmanız ya da mevcut bir VNet’i kullanmanız gerekir. Sanal ağda VM’lerinize nasıl erişilmesini istediğinize de karar vermeniz gerekir. [Kaynakları oluşturmadan önce planlama yapmak](../virtual-network/virtual-network-vnet-plan-design-arm.md) ve [ağ kaynaklarının sınırlarını](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) anladığınızdan emin olmak önemlidir.

Aşağıdaki şekilde VM’ler web sunucuları ve veritabanı sunucuları olarak temsil edilmektedir. Her VM kümesi, sanal ağdaki farklı alt ağlara atanmıştır.

![Azure sanal ağı](./media/virtual-machines-common-network-overview/vnetoverview.png)

VM oluşturmadan önce bir sanal ağ oluşturabilirsiniz veya bir VM oluşturun. Bir VM ile iletişimi desteklemek için şu kaynakları oluşturursunuz:

- Ağ arabirimleri
- IP adresleri
- Sanal ağ ve alt ağlar

Bu temel kaynaklara ek olarak şu isteğe bağlı kaynakları da göz önünde bulundurmalısınız:

- Ağ güvenlik grupları
- Yük dengeleyiciler 

## <a name="network-interfaces"></a>Ağ arabirimleri

Bir [ağ arabirimi (NIC)](../virtual-network/virtual-network-network-interface.md) , bir VM ile sanal ağ (VNet) arasındaki bir iç bağlantı olur. Bir VM en az bir NIC içermelidir, ancak oluşturduğunuz VM’nin boyutuna bağlı olarak birden fazla NIC içerebilir. Her VM boyutunun kaç NIC tarafından desteklendiği hakkında bilgi edinin, bkz. [VM boyutları](sizes.md).

Birden çok NIC bulunan bir VM oluşturabilir ve bir VM'nin yaşam döngüsü boyunca NIC ekleyip kaldırabilirsiniz. Birden çok NIC, VM'nin farklı alt ağlara bağlanarak trafik alışverişini en uygun arabirimden gerçekleştirmesini sağlar. VM boyutu tarafından desteklenen sayıyla sınırlı olmak üzere aynı kullanılabilirlik kümesinde farklı sayıda ağ arabirime sahip VM'ler bulunabilir. 

Bir VM’ye bağlı her NIC’nin VM ile aynı konum ve abonelikte bulunması gerekir. Her NIC’nin NIC ile aynı Azure konumunda ve aboneliğinde bulunan bir VNet’e bağlanması gerekir. NIC oluşturulduktan sonra bağlı olduğu alt ağ değiştirebilirsiniz ancak sanal ağı değiştiremezsiniz. Bir VM’ye bağlı her NIC’ye VM silinene kadar değişmeyen bir MAC adresi atanır.

Bu tabloda bir ağ arabirimi oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| Azure portalı | Azure portalında bir VM oluşturduğunuzda, sizin için otomatik olarak bir ağ arabirimi oluşturulur (ayrı olarak oluşturduğunuz bir NIC’yi kullanamazsınız). Portal yalnızca tek NIC’li bir VM oluşturur. Birden fazla NIC içeren bir VM oluşturmak istiyorsanız VM’yi farklı bir yöntemle oluşturmanız gerekir. |
| [Azure PowerShell](./windows/multiple-nics.md) | Daha önce oluşturduğunuz genel IP adresinin tanımlayıcısını sağlamak için [New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface) ile **-publicıpaddressid** parametresini kullanın. |
| [Azure CLI](./linux/multiple-nics.md) | Daha önce oluşturduğunuz genel IP adresinin tanımlayıcısını sağlamak için [az Network Nic Create](/cli/azure/network/nic) ' i **--Public-IP-Address** parametresiyle birlikte kullanın. |
| [Şablon](../virtual-network/template-samples.md) | Bir şablon kullanarak ağ arabirimi dağıtmak için [Genel IP Adresli bir Sanal Ağda Ağ Arabirimi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) konusunu bir kılavuz olarak kullanın. |

## <a name="ip-addresses"></a>IP adresleri 

Azure’daki bir NIC’ye şu tür [IP adresleri](../virtual-network/public-ip-addresses.md) atayabilirsiniz:

- **Genel IP adresleri**: İnternet’le ve bir VNet’e bağlı olmayan diğer Azure kaynaklarıyla gelen ve giden (ağ adresi çevirisi (NAT) olmadan) iletişimleri gerçekleştirmek için kullanılır. Bir NIC’ye genel IP adresi atanıp atanmayacağı isteğe bağlıdır. Genel IP adreslerinin nominal bir ücreti vardır ve her abonelik için kullanılabilecek maksimum sayı vardır.
- **Özel IP adresleri**: Bir VNet, şirket içi ağınız ve İnternet (NAT ile) içerisinde iletişim için kullanılır. VM’ye en az bir özel IP adresi atamalısınız. Azure’da NAT ile ilgili bilgi edinmek için [Azure’da giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md) konusunu okuyun.

VM’lere veya internet’e yönelik yük dengeleyicilere genel IP adresleri atayabilirsiniz. VM’lere ve iç yük dengeleyicilere özel IP adresleri atayabilirsiniz. VM’ye IP adresleri atamak için bir ağ arabirimi kullanılır.

Bir kaynağa IP adresi ayırmak için iki yöntem vardır: Dinamik veya statik. Varsayılan ayırma yöntemi, bir IP adresinin oluşturulduğu sırada ayrılmadığı yöntem olan dinamik yöntemdir. Bunun yerine, IP adresi bir VM oluşturduğunuzda bir VM’yi durdurduğunuzda ayrılır. VM’yi durdurduğunuzda veya sildiğinizde IP adresi serbest kalır. 

VM’nin IP adresinin aynı kalmasını sağlamak için ayırma yöntemini açıkça statik olarak ayarlayabilirsiniz. Bu durumda, anında bir IP adresi atanır. Adres, yalnızca VM’yi sildiğinizde veya ayırma yöntemini dinamik olarak değiştirdiğinizde serbest kalır.
    
Bu tabloda bir IP adresi oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| [Azure portalı](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Varsayılan olarak, genel IP adresleri dinamiktir ve VM durdurulduğunda ya da silindiğinde VM ile ilişkili adres değişebilir. VM’nin her zaman aynı genel IP adresini kullanmasını sağlamak için statik bir genel IP adresi oluşturun. Varsayılan olarak, bir VM oluşturulurken NIC’ye portal tarafından dinamik özel IP adresi atanır. VM oluşturulduktan sonra bu IP adresini statik olarak değiştirebilirsiniz.|
| [Azure PowerShell](../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) ' i **-Allocationmethod** parametresiyle dinamik veya statik olarak kullanırsınız. |
| [Azure CLI](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | [az network public-ip create](/cli/azure/network/public-ip) komutunu **--allocation-method** parametresi Dinamik veya Statik olacak şekilde kullanırsınız. |
| [Şablon](../virtual-network/template-samples.md) | Bir şablon kullanarak genel IP adresi dağıtmak için [Genel IP Adresli bir Sanal Ağda Ağ Arabirimi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) konusunu bir kılavuz olarak kullanın. |

Genel bir IP adresi oluşturduktan sonra bu adresi bir NIC’ye atayarak bir VM ile ilişkilendirebilirsiniz.

## <a name="virtual-network-and-subnets"></a>Sanal ağ ve alt ağlar

Alt ağ, sanal ağ içindeki bir IP adresleri aralığıdır. Bir sanal ağı organizasyon ve güvenlik için birden çok alt ağa bölebilirsiniz. Bir VM’deki her NIC, bir VNet’teki bir alt ağa bağlanır. Bir VNet içindeki alt ağlara (aynı veya farklı) bağlı NIC’ler, ek bir yapılandırma gerektirmeden birbirleriyle iletişim kurabilir.

Bir VNet ayarlarken kullanılabilir adres alanları ve alt ağlar da dahil olmak üzere ağın topolojisini siz belirtirsiniz. VNet diğer VNet’lere veya şirket içi ağlara bağlanacaksa birbiriyle çakışmayan adres aralıkları seçmeniz gerekir. IP adresleri özeldir ve Internet 'ten erişilemez. Bu, yalnızca 10.0.0.0/8, 172.16.0.0/12 veya 192.168.0.0/16 gibi yönlendirilebilir olmayan IP adresleri için geçerlidir. Azure artık tüm adres aralıklarını yalnızca VNet içerisinden, birbirine bağlı VNet’lerden ve şirket içi konumunuzdan erişilebilen özel VNet IP adresi alanının bir parçası olarak görür. 

İç ağlardan başka birinin sorumlu olduğu bir kurumda çalışıyorsanız adres alanınızı seçmeden önce bu kişiyle konuşmalısınız. Çakışma olmadığından emin olun ve kullanmak istediğiniz alanı, aynı IP adresi aralığını kullanmayı denemelerine izin verin. 

Varsayılan olarak alt ağlar arasında herhangi bir güvenlik sınırı olmadığından, bu alt ağların her birindeki VM’ler birbiriyle iletişim kurabilir. Bununla birlikte, alt ağların ve VM’lerin birbirleri arasındaki trafik akışını denetlemenize imkan sağlayan Ağ Güvenlik Grupları (NSG) ayarlayabilirsiniz. 

Bu tabloda, bir VNet ve alt ağlar oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.    

| Yöntem | Açıklama |
| ------ | ----------- |
| [Azure portalı](../virtual-network/quick-create-portal.md) | Bir VM oluştururken VNet oluşturma işlemini Azure’a bırakırsanız, ad VNet’i ve **-vnet**’i içeren kaynak grubunun adının bir birleşimi olur. Adres alanı 10.0.0.0/24, gerekli alt ağ adı **default** ve alt ağ adres aralığı 10.0.0.0/24’tür. |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | Bir alt ağ ve VNet oluşturmak için [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) ve [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) kullanın. Mevcut bir VNet 'e alt ağ eklemek için [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) komutunu da kullanabilirsiniz. |
| [Azure CLI](../virtual-network/quick-create-cli.md) | Alt ağ ve VNet aynı anda oluşturulur. [az network vnet create](/cli/azure/network/vnet) komutuna alt ağın adını içeren bir **--subnet-name** parametresi sağlayın. |
| Şablon | VNet ve alt ağları oluşturmanın en kolay yolu, [iki alt ağa sahip sanal ağ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)gibi var olan bir şablonu indirmenin yanı sıra gereksinimlerinize göre değiştirmektir. |

## <a name="network-security-groups"></a>Ağ güvenlik grupları

[Ağ güvenlik grubu (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md), alt ağlara, NIC’lere veya her ikisine yönelik ağ trafiğine izin veren veya trafiği reddeden Erişim Denetimi Listesi (ACL) kurallarının bir listesini içerir. NSG’ler alt ağlarla veya bir alt ağa bağlı tekil NIC’lerle örnekleriyle ilişkili olabilir. NSG bir alt ağ ile ilişkili olduğunda ACL kuralları bu alt ağdaki tüm VM’ler için geçerli olur. Ayrıca, tekil bir NIC’ye yönelik trafik, bir NSG’nin doğrudan bu NIC ile ilişkilendirilmesi yoluyla sınırlanabilir.

NSG'ler iki kural kümesi içerir: gelen ve giden. Bir kurala ait öncelik her küme içinde benzersiz olmalıdır. Her kuralın protokol, kaynak ve hedef bağlantı noktası aralıkları, adres ön ekleri, trafik yönü, öncelik ve erişim türü özellikleri vardır. 

Tüm NSG'ler bir varsayılan kurallar kümesini içerir. Varsayılan kurallar silinemez ancak en düşük önceliğe atanmış oldukları için sizin oluşturduğunuz kurallar tarafından geçersiz kılınabilirler. 

Bir NSG'yi bir NIC ile ilişkilendirdiğinizde, NSG'deki ağ erişim kuralları yalnızca bu NIC'ye uygulanır. Çok NIC’li bir VM’de bir NSG tek bir NIC’ye uygulandığı zaman diğer NIC’lere giden trafik etkilenmez. Farklı NSG’leri bir NIC ile (veya dağıtım modeline bağlı olarak VM ile) ve NIC’nin veya VM’nin bağlı olduğu ağ ile ilişkilendirebilirsiniz. Trafiğin yönüne bağlı olarak öncelik verilir.

VM’leriniz ve VNet’inizle ilgili plan yaptığınız sırada NSG’lerinizi de mutlaka [planlayın](../virtual-network/virtual-network-vnet-plan-design-arm.md).

Bu tabloda bir ağ güvenlik grubu oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| [Azure portalı](../virtual-network/tutorial-filter-network-traffic.md) | Azure portalında bir VM oluşturduğunuzda otomatik olarak bir NSG oluşturulur ve portalın oluşturduğu NIC ile ilişkilendirilir. NSG’nin adı, VM’nin adı ile **-nsg**’nin birleşiminde oluşur. Bu NSG, önceliği 1000 olan, hizmeti RDP olarak, protokolü TCP olarak, bağlantı noktası 3389 olarak ve eylemi İzin ver olarak ayarlanmış bir gelen kural içerir. VM’ye yönelik başka bir gelen trafiğe izin vermek istiyorsanız NSG’ye ek kurallar eklemeniz gerekir. |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) komutunu kullanın ve gerekli kural bilgilerini sağlayın. NSG 'yi oluşturmak için [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) kullanın. Alt ağ için NSG 'yi yapılandırmak üzere [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) komutunu kullanın. NSG 'yi VNet 'e eklemek için [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) kullanın. |
| [Azure CLI](../virtual-network/tutorial-filter-network-traffic-cli.md) | NSG’yi ilk olarak oluşturmak için [az network nsg create](/cli/azure/network/nsg) komutunu kullanın. NSG’ye kural eklemek için [az network nsg rule create](/cli/azure/network/nsg/rule) komutunu kullanın. NSG’yi alt ağa eklemek için [az network vnet subnet update](/cli/azure/network/vnet/subnet) komutunu kullanın. |
| [Şablon](../virtual-network/template-samples.md) | Bir şablon kullanarak ağ güvenlik grubu dağıtmak için [Ağ Güvenlik Grubu Oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) konusunu kılavuz olarak kullanın. |

## <a name="load-balancers"></a>Yük dengeleyiciler

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) uygulamalarınıza yüksek düzeyde kullanılabilirlik ve ağ performansı sunar. Bir yük dengeleyici, VM’lere yönelik [gelen İnternet trafiğini dengeleme](../load-balancer/components.md#frontend-ip-configurations) veya [bir VNet’teki VM’ler arasında trafiği dengeleme](../load-balancer/components.md#frontend-ip-configurations) rolünü üstlenecek şekilde yapılandırılabilir. Ayrıca, bir yük dengeleyici şirket içi bilgisayarlar ile şirketler arası VM’ler arasındaki trafiği dengeleme ya da dış trafiği belirli bir VM’ye yönlendirme rollerini üstlenebilir.

Yük dengeleyici, genel IP adresi ile yük dengeleyicideki bağlantı noktası arasında ve özel IP adresi ile VM’nin bağlantı noktası arasında gelen ve giden trafiği eşler.

Bir yük dengeleyici oluştururken şu yapılandırma öğelerini de dikkate almanız gerekir:

- **Ön uç IP yapılandırması** – bir yük dengeleyici bir veya daha fazla ön uç IP adresi içerebilir. Bu IP adresleri trafik için bir giriş işlevi görür.
- **Arka uç adres havuzu**: Yükün dağıtıldığı NIC ile ilişkili IP adresleri.
- **[Bağlantı noktası iletme](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** -gelen trafiğin ön uç IP 'si üzerinden nasıl akacağını ve gelen NAT kurallarından yararlanarak arka uç IP 'ye dağıtıldığını tanımlar.
- **Yük dengeleyici kuralları**: Belirli bir ön uç IP’si ve bağlantı noktası birleşimini bir dizi arka uç IP adresi ve bağlantı noktası bileşimiyle eşler. Tek bir yük dengeleyici birden çok dengeleme kuralına sahip olabilir. Her kural, bir ön uç IP’si ile bağlantı noktasının ve arka uç IP’si ile VM’lerle ilişkilendirilmiş bağlantı noktasının birleşiminden oluşur.
- **[Araştırmalar](../load-balancer/load-balancer-custom-probe-overview.md)**: VM’lerin durumunu izler. Bir araştırma yanıt veremediğinde, yük dengeleyici sağlıksız VM’ye yeni bağlantı göndermeyi durdurur. Mevcut bağlantılar bu durumdan etkilenmez ve yeni bağlantılar sağlıklı VM’lere gönderilir.
- **[Giden kuralları](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** -giden bir kural, ön uca çevrilecek standart Load Balancer arka uç havuzu tarafından tanımlanan tüm sanal makineler veya örnekler Için giden ağ adresi ÇEVIRISINI (NAT) yapılandırır.

Bu tabloda İnternet’e yönelik bir yük dengeleyici oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| Azure portalı |  [Azure Portal kullanarak, sanal makinelere internet trafiği dengeleyebilirsiniz](../load-balancer/quickstart-load-balancer-standard-public-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | Daha önce oluşturduğunuz genel IP adresinin tanımlayıcısını sağlamak için [New-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) komutunu **-publicıpaddress** parametresiyle birlikte kullanın. Arka uç adres havuzunun yapılandırmasını oluşturmak için [New-Azloadbalancerbackendadddresspoolconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) komutunu kullanın. Oluşturduğunuz ön uç IP yapılandırmasıyla ilişkili gelen NAT kuralları oluşturmak için [New-Azloadbalancerınboundnatrutaconfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) komutunu kullanın. İhtiyacınız olan araştırmaları oluşturmak için [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) komutunu kullanın. Yük dengeleyici yapılandırmasını oluşturmak için [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) komutunu kullanın. Yük dengeleyiciyi oluşturmak için [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) ' i kullanın.|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | İlk yük dengeleyici yapılandırmasını oluşturmak için [az network lb create](/cli/azure/network/lb) komutunu kullanın. Daha önce oluşturduğunuz genel IP adresini eklemek için [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) komutunu kullanın. Arka uç adres havuzunun yapılandırmasını eklemek için [az network lb address-pool create](/cli/azure/network/lb/address-pool) komutunu kullanın. NAT kuralları eklemek için [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) komutunu kullanın. Yük dengeleyici kurallarını eklemek için [az network lb rule create](/cli/azure/network/lb/rule) komutunu kullanın. Araştırmaları eklemek için [az network lb probe create](/cli/azure/network/lb/probe) komutunu kullanın. |
| [Şablon](../load-balancer/quickstart-load-balancer-standard-public-template.md) | Bir Load Balancer bir şablon kullanarak yük dengeleyici dağıtmak için kılavuz olarak [bir 3 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create) kullanın. |
    
Bu tabloda bir iç yük dengeleyici oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| Azure portalı | [Azure Portal bir yük dengeleyici ile iç trafik yükünü dengeleyebilirsiniz](../load-balancer/quickstart-load-balancer-standard-internal-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Ağ alt ağında özel bir IP adresi sağlamak için [New-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) komutunu **-privateıpaddress** parametresiyle birlikte kullanın. Arka uç adres havuzunun yapılandırmasını oluşturmak için [New-Azloadbalancerbackendadddresspoolconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) komutunu kullanın. Oluşturduğunuz ön uç IP yapılandırmasıyla ilişkili gelen NAT kuralları oluşturmak için [New-Azloadbalancerınboundnatrutaconfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) komutunu kullanın. İhtiyacınız olan araştırmaları oluşturmak için [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) komutunu kullanın. Yük dengeleyici yapılandırmasını oluşturmak için [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) komutunu kullanın. Yük dengeleyiciyi oluşturmak için [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) ' i kullanın.|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | İlk yük dengeleyici yapılandırmasını oluşturmak için [az network lb create](/cli/azure/network/lb) komutunu kullanın. Özel IP adresini tanımlamak için [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) komutunu **--private-ip-address** parametresiyle birlikte kullanın. Arka uç adres havuzunun yapılandırmasını eklemek için [az network lb address-pool create](/cli/azure/network/lb/address-pool) komutunu kullanın. NAT kuralları eklemek için [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) komutunu kullanın. Yük dengeleyici kurallarını eklemek için [az network lb rule create](/cli/azure/network/lb/rule) komutunu kullanın. Araştırmaları eklemek için [az network lb probe create](/cli/azure/network/lb/probe) komutunu kullanın.|
| [Şablon](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | Bir Load Balancer bir şablon kullanarak yük dengeleyici dağıtmak için kılavuz olarak [bir 2 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) kullanın. |

### <a name="virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleri

Yük dengeleyici ve sanal makine ölçek kümeleri hakkında daha fazla bilgi için bkz. [Azure sanal makine ölçek kümeleri Için ağ](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

## <a name="vms"></a>VM'ler

VM’ler aynı sanal ağda oluşturulabilir ve özel IP adreslerini kullanarak birbirine bağlanabilir. VM’ler, farklı alt ağlarda olsalar bile bir ağ geçidi yapılandırma ya da genel IP adresleri yapılandırma gerekstirmeden birbirine bağlanabilir. VM’leri bir VNet’e eklemek için VNet’i oluşturursunuz ve her VM’yi oluşturduğunuz sırada VNet’e ve alt ağa atarsınız. VM’ler ağ ayarlarını dağıtım veya başlatma sırasında alır.  

VM’ler dağıtıldığı sırada VM’lere bir IP adresi atanır. Bir VNet veya alt ağa birden çok VM dağıtırsanız, bunlara başlatıldıkları sırada IP adresleri atanır. Ayrıca, bir VM 'ye statik IP ayırabilirsiniz. Statik bir IP ayırdıysanız, başka bir VM için yanlışlıkla statik bir IP 'yi yeniden kullanmak zorunda kalmamak için belirli bir alt ağ kullanmayı göz önünde bulundurmanız gerekir.  

Bir VM oluşturur ve daha sonra bu VM’yi bir VNet’e geçirmek isterseniz bu işlem basit bir yapılandırma değişikliği değildir. VM’yi yeniden VNet’e dağıtmanız gerekir. Yeniden dağıtmanın en kolay yolu VM’ye bağlı diskleri silmeden VM’yi silmek ve özgün diskleri kullanarak VM’yi VNet’te yeniden oluşturmaktır. 

Bu tabloda, bir VNet’te VM oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| [Azure portalı](./windows/quick-create-portal.md) | Daha önce bahsedilen varsayılan ayarlarını kullanarak tek NIC’li bir VM oluşturur. Birden çok NIC içeren bir VM oluşturmak için farklı bir yöntem kullanmalısınız. |
| [Azure PowerShell](./windows/tutorial-manage-vm.md) | Daha önce oluşturduğunuz NIC 'yi VM yapılandırmasına eklemek için [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface) kullanımını içerir. |
| [Azure CLI](./linux/create-cli-complete.md) | Bir VM 'yi oluşturun ve tek bir adım olarak oluşturan bir VNet, alt ağ ve NIC 'ye bağlayın. |
| [Şablon](./windows/ps-template.md) | Bir şablon kullanarak VM dağıtmak için [Çok basit Windows VM dağıtımı](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) konusunu kılavuz olarak kullanın. |

## <a name="next-steps"></a>Sonraki adımlar
VM 'Ler için Azure sanal ağlarını yönetme hakkında VM 'ye özgü adımlar için bkz. [Windows](../virtual-machines/windows/tutorial-virtual-network.md) veya [Linux](../virtual-machines/linux/tutorial-virtual-network.md) öğreticileri.

Ayrıca, VM 'Lerin yük dengelenmesi ve [Windows](../virtual-machines/windows/tutorial-load-balancer.md) veya [Linux](../virtual-machines/linux/tutorial-load-balancer.md)için yüksek düzeyde kullanılabilir uygulamalar oluşturma konusunda öğreticiler de vardır.

- [Kullanıcı tanımlı yollar ve IP iletimi](../virtual-network/virtual-networks-udr-overview.md) yapılandırma hakkında bilgi edinin. 
- [VNet’ten VNet’e bağlantılar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) yapılandırma hakkında bilgi edinin.
- [Yollarla ilgili sorunları giderme](../virtual-network/diagnose-network-routing-problem.md) hakkında bilgi edinin.
- [Sanal makine ağ bant genişliği](../virtual-network/virtual-machine-network-throughput.md)hakkında daha fazla bilgi edinin.