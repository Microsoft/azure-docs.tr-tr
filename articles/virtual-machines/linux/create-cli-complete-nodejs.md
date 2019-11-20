---
title: Klasik Azure CLı ile tüm Linux ortamı oluşturma
description: Azure klasik CLı kullanarak sıfırdan depolama, Linux VM, sanal ağ ve alt ağ, yük dengeleyici, NIC, genel IP ve ağ güvenlik grubu oluşturun.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 1ee89ce18600685f3f82bfb49d4d8ecbaf192b04
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036520"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Klasik Azure CLı ile tüm Linux ortamı oluşturma
Bu makalede, bir yük dengeleyiciye ve geliştirme ve basit bilgi işlem için yararlı olan bir sanal makine çiftinden oluşan basit bir ağ oluşturacağız. Internet 'te herhangi bir yerden bağlantı kurmak için iki adet çalışan, güvenli Linux VM 'ye sahip olana kadar Process komutuyla komut aracılığıyla ilerliyoruz. Daha sonra, daha karmaşık ağlara ve ortamlara geçebilirsiniz.

Bu şekilde, Kaynak Yöneticisi dağıtım modelinin size verdiği bağımlılık hiyerarşisi ve ne kadar güç sağladığını öğrenirsiniz. Sistemin nasıl oluşturulduğunu görtikten sonra, [Azure Resource Manager şablonlarını](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)kullanarak daha hızlı bir şekilde yeniden oluşturabilirsiniz. Ayrıca, ortamınızın bölümlerinin nasıl bir araya geldiğini öğrendikten sonra, bunları otomatik hale getirmek için şablon oluşturma daha kolay hale gelir.

Ortam şunları içerir:

* Bir kullanılabilirlik kümesi içinde iki VM.
* 80 numaralı bağlantı noktasında Yük Dengeleme kuralına sahip yük dengeleyici.
* Ağ güvenlik grubu (NSG) kuralları, sanal makinenizin istenmeyen trafiğinden korunmasını sağlar.

