---
title: Öğretici-Azure CLı ile bir ölçek kümesine uygulama yüklemesi
description: Azure CLI kullanarak Özel Betik Uzantısı ile sanal makine ölçek kümelerine uygulama yükleme işleminin nasıl yapılacağını öğrenin
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: cf383d41df5d41ea52065f3651b2068861631116
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934623"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli"></a>Öğretici: Azure CLI ile sanal makine ölçek kümelerine uygulama yükleme
Bir ölçek kümesindeki sanal makine (VM) örneklerinde uygulamaları çalıştırmak için önce uygulama bileşenlerini ve gerekli dosyaları yüklemeniz gerekir. Önceki bir öğreticide, sanal makine örneklerinizi dağıtmak için nasıl özel sanal makine görüntüsü oluşturulacağını ve kullanılacağını öğrendiniz. Bu özel görüntüde, el ile uygulama yüklemeleri ve yapılandırmaları yer alıyordu. Her sanal makine örneği dağıtıldıktan sonra bir ölçek kümesine uygulamaların yüklenmesini otomatikleştirebilir veya önceden ölçek kümesinde çalıştırılan bir uygulamayı güncelleştirebilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Ölçek kümenize otomatik olarak uygulama yükleme
> * Azure Özel Betik Uzantısı’nı kullanma
> * Ölçek kümesinde çalıştırılan bir uygulamayı güncelleştirme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.29 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 


## <a name="what-is-the-azure-custom-script-extension"></a>Azure Özel Betik Uzantısı nedir?
Özel Betik Uzantısı, Azure VM’lerinde betik indirir ve yürütür. Bu uzantı dağıtım sonrası yapılandırma, yazılım yükleme veya diğer yapılandırma/yönetim görevleri için kullanışlıdır. Betikler Azure depolama veya GitHub konumlarından indirilebilir ya da Azure portalına uzantı çalışma zamanında iletilebilir.

