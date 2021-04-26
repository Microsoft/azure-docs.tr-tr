---
title: Azure 'da VM 'Ler ve ölçek kümeleri için otomatik uzantı yükseltmesi
description: Azure 'daki sanal makineleriniz ve sanal makine ölçek kümeleriniz için otomatik uzantı yükseltmesini nasıl etkinleştireceğinizi öğrenin.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: bf9e802e2485e84211044ce650c7748e789e752e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762614"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Önizleme: Azure 'da VM 'Ler ve ölçek kümeleri için otomatik uzantı yükseltmesi

Otomatik uzantı yükseltmesi, Azure VM 'Leri ve Azure sanal makine ölçek kümeleri için Önizleme sürümünde kullanılabilir. Bir VM veya ölçek kümesi üzerinde otomatik uzantı yükseltmesi etkinleştirildiğinde, uzantı yayımcısı bu uzantı için yeni bir sürüm yayınlarsa uzantı otomatik olarak yükseltilir.

 Otomatik uzantı yükseltme aşağıdaki özelliklere sahiptir:
- Azure VM 'Leri ve Azure sanal makine ölçek kümeleri için desteklenir. Service Fabric sanal makine ölçek kümeleri Şu anda desteklenmiyor.
- Yükseltmeler, bir kullanılabilirlik-ilk dağıtım modelinde (aşağıda ayrıntılı olarak) uygulanır.
- Bir sanal makine ölçek kümesi için, ölçek kümesi sanal makinelerinin %20 ' inden fazlası tek bir toplu işte yükseltilmeyecektir. En küçük toplu iş boyutu bir sanal makinedir.
- Tüm VM boyutları ve hem Windows hem de Linux uzantıları için geçerlidir.
- Dilediğiniz zaman Otomatik yükseltmeleri devre dışı bırakabilirsiniz.
- Otomatik uzantı yükseltmesi, her boyuttaki bir sanal makine ölçek kümesinde etkinleştirilebilir.
- Desteklenen her uzantı ayrı ayrı kaydedilir ve hangi uzantıların otomatik olarak yükseltilecek olduğunu seçebilirsiniz.
- Tüm genel bulut bölgelerinde desteklenir.


> [!IMPORTANT]
> Otomatik uzantı yükseltmesi şu anda genel önizlemededir. Aşağıda açıklanan genel önizleme işlevlerini kullanmak için bir katılım prosedürü gereklidir.
> Bu önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Otomatik uzantı yükseltmesi nasıl çalışır?
Uzantı yükseltme işlemi, bir VM 'deki mevcut uzantı sürümünün uzantısını uzantı yayımcısı tarafından yayımlandığında aynı uzantının yeni bir sürümüyle değiştirir. Yeni uzantı yüklendikten sonra sanal makinenin sistem durumu izlenir. VM yükseltme tamamlandığında 5 dakika içinde sağlıklı bir durumda değilse, uzantı sürümü önceki sürüme geri döndürülür.

Başarısız bir uzantı güncelleştirmesi otomatik olarak yeniden denenir. Yeniden deneme, her gün Kullanıcı müdahalesi olmadan otomatik olarak denenir.

### <a name="availability-first-updates"></a>Kullanılabilirlik-ilk güncelleştirmeler
Platform tarafından düzenlenen güncelleştirmeler için kullanılabilirlik-ilk model, Azure 'daki kullanılabilirlik yapılandırmalarının birden çok kullanılabilirlik düzeyi arasında olmasını sağlar.

Güncelleştirme yapılmakta olan bir sanal makine grubu için, Azure platformu güncelleştirmeleri düzenler:

**Bölgeler arasında:**
- Azure genelinde dağıtım başarısızlıklarını engellemek için bir güncelleştirme, Azure üzerinden küresel bir şekilde hareket edecektir.
- Bir ' aşama ' bir veya daha fazla bölgeye sahip olabilir ve bir güncelleştirme yalnızca önceki aşamadaki uygun VM 'Ler başarıyla güncellediğinde aşamalar arasında gider.
- Coğrafi eşleştirilmiş bölgeler eşzamanlı olarak güncelleştirilmeyecek ve aynı bölgesel aşamada olamaz.
- Bir güncelleştirmenin başarısı, bir VM Post güncelleştirmesi durumunun izlenmesi ile ölçülür. VM sistem durumu, VM için platform sistem durumu göstergeleri aracılığıyla izlenir. Sanal Makine Ölçek Kümeleri için, VM sistem durumu, ölçek kümesine uygulandığında uygulama durumu araştırmaları veya uygulama sistem durumu uzantısı aracılığıyla izlenir.

**Bir bölge içinde:**
- Farklı Kullanılabilirlik Alanları sanal makineler eşzamanlı olarak güncellenmez.
- Bir abonelik içindeki tüm VM 'Lerin eş zamanlı güncelleştirmelerini önlemek için bir kullanılabilirlik kümesinin parçası olmayan tek VM 'Ler en iyi çaba temelinde toplu olarak oluşturulur.  

**' Set ' içinde:**
- Ortak bir kullanılabilirlik kümesindeki veya ölçek kümesindeki tüm VM 'Ler eşzamanlı olarak güncellenmez.  
- Ortak bir kullanılabilirlik kümesindeki VM 'Ler güncelleştirme etki alanı sınırları içinde güncelleştirilir ve birden çok güncelleştirme etki alanındaki VM 'Ler eşzamanlı olarak güncellenmez.  
- Ortak bir sanal makine ölçek kümesindeki sanal makineler toplu olarak gruplandırılır ve güncelleştirme etki alanı sınırları içinde güncelleştirilir.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri için yükseltme işlemi
1. Yükseltme işlemine başlamadan önce Orchestrator, tüm ölçek kümesindeki VM 'lerin %20 ' inden daha fazla olmadığından emin olur (herhangi bir nedenle).

