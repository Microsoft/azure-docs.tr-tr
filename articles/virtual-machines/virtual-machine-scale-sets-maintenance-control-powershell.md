---
title: PowerShell kullanarak Azure sanal makine ölçek kümelerinde işletim sistemi görüntüsü yükseltmeleri için bakım denetimi
description: Otomatik işletim sistemi görüntüsü yükseltmelerinden, bakım denetimi ve PowerShell kullanarak Azure sanal makine ölçek kümeleriniz için ne zaman kullanıma alınacağını nasıl denetleyeceğinizi öğrenin.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91974847"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>Önizleme: PowerShell kullanarak Azure sanal makine ölçek kümelerinde işletim sistemi görüntüsü yükseltmeleri için bakım denetimi

Bakım denetimi, sanal makine ölçek kümeleriniz için otomatik Konuk işletim sistemi görüntüsü yükseltmelerini ne zaman uygulayacağınıza karar vermenize olanak tanır. Bu konu, bakım denetimi için Azure PowerShell seçeneklerini içerir. Bakım denetimini kullanma hakkında daha fazla bilgi için bkz. [Azure sanal makine ölçek kümeleri Için bakım denetimi](virtual-machine-scale-sets-maintenance-control.md).

> [!IMPORTANT]
> Azure sanal makine ölçek kümelerinde işletim sistemi görüntüsü yükseltmeleri için bakım denetimi şu anda genel önizlemededir.
> Bu önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="enable-the-powershell-module"></a>PowerShell modülünü etkinleştir

Güncel olduğundan emin olun `PowerShellGet` .    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

`Az.Maintenance`PowerShell modülünü yükler.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Yerel olarak yüklüyorsanız, PowerShell komut dosyanızı yönetici olarak açtığınızdan emin olun.

Ayrıca, *Güvenilmeyen bir depodan* yüklemek istediğinizi onaylamanız istenebilir. `Y`Modülünü yüklemek için yazın veya **Evet** ' i seçin.

## <a name="connect-to-an-azure-account"></a>Azure hesabına bağlanma

[Connect-azaccount](/powershell/module/az.accounts/connect-azaccount) ve [set-Azaccount](/powershell/module/az.accounts/set-azcontext)kullanarak istediğiniz Azure hesabınıza bağlanın.

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Bakım yapılandırması oluşturun

Yapılandırmanız için kapsayıcı olarak bir kaynak grubu oluşturun. Bu örnekte, *eastus2* içinde *myMaintenanceRG* adlı bir kaynak grubu oluşturulur. Kullanmak istediğiniz bir kaynak grubunuz zaten varsa, bu bölümü atlayabilirsiniz. Örneklerin geri kalanında kaynak grubu adını kendi adınızla değiştirmeniz yeterlidir.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

Bir bakım yapılandırması oluşturmak için [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) kullanın. Bu örnek, işletim sistemi görüntüsüne *MyConfig* kapsamı adlı bir bakım yapılandırması oluşturur. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> Bakım **süresi** *5 saat* veya daha uzun olmalıdır. Bakım **tekrarlamaları** *gün* olarak ayarlanmalıdır.

Kullanılarak `-MaintenanceScope OSImage` , Konuk işletim sistemindeki güncelleştirmelerin denetlenmesi için bakım yapılandırmasının kullanılması sağlanır.

Aynı ada sahip bir yapılandırma oluşturmaya çalışırsanız, ancak farklı bir konumda hata alırsınız. Yapılandırma adları, kaynak grubunuz için benzersiz olmalıdır.

[Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)kullanarak kullanılabilir bakım yapılandırması için sorgulama yapabilirsiniz.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>Sanal makine ölçek kümesini bakım yapılandırmasıyla ilişkilendirin

Bakım yapılandırmasının bölgesinden ve aboneliğinden bağımsız olarak, bir sanal makine ölçek kümesi herhangi bir bakım yapılandırmasıyla ilişkilendirilebilir. Bakım yapılandırmasında seçim yaparak, ölçek kümesi için yeni işletim sistemi görüntüsü güncelleştirmeleri, bir sonraki kullanılabilir bakım penceresinde otomatik olarak zamanlanır.

[Yeni-Azconfigurationatama](/powershell/module/az.maintenance/new-azconfigurationassignment) kullanarak sanal makine ölçek kümesini, bakım yapılandırmasını ilişkilendirebilirsiniz.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Otomatik işletim sistemi yükseltmesini etkinleştir

Bakım denetimini kullanacak olan her bir sanal makine ölçek kümesi için otomatik işletim sistemi yükseltmelerini etkinleştirebilirsiniz. Sanal makine ölçek kümesinde otomatik işletim sistemi yükseltmelerini etkinleştirmek için [bkz. Azure sanal makine ölçek kümesi otomatik işletim sistemi görüntüsü yükseltmeleri](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) . 


## <a name="next-steps"></a>Sonraki adımlar

Azure 'da çalışan sanal makineler için bakım ve güncelleştirme hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Bakım ve güncelleştirmeler](maintenance-and-updates.md)