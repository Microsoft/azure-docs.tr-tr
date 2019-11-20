---
title: Azure sanal makine ölçek kümesine uygulama dağıtma | Microsoft Docs
description: Bir ölçek kümesindeki Linux ve Windows sanal makine örneklerine uygulama dağıtmayı öğrenin
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 0dc1c52e65090acd5f63d1b23d8da6f37e3cf567
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960737"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Uygulamanızı sanal makine ölçek kümelerine dağıtma

Bir ölçek kümesindeki sanal makine (VM) örneklerinde uygulamaları çalıştırmak için önce uygulama bileşenlerini ve gerekli dosyaları yüklemeniz gerekir. Bu makalede, bir ölçek kümesindeki örnekler için özel bir VM görüntüsü oluşturmanın veya var olan sanal makine örneklerine otomatik olarak yüklenen betikleri çalıştırmanın yolları açıklanır. Ayrıca, bir ölçek kümesi genelinde uygulama veya işletim sistemi güncelleştirmelerini yönetmeyi öğreneceksiniz.


## <a name="build-a-custom-vm-image"></a>Özel bir VM görüntüsü oluşturma
Ölçek kümesindeki örnekleri oluşturmak için Azure platform görüntülerinden birini kullandığınızda, ek yazılım yüklenmez veya yapılandırılmaz. Bu bileşenlerin yüklemesini otomatik hale getirebilirsiniz, ancak ölçek kümelerinizi VM örnekleri sağlamak için gereken süreye ekler. VM örneklerine birçok yapılandırma değişikliği uygularsanız, bu yapılandırma betiklerine ve görevlere sahip yönetim ek yükü vardır.

Yapılandırma yönetimini ve VM sağlama süresini azaltmak için, ölçek kümesinde bir örnek sağlandığı anda uygulamanızı çalıştırmaya hazır olan özel bir VM görüntüsü oluşturabilirsiniz. Ölçek kümesiyle özel bir VM görüntüsü oluşturma ve kullanma hakkında daha fazla bilgi için aşağıdaki öğreticilere bakın:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Özel Betik uzantısı ile uygulama yükler
Özel Betik Uzantısı, Azure VM’lerinde betik indirir ve yürütür. Bu uzantı dağıtım sonrası yapılandırma, yazılım yükleme veya diğer yapılandırma/yönetim görevleri için kullanışlıdır. Betikler Azure depolama veya GitHub konumlarından indirilebilir ya da Azure portalına uzantı çalışma zamanında iletilebilir. Özel bir betik uzantısıyla bir uygulamanın nasıl yükleneceğine ilişkin daha fazla bilgi için aşağıdaki öğreticilere bakın:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager şablonu](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>PowerShell DSC ile Windows VM 'ye uygulama yüklemek
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) hedef makineler tanımlamak için bir yönetim platformu. DSC yapılandırmaları ne bir makineye yükleyin ve ana bilgisayar yapılandırma tanımlar. İstenen Eylemler gönderilen yapılandırmalarına göre işler her hedef düğümde yerel Configuration Manager'ı (LCM) altyapısı çalıştırır.

PowerShell DSC Uzantısı, PowerShell ile bir ölçek kümesindeki sanal makine örneklerini özelleştirmenize olanak sağlar. Aşağıdaki örnek:

- Sanal makine örneklerine GitHub 'dan bir DSC paketi indirmesini söyler *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Uzantıyı bir Install betiği çalıştıracak şekilde ayarlar-`configure-http.ps1`
- [Get-AzVmss](/powershell/module/az.compute/get-azvmss) ile bir ölçek kümesi hakkında bilgi alır
- Uzantıyı [Update-AzVmss](/powershell/module/az.compute/update-azvmss) ile sanal makine örneklerine uygular

