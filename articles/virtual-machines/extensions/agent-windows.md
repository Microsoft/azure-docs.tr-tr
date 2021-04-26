---
title: Azure Sanal Makine Aracısına Genel Bakış
description: Azure Sanal Makine Aracısına Genel Bakış
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 07/20/2019
ms.openlocfilehash: bd845c5ef5a06e3a0bca1cebe54ded2e49355617
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309824"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure sanal makine aracısına genel bakış
Microsoft Azure sanal makine Aracısı (VM Aracısı), Azure yapı denetleyicisi ile sanal makine (VM) etkileşimini yöneten güvenli ve hafif bir işlemdir. VM aracısının Azure sanal makine uzantıları 'nı etkinleştirmek ve yürütmek için birincil bir rolü vardır. VM uzantıları, yazılım yükleme ve yapılandırma gibi VM 'nin dağıtım sonrası yapılandırmasını etkinleştirir. VM uzantıları, bir VM 'nin yönetici parolasını sıfırlama gibi kurtarma özelliklerini de etkinleştirir. Azure VM Aracısı olmadan VM uzantıları çalıştırılamaz.

Bu makalede, Azure sanal makine aracısının yüklenmesi ve algılanması ayrıntılı olarak anlatılmaktadır.

## <a name="install-the-vm-agent"></a>VM aracısını yükler

### <a name="azure-marketplace-image"></a>Azure Market görüntüsü

Azure VM Aracısı, Azure Marketi görüntüsünden dağıtılan tüm Windows VM 'leri için varsayılan olarak yüklenir. Portal, PowerShell, komut satırı arabirimi veya bir Azure Resource Manager şablonundan bir Azure Market görüntüsü dağıttığınızda, Azure VM Aracısı da yüklenir.

Windows Konuk Aracısı paketi iki kısma ayrılmıştır:

- Sağlama Aracısı (PA)
- Windows Konuk Aracısı (WinGA)

Bir VM 'yi önyüklemek için VM 'de PA yüklü olmalıdır, ancak WinGA 'nin yüklenmesi gerekmez. VM dağıtım zamanında, WinGA 'yi yüklememeyi seçebilirsiniz. Aşağıdaki örnek, bir Azure Resource Manager şablonuyla *Provisionvmagent* seçeneğinin nasıl kullanılacağını gösterir:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Yüklü aracılar yoksa, Azure Backup veya Azure güvenliği gibi bazı Azure hizmetlerini kullanamazsınız. Bu hizmetler için bir uzantı yüklenmesi gerekir. WinGA olmadan bir VM dağıttıysanız, daha sonra aracının en son sürümünü yükleyebilirsiniz.

### <a name="manual-installation"></a>El ile yükleme
Windows VM Aracısı bir Windows Installer paketiyle el ile yüklenebilir. Azure 'a dağıtılan özel bir VM görüntüsü oluşturduğunuzda el ile yükleme gerekli olabilir. Windows VM aracısını el ile yüklemek için [VM Aracısı yükleyicisini indirin](https://go.microsoft.com/fwlink/?LinkID=394789). [GitHub Windows IaaS VM Aracısı sürümlerindeki](https://github.com/Azure/WindowsVMAgent/releases)belirli bir sürümü de arayabilirsiniz. VM Aracısı Windows Server 2008 (64 bit) ve sonraki sürümlerde desteklenir.

> [!NOTE]
> VMAgent 'ı, ProvisionVMAgent Enable olmadan görüntüden dağıtılan bir VM 'ye el ile yükledikten sonra AllowExtensionOperations seçeneğinin güncelleştirilmesi önemlidir.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Önkoşullar

