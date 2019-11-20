---
title: Azure CLı kullanarak bölge yedekli VM 'Ler için Yük Dengeleme
titlesuffix: Azure Load Balancer
description: Azure CLı kullanarak bölge yedekli ön uç ile Genel Standart Load Balancer oluşturma hakkında bilgi edinin
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: 6a22ac9a2727c537d98e692e67076637fe8cc457
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274324"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Azure CLı kullanarak tüm kullanılabilirlik bölgelerindeki VM 'Lerin yükünü dengeleme

Bu makalede, birden çok DNS kaydına bağımlılığı olmadan bölge artıklığı elde etmek için bölgesel olarak yedekli ön uç ile genel [Standart Load Balancer](https://aka.ms/azureloadbalancerstandard) oluşturma adımları sağlanır. Tek bir ön uç IP adresi otomatik olarak bölge yedekli olur.  Yük dengeleyiciniz için bir bölge yedekli ön ucu kullanarak tek bir IP adresi ile, artık tüm Kullanılabilirlik Alanları bir bölgedeki sanal ağdaki herhangi bir VM 'ye ulaşabilirsiniz. Uygulamalarınızı beklenmeyen hatalardan veya tüm veri merkezinin kaybedilmesinden korumak için kullanılabilirlik alanlarından yararlanın.

Standart Yük Dengeleyici ile Kullanılabilirlik alanlarını kullanma hakkında daha fazla bilgi için [Standart Yük Dengeleyici ve Kullanılabilirlik Alanları](load-balancer-standard-availability-zones.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLı sürüm 2.0.17 veya üstünü çalıştırıyor olmanız gerekir.  Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Kullanılabilirlik bölgeleri, seçili Azure kaynakları ve bölgeler ve sanal makine boyutu aileleri için kullanılabilir. Kullanmaya başlamak nasıl daha fazla bilgi ve hangi Azure kaynakları, bölgeleri ve kullanılabilirlik alanları ile deneyebilirsiniz sanal makine boyutu aileleri için bkz. [kullanılabilirlik alanlarına genel bakış](https://docs.microsoft.com/azure/availability-zones/az-overview). Destek için [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) üzerinden bize ulaşabilir veya [bir Azure destek bileti açabilirsiniz](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group#az-group-create) ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek *westeurope* konumunda *Myresourcegroupslb* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Bölgesel olarak yedekli genel IP standardı oluşturma
Uygulamanıza İnternet’ten erişmek için yük dengeleyicinin genel IP adresi gereklidir. Bölgesel olarak yedekli ön uç, bir bölgedeki tüm kullanılabilirlik bölgeleri tarafından aynı anda sunulur. [Az Network public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create)komutuyla bölge YEDEKLI genel IP adresi oluşturun. Standart bir genel IP adresi oluşturduğunuzda, bu bölge varsayılan olarak bölgesel olarak yedekli olur.

Aşağıdaki örnek, *Myresourcegrouploadbalancer* kaynak grubu Içinde *Mypublicıp* adlı BIR bölge yedekli genel IP adresi oluşturur.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Azure Standart Load Balancer oluşturma
Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:
- Yük dengeleyicideki gelen ağ trafiğini alan bir ön uç IP havuzu.
- Ön uç havuzunun yük dengelemesi yapılmış ağ trafiğini gönderdiği bir arka uç IP havuzu.
- Arka uç sanal makine örneklerinin durumunu belirleyen bir durum araştırması.
- Trafiğin sanal makinelere dağıtımını tanımlayan bir yük dengeleyici kuralı.

### <a name="create-the-load-balancer"></a>Yük dengeleyiciyi oluşturma
[Az Network lb Create](/cli/azure/network/lb#az-network-lb-create)komutuyla bir standart yük dengeleyici oluşturun. Aşağıdaki örnek *Myloadbalancer* adlı bir yük dengeleyici oluşturur ve *Mypublicıp* adresini ön uç IP yapılandırmasına atar.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>80 numaralı bağlantı noktasında durum araştırması oluşturma

Sistem durumu araştırması tüm sanal makine örneklerini denetleyerek ağ trafiği gönderdiklerinden emin olur. Sistem durumu denetimi başarısız olan sanal makine örnekleri tekrar çevrimiçi olana ve sistem durumu denetimi iyi olduğuna karar verene kadar yük dengeleyiciden kaldırılır. Sanal makinelerin durumunu izlemek için az Network lb araştırması oluştur komutuyla bir sistem durumu araştırması oluşturun. TCP durum araştırması oluşturmak için [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) komutunu kullanın. Aşağıdaki örnek *myHealthProbe* adında bir durum araştırması oluşturur:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>80 numaralı bağlantı noktası için yük dengeleyici kuralı oluşturma
Yük dengeleyici kuralı, gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırmasını ve trafiği almak için arka uç IP havuzunu tanımlar. *myFrontEndPool* ön uç havuzunda 80 numaralı bağlantı noktasını dinlemek ve yine 80 numaralı bağlantı noktasını kullanarak *myBackEndPool* arka uç adres havuzuna yük dengelemesi yapılmış ağ trafiğini göndermek için [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) ile *myLoadBalancerRuleWeb* yük dengeleyici kuralı oluşturun.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
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

MyResourceGroup adlı bir alt ağ ile Myvnet adlı bir  sanal ağ oluşturun [az Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create)komutunu kullanın.


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

[Az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)komutunu kullanarak sanal ağınıza gelen bağlantıları tanımlamak Için *Mynetworksecuritygroup* adlı ağ güvenlik grubu oluşturun.

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

80 numaralı bağlantı noktası için, [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)Ile *Mynetworksecuritygrouprule* adlı bir ağ güvenlik grubu kuralı oluşturun.

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
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
[Az Network Nic Create](/cli/azure/network/nic#az-network-nic-create) ile üç sanal NIC oluşturun ve BUNLARı genel IP adresi ve ağ güvenlik grubuyla ilişkilendirin. Aşağıdaki örnek altı sanal NIC oluşturur. (Sonraki adımlarda uygulamanız için oluşturduğunuz her bir VM için bir sanal NIC). İstediğiniz zaman ek sanal NIC’ler ve VM’ler oluşturabilir ve bunları yük dengeleyiciye ekleyebilirsiniz:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma
Bu örnekte, bölge 1, bölge 2 ve bölge 3 ' te, yük dengeleyici için arka uç sunucular olarak kullanılacak üç sanal makine oluşturursunuz. Yük dengeleyicinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere NGıNX de yüklersiniz.

### <a name="create-cloud-init-config"></a>cloud-init yapılandırması oluşturma

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

### <a name="create-the-zonal-virtual-machines"></a>Bölgesel sanal makinelerini oluşturma
[Az VM Create](/cli/azure/vm#az-vm-create) bölge 1, bölge 2 ve bölge 3 Ile VM 'ler oluşturun. Aşağıdaki örnek, her bölgede bir VM oluşturur ve henüz yoksa SSH anahtarları oluşturur:

*Westeurope* konumunun her bölgesinde (bölge 1, bölge 2 ve bölge 3) bir VM oluşturun.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

[Az Network public-IP Show](/cli/azure/network/public-ip#az-network-public-ip-show)kullanarak yük DENGELEYICININ genel IP adresini alın. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Sonra da genel IP adresini bir web tarayıcısına girebilirsiniz. Yük dengeleyicinin VM’lere trafik dağıtmaya başlamadan önce VM’lerin hazır olması için birkaç dakika geçmesi gerektiğini unutmayın. Uygulama, yük dengeleyicinin trafiği dağıttığı VM’nin ana bilgisayar adı ile aşağıdaki gibi görüntülenir:

![Node.js uygulaması çalıştırma](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Yük dengeleyicinin trafiği, uygulamanızı çalıştıran üç kullanılabilirlik bölgesindeki VM 'Ler arasında dağıtmalarını görmek için belirli bir bölgedeki bir sanal makineyi durdurabilir ve tarayıcınızı yenileyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- [Standart Yük Dengeleyici](./load-balancer-standard-overview.md) hakkında daha fazla bilgi edinin



