---
title: Öğretici-Windows VM 'Leri için Azure sanal ağları oluşturma ve yönetme
description: Bu öğreticide, Azure PowerShell kullanarak Windows sanal makineleri için Azure sanal ağları oluşturup yönetmeyi öğrenirsiniz
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 67cfb04f67e3454bde25969b634116f2871cbeb5
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74064757"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Öğretici - Windows VM’ler için Azure sanal ağları oluşturma ve yönetme | Microsoft Docs

Azure sanal makineleri, iç ve dış ağ iletişimi için Azure ağını kullanır. Bu öğretici, iki sanal makineyi dağıtma ve bu VM’ler için Azure ağını yapılandırma konusunda rehberlik sunar. Bu öğreticideki örneklerde, VM 'Lerin bir veritabanı arka ucu olan bir Web uygulamasını barındırmakta olduğu, ancak öğreticide bir uygulamanın dağıtılmadığı varsayılır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ ve alt ağ oluşturma
> * Genel IP adresi oluşturma
> * Ön uç VM’si oluşturma
> * Ağ trafiğinin güvenliğini sağlama
> * Arka uç VM’si oluşturma


## <a name="vm-networking-overview"></a>VM ağına genel bakış

Azure sanal ağları, sanal makineler ile İnternet ve Azure SQL veritabanı gibi diğer Azure hizmetleri arasında güvenli ağ bağlantıları kurulmasını sağlar. Sanal ağlar, alt ağ adı verilen mantıksal segmentlere ayrılır. Alt ağlar, ağ akışını denetlemek için ve güvenlik sınırı olarak kullanılır. Bir VM dağıtılırken, genellikle bir alt ağa eklenmiş sanal ağ arabirimine sahiptir.

Bu öğreticiyi tamamladığınızda şu kaynakların oluşturulduğunu görebilirsiniz:

![İki alt ağ içeren sanal ağ](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM’lerin birbirleriyle ve İnternet’le iletişim kurmak için kullandığı sanal ağ.
- *myFrontendSubnet* - Ön uç kaynakları tarafından kullanılan *myVNet*’teki alt ağ.
- *myPublicIPAddress* - İnternet’ten *myFrontendVM*’ye erişmek için kullanılan genel IP adresi.
- *myFrontendNic* - *myBackendVM* ile iletişim kurmak için *myFrontendVM* tarafından kullanılan ağ arabirimi.
- *myFrontendVM* - İnternet ile *myBackendVM* arasında iletişim kurmak için kullanılan VM.
- *myBackendNSG* - *myFrontendVM* ile *myBackendVM* arasındaki iletişimi denetleyen ağ güvenlik grubu.
- *myBackendSubnet* - *myBackendNSG* ile ilişkilendirilmiş ve arka uç kaynakları tarafından kullanılan alt ağ.
- *myBackendNic* - *myFrontendVM* ile iletişim kurmak için *myBackendVM* tarafından kullanılan ağ arabirimi.
- *myBackendVM* - 1433 numaralı bağlantı noktasını kullanarak *myFrontendVM* ile iletişim kuran VM.


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell'i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. İsterseniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) adresine giderek Cloud Shell'i ayrı bir tarayıcı sekmesinde de başlatabilirsiniz. **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.


## <a name="create-subnet"></a>Alt ağ oluşturma 

Bu öğreticide iki alt ağa sahip tek bir sanal ağ oluşturulur. Web uygulamasını barındırmak için bir ön uç alt ağı ve veritabanı sunucusunu barındırmak için bir arka uç alt ağı.

Bir sanal ağ oluşturabilmeniz için, [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnekte *EastUS* konumunda *myRGNetwork* adlı bir kaynak grubu oluşturulur:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

[New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig)kullanarak *myfrontendsubnet* adlı bir alt ağ yapılandırması oluşturun:

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Sonra, *myBackendSubnet* adlı bir alt ağ yapılandırması oluşturun:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Sanal ağ oluşturma

[New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)kullanarak *myfrontendsubnet* ve *mybackendsubnet* kullanarak *myvnet* adlı bir sanal ağ oluşturun:

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Bu noktada ağ oluşturulur ve biri ön uç hizmetlerine, diğeri ise arka uç hizmetlerine yönelik olan iki alt ağ segmentine ayrılır. Sonraki bölümde sanal makineler oluşturulacak ve bu alt ağlara bağlanacak.

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Genel IP adresi, Azure kaynaklarına İnternet’ten erişilmesine izin verir. Genel IP adresi ayırma yöntemi dinamik veya statik olarak yapılandırılabilir. Genel IP adresi varsayılan olarak dinamik biçimde ayrılır. Bir VM serbest bırakıldığında dinamik IP adresleri de serbest bırakılır. Bu davranış, VM’nin serbest bırakılmasını içeren tüm işlemlerde IP adresinin değişmesine neden olur.

Ayırma yöntemi statik olarak ayarlanabilir, bu, serbest bırakılmış bir durum sırasında bile IP adresinin bir VM 'ye atanmasını sağlar. Statik bir IP adresi kullanıyorsanız, IP adresi belirtilemez. Bunun yerine, kullanılabilir bir adres havuzundan ayrılır.

[New-Azpublicıpaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress)kullanarak *Mypublicıpaddress* adlı bir genel IP adresi oluşturun:

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Statik bir genel IP adresi atamak için AllocationMethod parametresini `Static` olarak değiştirebilirsiniz.

## <a name="create-a-front-end-vm"></a>Ön uç VM’si oluşturma

Bir VM’nin sanal ağ içerisinde iletişim kurabilmesi için bir sanal ağ arabirimine (NIC) sahip olması gerekir. [New-Aznetworkınterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface)kullanarak bir NIC oluşturun:

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) komutunu kullanarak VM’de yönetici hesabı için gereken kullanıcı adı ve parolasını ayarlayın. Ek adımlarda VM’ye bağlanmak için bu kimlik bilgilerini kullanacaksınız:

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)kullanarak VM 'ler oluşturun.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Ağ trafiğinin güvenliğini sağlama

