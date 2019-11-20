---
title: Öğretici-Azure 'da Linux sanal makinelerinin yükünü dengeleyin
description: Bu öğreticide, üç Linux sanal makinesi arasında yüksek kullanılabilirliğe sahip ve güvenli uygulama için bir yük dengeleyici oluşturmak üzere Azure CLI kullanmayı öğreneceksiniz
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: daad74ae5f046edb1b4bf6eef547c963e52593f5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034427"
---
# <a name="tutorial-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application-with-the-azure-cli"></a>Öğretici: Azure CLI ile yüksek oranda kullanılabilir bir uygulama oluşturmak için Azure’da Linux sanal makinelerinde yük dengeleme

Yük dengeleme, gelen istekleri birden çok sanal makineye dağıtarak yüksek düzeyde kullanılabilirlik sunar. Bu öğreticide, Azure yük dengeleyicisinin trafiği dağıtan ve yüksek kullanılabilirlik sağlayan farklı bileşenleri hakkında bilgi edinebilirsiniz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Azure yük dengeleyici oluşturma
> * Yük dengeleyici durum yoklaması oluşturma
> * Yük dengeleyici trafik kuralları oluşturma
> * Basit bir Node.js uygulaması oluşturmak için cloud-init kullanma
> * Sanal makineler oluşturma ve yük dengeleyici oluşturma
> * Çalışan yük dengeleyiciyi görüntüleme
> * VM’leri yük dengeleyiciye ekleme ve kaldırma

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="azure-load-balancer-overview"></a>Azure yük dengeleyiciye genel bakışı
Azure yük dengeleyici, gelen trafiği iyi durumdaki VM'ler arasında dağıtmak için yüksek kullanılabilirlik sağlayan bir 4. Katman (TCP, UDP) yük dengeleyicidir. Yük dengeleyici durum araştırması, her VM'deki belirli bir bağlantı noktasını izler ve trafiği yalnızca çalışır durumdaki VM'lere yönlendirir.

Bir veya daha fazla genel IP adresi içeren bir ön uç IP yapılandırması tanımlayın. Bu ön uç IP yapılandırması yük dengeleyicinize ve uygulamalarınıza İnternet üzerinden erişilmesine izin verir. 

Sanal makineler, sanal ağ arabirim kartını (NIC) kullanarak bir yük dengeleyiciye bağlanır. Trafiği VM’lere dağıtmak için, bir arka uç adres havuzunda yük dengeleyiciye bağlı sanal NIC’lerin IP adresleri barındırılır.

Trafiğin akışını denetlemek için VM’lerinizle eşlenen belirli bağlantı noktaları ve protokoller için yük dengeleyici kuralları tanımlayın.

Önceki öğreticiyi [sanal makine ölçek kümesi oluşturma](tutorial-create-vmss.md) bölümüne kadar izlediğinizde sizin için bir yük dengeleyici oluşturulur. Bu bileşenlerin tümü sizin için ölçek kümesinin parçası olarak yapılandırılır.


## <a name="create-azure-load-balancer"></a>Azure yük dengeleyici oluşturma
Bu bölümde yük dengeleyicinin her bir bileşenini nasıl oluşturacağınız ve yapılandıracağınız açıklanmaktadır. Yük dengeleyicinizi oluşturmadan önce [az group create](/cli/azure/group) komutu ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroupLoadBalancer* adlı bir kaynak grubu oluşturur:

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma
Uygulamanıza İnternet’ten erişmek için yük dengeleyicinin genel IP adresi gereklidir. [az network public-ip create](/cli/azure/network/public-ip) komutu ile bir genel IP adresi oluşturun. Aşağıdaki örnek, *myResourceGroupLoadBalancer* kaynak grubunda *myPublicIP* adlı bir genel IP adresi oluşturur:

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Yük dengeleyici oluşturma
[az network lb create](/cli/azure/network/lb) komutu ile bir yük dengeleyici oluşturun. Aşağıdaki örnek, *myLoadBalancer* adlı bir yük dengeleyici oluşturur ve *myPublicIP* adresini ön uç IP yapılandırmasına atar:

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma
Yük dengeleyicinin uygulamanızın durumunu izlemesine izin vermek için durum araştırması kullanabilirsiniz. Durum yoklaması, durum denetimlerine verdikleri yanıtlara göre VM’leri dinamik olarak yük dengeleyici rotasyonuna ekler ve kaldırır. VM, 15 saniyelik aralıklarda art arda iki kez başarısız olursa varsayılan olarak yük dengeleyici dağıtımından kaldırılır. Bir protokolü temel alan bir durum araştırması veya uygulamanız için belirli bir sistem durumu denetim sayfası oluşturun. 

