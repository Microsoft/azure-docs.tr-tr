---
title: Öğretici - Azure CLI ile bir ölçek kümesindeki özel sanal makine görüntüsünü kullanma | Microsoft Docs
description: Azure CLI kullanarak, sanal makine ölçek kümesini dağıtmak için kullanabileceğiniz bir özel sanal makine görüntüsünün nasıl oluşturulacağını öğrenin
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6368a36eaa2e6832f22a13f20bc35d66c4425b4b
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311569"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Öğretici: Azure CLI ile sanal makine ölçek kümeleri için özel görüntü oluşturma ve kullanma
Ölçek kümesi oluşturduğunuzda, sanal makine örnekleri dağıtılırken kullanılacak bir görüntü belirtirsiniz. Sanal makine örnekleri dağıtıldıktan sonraki görev sayısını azaltmak için özel bir sanal makine görüntüsünü kullanabilirsiniz. Bu özel sanal makine görüntüsü, gerekli uygulama yüklemelerini veya yapılandırmalarını içerir. Ölçek kümesinde oluşturulan tüm sanal makine örnekleri, özel sanal makine görüntüsünü kullanır ve uygulama trafiğinizi sunmaya hazır olur. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal makine oluşturma ve özelleştirme
> * Sanal makinenin sağlamasını kaldırma ve sanal makineyi genelleştirme
> * Özel bir sanal makine görüntüsü oluşturma
> * Özel sanal makine görüntüsünü kullanan bir ölçek kümesini dağıtma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.29 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Kaynak sanal makine oluşturma ve yapılandırma

>[!NOTE]
> Bu öğreticide, genelleştirilmiş bir sanal makine görüntüsü oluşturma ve kullanma işlemi gösterilmektedir. Özelleştirilmiş bir sanal makine görüntüsünden ölçek kümesi oluşturulması desteklenmez.

İlk olarak, [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun ve sonra [az vm create](/cli/azure/vm) ile bir sanal makine oluşturun. Bu sanal makine daha sonra özel bir sanal makine görüntüsü için kaynak olarak kullanılır. Aşağıdaki örnek, *myResourceGroup* adlı kaynak grubunda *myVM* adlı bir sanal makine oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

[az vm create](/cli/azure/vm) komutunun çıktısında, sanal makinenizin genel IP adresi gösterilir. Aşağıdaki adımları uygulayarak sanal makinenizin genel IP adresinde SSH oturumu açın:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Şimdi sanal makinenizi özelleştirmek için bir temel web sunucusu yükleyelim. Ölçek kümesindeki sanal makine örneği dağıtılacağı zaman, bir web uygulamasını çalıştırmak için gerekli tüm paketleri içerir. Aşağıdaki adımları uygulayarak `apt-get` komutunu kullanıp *NGINX*’i yükleyin:

```bash
sudo apt-get install -y nginx
```

Sanal makinenizi özel görüntü olarak kullanılmaya hazırlamanın son adımı, sanal makinenizin sağlamasının kaldırılmasıdır. Bu adım, makineye özgü bilgileri sanal makineden kaldırır ve tek bir görüntüden birçok sanal makine dağıtılmasını mümkün kılar. Sanal makinenin sağlaması kaldırıldığında ana bilgisayar adı sıfırlanarak *localhost.localdomain* olur. SSH ana bilgisayar anahtarları, ad sunucusu yapılandırmaları, kök parolası ve önbelleğe alınan DHCP kiraları da ayrıca silinir.

Sanal makinenin sağlamasını kaldırmak için Azure VM aracısını (*waagent*) kullanın. Azure VM aracısı her sanal makineye yüklenir ve Azure platformu ile iletişim kurmak için kullanılır. `-force` parametresi, aracıya makineye özgü bilgileri sıfırlamak için istemleri kabul etmesini bildirir.

```bash
sudo waagent -deprovision+user -force
```

Sanal makineyle olan SSH bağlantınızı kapatın:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Kaynak sanal makineden özel bir sanal makine görüntüsü oluşturma
Kaynak sanal makine şimdi Nginx web sunucusunun yüklenmesiyle birlikte özelleştirilir. Şimdi ölçek kümesi ile kullanılacak özel sanal makine görüntüsü oluşturalım.

Bir görüntü oluşturmak için VM’nin serbest bırakılması gerekir. [az vm deallocate](/cli//azure/vm) komutunu kullanarak sanal makineyi serbest bırakın. Daha sonra, Azure platformunun sanal makinenin özel bir görüntüyle kullanılmaya hazır olduğunu bilmesi için [az vm generalize](/cli//azure/vm) komutunu kullanarak sanal makinenin durumunu genelleştirilmiş olarak ayarlayın. Yalnızca genelleştirilmiş bir sanal makineden görüntü oluşturabilirsiniz:


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Sanal makinenin serbest bırakılıp genelleştirilmesi birkaç dakika sürebilir.

Şimdi [az image create](/cli//azure/image) komutunu kullanarak sanal makinenin bir görüntüsünü oluşturun. Aşağıdaki örnek, sanal makinenizden *myImage* adlı bir görüntü oluşturur:

> NOTUN Kaynak grubu ve sanal makine konumu farklıysa, görüntüyü oluşturmak için kullanılan kaynak VM 'nin konumunu specificeklemek için aşağıdaki komutlara `--location` parametresini ekleyebilirsiniz. 

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Özel bir sanal makine görüntüsünden ölçek kümesi oluşturma
[az vmss create](/cli/azure/vmss#az-vmss-create) komutunu kullanarak bir ölçek kümesi oluşturun. *UbuntuLTS* veya *CentOS* gibi bir platform görüntüsü yerine, özel sanal makine görüntünüzün adını belirtin. Aşağıdaki örnek, önceki adımda yer alan *myImage* adlı özel görüntüyü kullanan *myScaleSet* adlı bir ölçek kümesi oluşturur:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.


## <a name="test-your-scale-set"></a>Ölçek kümenizi test etme
Trafiğin ölçek kümenize ulaşmasına izin vermek ve web sunucusunun düzgün çalıştığını doğrulamak için [az network lb rule create](/cli/azure/network/lb/rule) komutunu kullanarak bir yük dengeleyici kuralı oluşturun. Aşağıdaki örnek, *80* numaralı *TCP* bağlantı noktasında trafiğe izin veren, *myLoadBalancerRuleWeb* adlı bir kural oluşturur:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Ölçek kümenizi çalışır halde görmek için [az network public-ip show](/cli/azure/network/public-ip) komutunu kullanarak yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek, ölçek kümesinin bir parçası olarak oluşturulan *myScaleSetLBPublicIP* için IP adresini alır:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Genel IP adresini bir web tarayıcınıza yazın. Aşağıdaki örnekte gösterildiği gibi varsayılan NGINX web sayfası görüntülenir:

![Özel sanal makine görüntüsünden çalıştırılan Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme
Ölçek kümenizi ve ek kaynaklarınızı kaldırmak için [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu ve bu kaynak grubunun tüm kaynaklarını silin. `--no-wait` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür. `--yes` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure CLI ile ölçek kümeleriniz için özel sanal makine görüntüsü oluşturma ve kullanma işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Sanal makine oluşturma ve özelleştirme
> * Sanal makinenin sağlamasını kaldırma ve sanal makineyi genelleştirme
> * Özel bir sanal makine görüntüsü oluşturma
> * Özel sanal makine görüntüsünü kullanan bir ölçek kümesini dağıtma

Uygulamaların ölçek kümenize nasıl dağıtılacağını öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Ölçek kümelerinize uygulama dağıtma](tutorial-install-apps-cli.md)
