---
title: "Azure’da sanal ağlar ve Windows sanal makineleri | Microsoft Belgeleri"
description: "Azure’da Windows sanal makineleri oluşturmaya ilişkin temel bilgiler bağlamında ağ hakkında bilgi edinin."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5493e9f7-7d45-4e98-be9a-657a53708746
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b3a69fdbdab2d7118135ed3e9d26aa01adb52e17
ms.lasthandoff: 03/31/2017


---

# <a name="virtual-networks-and-windows-virtual-machines-in-azure"></a>Azure’da sanal ağlar ve Windows sanal makineleri 

Azure sanal makinesi (VM) oluştururken bir [sanal ağ](../../virtual-network/virtual-networks-overview.md) (VNet) oluşturmanız ya da mevcut bir VNet’i kullanmanız gerekir. Sanal ağda VM’lerinize nasıl erişilmesini istediğinize de karar vermeniz gerekir. [Kaynakları oluşturmadan önce planlama yapmak](../../virtual-network/virtual-network-vnet-plan-design-arm.md) ve [ağ kaynaklarının sınırlarını](../../azure-subscription-service-limits.md#networking-limits) anladığınızdan emin olmak önemlidir.

Aşağıdaki şekilde VM’ler web sunucuları ve veritabanı sunucuları olarak temsil edilmektedir. Her VM kümesi, sanal ağdaki farklı alt ağlara atanmıştır.

![Azure sanal ağı](./media/network-overview/vnetoverview.png)

Dilerseniz VM oluşturmadan önce bir VNet oluşturabilir, dilerseniz de VNet’i VM oluşturduğunuz sırada oluşturabilirsiniz. Kendiniz bir VNet oluşturmalısınız veya VM oluşturduğunuz sırada sizin için bir VNet oluşturulur.

Bir VM ile iletişimi desteklemek için şu kaynakları oluşturursunuz:

- Ağ arabirimleri
- IP adresleri
- Sanal ağ ve alt ağlar

Bu temel kaynaklara ek olarak şu isteğe bağlı kaynakları da göz önünde bulundurmalısınız:

- Ağ güvenlik grupları
- Yük dengeleyiciler 

## <a name="network-interfaces"></a>Ağ arabirimleri

[Ağ arabirimi (NIC)](../../virtual-network/virtual-network-network-interface.md), bir VM ile bir sanal ağ (VNet) arasındaki çift yönlü bağlantıdır. Bir VM en az bir NIC içermelidir, ancak oluşturduğunuz VM’nin boyutuna bağlı olarak birden fazla NIC içerebilir. [Azure’da sanal makine boyutları](sizes.md) konusundan her VM boyutunun kaç NIC desteklediğini öğrenebilirsiniz. 

Birden fazla NIC içeren bir VM oluşturursanız VM’yi en az iki NIC ile oluşturmanız gerekir.  VM oluşturulduktan sonra VM boyutu tarafından desteklenen sayıya kadar ek NIC ekleyebilirsiniz, ancak tek NIC ile oluşturulmuş bir VM’ye, VM boyutunun kaç NIC desteklediğinden bağımsız olarak, ek NIC ekleyemezsiniz. 

VM bir kullanılabilirlik kümesine eklenmişse, kullanılabilirlik kümesindeki VM’lerin tamamı bir veya birden çok NIC içermelidir. Birden çok NIC içeren VM’lerin aynı sayıda NIC içermesi gerekmez, ancak hepsinin en az iki tane NIC’si olmalıdır.

Bir VM’ye bağlı her NIC’nin VM ile aynı konum ve abonelikte bulunması gerekir. Her NIC’nin NIC ile aynı Azure konumunda ve aboneliğinde bulunan bir VNet’e bağlanması gerekir. Bir NIC oluşturulduktan sonra NIC’nin bağlı olduğu alt ağ değiştirebilir, ancak bağlı olduğu VNet’i değiştiremezsiniz.  Bir VM’ye bağlı her NIC’ye VM silinene kadar değişmeyen bir MAC adresi atanır.

Bu tabloda bir ağ arabirimi oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| Azure portalına | Azure portalında bir VM oluşturduğunuzda, sizin için otomatik olarak bir ağ arabirimi oluşturulur (ayrı olarak oluşturduğunuz bir NIC’yi kullanamazsınız). Portal yalnızca tek NIC’li bir VM oluşturur. Birden fazla NIC içeren bir VM oluşturmak istiyorsanız VM’yi farklı bir yöntemle oluşturmanız gerekir. |
| [Azure PowerShell](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md) | Daha önce oluşturduğunuz genel IP adresinin tanımlayıcısını sağlamak için [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkInterface) komutunu **-PublicIpAddressId** parametresiyle birlikte kullanın. |
| [Azure CLI](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md) | Daha önce oluşturduğunuz genel IP adresinin tanımlayıcısını sağlamak için [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) komutunu **--public-ip-address** parametresi ile birlikte kullanın. |
| [Şablon](../../virtual-network/virtual-network-deploy-multinic-arm-template.md) | Bir şablon kullanarak ağ arabirimi dağıtmak için [Genel IP Adresli bir Sanal Ağda Ağ Arabirimi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) konusunu bir kılavuz olarak kullanın. |

## <a name="ip-addresses"></a>IP adresleri 

Azure’daki bir NIC’ye şu tür [IP adresleri](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) atayabilirsiniz:

- **Genel IP adresleri**: İnternet’le ve bir VNet’e bağlı olmayan diğer Azure kaynaklarıyla gelen ve giden (ağ adresi çevirisi (NAT) olmadan) iletişimleri gerçekleştirmek için kullanılır. Bir NIC’ye genel IP adresi atanıp atanmayacağı isteğe bağlıdır. Genel IP adreslerinin nominal bir ücreti vardır ve abonelik başına kullanılabilecek en fazla sayı sınırlaması vardır.
- **Özel IP adresleri**: Bir VNet, şirket içi ağınız ve İnternet (NAT ile) içerisinde iletişim için kullanılır. VM’ye en az bir özel IP adresi atamalısınız. Azure’da NAT ile ilgili bilgi edinmek için [Azure’da giden bağlantıları anlama](../../load-balancer/load-balancer-outbound-connections.md) konusunu okuyun.

VM’lere veya internet’e yönelik yük dengeleyicilere genel IP adresleri atayabilirsiniz. VM’lere ve iç yük dengeleyicilere özel IP adresleri atayabilirsiniz. VM’ye IP adresleri atamak için bir ağ arabirimi kullanılır.

Bir kaynağa IP adresi ayırmak için iki yöntem vardır: Dinamik veya statik. Varsayılan ayırma yöntemi, bir IP adresinin oluşturulduğu sırada ayrılmadığı yöntem olan dinamik yöntemdir. Bunun yerine, IP adresi bir VM oluşturduğunuzda bir VM’yi durdurduğunuzda ayrılır. VM’yi durdurduğunuzda veya sildiğinizde IP adresi serbest kalır. 

VM’nin IP adresinin aynı kalmasını sağlamak için ayırma yöntemini açıkça statik olarak ayarlayabilirsiniz. Bu durumda, anında bir IP adresi atanır. Adres, yalnızca VM’yi sildiğinizde veya ayırma yöntemini dinamik olarak değiştirdiğinizde serbest kalır.
    
Bu tabloda bir IP adresi oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| [Azure portal](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Varsayılan olarak, genel IP adresleri dinamiktir ve VM durdurulduğunda ya da silindiğinde VM ile ilişkili adres değişebilir. VM’nin her zaman aynı genel IP adresini kullanmasını sağlamak için statik bir genel IP adresi oluşturun. Varsayılan olarak, bir VM oluşturulurken NIC’ye portal tarafından dinamik özel IP adresi atanır. VM oluşturulduktan sonra bu adresi statik olarak değiştirebilirsiniz.|
| [Azure PowerShell](../../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmPublicIpAddress) komutunu **-AllocationMethod** parametresi Dinamik veya Statik olacak şekilde kullanırsınız. |
| [Azure CLI](../../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) komutunu **--allocation-method** parametresi Dinamik veya Statik olacak şekilde kullanırsınız. |
| [Şablon](../../virtual-network/virtual-network-deploy-static-pip-arm-template.md) | Bir şablon kullanarak genel IP adresi dağıtmak için [Genel IP Adresli bir Sanal Ağda Ağ Arabirimi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) konusunu bir kılavuz olarak kullanın. |

Genel bir IP adresi oluşturduktan sonra bu adresi bir NIC’ye atayarak bir VM ile ilişkilendirebilirsiniz.

## <a name="virtual-network-and-subnets"></a>Sanal ağ ve alt ağlar

Alt ağ, sanal ağ içindeki bir IP adresleri aralığıdır. Bir sanal ağı organizasyon ve güvenlik için birden çok alt ağa bölebilirsiniz. Bir VM’deki her NIC, bir VNet’teki bir alt ağa bağlanır. Bir VNet içindeki alt ağlara (aynı veya farklı) bağlı NIC’ler, ek bir yapılandırma gerektirmeden birbirleriyle iletişim kurabilir.

Bir VNet ayarlarken kullanılabilir adres alanları ve alt ağlar da dahil olmak üzere ağın topolojisini siz belirtirsiniz. VNet diğer VNet’lere veya şirket içi ağlara bağlanacaksa birbiriyle çakışmayan adres aralıkları seçmeniz gerekir. IP adresleri özeldir ve bu ağlara İnternet’ten erişilemez (yalnızca 10.0.0.0/8, 172.16.0.0/12 veya 192.168.0.0/16 gibi yönlendirilemeyen IP adresleri için geçerli). Azure artık tüm adres aralıklarını yalnızca VNet içerisinden, birbirine bağlı VNet’lerden ve şirket içi konumunuzdan erişilebilen özel VNet IP adresi alanının bir parçası olarak görür. 

İç ağlardan başka birinin sorumlu olduğu bir kurumda çalışıyorsanız adres alanınızı seçmeden önce bu kişiyle konuşmalısınız. Herhangi bir çakışma olmadığından emin olun ve sorumlu kişinin aynı IP adresi aralığını kullanmaya çalışmaması için bu kişiye hangi alanı kullanmak istediğinizi bildirin. 

Varsayılan olarak alt ağlar arasında herhangi bir güvenlik sınırı olmadığından, bu alt ağların her birindeki VM’ler birbiriyle iletişim kurabilir. Bununla birlikte, alt ağların ve VM’lerin birbirleri arasındaki trafik akışını denetlemenize imkan sağlayan Ağ Güvenlik Grupları (NSG) ayarlayabilirsiniz. 

Bu tabloda, bir VNet ve alt ağlar oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.    

| Yöntem | Açıklama |
| ------ | ----------- |
| [Azure Portal](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md) | Bir VM oluştururken VNet oluşturma işlemini Azure’a bırakırsanız, ad VNet’i ve **-vnet**’i içeren kaynak grubunun adının bir birleşimi olur. Adres alanı 10.0.0.0/24, gerekli alt ağ adı **default** ve alt ağ adres aralığı 10.0.0.0/24’tür. |
| [Azure PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md) | Bir alt ağ ve VNet oluşturmak için [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmVirtualNetworkSubnetConfig) ve [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmVirtualNetwork) komutlarını kullanırsınız. Ayrıca, mevcut bir VNet’e alt ağ eklemek için [Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/Add-AzureRmVirtualNetworkSubnetConfig) komutunu da kullanabilirsiniz. |
| [Azure CLI](../../virtual-network/virtual-networks-create-vnet-arm-cli.md) | Alt ağ ve VNet aynı anda oluşturulur. [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) komutuna alt ağın adını içeren bir **--subnet-name** parametresi sağlayın. |
| [Şablon](../../virtual-network/virtual-networks-create-vnet-arm-template-click.md) | VNet ve alt ağlar oluşturmanın en kolay yolu, [İki Alt Ağlı Sanal Ağ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets) gibi mevcut bir şablonu indirerek kendi gereksinimlerinize göre değiştirmektir. |

## <a name="network-security-groups"></a>Ağ güvenlik grupları

[Ağ güvenlik grubu (NSG)](../../virtual-network/virtual-networks-nsg.md), alt ağlara, NIC’lere veya her ikisine yönelik ağ trafiğine izin veren veya trafiği reddeden Erişim Denetimi Listesi (ACL) kurallarının bir listesini içerir. NSG’ler alt ağlarla veya bir alt ağa bağlı tekil NIC’lerle örnekleriyle ilişkili olabilir. NSG bir alt ağ ile ilişkili olduğunda ACL kuralları bu alt ağdaki tüm VM’ler için geçerli olur. Ayrıca, tekil bir NIC’ye yönelik trafik, bir NSG’nin doğrudan bu NIC ile ilişkilendirilmesi yoluyla sınırlanabilir.

NSG'ler iki kural kümesi içerir: gelen ve giden. Bir kurala ait öncelik her küme içinde benzersiz olmalıdır. Her kuralın protokol, kaynak ve hedef bağlantı noktası aralıkları, adres ön ekleri, trafik yönü, öncelik ve erişim türü özellikleri vardır. 

Tüm NSG'ler bir varsayılan kurallar kümesini içerir. Varsayılan kurallar silinemez ancak en düşük önceliğe atanmış oldukları için sizin oluşturduğunuz kurallar tarafından geçersiz kılınabilirler. 

Bir NSG'yi bir NIC ile ilişkilendirdiğinizde, NSG'deki ağ erişim kuralları yalnızca bu NIC'ye uygulanır. Çok NIC’li bir VM’de bir NSG tek bir NIC’ye uygulandığı zaman diğer NIC’lere giden trafik etkilenmez. Farklı NSG’leri bir NIC ile (veya dağıtım modeline bağlı olarak VM ile) ve NIC’nin veya VM’nin bağlı olduğu ağ ile ilişkilendirebilirsiniz. Trafiğin yönüne bağlı olarak öncelik verilir.

VM’leriniz ve VNet’inizle ilgili plan yaptığınız sırada NSG’lerinizi de mutlaka [planlayın](../../virtual-network/virtual-networks-nsg.md#planning).

Bu tabloda bir ağ güvenlik grubu oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| [Azure portal](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md) | Azure portalında bir VM oluşturduğunuzda otomatik olarak bir NSG oluşturulur ve portalın oluşturduğu NIC ile ilişkilendirilir. NSG’nin adı, VM’nin adı ile **-nsg**’nin birleşiminde oluşur. Bu NSG, önceliği 1000 olan, hizmeti RDP olarak, protokolü TCP olarak, bağlantı noktası 3389 olarak ve eylemi İzin ver olarak ayarlanmış bir gelen kural içerir. VM’ye yönelik başka bir gelen trafiğe izin vermek istiyorsanız NSG’ye ek kurallar eklemeniz gerekir. |
| [Azure PowerShell](../../virtual-network/virtual-networks-create-nsg-arm-ps.md) | [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkSecurityRuleConfig) komutunu kullanın ve gerekli kural bilgilerini sağlayın. NSG’yi oluşturmak için [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkSecurityGroup) komutunu kullanın. NSG’yi alt ağa yönelik olarak yapılandırmak için [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/Set-AzureRmVirtualNetworkSubnetConfig) komutunu kullanın. NSG’yi VNet’e eklemek için [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/Set-AzureRmVirtualNetwork?redirectedfrom=msdn) komutunu kullanın. |
| [Azure CLI](../../virtual-network/virtual-networks-create-nsg-arm-cli.md) | NSG’yi ilk olarak oluşturmak için [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) komutunu kullanın. NSG’ye kural eklemek için [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) komutunu kullanın. NSG’yi alt ağa eklemek için [az network vnet subnet update](https://docs.microsoft.com/en-us/cli/azure/network/vnet/subnet#update) komutunu kullanın. |
| [Şablon](../../virtual-network/virtual-networks-create-nsg-arm-template.md) | Bir şablon kullanarak ağ güvenlik grubu dağıtmak için [Ağ Güvenlik Grubu Oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) konusunu kılavuz olarak kullanın. |

## <a name="load-balancers"></a>Yük dengeleyiciler

[Azure Load Balancer](../../load-balancer/load-balancer-overview.md), uygulamalarınıza yüksek düzeyde kullanılabilirlik ve ağ performansı sağlar. Bir yük dengeleyici, VM’lere yönelik [gelen İnternet trafiğini dengeleme](../../load-balancer/load-balancer-internet-overview.md) veya [bir VNet’teki VM’ler arasında trafiği dengeleme](../../load-balancer/load-balancer-internal-overview.md) rolünü üstlenecek şekilde yapılandırılabilir. Ayrıca, bir yük dengeleyici şirket içi bilgisayarlar ile şirketler arası VM’ler arasındaki trafiği dengeleme ya da dış trafiği belirli bir VM’ye yönlendirme rollerini üstlenebilir.

Yük dengeleyici, genel IP adresi ile yük dengeleyicideki bağlantı noktası arasında ve özel IP adresi ile VM’nin bağlantı noktası arasında gelen ve giden trafiği eşler.

Bir yük dengeleyici oluştururken şu yapılandırma öğelerini de dikkate almanız gerekir:

- **Ön uç IP yapılandırması**: Bir yük dengeleyici, sanal IP’ler (VIP) olarak da bilinen bir veya daha fazla ön uç IP adresi içerebilir. Bu IP adresleri trafik için bir giriş işlevi görür.
- **Arka uç adres havuzu**: Yükün dağıtıldığı NIC ile ilişkili IP adresleri.
- **NAT kuralları**: Gelen trafik akışının ön uç IP’sinden geçip arka uç IP’sine nasıl dağıtılacağını tanımlar.
- **Yük dengeleyici kuralları**: Belirli bir ön uç IP’si ve bağlantı noktası birleşimini bir dizi arka uç IP adresi ve bağlantı noktası bileşimiyle eşler. Tek bir yük dengeleyici birden çok dengeleme kuralına sahip olabilir. Her kural, bir ön uç IP’si ile bağlantı noktasının ve arka uç IP’si ile VM’lerle ilişkilendirilmiş bağlantı noktasının birleşiminden oluşur.
- **[Araştırmalar](../../load-balancer/load-balancer-custom-probe-overview.md)**: VM’lerin durumunu izler. Bir araştırma yanıt veremediğinde, yük dengeleyici sağlıksız VM’ye yeni bağlantı göndermeyi durdurur. Mevcut bağlantılar bu durumdan etkilenmez ve yeni bağlantılar sağlıklı VM’lere gönderilir.

Bu tabloda İnternet’e yönelik bir yük dengeleyici oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| Azure portalına | Şu anda Azure portalını kullanarak İnternet’e yönelik bir yük dengeleyici oluşturamazsınız. |
| [Azure PowerShell](../../load-balancer/load-balancer-get-started-internet-arm-ps.md) | Daha önce oluşturduğunuz genel IP adresinin tanımlayıcısını sağlamak için [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerFrontendIpConfig) komutunu **-PublicIpAddress** parametresiyle birlikte kullanın. Arka uç adres havuzunun yapılandırmasını oluşturmak için [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerBackendAddressPoolConfig) komutunu kullanın. Oluşturduğunuz ön uç IP yapılandırmasıyla ilişkili gelen NAT kuralları oluşturmak için [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerInboundNatRuleConfig) komutunu kullanın. İhtiyacınız olan araştırmaları oluşturmak için [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerProbeConfig) komutunu kullanın. Yük dengeleyici yapılandırmasını oluşturmak için [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerRuleConfig) komutunu kullanın. Yük dengeleyiciyi oluşturmak için [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancer) komutunu kullanın.|
| [Azure CLI](../../load-balancer/load-balancer-get-started-internet-arm-cli.md) | İlk yük dengeleyici yapılandırmasını oluşturmak için [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) komutunu kullanın. Daha önce oluşturduğunuz genel IP adresini eklemek için [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) komutunu kullanın. Arka uç adres havuzunun yapılandırmasını eklemek için [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) komutunu kullanın. NAT kuralları eklemek için [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) komutunu kullanın. Yük dengeleyici kurallarını eklemek için [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) komutunu kullanın. Araştırmaları eklemek için [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) komutunu kullanın. |
| [Şablon](../../load-balancer/load-balancer-get-started-internet-arm-template.md) | Bir şablon kullanarak yük dengeleyici dağıtmak için [Bir Yük Dengeleyici’de 2 VM ve LB’de NAT kuralları yapılandırma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-natrules) konusunu kılavuz olarak kullanın. |
    
Bu tabloda bir iç yük dengeleyici oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| Azure portalına | Şu anda Azure portalını kullanarak bir iç yük dengeleyici oluşturamazsınız. |
| [Azure PowerShell](../../load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Ağ alt ağında özel bir IP adresi sağlamak için [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerFrontendIpConfig) komutunu **-PrivateIpAddress** parametresiyle birlikte kullanın. Arka uç adres havuzunun yapılandırmasını oluşturmak için [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerBackendAddressPoolConfig) komutunu kullanın. Oluşturduğunuz ön uç IP yapılandırmasıyla ilişkili gelen NAT kuralları oluşturmak için [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerInboundNatRuleConfig) komutunu kullanın. İhtiyacınız olan araştırmaları oluşturmak için [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerProbeConfig) komutunu kullanın. Yük dengeleyici yapılandırmasını oluşturmak için [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerRuleConfig) komutunu kullanın. Yük dengeleyiciyi oluşturmak için [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancer) komutunu kullanın.|
| [Azure CLI](../../load-balancer/load-balancer-get-started-ilb-arm-cli.md) | İlk yük dengeleyici yapılandırmasını oluşturmak için [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) komutunu kullanın. Özel IP adresini tanımlamak için [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) komutunu **--private-ip-address** parametresiyle birlikte kullanın. Arka uç adres havuzunun yapılandırmasını eklemek için [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) komutunu kullanın. NAT kuralları eklemek için [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) komutunu kullanın. Yük dengeleyici kurallarını eklemek için [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) komutunu kullanın. Araştırmaları eklemek için [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) komutunu kullanın.|
| [Şablon](../../load-balancer/load-balancer-get-started-ilb-arm-template.md) | Bir şablon kullanarak yük dengeleyici dağıtmak için [Bir Yük Dengeleyici’de 2 VM ve LB’de NAT kuralları yapılandırma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) konusunu kılavuz olarak kullanın. |

## <a name="vms"></a>VM'ler

VM’ler aynı sanal ağda oluşturulabilir ve özel IP adreslerini kullanarak birbirine bağlanabilir. VM’ler, farklı alt ağlarda olsalar bile bir ağ geçidi yapılandırma ya da genel IP adresleri yapılandırma gerekstirmeden birbirine bağlanabilir. VM’leri bir VNet’e eklemek için VNet’i oluşturursunuz ve her VM’yi oluşturduğunuz sırada VNet’e ve alt ağa atarsınız. VM’ler ağ ayarlarını dağıtım veya başlatma sırasında alır.  

VM’ler dağıtıldığı sırada VM’lere bir IP adresi atanır. Bir VNet veya alt ağa birden çok VM dağıtırsanız, bunlara başlatıldıkları sırada IP adresleri atanır. Bir VM ile ilişkili iç IP adresi dinamik bir IP adresidir (DIP). Bir VM’ye statik bir DIP ayırabilirsiniz. Statik bir DIP ayırmanız durumunda, yanlışlıkla başka bir VM’nin statik DIP’sini kullanmaktan kaçınmak için belirli bir alt ağı kullanmayı göz önünde bulundurun.  

Bir VM oluşturur ve daha sonra bu VM’yi bir VNet’e geçirmek isterseniz bu işlem basit bir yapılandırma değişikliği değildir. VM’yi yeniden VNet’e dağıtmanız gerekir. Yeniden dağıtmanın en kolay yolu VM’ye bağlı diskleri silmeden VM’yi silmek ve özgün diskleri kullanarak VM’yi VNet’te yeniden oluşturmaktır. 

Bu tabloda, bir VNet’te VM oluşturmak için kullanabileceğiniz yöntemler listelenmiştir.

| Yöntem | Açıklama |
| ------ | ----------- |
| [Azure portal](../virtual-machines-windows-hero-tutorial.md) | Daha önce bahsedilen varsayılan ayarlarını kullanarak tek NIC’li bir VM oluşturur. Birden çok NIC içeren bir VM oluşturmak için farklı bir yöntem kullanmalısınız. |
| [Azure PowerShell](../virtual-machines-windows-ps-create.md) | Daha önce oluşturduğunuz NIC’nin VM yapılandırmasına eklenmesi için [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v1.3.4/Add-AzureRmVMNetworkInterface) komutunun kullanılmasını gerektirir. |
| [Şablon](ps-template.md) | Bir şablon kullanarak VM dağıtmak için [Çok basit Windows VM dağıtımı](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) konusunu kılavuz olarak kullanın. |

## <a name="next-steps"></a>Sonraki adımlar

- [Kullanıcı tanımlı yollar ve IP iletimi](../../virtual-network/virtual-networks-udr-overview.md) yapılandırma hakkında bilgi edinin. 
- [VNet’ten VNet’e bağlantılar](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) yapılandırma hakkında bilgi edinin.
- [Yollarla ilgili sorunları giderme](../../virtual-network/virtual-network-routes-troubleshoot-portal.md) hakkında bilgi edinin.

