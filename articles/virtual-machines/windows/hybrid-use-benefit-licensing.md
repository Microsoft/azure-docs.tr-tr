---
title: Windows Server için Azure Hibrit Avantajı
description: Azure 'a şirket içi lisanslar getirmek için Windows yazılım güvencesi avantajlarınızı nasıl en üst düzeye çıkaracağınızı öğrenin.
author: xujing-ms
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: c5a9386540f418c8f490ca146e250e780737e478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668055"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Windows Server için Azure Hibrit Avantajı
Yazılım güvencesi olan müşteriler için, Windows Server Azure Hibrit Avantajı, şirket içi Windows Server lisanslarınızı kullanmanıza ve Azure 'da Windows sanal makinelerini daha düşük bir maliyetle çalıştırmanıza olanak sağlar. Windows işletim sistemi ile yeni sanal makineler dağıtmak için Windows Server Azure Hibrit Avantajı kullanabilirsiniz. Bu makalede, Windows Server için Azure Hibrit Avantajı ile yeni VM 'Leri dağıtma ve var olan çalışan VM 'Leri güncelleştirme adımları ele alınarak devam edebilir. Windows Server Lisanslama ve maliyet tasarrufları için Azure Hibrit Avantajı hakkında daha fazla bilgi için, bkz. [Windows Server Lisanslama için Azure hibrit avantajı sayfası](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Her 2 işlemci lisansı veya her bir 16 çekirdekli lisans kümesi, en fazla 8 çekirdeğe veya 16 adede kadar çekirdeğe kadar olan iki örneğe sahiptir. Standart sürüm lisansları için Azure Hibrit Avantajı, şirket içinde veya Azure 'da yalnızca bir kez kullanılabilir. Datacenter Edition avantajları, hem şirket içinde hem de Azure 'da eşzamanlı kullanım sağlar.

Windows Server işletim sistemi çalıştıran herhangi bir VM ile Windows Server için Azure Hibrit Avantajı kullanmak, SQL Server veya üçüncü taraf Market yazılımları gibi ek yazılıma sahip VM 'Ler de dahil olmak üzere tüm bölgelerde desteklenmektedir. 


## <a name="classic-vms"></a>Klasik VM'ler

Klasik VM 'Ler için, yalnızca şirket içi özel görüntülerden yeni VM dağıtımı desteklenir. Bu makalede desteklenen özelliklerden faydalanmak için, önce klasik VM 'Leri Kaynak Yöneticisi modele geçirmeniz gerekir.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Windows Server için Azure Hibrit Avantajı kullanmanın yolları
Windows sanal makinelerini Azure Hibrit Avantajı kullanmanın birkaç yolu vardır:

1. Azure Marketi 'nde, sağlanmış Windows Server görüntülerinden birindeki VM 'Leri dağıtabilirsiniz
2. Özel bir VM 'yi karşıya yükleyebilir ve bir Kaynak Yöneticisi şablonu kullanarak dağıtabilirsiniz Azure PowerShell
3. Mevcut VM 'yi Azure Hibrit Avantajı ile çalıştırma arasında açıp dönüştürebilirsiniz veya Windows Server için isteğe bağlı maliyet ödeyebilirsiniz
4. Ayrıca, sanal makine ölçek kümesindeki Windows Server için Azure Hibrit Avantajı de uygulayabilirsiniz


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Windows Server için Azure Hibrit Avantajı VM oluşturma
Windows Server için Azure Hibrit Avantajı için tüm Windows Server işletim sistemi tabanlı görüntüler desteklenir. Azure platform destek görüntülerini kullanabilir veya kendi özel Windows Server görüntülerinizi yükleyebilirsiniz. 

### <a name="portal"></a>Portal
Windows Server için Azure Hibrit Avantajı bir VM oluşturmak için, oluşturma işlemi sırasında **temel bilgiler** sekmesinin en altına kaydırın ve **lisanslama** bölümünde var olan bir Windows Server lisansını kullanmak için kutuyu işaretleyin. 

### <a name="powershell"></a>PowerShell

