---
title: Bir iç temel yük dengeleyici - Azure CLI'yı oluşturma
titlesuffix: Azure Load Balancer
description: Bu makalede, Azure CLı kullanarak iç yük dengeleyici oluşturmayı öğrenin
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/27/2018
ms.author: allensu
ms.openlocfilehash: 8492ce820a31183b0b3078b1fb366c25a39b7639
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076074"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Azure CLI kullanarak sanal makinelerin yük dengelemesi için iç yük dengeleyici oluşturma

Bu makalede, sanal makinelerin yük dengelemesi için iç yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir. Yük dengeleyiciyi test etmek için, Ubuntu server çalıştıran iki sanal makine (VM) dağıtarak bir web uygulamasının yük dengelemesini sağlayın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.28 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](https://docs.microsoft.com/cli/azure/group) ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek *eastus* konumunda *myResourceGroupILB* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

*az network vnet create* komutunu kullanarak *myResourceGroup* içinde *mySubnet* adlı bir alt ağ ile [myVnet](https://docs.microsoft.com/cli/azure/network/vnet) adlı bir sanal ağ oluşturun.

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>Temel Yük Dengeleyici Oluşturma

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:
  - Yük dengeleyicideki gelen ağ trafiğini alan bir ön uç IP yapılandırması.
  - Ön uç havuzunun yük dengelemesi yapılmış ağ trafiğini gönderdiği bir arka uç IP havuzu.
  - Arka uç sanal makine örneklerinin durumunu belirleyen bir durum araştırması.
  - Trafiğin sanal makinelere dağıtımını tanımlayan bir yük dengeleyici kuralı.

### <a name="create-the-load-balancer"></a>Yük dengeleyiciyi oluşturma

"* Kısmına 10.0.0.7" özel IP adresi ile ilişkili **Mybackendpool** adlı bir arka uç havuzu olan **myön uç**adlı ön uç IP **yapılandırmasını içeren,** [az Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) adlı bir iç Load Balancer oluşturun.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>Durum araştırması oluşturma

Durum araştırması tüm sanal makine örneklerini denetleyerek ağ trafiği aldıklarından emin olur. Sistem durumu denetimi başarısız olan sanal makine örnekleri tekrar çevrimiçi olana ve sistem durumu denetimi iyi olduğuna karar verene kadar yük dengeleyiciden kaldırılır. Sanal makinelerin durumunu izlemek için [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) ile bir durum araştırması oluşturun. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırmasını ve trafiği almak için arka uç IP havuzunu tanımlar. *myFrontEnd* ön uç havuzunda 80 numaralı bağlantı noktasını dinlemek ve yine 80 numaralı bağlantı noktasını kullanarak [myBackEndPool](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) arka uç adres havuzuna yük dengelemesi yapılmış ağ trafiğini göndermek için *az network lb rule create* ile *myHTTPRule* yük dengeleyici kuralı oluşturun. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>Arka uç adres havuzu için sunucular oluşturma

VM’leri dağıtmadan ve dengeleyicinizi test etmeden önce yardımcı sanal ağ kaynaklarını oluşturun.

### <a name="create-nics"></a>NIC’leri oluşturma

[az network nic create](/cli/azure/network/nic#az-network-nic-create) ile iki ağ arabirimi oluşturun ve bunları özel IP adresi ile ilişkilendirin. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu örnekte, yük dengeleyici için arka uç sunucular olarak kullanılacak iki sanal makine oluşturacaksınız. Yük dengeleyicinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere NGINX de yükleyin.

### <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma

[az vm availabilityset create](/cli/azure/network/nic) komutunu kullanarak kullanılabilirlik kümesi oluşturun

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>İki sanal makine oluşturma

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
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
Sanal makinelerin dağıtılması birkaç dakika sürebilir.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Yük dengeleyiciyi test etmek için sanal makine oluşturma

Yük dengeleyiciyi test etmek için, *myVMTest* adlı bir sanal makine oluşturun ve bunu *myNic3* ile ilişkilendirin.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>İç yük dengeleyiciyi test etme

Yük dengeleyiciyi test etmek için önce yük dengeleyicinin özel IP adresini almanız gerekir. Ardından, myVMTest adlı sanal makinede oturum açın ve web tarayıcısının adres çubuğuna özel IP adresini yazın.

Yük dengeleyicinin özel IP adresini almak için [az network lb show](/cli/azure/network/public-ip) komutunu kullanın. Özel IP adresini kopyalayıp *myVMTest* adlı sanal makinenizin web tarayıcısında adres çubuğuna yapıştırın.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
``` 
![Yük dengeleyiciyi sınama](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group#az-group-delete) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, iç Temel Yük Dengeleyici oluşturdunuz, buna sanal makineler eklediniz, yük dengeleyici trafik kuralını ve durum araştırmasını yapılandırdınız ve yük dengeleyiciyi test ettiniz. Yük dengeleyiciler ve bunların ilişkili kaynakları hakkında daha fazla bilgi edinmek için nasıl yapılır makaleleriyle devam edin.
