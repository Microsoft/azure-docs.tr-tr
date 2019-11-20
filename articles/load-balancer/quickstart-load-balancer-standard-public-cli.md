---
title: 'Hızlı başlangıç: Genel Standart Load Balancer oluşturma-Azure CLı'
titlesuffix: Azure Load Balancer
description: Bu hızlı başlangıçta, Azure CLI kullanarak genel bir yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: fb292e268df57dda8ed2db56a915270b7fe51bb2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839827"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak VM 'Lerin yükünü dengelemek için Standart Load Balancer oluşturma

Bu hızlı başlangıçta nasıl Standart Yük Dengeleyici oluşturulacağı gösterilmektedir. Yük dengeleyiciyi test etmek için, Ubuntu server çalıştıran iki sanal makine (VM) dağıtın ve iki sanal makine arasında bir web uygulamasının yük dengelemesini yapın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.28 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](https://docs.microsoft.com/cli/azure/group) ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek *eastus* konumunda *myResourceGroupSLB* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-standard-ip-address"></a>Genel Standart IP adresi oluşturma

Web uygulamanıza İnternet’ten erişmek için yük dengeleyicinin genel IP adresi gereklidir. Standart Yük Dengeleyici yalnızca Standart Genel IP adreslerini destekler. [myResourceGroupSLB](https://docs.microsoft.com/cli/azure/network/public-ip) içinde *myPublicIP* adlı bir Standart Genel IP adresi oluşturmak için *az network public-ip create* komutunu kullanın.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

## <a name="create-azure-load-balancer"></a>Azure yük dengeleyici oluşturma

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:
  - Yük dengeleyicideki gelen ağ trafiğini alan bir ön uç IP havuzu.
  - Ön uç havuzunun yük dengelemesi yapılmış ağ trafiğini gönderdiği bir arka uç IP havuzu.
  - Arka uç sanal makine örneklerinin durumunu belirleyen bir durum araştırması.
  - Trafiğin sanal makinelere dağıtımını tanımlayan bir yük dengeleyici kuralı.

### <a name="create-the-load-balancer"></a>Yük dengeleyiciyi oluşturma

Önceki adımda oluşturduğunuz [myPublicIP](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) genel IP adresiyle ilişkilendirilmiş **myBackEndPool** adlı bir arka uç havuzunu ve **myFrontEnd** adlı bir ön uç havuzunu içeren **myLoadBalancer** adlı **az network lb create** ile genel bir Azure Load Balancer oluşturun.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Durum araştırması oluşturma

Sistem durumu araştırması tüm sanal makine örneklerini denetleyerek ağ trafiği gönderdiklerinden emin olur. Sistem durumu denetimi başarısız olan sanal makine örnekleri tekrar çevrimiçi olana ve sistem durumu denetimi iyi olduğuna karar verene kadar yük dengeleyiciden kaldırılır. Sanal makinelerin durumunu izlemek için [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) ile bir durum araştırması oluşturun. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırmasını ve trafiği almak için arka uç IP havuzunu tanımlar. *myFrontEnd* ön uç havuzunda 80 numaralı bağlantı noktasını dinlemek ve yine 80 numaralı bağlantı noktasını kullanarak [myBackEndPool](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) arka uç adres havuzuna yük dengelemesi yapılmış ağ trafiğini göndermek için *az network lb rule create* ile *myLoadBalancerRuleWeb* yük dengeleyici kuralı oluşturun. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Sanal ağ yapılandırma

VM’leri dağıtmadan ve dengeleyicinizi test etmeden önce yardımcı sanal ağ kaynaklarını oluşturun.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

*az network vnet create* komutunu kullanarak *myResourceGroup* içinde *mySubnet* adlı bir alt ağ ile [myVnet](https://docs.microsoft.com/cli/azure/network/vnet) adlı bir sanal ağ oluşturun.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Standart Yük Dengeleyici için, arka uç adresindeki sanal makinelerin bir Ağ Güvenliği grubuna ait olan NIC’lere sahip olması gerekir. Sanal ağınıza gelen bağlantıları tanımlamak için ağ güvenlik grubu oluşturun.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Ağ güvenlik grubu kuralı oluşturma

80 numaralı bağlantı noktasından gelen bağlantılara izin vermek için bir ağ güvenlik grubu kuralı oluşturun.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>NIC’leri oluşturma

[az network nic create](/cli/azure/network/nic#az-network-nic-create) ile üç ağ arabirimi oluşturun ve bunları Genel IP adresi ve ağ güvenlik grubu ile ilişkilendirin. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```


## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu örnekte, yük dengeleyici için arka uç sunucular olarak kullanılacak üç sanal makine oluşturacaksınız. Yük dengeleyicinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere NGINX de yükleyin.

### <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma

[az vm availabilityset create](/cli/azure/network/nic) komutunu kullanarak kullanılabilirlik kümesi oluşturun

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupSLB \
    --name myAvailabilitySet
```

### <a name="create-three-virtual-machines"></a>Üç sanal makine oluşturma

cloud-init yapılandırma dosyasını kullanarak NGINX’i yükleyin ve Linux sanal makinesinde 'Merhaba Dünya' Node.js uygulaması çalıştırın. Geçerli kabuğunuzda cloud-init.txt adlı bir dosya oluşturup aşağıdaki yapılandırmayı kopyalayıp kabuğa yapıştırın. Başta birinci satır olmak üzere cloud-init dosyasının tamamını doğru bir şekilde kopyaladığınızdan emin olun:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
``` 
 
[az vm create](/cli/azure/vm#az-vm-create) komutunu kullanarak sanal makineleri oluşturun.

 ```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```
Sanal makinelerin dağıtılması birkaç dakika sürebilir.

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Yük dengeleyicinin genel IP adresini almak için [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) komutunu kullanın. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
   ![Yük dengeleyiciyi test etme](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group#az-group-delete) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurecli-interactive 
  az group delete --name myResourceGroupSLB
```
## <a name="next-step"></a>Sonraki adım
Bu hızlı başlangıçta, bir Standart Load Balancer oluşturdunuz, buna VM’ler eklediniz, yük dengeleyici trafik kuralını ve durum araştırmasını yapılandırdınız ve yük dengeleyiciyi test ettiniz. Azure Load Balancer hakkında daha fazla bilgi almak için Azure Load Balancer öğreticisine devam edin.

> [!div class="nextstepaction"]
> [Azure Load Balancer öğreticileri](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

