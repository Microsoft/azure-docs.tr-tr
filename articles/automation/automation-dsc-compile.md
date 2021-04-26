---
title: Azure Otomasyonu durum yapılandırmasında DSC yapılandırmalarını derleme
description: Bu makalede, Azure Otomasyonu için Istenen durum yapılandırması (DSC) yapılandırmalarının nasıl derleneceği açıklanır.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e0a8bc3057bc098855eb7ff65a8feeb83b7c746
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834831"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırmasında DSC yapılandırmalarını derleme

Azure Otomasyonu durum yapılandırması ' nda Istenen durum yapılandırması (DSC) yapılandırmalarını aşağıdaki yollarla derleyebilirsiniz:

- Azure Durum Yapılandırması derleme hizmeti
  - Etkileşimli kullanıcı arabirimiyle başlangıç yöntemi
   - İş durumunu kolayca izleyin

- Windows PowerShell
  - Yerel iş istasyonunda veya derleme hizmetinde Windows PowerShell 'den çağrı
  - Geliştirme testi işlem hattı ile tümleştirme
  - Karmaşık parametre değerlerini sağlayın
  - Ölçekteki node ve Node olmayan verilerle çalışma
  - Önemli performans iyileştirmesi

Azure VM 'lerinize yapılandırma göndermek için Azure Istenen durum yapılandırması (DSC) uzantısıyla birlikte Azure Resource Manager şablonlarını da kullanabilirsiniz. Azure DSC Uzantısı, Azure VM 'lerde çalışan DSC yapılandırmasını teslim etmek, uygulamak ve raporlamak için Azure VM Aracısı çerçevesini kullanır. Azure Resource Manager şablonları kullanarak derleme ayrıntıları için, [Azure Resource Manager şablonlarla Istenen durum yapılandırma uzantısı](../virtual-machines/extensions/dsc-template.md#details)' na bakın. 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Azure durum yapılandırmasında DSC yapılandırması derleme

### <a name="portal"></a>Portal

1. Otomasyon hesabınızda **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Yapılandırmalar** sekmesine tıklayın ve ardından derlemek için yapılandırma adına tıklayın.
1. **Derle**' ye tıklayın.
1. Yapılandırmada parametre yoksa, derlemek isteyip istemediğinizi onaylamanız istenir. Yapılandırmanın parametreleri varsa, parametre değerleri sağlayabilmeniz için **derleme yapılandırma** dikey penceresi açılır.
1. Derleme işi sayfası, derleme işi durumunu izleyebilmeniz için açılır. Bu sayfayı Ayrıca, Azure Otomasyonu durum yapılandırması çekme sunucusuna yerleştirilmiş düğüm yapılandırmalarını (MOF yapılandırma belgeleri) izlemek için de kullanabilirsiniz.

### <a name="azure-powershell"></a>Azure PowerShell

Windows PowerShell ile derlemeyi başlatmak için [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) kullanabilirsiniz. Aşağıdaki örnek kod **SampleConfig** ADLı bir DSC yapılandırmasının derlemesini başlatır.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` iş durumunu izlemek için kullanabileceğiniz bir derleme işi nesnesi döndürür. Ardından, derleme işinin durumunu öğrenmek için [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) ile bu derleme işi nesnesini kullanabilir ve kendi akışlarını (çıkış) görüntülemek için [Get-Azautomationdsccompilationjoi put](/powershell/module/az.automation/get-azautomationdscconfiguration) ' i kullanabilirsiniz. Aşağıdaki örnek SampleConfig yapılandırmasının derlemesini başlatır, tamamlanana kadar bekler ve ardından akışlarını görüntüler.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Temel parametreleri bildir

Parametre türleri ve özellikler dahil olmak üzere DSC yapılandırmalarında parametre bildirimi, Azure Otomasyonu runbook 'larıyla aynı şekilde çalışmaktadır. Runbook parametreleri hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu 'nda runbook başlatma](./start-runbooks.md) .

Aşağıdaki örnekte `FeatureName` ve parametreleri, `IsPresent` derleme sırasında oluşturulan **parametersexsample. örnek** düğüm yapılandırmasındaki özelliklerin değerlerini belirlemekte kullanılır.

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

Azure Otomasyonu durum yapılandırma portalında veya Azure PowerShell ile temel parametreleri kullanan DSC yapılandırmalarını derleyebilirsiniz.

#### <a name="portal"></a>Portal

Portalda, **Derle**' ye tıkladıktan sonra parametre değerleri girebilirsiniz.

![Yapılandırma derleme parametreleri](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell, anahtarın parametre adıyla eşleştiği ve değerin parametre değerine eşit olduğu bir [Hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables)'da parametreler gerektirir.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Nesneleri parametre olarak geçirme hakkında daha fazla bilgi için `PSCredential` bkz. [Credential varlıkları](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Azure Otomasyonu 'nda bileşik kaynakları içeren yapılandırma derleme

**Bileşik kaynaklar** ÖZELLIĞI, DSC yapılandırmalarını bir yapılandırma içinde iç içe geçmiş kaynaklar olarak kullanmanıza olanak tanır. Bu özellik, birden çok yapılandırmanın tek bir kaynak üzerinde uygulamasını sağlar. Bileşik kaynaklar hakkında daha fazla bilgi edinmek için bkz. [bileşik kaynaklar: kaynak olarak DSC yapılandırması kullanma](/powershell/scripting/dsc/resources/authoringresourcecomposite) .

> [!NOTE]
> Bileşik kaynakları içeren yapılandırmaların doğru şekilde derlenmesi için, önce Birleşik sitelerin kullandığı DSC kaynaklarını Azure Otomasyonu 'na aktarmanız gerekir. DSC bileşik kaynağı ekleme işlemi, Azure Otomasyonu 'na herhangi bir PowerShell modülü eklemekten farklı değildir. Bu işlem, [Azure Automation 'Da modülleri yönetme](./shared-resources/modules.md)bölümünde belgelenmiştir.

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Azure Otomasyonu 'nda yapılandırma derlerken ConfigurationData yönetimi

`ConfigurationData` , PowerShell DSC 'yi kullanırken herhangi bir ortama özgü yapılandırmadan yapısal yapılandırmayı ayırmanızı sağlayan yerleşik bir DSC parametresidir. Daha fazla bilgi için bkz. [POWERSHELL DSC 'de "Where" den "neleri" ayırma](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Azure Otomasyonu durum yapılandırması 'nda derlerken, `ConfigurationData` Azure portal Azure PowerShell ancak kullanamazsınız.

Aşağıdaki örnek DSC yapılandırması `ConfigurationData` , `$ConfigurationData` ve `$AllNodes` anahtar kelimeleri aracılığıyla kullanılır. Bu örnek için [xWebAdministration modülüne](https://www.powershellgallery.com/packages/xWebAdministration/) da ihtiyacınız vardır.

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

Önceki DSC yapılandırmasını Windows PowerShell ile derleyebilirsiniz. Aşağıdaki betik, Azure Otomasyonu durum yapılandırması çekme hizmeti 'ne iki düğüm yapılandırması ekler: **configurationdatasample. MyVM1** ve **Configurationdatasample. MyVM3**.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Derleme sırasında Azure Otomasyonu 'nda varlıklarla çalışma

Varlık başvuruları hem Azure Otomasyonu durum Yapılandırması hem de runbook 'larda aynıdır. Daha fazla bilgi için, aşağıdakilere bakın:

- [Sertifikalar](./shared-resources/certificates.md)
- [Bağlantılar](automation-connections.md)
- [Kimlik Bilgileri](./shared-resources/credentials.md)
- [Değişkenler](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Kimlik bilgisi varlıkları

Azure Otomasyonu 'ndaki DSC yapılandırması, cmdlet 'ini kullanarak Otomasyon kimlik bilgileri varlıklarına başvurabilir `Get-AutomationPSCredential` . Bir yapılandırma bir nesneyi belirten bir parametreye sahipse `PSCredential` , `Get-AutomationPSCredential` kimlik bilgisini almak Için bir Azure Otomasyonu kimlik bilgisi varlığının dize adını cmdlet 'ine geçirerek kullanın. Sonra nesneyi gerektiren parametre için bu nesneyi kullanın `PSCredential` . Arka planda, bu adı taşıyan Azure Otomasyonu kimlik bilgisi varlığı alınır ve yapılandırmaya geçirilir. Aşağıdaki örnekte bu senaryonun eylemde bulunduğu gösterilmektedir.

Düğüm yapılandırmalarında (MOF yapılandırma belgeleri) kimlik bilgilerinin güvenli tutulması, düğüm yapılandırması MOF dosyasındaki kimlik bilgilerinin şifrelenmesini gerektirir. Şu anda, düğüm yapılandırması MOF oluşturma sırasında düz metinde çıkış kimlik bilgileri için PowerShell DSC iznine sahip olmanız gerekir. PowerShell DSC, Azure Otomasyonu 'nun, derleme işi aracılığıyla oluşturulduktan sonra tüm MOF dosyasını şifrelediği farkında değildir.

PowerShell DSC 'ye, kimlik bilgilerinin oluşturulan düğüm yapılandırması MOF 'lar yapılandırma verileri kullanılarak düz metin olarak outputolduğunu söyleyebilirsiniz. `PSDscAllowPlainTextPassword = $true` `ConfigurationData` DSC yapılandırmasında görüntülenen ve kimlik bilgilerini kullanan her düğüm blok adı için aracılığıyla geçiş yapmalısınız.

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

Önceki DSC yapılandırmasını PowerShell ile derleyebilirsiniz. Aşağıdaki PowerShell kodu, Azure Otomasyonu durum yapılandırması çekme sunucusuna iki düğümlü yapılandırma ekler: **Credentialsample. MyVM1** ve **Credentialsample. MyVM2**.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Derleme tamamlandığında, `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` Bu iletiyi güvenle yoksayabilirsiniz hata iletisiyle karşılaşabilirsiniz.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Windows PowerShell 'de DSC yapılandırmanızı derleme

Windows PowerShell 'de DSC yapılandırmalarını derleme işlemi, PowerShell DSC belgelerine [bir yapılandırma yazma, derleme ve uygulama](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)işlemlerini içerir.
Bu işlemi bir geliştirici iş istasyonundan veya [Azure DevOps](https://dev.azure.com)gibi bir yapı hizmeti içinden yürütebilirsiniz. Daha sonra yapılandırmayı Azure durum yapılandırma hizmetine derleyerek üretilen MOF dosyalarını içeri aktarabilirsiniz.

Windows PowerShell 'de derlemek, yapılandırma içeriğini imzalama seçeneğini de sağlar. DSC Aracısı, yönetilen bir düğümde yerel olarak imzalı bir düğüm yapılandırmasını doğrular. Doğrulama, düğüme uygulanan yapılandırmanın yetkili bir kaynaktan geldiğinden emin olmanızı sağlar.

Ayrıca, Azure dışında derlenen düğüm yapılandırmalarının (MOF dosyaları) içeri aktarabilirsiniz. İçeri aktarma, geliştirici iş istasyonundan veya [Azure DevOps](https://dev.azure.com)gibi bir hizmette derleme içerir. Bu yaklaşımın performansı ve güvenilirliği dahil olmak üzere birden çok avantajı vardır.

> [!NOTE]
> Azure Otomasyonu 'nun içeri aktarmaya izin vermek için düğüm yapılandırma dosyasının 1 MB 'den büyük olmaması gerekir.

Düğüm yapılandırmalarının imzalanması hakkında daha fazla bilgi için bkz. [WMF 5,1 ' deki geliştirmeler-yapılandırma ve modülün imzalanması](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Azure portal düğüm yapılandırmasını içeri aktarma

1. Otomasyon hesabınızda, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** seçeneğine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **Ekle**' ye tıklayın.
1. Içeri aktarma sayfasında, yerel bilgisayarınızdaki bir düğüm yapılandırması MOF dosyasına gitmek için **düğüm yapılandırma dosyası** alanının yanındaki klasör simgesine tıklayın.

   ![Yerel dosya için araştır](./media/automation-dsc-compile/import-browse.png)

1. **Yapılandırma adı** alanına bir ad girin. Bu ad, düğüm yapılandırmasının derlendiği yapılandırmanın adıyla aynı olmalıdır.
1. **Tamam**'a tıklayın.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Azure PowerShell bir düğüm yapılandırmasını içeri aktarma

Otomasyon hesabınıza bir düğüm yapılandırmasını içeri aktarmak için [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) cmdlet 'ini kullanabilirsiniz.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [Azure Otomasyonu durum yapılandırmasını kullanmaya başlama](automation-dsc-getting-started.md).
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum YAPıLANDıRMASıNDA DSC yapılandırmalarını derleme](automation-dsc-compile.md).
- PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).
- Sürekli dağıtım işlem hattında durum yapılandırması kullanmanın bir örneği için bkz. [Chocolatey ile sürekli dağıtımı ayarlama](automation-dsc-cd-chocolatey.md).