Ağ güvenlik grubu (NSG), Azure Sanal Ağlara (VNet) bağlı kaynaklara ağ trafiğine izin veren veya reddeden güvenlik kurallarının listesini içerir. NSG’ler alt ağlarla veya tek tek ağ arabirimleriyle ilişkilendirilebilir. Bir NSG, bir ağ arabirimiyle ilişkilendirilir ve yalnızca ilişkili VM için geçerlidir. Bir NSG bir alt ağ ile ilişkilendirildiğinde kurallar alt ağa bağlı tüm kaynaklar için geçerli olur.

### <a name="network-security-group-rules"></a>Ağ güvenlik grubu kuralları

NSG kuralları trafiğe izin verilen veya trafiğin engellendiği ağ bağlantı noktalarını tanımlar. Trafiğin belirli sistemler veya alt ağlar arasında denetlenmesi için kurallar, kaynak ve hedef IP adresi aralıkları içerebilir. Ayrıca NSG kuralları öncelik (1 ile 4.096 arasında) içerir. Kurallar öncelik sırasına göre değerlendirilir. 100 önceliğine sahip bir kural, 200 önceliğine sahip kuraldan önce değerlendirilir.

Tüm NSG'ler bir varsayılan kurallar kümesini içerir. Varsayılan kurallar silinemez, ancak en düşük önceliğe atandıklarından, oluşturduğunuz kurallar tarafından geçersiz kılınabilir.

- **Sanal ağ** - Kaynağı bir sanal ağ olan ve bir sanal ağda biten trafiğe hem gelen hem de giden yönlerde izin verilir.
- **İnternet** - Giden trafiğe izin verilir, ancak gelen trafik engellenir.
- **Yük dengeleyici** - VM’lerinizin ve rol örneklerinizin sistem durumunu araştıran Azure yük dengeleyicisine izin verir. Yük dengeli bir küme kullanmıyorsanız bu kuralı geçersiz kılabilirsiniz.

### <a name="create-network-security-groups"></a>Ağ güvenlik grupları oluşturma

[New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig)kullanarak *Myfrontendvm* üzerinde gelen Web trafiğine Izin vermek Için *myfrontendnsgrule* adlı bir gelen kural oluşturun:

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Arka uç alt ağı için bir NSG oluşturarak *myBackendVM*’ye gelen iç trafiği yalnızca *myFrontendVM*’den gelecek şekilde sınırlayabilirsiniz. Aşağıdaki örnekte *myBackendNSGRule* adlı bir kural oluşturulur:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

[New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup)kullanarak *Myfrontendnsg* adlı bir ağ güvenlik grubu ekleyin:

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Şimdi New-AzNetworkSecurityGroup kullanarak *Mybackendnsg* adlı bir ağ güvenlik grubu ekleyin:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Ağ güvenlik gruplarını alt ağlara ekleyin:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Arka uç VM’si oluşturma

Bu öğretici için gerekli arka uç VM’yi oluşturmanın en kolay yolu bir SQL Server görüntüsü kullanmaktır. Bu öğreticide yalnızca veritabanı sunucusu ile VM oluşturulurken, veritabanına erişim hakkında bilgi sağlanmaz.

*myBackendNic* öğesini oluşturun:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Get-Credential komutuyla VM’de yönetici hesabı için gereken kullanıcı adı ve parolasını ayarlayın:

```azurepowershell-interactive
$cred = Get-Credential
```

*myBackendVM* öğesini oluşturun.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Bu örnekteki görüntüde yüklü SQL Server vardır, ancak bu öğreticide kullanılmaz. Bir sanal makineyi, Web trafiğini işlemek için bir VM 'yi ve veritabanı yönetimini işlemek için bir VM 'yi nasıl yapılandırabileceğini göstermek için eklenmiştir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide sanal makinelerle ilgili Azure ağlarını oluşturup ve güvenliğini sağladınız. 

> [!div class="checklist"]
> * Sanal ağ ve alt ağ oluşturma
> * Genel IP adresi oluşturma
> * Ön uç VM’si oluşturma
> * Ağ trafiğinin güvenliğini sağlama
> * Arka uç VM’si oluşturma

Azure Backup kullanarak sanal makinelerdeki verilerin güvenliğini izlemeyi öğrenmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Windows sanal makinelerini Azure’da yedekleyin](./tutorial-backup-vms.md)
