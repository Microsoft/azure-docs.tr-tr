---
title: Azure CLı ile Linux ortamı oluşturma
description: Azure CLı kullanarak sıfırdan depolama, Linux VM, sanal ağ ve alt ağ, yük dengeleyici, NIC, genel IP ve bir ağ güvenlik grubu oluşturun.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 56b476c431ed8b41f04b1a1c11c730e5260ade8d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036540"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Azure CLı ile tamamen bir Linux sanal makinesi oluşturma
Azure 'da hızlı bir şekilde bir sanal makine (VM) oluşturmak için, gerekli destekleyici kaynakları oluşturmak üzere varsayılan değerleri kullanan tek bir Azure CLı komutu kullanabilirsiniz. Sanal ağ, genel IP adresi ve ağ güvenlik grubu kuralları gibi kaynaklar otomatik olarak oluşturulur. Üretim kullanımıyla ortamınızda daha fazla denetim için bu kaynakları daha önce oluşturabilir ve ardından sanal makinelerinizi bunlara ekleyebilirsiniz. Bu makalede, bir sanal makine oluşturma ve tek tek destekleyen kaynakların her biri için size kılavuzluk eder.

En son [Azure CLI](/cli/azure/install-az-cli2) 'yi yüklediğinizden ve [az Login](/cli/azure/reference-index)ile içinde bir Azure hesabına oturum açtığınızdan emin olun.

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. Örnek parametre adları *Myresourcegroup*, *Myvnet*ve *myvm*' i içerir.

