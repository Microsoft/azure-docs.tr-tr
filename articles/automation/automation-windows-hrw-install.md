---
title: Azure Otomasyonu 'nda Windows karma runbook çalışanı dağıtma
description: Bu makalede, yerel veri merkezinizdeki veya bulut ortamınızda Windows tabanlı makinelerde runbook 'lar çalıştırmak için kullanabileceğiniz bir karma Runbook Worker dağıtımı açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4bf27ffc888e189f15e1c435309cbeddb1c11fec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830349"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows karma runbook çalışanı dağıtma

Azure 'da [çalışan sunucular da](../azure-arc/servers/overview.md)dahil olmak üzere runbook 'Ları doğrudan Azure 'Da veya Azure dışı bir makinede çalıştırmak Için Azure Otomasyonu 'Nun Kullanıcı karma Runbook Worker özelliğini kullanabilirsiniz. Rolü barındıran makineden veya sunucudan, bu yerel kaynakları yönetmek için Runbook 'ları doğrudan buna karşı ve ortamdaki kaynaklara karşı çalıştırabilirsiniz.

Azure Otomasyonu runbook 'ları depolar ve yönetir ve ardından bunları bir veya daha fazla belirlenen makineye gönderir. Bu makalede bir Windows makinesinde Kullanıcı karma Runbook Worker dağıtımı, çalışan kaldırma ve karma Runbook Worker grubunun nasıl kaldırılacağı açıklanır.

Runbook Worker 'ı başarıyla dağıttıktan sonra, runbook 'larınızı şirket içi veri merkezinizde veya diğer bulut ortamınızda otomatikleştirmek üzere nasıl yapılandıracağınızı öğrenmek için [karma Runbook Worker 'daki runbook 'Ları Çalıştır](automation-hrw-run-runbooks.md) ' ı inceleyin.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdakilere sahip olduğunuzdan emin olun.

### <a name="a-log-analytics-workspace"></a>Log Analytics çalışma alanı

Karma Runbook Worker rolü, rolü yüklemek ve yapılandırmak için bir Azure Izleyici Log Analytics çalışma alanına bağlıdır. [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)aracılığıyla veya [Azure Portal](../azure-monitor/logs/quick-create-workspace.md)aracılığıyla oluşturabilirsiniz.

Azure Izleyici Log Analytics çalışma alanınız yoksa, çalışma alanını oluşturmadan önce [Azure Izleyici günlüğü tasarım kılavuzunu](../azure-monitor/logs/design-logs-deployment.md) gözden geçirin.

### <a name="log-analytics-agent"></a>Log Analytics aracısı

Karma Runbook Worker rolü, desteklenen Windows işletim sistemi için [Log Analytics aracısına](../azure-monitor/agents/log-analytics-agent.md) gerek duyar. Azure dışında barındırılan sunucular veya makineler için, [Azure Arc etkin sunucularını](../azure-arc/servers/overview.md)kullanarak Log Analytics aracısını yükleyebilirsiniz.

### <a name="supported-windows-operating-system"></a>Desteklenen Windows işletim sistemi

Karma Runbook Worker özelliği aşağıdaki işletim sistemlerini destekler:

* Windows Server 2019 (sunucu çekirdeği dahil)
* Windows Server 2016, sürüm 1709 ve 1803 (sunucu çekirdeği hariç)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (çoklu oturum dahil) ve Pro
* Windows 8 Enterprise ve Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimum gereksinimler

Bir Windows sistemi ve Kullanıcı karma Runbook Worker için en düşük gereksinimler şunlardır:

