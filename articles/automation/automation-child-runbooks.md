---
title: Azure Otomasyonu 'nda modüler runbook 'lar oluşturma
description: Bu makalede, başka bir runbook tarafından çağrılan bir runbook 'un nasıl oluşturulacağı açıklanmaktadır.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 101ff9affe43dcc97de6cf5a535c82559aafeced
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834921"
---
# <a name="create-modular-runbooks"></a>Modüler runbook’lar oluşturma

Başka runbook 'lar tarafından çağrılan ayrı bir işlevle yeniden kullanılabilir, modüler runbook 'lar yazmak için Azure Otomasyonu 'nda önerilen bir uygulamadır. Bir üst runbook, gerekli işlevselliği gerçekleştirmek için genellikle bir veya daha fazla alt runbook 'u çağırır. 

Alt runbook 'u bulmanın iki yolu vardır ve senaryolarınız için en uygun olanı belirleyebilmek için anlamanız gereken ayrı farklılıklar vardır. Aşağıdaki tabloda, bir runbook 'u diğerinden çağırmak için iki yol arasındaki farklar özetlenmektedir.

|  | Satır içi | Cmdlet |
|:--- |:--- |:--- |
| **İş** |Alt runbook'lar üst runbook'la aynı işte çalışır. |Alt runbook için ayrı bir iş oluşturulur. |
| **Yürütme** |Üst runbook devam etmeden önce alt runbook'un tamamlanmasını bekler. |Üst runbook, alt runbook başlatıldıktan hemen sonra devam eder *veya* üst Runbook alt işin bitmesini bekler. |
| **Çıktı** |Üst runbook doğrudan alt runbook'tan çıkış alabilir. |Üst Runbook alt runbook işinden çıkış almalıdır *veya* üst runbook, alt runbook 'tan doğrudan çıkış alabilir. |
| **Parametreler** |Alt runbook parametre değerleri ayrı ayrı belirtilir ve herhangi bir veri türünü kullanabilir. |Alt runbook parametrelerinin değerlerinin tek bir Hashtable 'da birleştirilmesi gerekir. Bu Hashtable yalnızca JSON serileştirmesi kullanan basit, dizi ve nesne veri türlerini içerebilir. |
| **Otomasyon Hesabı** |Üst runbook aynı Otomasyon hesabında yalnızca alt runbook 'u kullanabilir. |Üst runbook 'lar herhangi bir Otomasyon hesabından, aynı Azure aboneliğinden ve hatta bir bağlantınızın olduğu farklı bir abonelikte bulunan bir alt runbook 'u kullanabilir. |
| **Yayımlama** |Üst runbook yayımlanmadan önce alt runbook yayımlanmalıdır. |Alt runbook, üst runbook başlatılmadan önce yayımlanır. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Satır içi yürütme kullanarak alt runbook 'u çağırma

Başka bir runbook 'tan bir runbook 'u satır içi olarak çağırmak için, bir etkinlik veya cmdlet kullandığınız gibi, runbook 'un adını kullanın ve parametreleri için değerler sağlayın. Aynı Otomasyon hesabındaki tüm runbook 'lar bu şekilde kullanılmak üzere tüm diğerleri tarafından kullanılabilir. Üst Runbook bir sonraki satıra geçmeden önce alt runbook 'un tamamlanmasını bekler ve herhangi bir çıkış doğrudan üst öğeye geri döner.

Bir runbook'u satır içi olarak çağırdığınızda üst runbook ile aynı işi çalıştırır. Alt runbook 'un iş geçmişinde hiçbir belirti yoktur. Alt runbook 'tan gelen tüm özel durumlar ve akış çıkışları üst öğeyle ilişkilendirilir. Bu davranış, daha az iş ile sonuçlanır ve izleme ve sorun giderme işlemlerini kolaylaştırır.

