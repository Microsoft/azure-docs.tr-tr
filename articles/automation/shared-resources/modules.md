---
title: Azure Otomasyonu’nda modülleri yönetme
description: Bu makalede, DSC yapılandırmalarında runbook 'larda ve DSC kaynaklarında cmdlet 'leri etkinleştirmek için PowerShell modüllerinin nasıl kullanılacağı açıklanır.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eaff96907b48ddc0fc92296a015ceb063149e6ec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832761"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Otomasyonu’nda modülleri yönetme

Azure Otomasyonu, DSC yapılandırmalarında runbook 'larda ve DSC kaynaklarında cmdlet 'leri etkinleştirmek için bir dizi PowerShell modülü kullanır. Desteklenen modüller şunlardır:

* [Az. Automation Azure PowerShell](/powershell/azure/new-azureps-module-az).
* [Azurerd. Automation Azure PowerShell](/powershell/module/azurerm.automation/).
* Diğer PowerShell modülleri.
* İç `Orchestrator.AssetManagement.Cmdlets` modül.
* Python 2 modülleri.
* Oluşturduğunuz özel modüller.

Bir Otomasyon hesabı oluşturduğunuzda, Azure Otomasyonu bazı modülleri varsayılan olarak içeri aktarır. Bkz. [varsayılan modüller](#default-modules).

## <a name="sandboxes"></a>Korumalı Alanlar

Otomasyon Runbook ve DSC derleme işlerini yürüttüğünde, modülleri runbook 'ların çalıştırılabileceği ve DSC yapılandırmalarının derleyeceği sanal alanlara yükler. Otomasyon Ayrıca DSC çekme sunucusundaki modüllerde DSC kaynaklarını otomatik olarak koyar. Makineler DSC yapılandırmasını uygularsa kaynakları çekebilir.

>[!NOTE]
>Yalnızca runbook 'larınızın ve DSC yapılandırmalarının gerektirdiği modülleri içeri aktardığınızdan emin olun. Kök az modülünün içeri aktarılmasını önermiyoruz. Bu, ihtiyacınız olmayan birçok farklı modül içerir ve bu da performans sorunlarına neden olabilir. Bunun yerine, az. COMPUTE gibi ayrı modülleri içeri aktarın.

Bulut korumalı alanı, en fazla 48 sistem çağrısı destekler ve tüm diğer çağrıları güvenlik nedenleriyle kısıtlar. Kimlik bilgisi yönetimi ve bazı ağ gibi diğer işlevler, bulut korumalı alanında desteklenmez.

Dahil edilen modül ve cmdlet 'lerin sayısı nedeniyle cmdlet 'lerinin desteklenmeyen çağrılar yapıp yapabileceklerini anlamak zordur. Genellikle, yükseltilmiş erişim gerektiren cmdlet 'lerle ilgili sorunları gördük, parametre olarak kimlik bilgisi veya ağ ile ilgili cmdlet 'ler gerektirir. Tam yığın ağ işlemleri gerçekleştiren tüm cmdlet 'ler, Aıpservice PowerShell modülünden [Connect-aıpservice](/powershell/module/aipservice/connect-aipservice) ve DNSClient modülünden [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) de dahil olmak üzere korumalı alanda desteklenmez.

Bunlar, korumalı alan ile ilgili bilinen sınırlamalardır. Önerilen geçici çözüm, [karma bir runbook worker](../automation-hybrid-runbook-worker.md) dağıtmak veya [Azure işlevleri](../../azure-functions/functions-overview.md)kullanmaktır.

## <a name="default-modules"></a>Varsayılan modüller

Aşağıdaki tabloda, Otomasyon hesabınızı oluştururken Azure Automation 'ın varsayılan olarak içe aktardığı modüller listelenmektedir. Otomasyon, bu modüllerin daha yeni sürümlerini içeri aktarabilir. Ancak, daha yeni bir sürümü silseniz bile, orijinal sürümü Otomasyon hesabınızdan kaldıramazsınız. Bu varsayılan modüllerin çeşitli Azurerd modülleri olduğunu unutmayın.

Varsayılan modüller genel modüller olarak da bilinir. Azure portal, **genel modül** özelliği, hesap oluşturulduğunda içeri aktarılan bir modül görüntülenirken **doğru** olacaktır.

![Azure portalında genel modül özelliğinin ekran görüntüsü](../media/modules/automation-global-modules.png)

Otomasyon, kök az modülünü yeni veya mevcut Otomasyon hesaplarına otomatik olarak içeri aktarmaz. Bu modüllerle çalışma hakkında daha fazla bilgi için, bkz. [az modules 'e geçme](#migrate-to-az-modules).

> [!NOTE]
> [VM'leri çalışma saatleri dışında Başlat/Durdur](../automation-solution-vm-management.md) özelliğinin dağıtımı Için kullanılan Otomasyon hesaplarında modül ve Runbook 'ların değiştirilmesini önermiyoruz.

|Modül adı|Sürüm|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure Depolama | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
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

## <a name="az-modules"></a>Az modüller

Az. Automation için, cmdlet 'lerin çoğunluğu Azurerd modülleri için kullanılanlarla aynı adlara sahiptir, ancak `AzureRM` öneki olarak değişir `Az` . Bu adlandırma kuralını izlemeden az modüllerle ilgili bir liste için bkz. [özel durumlar listesi](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>İç cmdlet 'ler

Azure Otomasyonu, `Orchestrator.AssetManagement.Cmdlets` Varsayılan olarak yüklenen Windows için Log Analytics aracısına yönelik iç modülü destekler. Aşağıdaki tabloda iç cmdlet 'ler tanımlanmaktadır. Bu cmdlet 'ler, paylaşılan kaynaklarla etkileşim kurmak için Azure PowerShell cmdlet 'leri yerine kullanılmak üzere tasarlanmıştır. Şifrelenmiş değişkenlerin, kimlik bilgilerinin ve şifreli bağlantıların gizli dizilerini alabilirler.

>[!NOTE]
>İç cmdlet 'ler yalnızca Azure korumalı alan ortamında veya bir Windows karma runbook Çalışanındaki runbook 'ları yürütürken kullanılabilir. 

|Ad|Açıklama|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

İç cmdlet 'lerin, az ve Azurermcmdlet 'lerini adlandırmada farklı olduğunu unutmayın. İç cmdlet adları, adında veya adında sözcükler içermez `Azure` `Az` , ancak Word 'ü kullanır `Automation` . Bir Azure korumalı alanı veya Windows karma runbook çalışanı üzerinde runbook yürütmesi sırasında az veya Azurerk cmdlet 'lerinin kullanılmasını öneririz. Daha az parametre gerektirir ve zaten çalışmakta olan işiniz bağlamında çalışır.

Otomasyon kaynaklarını bir runbook bağlamı dışında işlemek için az veya Azurerd cmdlet 'lerini kullanın. 

## <a name="python-modules"></a>Python modülleri

Azure Otomasyonu 'nda Python 2 runbook 'ları oluşturabilirsiniz. Python modülü bilgileri için bkz. [Azure Otomasyonu 'Nda Python 2 paketlerini yönetme](../python-packages.md).

## <a name="custom-modules"></a>Özel modüller

Azure Otomasyonu, runbook 'larınızla ve DSC yapılandırmalarında kullanmak için oluşturduğunuz özel PowerShell modüllerini destekler. Bir tür özel modül, isteğe bağlı olarak modül cmdlet 'leri için özel işlevselliği tanımlamak üzere meta veri dosyası içeren bir tümleştirme modülüdür. Bir tümleştirme modülünün kullanılmasına bir örnek, [bağlantı türü ekleme](../automation-connections.md#add-a-connection-type)' de verilmiştir.

Azure Otomasyonu, cmdlet 'lerini kullanılabilir hale getirmek için özel bir modül içeri aktarabilir. Arka planda, bu modül, diğer modülleri de olduğu gibi, modülü depolar ve Azure korumalı alanında kullanır.

## <a name="migrate-to-az-modules"></a>Az modüllere geçir

Bu bölüm, otomasyondaki az modüllere nasıl geçiş yapılacağını anlatır. Daha fazla bilgi için bkz. [Azurerd 'Den az ' a geçiş Azure PowerShell](/powershell/azure/migrate-from-azurerm-to-az).

Aynı Otomasyon hesabında Azurermmodules ve az modules çalıştırılmasını önermiyoruz. Azurerd 'den az ' a geçiş yapmak istediğinizden emin olduğunuzda, tam bir geçişe tam olarak kaydedilmesi en iyisidir. Otomasyon, genellikle Otomasyon hesabı içindeki korumalı alanlar 'ı başlangıç zamanlarında kaydedilecek şekilde yeniden kullanır. Tam modül geçişi yapmazsanız, yalnızca Azurere modüllerini kullanan bir iş başlatabilir ve ardından yalnızca az modüller kullanan başka bir iş başlatabilirsiniz. Korumalı alan yakında çöker ve modüllerin uyumlu olmadığı belirten bir hata alırsınız. Bu durum, belirli bir runbook veya yapılandırma için rastgele oluşan kilitlenmelere neden olur.

>[!NOTE]
>Yeni bir Otomasyon hesabı oluşturduğunuzda, az modüllere geçişten sonra bile Otomasyon, Azurermmodules modüllerini varsayılan olarak de yüklerse. Öğretici runbook 'ları Azurerd cmdlet 'leriyle yine de güncelleştirebilirsiniz. Ancak, bu runbook 'ları çalıştırmamanız gerekir.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Modül geçişten önce runbook 'larınızı ve DSC yapılandırmalarının sınamasını yapın

Az modüllere geçirmeden önce, tüm runbook 'ları ve DSC yapılandırmalarının ayrı bir Otomasyon hesabında dikkatli bir şekilde test ettiğinizden emin olun. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Azurerd modülleri kullanan tüm runbook 'ları durdurma ve zamanlamayı kaldırma

Azurerd modüllerini kullanan mevcut runbook 'ları veya DSC yapılandırmasını çalıştırmazsanız emin olmak için, etkilenen tüm runbook 'ları ve konfigürasyonları durdurmanız ve zamanlamanın zamanlamasını geri almanız gerekir. İlk olarak, gerekirse daha sonra öğeyi yeniden zamangelebilmeniz için her runbook veya DSC yapılandırmasını ve zamanlamalarını ayrı ayrı gözden geçirdiğinizden emin olun.

Zamanlamalarınızı kaldırmaya hazırsanız, Azure portal ya da [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) cmdlet 'ini kullanabilirsiniz. Bkz. [zamanlamayı kaldırma](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Azurerd modüllerini kaldır

Az modülleri içeri aktarmadan önce Azurere modüllerini kaldırmak mümkündür. Ancak, bunu yaparsanız, kaynak denetimi eşitlemesini kesintiye uğratabilir ve hala zamanlanan betiklerin başarısız olmasına neden olur. Modülleri kaldırmaya karar verirseniz, bkz. [Azurerd 'Yi kaldırma](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>İçeri Aktar az Modules

Az modülünün Otomasyon hesabınıza aktarılması, modülün runbook 'ların kullandığı PowerShell oturumuna otomatik olarak aktarılmaz. Modüller aşağıdaki durumlarda PowerShell oturumuna aktarılır:

* Bir runbook bir modülden bir cmdlet istediğinde.
* Bir runbook, modülü [Import-Module](/powershell/module/microsoft.powershell.core/import-module) cmdlet 'i ile açıkça içeri aktardığında.
* Bir runbook, modülü [using Module](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) ifadesiyle açıkça içeri aktardığında. Using deyimleri Windows PowerShell 5,0 ile başlayarak desteklenir ve sınıfları ve sabit listesi türü içeri aktarmayı destekler.
* Bir runbook başka bir bağımlı modülü içeri aktardığında.

Az modules Azure portal Otomasyon hesabına aktarabilirsiniz. Kullanılabilir az modülle değil, yalnızca ihtiyaç duyduğunuz az modülleri içe aktarmayı unutmayın. [Az. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) diğer az modüllerle ilgili bir bağımlılık olduğundan, bu modülü diğerlerinden önce içeri aktardığınızdan emin olun.

1. Otomasyon hesabınızdan, **paylaşılan kaynaklar** altında **modüller**' i seçin.
2. **Galeriye gözatamazsınız**' ı seçin.  
3. Arama çubuğuna modül adını (örneğin, `Az.Accounts` ) girin.
4. PowerShell modülü sayfasında, modülü Otomasyon hesabınıza aktarmak için **Içeri aktar** ' ı seçin.

    ![Otomasyon hesabınıza modülleri içeri aktarma ekran görüntüsü](../media/modules/import-module.png)

Ayrıca, içeri aktarılacak modül için arama yaparak bu [PowerShell Galerisi](https://www.powershellgallery.com)aracılığıyla içeri aktarma yapabilirsiniz. Modülü bulduğunuzda, seçin ve **Azure Otomasyonu** sekmesini seçin. **Azure Otomasyonu 'Na dağıt**' ı seçin.

![Modülleri doğrudan PowerShell Galerisi içeri aktarma ekran görüntüsü](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Runbook 'larınızı test edin

Az modülleri Otomasyon hesabına aktardıktan sonra, yeni modülleri kullanmak için Runbook 'larınızı ve DSC yapılandırmasını düzenleyebilirsiniz. Yeni cmdlet 'leri kullanmak için Runbook 'un değiştirilmesini sınamanın bir yolu, `Enable-AzureRmAlias -Scope Process` runbook 'un başlangıcında komutunu kullanmaktır. Bu komut, runbook 'a eklenerek, betik değişiklik yapılmadan çalıştırılabilir.

## <a name="author-modules"></a>Yazma modülleri

Azure Otomasyonu 'nda kullanılmak üzere özel bir PowerShell modülü yazdığınızda bu bölümdeki hususları izlemeniz önerilir. Modülünüzü içeri aktarmaya hazırlamak için modül klasörüyle aynı ada sahip en az bir. psd1,. psm1 veya PowerShell Module **. dll** dosyası oluşturmanız gerekir. Daha sonra, Azure Otomasyonu 'nun dosyayı tek bir dosya olarak içe aktarabilmesi için modül klasörünü sıkıştırarak. **. Zip** paketi, içerilen modül klasörüyle aynı ada sahip olmalıdır.

PowerShell modülü yazma hakkında daha fazla bilgi edinmek için bkz. [PowerShell betik modülü yazma](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

### <a name="version-folder"></a>Sürüm klasörü

PowerShell yan yana Modül sürümü oluşturma, PowerShell 'de bir modülün birden fazla sürümünü kullanmanıza olanak sağlar. Bu, test edilmiş ve yalnızca belirli bir PowerShell modülünün bir sürümüne karşı çalışan, ancak diğer betikler aynı PowerShell modülünün daha yeni bir sürümünü gerektiren eski betikleriniz varsa yararlı olabilir.

Birden çok sürüm içerecek şekilde PowerShell modülleri oluşturmak için, modül klasörünü oluşturun ve ardından, kullanılabilir olmasını istediğiniz modülün her sürümü için bu modül klasörü içinde bir klasör oluşturun. Aşağıdaki örnekte, *TestModule* adlı bir modül 1.0.0 ve 2.0.0 olmak üzere iki sürüm sağlar.

```dos
TestModule
   1.0.0
   2.0.0
```

Sürüm klasörlerinin her biri içinde, ilgili sürüm klasörüne bir modül oluşturan PowerShell. psm1,. psd1 veya PowerShell Module **. dll** dosyalarını kopyalayın. Azure Otomasyonu 'nun dosyayı tek bir. zip dosyası olarak içe aktarabilmesi için modül klasörünü ZIP. Automation yalnızca, içeri aktarılan modülün en yüksek sürümünü gösteriyorsa, modül paketi modülün yan yana sürümlerini içeriyorsa, runbook 'larınızda veya DSC yapılandırmalarında kullanıma sunulmuştur.  

Otomasyon aynı paket içinde yan yana sürümler içeren modülleri destekleirken, modül paketi içeri aktarmaları arasında bir modülün birden çok sürümünün kullanılmasını desteklemez. Örneğin, 1 ve 2 sürümlerini Otomasyon hesabınıza içeren **A modülünü** içeri aktarırsınız. Daha sonra, **A modülünü** 3 ve 4 sürümlerini içerecek şekilde güncelleştirdiğinizde, Otomasyon hesabınıza aktardığınızda yalnızca 3 ve 4 sürümleri herhangi bir runbook 'TA veya DSC yapılandırmasında kullanılabilir. Tüm 1, 2, 3 ve 4 sürümlerinin kullanılabilir olmasını istiyorsanız, içeri aktardığınız. zip dosyası sürüm 1, 2, 3 ve 4 ' ü içermelidir.

Runbook 'lar arasında aynı modülün farklı sürümlerini kullanacaksanız, cmdlet 'ini kullanarak runbook 'unuz üzerinde kullanmak istediğiniz sürümü her zaman bildirmeniz `Import-Module` ve parametresini eklemeniz gerekir `-RequiredVersion <version>` . Kullanmak istediğiniz sürüm en son sürümse bile. Bunun nedeni, runbook işlerinin aynı korumalı alanda çalıştırılabileceğinden kaynaklanır. Korumalı alan, belirli bir sürüm numarasına ait bir modülü zaten açık bir biçimde yüklemiştir, çünkü o korumalı alanda önceki bir iş bu şekilde olduğu gibi, bu korumalı alanda gelecekte yapılacak işler o modülün en son sürümünü otomatik olarak yüklemez. Bunun nedeni, onun bazı sürümünün korumalı alana zaten yüklenmiş olması olabilir.

Bir DSC kaynağı için, belirli bir sürümü belirtmek üzere aşağıdaki komutu kullanın:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Yardım bilgileri

Modülünüzün her cmdlet için bir özeti, açıklama ve yardım URI 'SI ekleyin. PowerShell 'de cmdlet 'ini kullanarak cmdlet 'ler için yardım bilgilerini tanımlayabilirsiniz `Get-Help` . Aşağıdaki örnek, bir **. psm1** modül dosyasında bir özeti ve Help URI 'sinin nasıl tanımlanacağını gösterir.

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
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Bu bilgilerin sağlanması `Get-Help` , PowerShell konsolundaki cmdlet 'i aracılığıyla yardım metnini gösterir. Bu metin de Azure portal görüntülenir.

  ![Tümleştirme modülü yardımı 'nın ekran görüntüsü](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Bağlantı türü

Modül bir dış hizmete bağlanıyorsa [özel bir tümleştirme modülü](#custom-modules)kullanarak bir bağlantı türü tanımlayın. Modüldeki her cmdlet, bu bağlantı türünün bir örneğini (bağlantı nesnesi) bir parametre olarak kabul etmelidir. Kullanıcılar, bir cmdlet 'i her çağırışınızda bağlantı varlığının parametrelerini cmdlet 'inin karşılık gelen parametrelere eşler.

![Azure portal özel bir bağlantı kullanın](../media/modules/connection-create-new.png)

Aşağıdaki runbook örneği, `ContosoConnection` contoso kaynaklarına erişmek ve dış hizmetten veri döndürmek için adlı bir contoso bağlantı varlığı kullanır. Bu örnekte, alanlar `UserName` `Password` bir nesnenin ve özelliklerine eşlenir `PSCredential` ve sonra cmdlet 'e geçirilir.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Bu davranışa daha kolay ve daha iyi bir yol, bağlantı nesnesini doğrudan cmdlet 'e geçirmektir:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Yalnızca parametreler için bağlantı alanları yerine doğrudan bir parametre olarak bir bağlantı nesnesini kabul etmesine izin vererek cmdlet 'leriniz için benzer davranışı etkinleştirebilirsiniz. Genellikle her biri için bir parametre kümesi istediğinizde, Otomasyonu kullanmayan bir kullanıcının, bağlantı nesnesi olarak davranacak bir Hashtable oluşturmadan cmdlet 'lerinizi çağırabilmesi gerekir. Parametre kümesi, `UserAccount` bağlantı alanı özelliklerini geçirmek için kullanılır. `ConnectionObject` bağlantıyı doğrudan iletmenizi sağlar.

### <a name="output-type"></a>Çıkış türü

Modülünüzün tüm cmdlet 'ler için çıkış türünü tanımlayın. Bir cmdlet için çıkış türü tanımlama, yazma sırasında cmdlet 'in çıkış özelliklerini belirlemesine yardımcı olmak için tasarım zamanı IntelliSense 'e izin verir. Bu uygulama, tasarım zamanı bilgisinin modülle kolay bir kullanıcı deneyimi için anahtar olduğu grafik runbook 'u yazma sırasında özellikle yararlıdır.

`[OutputType([<MyOutputType>])]` `MyOutputType` Geçerli bir tür olan öğesini ekleyin. Hakkında daha fazla bilgi edinmek için `OutputType` bkz. [About Functions output typeattribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Aşağıdaki kod, `OutputType` bir cmdlet 'e ekleme örneğidir:

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

  ![Grafik runbook 'u çıkış türünün ekran görüntüsü](../media/modules/runbook-graphical-module-output-type.png)

  Bu davranış, bir cmdlet 'in, PowerShell tümleştirme hizmeti ortamındaki çıkışının çalıştırması gerekmeden "daha önce yazma" işlevselliğine benzer.

  ![POSH IntelliSense ekran görüntüsü](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet durumu

Modülünüzün tüm cmdlet 'leri durum bilgisiz yapın. Birden çok runbook işi aynı anda aynı `AppDomain` ve aynı işlem ve korumalı alanda çalıştırılabilir. Bu düzeylerde paylaşılan bir durum varsa, işler birbirini etkileyebilir. Bu davranış aralıklı ve tanınmaya duyarlı sorunlara yol açabilir. Yapacaklarınız için bir örnek aşağıda verilmiştir:

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

### <a name="module-dependency"></a>Modül bağımlılığı

Modülün XCOPY kullanılarak kopyalanabilen bir pakette tam olarak bulunduğundan emin olun. Automation modülleri, runbook 'lar yürütülürken Otomasyon korumalı alanlarına dağıtılır. Modüller, onları çalıştıran konaktan bağımsız olarak çalışmalıdır.

Bir modül paketini oluşturup taşıyabilmeniz ve başka bir konağın PowerShell ortamına aktarıldığında normal olarak çalışmasını sağlayabilirsiniz. Bunun gerçekleşmesi için, modülün, modül Otomasyonu 'na aktarıldığında sıkıştırılmış modül klasörü dışındaki dosyalara bağlı olmadığından emin olun.

Modülünüzün bir konaktaki benzersiz kayıt defteri ayarlarına bağlı olmaması gerekir. Örnekler, bir ürün yüklendiğinde yapılan ayarlardır.

### <a name="module-file-paths"></a>Modül dosyası yolları

Modüldeki tüm dosyaların 140 karakterden kısa olan yollara sahip olduğundan emin olun. 140 karakter üzerindeki tüm yollar runbook 'ların içeri aktarılmasıyla ilgili sorunlara neden olur. Otomasyon, 140 karakter üzerinde yol boyutu olan bir dosyayı ile PowerShell oturumuna alamaz `Import-Module` .

## <a name="import-modules"></a>Modülleri içeri aktarma

Bu bölümde, Otomasyon hesabınıza bir modül içeri aktarabileceğiniz çeşitli yollar tanımlanmaktadır.

### <a name="import-modules-in-the-azure-portal"></a>Azure portal modülleri içeri aktar

Azure portal bir modül içeri aktarmak için:

1. Otomasyon hesabınıza gidin.
2. **Paylaşılan kaynaklar** altında **modüller**' i seçin.
3. **Modül Ekle**' yi seçin.
4. Modülünüzü içeren **. zip** dosyasını seçin.
5. İşlemi içeri aktarmaya başlamak için **Tamam ' ı** seçin.

### <a name="import-modules-by-using-powershell"></a>PowerShell kullanarak modülleri içeri aktarma

[Yeni-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) cmdlet 'Ini kullanarak Otomasyon hesabınıza bir modül aktarabilirsiniz. Cmdlet 'i Module. zip paketi için bir URL alır.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Ayrıca, PowerShell Galerisi doğrudan bir modül içeri aktarmak için aynı cmdlet 'i de kullanabilirsiniz. `ModuleName`PowerShell Galerisi ve ' den aldığınızdan emin olun `ModuleVersion` . [](https://www.powershellgallery.com)

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>PowerShell Galerisi modülleri içeri aktarın

[PowerShell Galerisi](https://www.powershellgallery.com) modüllerini doğrudan Galeriden ya da Otomasyon hesabınızdan içeri aktarabilirsiniz.

Bir modülü doğrudan PowerShell Galerisi içeri aktarmak için:

1. Adresine gidin https://www.powershellgallery.com ve içeri aktarılacak modüle yönelik arama yapın.
2. **Yükleme seçenekleri** altında **Azure Otomasyonu** sekmesinde **Azure Otomasyonu 'na dağıt**' ı seçin. Bu eylem Azure portal açar. 
3. Içeri Aktar sayfasında Otomasyon hesabınızı seçin ve **Tamam**' ı seçin.

![PowerShell Galerisi içeri aktarma modülünün ekran görüntüsü](../media/modules/powershell-gallery.png)

Bir PowerShell Galerisi modülünü doğrudan Otomasyon hesabınızdan içeri aktarmak için:

1. **Paylaşılan kaynaklar** altında **modüller**' i seçin. 
2. **Gözden geçirme Galerisi**' ni seçin ve ardından galeride bir modül arayın. 
3. İçeri aktarılacak modülü seçin ve **Içeri aktar**' ı seçin. 
4. İçeri aktarma işlemini başlatmak için **Tamam ' ı** seçin.

![Azure portal PowerShell Galerisi modülünü içeri aktarma ekran görüntüsü](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modülleri Sil

Modülle ilgili sorunlarınız varsa veya bir modülün önceki bir sürümüne geri dönmeniz gerekirse, Otomasyon hesabınızdan silebilirsiniz. Bir Otomasyon hesabı oluşturduğunuzda içeri aktarılan [varsayılan modüllerin](#default-modules) orijinal sürümlerini silemezsiniz. Silinecek modül [varsayılan modüllerden](#default-modules)birinin daha yeni bir sürümüdür, Otomasyon hesabınızla yüklenen sürüme geri döner. Aksi takdirde, Otomasyon hesabınızdan sildiğiniz herhangi bir modül kaldırılır.

### <a name="delete-modules-in-the-azure-portal"></a>Azure portal modül silme

Azure portal bir modülü kaldırmak için:

1. Otomasyon hesabınıza gidin. **Paylaşılan kaynaklar** altında **modüller**' i seçin.
2. Kaldırmak istediğiniz modülü seçin.
3. Modül sayfasında **Sil**' i seçin. Bu modül [varsayılan modüllerden](#default-modules)biri Ise, Otomasyon hesabı oluşturulduğunda var olan sürüme geri döner.

### <a name="delete-modules-by-using-powershell"></a>PowerShell kullanarak modülleri silme

Bir modülü PowerShell aracılığıyla kaldırmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure PowerShell modüllerini kullanma hakkında daha fazla bilgi için bkz. [Azure PowerShell kullanmaya başlama](/powershell/azure/get-started-azureps).

* PowerShell modülleri oluşturma hakkında daha fazla bilgi için bkz. [Windows PowerShell modülü yazma](/powershell/scripting/developer/module/writing-a-windows-powershell-module).