## <a name="create-resource-group"></a>Kaynak grubu oluşturma
Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bir sanal makineden önce bir kaynak grubu oluşturulmalıdır ve sanal ağ kaynakları desteklenmelidir. [Az Group Create](/cli/azure/group)ile kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location eastus
```

Varsayılan olarak, Azure CLı komutlarının çıkışı JSON (JavaScript Nesne Gösterimi) ' dir. Örneğin, varsayılan çıktıyı bir liste veya tablo olarak değiştirmek için [az configure--output](/cli/azure/reference-index)kullanın. Ayrıca, çıkış biçiminde tek bir zaman değişikliği için herhangi bir komuta `--output` ekleyebilirsiniz. Aşağıdaki örnek, `az group create` komutundan JSON çıkışını gösterir:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Sanal ağ ve alt ağ oluşturma
Ardından, Azure 'da sanal ağ ve VM 'lerinizi oluşturabileceğiniz bir alt ağ oluşturursunuz. *192.168.0.0/16* adres ön ekine sahip *myvnet* adlı bir sanal ağ oluşturmak için [az Network VNET Create](/cli/azure/network/vnet) kullanın. *192.168.1.0/24*adres ön ekine sahip *mysubnet* adlı bir alt ağ da eklersiniz:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Çıktı alt ağın sanal ağ içinde mantıksal olarak oluşturulduğunu gösterir:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma
Şimdi [az Network public-IP Create](/cli/azure/network/public-ip)komutuyla BIR genel IP adresi oluşturalım. Bu genel IP adresi, Internet 'ten sanal makinelerinize bağlanmanızı sağlar. Varsayılan Adres dinamik olduğundan, `--domain-name-label` parametresiyle adlandırılmış bir DNS girişi oluşturun. Aşağıdaki örnek, mypublicdns DNS adıyla *Mypublicıp* adlı BIR genelIP oluşturur. DNS adının benzersiz olması gerektiğinden, kendi benzersiz DNS adınızı sağlayın:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Çıktı:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma
VM 'lerinizin içindeki ve içindeki trafik akışını denetlemek için bir sanal NIC veya alt ağa ağ güvenlik grubu uygularsınız. Aşağıdaki örnek, *Mynetworksecuritygroup*adlı bir ağ güvenlik grubu oluşturmak için [az Network NSG Create](/cli/azure/network/nsg) komutunu kullanır:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Belirli trafiğe izin veren veya reddeden kurallar tanımlarsınız. 22 numaralı bağlantı noktasında (SSH erişimini etkinleştirmek için) gelen bağlantılara izin vermek için [az Network NSG Rule Create](/cli/azure/network/nsg/rule)komutuyla bir gelen kuralı oluşturun. Aşağıdaki örnek, *Mynetworksecuritygroupkurallarını sh*adlı bir kural oluşturur:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

80 numaralı bağlantı noktasında gelen bağlantılara izin vermek için (Web trafiği için), başka bir ağ güvenlik grubu kuralı ekleyin. Aşağıdaki örnek, *Mynetworksecuritygrouprulehttp*adlı bir kural oluşturur:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Ağ güvenlik grubu ve kurallarını, [az Network NSG Show](/cli/azure/network/nsg)komutuyla inceleyin:

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Çıktı:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Sanal NIC oluşturma
Sanal ağ arabirim kartları (NIC 'ler), kullanımları için kurallar uygulayabileceğiniz için programlı olarak kullanılabilir. [VM boyutuna](sizes.md)bağlı olarak, bir VM 'ye birden çok sanal NIC ekleyebilirsiniz. Aşağıdaki [az Network Nic Create](/cli/azure/network/nic) komutunda, *MYNIC* adlı bir NIC oluşturup ağ güvenlik grubunuzla ilişkilendirirsiniz. Genel IP adresi *Mypublicıp* , sanal NIC ile de ilişkilendirilir.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Çıktı:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma
Kullanılabilirlik kümeleri, VM 'lerinizi hata etki alanlarına ve güncelleştirme etki alanlarına yaymaya yardımcı olur. Şu anda yalnızca bir VM oluşturabilirsiniz, ancak gelecekte genişletmeyi kolaylaştırmak için kullanılabilirlik kümeleri kullanmak en iyi uygulamadır. 

Hata etki alanları, ortak bir güç kaynağı ve ağ anahtarını paylaşan bir sanal makine gruplandırması tanımlar. Varsayılan olarak, kullanılabilirlik kümesi içinde yapılandırılan sanal makineler, en çok üç hata etki alanı arasında ayrılır. Bu hata etki alanlarından birindeki bir donanım sorunu, uygulamanızı çalıştıran her VM 'yi etkilemez.

Güncelleştirme etki alanları, sanal makinelerin ve temel alınan fiziksel donanımların aynı anda yeniden başlatılabilen gruplarını gösterir. Planlı bakım sırasında, güncelleştirme etki alanlarının yeniden başlatıldığı sıra sıralı olmayabilir, ancak aynı anda yalnızca bir güncelleştirme etki alanı yeniden başlatılır.

Azure, bir kullanılabilirlik kümesine yerleştirilirken VM 'Leri hata ve güncelleştirme etki alanlarına otomatik olarak dağıtır. Daha fazla bilgi için bkz. [sanal makinelerin kullanılabilirliğini yönetme](manage-availability.md).

[Az VM kullanılabilirliği-set create](/cli/azure/vm/availability-set)komutuyla VM 'niz için bir kullanılabilirlik kümesi oluşturun. Aşağıdaki örnek *myAvailabilitySet* adında bir kullanılabilirlik kümesi oluşturur:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Çıkış notları hata etki alanları ve güncelleştirme etki alanları:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>VM oluşturma
Internet erişimli VM 'Leri desteklemek için ağ kaynaklarını oluşturdunuz. Şimdi bir VM oluşturun ve bir SSH anahtarıyla güvenli hale getirin. Bu örnekte, en son LTS 'yi temel alan bir Ubuntu VM oluşturalım. [Azure VM görüntülerini bulma](cli-ps-findimage.md)bölümünde açıklandığı gibi [az VM image List](/cli/azure/vm/image)ile ek görüntüler bulabilirsiniz.

Kimlik doğrulaması için kullanılacak bir SSH anahtarı belirtin. SSH ortak anahtar çiftiniz yoksa, [bunları](mac-create-ssh-keys.md) oluşturabilir veya sizin için oluşturmak üzere `--generate-ssh-keys` parametresini kullanabilirsiniz. Zaten bir anahtar çiftiniz varsa, bu parametre `~/.ssh`var olan anahtarları kullanır.

Tüm kaynakları ve bilgileri [az VM Create](/cli/azure/vm) komutuyla birlikte getirerek VM 'yi oluşturun. Aşağıdaki örnek *myVM* adlı bir VM oluşturur:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Genel IP adresini oluştururken verdiğiniz DNS girdisiyle sanal makinenize SSH. Bu `fqdn`, sanal makineyi oluştururken çıktıda gösterilir:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Çıktı:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

NGıNX 'i yükleyebilir ve trafik akışını sanal makineye görebilirsiniz. NGıNX 'i şu şekilde yükler:

```bash
sudo apt-get install -y nginx
```

Varsayılan NGıNX sitesini eylem bölümünde görmek için Web tarayıcınızı açın ve FQDN 'nizi girin:

![VM 'nizin varsayılan NGıNX sitesi](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Şablon olarak dışarı aktar
Artık aynı parametrelerle veya bununla eşleşen bir üretim ortamıyla ek bir geliştirme ortamı oluşturmak isterseniz ne olur? Kaynak Yöneticisi, ortamınız için tüm parametreleri tanımlayan JSON şablonları kullanır. Bu JSON şablonuna başvurarak tüm ortamları oluşturursunuz. JSON [şablonlarını el ile](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oluşturabilir veya var olan bir ortamı, sızın için JSON şablonu oluşturmak üzere dışarı aktarabilirsiniz. Kaynak grubunuzu aşağıdaki gibi dışarı aktarmak için [az Group Export](/cli/azure/group) kullanın:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Bu komut, geçerli çalışma dizininizde `myResourceGroup.json` dosyasını oluşturur. Bu şablondan bir ortam oluşturduğunuzda, sizden tüm kaynak adları istenir. `az group export` komutuna `--include-parameter-default-value` parametresini ekleyerek, bu adları şablon dosyanızda doldurabilirsiniz. Kaynak adlarını belirtmek için JSON şablonunuzu düzenleyin veya kaynak adlarını belirten [Parameters. JSON dosyası oluşturun](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

Şablonunuzda bir ortam oluşturmak için [az Group Deployment Create](/cli/azure/group/deployment) ' ı aşağıdaki gibi kullanın:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

[Şablonlardan dağıtma hakkında daha fazla](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)bilgi edinmek isteyebilirsiniz. Ortamları artımlı olarak güncelleştirme, parametreler dosyasını kullanma ve şablonları tek bir depolama konumundan erişme hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Artık birden çok ağ bileşeni ve VM ile çalışmaya başlamaya hazırsınız. Bu örnek ortamı, burada tanıtılan temel bileşenleri kullanarak uygulamanızı oluşturmak için kullanabilirsiniz.
