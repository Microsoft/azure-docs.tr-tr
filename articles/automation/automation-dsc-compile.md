---
title: Azure Otomasyonu durum yapılandırmasında yapılandırmaları derleme
description: Bu makalede, Azure Otomasyonu için Istenen durum yapılandırması (DSC) yapılandırmalarının nasıl derleneceği açıklanır.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3d9c6c9b73f8887d4fdb85da277b2e27d8f5221c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243562"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırmasında DSC yapılandırmalarını derleme

Istenen durum yapılandırması (DSC) yapılandırmalarını Azure Otomasyonu durum yapılandırması: Azure 'da ve Windows PowerShell 'de iki şekilde derleyebilirsiniz. Aşağıdaki tablo, her birinin özelliklerine göre hangi yöntemin kullanılacağını belirlemenize yardımcı olur:

- Azure Durum Yapılandırması derleme hizmeti
  - Etkileşimli kullanıcı arabirimiyle başlangıç yöntemi
   - İş durumunu kolayca izleyin

- Windows PowerShell
  - Yerel iş istasyonunda veya derleme hizmetinde Windows PowerShell 'den çağrı
  - Geliştirme testi işlem hattı ile tümleştirme
  - Karmaşık parametre değerlerini sağlayın
  - Ölçekteki node ve Node olmayan verilerle çalışma
  - Önemli performans iyileştirmesi

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Azure durum yapılandırmasında DSC yapılandırması derleme

### <a name="portal"></a>Portal

