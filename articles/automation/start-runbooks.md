---
title: Azure Otomasyonu'nda Runbook başlatma
description: Bu makalede, Azure Otomasyonu 'nda bir runbook 'u başlatma açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 01b6e060fcab9c7dab4934aad3d1ab6047ec5236
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829863"
---
# <a name="start-a-runbook-in-azure-automation"></a>Azure Otomasyonu'nda Runbook başlatma

Aşağıdaki tablo, belirli senaryonuz için en uygun olan Azure Otomasyonu 'nda bir runbook 'u başlatmak için yöntemi belirlemenize yardımcı olur. Bu makale, Azure portal ve Windows PowerShell ile bir runbook başlatma hakkındaki ayrıntıları içerir. Diğer yöntemler hakkındaki ayrıntılar, aşağıdaki bağlantılardan erişebileceğiniz diğer belgelerde sunulmaktadır.

| **Yöntem** | **Özellikler** |
| --- | --- |
| [Azure portalı](#start-a-runbook-with-the-azure-portal) |<li>Etkileşimli kullanıcı arabirimiyle en basit yöntem.<br> <li>Basit parametre değerleri sağlamak için form.<br> <li>İş durumunu kolayca izleyin.<br> <li>Azure oturum açma ile erişim kimliği doğrulandı. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Windows PowerShell cmdlet 'leri ile komut satırından çağrı.<br> <li>, Birden çok adımla otomatikleştirilmiş bir özelliğe eklenebilir.<br> <li>İstek sertifika veya OAuth Kullanıcı sorumlusu/hizmet sorumlusu ile doğrulandı.<br> <li>Basit ve karmaşık parametre değerlerini sağlayın.<br> <li>İş durumunu izleyin.<br> <li>PowerShell cmdlet 'lerini desteklemek için gereken istemci. |
| [Azure Otomasyonu API 'SI](/rest/api/automation/) |<li>En esnek yöntem, ancak çoğu karmaşıktır.<br> <li>HTTP istekleri yapan herhangi bir özel koddan çağırın.<br> <li>Sertifika veya OAuth Kullanıcı sorumlusu/hizmet sorumlusu ile kimlik doğrulaması iste.<br> <li>Basit ve karmaşık parametre değerlerini sağlayın. *API 'yi kullanarak bir Python runbook 'u arıyorsanız JSON yükünün serileştirilmesi gerekir.*<br> <li>İş durumunu izleyin. |
| [Web Kancaları](automation-webhooks.md) |<li>Tek bir HTTP isteğinden runbook 'u başlatın.<br> <li>URL 'de güvenlik belirteci ile kimlik doğrulandı.<br> <li>İstemci, Web kancası oluşturulduğunda belirtilen parametre değerlerini geçersiz kılamaz. Runbook, HTTP istek ayrıntıları ile doldurulmuş tek bir parametre tanımlayabilir.<br> <li>Web kancası URL 'SI aracılığıyla iş durumunu izleyebilme yeteneği yoktur. |
| [Azure uyarısına yanıt verme](../azure-monitor/alerts/alerts-overview.md) |<li>Azure uyarısına yanıt olarak bir runbook başlatın.<br> <li>Runbook için Web kancasını yapılandırın ve uyarı bağlantısı yapın.<br> <li>URL 'de güvenlik belirteci ile kimlik doğrulandı. |
| [Zamanla](./shared-resources/schedules.md) |<li>Runbook 'u saatlik, günlük, haftalık veya aylık zamanlamaya göre otomatik olarak başlatın.<br> <li>Azure portal, PowerShell cmdlet 'leri veya Azure API aracılığıyla zamanlamayı işleme.<br> <li>Zamanlamaya göre kullanılacak parametre değerlerini sağlayın. |
| [Başka bir runbook 'tan](automation-child-runbooks.md) |<li>Runbook 'u başka bir runbook 'ta etkinlik olarak kullanın.<br> <li>Birden çok runbook tarafından kullanılan işlevsellik için faydalıdır.<br> <li>Alt runbook 'a parametre değerleri sağlayın ve üst runbook 'ta çıktıyı kullanın. |

Aşağıdaki görüntüde, bir runbook 'un yaşam döngüsünde ayrıntılı adım adım işlem gösterilmektedir. Bu, bir runbook 'un Azure Otomasyonu 'nda başladığı farklı yollarını içerir. Bu, karma Runbook Worker için gereken bileşenleri Azure Otomasyonu runbook 'larını ve farklı bileşenler arasında etkileşimleri yürütmek için gereklidir. Veri merkezinizde Otomasyon Runbook 'larını yürütme hakkında bilgi edinmek için [karma runbook çalışanlarına](automation-hybrid-runbook-worker.md) bakın

![Runbook mimarisi](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Runbook parametreleriyle çalışma

Azure portal veya Windows PowerShell 'den bir runbook başlattığınızda, yönerge Azure Otomasyonu web hizmeti aracılığıyla gönderilir. Bu hizmet, karmaşık veri türleriyle parametreleri desteklemiyor. Karmaşık bir parametre için bir değer sağlamanız gerekiyorsa, bunu [Azure Otomasyonu 'Nda alt runbook 'lar](automation-child-runbooks.md)bölümünde açıklandığı gibi başka bir runbook 'tan satır içi olarak çağırmanız gerekir.

Azure Otomasyonu web hizmeti, aşağıdaki bölümlerde açıklandığı gibi belirli veri türlerini kullanarak parametreler için özel işlevler sağlar:

### <a name="named-values"></a>Adlandırılmış değerler

Parametre, [Object] veri türünde ise, adlandırılmış değerlerin bir listesini göndermek için şu JSON biçimini kullanabilirsiniz: *{name1: ' değer1 ', AD2: ' değer2 ', Name3: ' Value3 '}*. Bu değerler basit türler olmalıdır. Runbook, parametreyi her bir adlandırılmış değere karşılık gelen özelliklerle birlikte bir [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) olarak alır.

kullanıcıadlı bir parametreyi kabul eden aşağıdaki sınama runbook’u göz önünde bulundurun.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Aşağıdaki metin kullanıcı parametresi için kullanılabilir.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Bu, aşağıdaki çıkışa neden olur:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Diziler

Parametre, [dizi] veya [String []] gibi bir diziyse, değerlerin bir listesini göndermek için şu JSON biçimini kullanabilirsiniz: *[değer1, değer2, Value3]*. Bu değerler basit türler olmalıdır.

*kullanıcı* adlı bir parametreyi kabul eden aşağıdaki sınama runbook’u göz önünde bulundurun.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Aşağıdaki metin kullanıcı parametresi için kullanılabilir.

```input
["Joe","Smith",2,true]
```

Bu, aşağıdaki çıkışa neden olur:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Kimlik bilgileri

Parametre veri türü ise `PSCredential` , bir Azure Otomasyonu [kimlik bilgisi varlığının](./shared-resources/credentials.md)adını sağlayabilirsiniz. Runbook, kimlik bilgisini belirttiğiniz adla alır. Aşağıdaki test runbook 'u adlı bir parametreyi kabul eder `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Aşağıdaki metin, adlı bir kimlik bilgisi varlığı olduğunu varsayarak Kullanıcı parametresi için kullanılabilir `My Credential` .

```input
My Credential
```

Kimlik bilgilerinde kullanıcı adının nerede olduğunu varsayarsak `jsmith` , aşağıdaki çıktı görüntülenir.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Azure portal bir runbook 'u başlatma

1. Azure portal, **Otomasyon** ' u seçin ve ardından bir Otomasyon hesabının adına tıklayın.
2. Hub menüsünde **runbook 'lar**' ı seçin.
3. Runbook 'Lar sayfasında, bir runbook seçin ve ardından **Başlat**' a tıklayın.
4. Runbook 'un parametreleri varsa her parametre için bir metin kutusuyla birlikte değerler sağlamanız istenir. Parametreler hakkında daha fazla bilgi için bkz. [runbook parametreleri](#work-with-runbook-parameters).
5. Iş bölmesinde, runbook işinin durumunu görüntüleyebilirsiniz.

## <a name="start-a-runbook-with-powershell"></a>PowerShell ile runbook başlatma

Windows PowerShell ile bir runbook başlatmak için [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) 'u kullanabilirsiniz. Aşağıdaki örnek kod, **Test-runbook** adlı bir runbook 'u başlatır.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` Runbook başlatıldıktan sonra durumu izlemek için kullanabileceğiniz bir iş nesnesi döndürür. Daha sonra bu iş nesnesini [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) ile birlikte kullanarak, işinin durumunu belirleyebilir ve çıktısını almak için [Get-Azautomationjoi put](/powershell/module/az.automation/get-azautomationjoboutput) ' i kullanabilirsiniz. Aşağıdaki örnek, **Test-runbook** adlı bir runbook başlatır, tamamlanmasını bekler ve ardından çıktısını görüntüler.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook -AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup -Stream Output
```

Runbook için parametreler gerekiyorsa, bunları bir [Hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables)olarak belirtmeniz gerekir. Hashtable 'ın anahtarı parametre adıyla ve değer parametre değeri olmalıdır. Aşağıdaki örnek; FirstName ve LastName adlı iki dize parametresi, RepeatCount adlı bir tamsayı ve Show adlı bir boolean parametresiyle bir runbook’u nasıl çalıştıracağınızı gösterir. Parametreler hakkında daha fazla bilgi için bkz. [runbook parametreleri](#work-with-runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" -Parameters $params
```

## <a name="next-steps"></a>Sonraki adımlar

* Runbook yönetimi hakkında ayrıntılı bilgi için bkz. [Azure Otomasyonu 'nda runbook 'Ları yönetme](manage-runbooks.md).
* PowerShell ayrıntıları için bkz. [PowerShell belgeleri](/powershell/scripting/overview).
* Runbook yürütmeyle ilgili sorunları gidermek için bkz. [runbook sorunlarını giderme](troubleshoot/runbooks.md).