2. Yükseltme Orchestrator, Yükseltilecek sanal makine örneklerinin toplu işini tanımlar. Bir yükseltme toplu işi, bir sanal makinenin en düşük toplu iş boyutuna bağlı olarak toplam VM sayısının en fazla %20 ' si olabilir.

3. Yapılandırılmış uygulama durumu araştırmaları veya uygulama sistem durumu uzantısı olan ölçek kümeleri için, yükseltme, sonraki toplu işi yükseltmeden önce VM 'nin sağlıklı olması için 5 dakikaya (veya tanımlı sistem durumu araştırması yapılandırmasına) kadar bekler. Bir VM bir yükseltmeden sonra durumunu kurtarmaz, varsayılan olarak VM 'deki önceki uzantı sürümü yeniden yüklenir.

4. Yükseltme Orchestrator Ayrıca, bir yükseltmeden sonra sağlıksız hale gelen VM 'lerin yüzdesini de izler. Yükseltme işlemi sırasında yükseltilen örneklerin %20 ' den fazlası sağlıksız hale gelirse yükseltme durdurulur.

Yukarıdaki işlem, ölçek kümesindeki tüm örnekler yükseltilene kadar devam eder.

Ölçek kümesi yükseltme Orchestrator, her toplu işi yükseltmeden önce genel ölçek kümesi sistem durumunu denetler. Bir toplu işi yükseltirken, ölçek kümesi sanal makinelerinizin sistem durumunu etkileyebilecek diğer eşzamanlı planlı veya planlanmamış bakım etkinlikleri olabilir. Bu gibi durumlarda, ölçek kümesinin örneklerinin %20 ' si sağlıksız hale gelirse, ölçek kümesi yükseltmesi geçerli toplu işin sonunda duraklar.

## <a name="supported-extensions"></a>Desteklenen uzantılar
Otomatik uzantı yükseltmesinin önizlemesi aşağıdaki uzantıları destekler (ve daha fazla düzenli olarak eklenir):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) ve [Linux](./extensions/agent-dependency-linux.md)
- [Uygulama durumu uzantısı](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows ve Linux


## <a name="enabling-preview-access"></a>Önizleme erişimini etkinleştirme
Önizleme işlevselliğinin etkinleştirilmesi, aşağıda açıklandığı gibi, abonelik başına **Automaticextensionupgradepreview** özelliği için tek seferlik bir kabul gerektirir.

### <a name="rest-api"></a>REST API
Aşağıdaki örnekte, aboneliğiniz için önizlemenin nasıl etkinleştirileceği açıklanmaktadır:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Aboneliğiniz için Önizlemeyi etkinleştirmek üzere [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 'ini kullanın.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
Aboneliğiniz için Önizlemeyi etkinleştirmek üzere [az Feature Register](/cli/azure/feature#az_feature_register) kullanın.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Otomatik uzantı yükseltmesini etkinleştirme
Bir uzantı için otomatik uzantı yükseltmesini etkinleştirmek üzere, *Enableautomaticupgrade* özelliğinin *true* olarak ayarlandığından ve her uzantı tanımına ayrı ayrı eklendiğinden emin olmanız gerekir.


### <a name="rest-api-for-virtual-machines"></a>Sanal makineler için REST API
Bir uzantıya yönelik otomatik uzantı yükseltmesini etkinleştirmek için (Bu örnekte Dependency Agent uzantısı) bir Azure VM 'de aşağıdakileri kullanın:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri için REST API
Uzantıyı ölçek kümesi modeline eklemek için aşağıdakileri kullanın:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Sanal makineler için Azure PowerShell
[Set-Azvmexgeri](/powershell/module/az.compute/set-azvmextension) cmdlet 'ini kullanın:

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri için Azure PowerShell
Uzantıyı ölçek kümesi modeline eklemek için [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet 'ini kullanın:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Uzantı eklendikten sonra [Update-AzVmss](/powershell/module/az.compute/update-azvmss) kullanarak ölçek kümesini güncelleştirin.


### <a name="azure-cli-for-virtual-machines"></a>Sanal Makineler için Azure CLI
[Az VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) cmdlet 'ini kullanın:

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri için Azure CLı
Uzantıyı ölçek kümesi modeline eklemek için [az VMSS Extension set](/cli/azure/vmss/extension#az_vmss_extension_set) cmdlet 'ini kullanın:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Birden çok uzantıya sahip uzantı yükseltmeleri

Bir VM veya sanal makine ölçek kümesi, otomatik uzantı yükseltmesi etkin olan birden çok uzantıya sahip olabilir. Aynı VM veya ölçek kümesi, otomatik genişletme yükseltmesi olmayan diğer uzantılara de sahip olabilir.  

Bir sanal makine için birden çok uzantı yükseltmesi varsa, yükseltmeler birlikte toplanmış olabilir, ancak her uzantı yükseltmesi bir sanal makinede ayrı ayrı uygulanır. Bir uzantıdaki hata, yükseltilemeyebilir olan diğer uzantıları etkilemez. Örneğin, iki uzantı bir yükseltme için zamanlanırsa ve ilk uzantı yükseltmesi başarısız olursa ikinci uzantı yine de yükseltilmeye devam edecektir.

Otomatik uzantı yükseltmeleri, bir VM veya sanal makine ölçek kümesinde [uzantı sıralaması](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)ile yapılandırılmış birden çok uzantı olduğunda da uygulanabilir. Uzantı sıralaması, sanal makinenin ilk kez dağıtılması için geçerlidir ve bir uzantıdaki gelecek uzantı yükseltmeleri bağımsız olarak uygulanır.


## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Uygulama sistem durumu uzantısı hakkında bilgi edinin](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
