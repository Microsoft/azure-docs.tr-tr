---
title: Windows için Azure VM uzantıları ve özellikleri
description: Azure sanal makineleri için neler sundukları veya iyileştirebilecekleri ile gruplanan uzantıları öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79c6658d2b3758eed94f273bf0b3685bbd146278
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073076"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Windows için sanal makine uzantıları ve özellikleri

Azure sanal makinesi (VM) uzantıları, Azure VM 'lerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlayan küçük uygulamalardır. Örneğin, bir sanal makine yazılım yüklemesi, virüsten koruma koruması veya bunun içinde bir komut dosyası çalıştırmak istiyorsa, bir VM Uzantısı kullanılabilir. Azure VM uzantıları, Azure CLı, PowerShell, Azure Resource Manager şablonları ve Azure portal birlikte çalıştırılabilir. Uzantılar, yeni bir VM dağıtımıyla birlikte paketlenmiştir veya var olan herhangi bir sisteme karşı çalıştırılabilir.

Bu makalede VM uzantılarına genel bakış, Azure VM uzantıları kullanma önkoşulları ve VM uzantılarının algılanması, yönetilmesi ve kaldırılması ile ilgili yönergeler sunulmaktadır. Bu makalede birçok VM Uzantısı kullanılabildiği için, her biri potansiyel olarak benzersiz bir yapılandırma olan Genelleştirilmiş bilgiler sağlanmaktadır. Uzantıya özgü ayrıntılar, tek tek uzantıya özgü her belge içinde bulunabilir.

 

## <a name="use-cases-and-samples"></a>Kullanım örnekleri ve örnekler

Birçok farklı Azure VM uzantısı, her biri belirli bir kullanım durumu ile kullanılabilir. Bazı örnekler:

