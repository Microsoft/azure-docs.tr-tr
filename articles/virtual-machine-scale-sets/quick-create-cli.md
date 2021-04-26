---
title: Hızlı başlangıç-Azure CLı ile sanal makine ölçek kümesi oluşturma
description: Azure CLı ile hızlı bir şekilde sanal makine ölçek kümesi oluşturmayı öğrenerek dağıtımlarınızla çalışmaya başlayın.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d65df03bc23704fb4d4c037655cc4b84ccc4e576
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934657"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Hızlı Başlangıç: Azure CLI ile sanal makine ölçek kümesi oluşturma
Bir sanal makine ölçek kümesi, otomatik ölçeklendirme sanal makineleri kümesini dağıtmanıza ve yönetmenize olanak tanır. Ölçek kümesi içindeki sanal makine sayısını el ile ölçeklendirebilir veya CPU, bellek talebi ya da ağ trafiği gibi kaynak kullanımını temel alan otomatik ölçeklendirme kuralları tanımlayabilirsiniz. Azure Load Balancer daha sonra ölçek kümesindeki sanal makine örneklerine trafiği dağıtır. Bu hızlı başlangıçta, Azure CLI ile bir sanal makine ölçek kümesi oluşturur ve örnek uygulama dağıtırsınız.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.29 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 


## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma
Ölçek kümesi oluşturabilmek için [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Bu adımda [az vmss create](/cli/azure/vmss) ile bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, değişiklikler uygulandıkça otomatik güncelleştirilecek şekilde ayarlanan *myScaleSet* adlı bir ölçek kümesi oluşturur ve *~/.ssh/id_rsa* içinde yoksa SSH anahtarları oluşturur. Sanal makine örneklerinde oturum açmanız gerekirse bu SSH anahtarları kullanılır. Mevcut bir SSH anahtarı kümesini kullanmak için, `--ssh-key-value` parametresini kullanın ve anahtarlarınızın konumunu belirtin.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.


## <a name="deploy-sample-application"></a>Örnek uygulama dağıtma
Ölçek kümenizi test etmek için temel web uygulaması yükleyin. Sanal makine örneklerine uygulama yükleyen bir betik indirip çalıştırmak için Azure Özel Betik Uzantısı kullanılır. Bu uzantı dağıtım sonrası yapılandırma, yazılım yükleme veya diğer yapılandırma/yönetim görevleri için kullanışlıdır. Daha fazla bilgi için bkz. [Özel Betik Uzantısı'na genel bakış](../virtual-machines/extensions/custom-script-linux.md).

Temel bir NGINX web sunucusu yüklemek için Özel Betik Uzantısı kullanın. [az vmss extension set](/cli/azure/vmss/extension) ile NGINX yükleyen Özel Betik Uzantısı'nı aşağıdaki şekilde uygulayın:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Uygulamaya giden trafiğe izin verme
Ölçek kümesi oluşturulduğunda otomatik olarak bir Azure yük dengeleyici dağıtılmıştır. Yük dengeleyici, ölçek kümesindeki sanal makine örneklerine trafiği dağıtır. Trafiğin örnek web uygulamasına ulaşmasına izin vermek için [az network lb rule create](/cli/azure/network/lb/rule) ile bir yük dengeleyici kuralı oluşturun. Aşağıdaki örnek *myLoadBalancerRuleWeb* adlı bir kural oluşturur:

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


## <a name="test-your-scale-set"></a>Ölçek kümenizi test etme
Ölçek kümenizi çalışır halde görmek için bir web tarayıcısında örnek web uygulamasına erişin. [az network public-ip show](/cli/azure/network/public-ip) komutuyla yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek ölçek kümesinin bir parçası olarak oluşturulan *myScaleSetLBPublicIP* için IP adresini alır:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Yük dengeleyicinin genel IP adresini bir web tarayıcısına girin. Aşağıdaki örnekte gösterildiği gibi yük dengeleyici trafiği VM örneklerinizden birine dağıtır:

![NGINX varsayılan web sayfası](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli değilse, aşağıdaki gibi [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu, ölçek kümesini tüm ilgili kaynakları kaldırabilirsiniz. `--no-wait` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür. `--yes` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta basit bir ölçek kümesi oluşturdunuz ve Özel Betik Uzantısı'nı kullanarak VM örneklerine temel bir NGINX web sunucusu yüklediniz. Daha fazla bilgi edinmek için Azure sanal makine ölçek kümelerinin nasıl oluşturulacağı ve yönetileceğine ilişkin öğreticiyle devam edin.

> [!div class="nextstepaction"]
> [Azure sanal makine ölçek kümeleri oluşturma ve yönetme](tutorial-create-and-manage-cli.md)