Aşağıdaki örnek bir TCP araştırması oluşturur. Ayrıca daha ayrıntılı sistem durumu denetimleri için özel HTTP araştırmaları oluşturabilirsiniz. Özel bir HTTP araştırması kullandığınızda *healthcheck.js* gibi bir sistem durumu denetimi sayfası oluşturmanız gerekir. Konağı dönüşüm içinde tutmak üzere araştırmanın yük dengeleyici için bir **HTTP 200 OK** yanıtı döndürmesi gerekir.

TCP durum araştırması oluşturmak için [az network lb probe create](/cli/azure/network/lb/probe) komutunu kullanın. Aşağıdaki örnek *myHealthProbe* adında bir durum araştırması oluşturur:

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma
Trafiğin VM’lere dağıtımını tanımlamak için bir yük dengeleyici kuralı kullanılır. Gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırması ve trafiği almak için arka uç IP havuzu tanımlamanız gerekir. Yalnızca durumu iyi olan VM’lerin trafik almasını sağlamak için kullanılacak durum araştırmasını da tanımlamanız gerekir.

[az network lb rule create](/cli/azure/network/lb/rule) komutuyla bir yük dengeleyici kuralı oluşturun. Aşağıdaki örnek, *myHealthProbe* sağlık araştırmasını kullanan ve trafiği *80* numaralı bağlantı noktasında dengeleyen *myLoadBalancerRule* adında bir kural oluşturur:

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Sanal ağ yapılandırma
VM’leri dağıtmadan ve dengeleyicinizi sınamadan önce yardımcı sanal ağ kaynaklarını oluşturun. Sanal ağlar hakkında daha fazla bilgi edinmek için [Azure Sanal Ağlarını Yönetme](tutorial-virtual-network.md) öğreticisine göz atın.

### <a name="create-network-resources"></a>Ağ kaynakları oluşturma
[az network vnet create](/cli/azure/network/vnet) komutu ile bir sanal ağ oluşturun. Aşağıdaki örnek *mySubnet* alt ağına sahip *myVnet* adında bir sanal ağ oluşturur:

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Bir ağ güvenlik grubu eklemek için [az network nsg create](/cli/azure/network/nsg) komutunu kullanın. Aşağıdaki örnek *myNetworkSecurityGroup* adında bir ağ güvenlik grubu oluşturur:

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule) komutu ile bir ağ güvenlik grubu kuralı oluşturun. Aşağıdaki örnek *myNetworkSecurityGroupRule* adında bir ağ güvenlik grubu kuralı oluşturur:

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

Sanal NIC’ler [az network nic create](/cli/azure/network/nic) komutu ile oluşturulur. Aşağıdaki örnek üç sanal NIC oluşturur. (Sonraki adımlarda uygulamanız için oluşturduğunuz her bir VM için bir sanal NIC). İstediğiniz zaman ek sanal NIC’ler ve VM’ler oluşturabilir ve bunları yük dengeleyiciye ekleyebilirsiniz:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

Üç sanal NIC’nin tamamı oluşturulduğunda sonraki adıma geçin


## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

### <a name="create-cloud-init-config"></a>cloud-init yapılandırması oluşturma
[İlk önyüklemede Linux sanal makinelerini özelleştirme](tutorial-automate-vm-deployment.md) konulu önceki bir öğreticide, cloud-init ile VM özelleştirmeyi nasıl otomatikleştirebileceğinizi öğrendiniz. Sonraki adımda NGINX’i yüklemek için aynı cloud-init yapılandırma dosyasını kullanabilir ve basit bir 'Merhaba Dünya' Node.js uygulaması çalıştırabilirsiniz. Yük dengeleyiciyi çalışırken görüntülemek için öğreticinin sonunda bir web tarayıcısından bu basit uygulamaya erişebilirsiniz.