1. Otomasyon hesabınızdan **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Yapılandırmalar** sekmesine tıklayın ve ardından derlemek için yapılandırma adına tıklayın.
1. **Derle**' ye tıklayın.
1. Yapılandırmada parametre yoksa, derlemek isteyip istemediğinizi onaylamanız istenir. Yapılandırmanın parametreleri varsa, parametre değerlerini sağlayabilmeniz için **derleme yapılandırma** dikey penceresi açılır. Parametrelerle ilgili daha fazla ayrıntı için aşağıdaki [**temel parametreler**](#basic-parameters) bölümüne bakın.
1. Derleme **işi sayfası,** derleme işinin durumunu ve Azure Otomasyonu durum yapılandırması çekme sunucusuna yerleştirilmesine neden olan düğüm YAPıLANDıRMALARıNı (MOF yapılandırma belgeleri) izleyebilmeniz için açılır.

### <a name="azure-powershell"></a>Azure PowerShell

Windows PowerShell ile derlemeye başlamak için [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) kullanabilirsiniz. Aşağıdaki örnek kod **SampleConfig**ADLı bir DSC yapılandırmasının derlemesini başlatır.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob`, durumunu izlemek için kullanabileceğiniz bir derleme işi nesnesi döndürür. Daha sonra bu derleme iş nesnesini [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) ile kullanabilirsiniz
derleme işinin durumunu ve [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) ' i belirleme
akışlarını görüntülemek için (çıkış). Aşağıdaki örnek kod **SampleConfig** yapılandırmasının derlemesini başlatır, tamamlanana kadar bekler ve ardından akışlarını görüntüler.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Temel parametreler

Parametre türleri ve özellikler dahil olmak üzere DSC yapılandırmalarında parametre bildirimi, Azure Otomasyonu runbook 'larıyla aynı şekilde çalışmaktadır. Runbook parametreleri hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu 'nda runbook başlatma](automation-starting-a-runbook.md) .

Aşağıdaki örnek, **Parametersexsample. örnek** düğüm yapılandırmasındaki derleme sırasında oluşturulan özelliklerin değerlerini belirleyebilmek için **featurename** ve **ısun**adlı iki parametreyi kullanır.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Azure Otomasyonu durum yapılandırması portalında veya Azure PowerShell ile temel parametreleri kullanan DSC yapılandırmalarını derleyebilirsiniz:

#### <a name="portal"></a>Portal

Portalda, **Derle**' ye tıkladıktan sonra parametre değerleri girebilirsiniz.

![Yapılandırma derleme parametreleri](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell, anahtarın parametre adıyla eşleştiği bir [Hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 'da parametreler gerektirir ve değer parametre değerine eşittir.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials parametresi olarak geçirme hakkında daha fazla bilgi için, aşağıdaki [kimlik bilgileri varlıkları](#credential-assets) bölümüne bakın.

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Azure Otomasyonu 'nda bileşik kaynakları içeren yapılandırmaların derlenmesi

**Bileşik kaynaklar** , DSC yapılandırmalarını bir yapılandırma içinde iç içe geçmiş kaynaklar olarak kullanmanıza olanak tanır. Bu, tek bir kaynağa birden çok yapılandırma uygulamanıza olanak sağlar. Bileşik **kaynaklar hakkında**daha fazla bilgi edinmek için bkz. [bileşik kaynaklar: kaynak olarak DSC yapılandırması kullanma](/powershell/scripting/dsc/resources/authoringresourcecomposite) .

> [!NOTE]
> **Bileşik kaynakları** içeren yapılandırmaların doğru bir şekilde derlenmesi için önce, ilk olarak kompozit 'nin bağımlı olduğu tüm DSC kaynaklarının Azure Automation 'a içeri aktarıldığından emin olmanız gerekir.

DSC **bileşik kaynağı** eklemek, Azure Otomasyonu 'na herhangi bir PowerShell modülü eklemekten farklı değildir.
Bu süreç için adım adım yönergeler, [Azure Automation 'Da modülleri yönetme](/azure/automation/shared-resources/modules)makalesinde belgelenmiştir.

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Azure Otomasyonu 'nda yapılandırma derlenirken ConfigurationData yönetimi

**ConfigurationData** , PowerShell DSC 'yi kullanırken herhangi bir ortama özgü yapılandırmadan yapısal yapılandırmayı ayırmanızı sağlar. **ConfigurationData**hakkında daha fazla bilgi edinmek için bkz. [PowerShell DSC 'de "Where" den "Where"](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) öğesinin ayrılması.

> [!NOTE]
> Azure Otomasyonu durum yapılandırmasında derlenirken, Azure PowerShell kullanarak **configurationData** kullanabilirsiniz, ancak Azure Portal.

Aşağıdaki örnek DSC yapılandırması, **$ConfigurationData** ve **$AllNodes** anahtar kelimeleri aracılığıyla **configurationData** kullanır. Bu örnek için [ **xWebAdministration** modülüne](https://www.powershellgallery.com/packages/xWebAdministration/) da ihtiyacınız vardır:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Önceki DSC yapılandırmasını Windows PowerShell ile derleyebilirsiniz. Aşağıdaki betik, Azure Otomasyonu durum yapılandırması çekme hizmeti 'ne iki düğüm yapılandırması ekler: **Configurationdatasample. MyVM1** ve **Configurationdatasample. MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Derleme sırasında Azure Otomasyonu 'nda varlıklarla çalışma

Varlık başvuruları, Azure Otomasyonu durum yapılandırması ve Runbook 'larında aynıdır. Daha fazla bilgi için aşağıdakilere bakın:

- [Sertifikalar](automation-certificates.md)
- [Bağlantılar](automation-connections.md)
- [Kimlik Bilgileri](automation-credentials.md)
- [Değişkenler](automation-variables.md)

#### <a name="credential-assets"></a>Kimlik bilgisi varlıkları

Azure Otomasyonu 'ndaki DSC yapılandırması, `Get-AutomationPSCredential` cmdlet 'ini kullanarak Otomasyon kimlik bilgileri varlıklarına başvurabilir. Bir yapılandırma bir **PSCredential** türüne sahip bir parametreye sahipse, kimlik bilgisini almak Için bir Azure Otomasyonu kimlik bilgisi varlığının dize adını cmdlet 'ine geçirerek `Get-AutomationPSCredential` cmdlet 'ini kullanabilirsiniz. Daha sonra, **PSCredential** nesnesini gerektiren parametre için bu nesneyi kullanabilirsiniz. Arka planda, bu adı taşıyan Azure Otomasyonu kimlik bilgisi varlığı alınır ve yapılandırmaya geçirilir. Aşağıdaki örnekte bu işlem sırasında gösterilmektedir.

Düğüm yapılandırmalarında (MOF yapılandırma belgeleri) kimlik bilgilerinin güvenli tutulması, düğüm yapılandırması MOF dosyasındaki kimlik bilgilerinin şifrelenmesini gerektirir. Ancak, şu anda PowerShell DSC 'nin, düğüm yapılandırması MOF oluşturma sırasında düz metin olarak Output, bu nedenle PowerShell DSC 'nin, oluşturma sonrasında MOF dosyasının tamamını şifreleyeceğinizi bilmediği için, PowerShell DSC 'ye söylemeniz gerekir. bir derleme işi aracılığıyla.

PowerShell DSC 'ye, kimlik bilgilerinin oluşturulan düğüm yapılandırması MOF 'lar yapılandırma verileri kullanılarak düz metin olarak outputolduğunu söyleyebilirsiniz. DSC yapılandırmasında görüntülenen ve kimlik bilgilerini kullanan her bir düğüm bloğunun adı için **configurationData** aracılığıyla `PSDscAllowPlainTextPassword = $true` geçirmeniz gerekir.

Aşağıdaki örnek, bir Otomasyon kimlik bilgisi varlığı kullanan bir DSC yapılandırmasını gösterir.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Önceki DSC yapılandırmasını PowerShell ile derleyebilirsiniz. Aşağıdaki PowerShell Azure Otomasyonu durum yapılandırması çekme sunucusuna iki düğüm yapılandırması ekler: **Credentialsample. MyVM1** ve **Credentialsample. MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Derleme tamamlandığında, ' Microsoft. PowerShell. Management ' eklentisi **zaten içeri aktarıldığından ' Microsoft. PowerShell. Management ' modülünün içeri aktarılmadığı** için bir hata alabilirsiniz. Bu uyarı güvenle yoksayılabilir.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Windows PowerShell 'de yapılandırma derleme ve Azure Otomasyonu 'nda yayımlama

Ayrıca, Azure dışında derlenen düğüm yapılandırmalarının (MOF 'lar) içeri aktarabilirsiniz.
Bu, bir geliştirici iş istasyonundan veya [Azure DevOps](https://dev.azure.com)gibi bir hizmette derleme içerir.
Bu yaklaşımın performansı ve güvenilirliği dahil birden çok avantajı vardır.
Windows PowerShell 'de derlemek, yapılandırma içeriğini imzalama seçeneğini de sağlar.
İmzalı bir düğüm yapılandırması, DSC Aracısı tarafından yönetilen bir düğümde yerel olarak doğrulanır, bu da düğüme uygulanan yapılandırmanın yetkili bir kaynaktan geldiğinden emin olur.

> [!NOTE]
> Düğüm yapılandırma dosyası, Azure Otomasyonu 'na aktarılmaya izin vermek için 1 MB 'den büyük olmamalıdır.

Düğüm yapılandırmalarını imzalama hakkında daha fazla bilgi için bkz. [WMF 5,1 'de geliştirmeler-yapılandırma ve modülün nasıl imzalanıp](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Windows PowerShell 'de yapılandırma derleme

Windows PowerShell 'de DSC yapılandırmalarını derleme işlemi, PowerShell DSC belgelerine [bir yapılandırma yazma, derleme ve uygulama](/powershell/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)işlemlerini içerir.
Bu, bir geliştirici iş istasyonundan veya [Azure DevOps](https://dev.azure.com)gibi bir yapı hizmeti içinden yürütülebilir.

Yapılandırma derlenerek oluşturulan MOF dosyası veya dosyalar daha sonra doğrudan Azure durum yapılandırma hizmetine aktarılabilir.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Azure portal düğüm yapılandırmasını içeri aktarma

1. Otomasyon hesabınızdan, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** seçeneğine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **+ Ekle**' ye tıklayın.
1. **Içeri aktarma** sayfasında, yerel bilgisayarınızdaki düğüm yapılandırma DOSYASıNA (MOF) gitmek Için **düğüm yapılandırma dosyası** metin kutusunun yanındaki klasör simgesine tıklayın.

   ![Yerel dosya için araştır](./media/automation-dsc-compile/import-browse.png)

1. **Yapılandırma adı** metin kutusuna bir ad girin. Bu ad, düğüm yapılandırmasının derlendiği yapılandırmanın adıyla aynı olmalıdır.
1. **Tamam**’a tıklayın.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Azure PowerShell ile düğüm yapılandırmasını içeri aktarma

Otomasyon hesabınıza bir düğüm yapılandırmasını içeri aktarmak için [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) cmdlet 'ini kullanabilirsiniz.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [Azure Otomasyonu durum yapılandırması ile çalışmaya](automation-dsc-getting-started.md) başlama
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum yapılandırmasında yapılandırmaları derleme](automation-dsc-compile.md)
- PowerShell cmdlet başvurusu için bkz. [Azure Otomasyonu durum yapılandırması cmdlet 'leri](/powershell/module/azurerm.automation/#automation)
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/)
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmaya ilişkin bir örnek görmek için bkz. [Azure Otomasyonu durum yapılandırması ve Chocolatey kullanarak sürekli dağıtım](automation-dsc-cd-chocolatey.md)
