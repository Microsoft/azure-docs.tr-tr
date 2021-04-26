---
title: PowerShell kullanarak Azure sanal makineleri için bakım denetimi
description: Bakım denetimini ve PowerShell 'i kullanarak Azure VM 'lerinize bakım uygulandığını nasıl denetleyeceğinizi öğrenin.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 2868d559f0d848095fa7fec174e09e1b9376c4ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552465"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Bakım denetimi ve Azure PowerShell güncelleştirmeleri denetleme

Bakım denetimi, yalıtılmış sanal makinelerinizin ve Azure adanmış konaklarınızın konak altyapısına platform güncelleştirmelerinin ne zaman uygulanacağını belirlemenize olanak sağlar. Bu konu, bakım denetimi için Azure PowerShell seçeneklerini içerir. Bakım denetimini, sınırlamalarını ve diğer yönetim seçeneklerini kullanmanın avantajları hakkında daha fazla bilgi için bkz. [Platform güncelleştirmelerini bakım denetimiyle yönetme](maintenance-control.md).
 
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


## <a name="create-a-maintenance-configuration"></a>Bakım yapılandırması oluşturun

Yapılandırmanız için kapsayıcı olarak bir kaynak grubu oluşturun. Bu örnekte, *eastus* içinde *myMaintenanceRG* adlı bir kaynak grubu oluşturulur. Kullanmak istediğiniz bir kaynak grubunuz zaten varsa, bu bölümü atlayabilir ve örneklerin geri kalanında kaynak grubu adını sahipsiz olarak değiştirebilirsiniz.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Bir bakım yapılandırması oluşturmak için [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) kullanın. Bu örnek, konakta *MyConfig* adlı bir bakım yapılandırması oluşturur. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Kullanımı, `-MaintenanceScope host` bakım yapılandırmasının konaktaki güncelleştirmeleri denetlemek için kullanılmasını sağlar.

Aynı ada sahip bir yapılandırma oluşturmaya çalışırsanız, ancak farklı bir konumda hata alırsınız. Yapılandırma adları, kaynak grubunuz için benzersiz olmalıdır.

[Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)kullanarak kullanılabilir bakım yapılandırması için sorgulama yapabilirsiniz.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Zamanlanan pencere ile bakım yapılandırması oluşturma

Ayrıca, Azure 'un kaynaklarınıza güncelleştirmeleri uygulayabilmesi için zamanlanmış bir pencere de bildirebilirsiniz. Bu örnek, her ayın dördüncü Pazartesi günü 5 saatlik zamanlanan bir pencere ile myConfig adlı bir bakım yapılandırması oluşturur. Zamanlanmış bir pencere oluşturduktan sonra güncelleştirmeleri el ile uygulamanız gerekmez.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> Bakım **süresi** *2 saat* veya daha uzun olmalıdır. Bakım **tekrarlamaları** , 35 gün içinde en az bir kez gerçekleşmelidir.

Bakım **yinelemesi** günlük, haftalık veya aylık olarak ifade edilebilir. Bazı örnekler şunlardır:
 - **günlük**-recurevery "gün" **veya** "3gün" 
 - **haftalık**-recureçok "3hafta" **veya** "hafta Cumartesi, Pazar" 
 - **aylık**-recurevery "month day23, day24" **veya** "ay son Pazar" **veya** "aylık dördüncü Pazartesi"  
      

## <a name="assign-the-configuration"></a>Yapılandırmayı ata

Yapılandırmayı yalıtılmış sanal makinenize veya Azure adanmış ana bilgisayarınıza atamak için [New-Azconfigurationatama](/powershell/module/az.maintenance/new-azconfigurationassignment) ' yı kullanın.

### <a name="isolated-vm"></a>Yalıtılmış VM

Yapılandırma KIMLIĞINI kullanarak yapılandırmayı bir VM 'ye uygulayın. İçin `-ResourceType VirtualMachines` VM 'nin adını `-ResourceName` ve VM 'nin kaynak grubunu belirtin ve sağlayın `-ResourceGroupName` . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Ayrılmış konak

Bir yapılandırmayı adanmış bir konağa uygulamak için, `-ResourceType hosts` `-ResourceParentName` konak grubu adı ile birlikte da dahil etmeniz gerekir `-ResourceParentType hostGroups` . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Bekleyen güncelleştirmeleri denetle

Bekleyen güncelleştirmeler olup olmadığını görmek için [Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) kullanın. `-subscription`Oturum açmış olduğunuz sunucudan FARKLıYSA VM 'Nin Azure aboneliğini belirtmek için kullanın.

Gösterilecek güncelleştirme yoksa, bu komut hiçbir şey döndürmez. Aksi takdirde, bir PSApplyUpdate nesnesi döndürür:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Yalıtılmış VM

Yalıtılmış bir VM için bekleyen güncelleştirmeleri denetleyin. Bu örnekte, çıkış okunabilirlik için bir tablo olarak biçimlendirilir.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Ayrılmış konak

Adanmış bir ana bilgisayar için bekleyen güncelleştirmeleri denetlemek için. Bu örnekte, çıkış okunabilirlik için bir tablo olarak biçimlendirilir. Kaynakların değerlerini kendi değerlerinizle değiştirin.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Güncelleştirme uygulama

Bekleyen güncelleştirmeleri uygulamak için [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) kullanın.

### <a name="isolated-vm"></a>Yalıtılmış VM

Yalıtılmış bir VM 'ye güncelleştirme uygulamak için bir istek oluşturun.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Bu komut başarılı olduğunda bir `PSApplyUpdate` nesne döndürür. `Get-AzApplyUpdate`Güncelleştirme durumunu denetlemek için komutundaki Name özniteliğini kullanabilirsiniz. Bkz. [güncelleştirme durumunu denetleme](#check-update-status).

### <a name="dedicated-host"></a>Ayrılmış konak

Güncelleştirmeleri adanmış bir konağa uygulayın.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Güncelleştirme durumunu denetle
Bir güncelleştirmenin durumunu denetlemek için [Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) kullanın. Aşağıda gösterilen komutlar, parametresi için kullanılarak en son güncelleştirmenin durumunu gösterir `default` `-ApplyUpdateName` . Belirli bir güncelleştirmenin durumunu almak için, güncelleştirmenin adını ( [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) komutu tarafından döndürülen) kullanabilirsiniz.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime, güncelleştirme tamamlandığında sizin tarafınızdan ya da kendi kendine bakım penceresi kullanılmadığınızda platform tarafından başlatılan zaman olacaktır. Bakım denetimi aracılığıyla uygulanan bir güncelleştirme olmamışsa, varsayılan değer gösterilir.

### <a name="isolated-vm"></a>Yalıtılmış VM

Belirli bir sanal makinede güncelleştirmeleri denetleyin.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Ayrılmış konak

Adanmış bir konaktaki güncelleştirmeleri denetleyin.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Bakım yapılandırmasını kaldırma

Bir bakım yapılandırmasını silmek için [Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) kullanın.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [bakım ve güncelleştirmeler](maintenance-and-updates.md).
