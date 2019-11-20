---
title: Azure Otomasyonu 'nda modülleri yönetme
description: Bu makalede, Azure Otomasyonu 'nda modüllerin nasıl yönetileceği açıklanmaktadır
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82f02f0ac11c80161f709b3b493306bc8aafc8bd
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435460"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Otomasyonu 'nda modülleri yönetme

Azure Otomasyonu, PowerShell tabanlı runbook 'lar tarafından kullanılmak üzere PowerShell modüllerini Otomasyon hesabınıza aktarma olanağı sağlar. Bu modüller, PowerShell Galerisi veya Azure için Azurerd ve az modüllerden oluşturduğunuz özel modüller olabilir. Bir Otomasyon hesabı oluşturduğunuzda, bazı modüller varsayılan olarak içeri aktarılır.

## <a name="import-modules"></a>Modülleri içeri aktarma

Otomasyon hesabınıza bir modül almanın birden çok yolu vardır. Aşağıdaki bölümlerde bir modülün içeri aktarılması için farklı yollar gösterilmektedir.

> [!NOTE]
> Azure Otomasyonu 'nda kullanılacak modüldeki bir dosyanın en büyük yolu 140 karakterdir. 140 karakter üzerinden herhangi bir yol, `Import-Module` ile PowerShell oturumuna aktarılamaz.

### <a name="powershell"></a>PowerShell

[New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) ' i Otomasyon hesabınıza bir modül içeri aktarmak için kullanabilirsiniz. Cmdlet 'i bir modül ZIP paketinin URL 'sini alır.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure portalı

Azure portal, Otomasyon hesabınıza gidin ve **paylaşılan kaynaklar**altındaki **modüller** ' i seçin. **+ Modül Ekle**' ye tıklayın. Modülünüzü içeren bir **. zip** dosyası seçin ve işlemi içeri aktarmaya başlamak için **Tamam** ' a tıklayın.

### <a name="powershell-gallery"></a>PowerShell Galerisi