- Windows VM Aracısı 'nın çalışması için en az Windows Server 2008 SP2 (64 bit) gerekir. .NET Framework 4,0. Bkz. [Azure 'da sanal makine aracıları Için en düşük sürüm desteği](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

- VM 'nizin 168.63.129.16 IP adresine erişimi olduğundan emin olun. Daha fazla bilgi için bkz. [IP adresi 168.63.129.16 nedir](../../virtual-network/what-is-ip-address-168-63-129-16.md).

- Konuk VM içinde DHCP 'nin etkinleştirildiğinden emin olun. IaaS VM Aracısı ve uzantıları 'nın çalışması için DHCP 'den ana bilgisayar veya doku adresini almak için bu gereklidir. Statik bir özel IP gerekiyorsa, Azure portal veya PowerShell aracılığıyla yapılandırmanız gerekir ve VM içindeki DHCP seçeneğinin etkinleştirildiğinden emin olun. PowerShell ile statik IP adresi ayarlama hakkında [daha fazla bilgi edinin](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) .


## <a name="detect-the-vm-agent"></a>VM aracısını Algıla

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell modülü, Azure VM 'Leri hakkında bilgi almak için kullanılabilir. Azure VM aracısının sağlama durumu gibi bir VM hakkındaki bilgileri görmek için [Get-AzVM](/powershell/module/az.compute/get-azvm)' yi kullanın:

```powershell
Get-AzVM
```

Aşağıdaki sıkıştırılmış örnek çıktıda, içinde iç içe geçmiş *Provisionvmagent* özelliği gösterilmektedir `OSProfile` . Bu özellik VM aracısının VM 'ye dağıtılıp dağıtılmadığını anlamak için kullanılabilir:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Aşağıdaki betik, VM adlarının kısa bir listesini ve VM aracısının durumunu döndürmek için kullanılabilir:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>El ile algılama

Bir Windows VM 'de oturum açıldığında, çalışan işlemlerin incelenmesi için Görev Yöneticisi kullanılabilir. Azure VM aracısını denetlemek için, Görev Yöneticisi 'ni açın, *Ayrıntılar* sekmesine tıklayın ve **WindowsAzureGuestAgent.exe** bir işlem adı arayın. Bu işlemin varlığı, VM aracısının yüklü olduğunu gösterir.


## <a name="upgrade-the-vm-agent"></a>VM aracısını yükseltme
Windows için Azure VM Aracısı, Azure Marketi 'nden dağıtılan görüntülerde otomatik olarak yükseltilir. Yeni VM 'Ler Azure 'a dağıtıldığında, sanal makine sağlama zamanında en son VM aracısını alırlar. Aracıyı el ile yüklediyseniz veya özel VM görüntülerini dağıtıyorsanız, yeni VM aracısını görüntü oluşturma zamanına dahil etmek için el ile güncelleştirmeniz gerekir.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows Konuk Aracısı otomatik Günlükler koleksiyonu
Windows Konuk Aracısı, bazı günlükleri otomatik olarak toplamak için bir özelliğe sahiptir. Bu özellik CollectGuestLogs.exe işlem tarafından denetleyici 'dir. Hem PaaS Cloud Services hem de IaaS sanal makineleri ve hedefi &, bir VM 'den bazı tanılama günlüklerini otomatik olarak toplamaktır ve bu nedenle çevrimdışı analizler için kullanılabilirler. Toplanan Günlükler olay günlüklerdir, işletim sistemi günlükleri, Azure günlükleri ve bazı kayıt defteri anahtarlarıdır. VM 'nin konağa aktarılan bir ZIP dosyası üretir. Bu ZIP dosyası daha sonra mühendislik ekiplerine ve destek uzmanlarına bakarak VM 'ye sahip olan müşterinin isteğiyle ilgili sorunları araştırmak için göz ardı edilebilir.

## <a name="guest-agent-and-osprofile-certificates"></a>Konuk Aracısı ve OSProfile sertifikaları
Azure VM Aracısı, `OSProfile` BIR VM 'de veya sanal makine ölçek kümesinde başvurulan sertifikaların yüklenmesinden sorumludur. Bu sertifikaları Konuk VM 'nin içindeki Sertifikalar MMC konsolundan el ile kaldırırsanız, Konuk aracısının onları geri eklemesi beklenir.
Bir sertifikayı kalıcı olarak kaldırmak için, ' dan kaldırmanız `OSProfile` ve ardından Konuk işletim sisteminin içinden kaldırmanız gerekir.

Bir sanal makine için, ' dan sertifikaları kaldırmak için [Remove-AzVMSecret]() ' i kullanın `OSProfile` .

Sanal makine ölçek kümesi sertifikaları hakkında daha fazla bilgi için bkz. [Sanal Makine Ölçek Kümeleri-nasıl yaparım? kullanımdan kaldırılan sertifikaları kaldırma](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#how-do-i-remove-deprecated-certificates-) .


## <a name="next-steps"></a>Sonraki adımlar
VM uzantıları hakkında daha fazla bilgi için bkz. [Azure sanal makine uzantılarına ve özelliklerine genel bakış](overview.md).
