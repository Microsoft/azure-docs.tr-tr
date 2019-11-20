---
title: Azure Otomasyonu ile sunucuları istenen duruma göre yapılandırma ve kaymaları yönetme
description: Öğretici-Azure Otomasyonu durum yapılandırması ile sunucu yapılandırmalarını yönetme
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: b44bcf7edeaad07fbe0b3093ba3c7100cb0c24c4
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432070"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Sunucuları istenen bir duruma göre yapılandırma ve DRFT 'yi yönetme

Azure Otomasyonu durum yapılandırması sunucularınız için yapılandırmalar belirtmenize ve bu sunucuların zaman içinde belirtilen durumda olduğundan emin olmanızı sağlar.

> [!div class="checklist"]
> - Azure Automation DSC tarafından yönetilecek bir VM ekleme
> - Azure Otomasyonu 'na bir yapılandırma yükleme
> - Düğüm yapılandırmasında yapılandırma derleme
> - Yönetilen düğüme düğüm yapılandırması atama
> - Yönetilen bir düğümün uyumluluk durumunu denetleme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

- Azure Otomasyonu hesabı. Bir Azure Otomasyonu Garklı Çalıştır hesabı oluşturma yönergeleri için bkz. [Azure Farklı Çalıştır Hesabı](automation-sec-configure-azure-runas-account.md).
- Windows Server 2008 R2 veya üstünü çalıştıran bir Azure Resource Manager VM (klasik değil). VM oluşturma yönergeleri için bkz. [Azure portalında ilk Windows sanal makinenizi oluşturma](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell modülü 3,6 veya sonraki bir sürümü. Sürümü bulmak için `Get-Module -ListAvailable AzureRM` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/azurerm/install-azurerm-ps).
- Istenen durum yapılandırması (DSC) ile benzerlik. DSC hakkında daha fazla bilgi için bkz. [Windows PowerShell Istenen durum yapılandırmasına genel bakış](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Azure'da oturum açma

`Connect-AzureRmAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Azure Otomasyonu 'na bir yapılandırma oluşturma ve yükleme

Bu öğreticide, IIS 'nin VM 'de yüklü olmasını sağlayan basit bir DSC yapılandırması kullanacağız.

DSC yapılandırmaları hakkında bilgi edinmek için bkz. [DSC yapılandırmaları](/powershell/scripting/dsc/configurations/configurations).

Bir metin düzenleyicisine aşağıdakileri yazıp `TestConfig.ps1` adıyla yerel ortamda kaydedin.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> DSC kaynaklarını sağlayan birden çok modülün içeri aktarılmasını gerektiren daha Gelişmiş senaryolarda, her modülün yapılandırmanızda benzersiz bir `Import-DscResource` satırı olduğundan emin olun.

Yapılandırmayı Otomasyon hesabınıza yüklemek için `Import-AzureRmAutomationDscConfiguration` cmdlet 'ini çağırın:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Düğüm yapılandırmasında yapılandırma derleme

Bir DSC yapılandırmasının bir düğüme atanabilmesi için önce düğüm yapılandırmasına derlenmesi gerekir.

Yapılandırmaların derlenmesi hakkında daha fazla bilgi için bkz. [DSC yapılandırması](/powershell/scripting/dsc/configurations/configurations).

@No__t-1 yapılandırmasını bir düğüm yapılandırmasına derlemek için `Start-AzureRmAutomationDscCompilationJob` cmdlet 'ini çağırın:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Bu, Otomasyon hesabınızda `TestConfig.WebServer` adlı bir düğüm yapılandırması oluşturur.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Bir VM 'yi durum yapılandırması tarafından yönetilecek şekilde kaydetme

Azure Otomasyonu durum yapılandırması 'nı kullanarak Azure VM 'lerini (hem klasik hem de Kaynak Yöneticisi), şirket içi VM 'Lere, Linux makinelere, AWS VM 'lerine ve şirket içi fiziksel makinelere yönetebilirsiniz. Bu konu başlığında yalnızca Azure Resource Manager VM 'lerinin nasıl kaydedileceği ele alınmaktadır. Diğer makine türlerini kaydetme hakkında daha fazla bilgi için bkz. [Azure Otomasyonu durum yapılandırmasına göre yönetim için makine ekleme](automation-dsc-onboarding.md).

VM 'nizi Azure Otomasyonu durum yapılandırması ile kaydetmek için `Register-AzureRmAutomationDscNode` cmdlet 'ini çağırın.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Bu, belirtilen VM 'yi durum yapılandırması 'nda yönetilen bir düğüm olarak kaydeder.

### <a name="specify-configuration-mode-settings"></a>Yapılandırma modu ayarlarını belirtin

Bir VM 'yi yönetilen düğüm olarak kaydettiğinizde, yapılandırmanın özelliklerini de belirtebilirsiniz. Örneğin, makinenin durumunun yalnızca bir kez uygulanacağını belirtebilirsiniz (DSC, ilk denetim sonrasında yapılandırmayı uygulamayı denemez) **Configurationmode** özelliğinin değeri olarak `ApplyOnly` belirterek:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Ayrıca, DSC 'nin yapılandırma durumunu **Configurationmodefkarşılandığından** bir özelliği kullanarak ne sıklıkta denetleyeceğini belirtebilirsiniz:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Yönetilen bir düğümün yapılandırma özelliklerini ayarlama hakkında daha fazla bilgi için bkz. [register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

DSC yapılandırma ayarları hakkında daha fazla bilgi için bkz. [yerel Configuration Manager yapılandırma](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Yönetilen düğüme düğüm yapılandırması atama

Artık derlenen düğüm yapılandırmasını yapılandırmak istediğimiz sanal makineye atayabiliriz.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Bu, `TestConfig.WebServer` adlı düğüm yapılandırmasını `DscVm` adlı kayıtlı DSC düğümüne atar.
Varsayılan olarak, DSC düğümü her 30 dakikada bir düğüm yapılandırmasıyla uyumluluk için denetlenir.
Uyumluluk denetimi aralığını değiştirme hakkında daha fazla bilgi için bkz. [yerel Configuration Manager yapılandırma](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Kısmi yapılandırmalara göre çalışma

Azure Otomasyonu durum yapılandırması, [kısmi yapılandırmaların](/powershell/dsc/pull-server/partialconfigs)kullanımını destekler.
Bu senaryoda DSC, birden fazla yapılandırmayı bağımsız olarak yönetmek üzere yapılandırılmıştır ve her Yapılandırma Azure Otomasyonu 'ndan alınır.
Ancak, Otomasyon hesabı başına bir düğüme yalnızca bir yapılandırma atanabilir.
Bu, bir düğüm için iki yapılandırma kullanıyorsanız iki Otomasyon hesabı zorunlu kılmasıdır.

Çekme hizmetinden kısmi bir yapılandırmanın nasıl kaydedileceği hakkındaki ayrıntılar için, [kısmi yapılandırmalar](https://docs.microsoft.com/powershell/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)belgelerine bakın.

Yapılandırma olarak Configuration 'ı kullanarak sunucuları yönetmek için ekiplerin birlikte nasıl çalıştığı hakkında daha fazla bilgi için bkz. [BIR CI/CD işlem HATTıNDA DSC 'nin rolünü anlama](/powershell/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Yönetilen bir düğümün uyumluluk durumunu denetleme

@No__t-0 cmdlet 'ini çağırarak yönetilen bir düğümün uyumluluk durumu hakkında raporlar alabilirsiniz:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Düğümler hizmetten kaldırılıyor

Azure Otomasyonu durum yapılandırmasına bir düğüm eklediğinizde yerel Configuration Manager ayarları, makineyi yapılandırmak için hizmet ve çekme yapılandırmalarına ve gerekli modüllere kaydolmak üzere ayarlanır.
Düğümü hizmetten kaldırmayı seçerseniz, Azure portal ya da az cmdlet 'lerini kullanarak bunu yapabilirsiniz.

> [!NOTE]
> Bir düğümün hizmetten kaydı, düğümün artık hizmete bağlanmaması için yalnızca yerel Configuration Manager ayarlarını ayarlar.
> Bu, düğüme uygulanmış olan yapılandırmayı etkilemez.
> Geçerli yapılandırmayı kaldırmak için [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) 'i kullanın veya yerel yapılandırma dosyasını silin (Bu, Linux düğümlerine yönelik tek seçenektir).

### <a name="azure-portal"></a>Azure portalı

Azure Otomasyonu ' ndan içindekiler tablosunda **Durum Yapılandırması (DSC)** seçeneğine tıklayın.
Ardından, hizmete kayıtlı düğümlerin listesini görüntülemek için **düğümler** ' e tıklayın.
Kaldırmak istediğiniz düğümün adına tıklayın.
Açılan düğüm görünümünde, **kayıt Sil**' e tıklayın.

### <a name="powershell"></a>PowerShell

PowerShell kullanarak Azure Otomasyonu durum yapılandırma hizmeti 'nden bir düğümün kaydını silmek için, [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0)cmdlet 'inin belgelerini izleyin.

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [Azure Otomasyonu durum yapılandırması ile çalışmaya](automation-dsc-getting-started.md) başlama
- Düğümlerin nasıl ekleneceğini öğrenmek için bkz. [Azure Otomasyonu durum yapılandırmasına göre yönetim için makineleri ekleme](automation-dsc-onboarding.md)
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum yapılandırmasında yapılandırmaları derleme](automation-dsc-compile.md)
- PowerShell cmdlet başvurusu için bkz. [Azure Otomasyonu durum yapılandırması cmdlet 'leri](/powershell/module/azurerm.automation/#automation)
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/)
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmaya ilişkin bir örnek görmek için bkz. [Azure Otomasyonu durum yapılandırması ve Chocolatey kullanarak sürekli dağıtım](automation-dsc-cd-chocolatey.md)