```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Şablon
Kaynak Yöneticisi şablonlarınız içinde ek bir parametre `licenseType` belirtilmesi gerekir. [Azure Resource Manager şablonları yazma](../../azure-resource-manager/templates/template-syntax.md) hakkında daha fazla bilgi edinebilirsiniz
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Windows Server için Azure Hibrit Avantajı kullanarak var olan bir VM 'yi dönüştürme
Windows Server Azure Hibrit Avantajı avantajlarından yararlanmak için dönüştürmek istediğiniz mevcut bir VM 'niz varsa, aşağıdaki yönergeleri izleyerek sanal makinenizin lisans türünü güncelleştirebilirsiniz.

> [!NOTE]
> VM 'deki lisans türünü değiştirmek sistemin yeniden başlatılmasına veya hizmet görüşmesine neden olmasına neden olmaz.  Meta veri bayrağına yalnızca bir güncelleştirmedir.
> 

### <a name="portal"></a>Portal
Portal VM dikey penceresinde, "yapılandırma" seçeneğini belirleyerek ve "Azure hibrit avantajı" seçeneğini değiştirerek sanal makineyi Azure Hibrit Avantajı kullanacak şekilde güncelleştirebilirsiniz.

### <a name="powershell"></a>PowerShell
- Mevcut Windows Server VM 'lerini Windows Server için Azure Hibrit Avantajı Dönüştür

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Windows Server VM 'lerini Kullandıkça Öde 'e geri dönme

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Mevcut Windows Server VM 'lerini Windows Server için Azure Hibrit Avantajı Dönüştür

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM 'nizin lisans avantajını kullandığını doğrulama
VM 'nizi PowerShell, Kaynak Yöneticisi Şablon ya da Portal aracılığıyla dağıttıktan sonra, aşağıdaki yöntemlerde ayarı doğrulayabilirsiniz.

### <a name="portal"></a>Portal
Portal VM dikey penceresinde, "yapılandırma" sekmesini seçerek Windows Server için Azure Hibrit Avantajı geçişi 'ni görüntüleyebilirsiniz.

### <a name="powershell"></a>PowerShell
Aşağıdaki örnekte, tek bir VM için lisans türü gösterilmektedir
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Çıkış:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Bu çıktı, Windows Server Lisansı için Azure Hibrit Avantajı olmadan dağıtılan aşağıdaki VM ile karşıttır:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> VM 'deki lisans türünü değiştirmek sistemin yeniden başlatılmasına veya hizmet görüşmesine neden olmasına neden olmaz. Yalnızca meta veri lisanslama bayrağıdır.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Bir abonelikte Windows Server için Azure Hibrit Avantajı tüm VM 'Leri listeleme
Windows Server için Azure Hibrit Avantajı ile dağıtılan tüm sanal makineleri görmek ve saymak için aboneliğinizden aşağıdaki komutu çalıştırabilirsiniz:

### <a name="portal"></a>Portal
Sanal makine veya sanal makine ölçek kümeleri kaynak dikey penceresinde, tablo sütununu "Azure Hibrit Avantajı" içerecek şekilde yapılandırarak tüm VM 'lerinizin ve lisanslama türünün bir listesini görüntüleyebilirsiniz. VM ayarı "etkin", "etkin değil" veya "desteklenmiyor" durumunda olabilir.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Windows Server için Azure Hibrit Avantajı bir sanal makine ölçek kümesi dağıtma
Sanal Makine Ölçek Kümesi Kaynak Yöneticisi şablonlarında, `licenseType` VirtualMachineProfile özelliği içinde ek bir parametre belirtilmelidir. ARM şablonu, PowerShell, Azure CLı veya REST aracılığıyla ölçek kümesi oluşturma veya güncelleştirme sırasında bunu yapabilirsiniz.

Aşağıdaki örnek ARM şablonunu bir Windows Server 2016 Datacenter görüntüsüyle kullanır:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Ayrıca, ölçek kümesini güncelleştirme hakkında daha fazla yol için [bir sanal makine ölçek kümesini değiştirme](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) hakkında daha fazla bilgi edinebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure hibrit avantajı ile tasarruf etme](https://azure.microsoft.com/pricing/hybrid-use-benefit/) hakkında daha fazla bilgi edinin
- [Azure hibrit avantajı hakkında sık sorulan sorular](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/) hakkında daha fazla bilgi edinin
- [Windows Server Lisanslama ayrıntılı Kılavuzu için Azure hibrit avantajı](/windows-server/get-started/azure-hybrid-benefit) hakkında daha fazla bilgi edinin
- Windows Server Azure Hibrit Avantajı hakkında daha fazla bilgi edinin [ve Azure Site Recovery Azure 'a uygulama geçirmeyi daha da verimli hale getirin](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- [Azure 'Da çok kiracılı barındırma Ile Windows 10](./windows-desktop-multitenant-hosting-deployment.md) hakkında daha fazla bilgi edinin
- [Kaynak Yöneticisi şablonlarını kullanma](../../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin
