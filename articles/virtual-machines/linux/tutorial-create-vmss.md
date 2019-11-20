---
title: Öğretici-Azure 'da Linux için sanal makine ölçek kümesi oluşturma
description: Bu öğreticide, bir sanal makine ölçek kümesini kullanarak Linux VM'leri üzerinde yüksek oranda kullanılabilir bir uygulama oluşturmak ve dağıtmak için Azure CLI kullanmayı öğreneceksiniz
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b9368da24af3b0fe08045ed869c752eefe3912fd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034526"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux-with-the-azure-cli"></a>Öğretici: Azure CLI ile sanal makine ölçek kümesi oluşturma ve Linux üzerinde yüksek oranda kullanılabilir bir uygulama dağıtma

Sanal makine ölçek kümesi, birbiriyle aynı ve otomatik olarak ölçeklendirilen sanal makine kümesi dağıtmanızı ve yönetmenizi sağlar. Ölçek kümesi içindeki VM sayısını el ile ölçeklendirebilir veya CPU, bellek isteği ya da ağ trafiği gibi kaynak kullanımını temel alan otomatik ölçeklendirme kuralları tanımlayabilirsiniz. Bu öğreticide, Azure’da bir sanal makine ölçek kümesi dağıtılmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Cloud-init kullanarak ölçeklendirilecek bir uygulama oluşturma
> * Sanal makine ölçek kümesi oluşturma
> * Ölçek kümesindeki örnek sayısını artırma veya azaltma
> * Otomatik ölçeklendirme kuralları oluşturma
> * Ölçek kümesi örneklerine ait bağlantı bilgilerini görüntüleme
> * Ölçek kümesinde veri diskleri kullanma

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="scale-set-overview"></a>Ölçek Kümesine genel bakış
Sanal makine ölçek kümesi, birbiriyle aynı ve otomatik olarak ölçeklendirilen sanal makine kümesi dağıtmanızı ve yönetmenizi sağlar. Ölçek kümesindeki VM’ler, bir veya daha fazla *yerleştirme grubu* şeklinde mantık hatası ve güncelleme etki alanlarında dağıtılır. Bu gruplar, [kullanılabilirlik kümeleri](tutorial-availability-sets.md) gibi benzer şekilde yapılandırılmış VM’lerdir.

VM’ler, ölçek kümesinde gerektiğinde oluşturulur. Ölçek kümesinde VM eklenmesi veya kaldırılması işlemlerinin nasıl ve ne zaman gerçekleştirileceğini denetlemek için otomatik ölçeklendirme kurallarını tanımlanır. Bu kurallar, CPU yükü, bellek kullanımı veya ağ trafiği gibi ölçümlere dayalı olarak tetiklenebilir.

Bir Azure platform görüntüsü kullanılırsa ölçek kümeleri 1.000 adede kadar VM'i destekler. Ciddi derecede yükleme veya VM özelleştirme gereksinimleri olan iş yükleri için [özel bir VM görüntüsü oluşturulması](tutorial-custom-images.md) iyi olabilir. Özel bir görüntü kullanırken ölçek kümesinde 300 adede kadar VM oluşturabilirsiniz.


## <a name="create-an-app-to-scale"></a>Ölçeklendirilecek bir uygulama oluşturma
Üretim kullanımı için uygulamanızı yüklenmiş ve yapılandırılmış olarak içeren [özel bir VM görüntüsü oluşturmak](tutorial-custom-images.md) isteyebilirsiniz. Bu öğreticide, ölçek kümesini hemen iş başında görmek için VM’leri ilk önyüklemede özelleştirelim.

Daha önceki bir öğreticide cloud-init ile [ilk önyüklemede Linux sanal makinelerini özelleştirmeyi](tutorial-automate-vm-deployment.md) öğrendiniz. Aynı cloud-init yapılandırma dosyasını kullanarak NGINX’i yükleyebilir ve basit bir “Merhaba Dünya” Node.js uygulaması çalıştırabilirsiniz.

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


## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma
Ölçek kümesi oluşturabilmek için [az group create](/cli/azure/group#az-group-create) ile bir kaynak grubu oluşturun. Aşağıdaki örnek, *eastus* konumunda *myResourceGroupScaleSet* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroupScaleSet --location eastus
```

Bu adımda [az vmss create](/cli/azure/vmss#az-vmss-create) ile bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, *myScaleSet* adlı bir ölçek kümesi oluşturur, cloud-init dosyasını kullanarak VM’yi özelleştirir ve yoksa SSH anahtarlarını oluşturur:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer. Azure CLI sizi isteme geri döndürdükten sonra çalışmaya devam eden arka plan görevleri vardır. Uygulamaya erişmeniz birkaç dakika sürebilir.


## <a name="allow-web-traffic"></a>Web trafiğine izin verme
Sanal makine ölçek kümesinin bir parçası olarak otomatik olarak bir yük dengeleyici oluşturuldu. Yük dengeleyici, yük dengeleyici kurallarını kullanarak trafiği tanımlı bir VM'ler kümesi arasında dağıtır. Yük dengeleyiciye ilişkin kavramlar ve yapılandırma hakkında [Azure’da sanal makinelerin yükünü dengeleme](tutorial-load-balancer.md) adlı sıradaki öğreticide daha fazla bilgi edinebilirsiniz.

Trafiğin web uygulamanıza ulaşmasına izin vermek için [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) ile bir kural oluşturun. Aşağıdaki örnek *myLoadBalancerRuleWeb* adlı bir kural oluşturur:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Uygulamanızı test etme
Node.js uygulamanızı Web’de görmek için [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) ile yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek ölçek kümesinin bir parçası olarak oluşturulan *myScaleSetLBPublicIP* için IP adresini alır:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Genel IP adresini bir web tarayıcısına girin. Uygulama, yük dengeleyicinin trafiği dağıttığı VM’nin ana bilgisayar adı ile birlikte görüntülenir:

![Node.js uygulaması çalıştırma](./media/tutorial-create-vmss/running-nodejs-app.png)

Ölçek kümesini çalışırken görmek için web tarayıcınızı yenilemeye zorlayarak yük dengeleyicinin trafiği, uygulamanızı çalıştıran üç VM’ye dağıtmasını görebilirsiniz.


## <a name="management-tasks"></a>Yönetim görevleri
Ölçek kümesinin yaşam döngüsü boyunca bir veya daha fazla yönetim görevi çalıştırmanız gerekebilir. Ayrıca, çeşitli yaşam döngüsü görevlerini otomatikleştiren betikler oluşturmak isteyebilirsiniz. Azure CLI tüm bunları hızlıca yapabilmenizi sağlar. Aşağıda birkaç yaygın görev açıklanmaktadır.

### <a name="view-vms-in-a-scale-set"></a>Ölçek kümesindeki VM’leri görüntüleme
Ölçek kümenizde çalışan VM’lerin listesini görüntülemek için [az vmss list-instances](/cli/azure/vmss#az-vmss-list-instances) komutunu şu şekilde kullanın:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

Çıktı aşağıdaki örneğe benzer:

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="manually-increase-or-decrease-vm-instances"></a>VM örneklerinin sayısını el ile artırma veya azaltma
Ölçek kümesinde şu anda yer alan örneklerin sayısını görmek için [az vmss show](/cli/azure/vmss#az-vmss-show) komutunu kullanarak *sku.capacity* üzerinde bir sorgu çalıştırın:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Ardından [az vmss scale](/cli/azure/vmss#az-vmss-scale) ile ölçek kümesindeki sanal makinelerin sayısını elle artırabilir veya azaltabilirsiniz. Aşağıdaki örnek, ölçek kümenizdeki VM'lerin sayısını *3* olarak ayarlar:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```

### <a name="get-connection-info"></a>Bağlantı bilgilerini alma
Ölçek kümelerinizdeki VM'lere ilişkin bağlantı bilgilerini almak için [az vmss list-instance-connection-info](/cli/azure/vmss#az-vmss-list-instance-connection-info) komutunu kullanın. Bu komut, çıktı olarak her bir VM’nin genel IP adresini ve bağlantı noktasını verir. Bu bilgiler, SSH ile bağlanmanıza olanak sağlar:

```azurecli-interactive
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Ölçek kümeleri ile veri diskleri kullanma
Veri diskleri oluşturup ölçek kümeleri ile kullanabilirsiniz. Daha önceki bir öğreticide [Azure disklerini yönetmeyi](tutorial-manage-disks.md) öğrenirken işletim sistemi diski yerine veri diskleri üzerinde uygulama oluşturmaya yönelik en iyi yöntemleri ve performans geliştirmelerini genel hatlarıyla gördünüz.

### <a name="create-scale-set-with-data-disks"></a>Veri diskleri ile ölçek kümesi oluşturma
Ölçek kümesi oluşturup veri diskleri eklemek için `--data-disk-sizes-gb`az vmss create[ komutuna ](/cli/azure/vmss#az-vmss-create) parametresini ekleyin. Aşağıdaki örnek, her bir örneğe *50* GB’lık bir veri diski eklenmiş şekilde bir ölçek kümesi oluşturur:

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Örnekler ölçek kümesinden kaldırıldığında ekli tüm veri diskleri de kaldırılır.

### <a name="add-data-disks"></a>Veri diski ekleme
Ölçek kümenizdeki örneklere bir veri diski eklemek için [az vmss disk attach](/cli/azure/vmss/disk#az-vmss-disk-attach) komutunu kullanın. Aşağıdaki örnek, her bir örneğe *50* GB’lık bir veri diski ekler:

```azurecli-interactive
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Veri diskini çıkarma
Ölçek kümenizdeki örneklerden veri diskini kaldırmak için [az vmss disk detach](/cli/azure/vmss/disk#az-vmss-disk-detach) komutunu kullanın. Aşağıdaki örnek, her bir örnekten LUN *2*’deki veri diskini kaldırır:

```azurecli-interactive
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir sanal makine ölçek kümesi oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Cloud-init kullanarak ölçeklendirilecek bir uygulama oluşturma
> * Sanal makine ölçek kümesi oluşturma
> * Ölçek kümesindeki örnek sayısını artırma veya azaltma
> * Otomatik ölçeklendirme kuralları oluşturma
> * Ölçek kümesi örneklerine ait bağlantı bilgilerini görüntüleme
> * Ölçek kümesinde veri diskleri kullanma

Sanal makinelere ilişkin yük dengeleme kavramları hakkında daha fazla bilgi edinmek için sıradaki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Sanal makinelerde yük dengeleme](tutorial-load-balancer.md)
