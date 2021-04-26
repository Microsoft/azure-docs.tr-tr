---
title: Öğretici-Azure şablonları ile bir ölçek kümesine uygulama kurma
description: Azure Resource Manager şablonlarını kullanarak Özel Betik Uzantısı ile sanal makine ölçek kümelerine uygulama yükleme işleminin nasıl yapılacağını öğrenin
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 1ce7795f1cb823be7940f065c9dba4ecc51926c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934608"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Öğretici: Azure şablonu ile sanal makine ölçek kümelerine uygulama yükleme
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

Özel Betik Uzantısı’nı çalışır halde görmek için, NGINX web sunucusunu yükleyen ve ölçek kümesi sanal makine örneğinin ana bilgisayar adını veren bir ölçek kümesi oluşturun. Aşağıdaki Özel Betik Uzantısı tanımı, GitHub’dan bir örnek betiği indirir, gerekli paketleri yükler, sonra sanal makine örneği ana bilgisayar adını bir temel HTML sayfasına yazar.


## <a name="create-custom-script-extension-definition"></a>Özel Betik Uzantısı tanımı oluşturma
Azure şablonu ile bir sanal makine ölçek kümesi tanımladığınızda *Microsoft.Compute/virtualMachineScaleSets* kaynak sağlayıcısı, uzantılara bir bölüm ekleyebilir. *extensionsProfile*, bir ölçek kümesindeki sanal makine örneklerine nelerin uygulanacağını açıklar. Özel Betik Uzantısı’nı kullanmak için *Microsoft.Azure.Extensions* yayımcısını ve *CustomScript* türünü belirtirsiniz.

Kaynak yükleme betiklerini veya paketlerini tanımlamak için *fileUris* özelliği kullanılır. Yükleme işlemini başlatmak için gereken betikler, *commandToExecute* komutunda tanımlanır. Aşağıdaki örnek, NGINX web sunucusunu yükleyip yapılandıran GitHub’daki bir örnek betiği tanımlar:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Bir ölçek kümesi ve özel Betik uzantısı dağıtan bir Azure şablonuna ilişkin tüm bir örnek için bkz [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json) .. Bu örnek şablon, sonraki bölümde kullanılmaktadır.


## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma
Şimdi bir ölçek kümesi oluşturmak ve Özel Betik Uzantısı’nı uygulamak için örnek şablonu kullanalım. Öncelikle [az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Şimdi [az Deployment Group Create](/cli/azure/deployment/group)komutuyla bir sanal makine ölçek kümesi oluşturun. İstendiğinde, her bir sanal makine örneği için kimlik bilgileri olarak kullanılan kendi kullanıcı adınızı ve parolanızı sağlayın:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.

Ölçek kümesindeki her sanal makine örneği, GitHub’dan betiği indirip çalıştırır. Daha karmaşık bir örnekte, birden fazla uygulama bileşeni ve dosya yüklenebilir. Ölçek kümesinin ölçeği genişletilirse yeni sanal makine örnekleri otomatik olarak aynı Özel Betik Uzantısı tanımını uygular ve gerekli uygulamayı yükler.


## <a name="test-your-scale-set"></a>Ölçek kümenizi test etme
Web sunucunuzu çalışır halde görmek için [az network public-ip show](/cli/azure/network/public-ip) ile yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek, ölçek kümesinin bir parçası olarak oluşturulan *myScaleSetPublicIP* için IP adresini alır:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Yük dengeleyicinin genel IP adresini bir web tarayıcısına girin. Aşağıdaki örnekte gösterildiği gibi yük dengeleyici trafiği VM örneklerinizden birine dağıtır:

![Nginx’teki temel web sayfası](media/tutorial-install-apps-template/running-nginx.png)

Sonraki adımda güncelleştirilmiş bir sürümü görebilmeniz için web tarayıcısını açık bırakın.


## <a name="update-app-deployment"></a>Uygulama dağıtımını güncelleştirme
Bir ölçek kümesinin yaşam döngüsü boyunca, uygulamanızın güncelleştirilmiş bir sürümünü dağıtmanız gerekebilir. Özel Betik Uzantısı ile, güncelleştirilmiş bir dağıtım betiğine başvurabilir ve sonra uzantıyı ölçek kümenize yeniden uygulayabilirsiniz. Önceki adımda ölçek kümesi oluşturulduğunda, *Upgradepolicy* *Otomatik* olarak ayarlanmıştır. Bu ayar, ölçek kümesindeki sanal makine örneklerinin otomatik olarak güncelleştirilmesini ve uygulamanızın en son sürümünü uygulamasını sağlar.

Özel Betik Uzantısı tanımını güncelleştirmek için şablonunuzu yeni bir yükleme betiğine başvuracak şekilde düzenleyin. Özel Betik Uzantısı’nın değişikliği tanıması için yeni bir dosya adı kullanılmalıdır. Özel Betik Uzantısı, değişiklikleri belirlemek için betiğin içeriklerini incelemez. Aşağıdaki tanım, güncelleştirilmiş yükleme betiğini, adının sonuna *_v2* eklenmiş şekilde kullanır:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Özel Betik uzantısı yapılandırmasını, daha [az dağıtım grubu oluştur](/cli/azure/deployment/group)seçeneğiyle yeniden ölçek kümesindeki sanal makine örneklerine uygulayın. Uygulamanın güncelleştirilmiş sürümünü uygulamak için bu *azuredeployv2.json* şablonu kullanılır. Uygulamada, önceki bölümde gösterildiği gibi güncelleştirilmiş yükleme betiğine başvurmak için mevcut *azuredeploy.json* şablonunu düzenleyin. İstendiğinde, ölçek kümesini ilk oluşturduğunuzda kullanıldığı haliyle aynı kullanıcı adı ve parola bilgilerini girin:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Ölçek kümesindeki tüm sanal makine örnekleri otomatik olarak örnek web sayfasının en son sürümüyle güncelleştirilir. Güncelleştirilmiş sürümü görmek için tarayıcınızda web sitesini yenileyin:

![Nginx’teki güncelleştirilmiş web sayfası](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme
Ölçek kümenizi ve ek kaynaklarınızı kaldırmak için [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu ve bu kaynak grubunun tüm kaynaklarını silin. `--no-wait` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür. `--yes` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure şablonları ile ölçek kümenizde otomatik olarak uygulama yükleme ve güncelleştirme işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Ölçek kümenize otomatik olarak uygulama yükleme
> * Azure Özel Betik Uzantısı’nı kullanma
> * Ölçek kümesinde çalıştırılan bir uygulamayı güncelleştirme

Ölçek kümenizi otomatik olarak ölçeklendirme hakkında bilgi almak için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Ölçek kümelerinizi otomatik olarak ölçeklendirme](tutorial-autoscale-template.md)