Bu özel ortamı oluşturmak için, [Azure klasik](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) en son CLI kaynak yöneticisi modunda (`azure config mode arm`) gereklidir. Ayrıca bir JSON ayrıştırma aracına ihtiyacınız vardır. Bu örnek [JQ](https://stedolan.github.io/jq/)kullanır.


## <a name="cli-versions-to-complete-the-task"></a>Görevi tamamlamak için kullanılacak CLI sürümleri
Görevi aşağıdaki CLI sürümlerinden birini kullanarak tamamlayabilirsiniz:

- [Azure klasık CLI](#quick-commands) : klasik ve kaynak yönetimi dağıtım MODELLERINE yönelik CLI 'mız (Bu makale)
- [Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) -kaynak yönetimi dağıtım modeli için yenı nesil CLI 'mız


## <a name="quick-commands"></a>Hızlı komutlar
Görevi hızlı bir şekilde gerçekleştirmeniz gerekirse, aşağıdaki bölümde bir VM 'yi Azure 'a yüklemek için temel komutların ayrıntıları verilmiştir. Her adım için daha ayrıntılı bilgi ve bağlam, belgenin geri kalanında [buradan](#detailed-walkthrough)başlayarak bulunabilir.

[Azure klasık CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ile oturum açtığınızdan ve Kaynak Yöneticisi modunda kullandığınızdan emin olun:

```azurecli
azure config mode arm
```

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. Örnek parametre adları `myResourceGroup`, `mystorageaccount`ve `myVM`içerir.

Kaynak grubunu oluşturun. Aşağıdaki örnek `myResourceGroup` konumunda `westeurope` adlı bir kaynak grubu oluşturur:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

JSON ayrıştırıcısı kullanarak kaynak grubunu doğrulayın:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Depolama hesabı oluşturun. Aşağıdaki örnek, `mystorageaccount`adlı bir depolama hesabı oluşturur. (Depolama hesabı adının benzersiz olması gerekir, bu nedenle kendi benzersiz adınızı sağlayın.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

JSON ayrıştırıcısı kullanarak depolama hesabını doğrulayın:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Sanal ağı oluşturun. Aşağıdaki örnek, `myVnet`adlı bir sanal ağ oluşturur:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Bir alt ağ oluşturun. Aşağıdaki örnek, `mySubnet`adlı bir alt ağ oluşturur:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

JSON ayrıştırıcısı kullanarak sanal ağı ve alt ağı doğrulayın:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Genel IP oluşturun. Aşağıdaki örnek, `mypublicdns`DNS adıyla `myPublicIP` adlı bir genel IP oluşturur. (DNS adı benzersiz olmalıdır, bu nedenle kendi benzersiz adınızı sağlayın.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Yük dengeleyiciyi oluşturun. Aşağıdaki örnek, `myLoadBalancer`adlı bir yük dengeleyici oluşturur:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Yük Dengeleyici için bir ön uç IP havuzu oluşturun ve genel IP 'yi ilişkilendirin. Aşağıdaki örnek, `mySubnetPool`adlı bir ön uç IP havuzu oluşturur:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Yük Dengeleyici için arka uç IP havuzu oluşturun. Aşağıdaki örnek, `myBackEndPool`adlı bir arka uç IP havuzu oluşturur:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Yük Dengeleyici için SSH gelen ağ adresi çevirisi (NAT) kuralları oluşturun. Aşağıdaki örnek, `myLoadBalancerRuleSSH1` ve `myLoadBalancerRuleSSH2`iki yük dengeleyici kuralı oluşturur:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Yük Dengeleyici için Web gelen NAT kurallarını oluşturun. Aşağıdaki örnek, `myLoadBalancerRuleWeb`adlı bir yük dengeleyici kuralı oluşturur:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Yük dengeleyici durum araştırması oluşturun. Aşağıdaki örnek, `myHealthProbe`adlı bir TCP araştırması oluşturur:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

JSON ayrıştırıcısı kullanarak yük dengeleyiciyi, IP havuzlarını ve NAT kurallarını doğrulayın:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

İlk ağ arabirim kartını (NIC) oluşturun. `#####-###-###` bölümlerini kendi Azure abonelik KIMLIĞINIZLE değiştirin. Oluşturduğunuz kaynakları incelerken, abonelik KIMLIĞINIZ **JQ** çıktısında belirtilmiştir. Ayrıca, abonelik KIMLIĞINIZI `azure account list`görüntüleyebilirsiniz.

Aşağıdaki örnek, `myNic1`adlı bir NIC oluşturur:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

İkinci NIC 'yi oluşturun. Aşağıdaki örnek, `myNic2`adlı bir NIC oluşturur:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

JSON ayrıştırıcısı kullanarak iki NIC 'yi doğrulayın:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Ağ güvenlik grubunu oluşturun. Aşağıdaki örnek, `myNetworkSecurityGroup`adlı bir ağ güvenlik grubu oluşturur:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Ağ güvenlik grubu için iki gelen kuralı ekleyin. Aşağıdaki örnek, `myNetworkSecurityGroupRuleSSH` ve `myNetworkSecurityGroupRuleHTTP`iki kural oluşturur:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

JSON ayrıştırıcısı kullanarak ağ güvenlik grubunu ve gelen kurallarını doğrulayın:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Ağ güvenlik grubunu iki NIC 'ye bağlama:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Kullanılabilirlik kümesi oluşturun. Aşağıdaki örnek, `myAvailabilitySet`adlı bir kullanılabilirlik kümesi oluşturur:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

İlk Linux VM 'yi oluşturun. Aşağıdaki örnek, `myVM1`adlı bir sanal makine oluşturur:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

İkinci Linux VM 'yi oluşturun. Aşağıdaki örnek, `myVM2`adlı bir sanal makine oluşturur:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Oluşturulan her şeyin doğrulanması için JSON ayrıştırıcısı kullanın:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Yeni örnekleri hızlı bir şekilde yeniden oluşturmak için yeni ortamınızı bir şablona dışarı aktarın:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Ayrıntılı kılavuz
Aşağıdaki ayrıntılı adımlarda, ortamınızı oluştururken her komutun ne yaptığını açıklanmaktadır. Bu kavramlar, geliştirme veya üretim için kendi özel ortamlarınızı oluştururken faydalıdır.

[Azure klasık CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ile oturum açtığınızdan ve Kaynak Yöneticisi modunda kullandığınızdan emin olun:

```azurecli
azure config mode arm
```

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. Örnek parametre adları `myResourceGroup`, `mystorageaccount`ve `myVM`içerir.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Kaynak grupları oluşturma ve dağıtım konumlarını seçme
Azure Kaynak grupları, kaynak dağıtımlarının mantıksal yönetimini etkinleştirmek için yapılandırma bilgilerini ve meta verileri içeren mantıksal dağıtım varlıklarıdır. Aşağıdaki örnek `myResourceGroup` konumunda `westeurope` adlı bir kaynak grubu oluşturur:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Çıktı:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
VM diskleriniz ve eklemek istediğiniz tüm ek veri diskleri için depolama hesaplarına ihtiyacınız vardır. Kaynak grupları oluşturduktan hemen sonra depolama hesapları oluşturursunuz.

Burada, hesap konumunu, onu denetleyen kaynak grubunu ve istediğiniz depolama desteğinin türünü geçirerek `azure storage account create` komutunu kullanırız. Aşağıdaki örnek, `mystorageaccount`adlı bir depolama hesabı oluşturur:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Çıktı:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Kaynak grubumuzu `azure group show` komutunu kullanarak incelemek için, [JQ](https://stedolan.github.io/jq/) aracını `--json` Azure CLI seçeneğiyle birlikte kullanalım. ( **Jsawk** veya JSON 'ı ayrıştırmayı tercih ettiğiniz herhangi bir dil kitaplığını kullanabilirsiniz.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Çıktı:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

CLı kullanarak depolama hesabını araştırmak için, önce hesap adlarını ve anahtarlarını ayarlamanız gerekir. Aşağıdaki örnekteki depolama hesabının adını seçtiğiniz bir adla değiştirin:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Daha sonra, depolama bilgilerinizi kolayca görüntüleyebilirsiniz:

```azurecli
azure storage container list
```

Çıktı:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Sanal ağ ve alt ağ oluşturma
Ardından, Azure 'da çalışan bir sanal ağ ve VM 'lerinizi oluşturabileceğiniz bir alt ağ oluşturmanız gerekir. Aşağıdaki örnek, `192.168.0.0/16` adresi önekiyle `myVnet` adlı bir sanal ağ oluşturur:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Çıktı:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Ayrıca, kaynaklarımızı nasıl oluşturduğumuzu görmek için `azure group show` ve `jq` ' nin--JSON seçeneğini kullanalım. Artık bir `storageAccounts` kaynağı ve bir `virtualNetworks` kaynağıdır.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Çıktı:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Şimdi VM 'Lerin dağıtıldığı `myVnet` sanal ağında bir alt ağ oluşturalım. `azure network vnet subnet create` komutunu, zaten oluşturduğumuz kaynaklarla birlikte kullanıyoruz: `myResourceGroup` kaynak grubu ve `myVnet` sanal ağı. Aşağıdaki örnekte, `192.168.1.0/24`alt ağ adresi ön ekine sahip `mySubnet` adlı alt ağı ekleyeceğiz:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Çıktı:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Alt ağ sanal ağ içinde mantıksal olarak olduğundan, alt ağ bilgilerine biraz farklı bir komutla bakacağız. Kullandığımız komut `azure network vnet show`, ancak `jq`kullanarak JSON çıkışını incelemenize devam ediyoruz.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Çıktı:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma
Şimdi yük dengeleyicisine atadığımız genel IP adresini (PıP) oluşturalım. `azure network public-ip create` komutunu kullanarak, Internet 'ten sanal makinelerinize bağlanmanızı sağlar. Varsayılan Adres dinamik olduğundan, **cloudapp.Azure.com** etki alanında `--domain-name-label` seçeneğini kullanarak adlandırılmış bir DNS girişi oluşturacağız. Aşağıdaki örnek, `mypublicdns`DNS adıyla `myPublicIP` adlı bir genel IP oluşturur. DNS adının benzersiz olması gerektiğinden, kendi benzersiz DNS adınızı sağlarsınız:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Çıktı:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Genel IP adresi de bir üst düzey kaynaktır, bu sayede `azure group show`görebilirsiniz.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Çıktı:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Tam `azure network public-ip show` komutunu kullanarak alt etki alanının tam etki alanı adı (FQDN) dahil olmak üzere daha fazla kaynak ayrıntısı inceleyebilirsiniz. Genel IP adresi kaynağı mantıksal olarak ayrıldı, ancak belirli bir adres henüz atanmadı. Bir IP adresi almak için, henüz oluşturduğum bir yük dengeleyiciye ihtiyacınız vardır.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Çıktı:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Yük dengeleyici ve IP havuzları oluşturma
Yük dengeleyici oluşturduğunuzda, trafiği birden çok VM arasında dağıtmanıza olanak sağlar. Ayrıca bakım veya ağır yük durumunda Kullanıcı isteklerine yanıt veren birden çok VM çalıştırarak uygulamanıza yedeklilik sağlar. Aşağıdaki örnek, `myLoadBalancer`adlı bir yük dengeleyici oluşturur:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Çıktı:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Yük dengeleyicimiz oldukça boş olduğundan bazı IP havuzları oluşturalım. Biri ön uç ve diğeri arka uç için olmak üzere yük dengeleyicimiz için iki IP havuzu oluşturmak istiyoruz. Ön uç IP havuzu herkese açık bir şekilde görünür. Ayrıca, daha önce oluşturduğumuz PıP 'yi atadığımızda yer vardır. Sonra, bağlantı kurmak için sanal makinelerimiz için arka uç havuzunu bir konum olarak kullanırız. Bu şekilde trafik, yük dengeleyiciden VM 'lere akabilir.

İlk olarak, ön uç IP havuzumuzu oluşturalım. Aşağıdaki örnek, `myFrontEndPool`adında bir ön uç havuzu oluşturur:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Çıktı:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Daha önce oluşturduğumuz `myPublicIP` geçirmek için `--public-ip-name` anahtarını nasıl kullandığımızda unutmayın. Genel IP adresini yük dengeleyiciye atamak, sanal makinelerinize Internet üzerinden ulaşmanıza olanak sağlar.

Ardından, ikinci IP havuzumuzu bu kez arka uç trafiğiniz için oluşturalım. Aşağıdaki örnek, `myBackEndPool`adlı bir arka uç havuzu oluşturur:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Çıktı:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Yük dengeleyicimizin yaptığı `azure network lb show` ve JSON çıkışını inceleyerek, bu işlemi nasıl yaptığını görebiliriz:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Çıktı:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Yük dengeleyici NAT kuralları oluşturma
Yük dengeleyicimizde trafik akışını almak için gelen veya giden eylemleri belirten ağ adresi çevirisi (NAT) kuralları oluşturmamız gerekir. Kullanılacak protokolü belirtebilir, sonra dış bağlantı noktalarını iç bağlantı noktalarına istediğiniz şekilde eşleyebilirsiniz. Ortamımızda, sanal makinelerimize yük dengeleyicimiz aracılığıyla SSH 'ye izin veren bazı kurallar oluşturalım. 4222 ve 4223 numaralı TCP bağlantı noktalarını sanal makinelerimizde (daha sonra oluşturduğumuz) TCP bağlantı noktası 22 ' ye yönlendirmek için ayarladık. Aşağıdaki örnek, TCP bağlantı noktası 4222 ' i 22 numaralı bağlantı noktasına eşlemek için `myLoadBalancerRuleSSH1` adlı bir kural oluşturur:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Çıktı:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

SSH için ikinci NAT kuralınız yordamını tekrarlayın. Aşağıdaki örnek, TCP bağlantı noktası 4223 ' i 22 numaralı bağlantı noktasına eşlemek için `myLoadBalancerRuleSSH2` adlı bir kural oluşturur:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Ayrıca, Web trafiği için TCP bağlantı noktası 80 için bir NAT kuralı oluşturalım ve kuralı IP havuzlarımıza kadar denetlemenizi sağlar. Kuralı, sanal makinelerimize tek tek bağlamak yerine bir IP havuzuna yedekliyoruz, VM 'Leri IP havuzuna ekleyip kaldırabiliriz. Yük dengeleyici trafik akışını otomatik olarak ayarlar. Aşağıdaki örnek, 80 numaralı bağlantı 80 noktasına numaralı TCP bağlantı noktasını eşlemek için `myLoadBalancerRuleWeb` adlı bir kural oluşturur:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Çıktı:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Yük dengeleyici durum yoklaması oluşturma
Bir sistem durumu araştırması, yük dengeleyicimize ait VM 'Leri düzenli olarak denetler. Aksi takdirde, kullanıcıların bunlara yönlendirilmediğinden emin olmak için bu işlemler işlemden kaldırılır. Sistem durumu araştırması için aralıklar ve zaman aşımı değerleriyle birlikte özel denetimler tanımlayabilirsiniz. Sistem durumu araştırmaları hakkında daha fazla bilgi için bkz. [Load Balancer araştırmaları](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Aşağıdaki örnek, `myHealthProbe`adlı bir TCP sistem durumu oluşturur:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Çıktı:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Burada, sistem durumu denetliyoruz için 15 saniyelik bir Aralık belirttik. Yük dengeleyici konağın artık çalışmadığını kabul etmeden önce en fazla dört araştırma (bir dakika) kaçırırız.

## <a name="verify-the-load-balancer"></a>Yük dengeleyiciyi doğrulama
Şimdi yük dengeleyici yapılandırması bitti. Bu adımlar aşağıda verilmiştir:

1. Yük dengeleyici oluşturdunuz.
2. Ön uç IP havuzu oluşturdunuz ve buna genel IP atanmış olursunuz.
3. VM 'Lerin bağlanabileceği bir arka uç IP havuzu oluşturdunuz.
4. Yönetim için VM 'lere SSH sağlayan NAT kuralları oluşturdunuz ve web uygulamamız için TCP bağlantı noktası 80 ' i sağlayan bir kuralla birlikte.
5. VM 'Leri düzenli olarak denetlemek için bir sistem durumu araştırması eklediniz. Bu sistem durumu araştırması, kullanıcıların artık çalışmayan veya içeriğe hizmet veren bir VM 'ye erişmeyi denememesini sağlar.

Şimdi yük dengeleyicinizin nasıl göründüğünü gözden geçirelim:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Çıktı:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Linux VM ile kullanılacak bir NIC oluşturma
Kullanılabilir kuralları kendi kullanımına uygulayabileceğiniz için NIC 'ler programlı bir şekilde kullanılabilir. Ayrıca birden fazla tane olabilir. Aşağıdaki `azure network nic create` komutunda NIC 'yi yük arka uç IP havuzuna yedekler ve SSH trafiğine izin vermek için NAT kuralıyla ilişkilendirebilirsiniz.

`#####-###-###` bölümlerini kendi Azure abonelik KIMLIĞINIZLE değiştirin. Oluşturduğunuz kaynakları incelerken abonelik KIMLIĞINIZ `jq` çıktısında belirtilmiştir. Ayrıca, abonelik KIMLIĞINIZI `azure account list`görüntüleyebilirsiniz.

Aşağıdaki örnek, `myNic1`adlı bir NIC oluşturur:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Çıktı:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Kaynağı doğrudan inceleyerek ayrıntıları görebilirsiniz. `azure network nic show` komutunu kullanarak kaynağı inceleyebilirsiniz:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Çıktı:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Şimdi, arka uç IP havuzumuza yeniden bağlandığımız ikinci NIC 'yi oluşturacağız. Bu kez ikinci NAT kuralı SSH trafiğine izin verir. Aşağıdaki örnek, `myNic2`adlı bir NIC oluşturur:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Ağ güvenlik grubu ve kuralları oluşturma
Şimdi, NIC 'ye erişimi yöneten bir ağ güvenlik grubu ve gelen kuralları oluşturacağız. Bir ağ güvenlik grubu, bir NIC veya alt ağa uygulanabilir. Sanal makinelerinizin içindeki ve içindeki trafik akışını denetlemek için kurallar tanımlarsınız. Aşağıdaki örnek, `myNetworkSecurityGroup`adlı bir ağ güvenlik grubu oluşturur:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Bkz. bağlantı noktası 22 ' de gelen bağlantılara izin vermek için NSG için gelen kuralı ekleyelim (SSH 'yi desteklemek için). Aşağıdaki örnek, bağlantı noktası 22 ' de TCP 'ye izin vermek için `myNetworkSecurityGroupRuleSSH` adlı bir kural oluşturur:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Şimdi NSG bağlantı noktası 80 ' de gelen bağlantılara izin verecek şekilde (Web trafiğini desteklemek için) NSG için gelen kuralı ekleyelim. Aşağıdaki örnek, 80 numaralı bağlantı noktasında TCP 'ye izin vermek için `myNetworkSecurityGroupRuleHTTP` adlı bir kural oluşturur:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Gelen kuralı, gelen ağ bağlantıları için bir filtredir. Bu örnekte, NSG 'yi VM 'Ler sanal NIC 'sine bağladık, bu da 22 numaralı bağlantı noktasına gönderilen tüm istekler sanal makinemizdeki NIC 'ye geçirilir. Bu gelen kural, klasik dağıtımlarda olduğu gibi bir uç nokta hakkında değil, bir ağ bağlantısıyla ilgilidir. Bir bağlantı noktasını açmak için **, istenen bağlantı noktasından gelen** istekleri kabul etmek üzere `--source-port-range` '\*' (varsayılan değer) olarak bırakmanız gerekir. Bağlantı noktaları genellikle dinamiktir.
>
>

## <a name="bind-to-the-nic"></a>NIC 'ye bağlama
NSG 'yi NIC 'lere bağlayın. NIC 'lerimizi ağ güvenlik grubumuza bağlanmamız gerekiyor. Her iki NIC 'i de bağlamak için her iki komutu çalıştırın:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma
Kullanılabilirlik kümeleri, VM 'lerinizi hata etki alanları ve yükseltme etki alanlarında yaymaya yardımcı olur. VM 'niz için bir kullanılabilirlik kümesi oluşturalım. Aşağıdaki örnek, `myAvailabilitySet`adlı bir kullanılabilirlik kümesi oluşturur:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Hata etki alanları, ortak bir güç kaynağı ve ağ anahtarını paylaşan bir sanal makine gruplandırması tanımlar. Varsayılan olarak, kullanılabilirlik kümesi içinde yapılandırılan sanal makineler, en çok üç hata etki alanı arasında ayrılır. Fikir, bu hata etki alanlarından birindeki bir donanım sorununun uygulamanızı çalıştıran her VM 'yi etkilemesidir. Azure, VM 'Leri bir kullanılabilirlik kümesine yerleştirirken hata etki alanlarına otomatik olarak dağıtır.

Yükseltme etki alanları, sanal makine gruplarını ve aynı anda yeniden başlatılabilen temel fiziksel donanımı gösterir. Yükseltme etki alanlarının yeniden başlatıldığı sıra, planlı bakım sırasında sıralı olmayabilir, ancak aynı anda yalnızca bir yükseltme yeniden başlatılır. Azure, bir kullanılabilirlik sitesine yerleştirilirken sanal makinelerinizi yükseltme etki alanları arasında otomatik olarak dağıtır.

[VM 'lerin kullanılabilirliğini yönetme](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)hakkında daha fazla bilgi edinin.

## <a name="create-the-linux-vms"></a>Linux VM 'lerini oluşturma
Internet erişimli VM 'Leri desteklemek için depolama ve ağ kaynakları oluşturdunuz. Şimdi bu VM 'Leri oluşturalım ve parola olmayan bir SSH anahtarıyla güvenli hale getirmeye izin verin. Bu durumda, en son LTS 'yi temel alan bir Ubuntu VM oluşturacağız. [Azure VM görüntülerini bulma](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)bölümünde açıklandığı gibi, bu görüntü bilgilerini `azure vm image list`kullanarak bulduk.

Komut `azure vm image list westeurope canonical | grep LTS`kullanarak bir görüntü seçtik. Bu durumda `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`kullanırız. Son alan için `latest`, gelecekte her zaman en son derlemeyi edinmemiz için geçiririz. (Kullandığımız dize `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Bu sonraki adım, Linux veya Mac 'te **SSH-keygen-t RSA-b 2048**kullanarak zaten ssh rsa ortak ve özel anahtar çifti oluşturmuş olan herkese tanıdık gelecektir. `~/.ssh` dizininizde hiç sertifika anahtarı çifti yoksa, bunları oluşturabilirsiniz:

* `azure vm create --generate-ssh-keys` seçeneği kullanılarak otomatik olarak.
* [Kendiniz oluşturmak için yönergeleri](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)kullanarak el ile.

Alternatif olarak, VM oluşturulduktan sonra SSH bağlantılarınızın kimliğini doğrulamak için `--admin-password` yöntemini kullanabilirsiniz. Bu yöntem genellikle daha az güvenlidir.

Tüm kaynaklarımızı ve bilgilerinizi `azure vm create` komutuyla birlikte yaparak VM 'yi oluşturacağız:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Çıktı:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Varsayılan SSH anahtarlarınızı kullanarak sanal makinenize hemen bağlanabilirsiniz. Yük dengeleyiciden geçirdiğimiz için uygun bağlantı noktasını belirttiğinizden emin olun. (İlk sanal makine için, 4222 numaralı bağlantı noktasını VM 'imize iletecek NAT kuralını ayarladık.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Çıktı:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Devam edin ve ikinci sanal makineyi aynı şekilde oluşturun:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Şimdi de `azure vm show myResourceGroup myVM1` komutunu kullanarak ne oluşturduğunuz inceleyebilirsiniz. Bu noktada, yalnızca SSH anahtar çiftinizle oturum açabilmeniz (parolalar devre dışı bırakıldığı için) Azure 'da bir yük dengeleyicinin arkasındaki Ubuntu sanal makinelerinizi çalıştırıyorsunuz. NGINX veya httpd 'yi yükleyebilir, bir Web uygulaması dağıtabilir ve her iki VM 'ye yük dengeleyici aracılığıyla trafik akışını görebilirsiniz.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Çıktı:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Ortamı bir şablon olarak dışarı aktarma
Bu ortamı oluşturduğunuza göre, aynı parametrelerle veya bununla eşleşen bir üretim ortamıyla ek bir geliştirme ortamı oluşturmak isterseniz ne olur? Kaynak Yöneticisi, ortamınız için tüm parametreleri tanımlayan JSON şablonları kullanır. Bu JSON şablonuna başvurarak tüm ortamları oluşturursunuz. JSON [şablonlarını el ile](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oluşturabilir veya var olan bir ortamı, sızın için JSON şablonu oluşturmak üzere dışarı aktarabilirsiniz:

```azurecli
azure group export --name myResourceGroup
```

Bu komut, geçerli çalışma dizininizde `myResourceGroup.json` dosyasını oluşturur. Bu şablondan bir ortam oluşturduğunuzda, yük dengeleyici, ağ arabirimleri veya VM 'Ler için adlar da dahil olmak üzere tüm kaynak adlarını girmeniz istenir. Daha önce gösterilen `azure group export` komutuna `-p` veya `--includeParameterDefaultValue` parametresini ekleyerek, bu adları şablon dosyanızda doldurabilirsiniz. Kaynak adlarını belirtmek için JSON şablonunuzu düzenleyin veya kaynak adlarını belirten [Parameters. JSON dosyası oluşturun](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

Şablonunuzda bir ortam oluşturmak için:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

[Şablonlardan dağıtma hakkında daha fazla](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)bilgi edinmek isteyebilirsiniz. Ortamları artımlı olarak güncelleştirme, parametreler dosyasını kullanma ve şablonları tek bir depolama konumundan erişme hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Artık birden çok ağ bileşeni ve VM ile çalışmaya başlamaya hazırsınız. Bu örnek ortamı, burada tanıtılan temel bileşenleri kullanarak uygulamanızı oluşturmak için kullanabilirsiniz.
