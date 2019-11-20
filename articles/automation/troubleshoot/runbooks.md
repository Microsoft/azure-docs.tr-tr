---
title: Azure Otomasyonu runbook 'Ları sorunlarını giderme
description: Azure Otomasyonu runbook 'larla ilgili sorunları nasıl giderebileceğinizi öğrenin
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 326a2dbf644cd5792a45dbf40d9574982053aae1
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743905"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook 'larda hatalarda sorun giderme

Azure Otomasyonu 'nda runbook yürütmekte olan hatalar olduğunda, sorunu tanılamanıza yardımcı olması için aşağıdaki adımları kullanabilirsiniz.

1. **Runbook betiğinizin yerel makinenizde başarıyla yürütüldüğünden emin olun:**  Dil başvurusu ve öğrenme modülleri için [PowerShell belgeleri](/powershell/scripting/overview) veya [Python belgeleri](https://docs.python.org/3/) bölümüne bakın.

   Komut dosyanızı yerel olarak yürütmek, şu gibi yaygın hataları bulabilir ve çözümleyebilir:

   - **Eksik modüller**
   - **Sözdizimi hataları**
   - **Mantık hataları**

2. **Runbook 'U araştır** belirli iletiler için [hata akışları](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) ve bunları aşağıdaki hatalarla karşılaştırın.

3. **Düğümlerinizin ve Otomasyon çalışma alanınızın gerekli modüllere sahip olduğundan emin olun:** Runbook 'ünüz herhangi bir modülü içeri aktardığında, [Içeri aktarma modülleri](../shared-resources/modules.md#import-modules)' nde listelenen adımları kullanarak Otomasyon hesabınızda kullanılabilir olduklarından emin olun. [Azure Otomasyonu 'Nda Azure modüllerini güncelleştirme](..//automation-update-azure-modules.md)bölümündeki yönergeleri izleyerek modüllerinizi en son sürüme güncelleştirin. Daha fazla sorun giderme bilgisi için bkz. [Modül sorunlarını giderme](shared-resources.md#modules).

Runbook 'U askıya alındı veya beklenmedik şekilde başarısız olduysa:

* [Iş durumlarını denetleme](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) runbook durumlarını ve olası nedenleri tanımlar.
* Runbook 'un askıya alınmadan önce ne olacağını belirlemek için Runbook 'a [ek çıkış ekleyin](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) .
* İşiniz tarafından oluşturulan [tüm özel durumları işleyin](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) .

## <a name="login-azurerm"></a>Senaryon Oturum açmak için login-AzureRMAccount komutunu çalıştırın

### <a name="issue"></a>Sorun

Bir runbook yürütürken aşağıdaki hatayı alıyorsunuz:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Nedeni

Bu hata RunAs hesabı kullanmadığınızda veya RunAs hesabının süresi dolduğunda oluşabilir. Bkz. [Azure Otomasyonu runas hesaplarını yönetme](https://docs.microsoft.com/azure/automation/manage-runas-account).

Bu hatanın iki birincil nedeni vardır:

* Azurerd modüllerinin farklı sürümleri.
* Kaynaklara ayrı bir abonelikte erişmeye çalışıyorsunuz.

### <a name="resolution"></a>Çözüm

Bir Azurere modülünü güncelleştirdikten sonra bu hatayı alırsanız, tüm Azurere modüllerinizi aynı sürüme güncelleştirmeniz gerekir.

Başka bir abonelikteki kaynaklara erişmeye çalışıyorsanız, izinleri yapılandırmak için aşağıdaki adımları izleyebilirsiniz.

1. Otomasyon hesabının farklı Çalıştır hesabına gidin ve uygulama KIMLIĞINI ve parmak izini kopyalayın.
  ![Uygulama KIMLIĞINI ve parmak Izini Kopyala](../media/troubleshoot-runbooks/collect-app-id.png)
1. Otomasyon hesabının barındırılmadığını ve yeni bir rol ataması eklemek için abonelik Access Control gidin.
  ![Erişim denetimi](../media/troubleshoot-runbooks/access-control.png)
1. Önceki adımda topladığınız uygulama KIMLIĞI ' ni ekleyin. Katkıda bulunan izinleri seçin.
   ![Rol ataması Ekle](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Sonraki adım için aboneliğin adını kopyalayın.
1. Artık Otomasyon hesabınızdaki izinleri diğer aboneliğe test etmek için aşağıdaki runbook kodunu kullanabilirsiniz.

    "\<CertificateThumbprint\>" değerini adım #1 ve adım #4 kopyaladığınız "\<subscriptionName\>" değeri ile değiştirin.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="unable-to-find-subscription"></a>Senaryon Azure aboneliği bulunamıyor

### <a name="issue"></a>Sorun

`Select-AzureSubscription` Veya`Select-AzureRmSubscription` cmdlet 'leriyle çalışırken şu hatayı alırsınız:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Hata

Bu hata şu durumlarda oluşabilir:

* Abonelik adı geçerli değil

* Abonelik ayrıntılarını almaya çalışan Azure Active Directory Kullanıcı, aboneliğin Yöneticisi olarak yapılandırılmamıştır.

### <a name="resolution"></a>Çözüm

Azure 'da kimlik doğrulaması yapılıp kalmadığını ve seçmek istediğiniz aboneliğe erişimi olup olmadığınızı belirlemek için aşağıdaki adımları uygulayın:

1. Tek başına çalıştığından emin olmak için, betiğinizi Azure Otomasyonu dışında test edin.
2. Cmdlet`Select-AzureSubscription` 'ini çalıştırmadan önce `Add-AzureAccount` cmdlet 'i çalıştırdığınızdan emin olun.
3. Runbook `Disable-AzureRmContextAutosave –Scope Process` 'un başlangıcına ekleyin. Bu cmdlet, tüm kimlik bilgilerinin yalnızca geçerli runbook 'un yürütülmesine uygulanmasını sağlar.
4. Hala bu hata iletisini görüyorsanız, `Add-AzureAccount` cmdlet 'ini izleyen **azurermcontext** parametresini ekleyerek kodunuzu değiştirin ve ardından kodu yürütün.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>Senaryon Multi-Factor Authentication etkinleştirildiğinden Azure kimlik doğrulaması başarısız oldu

### <a name="issue"></a>Sorun

Azure Kullanıcı adı ve parolanızla Azure 'da kimlik doğrulanırken aşağıdaki hatayı alırsınız:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Nedeni

Azure hesabınızda Multi-Factor Authentication varsa, Azure 'da kimlik doğrulaması yapmak için bir Azure Active Directory Kullanıcı kullanamazsınız. Bunun yerine, Azure 'da kimlik doğrulaması yapmak için bir sertifika veya hizmet sorumlusu kullanmanız gerekir.

### <a name="resolution"></a>Çözüm

Azure klasik dağıtım modeli cmdlet 'leriyle bir sertifika kullanmak için [Azure hizmetlerini yönetmek üzere bir sertifika oluşturma ve ekleme](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) bölümüne bakın. Azure Resource Manager cmdlet 'leriyle hizmet sorumlusu kullanmak için, [Azure Portal kullanarak hizmet sorumlusu oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md) ve [Azure Resource Manager ile hizmet sorumlusu kimlik doğrulama](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) konusuna bakın.

## <a name="get-serializationsettings"></a>Senaryon Get_SerializationSettings yöntemi hakkında iş akışlarınızdan bir hata görürsünüz

### <a name="issue"></a>Sorun

Bir runbook için İş akışlarınızın hata halinde şu iletiyi görürsünüz:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Nedeni

Bu hata, bir runbook 'ta hem Azurerd hem de az cmdlet 'ler kullanılarak oluşur. İçeri aktarmadan `Az` `AzureRM`önce içeri aktardığınızda oluşur.

### <a name="resolution"></a>Çözüm

Az ve Azurerd cmdlet 'leri aynı runbook 'ta içeri aktarılamaz ve kullanılamaz. Azure Otomasyonu 'nda az destek hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu 'Nda az Module support](../az-modules.md).

## <a name="task-was-cancelled"></a>Senaryon Runbook hata vererek başarısız olur: Bir görev iptal edildi

### <a name="issue"></a>Sorun

Runbook 'larınız aşağıdaki örneğe benzer bir hata ile başarısız olur:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Nedeni

Bu hata, eski Azure modülleri kullanılarak kaynaklanıyor olabilir.

### <a name="resolution"></a>Çözüm

Bu hata, Azure modüllerinizi en son sürüme güncelleştirerek çözülebilir.

Otomasyon hesabınızda **modüller**' e tıklayın ve **Azure modüllerini Güncelleştir**' e tıklayın. Güncelleştirme yaklaşık 15 dakika sürer, tamamlandıktan sonra başarısız olan runbook 'u yeniden çalıştırın. Modüllerinizi güncelleştirme hakkında daha fazla bilgi için bkz. Azure [Otomasyonu 'Nda Azure modüllerini güncelleştirme](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Senaryon Birden çok abonelikle ilgilenirken runbook 'lar başarısız olur

### <a name="issue"></a>Sorun

Runbook 'lar çalıştırılırken, runbook Azure kaynaklarını yönetemez.

### <a name="cause"></a>Nedeni

Runbook çalışırken doğru bağlamı kullanmıyor.

### <a name="resolution"></a>Çözüm

Birden çok abonelikle çalışırken, runbook 'lar çağrılırken abonelik içeriği kaybolmuş olabilir. Abonelik bağlamının runbook 'lara geçirildiğinden emin olmak için cmdlet 'ine `AzureRmContext` parametreyi ekleyin ve bağlamı ona geçirin. Ayrıca, kullandığınız kimlik bilgilerinin yalnızca geçerli `Disable-AzureRmContextAutosave` runbook için kullanıldığından emin olmak için cmdlet 'ini **işlem** kapsamıyla kullanmanız önerilir.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

Daha fazla bilgi için bkz. [birden çok abonelikle çalışma](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Senaryon Terim, bir cmdlet 'in adı olarak tanınmıyor, işlev, komut dosyası

### <a name="issue"></a>Sorun

Runbook 'larınız aşağıdaki örneğe benzer bir hata ile başarısız olur:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Nedeni

Bu hata, aşağıdaki nedenlerden biri temel alınarak oluşabilir:

* Cmdlet 'ini içeren modül Otomasyon hesabına içeri aktarılmaz
* Cmdlet 'i içeren modül içeri aktarıldı, ancak güncel değil

### <a name="resolution"></a>Çözüm

Bu hata, aşağıdaki görevlerden biri tamamlanarak çözülebilir:

Modül bir Azure modülüdür, Otomasyon hesabınızda modüllerinizi güncelleştirme hakkında bilgi edinmek için bkz. [Azure Automation 'da Azure PowerShell modüllerini güncelleştirme](../automation-update-azure-modules.md) .

Ayrı bir modüldür, Bu modülün Otomasyon hesabınızda içeri aktarıldığından emin olun.

## <a name="job-attempted-3-times"></a>Senaryon Runbook işinin başlaması üç kez denendi, ancak her seferinde başlatılamadı

### <a name="issue"></a>Sorun

Runbook 'larınız şu hata ile başarısız oluyor:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Nedeni

Bu hata, aşağıdaki sorunlardan biri nedeniyle oluşur:

* Bellek sınırı. Bir korumalı alana ne kadar bellek ayrılacağını gösteren belgelenen sınırlar, [Otomasyon Hizmeti sınırları](../../azure-subscription-service-limits.md#automation-limits)' nde bulunur. Bir iş, 400 MB 'tan fazla bellek kullanıyorsa bu işlem başarısız olabilir.

* Ağ yuvaları. Azure korumalı alanlar, [Otomasyon Hizmeti sınırları](../../azure-subscription-service-limits.md#automation-limits)bölümünde açıklandığı şekilde 1000 eşzamanlı ağ yuvası ile sınırlıdır.

* Modül uyumsuz. Bu hata, modül bağımlılıkları doğru değilse ve yoksa, runbook 'unuz genellikle bir "komut bulunamadı" veya "parametre bağlanamaz" iletisi döndürür.

* Runbook 'iniz, bir Azure korumalı alanında çalışan bir runbook 'ta bir çalıştırılabilir veya alt işlem çağırmaya çalıştı. Bu senaryo, Azure korumalı alanında desteklenmez.

* Runbook, çıkış akışına çok fazla özel durum verisi yazmayı denedi.

### <a name="resolution"></a>Çözüm

Aşağıdaki çözümlerden herhangi biri sorunu çözer:

* Bellek sınırı içinde çalışmak için önerilen yöntemler, iş yükünü birden çok runbook arasında bölmek, runbook 'ınızdan gereksiz çıkış yazmak değil veya PowerShell Iş akışınıza kaç denetim noktası yazılacağını göz önünde bulundurmaya yönelik bir işlemdir. 'ları. Değişkeni temizlemek ve çöp toplamayı hemen çalıştırmak için kullanmak `$myVar.clear()` `[GC]::Collect()` gibi Clear metodunu kullanabilirsiniz. Bu eylemler çalışma zamanı sırasında runbook 'larınızın bellek parmak izini azaltır.

* Azure [Otomasyonu 'nda Azure PowerShell modüllerini güncelleştirme](../automation-update-azure-modules.md)adımlarını izleyerek Azure modüllerinizi güncelleştirin.

* Başka bir çözüm de runbook 'u [karma Runbook Worker](../automation-hrw-run-runbooks.md)üzerinde çalıştıralım. Karma çalışanlar, Azure sanal ağınızın bulunduğu bellek ve ağ sınırları ile sınırlı değildir.

* Bir runbook 'ta bir işlemi (örneğin,. exe veya alt işlem. Call) çağırmanız gerekiyorsa, runbook 'u [karma Runbook Worker](../automation-hrw-run-runbooks.md)üzerinde çalıştırmanız gerekir.

* İş çıktı akışında bir 1 MB sınırı vardır. Bir try/catch bloğunda bir çalıştırılabilir veya alt işleme çağrıları aldığınızdan emin olun. Özel durum oluşturduklarında, bu özel durumdan bir Otomasyon değişkenine iletiyi yazın. Bu, iş çıkış akışına yazılmasını engeller.

## <a name="sign-in-failed"></a>Senaryon Azure hesabında oturum açılamadı

### <a name="issue"></a>Sorun

`Add-AzureAccount` Veya`Connect-AzureRmAccount` cmdlet 'leriyle çalışırken aşağıdaki hatalardan birini alırsınız:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Nedeni

Kimlik bilgisi varlık adı geçerli değilse bu hata oluşur. Bu hata, Otomasyon kimlik bilgisi varlığını ayarlamak için kullandığınız Kullanıcı adı ve parola geçerli değilse da oluşabilir.

### <a name="resolution"></a>Çözüm

Neyin yanlış olduğunu belirlemek için aşağıdaki adımları uygulayın:

1. Özel karakter olmadığından emin olun. Bu karakterler, Azure **\@** 'a bağlanmak için kullandığınız Otomasyon kimlik bilgileri varlık adındaki karakteri içerir.
2. Yerel PowerShell ıSE düzenleyicinizdeki Azure Otomasyonu kimlik bilgilerinde depolanan kullanıcı adını ve parolayı kullanıp kullanabileceğini denetleyin. PowerShell ıSE 'de aşağıdaki cmdlet 'leri çalıştırarak Kullanıcı adının ve parolanın doğru olup olmadığını kontrol edebilirsiniz:

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Kimlik doğrulama işlemi yerel olarak başarısız olursa, Azure Active Directory kimlik bilgilerinizi düzgün bir şekilde kurmadığınız anlamına gelir. Azure Active Directory hesabını doğru bir şekilde ayarlamak için Azure Active Directory blog gönderisini [kullanarak Azure 'Da kimlik doğrulaması](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) yapın bölümüne bakın.

4. Geçici bir hata gibi görünüyorsa, kimlik doğrulamasının daha sağlam olması için kimlik doğrulama yordamınızın yeniden deneme mantığını eklemeyi deneyin.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="child-runbook-object"></a>Nesne başvurusu bir nesne örneğine ayarlanmadı

### <a name="issue"></a>Sorun

`-Wait` Anahtar ile bir alt runbook çağrılırken aşağıdaki hatayı alıyorsunuz ve çıkış akışı bir nesne içeriyor:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Nedeni

[Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) 'un, nesneler içeriyorsa çıkış akışını doğru bir şekilde işlemediği bilinen bir sorun vardır.

### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için, bunun yerine bir yoklama mantığı uygulamanız ve çıktıyı almak için [Get-Azurermautomationjoi put](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) cmdlet 'ini kullanmanız önerilir. Bu mantığın bir örneği aşağıdaki örnekte tanımlanmıştır.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="fails-deserialized-object"></a>Senaryon Seri durumdan çıkarılan nesne nedeniyle runbook başarısız oluyor

### <a name="issue"></a>Sorun

Runbook 'larınız şu hata ile başarısız oluyor:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Nedeni

Runbook 'ağınız bir PowerShell Iş akışıdır, iş akışı askıya alınmışsa runbook 'unuzu kalıcı hale getirmek için karmaşık nesneleri seri durumdan çıkarılmış biçimde depolar.

### <a name="resolution"></a>Çözüm

Aşağıdaki üç çözüm bu sorunu çözer:

* Karmaşık nesneleri bir cmdlet 'ten diğerine yönelterek, bu cmdlet 'leri bir InlineScript içinde sarın.
* Tüm nesneyi geçirmek yerine, ihtiyacınız olan adı veya değeri karmaşık nesneden geçirin.
* PowerShell Iş akışı runbook 'u yerine bir PowerShell runbook 'u kullanın.

## <a name="quota-exceeded"></a>Senaryon Ayrılan kota aşıldığı için Runbook işi başarısız oldu

### <a name="issue"></a>Sorun

Runbook işiniz şu hata ile başarısız oluyor:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Nedeni

Bu hata, iş yürütmesi hesabınız için 500 dakikalık boş kotayı aştığında oluşur. Bu kota, tüm iş yürütme görevi türleri için geçerlidir. Bu görevlerden bazıları, bir işi test ediyor, portaldan bir işi başlatıyor, Web kancaları kullanarak bir işi yürütüyor veya Azure portal veya veri merkezinizde kullanarak yürütülecek bir işi planlıyor olabilir. Otomasyon fiyatlandırması hakkında daha fazla bilgi edinmek için bkz. [Automation fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Çözüm

Ayda 500 dakikadan daha fazla işlem kullanmak istiyorsanız, aboneliğinizi ücretsiz katmandan temel katmana değiştirmeniz gerekir. Aşağıdaki adımları uygulayarak temel katmana yükseltebilirsiniz:

1. Azure aboneliğinizde oturum açın
2. Yükseltmek istediğiniz Otomasyon hesabını seçin
3. **Ayarlar** > **fiyatlandırması**' na tıklayın.
4. Hesabınızı **temel** katmana yükseltmek için sayfada en altta **Etkinleştir** ' e tıklayın.

## <a name="cmdlet-not-recognized"></a>Senaryon Bir runbook çalıştırılırken cmdlet tanınmıyor

### <a name="issue"></a>Sorun

Runbook işiniz şu hata ile başarısız oluyor:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Nedeni

Bu hata, PowerShell motorunun runbook 'inizdeki kullanmakta olduğunuz cmdlet 'i bulamamasının nedeni olur. Bu hata, cmdlet 'ini içeren modülün hesapta eksik olması, runbook adıyla bir ad çakışması olması veya cmdlet 'inin başka bir modülde de mevcut olması veya Otomasyon 'un adı çözemediği nedeni olabilir.

### <a name="resolution"></a>Çözüm

Aşağıdaki çözümlerden herhangi biri sorunu çözer:

* Cmdlet adını doğru girdiğinizden emin olun.
* Cmdlet 'inin Otomasyon hesabınızda bulunduğundan ve çakışma olmadığından emin olun. Cmdlet 'in mevcut olup olmadığını doğrulamak için, düzenleme modunda bir runbook açın ve kitaplıkta bulmak istediğiniz cmdlet 'i arayın veya çalıştırın `Get-Command <CommandName>`. Cmdlet 'in hesap için kullanılabilir olduğunu ve diğer cmdlet 'ler veya Runbook 'larla ad çakışması olmadığını doğrulandıktan sonra, tuvali tuvale ekleyin ve Runbook 'larınızda geçerli bir parametre kümesini kullandığınızdan emin olun.
* Ad çakışması varsa ve cmdlet iki farklı modülde kullanılabiliyorsa, cmdlet için tam adı kullanarak bu sorunu çözebilirsiniz. Örneğin, **Module\\cmdletname**' i kullanabilirsiniz.
* Bir karma çalışan grubunda runbook 'u şirket içinde yürütüyorsanız, modülün ve cmdlet 'inin karma çalışanı barındıran makinede yüklü olduğundan emin olun.

## <a name="long-running-runbook"></a>Senaryon Uzun süre çalışan bir runbook tamamlanamazsa

### <a name="issue"></a>Sorun

Runbook 'larınız 3 saat boyunca çalıştıktan sonra **durdurulmuş** durumda görüntülenir. Ayrıca şu hatayı alabilirsiniz:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Bu davranış, Azure Otomasyonu 'ndaki işlemlerin "dengeli paylaştığı" izlenmesinden dolayı Azure sanal işlemlerinde tasarlanmalıdır. Üç saatten uzun bir süre yürütülüyorsa, dengeli bir şekilde bir runbook otomatik olarak durduruluyor. Ön paylaşma zaman sınırının ötesinde bir runbook 'un durumu runbook türüyle farklıdır. PowerShell ve Python runbook 'lar **durdurulmuş** bir duruma ayarlanır. PowerShell Iş akışı runbook 'ları **başarısız**olarak ayarlanır.

### <a name="cause"></a>Nedeni

Runbook, bir Azure korumalı alanında dengeli bir paylaşıma izin verilen 3 saatlik sınırın üzerinde çalışır.

### <a name="resolution"></a>Çözüm

Önerilen bir çözüm, runbook 'u [karma Runbook Worker](../automation-hrw-run-runbooks.md)üzerinde çalıştıralım.

Hibrit çalışanlar, Azure korumalı 2 ' nin bulunduğu [haksız Share](../automation-runbook-execution.md#fair-share) 3 saat runbook sınırı ile sınırlı değildir. Karma runbook çalışanları üzerinde çalıştırılan runbook 'lar, beklenmeyen yerel altyapı sorunları varsa yeniden başlatma davranışlarını destekleyecek şekilde geliştirilmiştir.

Diğer bir seçenek de runbook 'u [alt runbook 'lar](../automation-child-runbooks.md)oluşturarak iyileştirmenize olanak sağlar. Runbook 'iniz birkaç veritabanında bir veritabanı işlemi gibi birkaç kaynak üzerinde aynı işlevle döngüye geçtiğinde, bu işlevi bir alt runbook 'a taşıyabilirsiniz. Bu alt runbook 'ların her biri ayrı işlemlerde paralel olarak yürütülür. Bu davranış, üst runbook 'un tamamlanacağı toplam süreyi düşürür.

Alt runbook senaryosunu etkinleştiren PowerShell cmdlet 'leri şunlardır:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) -bu cmdlet, runbook 'u başlatıp parametreleri runbook 'a geçirmenize olanak sağlar

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) -alt runbook tamamlandıktan sonra gerçekleştirilmesi gereken işlemler varsa, bu cmdlet her çocuk için iş durumunu denetlemenizi sağlar.

## <a name="expired webhook"></a>Senaryon Durumlarına Web kancasını çağırırken 400 Hatalı Istek

### <a name="issue"></a>Sorun

Bir Azure Otomasyonu runbook 'u için bir Web kancası çağırmayı denediğinizde, şu hatayı alırsınız:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Nedeni

Çağırmayı denediğiniz Web kancası devre dışı veya zaman aşımına uğradı.

### <a name="resolution"></a>Çözüm

Web kancası devre dışıysa, Azure portal aracılığıyla Web kancasını yeniden etkinleştirebilirsiniz. Web kancası dolduğunda, Web kancasının silinip yeniden oluşturulması gerekir. [Web kancasını](../automation-webhooks.md#renew-webhook) yalnızca henüz dolmamışsa yenileyebilirsiniz.

## <a name="429"></a>Senaryon 429: Şu anda istek oranı fazla büyük. Lütfen tekrar deneyin

### <a name="issue"></a>Sorun

`Get-AzureRmAutomationJobOutput` Cmdlet 'ini çalıştırırken aşağıdaki hata iletisini alırsınız:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Nedeni

Bu hata, çok sayıda [ayrıntılı akışı](../automation-runbook-output-and-messages.md#verbose-stream)olan bir runbook 'tan iş çıktısı alınırken oluşabilir.

### <a name="resolution"></a>Çözüm

Bu hatayı çözmek için iki yol vardır:

* Runbook 'u düzenleyin ve yaydığı iş akışlarının sayısını azaltın.
* Cmdlet 'i çalıştırırken alınacak akış sayısını azaltın. Bu davranışı izlemek için, `-Stream Output` `Get-AzureRmAutomationJobOutput` cmdlet 'i yalnızca çıkış akışlarını almak için parametresini belirtebilirsiniz. 

## <a name="cannot-invoke-method"></a>Senaryon PowerShell işi şu hata ile başarısız oluyor: Metot çağrılamıyor

### <a name="issue"></a>Sorun

Azure 'da çalışan bir runbook 'ta bir PowerShell Işi başlatırken aşağıdaki hata iletisini alırsınız:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Nedeni

Bu hata, Azure 'da çalıştırılan bir runbook 'ta bir PowerShell işi başlattığınızda ortaya çıkabilir. Bu davranış, bir Azure korumalı alanında çalıştırılan runbook 'ların [tam dil modunda](/powershell/module/microsoft.powershell.core/about/about_language_modes)çalıştırılmadığı için oluşabilir.

### <a name="resolution"></a>Çözüm

Bu hatayı çözmek için iki yol vardır:

* Kullanmak yerine `Start-AzureRmAutomationRunbook` , bir runbook 'u başlatmak için kullanın `Start-Job`
* Runbook 'da bu hata iletisi varsa, karma Runbook Worker üzerinde çalıştırın

Bu davranış ve Azure Otomasyonu runbook 'larının diğer davranışları hakkında daha fazla bilgi edinmek için bkz. [runbook davranışı](../automation-runbook-execution.md#runbook-behavior).

## <a name="other"></a>Sorun yukarıda listelenmiyor

Aşağıdaki bölümlerde, sorununuzu çözmenize yardımcı olmak üzere belgelerinin desteklenmesinin yanı sıra diğer yaygın hatalar listelenmektedir.

## <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Karma runbook çalışanı işleri çalıştırmıyor veya yanıt vermiyor

Azure Otomasyonu yerine karma çalışanı kullanarak iş çalıştırıyorsanız, [karma çalışanın üzerinde sorun gidermeniz](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)gerekebilir.

## <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook "İzin yok" hatasıyla veya benzer bir hatayla başarısız oldu

RunAs hesapları, Azure kaynakları üzerinde geçerli hesabınızla aynı izinlere sahip olmayabilir. RunAs hesabınızın betiğinizde kullanılan [tüm kaynaklara erişmek için izinleri](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) olduğundan emin olun.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook'lar çalışıyordu ama aniden durduruldu

* Runbook 'lar daha önce yürütülerek durdurulmuş olsa da, [runas hesabının süresi dolmadığından emin olun](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Runbook'ları çalıştırmak için web kancaları kullanıyorsanız [web kancasının süresinin dolmadığından emin olun](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="issues-passing-parameters-into-webhooks"></a>Web kancalarına parametre geçirme sorunları

Web kancalarına parametre geçirme konusunda yardım için bkz. [Web kancasından runbook başlatma](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="issues-using-az-modules"></a>Az modüller kullanan sorunlar

Aynı Otomasyon Hesabında hem Az modüllerinin hem de AzureRM modüllerinin kullanılması desteklenmez. Daha fazla ayrıntı için lütfen [runbook 'Larda az modüller](https://docs.microsoft.com/azure/automation/az-modules) bölümüne bakın.

## <a name="inconsistent-behavior-in-runbooks"></a>Runbook'larda tutarsız davranış

Eşzamanlı işler, kaynakların birden fazla kez oluşturulması veya runbook'lardaki diğer zamanla ilgili mantıklarda sorun oluşmasını önlemek için [Runbook Yürütme](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) sayfasını inceleyin.

## <a name="runbook-fails-with-the-errors-no-permission-forbidden-403-or-some-variation"></a>Runbook hata vererek başarısız oluyor: İzin, yasak, 403 veya bazı çeşitler

RunAs hesapları, Azure kaynakları üzerinde geçerli hesabınızla aynı izinlere sahip olmayabilir. RunAs hesabınızın betikte kullanılan [kaynaklara erişim izni](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) olduğundan emin olun.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook'lar çalışıyordu ama aniden durduruldu

* Runbook 'lar daha önce yürütülerek durdurulmuş olsa da, RunAs hesabının [süresi dolmadığından](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)emin olun.
* Runbook 'ları başlatmak için Web kancaları kullanıyorsanız, Web kancasının [süresi dolmadığından](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)emin olun.

## <a name="passing-parameters-into-webhooks"></a>Web kancalarına parametre geçirme

Web kancalarına parametre geçirme konusunda yardım için bkz. [Web kancasından runbook başlatma](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="using-az-modules"></a>Az modüllerini kullanma

Aynı Otomasyon Hesabında hem Az modüllerinin hem de AzureRM modüllerinin kullanılması desteklenmez. Daha fazla ayrıntı için lütfen [runbook 'Larda az modüller](https://docs.microsoft.com/azure/automation/az-modules) bölümüne bakın.

## <a name="using-self-signed-certificates"></a>Otomatik Olarak İmzalanan Sertifikaları Kullanma

Otomatik olarak Imzalanan sertifikaları kullanmak için [Yeni bir sertifika oluşturma](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)Kılavuzu ' nu izlemeniz gerekir.

## <a name="recommended-documents"></a>Önerilen Belgeler

* [Azure Otomasyonu'nda Runbook Başlatma](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Azure Otomasyonu’nda Runbook Yürütme](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* [Azure Forumları](https://azure.microsoft.com/support/forums/) aracılığıyla Azure uzmanlarından yanıtlar alın
* [@AzureSupport](https://twitter.com/azuresupport) hesabı ile bağlantı kurun. Bu resmi Microsoft Azure hesabı, müşteri deneyimini geliştirmek için Azure topluluğunu doğru kaynaklara ulaştırır: yanıtlar, destek ve uzmanlar.
* Daha fazla yardıma ihtiyacınız varsa, bir Azure destek olayı dosyası gönderebilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
