---
title: Arka uç havuzu yönetimi
titleSuffix: Azure Load Balancer
description: Azure Load Balancer arka uç havuzunu yapılandırmayı ve yönetmeyi öğrenmeye başlayın
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: c49a721a4db758965c9cf8d71f5d73b5754b6088
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654484"
---
# <a name="backend-pool-management"></a>Arka uç havuzu yönetimi
Arka uç havuzu, yük dengeleyicinin kritik bir bileşenidir. Arka uç havuzu, belirli bir yük dengeleme kuralı için trafik sunacak kaynak grubunu tanımlar.

Arka uç havuzunu yapılandırmanın iki yolu vardır:
* Ağ arabirimi kartı (NIC)
* IP adresi ve sanal ağ (VNET) kaynak KIMLIĞI birleşimi

Mevcut sanal makineleri ve sanal makine ölçek kümelerini kullanırken arka uç havuzunuzu NIC ile yapılandırın. Bu yöntem, kaynağınız ile arka uç havuzu arasındaki en doğrudan bağlantıyı oluşturur. 

Arka uç havuzunuzu daha sonra sanal makineler ve sanal makine ölçek kümeleri oluşturmayı planladığınız bir IP adresi aralığıyla önceden ayırırken, arka uç havuzunuzu IP adresine ve VNET KIMLIĞI birleşimine göre yapılandırın.

Aynı yük dengeleyici için IP tabanlı ve NIC tabanlı arka uç havuzlarını yapılandırabilirsiniz, ancak NIC ve IP adresleri için aynı havuzda hedeflenen desteklenen adresler içeren tek bir arka uç havuzu oluşturamazsınız.

Bu makalenin yapılandırma bölümleri şu şekilde odaklanacaktır:

* Azure PowerShell
* Azure CLI
* REST API
* Azure Resource Manager şablonları 

Bu bölümler, arka uç havuzlarının her bir yapılandırma seçeneği için nasıl yapılandırıldığı hakkında fikir verir.

## <a name="configuring-backend-pool-by-nic"></a>NIC tarafından arka uç havuzunu yapılandırma
Arka uç havuzu, yük dengeleyici işleminin bir parçası olarak oluşturulur. NIC 'nin IP yapılandırma özelliği, arka uç havuzu üyelerini eklemek için kullanılır.

Aşağıdaki örnekler, bu iş akışını ve ilişkiyi vurgulamak için arka uç havuzunun oluştur ve doldur işlemlerine odaklanılmıştır.

  >[!NOTE] 
  >Ağ arabirimi aracılığıyla yapılandırılan arka uç havuzlarının, arka uç havuzundaki bir işlemin parçası olarak güncelleştirileceğini unutmayın. Arka uç kaynaklarının herhangi bir eklenmesi veya silinmesi, kaynağın ağ arabiriminde gerçekleşmelidir.

### <a name="powershell"></a>PowerShell
Yeni bir arka uç havuzu oluşturun:
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Yeni bir ağ arabirimi oluşturun ve arka uç havuzuna ekleyin:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

Bu ağ arabiriminin arka uç havuzuna eklendiğini onaylamak için yük dengeleyicinin arka uç havuzu bilgilerini alın:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

Yeni bir sanal makine oluşturun ve ağ arabirimini, arka uç havuzuna yerleştirmek üzere bağlayın:

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Arka uç havuzunu oluşturma:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

Yeni bir ağ arabirimi oluşturun ve arka uç havuzuna ekleyin:

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

IP adresinin doğru şekilde eklendiğini onaylamak için arka uç havuzunu alın:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

Yeni bir sanal makine oluşturun ve ağ arabirimini, arka uç havuzuna yerleştirmek üzere bağlayın:

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="resource-manager-template"></a>Resource Manager Şablonu

Yük dengeleyici ve sanal makineler dağıtmak ve ağ arabirimi aracılığıyla sanal makineleri arka uç havuzuna eklemek için bu [hızlı başlangıç Kaynak Yöneticisi şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) izleyin.

Yük dengeleyici ve sanal makineler dağıtmak ve sanal makineleri IP adresi aracılığıyla arka uç havuzuna eklemek için bu [hızlı başlangıç Kaynak Yöneticisi şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-ip-configured-backend-pool) izleyin.


## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>IP adresi ve sanal ağ ile arka uç havuzunu yapılandırma
Önceden doldurulmuş arka uç havuzlarıyla birlikte IP ve sanal ağ kullanın.

Tüm arka uç havuzu yönetimi, aşağıdaki örneklerde vurgulanan şekilde doğrudan arka uç havuzu nesnesi üzerinde yapılır.

### <a name="powershell"></a>PowerShell
Yeni arka uç havuzu oluştur:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

Mevcut sanal ağdan arka uç havuzunu yeni bir IP ile güncelleştir:
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

Arka uç adreslerinin arka uç havuzuna eklendiğini onaylamak için yük dengeleyicinin arka uç havuzu bilgilerini alın:

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
Bir ağ arabirimi oluşturun ve arka uç havuzuna ekleyin. IP adresini arka uç adreslerinden birine ayarlayın:

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

Bir VM oluşturun ve NIC 'yi arka uç havuzunda bir IP adresi ile ekleyin:
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
CLı kullanarak, arka uç havuzunu komut satırı parametreleriyle ya da bir JSON yapılandırma dosyası aracılığıyla doldurabilirsiniz. 

Komut satırı parametreleri aracılığıyla arka uç havuzunu oluşturun ve doldurun:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

Arka uç havuzunu JSON yapılandırma dosyası aracılığıyla oluşturun ve doldurun:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

JSON yapılandırma dosyası:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

Arka uç adreslerinin arka uç havuzuna eklendiğini onaylamak için yük dengeleyicinin arka uç havuzu bilgilerini alın:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

Bir ağ arabirimi oluşturun ve arka uç havuzuna ekleyin. IP adresini arka uç adreslerinden birine ayarlayın:

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Bir VM oluşturun ve NIC 'yi arka uç havuzunda bir IP adresi ile ekleyin:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```
 
### <a name="limitations"></a>Sınırlamalar
IP adresi tarafından yapılandırılan bir arka uç havuzu aşağıdaki sınırlamalara sahiptir:
  * Yalnızca standart yük dengeleyiciler için kullanılabilir
  * Arka uç havuzunda 100 IP adresi sınırı
  * Arka uç kaynakları, yük dengeleyici ile aynı sanal ağda olmalıdır
  * IP tabanlı arka uç havuzundaki bir Load Balancer özel bağlantı hizmeti olarak çalışamaz
  * Bu özellik şu anda Azure portal desteklenmiyor
  * ACI kapsayıcıları Şu anda bu özellik tarafından desteklenmiyor
  * Yük dengeleyiciler veya Application Gateway gibi hizmetler yük dengeleyicinin arka uç havuzunda yerleştirilemez
  * Gelen NAT kuralları IP adresi ile belirtilemiyor

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede Azure Load Balancer arka uç havuzu yönetimi ve IP adresi ve sanal ağ ile arka uç havuzu yapılandırma hakkında bilgi edindiniz.

[Azure Load Balancer](load-balancer-overview.md)hakkında daha fazla bilgi edinin.

IP tabanlı backendpool yönetimi için [REST API](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerbackendaddresspools/createorupdate) gözden geçirin.