Bir runbook yayımlandığında, çağırdığı tüm alt runbook 'lar zaten yayımlanmalıdır. Bunun nedeni, Azure Otomasyonu 'nun runbook 'u derlediğinde herhangi bir alt runbook ile ilişkilendirme oluşturmasının nedenidir. Alt runbook 'lar zaten yayımlanmamışsa, üst runbook düzgün şekilde yayınla görünür ancak başlatıldığında bir özel durum oluşturur. Bu durumda, alt runbook 'lara doğru şekilde başvurmak için üst runbook 'u yeniden yayımlayabilirsiniz. İlişki zaten oluşturulduğundan, herhangi bir alt runbook değiştirilirse üst runbook 'u yeniden yayımlamanız gerekmez.

Satır içi olarak adlandırılan bir alt runbook 'un parametreleri karmaşık nesneler de dahil olmak üzere herhangi bir veri türünde olabilir. Azure portal veya [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 'ini kullanarak runbook 'u başlattığınızda olduğu gibi, [JSON serileştirme](start-runbooks.md#work-with-runbook-parameters)yoktur.

### <a name="runbook-types"></a>Runbook türleri

Hangi runbook türleri birbirini çağırabilirler?

* Bir [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 'u ve bir [grafik runbook](automation-runbook-types.md#graphical-runbooks) , her ikisi de PowerShell tabanlı olduğundan, her bir satır içi her satırı çağırabilir.
* PowerShell iş akışı [runbook](automation-runbook-types.md#powershell-workflow-runbooks) 'u ve bir grafik PowerShell iş akışı runbook 'u, her Ikisi de PowerShell iş akışı tabanlı olan bir satır içi olarak çağırabilir.
* PowerShell türleri ve PowerShell Iş akışı türleri birbirini her satır içi çağırabilir ve kullanması gerekir `Start-AzAutomationRunbook` .

Siparişi ne zaman yayımlasın?

Runbook 'ların yayımlama sırası yalnızca PowerShell Iş akışı ve grafik PowerShell Iş akışı runbook 'ları için önemlidir.

Runbook 'unuza satır içi yürütme kullanarak bir grafik veya PowerShell Iş akışı alt runbook 'u çağırdığında, runbook 'un adını kullanır. `.\\`Betiğin yerel dizinde bulunduğunu belirtmek için adının ile başlaması gerekir.

### <a name="example"></a>Örnek

Aşağıdaki örnek, bir karmaşık nesne, bir tamsayı değeri ve bir Boolean değer kabul eden bir test alt runbook 'u başlatır. Alt runbook'un çıkışı bir değişkene atanır. Bu durumda, alt Runbook bir PowerShell Iş akışı runbook 'ıdır.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

Aşağıda, alt olarak bir PowerShell runbook 'u kullanan aynı örnek verilmiştir.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Cmdlet kullanarak bir alt runbook 'u başlatma

> [!IMPORTANT]
> Runbook 'unuza cmdlet 'i parametresine sahip bir alt runbook 'u çağırır `Start-AzAutomationRunbook` `Wait` ve alt Runbook bir nesne sonucu üretirse, işlem bir hatayla karşılaşabilir. Hataya geçici bir çözüm bulmak için [Get-Azautomationjoi Putrecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet 'ini kullanarak sonuçları yoklamaya yönelik mantığı nasıl uygulayacağınızı öğrenmek için [nesne çıkışı ile alt runbook 'lara](troubleshoot/runbooks.md#child-runbook-object) bakın.

`Start-AzAutomationRunbook` [Windows PowerShell ile bir runbook başlatmak için](start-runbooks.md#start-a-runbook-with-powershell)bölümünde açıklandığı gibi bir runbook 'u başlatmak için ' i kullanabilirsiniz. Bu cmdlet için iki mod kullanılması vardır. Tek modda, cmdlet alt runbook için iş oluşturulduğunda iş KIMLIĞINI döndürür. Betiğinizin bekleme parametresini belirterek izin *vermesini* sağlayan diğer modda, cmdlet alt iş bitene kadar bekler ve alt runbook 'tan çıkışı döndürür.

Alt runbook 'tan bir cmdlet ile başlatılan iş, üst runbook işinden ayrı olarak çalışır. Bu davranış, runbook 'un satır içi başlatılmasına kıyasla daha fazla iş sağlar ve işlerin izlenmesini zorlaştırır. Üst öğe, her birinin tamamlanmasını beklemeden, zaman uyumsuz olarak birden fazla alt runbook başlatabilir. Alt runbook 'ların satır içi olarak çağrılması bu paralel yürütme için, üst runbook 'un [Parallel anahtar sözcüğünü](automation-powershell-workflow.md#use-parallel-processing)kullanması gerekir.

Zamanlama nedeniyle alt Runbook çıkışı üst runbook 'a güvenilir bir şekilde geri dönmez. Ayrıca, ve gibi değişkenler `$VerbosePreference` `$WarningPreference` alt runbook 'lara yayılmayabilir. Bu sorunlardan kaçınmak için, alt runbook 'ları parametresiyle kullanarak ayrı otomasyon işleri olarak başlatabilirsiniz `Start-AzAutomationRunbook` `Wait` . Bu teknik, alt runbook tamamlanana kadar üst runbook 'u engeller.

Bekleme sırasında üst runbook 'un engellenmesini istemiyorsanız, alt runbook 'u `Start-AzAutomationRunbook` parametresi olmadan kullanarak başlatabilirsiniz `Wait` . Bu durumda, runbook 'unun iş tamamlanmasını beklemek için [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) kullanması gerekir. Sonuçları almak için [Get-Azautomationjoi put](/powershell/module/az.automation/get-azautomationjoboutput) ve [Get-Azautomationjoi putrecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) ' i de kullanmalıdır.

Bir cmdlet ile başlatılan bir alt runbook için parametreler [runbook parametreleri](start-runbooks.md#work-with-runbook-parameters)bölümünde açıklandığı gibi karma tablosu olarak sağlanır. Yalnızca basit veri türleri kullanılabilir. Runbook karmaşık veri türü içeren bir parametreye sahipse satır içi olarak çağrılmalıdır.

Alt runbook 'lar ayrı işler olarak başlatıldığında abonelik bağlamı kaybolabilir. Alt runbook 'un belirli bir Azure aboneliğine karşı az Module cmdlet 'leri yürütmesi için, alt öğenin bu abonelikte üst runbook 'tan bağımsız olarak kimlik doğrulaması gerekir.

Aynı Otomasyon hesabı içindeki işler birden fazla abonelikle çalışıyorsa, bir iş içinde bir abonelik seçmek diğer işler için o anda seçili olan abonelik bağlamını değiştirebilir. Bu durumdan kaçınmak için, `Disable-AzContextAutosave -Scope Process` her runbook 'un başlangıcında kullanın. Bu eylem yalnızca bu runbook yürütmesinin bağlamını kaydeder.

### <a name="example"></a>Örnek

Aşağıdaki örnek, parametreleri olan bir alt runbook başlatır ve sonra `Start-AzAutomationRunbook` parametresiyle cmdlet 'ini kullanarak tamamlanmasını bekler `Wait` . Tamamlandıktan sonra örnek, alt runbook 'tan cmdlet çıkışını toplar. Kullanmak için `Start-AzAutomationRunbook` , komut dosyasının Azure aboneliğinizde kimlik doğrulaması yapması gerekir.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    -Parameters $params -Wait
```

## <a name="next-steps"></a>Sonraki adımlar

* Runbook 'unuzu çalıştırmayı çalıştırmak için bkz. [Azure Otomasyonu 'nda runbook başlatma](start-runbooks.md).
* Runbook işleminin izlenmesi için bkz. [Azure Otomasyonu 'Nda Runbook çıkışı ve iletileri](automation-runbook-output-and-messages.md).
