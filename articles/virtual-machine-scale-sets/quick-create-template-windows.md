---
title: Hızlı başlangıç-Azure şablonuyla Windows sanal makine ölçek kümesi oluşturma
description: Örnek uygulama dağıtan ve otomatik ölçeklendirme kurallarını yöneten bir Azure Resource Manager şablonuyla hızlıca bir Windows sanal makine ölçek kümesi oluşturmayı öğrenin
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs
ms.openlocfilehash: d968225ea9d6ed966091c8c8dabcca10d454d88c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935475"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-arm-template"></a>Hızlı başlangıç: ARM şablonuyla Windows sanal makine ölçek kümesi oluşturma

Bir sanal makine ölçek kümesi, otomatik ölçeklendirme sanal makineleri kümesini dağıtmanıza ve yönetmenize olanak tanır. Ölçek kümesi içindeki sanal makine sayısını el ile ölçeklendirebilir veya CPU, bellek talebi ya da ağ trafiği gibi kaynak kullanımını temel alan otomatik ölçeklendirme kuralları tanımlayabilirsiniz. Azure Load Balancer daha sonra ölçek kümesindeki sanal makine örneklerine trafiği dağıtır. Bu hızlı başlangıçta, bir sanal makine ölçek kümesi oluşturur ve bir Azure Resource Manager şablonuyla örnek uygulamayı (ARM şablonu) dağıtırsınız.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

ARM şablonları, ilgili kaynakların gruplarını dağıtmanızı sağlar. Tek bir şablonda sanal makine ölçek kümesi oluşturabilir, uygulamaları yükleyebilir ve otomatik ölçeklendirme kurallarını yapılandırabilirsiniz. Değişkenleri ve parametreleri kullanarak bu şablonu var olan ölçek kümelerini güncelleştirme veya yenilerini oluşturma amacıyla tekrar kullanabilirsiniz. Azure portal, Azure CLı, Azure PowerShell veya sürekli tümleştirme/sürekli teslim (CI/CD) işlem hatları aracılığıyla şablon dağıtabilirsiniz.

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/) alınmıştır.

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json":::

Bu kaynaklar şu şablonlarda tanımlanmıştır:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/Publicıpaddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft. Insights/oto Scalesettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>Ölçek kümesi tanımlama

Şablonla ölçek kümesi oluşturmak için gerekli kaynakları tanımlamanız gerekir. Sanal makine ölçek kümesi kaynak türünün ana bölümleri şunlardır:

| Özellik                     | Özellik açıklaması                                  | Örnek şablon değeri                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| tür                         | Oluşturulacak Azure kaynağı türü                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Ölçek kümesi adı                                       | myScaleSet                                |
| location                     | Ölçek kümesinin oluşturulacağı konum                     | Doğu ABD                                   |
| sku.name                     | Her bir ölçek kümesi örneği için VM boyutu                  | Standard_A1                               |
| sku.capacity                 | Başlangıçta oluşturulacak VM örneği sayısı           | 2                                         |
| upgradePolicy.mode           | Değişiklik yapıldığında kullanılacak VM örneği yükseltme modu              | Automatic                                 |
| imageReference               | VM örnekleri için kullanılacak platform veya özel görüntü | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Her bir VM örneği için ad ön eki                     | myvmss                                    |
| osProfile.adminUsername      | Her bir VM örneği için kullanıcı adı                        | azureuser                                 |
| osProfile.adminPassword      | Her bir VM örneği için parola                        | P@ssw0rd!                                 |

Ölçek kümesi şablonunu özelleştirmek için VM boyutunu veya başlangıç kapasitesini değiştirebilirsiniz. Başka bir seçenek de farklı bir platform veya özel görüntü kullanmaktır.

### <a name="add-a-sample-application"></a>Örnek uygulama ekleme

Ölçek kümenizi test etmek için temel web uygulaması yükleyin. Bir ölçek kümesini dağıttığınızda VM uzantıları uygulama yükleme gibi dağıtım sonrası yapılandırma ve otomasyon görevlerini gerçekleştirebilir. Betikler Azure depolama veya GitHub konumlarından indirilebilir ya da Azure portalına uzantı çalışma zamanında iletilebilir. Ölçek kümenize uzantı uygulamak için önceki kaynak örneğine *extensionProfile* bölümünü eklemeniz gerekir. Uzantı profili temelde aşağıdaki özellikleri tanımlar:

- Uzantı türü
- Uzantı yayımcısı
- Uzantı sürümü
- Yapılandırma veya yükleme betiklerinin konumu
- VM örneklerinde yürütülecek komutlar

Şablon, IIS 'de çalışan bir ASP.NET MVC uygulamasını yüklemek için PowerShell DSC uzantısını kullanır.

*url* ile tanımlandığı üzere GitHub'dan bir yükleme betiği indirilir. Uzantı ardından *function* ve *Script* ile tanımlandığı üzere *IISInstall.ps1* betiğinden *InstallIIS* komutunu çalıştırır. ASP.NET uygulaması da *WebDeployPackagePath* ile tanımlandığı üzere GitHub'dan indirilen bir Web Dağıtımı paketine sahiptir:

## <a name="deploy-the-template"></a>Şablonu dağıtma

Şablonu **Azure 'A dağıt** düğmesini seçerek dağıtabilirsiniz. Bu düğme Azure portalını açar, şablonun tamamını yükler ve ölçek kümesi adı, örnek sayısı ve yönetici kimlik bilgileri gibi birkaç parametreyi sorar.

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Ayrıca, Azure PowerShell kullanarak bir Kaynak Yöneticisi şablonu dağıtabilirsiniz:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

VM örnekleri için ölçek kümesi adı ve yönetici kimlik bilgileri istemlerini yanıtlayın. Ölçek kümesinin oluşturulması ve uygulamayı yapılandırmak için uzantının uygulanması 10-15 dakika sürebilir.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Ölçek kümenizi çalışır halde görmek için bir web tarayıcısında örnek web uygulamasına erişin. [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) ile yük dengeleyicinizin genel IP adresini aşağıdaki şekilde edinin:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

*Http: \/ /Publicıpaddress/MyApp* biçiminde bir Web tarayıcısına yük dengeleyicinin genel IP adresini girin. Aşağıdaki örnekte gösterildiği gibi yük dengeleyici trafiği VM örneklerinizden birine dağıtır:

![Çalışan IIS sitesi](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) öğesini kullanarak kaynak grubunu, ölçek kümesini kaldırabilirsiniz. `-Force` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar. `-AsJob` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta ARM şablonuyla bir Windows ölçek kümesi oluşturdunuz ve PowerShell DSC uzantısını kullanarak VM örneklerine temel bir ASP.NET uygulaması yüklersiniz. Daha fazla bilgi edinmek için Azure sanal makine ölçek kümelerinin nasıl oluşturulacağı ve yönetileceğine ilişkin öğreticiyle devam edin.

> [!div class="nextstepaction"]
> [Azure sanal makine ölçek kümeleri oluşturma ve yönetme](tutorial-create-and-manage-powershell.md)
