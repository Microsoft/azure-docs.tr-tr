---
title: Azure Otomasyonu runbook sorunlarını giderme
description: Bu makalede, Azure Otomasyonu runbook 'larında sorunları gidermeye ve gidermeye nasıl çözüm yapılacağı açıklanır.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 7964bc62aefc912a0f61744841784600575c98de
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831231"
---
# <a name="troubleshoot-runbook-issues"></a>Runbook sorunlarını giderme

 Bu makalede oluşabilecek runbook sorunları ve bunların nasıl giderileceği açıklanmaktadır. Genel bilgiler için bkz. [Azure Otomasyonu 'Nda runbook yürütme](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Runbook sorunlarını tanılama

Azure Otomasyonu 'nda runbook yürütmesi sırasında hata aldığınızda, sorunları tanılamanıza yardımcı olması için aşağıdaki adımları kullanabilirsiniz:

1. Runbook betiğinizin yerel makinenizde başarıyla yürütüldüğünden emin olun.

    Dil başvurusu ve öğrenme modülleri için bkz. [PowerShell belgeleri](/powershell/scripting/overview) veya [Python belgeleri](https://docs.python.org/3/). Komut dosyanızı yerel olarak çalıştırmak, şu gibi yaygın hataları bulabilir ve çözümleyebilir:

      * Eksik modüller
      * Sözdizimi hataları
      * Mantık hataları

1. Runbook [hata akışlarını](../automation-runbook-output-and-messages.md#runbook-output)araştırın.

    Bu akışlara belirli iletiler için bakın ve bunları bu makalede belgelenen hatalarla karşılaştırın.

1. Düğümlerinizin ve Otomasyon çalışma alanınızın gerekli modüllere sahip olduğundan emin olun.

    Runbook 'ünüz herhangi bir modülü içeri aktardığında, [Içeri aktarma modüllerindeki](../shared-resources/modules.md#import-modules)adımları kullanarak Otomasyon hesabınızda kullanılabilir olduğunu doğrulayın. [Azure Automation 'Daki güncelleştirme Azure PowerShell modüllerindeki](../automation-update-azure-modules.md)yönergeleri izleyerek PowerShell modüllerinizi en son sürüme güncelleştirin. Daha fazla sorun giderme bilgisi için bkz. [Modül sorunlarını giderme](shared-resources.md#modules).

1. Runbook aboneliğiniz askıya alınırsa veya beklenmedik şekilde başarısız olursa:

    * Farklı çalıştır hesabının süresi dolmuşsa [sertifikayı yenileyin](../manage-runas-account.md#cert-renewal) .
    * Runbook 'u başlatmak için zaman aşımına uğradı bir Web kancasını kullanmaya çalışıyorsanız [Web kancasını yenileyin](../automation-webhooks.md#renew-a-webhook) .
    * Geçerli runbook durumlarını ve sorunun olası nedenlerini öğrenmek için [iş durumlarını denetleyin](../automation-runbook-execution.md#job-statuses) .
    * Runbook 'un askıya alınmadan önce ne olacağını belirlemek için Runbook 'a [ek çıkış ekleyin](../automation-runbook-output-and-messages.md#working-with-message-streams) .
    * İşiniz tarafından oluşturulan [tüm özel durumları işleyin](../automation-runbook-execution.md#exceptions) .

1. Bu adımı, runbook işi veya karma Runbook Worker ortamı yanıt vermezse yapın.

    Runbook 'larınızı Azure Otomasyonu yerine karma Runbook Worker üzerinde çalıştırıyorsanız, [karma çalışanın üzerinde sorun gidermeniz](hybrid-runbook-worker.md)gerekebilir.

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Senaryo: runbook, izin olmadan veya Yasak 403 hatasıyla başarısız oluyor

### <a name="issue"></a>Sorun

Runbook 'niz hiçbir izin veya Yasak 403 hatası ya da eşdeğer bir hata ile başarısız olur.

### <a name="cause"></a>Nedeni

Farklı Çalıştır hesapları, Azure kaynaklarında geçerli Otomasyon hesabınız olarak aynı izinlere sahip olmayabilir. 

### <a name="resolution"></a>Çözüm

Farklı Çalıştır hesabınızın betikte kullanılan [kaynaklara erişmek için izinlere](../../role-based-access-control/role-assignments-portal.md) sahip olduğundan emin olun.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Senaryo: Azure hesabında oturum açma başarısız oldu

### <a name="issue"></a>Sorun

Cmdlet 'le çalışırken aşağıdaki hatalardan birini alırsınız `Connect-AzAccount` :

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Nedeni

Kimlik bilgisi varlık adı geçerli değilse bu hatalar oluşur. Ayrıca, Otomasyon kimlik bilgisi varlığını ayarlamak için kullandığınız Kullanıcı adı ve parola geçerli değilse da meydana gelebilir.

### <a name="resolution"></a>Çözüm

Neyin yanlış olduğunu belirlemek için şu adımları izleyin:

1. Özel karakter olmadığından emin olun. Bu karakterler, `\@` Azure 'a bağlanmak için kullandığınız Otomasyon kimlik bilgileri varlık adındaki karakteri içerir.
1. Yerel PowerShell ıSE düzenleyicinizdeki Azure Otomasyonu kimlik bilgilerinde depolanan Kullanıcı adı ve parolasını kullanıp kullanabileceğini denetleyin. PowerShell ıSE 'de aşağıdaki cmdlet 'leri çalıştırın.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount -Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount -Credential $Cred
   ```

1. Kimlik doğrulama işlemi yerel olarak başarısız olursa, Azure Active Directory (Azure AD) kimlik bilgilerinizi düzgün bir şekilde kurmadınız. Azure AD hesabını doğru bir şekilde ayarlamak için [Azure Active Directory kullanarak Azure 'Da kimlik doğrulama](../automation-use-azure-ad.md)makalesine bakın.

1. Hata geçici gibi görünüyorsa, kimlik doğrulamasının daha sağlam olması için kimlik doğrulama yordamınızın yeniden deneme mantığını eklemeyi deneyin.

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
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Senaryo: oturum açmak için Login-AzureRMAccount çalıştırın

### <a name="issue"></a>Sorun

Bir runbook 'u çalıştırırken şu hatayı alırsınız:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Nedeni

Bu hata, farklı çalıştır hesabı kullanmadığınız veya farklı çalıştır hesabının süresi dolduğunda oluşabilir. Daha fazla bilgi için bkz. [Azure Otomasyonu farklı çalıştır hesaplarına genel bakış](../automation-security-overview.md#run-as-accounts).

Bu hatanın iki birincil nedeni vardır:

* Azurerd veya az modülünün farklı sürümleri vardır.
* Kaynaklara ayrı bir abonelikte erişmeye çalışıyorsunuz.

### <a name="resolution"></a>Çözüm

Bir Azurere veya az modülünü güncelleştirdikten sonra bu hatayı alırsanız, tüm modüllerinizi aynı sürüme güncelleştirin.

Başka bir abonelikteki kaynaklara erişmeye çalışıyorsanız, izinleri yapılandırmak için aşağıdaki adımları izleyin:

1. Otomasyon Farklı Çalıştır hesabına gidin ve **uygulama kimliğini** ve **parmak izini** kopyalayın.

    ![Uygulama KIMLIĞINI ve parmak Izini Kopyala](../media/troubleshoot-runbooks/collect-app-id.png)

1. Otomasyon hesabının *barındırılmadığını* , aboneliğin **erişim denetimine** gidin ve yeni bir rol ataması ekleyin.

    ![Erişim denetimi](../media/troubleshoot-runbooks/access-control.png)

1. Daha önce toplanan **uygulama kimliğini** ekleyin. **Katkıda bulunan** izinleri seçin.

    ![Rol ataması ekle](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Aboneliğin adını kopyalayın.

1. Artık Otomasyon hesabınızdaki izinleri diğer aboneliğe test etmek için aşağıdaki runbook kodunu kullanabilirsiniz. `<CertificateThumbprint>`Adım 1 ' de kopyalanmış değerle değiştirin. `"<SubscriptionName>"`Adım 4 ' te kopyalanmış değer ile değiştirin.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Senaryo: Azure aboneliği bulunamıyor

### <a name="issue"></a>Sorun

`Select-AzureSubscription`, `Select-AzureRMSubscription` , Veya cmdlet ile çalışırken aşağıdaki hatayı alırsınız `Select-AzSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Hata

Bu hata şu durumlarda oluşabilir:

* Abonelik adı geçerli değil.
* Abonelik ayrıntılarını almaya çalışan Azure AD kullanıcısı, aboneliğin Yöneticisi olarak yapılandırılmamış.
* Cmdlet kullanılamıyor.

### <a name="resolution"></a>Çözüm

Azure 'da kimlik doğrulaması yapılıp kalmadığını ve seçmek istediğiniz aboneliğe erişimi olup olmadığınızı belirlemek için aşağıdaki adımları izleyin:

1. Betiğinizin tek başına çalıştığından emin olmak için, Azure Otomasyonu dışında test edin.
1. Cmdlet 'ini çalıştırmadan önce betiğin [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 'ini çalıştırdığından emin olun `Select-*` .
1. `Disable-AzContextAutosave -Scope Process`Runbook 'un başlangıcına ekleyin. Bu cmdlet, tüm kimlik bilgilerinin yalnızca geçerli runbook 'un yürütülmesine uygulanmasını sağlar.
1. Hala hata iletisini görüyorsanız, için parametresini ekleyerek kodunuzu değiştirin `AzContext` `Connect-AzAccount` ve ardından kodu yürütün.

   ```powershell
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Senaryo: birden çok abonelikle ilgilenirken runbook 'Lar başarısız olur

### <a name="issue"></a>Sorun

Runbook 'lar çalıştırılırken, runbook Azure kaynaklarını yönetemez.

### <a name="cause"></a>Nedeni

Runbook çalışırken doğru bağlamı kullanmıyor. Bunun nedeni, runbook 'un yanlışlıkla yanlış aboneliğe erişmeye çalışıyor olması olabilir.

Şöyle bir hata görebilirsiniz:

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>Çözüm

Runbook birden çok runbook 'u çağırdığında abonelik bağlamı kaybolabilir. Yanlışlıkla yanlış aboneliğe erişmeye çalışmaktan kaçınmak için aşağıdaki yönergeleri izlemeniz gerekir.

* Yanlış aboneliğe başvurmayı önlemek için, her runbook 'un başlangıcında aşağıdaki kodu kullanarak Otomasyon Runbook 'larınızda bağlam kaydetmeyi devre dışı bırakın.

   ```azurepowershell-interactive
   Disable-AzContextAutosave -Scope Process
   ```

* Azure PowerShell cmdlet 'leri parametresini destekler `-DefaultProfile` . Bu, aynı işlemde birden fazla PowerShell betiğini çalıştırmayı desteklemek için tüm az ve Azurermcmdlet 'lerine eklenmiştir. bu sayede, her cmdlet için kullanılacak olan bağlamı ve hangi aboneliğin kullanılacağını belirtebilirsiniz. Runbook 'larınızla, runbook oluşturulduğunda (yani, bir hesap oturum açtığında) ve her değiştirildiğinde bağlam nesnesini runbook 'da kaydetmelisiniz ve az cmdlet belirttiğinizde bağlama başvurabilirsiniz.

   > [!NOTE]
   > [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) veya [Select-azsubscription](/powershell/module/servicemanagement/azure.service/set-azuresubscription)gibi cmdlet 'leri kullanarak bağlamı doğrudan işleyerek bile bağlam nesnesini geçirmeniz gerekir.

   ```azurepowershell-interactive
   $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName 
   $context = Add-AzAccount `
             -ServicePrincipal `
             -TenantId $servicePrincipalConnection.TenantId `
             -ApplicationId $servicePrincipalConnection.ApplicationId `
             -Subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749' `
             -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
   $context = Set-AzContext -SubscriptionName $subscription `
       -DefaultProfile $context
   Get-AzVm -DefaultProfile $context
   ```
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Senaryo: çok faktörlü kimlik doğrulaması etkinleştirildiğinden Azure kimlik doğrulaması başarısız olur

### <a name="issue"></a>Sorun

Azure Kullanıcı adı ve parolanızla Azure 'da kimlik doğrulanırken aşağıdaki hatayı alırsınız:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Nedeni

Azure hesabınızda çok faktörlü kimlik doğrulamanız varsa, Azure 'da kimlik doğrulaması yapmak için bir Azure Active Directory Kullanıcı kullanamazsınız. Bunun yerine, kimlik doğrulaması için bir sertifika veya hizmet sorumlusu kullanmanız gerekir.

### <a name="resolution"></a>Çözüm

Klasik bir farklı çalıştır hesabını Azure klasik dağıtım modeli cmdlet 'leriyle birlikte kullanmak için bkz. [Azure hizmetlerini yönetmek Için klasik farklı çalıştır hesabı oluşturma](../automation-create-standalone-account.md#create-a-classic-run-as-account). Azure Resource Manager cmdlet 'leriyle hizmet sorumlusu kullanmak için bkz. [Azure Portal kullanarak hizmet sorumlusu oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md) ve [Azure Resource Manager Ile hizmet sorumlusu kimlik doğrulama](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Senaryo: runbook "bir görev iptal edildi" hata iletisiyle başarısız oluyor

### <a name="issue"></a>Sorun

Runbook 'larınız aşağıdaki örneğe benzer bir hata ile başarısız olur:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Nedeni

Bu hata, eski Azure modülleri kullanılarak kaynaklanıyor olabilir.

### <a name="resolution"></a>Çözüm

Azure modüllerinizi en son sürüme güncelleştirerek bu hatayı çözebilirsiniz:

1. Otomasyon hesabınızda **modüller**' i seçin ve ardından **Azure modüllerini Güncelleştir**' i seçin.
1. Güncelleştirme yaklaşık 15 dakika sürer. Tamamlandıktan sonra, başarısız olan runbook 'u yeniden çalıştırın.

Modüllerinizi güncelleştirme hakkında daha fazla bilgi için bkz. Azure [Otomasyonu 'Nda Azure modüllerini güncelleştirme](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Senaryo: bir cmdlet, işlev veya betiğin adı olarak terim tanınmıyor

### <a name="issue"></a>Sorun

Runbook 'larınız aşağıdaki örneğe benzer bir hata ile başarısız olur:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Nedeni

Bu hata aşağıdaki nedenlerden kaynaklanabilir:

* Cmdlet 'ini içeren modül Otomasyon hesabına içeri aktarılmaz.
* Cmdlet 'i içeren modül içeri aktarıldı, ancak güncel değil.

### <a name="resolution"></a>Çözüm

Bu hatayı çözmek için aşağıdaki görevlerden birini yapın:

* Bir Azure modülü için bkz. [Azure Automation 'da Azure PowerShell modüllerini güncelleştirme](../automation-update-azure-modules.md) , Otomasyon hesabınızda modüllerinizi güncelleştirme hakkında bilgi edinin.
* Azure olmayan bir modül için, modülün Otomasyon hesabınıza içeri aktarıldığından emin olun.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Senaryo: cmdlet 'ı Azure Otomasyonu 'nda PnP PowerShell runbook 'unda başarısız oluyor

### <a name="issue"></a>Sorun

Bir runbook, Azure Otomasyonu çıkışına doğrudan bir PnP PowerShell tarafından oluşturulan nesne yazdığında, cmdlet çıktısı Automation 'a geri akış yapamıyor.

### <a name="cause"></a>Nedeni

Bu sorun, en yaygın olarak, Azure Otomasyonu, PnP PowerShell cmdlet 'lerini çağıran runbook 'ları (örneğin, `add-pnplistitem` dönüş nesnelerini yakalamak zorunda kalmadan) işlediğinde oluşur.

### <a name="resolution"></a>Çözüm

Cmdlet 'lerinizi, tüm nesneleri standart çıktıya yazmayı denememesi için değişkenlere bir dönüş değeri atamak üzere betikleri düzenleyin. Bir komut dosyası çıktı akışını burada gösterildiği gibi bir cmdlet 'e yönlendirebilir.

```azurecli
  $null = add-pnplistitem
```

Komut dosyası cmdlet çıkışını ayrıştırdığında, komut dosyası çıktıyı bir değişkende depolamalıdır ve yalnızca çıktıyı akışa alma yerine değişkeni işleyebilir.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Senaryo: bir runbook çalıştırılırken cmdlet tanınmıyor

### <a name="issue"></a>Sorun

Runbook işiniz şu hata ile başarısız oluyor:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Nedeni

Bu hata, PowerShell motorunun runbook 'inizdeki kullanmakta olduğunuz cmdlet 'i bulamamasının nedeni olur. Bu cmdlet 'i içeren modülün hesapta eksik olması, runbook adıyla bir ad çakışması olması veya cmdlet 'inin başka bir modülde de mevcut olması ve otomasyon 'un adı çözememem mümkündür.

### <a name="resolution"></a>Çözüm

Sorunu gidermek için aşağıdaki çözümlerden herhangi birini kullanın:

* Cmdlet adını doğru girdiğinizden emin olun.
* Cmdlet 'inin Otomasyon hesabınızda mevcut olduğundan ve çakışma olmadığından emin olun. Cmdlet 'in mevcut olup olmadığını doğrulamak için, düzenleme modunda bir runbook açın ve kitaplıkta bulmak istediğiniz cmdlet 'i arayın veya çalıştırın `Get-Command <CommandName>` . Cmdlet 'in hesapta kullanılabildiğini doğruladıktan ve diğer cmdlet 'ler veya Runbook 'larla ad çakışması yoksa, cmdlet 'ini tuvale ekleyin. Runbook 'da geçerli bir parametre kümesi kullandığınızdan emin olun.
* Ad çakışması varsa ve cmdlet iki farklı modülde kullanılabiliyorsa, cmdlet için tam adı kullanarak sorunu çözün. Örneğin `ModuleName\CmdletName` kullanabilirsiniz.
* Bir karma çalışan grubunda runbook 'u şirket içinde yürütüyorsanız, modülün ve cmdlet 'inin karma çalışanı barındıran makinede yüklü olduğundan emin olun.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Senaryo: Add-AzAccount çağrısında yanlış nesne başvurusu

### <a name="issue"></a>Sorun

`Add-AzAccount`Cmdlet 'i için bir diğer ad olan ile çalışırken bu hatayı alırsınız `Connect-AzAccount` :

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Nedeni

Bu hata, runbook `Add-AzAccount` Otomasyon hesabını eklemek için çağrılmadan önce doğru adımları gerçekleştirmezse meydana gelebilir. Gerekli adımlardan birine örnek olarak bir farklı çalıştır hesabı ile oturum açma işlemi yapılır. Runbook 'unda kullanılacak doğru işlemler için bkz. [Azure Otomasyonu 'Nda runbook yürütme](../automation-runbook-execution.md).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Senaryo: nesne başvurusu bir nesnenin örneğine ayarlanmadı

### <a name="issue"></a>Sorun

Parametresi ile bir alt runbook 'u çağırdığınızda `Wait` ve çıkış akışı bir nesne içeriyorsa aşağıdaki hatayı alırsınız:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Nedeni

Akış nesneler içeriyorsa, `Start-AzAutomationRunbook` Çıkış akışını doğru işlemez.

### <a name="resolution"></a>Çözüm

Bir yoklama mantığı uygulayın ve çıktıyı almak için [Get-Azautomationjoi put](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) cmdlet 'ini kullanın. Bu mantığın bir örneği burada tanımlanmıştır:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Senaryo: Serisi kaldırılan nesne nedeniyle runbook başarısız oluyor

### <a name="issue"></a>Sorun

Runbook 'larınız şu hata ile başarısız oluyor:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Nedeni

Runbook 'ağınız bir PowerShell Iş akışıdır, iş akışı askıya alınmışsa runbook 'unuzu kalıcı hale getirmek için karmaşık nesneleri seri durumdan çıkarılmış biçimde depolar.

### <a name="resolution"></a>Çözüm

Bu sorunu gidermek için aşağıdaki çözümlerden herhangi birini kullanın:

* Karmaşık nesneleri bir cmdlet 'ten diğerine çevreleyorsanız, bu cmdlet 'leri bir etkinlikte sarın `InlineScript` .
* Tüm nesneyi geçirmek yerine, ihtiyacınız olan adı veya değeri karmaşık nesneden geçirin.
* PowerShell Iş akışı runbook 'u yerine bir PowerShell runbook 'u kullanın.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Senaryo: Web kancasını çağırırken 400 Hatalı Istek durumu

### <a name="issue"></a>Sorun

Bir Azure Otomasyonu runbook 'u için bir Web kancası çağırmaya çalıştığınızda, şu hatayı alırsınız:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Nedeni

Çağırmayı denediğiniz Web kancası devre dışı veya zaman aşımına uğradı. 

### <a name="resolution"></a>Çözüm

Web kancası devre dışıysa, Azure portal aracılığıyla yeniden etkinleştirebilirsiniz. Web kancası süresi dolmuşsa, onu silip yeniden oluşturmanız gerekir. [Web kancasını](../automation-webhooks.md#renew-a-webhook) yalnızca henüz dolmamışsa yenileyebilirsiniz. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Senaryo: 429: istek oranı şu anda çok büyük

### <a name="issue"></a>Sorun

Cmdlet 'ini çalıştırırken aşağıdaki hata iletisini alırsınız `Get-AzAutomationJobOutput` :

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Nedeni

Bu hata, çok sayıda [ayrıntılı akışı](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream)olan bir runbook 'tan iş çıktısı alınırken oluşabilir.

### <a name="resolution"></a>Çözüm

Bu hatayı çözmek için aşağıdakilerden birini yapın:

* Runbook 'u düzenleyin ve yaydığı iş akışlarının sayısını azaltın.
* Cmdlet 'i çalıştırırken alınacak akış sayısını azaltın. Bunu yapmak için `Stream` [Get-Azautomationjoi put](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) cmdlet 'inin parametresinin değerini yalnızca çıkış akışlarını almak üzere ayarlayabilirsiniz. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Senaryo: ayrılan kota aşıldığı için Runbook işi başarısız oldu

### <a name="issue"></a>Sorun

Runbook işiniz şu hata ile başarısız oluyor:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Nedeni

Bu hata, iş yürütmesi hesabınız için 500 dakikalık boş kotayı aştığında oluşur. Bu kota, tüm iş yürütme görevi türleri için geçerlidir. Bu görevlerden bazıları, bir işi test ediyor, portaldan bir işi başlatıyor, Web kancalarını kullanarak bir işi yürütüyor veya Azure portal ya da veri merkeziniz kullanılarak yürütülecek bir iş zamanlıyor. Otomasyon fiyatlandırması hakkında daha fazla bilgi edinmek için bkz. [Automation fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Çözüm

Ayda 500 dakikadan fazla işlem kullanmak istiyorsanız aboneliğinizi ücretsiz katmandan temel katmana değiştirin:

1. Azure aboneliğinizde oturum açın.
1. Yükseltilecek Otomasyon hesabını seçin.
1. **Ayarlar**' ı seçin ve **fiyatlandırma**' yı seçin.
1. Hesabınızı temel katmana yükseltmek için en alttaki sayfada **Etkinleştir** ' i seçin.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Senaryo: runbook çıkış akışı 1 MB 'den büyük

### <a name="issue"></a>Sorun

Azure korumalı alanında çalışan runbook 'larınız aşağıdaki hatayla başarısız olur:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Nedeni

Bu hata, runbook 'larınızın çıkış akışına çok fazla özel durum verisi yazmayı denemesinden kaynaklanır.

### <a name="resolution"></a>Çözüm

İş çıktı akışında 1 MB 'lik bir sınır vardır. Runbook 'larınızın, ve blokları kullanarak bir çalıştırılabilir veya alt işleme çağrıları barındıraldığından emin olun `try` `catch` . İşlemler bir özel durum oluştura, kodun özel durumdan bir Otomasyon değişkenine ileti yazmasını sağlayabilirsiniz. Bu teknik, iletinin iş çıkış akışına yazılmasını engeller. Yürütülen karma Runbook Worker işleri için, çıkış akışı 1 MB 'a kesildi ve hata iletisi olmadan görüntülenir.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Senaryo: runbook işi üç kez denendi, ancak her seferinde başlatılamaz

### <a name="issue"></a>Sorun

Runbook 'larınız şu hata ile başarısız oluyor:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Nedeni

Bu hata, aşağıdaki sorunlardan biri nedeniyle oluşur:

* **Bellek sınırı.** Bir iş 400 MB 'tan fazla bellek kullanıyorsa başarısız olabilir. Bir korumalı alana ayrılan bellek üzerinde belgelenen sınırlar [Automation hizmeti sınırları](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)' nde bulunur. 

* **Ağ yuvaları.** Azure korumalı alanlar 1.000 eşzamanlı ağ yuvası ile sınırlıdır. Daha fazla bilgi için bkz. [Otomasyon Hizmeti sınırları](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* **Modül uyumsuz.** Modül bağımlılıkları doğru olmayabilir. Bu durumda, runbook genellikle bir `Command not found` veya `Cannot bind parameter` iletisi döndürür.

* **Korumalı alan için Active Directory kimlik doğrulaması yok.** Runbook 'iniz, bir Azure korumalı alanında çalışan bir yürütülebilir dosyayı veya alt işlemi çağırmayı denedi. Azure Active Directory kimlik doğrulama kitaplığı 'nı (ADAL) kullanarak Azure AD ile kimlik doğrulaması yapmak için Runbook 'ları yapılandırma desteklenmez.

### <a name="resolution"></a>Çözüm

* **Bellek sınırı, ağ yuvaları.** Bellek sınırları içinde çalışma için önerilen yollar, iş yükünü birden çok runbook arasında bölmek, bellekte daha az veri işlemek, runbook 'lerinizden gereksiz çıkış yazmaktan kaçınmamak ve PowerShell iş akışı runbook 'larınıza kaç denetim noktası yazılacağını göz önünde bulundurmaktır. `$myVar.clear`Değişkenleri temizlemek ve `[GC]::Collect` çöp toplamayı hemen çalıştırmak için kullanmak gibi Clear metodunu kullanın. Bu eylemler çalışma zamanı sırasında runbook 'larınızın bellek parmak izini azaltır.

* **Modül uyumsuz.** Azure [Otomasyonu 'nda Azure PowerShell modüllerini güncelleştirme](../automation-update-azure-modules.md)bölümündeki adımları izleyerek Azure modüllerinizi güncelleştirin.

* **Korumalı alan için Active Directory kimlik doğrulaması yok.** Azure AD 'de bir runbook ile kimlik doğrulaması yaptığınızda, Azure AD modülünün Otomasyon hesabınızda kullanılabilir olduğundan emin olun. Farklı Çalıştır hesabına runbook 'un otomatikleştiren görevleri gerçekleştirmek için gerekli izinleri verdiğinizden emin olun.

  Runbook 'iniz bir Azure korumalı alanında çalışan bir yürütülebilir dosyayı veya alt işlemi çağıramıyorum, bir [karma runbook çalışanında](../automation-hrw-run-runbooks.md)runbook 'u kullanın. Hibrit çalışanlar, Azure sanal ağı 'nın sahip olduğu bellek ve ağ limitleriyle sınırlı değildir.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Senaryo: PowerShell işi "yöntem çağrılamaz" hata iletisiyle başarısız oluyor

### <a name="issue"></a>Sorun

Azure 'da çalışan bir runbook 'ta bir PowerShell işi başlattığınızda aşağıdaki hata iletisini alırsınız:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Nedeni

Bu hata, bir Azure korumalı alanında çalışan runbook 'ların [tam dil modunda](/powershell/module/microsoft.powershell.core/about/about_language_modes)çalıştıramayacağı anlamına gelebilir.

### <a name="resolution"></a>Çözüm

Bu hatayı çözmek için iki yol vardır:

* [Başlangıç-iş](/powershell/module/microsoft.powershell.core/start-job)kullanmak yerine, runbook 'u başlatmak için [Start-azautomationrunbook](/powershell/module/az.automation/start-azautomationrunbook) kullanın.
* Bir karma runbook çalışanında runbook 'u çalıştırmayı deneyin.

Bu davranış ve Azure Otomasyonu runbook 'larının diğer davranışları hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu 'Nda runbook yürütme](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Senaryo: uzun süre çalışan bir runbook tamamlanamazsa

### <a name="issue"></a>Sorun

Runbook 'larınız, üç saat çalıştıktan sonra durdurulmuş durumda görüntülenir. Bu hatayı da alabilirsiniz:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Bu davranış, Azure Otomasyonu 'ndaki işlemlerin dengeli bir şekilde izlenmesi nedeniyle Azure korumalı alan tasarımı ile [tasarlanmalıdır](../automation-runbook-execution.md#fair-share) . Bir işlem üç saatten uzun bir süre yürütülüyorsa, dengeli bir şekilde bir runbook otomatik olarak durduruluyor. Dengeli bir şekilde geçen bir runbook 'un durumu, runbook türüne göre farklılık gösterir. PowerShell ve Python runbook 'lar durdurulmuş bir duruma ayarlanır. PowerShell Iş akışı runbook 'ları başarısız olarak ayarlanır.

### <a name="cause"></a>Nedeni

Runbook, bir Azure korumalı alanında dengeli bir paylaşıma izin verilen üç saatlik sınırın üzerinde çalışır.

### <a name="resolution"></a>Çözüm

Önerilen bir çözüm, runbook 'u [karma Runbook Worker](../automation-hrw-run-runbooks.md)üzerinde çalıştıralım. Hibrit çalışanlar, Azure korumalı alan tarafından kullanılan üç saatlik dengeli bir paylaşılan runbook sınırı ile sınırlı değildir. Karma runbook çalışanları üzerinde çalışan runbook 'lar, beklenmeyen yerel altyapı sorunları varsa yeniden başlatma davranışlarını destekleyecek şekilde geliştirilmiştir.

Diğer bir çözüm, runbook 'u [alt runbook 'lar](../automation-child-runbooks.md)oluşturarak en iyileştirmenize olanak sağlar. Runbook 'iniz birkaç kaynak üzerinde aynı işlevle döngüye geçtiğinde (örneğin, birkaç veritabanında bir veritabanı işleminde), işlevi bir alt runbook 'a taşıyabilirsiniz. Her alt runbook ayrı bir işlemde paralel olarak yürütülür. Bu davranış, üst runbook 'un tamamlanacağı toplam süreyi düşürür.

Alt runbook senaryosunu etkinleştiren PowerShell cmdlet 'leri şunlardır:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Bu cmdlet bir runbook başlatmanızı ve bu runbook'a parametre iletmenizi sağlar.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Alt runbook tamamlandıktan sonra gerçekleştirilmesi gereken işlemler varsa, bu cmdlet her çocuk için iş durumunu denetlemenizi sağlar.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Senaryo: get_SerializationSettings yöntemi hakkında iş akışlarında hata

### <a name="issue"></a>Sorun

Runbook için iş akışlarınız içinde aşağıdaki hatayı görürsünüz:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Nedeni

Bu hata, büyük olasılıkla Azurere 'den, runbook 'inizdeki diğer modüllere tamamlanmamış bir geçişin kullanılması nedeniyle oluşur. Bu durum, Azure Otomasyonu 'nun yalnızca Azurere modüllerini kullanarak bir runbook işi başlatmasını ve sonra yalnızca az modüller kullanarak başka bir iş, bir korumalı alan kilitlenmesine yol açar.

### <a name="resolution"></a>Çözüm

Aynı runbook 'ta az ve Azurerk cmdlet 'lerinin kullanılmasını önermiyoruz. Modüllerin doğru kullanımı hakkında daha fazla bilgi edinmek için, bkz. [az modules 'e geçirme](../shared-resources/modules.md#migrate-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Senaryo: runbook veya uygulama için Azure korumalı alanı kullanılırken erişim reddedildi

### <a name="issue"></a>Sorun

Runbook veya uygulamanız bir Azure korumalı alanında çalışmayı denediğinde, ortam erişimi reddeder.

### <a name="cause"></a>Nedeni

Bu sorun, Azure korumalı alanlar tüm işlem dışı COM sunucularına erişimi engellediği için ortaya çıkabilir. Örneğin, korumalı bir uygulama veya Runbook Windows Yönetim Araçları (WMI) veya Windows Installer hizmetine (msiserver.exe) çağıramaz.

### <a name="resolution"></a>Çözüm

Azure korumalı alanının kullanımı hakkında ayrıntılı bilgi için bkz. [runbook yürütme ortamı](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Senaryo: runbook içinde Key Vault kullanılırken geçersiz yasak durum kodu

### <a name="issue"></a>Sorun

Azure Otomasyonu runbook 'u aracılığıyla Azure Key Vault erişmeye çalıştığınızda şu hatayı alırsınız:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Nedeni

Bu sorunun olası nedenleri şunlardır:

* Farklı Çalıştır hesabı kullanmıyor.
* İzinler yetersiz.

### <a name="resolution"></a>Çözüm

#### <a name="not-using-a-run-as-account"></a>Farklı Çalıştır hesabı kullanmıyor

Key Vault erişmek için bir farklı çalıştır hesabı kullandığınızdan emin olmak için [Adım 5-Azure kaynaklarını yönetmek için kimlik doğrulaması ekleme](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) ' ye uyun.

#### <a name="insufficient-permissions"></a>Yetersiz izinler

Farklı Çalıştır hesabınızın Key Vault erişmek için yeterli izinlere sahip olduğundan emin olmak için [Key Vault Izinleri ekleyin](../manage-runas-account.md#add-permissions-to-key-vault) .

## <a name="recommended-documents"></a>Önerilen belgeler

* [Azure Otomasyonu 'nda runbook yürütmesi](../automation-runbook-execution.md)
* [Azure Otomasyonu 'nda runbook başlatma](../start-runbooks.md)

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada görmüyorsanız veya sorununuzu çözemezseniz daha fazla destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın. Azure desteği, yanıtlar, destek ve uzmanlar için sizi Azure Community 'ye bağlar.
* Daha fazla yardıma ihtiyacınız varsa, bir Azure destek olayı dosyası gönderebilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.