DSC Uzantısı, *Myresourcegroup*adlı kaynak grubundaki *myScaleSet* sanal makine örneklerine uygulanır. Kendi adlarınızı aşağıdaki gibi girin:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Ölçek kümesindeki yükseltme ilkesi *manuel*Ise, [Update-Azvmssınstance](/powershell/module/az.compute/update-azvmssinstance)ile sanal makine örneklerinizi güncelleştirin. Bu cmdlet, güncelleştirilmiş ölçek kümesi yapılandırmasını sanal makine örneklerine uygular ve uygulamanızı kurar.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Cloud-init ile Linux VM 'ye uygulama yüklemek
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html), Linux VM’sini ilk kez önyüklendiğinde özelleştirmeyi sağlayan, sık kullanılan bir yaklaşımdır. cloud-init’i paket yükleme, dosyalara yazma ve kullanıcılar ile güvenliği yapılandırma işlemleri için kullanabilirsiniz. cloud-init önyükleme işlemi sırasında çalışırken, yapılandırmanıza uygulayabileceğiniz ek adım veya gerekli aracı yoktur.

Cloud-init, dağıtımlar arasında da çalışır. Örneğin, bir paket yüklemek için **apt-get install** veya **yum install** kullanmazsınız. Bunun yerine, yüklenecek paketlerin listesini tanımlayabilirsiniz. Cloud-init, seçtiğiniz dağıtım için yerel paket yönetim aracını otomatik olarak kullanır.

Örnek *kabuğunuzda Cloud-init. txt* dosyası dahil olmak üzere daha fazla bilgi için bkz. [Cloud-Init kullanarak Azure VM 'leri özelleştirme](../virtual-machines/linux/using-cloud-init.md).

Bir ölçek kümesi oluşturmak ve bir Cloud-init dosyası kullanmak için, [az VMSS Create](/cli/azure/vmss) komutuna `--custom-data` parametresini ekleyin ve bir Cloud-init dosyasının adını belirtin. Aşağıdaki örnek, *Myresourcegroup* içinde *myScaleSet* adlı bir ölçek kümesi oluşturur ve sanal makine örneklerini *kabuğunuzda Cloud-init. txt*adlı bir dosyayla yapılandırır. Kendi adlarınızı aşağıdaki gibi girin:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Uygulamaları işletim sistemi güncelleştirmeleriyle birlikte yükler
Yeni işletim sistemi sürümleri kullanılabilir olduğunda, yeni bir özel görüntü oluşturabilir veya bir ölçek kümesine [işletim sistemi yükseltmeleri dağıtabilirsiniz](virtual-machine-scale-sets-upgrade-scale-set.md) . Her VM örneği, belirttiğiniz en son görüntüye yükseltilir. Uygulamanızı yükseltmeyi gerçekleştirirken otomatik olarak kullanılabilir olması için önceden yüklenmiş uygulama, Özel Betik uzantısı veya PowerShell DSC ile özel bir görüntü kullanabilirsiniz. Sürüm uyumluluk sorunları olmadığından emin olmak için bu işlemi gerçekleştirirken uygulama bakımı için plan yapmanız gerekebilir.

Uygulama önceden yüklenmiş olarak özel bir VM görüntüsü kullanıyorsanız, yeni görüntüleri oluşturmak ve ölçek kümesi üzerinde işletim sistemi yükseltmeleri dağıtmak için uygulama güncelleştirmelerini bir dağıtım işlem hattı ile tümleştirebilirsiniz. Bu yaklaşım, işlem hattının en son uygulama yapılarını almasına, bir VM görüntüsü oluşturmasına ve doğrulamasına ve sonra ölçek kümesindeki sanal makine örneklerini yükseltmesine olanak tanır. Özel VM görüntüleri genelinde uygulama güncelleştirmelerini oluşturup dağıtan bir dağıtım işlem hattı çalıştırmak için, [bir Packer görüntüsü oluşturabilir ve Azure DevOps Services ile dağıtabilir](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)ya da [Spinnaker](https://www.spinnaker.io/) veya [Jenkins](https://jenkins.io/)gibi başka bir platformu kullanabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
Ölçek kümelerinizi uygulamalar oluşturup dağıtırken, [Ölçek kümesi tasarımına genel bakış](virtual-machine-scale-sets-design-overview.md)' ı gözden geçirebilirsiniz. Ölçek kümesini yönetme hakkında daha fazla bilgi için bkz. [Ölçek kümesini yönetmek Için PowerShell 'ı kullanma](virtual-machine-scale-sets-windows-manage.md).
