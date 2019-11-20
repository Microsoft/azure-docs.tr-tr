---
title: Bir Azure sanal ağında alt ağ temsili ekleme veya kaldırma
titlesuffix: Azure Virtual Network
description: Azure 'da bir hizmet için Temsilcili bir alt ağ ekleme veya kaldırma hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 340c9192b43cbcf2daacfb791d85135518dd970c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747890"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Alt ağ temsili ekleme veya kaldırma

Alt ağ temsili, hizmeti dağıtma sırasında benzersiz bir tanımlayıcı kullanarak alt ağda hizmete özel kaynaklar oluşturmak için hizmete açık izinler verir. Bu makalede, bir Azure hizmeti için Temsilcili bir alt ağın nasıl ekleneceği veya kaldırılacağı açıklanır.

## <a name="portal"></a>Portal

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

Bu bölümde, bir sanal ağ ve daha sonra bir Azure hizmetine temsilci olarak kullanacağınız alt ağ oluşturursunuz.

1. Ekranın sol üst kısmında, **kaynak oluştur** > **ağ** > **sanal ağ**' ı seçin.
1. **Sanal ağ oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | *MyVirtualNetwork*girin. |
    | Adres alanı | *10.0.0.0/16*girin. |
    | Abonelik | Aboneliğinizi seçin.|
    | Kaynak grubu | **Yeni oluştur**' u seçin, *myresourcegroup*yazın ve ardından **Tamam**' ı seçin. |
    | Konum | **EastUS**öğesini seçin.|
    | Alt ağ adı | *Mysubnet*yazın. |
    | Alt Ağ - Adres aralığı | *10.0.0.0/24*girin. |
    |||
1. Kalanı varsayılan olarak bırakın ve ardından **Oluştur**' u seçin.

### <a name="permissions"></a>İzinler

Bir Azure hizmetine temsilci seçmek istediğiniz alt ağı oluşturmadıysanız, aşağıdaki izne sahip olmanız gerekir: `Microsoft.Network/virtualNetworks/subnets/write`.

Yerleşik [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolü de gerekli izinleri içerir.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Bir Azure hizmetine alt ağ atama

Bu bölümde, önceki bölümde oluşturduğunuz alt ağı bir Azure hizmetine devredebilirsiniz.

1. Portalın arama çubuğunda *myVirtualNetwork*girin. Arama sonuçlarında **myVirtualNetwork** göründüğünde seçin.
2. Arama sonuçlarında *myVirtualNetwork*' yi seçin.
3. **Ayarlar**altında **alt ağlar**' ı seçin ve ardından **mysubnet**' i seçin.
4. *Mysubnet* sayfasındaki **alt ağ temsilcisi** listesi için, **alt ağı bir hizmete devretmek** (örneğin, **Microsoft. dbforpostgresql/serversv2**) altında listelenen hizmetlerden seçim yapın.  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure hizmetinden alt ağ temsilcisini kaldırma

1. Portalın arama çubuğunda *myVirtualNetwork*girin. Arama sonuçlarında **myVirtualNetwork** göründüğünde seçin.
2. Arama sonuçlarında *myVirtualNetwork*' yi seçin.
3. **Ayarlar**altında **alt ağlar**' ı seçin ve ardından **mysubnet**' i seçin.
4. *Mysubnet* sayfasında **alt ağ temsilcisi** listesi için **alt ağ temsilcisi**altında listelenen hizmetlerden **hiçbiri** ' ni seçin. 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Bunun yerine Azure CLı 'yı yüklemeye ve kullanmaya karar verirseniz, bu makale Azure CLı sürüm 2.0.28 veya üstünü kullanmanızı gerektirir. Yüklü sürümünüzü bulmak için `az --version`çalıştırın. Bkz. Install veya Upgrade Info for [Azure CLI](/cli/azure/install-azure-cli) .

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
[az group create](https://docs.microsoft.com/cli/azure/group) ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek **eastus** konumunda **myResourceGroup** adlı bir kaynak grubu oluşturur:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma
**az network vnet create** komutunu kullanarak **myResourceGroup** içinde **mySubnet** adlı bir alt ağ ile [myVnet](https://docs.microsoft.com/cli/azure/network/vnet) adlı bir sanal ağ oluşturun.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>İzinler

Bir Azure hizmetine temsilci seçmek istediğiniz alt ağı oluşturmadıysanız, aşağıdaki izne sahip olmanız gerekir: `Microsoft.Network/virtualNetworks/subnets/write`.

Yerleşik [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolü de gerekli izinleri içerir.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Bir Azure hizmetine alt ağ atama

Bu bölümde, önceki bölümde oluşturduğunuz alt ağı bir Azure hizmetine devredebilirsiniz. 

**Mysubnet** adlı alt ağı bir Azure hizmetine temsilciyle güncelleştirmek için [az Network VNET subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) kullanın.  Bu örnekte, örnek temsili için **Microsoft. DBforPostgreSQL/serversv2** kullanılmıştır:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet
  --vnet-name myVnet
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Temsilinin uygulandığını doğrulamak için [az Network VNET subnet Show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)kullanın. Hizmetin, **HizmetAdı**özelliği altındaki alt ağa atanmış olduğunu doğrulayın:

```azurecli-interactive
  az network vnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure hizmetinden alt ağ temsilcisini kaldırma

**Mysubnet**adlı alt ağdan temsilciyi kaldırmak için [az Network VNET subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) kullanın:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup
  --name mySubnet
  --vnet-name myVnet
  --remove delegations
```
Temsilinin kaldırıldığını doğrulamak için [az Network VNET subnet Show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)kullanın. Hizmetin **ServiceName**özelliği altındaki alt ağdan kaldırıldığını doğrulayın:

```azurecli-interactive
  az network vnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet
  --query delegations
```
Komutun çıktısı boş bir köşeli ayracdır:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Azure'a Bağlanma

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
[New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group)ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Sanal ağ oluşturma

Myresourcegroup **adlı bir** alt ağ Ile **myvnet** adlı bir alt ağ ile **myresourcegroup** [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) [kullanan bir](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) sanal ağ oluşturun. Sanal ağın IP adresi alanı **10.0.0.0/16**' dır. Sanal ağ içindeki alt ağ **10.0.0.0/24**' dir.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>İzinler

Bir Azure hizmetine temsilci seçmek istediğiniz alt ağı oluşturmadıysanız, aşağıdaki izne sahip olmanız gerekir: `Microsoft.Network/virtualNetworks/subnets/write`.

Yerleşik [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolü de gerekli izinleri içerir.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Bir Azure hizmetine alt ağ atama

Bu bölümde, önceki bölümde oluşturduğunuz alt ağı bir Azure hizmetine devredebilirsiniz. 

**Mysubnet** adlı alt ağı bir Azure hizmetine **mytemsilciliğini** taşıyan bir temsilciyle güncelleştirmek Için [Add-aztemsilcisini](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) kullanın.  Bu örnekte, örnek temsili için **Microsoft. DBforPostgreSQL/serversv2** kullanılmıştır:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Temsilciyi doğrulamak için [Get-Aztemsilciyi](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) kullanın:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure hizmetinden alt ağ temsilcisini kaldırma

**Mysubnet**adlı alt ağdan temsilciyi kaldırmak için [Remove-aztemsilciyi](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) kullanın:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Temsilcinin kaldırıldığını doğrulamak için [Get-Aztemsilciyi](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) kullanın:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Sonraki adımlar
- [Azure 'da alt ağları yönetmeyi](virtual-network-manage-subnet.md)öğrenin.