Özel Betik uzantısı, Azure Resource Manager şablonları ile tümleştirilir ve Azure CLI, Azure PowerShell, Azure portalı veya REST API’si ile de kullanılabilir. Daha fazla bilgi için bkz. [Özel Betik Uzantısı'na genel bakış](../virtual-machines/extensions/custom-script-linux.md).

Azure CLI ile Özel Betik Uzantısı’nı kullanmak için, alınacak dosyaları ve yürütülecek komutları tanımlayan bir JSON dosyası oluşturursunuz. Bu JSON tanımları, tutarlı uygulama yüklemeleri uygulamak için ölçek kümesi dağıtımları arasında yeniden kullanılabilir.


## <a name="create-custom-script-extension-definition"></a>Özel Betik Uzantısı tanımı oluşturma
Özel Betik Uzantısı’nı çalışır halde görmek için, NGINX web sunucusunu yükleyen ve ölçek kümesi sanal makine örneğinin ana bilgisayar adını veren bir ölçek kümesi oluşturalım. Aşağıdaki Özel Betik Uzantısı tanımı, GitHub’dan bir örnek betiği indirir, gerekli paketleri yükler, sonra sanal makine örneği ana bilgisayar adını bir temel HTML sayfasına yazar.

Geçerli kabuğunuzda *customConfig.json* adlı bir dosya oluşturup aşağıdaki yapılandırmayı yapıştırın. Örneğin, dosyayı yerel makinenizde değil Cloud Shell’de oluşturun. İstediğiniz düzenleyiciyi kullanabilirsiniz. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için Cloud Shell’e `sensible-editor customConfig.json` adını girin.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

> [!CAUTION]
> JSON bloğunda tek (') ve çift tırnak (") kullanımını, aşağıdaki *--Settings* parametresinde bulunan *customConfig.js* dosyasına başvurmak yerine JSON bloğu içinde ters çevirip geri almanız gerekebilir. 


## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma
[az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Bu adımda [az vmss create](/cli/azure/vmss) ile bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, *myScaleSet* adlı bir ölçek kümesi oluşturur ve yoksa SSH anahtarlarını oluşturur:

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


## <a name="apply-the-custom-script-extension"></a>Özel Betik Uzantısı’nı uygulama
[az vmss extension set](/cli/azure/vmss/extension) komutunu kullanarak ölçek kümenizdeki sanal makine örneklerine Özel Betik Uzantısı yapılandırmasını uygulayın. Aşağıdaki örnek, *myResourceGroup* adlı kaynak grubundaki *myScaleSet* sanal makine örneklerine *customConfig.json* yapılandırmasını uygular:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Ölçek kümesindeki her sanal makine örneği, GitHub’dan betiği indirip çalıştırır. Daha karmaşık bir örnekte, birden fazla uygulama bileşeni ve dosya yüklenebilir. Ölçek kümesinin ölçeği genişletilirse yeni sanal makine örnekleri otomatik olarak aynı Özel Betik Uzantısı tanımını uygular ve gerekli uygulamayı yükler.


## <a name="test-your-scale-set"></a>Ölçek kümenizi test etme
Web trafiğinin web sunucunuza ulaşmasına izin vermek için [az network lb rule create](/cli/azure/network/lb/rule) ile bir yük dengeleyici kuralı oluşturun. Aşağıdaki örnek *myLoadBalancerRuleWeb* adlı bir kural oluşturur:

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

Web sunucunuzu çalışır halde görmek için [az network public-ip show](/cli/azure/network/public-ip) ile yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek ölçek kümesinin bir parçası olarak oluşturulan *myScaleSetLBPublicIP* için IP adresini alır:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Yük dengeleyicinin genel IP adresini bir web tarayıcısına girin. Aşağıdaki örnekte gösterildiği gibi yük dengeleyici trafiği VM örneklerinizden birine dağıtır:

![Nginx’teki temel web sayfası](media/tutorial-install-apps-cli/running-nginx.png)

Sonraki adımda güncelleştirilmiş bir sürümü görebilmeniz için web tarayıcısını açık bırakın.


## <a name="update-app-deployment"></a>Uygulama dağıtımını güncelleştirme
Bir ölçek kümesinin yaşam döngüsü boyunca, uygulamanızın güncelleştirilmiş bir sürümünü dağıtmanız gerekebilir. Özel Betik Uzantısı ile, güncelleştirilmiş bir dağıtım betiğine başvurabilir ve sonra uzantıyı ölçek kümenize yeniden uygulayabilirsiniz. Önceki bir adımda ölçek kümesi oluşturulduğunda `--upgrade-policy-mode`, *automatic* olarak ayarlandı. Bu ayar, ölçek kümesindeki sanal makine örneklerinin otomatik olarak güncelleştirilmesini ve uygulamanızın en son sürümünü uygulamasını sağlar.

Geçerli kabuğunuzda *customConfigv2.json* adlı bir dosya oluşturup aşağıdaki yapılandırmayı yapıştırın. Bu tanım, uygulama yükleme betiğinin güncelleştirilmiş bir *v2* sürümünü çalıştırır:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

[az vmss extension set](/cli/azure/vmss/extension) komutunu kullanarak ölçek kümenizdeki sanal makine örneklerine Özel Betik Uzantısı yapılandırmasını tekrar uygulayın. Uygulamanın güncelleştirilmiş sürümünü uygulamak için *customConfigv2.json* kullanılır:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Ölçek kümesindeki tüm sanal makine örnekleri otomatik olarak örnek web sayfasının en son sürümüyle güncelleştirilir. Güncelleştirilmiş sürümü görmek için tarayıcınızda web sitesini yenileyin:

![Nginx’teki güncelleştirilmiş web sayfası](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme
Ölçek kümenizi ve ek kaynaklarınızı kaldırmak için [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu ve bu kaynak grubunun tüm kaynaklarını silin. `--no-wait` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür. `--yes` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure CLI ile ölçek kümenizde otomatik olarak uygulama yükleme ve güncelleştirme işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Ölçek kümenize otomatik olarak uygulama yükleme
> * Azure Özel Betik Uzantısı’nı kullanma
> * Ölçek kümesinde çalıştırılan bir uygulamayı güncelleştirme

Ölçek kümenizi otomatik olarak ölçeklendirme hakkında bilgi almak için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Ölçek kümelerinizi otomatik olarak ölçeklendirme](tutorial-autoscale-cli.md)
