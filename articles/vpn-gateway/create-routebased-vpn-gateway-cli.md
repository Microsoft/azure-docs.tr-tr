---
title: 'Rota tabanlı Azure VPN Gateway oluşturma: CLı'
description: Azure CLı kullanarak, şirket içi ağa yönelik bir VPN bağlantısı veya sanal ağlara bağlanmak için hızlı bir şekilde rota tabanlı bir Azure VPN Ağ Geçidi oluşturun.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 87e3490711990944e017d2d463090f3c8697956c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484243"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>CLı kullanarak rota temelli VPN ağ geçidi oluşturma

Bu makale, Azure CLı kullanarak hızlı bir şekilde rota tabanlı Azure VPN Gateway oluşturmanıza yardımcı olur. VPN ağ geçidi, şirket içi ağınıza bir VPN bağlantısı oluştururken kullanılır. VNET 'leri bağlamak için bir VPN ağ geçidi de kullanabilirsiniz.

Bu makaledeki adımlarda bir VNet, alt ağ, bir ağ geçidi alt ağı ve rota tabanlı VPN Gateway (sanal ağ geçidi) oluşturulur. Bir sanal ağ geçidi, oluşturmak için 45 dakika veya daha fazla sürebilir. Ağ geçidi oluşturma işlemi tamamlandıktan sonra bağlantılar oluşturabilirsiniz. Bu adımlar, bir Azure aboneliği gerektirir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.4 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Az Group Create](/cli/azure/group) komutunu kullanarak bir kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Sanal ağ oluşturma

[Az Network VNET Create](/cli/azure/network/vnet) komutunu kullanarak bir sanal ağ oluşturun. Aşağıdaki örnek **EastUS** konumunda **VNet1** adlı bir sanal ağ oluşturur:

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Ağ geçidi alt ağı ekleme

Ağ geçidi alt ağı, sanal ağ geçidi hizmetlerinin kullandığı ayrılmış IP adreslerini içerir. Bir ağ geçidi alt ağı eklemek için aşağıdaki örnekleri kullanın:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Genel IP adresi iste

VPN ağ geçidi, dinamik olarak ayrılmış bir genel IP adresine sahip olmalıdır. Genel IP adresi, sanal ağınız için oluşturduğunuz VPN ağ geçidine tahsis edilir. Genel IP adresi istemek için aşağıdaki örneği kullanın:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>VPN ağ geçidini oluşturma

[az network vnet-gateway create](/cli/azure/network/vnet-gateway) komutunu kullanarak VPN ağ geçidini oluşturun.

Bu komutu parametresini kullanarak çalıştırırsanız `--no-wait` , hiçbir geri bildirim veya çıkış görmezsiniz. `--no-wait`Parametresi, ağ geçidinin arka planda oluşturulmasına olanak tanır. VPN ağ geçidinin hemen oluşturulduğu anlamına gelmez.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

VPN ağ geçidinin oluşturulması 45 dakika veya daha uzun sürebilir.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>VPN ağ geçidini görüntüleme

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Yanıt şuna benzer:

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Genel IP adresini görüntüle

Ağ geçidinize atanan genel IP adresini görüntülemek için aşağıdaki örneği kullanın:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

**IPAddress** alanıyla ilişkili değer, VPN ağ geçidinizin genel IP adresidir.

Örnek yanıt:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, [az Group Delete](/cli/azure/group) ' i kullanarak kaynak grubunu silin. Böylece, kaynak grubu ve içerdiği tüm kaynaklar silinir.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Ağ geçidinin oluşturma işlemi tamamlandıktan sonra, sanal ağınız ile başka bir VNet arasında bir bağlantı oluşturabilirsiniz. Veya, sanal ağınız ile şirket içi bir konum arasında bir bağlantı oluşturun.

> [!div class="nextstepaction"]
> [Siteden siteye bağlantı oluşturma](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Noktadan siteye bağlantı oluşturma](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Başka bir VNet bağlantısı oluşturma](vpn-gateway-vnet-vnet-rm-ps.md)