* Windows PowerShell 5,1 ([WMF 5,1 indirin](https://www.microsoft.com/download/details.aspx?id=54616)). PowerShell Core desteklenmiyor.
* .NET Framework 4.6.2 veya sonraki sürümü
* İki çekirdek
* 4 GB RAM
* Bağlantı noktası 443 (giden)

### <a name="network-configuration"></a>Ağ yapılandırması

Karma Runbook Worker ağ gereksinimleri için bkz. [ağınızı yapılandırma](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Karma Runbook Worker grubuna makine ekleme

Çalışan makineyi, Otomasyon hesaplarınızdan birindeki bir karma Runbook Worker grubuna ekleyebilirsiniz. Güncelleştirme Yönetimi tarafından yönetilen sistem karma Runbook Worker 'ı barındıran makinelerde, karma Runbook Worker grubuna eklenebilirler. Ancak hem Güncelleştirme Yönetimi hem de karma runbook çalışanı grup üyeliği için aynı Otomasyon hesabını kullanmanız gerekir.

>[!NOTE]
>Azure Otomasyonu [güncelleştirme yönetimi](./update-management/overview.md) , sistem karma runbook çalışanını güncelleştirme yönetimi için etkinleştirilmiş bir Azure veya Azure dışı makineye otomatik olarak yüklüyor. Ancak, bu çalışan Otomasyon hesabınızdaki herhangi bir karma Runbook Worker grubuna kayıtlı değildir. Runbook 'larınızı bu makinelerde çalıştırmak için, onları karma Runbook Worker grubuna eklemeniz gerekir. Bir gruba eklemek için [el ile dağıtım](#manual-deployment) bölümünde 6. adımı izleyin.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırması ile yönetimi etkinleştirme

Azure Otomasyonu durum yapılandırmasıyla yönetim için makinelerin etkinleştirilmesi hakkında bilgi için bkz. [Azure Otomasyonu durum yapılandırması tarafından yönetim için makineleri etkinleştirme](automation-dsc-onboarding.md).

> [!NOTE]
> Istenen durum yapılandırması (DSC) ile karma Runbook Worker rolünü destekleyen makinelerin yapılandırmasını yönetmek için, makineleri DSC düğümleri olarak eklemeniz gerekir.

## <a name="installation-options"></a>Yükleme seçenekleri

Bir Windows Kullanıcı karma Runbook Worker 'ı yüklemek ve yapılandırmak için aşağıdaki yöntemlerden birini kullanabilirsiniz.

* Bir veya daha fazla Windows makinesini yapılandırma işlemini tamamen [otomatik hale getirmek](#automated-deployment) için bir belirtilen PowerShell betiği kullanın. Bu, veri merkezinizdeki veya başka bir bulut ortamındaki makineler için önerilen yöntemdir.
* Otomasyon çözümünü el ile içeri aktarın, Windows için Log Analytics aracısını yükleyip makinede çalışan rolünü yapılandırın.

## <a name="automated-deployment"></a>Otomatik dağıtım

Karma Runbook Worker 'ı otomatik olarak dağıtmak için iki yöntem vardır. Azure portal runbook galerisinden runbook 'u içeri aktarabilir ve çalıştırabilir ya da PowerShell Galerisi bir betiği el ile indirebilirsiniz.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Runbook galerisinden runbook içeri aktarma

İçeri aktarma yordamı, [Azure Portal GitHub 'dan runbook 'Ları Içeri aktarma](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)bölümünde ayrıntılı olarak açıklanmıştır. İçeri aktarılacak runbook 'un adı **Otomasyon Windows HybridWorker oluştur**.

Runbook aşağıdaki parametreleri kullanır.

| Parametre | Durum | Açıklama |
| ------- | ----- | ----------- |
| `Location` | Zorunlu | Log Analytics çalışma alanının konumu. |
| `ResourceGroupName` | Zorunlu | Otomasyon hesabınızın kaynak grubu. |
| `AccountName` | Zorunlu | Karma çalıştırma çalışanının kaydedileceği Otomasyon hesabı adı. |
| `CreateLA` | Zorunlu | True ise, `WorkspaceName` bir Log Analytics çalışma alanı oluşturmak için değerini kullanır. False ise, değeri `WorkspaceName` var olan bir çalışma alanına başvurmalıdır. |
| `LAlocation` | İsteğe Bağlı | Log Analytics çalışma alanının oluşturulacağı konum veya zaten var. |
| `WorkspaceName` | İsteğe Bağlı | Kullanılacak Log Analytics çalışma alanının adı. |
| `CreateVM` | Zorunlu | True ise, `VMName` Yeni BIR VM 'nin adı olarak değerini kullanın. Yanlışsa, `VMName` mevcut VM 'yi bulmak ve kaydetmek için kullanın. |
| `VMName` | İsteğe Bağlı | Değerine bağlı olarak, oluşturulan veya kaydedilen sanal makinenin adı `CreateVM` . |
| `VMImage` | İsteğe Bağlı | Oluşturulacak VM görüntüsünün adı. |
| `VMlocation` | İsteğe Bağlı | Oluşturulan veya kaydedilen sanal makinenin konumu. Bu konum belirtilmemişse, değeri `LAlocation` kullanılır. |
| `RegisterHW` | Zorunlu | Doğru ise, VM 'yi karma çalışan olarak kaydedin. |
| `WorkerGroupName` | Zorunlu | Karma çalışanı grubunun adı. |

### <a name="download-a-script-from-the-powershell-gallery"></a>PowerShell Galerisi bir betiği indirin

Bu otomatik dağıtım yöntemi, Windows karma Runbook Worker rolünü otomatikleştirmek ve yapılandırmak için PowerShell betiği **New-OnPremiseHybridWorker.ps1** kullanır. Şunları gerçekleştirir:

* Gerekli modülleri yükleme
* Azure hesabınızla oturum açar
* Belirtilen kaynak grubu ve otomasyon hesabının varlığını doğrular
* Otomasyon hesabı özniteliklerine başvurular oluşturur
* Belirtilmemişse bir Azure Izleyici Log Analytics çalışma alanı oluşturur
* Çalışma alanında Azure Otomasyonu çözümünü etkinleştirme
* Windows için Log Analytics aracısını indirme ve yükleme
* Makineyi karma Runbook Worker olarak kaydet

Windows makinenize betiği kullanarak rolü yüklemek için aşağıdaki adımları gerçekleştirin.

1. **New-OnPremiseHybridWorker.ps1** betiğini [PowerShell Galerisi](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)indirin. Betiği indirdikten sonra hedef makinede kopyalayın veya çalıştırın. Betik aşağıdaki parametreleri kullanır.

    | Parametre | Durum | Açıklama |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Zorunlu | Otomasyon hesabınızla ilişkili kaynak grubunun adı. |
    | `AutomationAccountName` | Zorunlu | Otomasyon hesabınızın adı.
    | `Credential` | İsteğe Bağlı | Azure ortamında oturum açarken kullanılacak kimlik bilgileri. |
    | `HybridGroupName` | Zorunlu | Bu senaryoyu destekleyen runbook 'lar için hedef olarak belirttiğiniz karma Runbook Worker grubunun adı. |
    | `OMSResourceGroupName` | İsteğe Bağlı | Log Analytics çalışma alanı için kaynak grubunun adı. Bu kaynak grubu belirtilmemişse, değeri `AAResourceGroupName` kullanılır. |
    | `SubscriptionID` | Zorunlu | Otomasyon hesabınızla ilişkili Azure aboneliğinin tanıtıcısı. |
    | `TenantID` | İsteğe Bağlı | Otomasyon hesabınızla ilişkili kiracı kuruluşunun tanıtıcısı. |
    | `WorkspaceName` | İsteğe Bağlı | Log Analytics çalışma alanı adı. Log Analytics çalışma alanınız yoksa, betik bir tane oluşturur ve yapılandırır. |

1. Yükseltilmiş bir 64 bit PowerShell komut istemi açın.

1. PowerShell komut isteminde, indirdiğiniz betiği içeren klasöre gidin. ,,,, Ve parametreleri için değerleri değiştirin `AutomationAccountName` `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` `WorkspaceName` . Sonra betiği çalıştırın.

    Betiği çalıştırdıktan sonra Azure ile kimlik doğrulaması yapmanız istenir. Abonelik **yöneticileri** rolünün üyesi ve aboneliğin ortak Yöneticisi olan bir hesapla oturum açmalısınız.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

1. NuGet 'i yüklemek ve Azure kimlik bilgilerinizle kimlik doğrulaması yapmak için kabul etmeniz istenir. En son NuGet sürümüne sahip değilseniz, [mevcut NuGet dağıtım sürümlerinden](https://www.nuget.org/downloads)indirebilirsiniz.

1. Betiği tamamladıktan sonra dağıtımı doğrulayın. Otomasyon hesabınızdaki **karma runbook çalışanı grupları** sayfasında, **Kullanıcı karma runbook çalışanları grubu** sekmesinde, yeni grup ve üye sayısını gösterir. Mevcut bir gruptur, üye sayısı artırılır. Sayfadaki listeden grubu seçebilirsiniz, sol taraftaki menüden **hibrit çalışanlar** ' ı seçin. **Karma çalışanlar** sayfasında, grubun her bir üyesini listelendiğini görebilirsiniz.

## <a name="manual-deployment"></a>El ile dağıtım

Bir Windows karma runbook çalışanı yüklemek ve yapılandırmak için aşağıdaki adımları gerçekleştirin.

1. Yükseltilmiş bir PowerShell komut isteminde veya [Azure portal](https://portal.azure.com)Cloud Shell aşağıdaki komutu çalıştırarak Log Analytics çalışma alanınızda Azure Otomasyonu çözümünü etkinleştirin.

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. Log Analytics aracısını hedef makineye dağıtın.

    * Azure VM 'Leri için [Windows için sanal makine uzantısı](../virtual-machines/extensions/oms-windows.md)' nı kullanarak windows için Log Analytics aracısını yüklersiniz. Uzantı Log Analytics aracısını Azure sanal makinelerine yükleyip sanal makineleri mevcut bir Log Analytics çalışma alanına kaydeder. [ *Linux* veya *Windows* VM 'leri için dağıtım Log Analytics aracısını](../governance/policy/samples/built-in-policies.md#monitoring) atamak üzere bir Azure Resource Manager şablonu, PowerShell veya Azure İlkesi kullanabilirsiniz. Aracı yüklendikten sonra, makine Otomasyon hesabınızdaki bir karma Runbook Worker grubuna eklenebilir.
    
    * Azure dışı makineler için, [Azure Arc etkin sunucularını](../azure-arc/servers/overview.md)kullanarak Log Analytics aracısını yükleyebilirsiniz. Yay özellikli sunucular, aşağıdaki yöntemleri kullanarak Log Analytics aracısının dağıtılmasını destekler:
    
        - VM uzantıları çerçevesini kullanma.
        
            Azure Arc etkin sunucuları 'ndaki bu özellik, Azure olmayan bir Windows ve/veya Linux sunucusuna Log Analytics Agent VM uzantısını dağıtmanıza olanak tanır. SANAL makine uzantıları, karma makinelerinizde veya Arc etkin sunucularla yönetilen sunucularda aşağıdaki yöntemler kullanılarak yönetilebilir:
        
            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Kaynak Yöneticisi şablonları](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Azure Ilkesi 'ni kullanma.
        
            Bu yaklaşımı kullanarak, Arc etkin sunucusunda Log Analytics aracısının yüklü olup olmadığını denetlemek için Azure Ilke [dağıtma Log Analytics aracısını Linux veya Windows Azure Arc makineler](../governance/policy/samples/built-in-policies.md#monitoring) yerleşik ilkesine kullanırsınız. Aracı yüklü değilse, bir düzeltme görevi kullanarak otomatik olarak dağıtır. Alternatif olarak, makineleri VM'ler için Azure İzleyici ile izlemeyi planlıyorsanız, bunun yerine Log Analytics aracısını yüklemek ve yapılandırmak için [VM'ler için Azure izleyici etkinleştir](../governance/policy/samples/built-in-initiatives.md#monitoring) girişim kullanın.

    Azure Ilkesini kullanarak Windows veya Linux için Log Analytics Aracısı yüklemenizi öneririz.

1. Aracının çalışma alanına rapor ettiğini doğrulayın

    Windows için Log Analytics Aracısı, makineleri bir Azure Izleyici Log Analytics çalışma alanına bağlar. Aracıyı makinenize yükleyip çalışma alanınıza bağladığınızda, karma Runbook Worker için gereken bileşenleri otomatik olarak indirir.

    Aracı birkaç dakika sonra Log Analytics çalışma alanınıza başarıyla bağlandığında, çalışma alanına sinyal verisi gönderdiğini doğrulamak için aşağıdaki sorguyu çalıştırabilirsiniz.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Arama sonuçlarında, makineye bağlı olduğunu ve hizmete raporlanmasını belirten, makinenin sinyal kayıtlarını görmeniz gerekir. Varsayılan olarak, her aracı atanmış çalışma alanına bir sinyal kaydını iletir. Aracı yüklemesini ve kurulumunu gerçekleştirmek için aşağıdaki adımları kullanın.

1. Log Analytics aracısını barındıran makinede hibrit Runbook Worker sürümünü onaylayın, `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` **Sürüm** alt klasörüne göz atarak göz atabilirsiniz. Bu klasör, çalışma alanında çözüm etkinleştirildikten sonra makinede birkaç dakika sonra görüntülenir.

1. Runbook ortamını yükleyip Azure Otomasyonu ' na bağlanın. Bir aracıyı bir Log Analytics çalışma alanına rapor vermek ve **Otomasyon** çözümünü içeri aktarmak üzere yapılandırdığınızda, çözüm `HybridRegistration` PowerShell modülünü aşağı iter. Bu modül `Add-HybridRunbookWorker` cmdlet 'ini içerir. Runbook ortamını makineye yüklemek ve Azure Otomasyonu 'na kaydetmek için bu cmdlet 'i kullanın.

    Yönetici modunda bir PowerShell oturumu açın ve aşağıdaki komutları çalıştırarak modülü içeri aktarın.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. , `Add-HybridRunbookWorker` Ve parametrelerinin değerlerini belirten cmdlet 'ini çalıştırın `Url` `Key` `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Parametreler için gereken bilgileri `Url` ve `Key` Otomasyon hesabınızdaki **anahtarlar** sayfasını alabilirsiniz. Sayfanın sol tarafındaki **Hesap ayarları** bölümünün altında bulunan **anahtarlar** ' ı seçin.

    ![Anahtarları Yönet sayfası](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Parametresi için `Url` , **URL** değerini kopyalayın.

    * Parametresi için `Key` , **BIRINCIL erişim anahtarı** değerini kopyalayın.

    * Parametresi için `GroupName` karma Runbook Worker grubunun adını kullanın. Bu grup Otomasyon hesabında zaten mevcutsa, geçerli makine buna eklenir. Bu grup yoksa, eklenir.

    * Gerekirse, `Verbose` yükleme hakkındaki ayrıntıları almak için parametresini ayarlayın.

1. Komut tamamlandıktan sonra dağıtımı doğrulayın. Otomasyon hesabınızdaki **karma runbook çalışanı grupları** sayfasında, **Kullanıcı karma runbook çalışanları grubu** sekmesinde, yeni veya mevcut grup ve üye sayısını gösterir. Mevcut bir gruptur, üye sayısı artırılır. Sayfadaki listeden grubu seçebilirsiniz, sol taraftaki menüden **hibrit çalışanlar**' ı seçin. **Karma çalışanlar** sayfasında, grubun her bir üyesini listelendiğini görebilirsiniz.

## <a name="install-powershell-modules"></a>PowerShell modüllerini yükler

Runbook 'lar, Azure Otomasyonu ortamınızda yüklü olan modüllerde tanımlanan etkinlikleri ve cmdlet 'leri kullanabilir. Bu modüller şirket içi makinelere otomatik olarak dağıtılmadığından, el ile kurmanız gerekir. Özel durum Azure modülüdür. Bu modül varsayılan olarak yüklenir ve Azure Otomasyonu için tüm Azure hizmetleri ve etkinlikleri için cmdlet 'lere erişim sağlar.

Karma Runbook Worker 'ın birincil amacı yerel kaynakları yönettiğinden, büyük olasılıkla bu kaynakları destekleyen modülleri, özellikle modülünü yüklemeniz gerekir `PowerShellGet` . Windows PowerShell modüllerini yükleme hakkında bilgi için bkz. [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Yüklenen modüller, `PSModulePath` karma çalışanın otomatik olarak içe aktarabilmesi için ortam değişkeni tarafından başvurulan bir konumda olmalıdır. Daha fazla bilgi için bkz. [PSModulePath Içinde modül yüklemeleri](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Karma runbook çalışanını kaldırma

1. Azure portal Otomasyon hesabınıza gidin.

1. **Hesap ayarları** altında **anahtarlar** ' ı seçin ve **URL** ve **birincil erişim anahtarı** değerlerini aklınızda yapın.

1. Yönetici modunda bir PowerShell oturumu açın ve URL 'niz ile birincil erişim anahtarı değerlerinizle aşağıdaki komutu çalıştırın. `Verbose`Kaldırma işleminin ayrıntılı günlüğü için parametresini kullanın. Eski makineleri karma çalışanı grubundan kaldırmak için isteğe bağlı `machineName` parametresini kullanın.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Karma Çalışanı grubunu kaldırma

Karma runbook çalışanı grubunu kaldırmak için, önce karma runbook çalışanını grubun üyesi olan her makineden kaldırmanız gerekir. Sonra, grubu kaldırmak için aşağıdaki adımları kullanın:

1. Azure portal Otomasyon hesabını açın.

1. **Işlem Otomasyonu** altında **karma çalışan grupları** ' nı seçin. Silmek istediğiniz grubu seçin. Bu grubun Özellikler sayfası görüntülenir.

   ![Özellikler sayfası](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Seçili grubun Özellikler sayfasında **Sil**' i seçin. Bu eylemi onaylamanızı isteyen bir ileti. Devam etmek istediğinizden emin değilseniz **Evet** ' i seçin.

   ![Onay iletisi](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Bu işlemin tamamlanması birkaç saniye sürebilir. Bu işlemin ilerleme durumunu menüdeki **Bildirimler**’in altından izleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Runbook 'larınızı şirket içi veri merkezinizde veya diğer bulut ortamınızda otomatik hale getirmek üzere nasıl yapılandıracağınızı öğrenmek için bkz. [runbook 'Ları karma Runbook Worker üzerinde çalıştırma](automation-hrw-run-runbooks.md).

* Karma runbook çalışanlarınızın sorunlarını giderme hakkında bilgi edinmek için bkz. [karma Runbook Worker sorunlarını giderme](troubleshoot/hybrid-runbook-worker.md#general).