Geçerli kabuğunuzda *cloud-init.txt* adlı bir dosya oluşturup aşağıdaki yapılandırmayı yapıştırın. Örneğin, dosyayı yerel makinenizde değil Cloud Shell’de oluşturun. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud-init.txt` adını girin. Başta birinci satır olmak üzere cloud-init dosyasının tamamının doğru bir şekilde kopyalandığından emin olun:

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

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma
Uygulamanızın yüksek oranda kullanılabilir olmasını sağlamak için VM’lerinizi bir kullanılabilirlik kümesine yerleştirin. Kullanılabilirlik kümeleri hakkında daha fazla bilgi edinmek için [Yüksek oranda kullanılabilir sanal makineler oluşturma](tutorial-availability-sets.md) başlıklı önceki öğreticiye göz atın.

[az vm availability-set create](/cli/azure/vm/availability-set) komutunu kullanarak bir kullanılabilirlik kümesi oluşturun. Aşağıdaki örnek *myAvailabilitySet* adında bir kullanılabilirlik kümesi oluşturur:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Artık [az vm create](/cli/azure/vm) komutu ile VM’leri oluşturabilirsiniz. Aşağıdaki örnek üç VM ve yoksa SSH anahtarlarını oluşturur:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Azure CLI sizi isteme geri döndürdükten sonra çalışmaya devam eden arka plan görevleri vardır. `--no-wait` parametresi tüm görevlerin tamamlanmasını beklemez. Uygulamaya erişmeniz birkaç dakika sürebilir. Yük dengeleyici durum araştırması, uygulamanın her bir VM üzerinde ne zaman çalıştığını otomatik olarak algılar. Uygulama çalıştıktan sonra yük dengeleyici kuralı trafiği dağıtmaya başlar.


## <a name="test-load-balancer"></a>Yük dengeleyiciyi sınama
[az network public-ip show](/cli/azure/network/public-ip) komutuyla yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek, daha önce oluşturulan *myPublicIP* için IP adresini alır:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Sonra da genel IP adresini bir web tarayıcısına girebilirsiniz. Yük dengeleyicinin VM’lere trafik dağıtmaya başlamadan önce VM’lerin hazır olması için birkaç dakika geçmesi gerektiğini unutmayın. Uygulama, yük dengeleyicinin trafiği dağıttığı VM’nin ana bilgisayar adı ile aşağıdaki gibi görüntülenir:

![Node.js uygulaması çalıştırma](./media/tutorial-load-balancer/running-nodejs-app.png)

Yük dengeleyicinin trafiği, uygulamanızı çalıştıran üç VM’ye dağıtma işlemini görmek için web tarayıcınızı yenilemeye zorlayabilirsiniz.


## <a name="add-and-remove-vms"></a>VM’leri ekleme ve kaldırma
Uygulamanızı çalıştıran VM’lerde işletim sistemi güncelleştirmelerini yükleme gibi bakım işlemleri gerçekleştirmeniz gerekebilir. Uygulamanıza gelen trafiği fazla trafiği yönetmek için ek VM’lere ihtiyaç duyabilirsiniz. Bu bölümde VM’yi yük dengeleyiciden nasıl kaldırabileceğiniz veya ekleyebileceğiniz gösterilmektedir.

### <a name="remove-a-vm-from-the-load-balancer"></a>VM’yi yük dengeleyiciden kaldırma
VM’yi arka uç adres havuzundan [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool) komutu ile kaldırabilirsiniz. Aşağıdaki örnek **myVM2** için sanal NIC’yi *myLoadBalancer*’dan kaldırır:

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Yük dengeleyicinin trafiği, uygulamanızı çalıştıran kalan iki VM’ye dağıtma işlemini görmek için web tarayıcınızı yenilemeye zorlayabilirsiniz. Artık işletim sistemi güncelleştirmelerini yükleme veya VM’yi yeniden başlatma gibi bakım işlemlerini VM üzerinde gerçekleştirebilirsiniz.

Yük dengeleyiciye bağlı sanal NIC’lere sahip VM’lerin listesini görüntülemek için [az network lb address-pool show](/cli/azure/network/lb/address-pool) komutunu kullanın. Sanal NIC’nin kimliğini şu şekilde sorgulayın ve filtre uygulayın:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

Çıktı aşağıdaki örneğe benzer, burada VM 2 için sanal NIC’nın artık arka uç adres havuzunun bir parçası olmadığı gösterilmektedir:

```bash
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Yük dengeleyiciye VM ekleme
VM bakımını gerçekleştirdikten sonra veya kapasiteyi genişletmeniz gerekiyorsa arka uç adres havuzuna [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool) komutu ile bir VM ekleyebilirsiniz. Aşağıdaki örnek **myVM2** için sanal NIC’yi *myLoadBalancer*’a ekler:

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Sanal NIC’nin arka uç adres havuzuna bağlı olduğundan emin olmak için önceki adımdaki [az network lb address-pool show](/cli/azure/network/lb/address-pool) komutunu tekrar kullanın.


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir yük dengeleyici oluşturdunuz ve buna sanal makineler eklediniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure yük dengeleyici oluşturma
> * Yük dengeleyici durum yoklaması oluşturma
> * Yük dengeleyici trafik kuralları oluşturma
> * Basit bir Node.js uygulaması oluşturmak için cloud-init kullanma
> * Sanal makineler oluşturma ve yük dengeleyici oluşturma
> * Çalışan yük dengeleyiciyi görüntüleme
> * VM’leri yük dengeleyiciye ekleme ve kaldırma

Azure sanal ağ bileşenleri hakkında daha fazla bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [VM’leri ve sanal ağları yönetme](tutorial-virtual-network.md)