- PowerShell Istenen durum yapılandırmasını Windows için DSC uzantısına sahip bir VM 'ye uygulayın. Daha fazla bilgi için bkz. [Azure Istenen durum yapılandırması uzantısı](dsc-overview.md).
- Log Analytics Aracısı VM uzantısıyla bir VM 'nin izlenmesini yapılandırın. Daha fazla bilgi için bkz. [Azure VM 'leri Azure izleyici günlüklerine bağlama](../../log-analytics/log-analytics-azure-vm-extension.md).
- Chef kullanarak bir Azure VM yapılandırın. Daha fazla bilgi için bkz. [Chef Ile Azure VM dağıtımını otomatikleştirme](../windows/chef-automation.md).
- Dataköpek uzantısıyla Azure altyapınızı izlemeyi yapılandırın. Daha fazla bilgi için bkz. [Dataköpek blogu](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


İşleme özgü uzantılara ek olarak, hem Windows hem de Linux sanal makineleri için özel bir betik uzantısı vardır. Windows için özel Betik uzantısı, bir VM 'de herhangi bir PowerShell betiğinin çalıştırılmasını sağlar. Özel betikler, yerel Azure araçlarının sağlayabildiklerinin ötesinde yapılandırılması gereken Azure dağıtımlarını tasarlamak için yararlıdır. Daha fazla bilgi için bkz. [WINDOWS VM özel Betik uzantısı](custom-script-windows.md).

## <a name="prerequisites"></a>Önkoşullar

SANAL makinede uzantıyı işlemek için Azure Windows aracısının yüklü olması gerekir. Bazı ayrı uzantılar, kaynaklara veya bağımlılıklara erişim gibi önkoşullara sahiptir.

### <a name="azure-vm-agent"></a>Azure VM aracısı

Azure VM Aracısı, bir Azure VM ile Azure yapı denetleyicisi arasındaki etkileşimleri yönetir. VM Aracısı, çalışan VM uzantıları dahil olmak üzere Azure VM 'Leri dağıtmanın ve yönetmenin pek çok işlevsel yönünden sorumludur. Azure VM Aracısı, Azure Market görüntülerine önceden yüklenir ve desteklenen işletim sistemlerine el ile yüklenebilir. Windows için Azure VM Aracısı Windows Konuk Aracısı olarak bilinir.

Desteklenen işletim sistemleri ve yükleme yönergeleri hakkında daha fazla bilgi için bkz. [Azure sanal makine Aracısı](agent-windows.md).

#### <a name="supported-agent-versions"></a>Desteklenen Aracı sürümleri

Mümkün olan en iyi deneyimi sağlamak için aracının en düşük sürümü vardır. Daha fazla bilgi için [bu makaleye](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) bakın.

#### <a name="supported-oses"></a>Desteklenen Işletim sistemleri

Windows Konuk Aracısı birden çok OSes üzerinde çalışır, ancak uzantı çerçevesinin uzantıları bu uzantılara yönelik bir sınıra sahiptir. Daha fazla bilgi için [bu makaleye](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
) bakın.

Bazı uzantılar tüm Işletim sistemlerinde desteklenmez ve *51 hata kodu, ' desteklenmeyen işletim sistemi '* verilebilir. Desteklenebilirliği için bağımsız uzantı belgelerini denetleyin.

#### <a name="network-access"></a>Ağ erişimi

Uzantı paketleri Azure Storage uzantı deposundan indirilir ve uzantı durumu karşıya yüklemeleri Azure depolama 'ya gönderilir. Aracıların [desteklenen](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) sürümünü kullanıyorsanız, sanal makine bölgesindeki Azure depolama 'ya erişime izin vermeniz gerekmez, çünkü aracıyı aracı Iletişimleri için Azure Fabric denetleyicisi 'Ne (HostGAPlugin özelliği aracılığıyla) yeniden yönlendirmek için kullanabilir. Özel IP [168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)üzerinde ayrıcalıklı kanal). Aracının desteklenmeyen bir sürümü kullanıyorsanız, VM 'den o bölgedeki Azure depolama 'ya giden erişime izin vermeniz gerekir.

> [!IMPORTANT]
> Konuk güvenlik duvarını veya bir ara sunucu kullanarak *168.63.129.16* 'e erişimi engellediğiniz takdirde, uzantılar yukarıdakilerden bağımsız olarak başarısız olur. 80, 443 ve 32526 bağlantı noktaları gereklidir.

Aracılar yalnızca uzantı paketleri ve raporlama durumunu indirmek için kullanılabilir. Örneğin, bir uzantı yüklemesinin GitHub 'dan (özel betik) bir betiği indirmesi veya Azure depolama 'ya (Azure Backup) erişmesi gerekiyorsa, ek güvenlik duvarı/ağ güvenlik grubu bağlantı noktalarının açılması gerekir. Farklı uzantılar, kendi sağında uygulamalar olduklarından farklı gereksinimlere sahiptir. Azure depolama veya Azure Active Directory erişimi gerektiren uzantılar için, [Azure NSG hizmeti etiketlerini](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) depolama veya AzureActiveDirectory kullanarak erişime izin verebilirsiniz.

Windows Konuk Aracısı, aracı trafik isteklerini aracılığıyla yeniden yönlendirebilmeniz için proxy sunucu desteğine sahip değildir. Bu, Windows Konuk aracısının internet üzerindeki kaynaklara veya ana bilgisayardan IP aracılığıyla erişmek için özel ara sunucunuza (varsa) bağlı olacağı anlamına gelir. 168.63.129.16.

## <a name="discover-vm-extensions"></a>VM uzantılarını bulma

Azure VM'leri ile kullanabileceğiniz birçok farklı VM uzantısı vardır. Tam listeyi görmek için [Get-Azvmextensionımage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage)komutunu kullanın. Aşağıdaki örnek, *WestUS* konumundaki tüm kullanılabilir uzantıları listeler:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>VM uzantılarını Çalıştır

Azure VM uzantıları, mevcut VM 'lerde çalışır, bu, önceden dağıtılmış bir VM 'de yapılandırma değişiklikleri yapmanız veya bağlantıyı kurtarmanız gerektiğinde yararlı olur. VM uzantıları, Azure Resource Manager şablon dağıtımları ile de paketlenmiştir. Azure VM 'Leri, Kaynak Yöneticisi şablonlarıyla birlikte uzantıları kullanarak dağıtım sonrası müdahalesi olmadan dağıtılabilir ve yapılandırılabilir.

Aşağıdaki yöntemler mevcut bir VM 'ye karşı bir uzantı çalıştırmak için kullanılabilir.

### <a name="powershell"></a>PowerShell

Tek tek uzantıları çalıştırmak için çeşitli PowerShell komutları vardır. Bir listeyi görmek için, [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) ve *EXTENSION*FILTER kullanın:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Bu, aşağıdakine benzer bir çıktı sağlar:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

Aşağıdaki örnek, bir GitHub deposundan hedef sanal makineye bir betiği indirmek için özel betik uzantısını kullanır ve ardından betiği çalıştırır. Özel Betik uzantısı hakkında daha fazla bilgi için bkz. [Özel Betik uzantısına genel bakış](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

Aşağıdaki örnekte, VM erişimi uzantısı bir Windows sanal makinesinin yönetici parolasını geçici bir parolaya sıfırlamak için kullanılır. VM erişimi uzantısı hakkında daha fazla bilgi için bkz. [WINDOWS VM 'de Uzak Masaüstü hizmetini sıfırlama](../windows/reset-rdp.md). Bunu çalıştırdığınızda, ilk oturum açmada parolayı sıfırlamanız gerekir:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzVMExtension` komutu herhangi bir VM uzantısını başlatmak için kullanılabilir. Daha fazla bilgi için bkz. [set-Azvmexgerme başvurusu](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Azure portalında

VM uzantıları, mevcut bir VM 'ye Azure portal aracılığıyla uygulanabilir. Portalda VM 'yi seçin, **Uzantılar**' ı seçin ve **Ekle**' yi seçin. Kullanılabilir uzantılar listesinden istediğiniz uzantıyı seçin ve sihirbazdaki yönergeleri izleyin.

Aşağıdaki örnekte, Microsoft kötü amaçlı yazılımdan koruma uzantısının Azure portal yüklemesi gösterilmektedir:

![Kötü amaçlı yazılımdan koruma uzantısını yükler](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları

VM uzantıları, bir Azure Resource Manager şablonuna eklenebilir ve şablonun dağıtımıyla birlikte yürütülür. Bir uzantıyı bir şablon ile dağıttığınızda, tam olarak yapılandırılmış Azure dağıtımları oluşturabilirsiniz. Örneğin, bir Kaynak Yöneticisi şablonundan aşağıdaki JSON alınır bir yük dengeli VM 'Ler ve bir Azure SQL veritabanı dağıtır ve ardından her VM 'ye bir .NET Core uygulaması yükler. VM uzantısı, yazılım yüklemesini üstlenir.

Daha fazla bilgi için bkz. [tam kaynak yöneticisi şablonu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Kaynak Yöneticisi şablonları oluşturma hakkında daha fazla bilgi için bkz. [WINDOWS VM uzantıları Ile yazma Azure Resource Manager şablonları](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>VM Uzantısı verilerini güvenli hale getirme

Bir VM Uzantısı çalıştırdığınızda kimlik bilgileri, depolama hesabı adları ve depolama hesabı erişim anahtarları gibi hassas bilgileri eklemek gerekebilir. Birçok VM uzantısı, verileri şifreleyen ve yalnızca hedef VM 'nin şifresini çözdüğü korumalı bir yapılandırma içerir. Her uzantının belirli bir korumalı yapılandırma şeması vardır ve her biri uzantıya özgü belgelerde ayrıntılıdır.

Aşağıdaki örnek, Windows için özel betik uzantısının bir örneğini gösterir. Yürütülecek komut bir kimlik bilgileri kümesi içerir. Bu örnekte, yürütülecek komut şifrelenmemiştir:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Aşağıdaki örnekte gösterildiği gibi, komutu, **korumalı** yapılandırmaya özelliği **yürütmek üzere** taşımak, yürütme dizesinin güvenliğini sağlar:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Aracılar ve uzantılar nasıl güncelleştirilir?

Aracılar ve uzantılar aynı güncelleştirme mekanizmasını paylaşır. Bazı güncelleştirmeler ek güvenlik duvarı kuralları gerektirmez.

Bir güncelleştirme kullanılabilir olduğunda, yalnızca Uzantılardaki bir değişiklik olduğunda VM 'de ve diğer VM modeli değiştikçe şu şekilde değişir:

- Veri diskleri
- Uzantılar
- Önyükleme tanılaması kapsayıcısı
- Konuk işletim sistemi gizli dizileri
- VM boyutu
- Ağ profili

Yayımcılar, güncelleştirmeleri farklı zamanlarda bölgelere uygun hale getirir, böylece farklı sürümlerde farklı bölgelerde sanal makinelere sahip olabilirsiniz.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Bir VM 'ye dağıtılan uzantıları listeleme

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Aracı güncelleştirmeleri

Windows Konuk Aracısı yalnızca *uzantı işleme kodu*Içerir, *Windows sağlama kodu* ayrı olur. Windows Konuk Aracısı 'nı kaldırabilirsiniz. Pencere Konuk aracısının otomatik güncelleştirilmesini devre dışı bırakılamaz.

*Uzantı işleme kodu* , Azure dokusuna iletişim kurmaktan ve Yüklemeler, raporlama durumu, tek tek uzantıları güncelleştirme ve bunları KALDıRMA gibi VM uzantıları işlemlerinin işlenmesine yöneliktir. Güncelleştirmeler, güvenlik düzeltmelerini, hata düzeltmelerini ve *uzantı işleme koduna*yönelik geliştirmeleri içerir.

Çalıştırdığınız sürümü denetlemek için bkz. [yüklü Windows Konuk aracısını algılama](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Uzantı güncelleştirmeleri

Bir uzantı güncelleştirmesi kullanılabilir olduğunda, Windows Konuk Aracısı uzantıyı indirir ve yükseltir. Otomatik uzantı güncelleştirmeleri, *küçük* veya *Düzeltme*olabilir. Uzantıyı sağladığınızda uzantıların *küçük* güncelleştirmelerini kabul edebilir veya devre dışı bırakabilirsiniz. Aşağıdaki örnek, bir Kaynak Yöneticisi şablonundaki alt sürümlerin otomatik olarak *': true, '* ile nasıl yükseltilleceğini gösterir:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

En son küçük sürüm hata düzeltmelerini almak için, uzantı dağıtımlarınızda her zaman otomatik güncelleştirme ' yi seçmeniz önemle tavsiye edilir. Güvenlik veya anahtar hata düzeltmelerini taşıyan düzeltme güncelleştirmeleri devre dışı bırakılamaz.

### <a name="how-to-identify-extension-updates"></a>Uzantı güncelleştirmelerini belirleme

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Uzantının, bir VM 'de oto Upgrademinorversion ile ayarlanmış olup olmadığını belirleme

Uzantının ' oto Upgrademinorversion ' ile sağlanması durumunda VM modelinden bakabilirsiniz. Denetlemek için [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) ' i kullanın ve kaynak grubunu ve VM adını şu şekilde sağlayın:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Aşağıdaki örnek çıktıda, ' *true*' olarak ayarlanmış *olan bir çıktı* gösterilmektedir:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Bir bir bir bir bir bir bir bir oto Upgrademinorversion

Uzantıya yönelik bir güncelleştirmenin ne zaman oluştuğunu görmek için, *C:\windowsazure\logs\waappagent.log* konumundaki VM 'deki aracı günlüklerini gözden geçirin.

Aşağıdaki örnekte, VM 'de *Microsoft. COMPUTE. CustomScriptExtension 1,8* yüklü. *1,9*sürümüne bir düzeltme sunuldu:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Aracı izinleri

Görevlerini gerçekleştirmek için aracının *yerel sistem*olarak çalıştırılması gerekir.

## <a name="troubleshoot-vm-extensions"></a>VM uzantılarının sorunlarını giderme

Her VM uzantısının uzantıya özgü sorun giderme adımları olabilir. Örneğin, Özel Betik uzantısını kullandığınızda, komut dosyası yürütme ayrıntıları, uzantının çalıştırıldığı VM 'de yerel olarak bulunabilir. Uzantıya özgü tüm sorun giderme adımları uzantıya özgü belgelerde ayrıntılıdır.

Aşağıdaki sorun giderme adımları tüm VM uzantıları için geçerlidir.

1. Windows Konuk Aracısı günlüğünü denetlemek için, uzantınızın *C:\windowsazure\logs\waappagent.txt* dizininde sağlanması durumunda etkinliğe bakın

2. *C:\windowsazure\logs\eklentiler\<extensionName >* hakkında daha fazla ayrıntı için gerçek uzantı günlüklerine bakın

3. Hata kodları, bilinen sorunlar vb. için uzantıya özgü belge sorunlarını giderme bölümlerini denetleyin.

4. Sistem günlüklerine bakın. Uzantıya sahip olabilecek, Özel Paket Yöneticisi erişimi gerektiren başka bir uygulamanın uzun süre çalışan yüklemesi gibi diğer işlemleri denetleyin.

### <a name="common-reasons-for-extension-failures"></a>Uzantı hatalarının yaygın nedenleri

1. Uzantıların çalışması için 20 dakika vardır (özel durumlar CustomScript Extensions, Chef ve 90 dakikalık bir DSC 'Dir). Dağıtımınız bu saati aşarsa, zaman aşımı olarak işaretlenir. Bunun nedeni, düşük kaynak VM 'lerinin, diğer VM yapılandırmalarının/başlangıç görevlerinin, uzantının sağlamaya çalıştığı yüksek miktarda kaynak tüketiyor olmasından kaynaklanabilir.

2. En düşük önkoşul karşılanmadı. Bazı uzantıların, HPC görüntüleri gibi VM SKU 'Larında bağımlılıkları vardır. Uzantılar, Azure depolama veya ortak hizmetlere iletişim gibi belirli ağ erişim gereksinimleri gerektirebilir. Diğer örnekler paket depolarına, yetersiz disk alanı veya güvenlik kısıtlamalarına erişebilir.

3. Özel Paket Yöneticisi erişimi. Bazı durumlarda, büyük bir çalışan VM yapılandırması ve dahili uzantı yüklemesi ile karşılaşmanız, burada her ikisinin de paket yöneticisine özel erişime ihtiyacı olabilir.

### <a name="view-extension-status"></a>Uzantı durumunu görüntüle

VM uzantısı bir VM 'ye karşı çalıştırıldıktan sonra, uzantı durumunu döndürmek için [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) ' yi kullanın. *Alt durumlar [0]* uzantı sağlama işleminin başarılı olduğunu, yani VM 'ye başarıyla dağıtıldığını gösterir, ancak uzantının VM içinde yürütülmesi başarısız oldu, *alt durumlar [1]* .

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Çıktı aşağıdaki örnek çıkışına benzer:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Uzantı yürütme durumu Azure portal da bulunabilir. Bir uzantının durumunu görüntülemek için VM 'yi seçin, **Uzantılar**' ı seçin ve ardından istediğiniz uzantıyı seçin.

### <a name="rerun-vm-extensions"></a>VM uzantılarını yeniden çalıştır

Bir VM uzantısının yeniden çalıştırılması gereken durumlar olabilir. Bir uzantıyı kaldırarak yeniden çalıştırabilir ve sonra uzantıyı seçtiğiniz bir yürütme yöntemiyle yeniden çalıştırabilirsiniz. Bir uzantıyı kaldırmak için [Remove-Azvmexgerkomutunu](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) aşağıdaki gibi kullanın:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Azure portal bir uzantıyı aşağıdaki gibi da kaldırabilirsiniz:

1. Bir VM seçin.
2. **Uzantılar**' ı seçin.
3. İstediğiniz uzantıyı seçin.
4. **Kaldır**' ı seçin.

## <a name="common-vm-extensions-reference"></a>Ortak VM uzantıları başvurusu
| Uzantı adı | Açıklama | Daha fazla bilgi |
| --- | --- | --- |
| Windows için özel Betik uzantısı |Azure sanal makinesinde betikleri çalıştırma |[Windows için özel Betik uzantısı](custom-script-windows.md) |
| Windows için DSC Uzantısı |PowerShell DSC (Istenen durum yapılandırması) uzantısı |[Windows için DSC Uzantısı](dsc-overview.md) |
| Azure Tanılama Uzantısı |Azure Tanılama Yönet |[Azure Tanılama Uzantısı](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM erişimi uzantısı |Kullanıcıları ve kimlik bilgilerini yönetme |[Linux için VM erişimi uzantısı](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Sonraki adımlar

VM uzantıları hakkında daha fazla bilgi için bkz. [Azure sanal makine uzantılarına ve özelliklerine genel bakış](overview.md).