PowerShell galerisinden modüller [PowerShell Galerisi](https://www.powershellgallery.com) doğrudan veya Otomasyon hesabınızdan içeri aktarılabilir.

PowerShell Galerisi bir modülü içeri aktarmak için https://www.powershellgallery.com ' a gidin ve içeri aktarmak istediğiniz modülü arayın. **Yükleme seçenekleri**altındaki **Azure Otomasyonu** sekmesinde **Azure Otomasyonu 'na dağıt** ' a tıklayın. Bu eylem Azure portal açar. **Içeri aktar** sayfasında Otomasyon hesabınızı seçin ve **Tamam**' ı tıklatın.

![PowerShell Galerisi içeri aktarma modülü](../media/modules/powershell-gallery.png)

Ayrıca, PowerShell Galerisi modülleri doğrudan Otomasyon hesabınızdan de içeri aktarabilirsiniz. Otomasyon hesabınızda, **paylaşılan kaynaklar**altında **modüller** ' i seçin. Modüller sayfasında, **Galeriye gözatıp**' ye tıklayın ve ardından PowerShell Galerisi bir modül için arama yapın. İçeri aktarmak istediğiniz modülü seçin ve **Içeri aktar**' a tıklayın. **İçeri aktarma sayfasında,** içeri aktarma işlemini başlatmak için **Tamam** ' ı tıklatın.

![Azure portal içeri aktarma PowerShell Galerisi](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modülleri Sil

Modülle ilgili sorunlarınız varsa veya bir modülün önceki bir sürümüne geri dönmeniz gerekirse, Otomasyon hesabınızdan silebilirsiniz. Bir Otomasyon hesabı oluşturduğunuzda içeri aktarılan [varsayılan modüllerin](#default-modules) orijinal sürümünü silemezsiniz. Silmek istediğiniz modül yüklü [varsayılan modüllerden](#default-modules) birinin daha yeni bir sürümü Ise, Otomasyon hesabınızla yüklenen sürüme geri gönderilir. Aksi takdirde, Otomasyon hesabınızdan sildiğiniz herhangi bir modül kaldırılır.

### <a name="azure-portal"></a>Azure portalı

Azure portal, Otomasyon hesabınıza gidin ve **paylaşılan kaynaklar**altındaki **modüller** ' i seçin. Kaldırmak istediğiniz modülü seçin. **Modül** sayfasında, clcick **Delete**. Bu modül [varsayılan modüllerden](#default-modules)biri Ise, Otomasyon hesabı oluşturulduğunda mevcut olan sürüme geri alınacaktır.

### <a name="powershell"></a>PowerShell

Bir modülü PowerShell aracılığıyla kaldırmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>İç cmdlet 'ler

Aşağıda, her Otomasyon hesabına aktarılan iç `Orchestrator.AssetManagement.Cmdlets` modülündeki cmdlet 'lerin bir listesi verilmiştir. Bu cmdlet 'ler, runbook 'larınızda ve DSC yapılandırmalarında erişilebilir ve otomasyon hesabınızda varlıklarınızla etkileşime geçmesini sağlar. Ayrıca, iç cmdlet 'ler, şifrelenmiş **değişken** değerleri, **kimlik bilgileri**ve şifrelenmiş **bağlantı** alanlarından gizli dizileri almanızı sağlar. Azure PowerShell cmdlet 'leri bu gizli dizileri alamıyor. Bu cmdlet 'ler, Azure 'da kimlik doğrulaması yapmak için bir farklı çalıştır hesabı kullanma gibi, bunları kullanırken Azure 'a örtülü olarak bağlanmanızı gerektirmez.

|Adı|Açıklama|
|---|---|
|-AutomationCertificate 'ı al|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|-AutomationConnection 'ı al|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Bekleme-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Modülünüzü bir bağlantı türü ekleyin

Modülünüzü isteğe bağlı bir dosya ekleyerek Otomasyon hesabınızda kullanmanız için özel bir [bağlantı türü](../automation-connections.md) sağlayabilirsiniz. Bu dosya, Otomasyon hesabınızdaki modülün cmdlet 'leriyle kullanılacak Azure Otomasyonu bağlantı türünü belirten bir meta veri dosyasıdır. Bunu başarmak için öncelikle bir PowerShell modülünün nasıl yazılacağını bilmeniz gerekir. Modül yazma hakkında daha fazla bilgi için bkz. [PowerShell betik modülü yazma](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Azure portal özel bir bağlantı kullanın](../media/modules/connection-create-new.png)

Bir Azure Otomasyonu bağlantı türü eklemek için modülünüzün bağlantı türü özelliklerini belirten `<ModuleName>-Automation.json` adında bir dosya içermesi gerekir. JSON dosyası, sıkıştırılmış. zip dosyanızın modül klasörüne yerleştirilir. Bu dosya, modülün temsil ettiği sisteme veya hizmete bağlanmak için gereken bir bağlantının alanlarını içerir. Yapılandırma, Azure Otomasyonu 'nda bir bağlantı türü oluşturmayı sonlandırır. Bu dosyayı kullanarak, modülün bağlantı türü için alan adlarını, türleri ve alanların şifrelenip şifrelenmeyeceğini ve isteğe bağlı olup olmayacağını belirleyebilirsiniz. Aşağıdaki örnek, bir Kullanıcı adı ve parola özelliği tanımlayan JSON dosya biçimindeki bir şablondur:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>Modül en iyi yöntemleri

PowerShell modülleri, cmdlet 'lerinin runbook 'larda kullanılabilmesi ve DSC kaynakları DSC yapılandırmalarında kullanılabilir hale getirmek için Azure Otomasyonu 'na aktarılabilir. Arka planda, Azure Otomasyonu bu modülleri depolar, runbook işi ve DSC derleme işi yürütme süresi içinde bunları runbook 'ların yürütüldüğü ve DSC yapılandırmalarının derlenmesi konumundaki Azure Otomasyonu sanal alanlarına yükler. Modüllerdeki DSC kaynakları da Automation DSC çekme sunucusuna otomatik olarak eklenir. Bunlar, DSC yapılandırması uygularsa makineler tarafından çeklenebilir.

Azure Automation 'da kullanmak üzere bir PowerShell modülü yazarken aşağıdakileri göz önünde bulundurmanız önerilir:

* . Zip paketi içine bir sürüm klasörü eklemeyin.  Bu sorun runbook 'ların bir kaygısını azaltır, ancak durum yapılandırma hizmeti ile ilgili bir soruna neden olur.  Modül DSC tarafından yönetilen düğümlere dağıtıldığında Azure Otomasyonu otomatik olarak sürüm klasörünü oluşturur ve bir sürüm klasörü varsa, iki örnek ile sona erdir olur.  DSC modülünün örnek klasör yapısı:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Modüldeki her cmdlet için bir özet, açıklama ve yardım URI’sı vardır. PowerShell'de, kullanıcının **Get-Help** cmdlet'ini kullanarak cmdlet'ler hakkında yardım almasını sağlayacak bazı bilgiler tanımlayabilirsiniz. Aşağıdaki örnek, bir. psm1 modül dosyasında için bir özeti ve Help URI 'sinin nasıl tanımlanacağını göstermektedir:

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Bu bilgileri sağlamak, PowerShell konsolundaki **Get-Help** cmdlet 'ini kullanarak bu yardımı gösterir. Bu açıklama Azure portal de görüntülenir.

  ![Tümleştirme Modülü Yardımı](../media/modules/module-activity-description.png)

* Modül bir dış hizmete bağlanırsa, bir [bağlantı türü](#add-a-connection-type-to-your-module)içermelidir. Modüldeki her cmdlet, parametre olarak bir bağlantı nesnesi (ilgili bağlantı türünün bir örneği) alabilmelidir. Kullanıcılar, bir cmdlet 'i her çağırışınızda bağlantı varlığının parametrelerini cmdlet 'inin karşılık gelen parametrelere eşler. Yukarıdaki runbook örneğine bağlı olarak, contoso kaynaklarına erişmek ve dış hizmetten veri döndürmek için ContosoConnection adlı örnek bir contoso bağlantı varlığını kullanır.

  Aşağıdaki örnekte, alanlar `PSCredential` nesnesinin UserName ve Password özelliklerine eşlenir ve sonra cmdlet 'e geçirilir.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Bu davranışa daha kolay ve daha iyi bir yol, bağlantı nesnesini doğrudan cmdlet 'e geçirmektir:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Yalnızca parametreler için bağlantı alanları yerine doğrudan bir parametre olarak bir bağlantı nesnesini kabul etmesine izin vererek cmdlet 'leriniz için önceki örnek gibi davranışı etkinleştirebilirsiniz. Genellikle her biri için bir parametre kümesi istediğinizde, Azure Otomasyonu kullanmayan bir kullanıcının, bağlantı nesnesi olarak davranacak bir Hashtable oluşturmadan cmdlet 'lerinizi çağırabilmesi gerekir. @No__t-0 parametre kümesi, bağlantı alanı özelliklerini geçirmek için kullanılır. `ConnectionObject` bağlantıyı doğrudan iletmenize olanak tanır.

* Modüldeki tüm cmdlet 'ler için çıkış türünü tanımlayın. Cmdlet için bir çıktı türünün tanımlanması tasarım zamanında IntelliSense’in, cmdlet’in yazma sırasında kullanılan çıktı özelliklerini belirlemenize yardımcı olmasını sağlar. Tasarım zamanı bilgisinin modülle kolay bir kullanıcı deneyimine göre önemli olduğu Otomasyon Runbook grafik yazma işlemi sırasında özellikle yararlıdır.

M0,0,PutType geçerli bir tür olan `[OutputType([<MyOutputType>])]` ekleyin. OutputType hakkında daha fazla bilgi edinmek için bkz. [About Functions output typeattribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Aşağıdaki kod, bir cmdlet 'e `OutputType` ekleme örneğidir:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Grafik Runbook’u Çıktı Türü](../media/modules/runbook-graphical-module-output-type.png)

  Bu davranış, bir cmdlet 'in çalıştırmak zorunda kalmadan PowerShell ıSE çıkışının "daha önce yazma" işlevselliğine benzer.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Modüldeki tüm cmdlet’leri durum bilgisiz hale getirin. Aynı uygulama etki alanında ve aynı işlem ve korumalı alanda birden çok runbook işi aynı anda çalışabilir. Bu düzeylerde paylaşılan bir durum varsa, işler birbirini etkileyebilir. Bu davranış, sorunları saptamak için aralıklı ve zor bir yol açabilir.  İşte yapılmaması gereken bir örnek.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* Modül tam olarak xcopy özellikli bir pakette yer almalıdır. Azure Otomasyonu modülleri, runbook 'ların yürütülmesi gerektiğinde Otomasyon korumalı alanlarına dağıtılır. Modüllerin üzerinde çalıştıkları konaktan bağımsız olarak çalışması gerekir. Bir modül paketini oluşturup taşıyabilmeniz ve başka bir konağın PowerShell ortamına aktarıldığında normal olarak çalışmasını sağlayabilirsiniz. Bunun gerçekleşmesi için modülün modül klasörü dışındaki dosyalara bağlı olmaması gerekir. Bu klasör, modül Azure Otomasyonu 'na aktarıldığında sıkıştırılabilen klasördür. Modül Ayrıca, bir ürün yüklendiğinde ayarlanan bu ayarlar gibi bir konaktaki benzersiz kayıt defteri ayarlarına bağlı olmamalıdır. Modüldeki tüm dosyalar 140 karakterden kısa bir yola sahip olmalıdır. 140 karakter üzerindeki tüm yollar runbook 'unuzu içeri aktarma sorunlarına neden olur. Bu en iyi uygulama izlenmemişse, modül Azure Otomasyonu 'nda kullanılamaz.  

* Modülünüzün [Azure PowerShell az modüllerine](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) başvuruyorsam, `AzureRM` ' i de başvurmadığından emin olun. @No__t-0 modülü `AzureRM` modülleriyle birlikte kullanılamaz. `Az` runbook 'larda desteklenir, ancak varsayılan olarak içeri aktarılmaz. @No__t-0 modüller ve dikkate alınması gereken noktalar hakkında bilgi edinmek için bkz. [Azure Otomasyonu 'Nda az Module support](../az-modules.md).

## <a name="default-modules"></a>Varsayılan modüller

Aşağıdaki tabloda, bir Automation hesabı oluşturulduğunda varsayılan olarak içeri aktarılan modüller listelenmektedir. Aşağıda listelenen modüller, içeri aktarılabilen yeni sürümlerine sahip olabilir, ancak daha yeni bir sürümünü silseniz bile orijinal sürümü Otomasyon hesabınızdan kaldırılamaz.

|Modül adı|Sürüm|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure Depolama | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1'i |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. cmdlet 'Leri | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| Stateconfigkompozisyon Tereso, | 1 |
| Xdscdomainjoın | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Sonraki adımlar

* PowerShell Modülleri oluşturma hakkında daha fazla bilgi için bkz. [Windows PowerShell Modülü Yazma](/powershell/scripting/developer/windows-powershell)
