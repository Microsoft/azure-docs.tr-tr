---
title: Ölçek oturumu Azure Otomasyonu Windows sanal masaüstü 'Nü (klasik) barındırır-Azure
description: Azure Otomasyonu ile Windows sanal masaüstü (klasik) oturum Konakları otomatik olarak ölçeklendirin.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 907871a85680202a4a8b5f73b4454a9b2f2fe103
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444318"
---
# <a name="scale-windows-virtual-desktop-classic-session-hosts-using-azure-automation"></a>Azure Otomasyonu 'Nu kullanarak Windows sanal masaüstü (klasik) oturum konaklarına ölçeklendirme

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir.

Sanal makinelerinizi (VM 'Ler) ölçeklendirerek toplam Windows sanal masaüstü dağıtım maliyetinizi azaltabilirsiniz. Bu, yoğun olmayan kullanım saatlerinde oturum ana bilgisayar VM 'lerinin kapatılması ve serbest bir şekilde çıkarılması, daha sonra yeniden açılması ve yoğun saatlerde yeniden bulunması anlamına gelir.

Bu makalede, Azure Otomasyonu hesabı ve Azure Logic App ile oluşturulan ve Windows sanal masaüstü ortamınızdaki oturum ana bilgisayar VM 'lerini otomatik olarak ölçeklendirilen ölçeklendirme aracı hakkında bilgi edineceksiniz. Ölçeklendirme aracının nasıl kullanılacağını öğrenmek için [önkoşulların](#prerequisites)önüne atlayın.

## <a name="how-the-scaling-tool-works"></a>Ölçeklendirme aracının çalışması

Ölçeklendirme Aracı, oturum ana makinesi maliyetlerini iyileştirmek isteyen müşteriler için düşük maliyetli bir Otomasyon seçeneği sağlar.

Ölçeklendirme aracını kullanarak şunları yapabilirsiniz:

- VM 'Leri, en yüksek ve Off-Peak iş saatlerine göre başlayıp durduracak şekilde zamanlayın.
- CPU çekirdeği başına oturum sayısına göre VM 'Leri ölçeklendirin.
- Off-Peak saat boyunca VM 'lerde ölçeklendirin, en az sayıda oturum ana makinesi VM çalışır durumda bırakın.

Ölçeklendirme Aracı, bir Azure Otomasyonu hesabının, bir PowerShell runbook 'unun, Web kancası ve Azure mantıksal uygulamasının işlevini bir arada kullanır. Araç çalıştırıldığında Azure Logic App, Azure Otomasyonu runbook 'u başlatmak için bir Web kancası çağırır. Runbook daha sonra bir iş oluşturur.

En yüksek kullanım süresi boyunca iş, her konak havuzu için geçerli çalışan oturum ana bilgisayarının geçerli oturum sayısını ve VM kapasitesini denetler. Çalışan oturum ana bilgisayar VM 'lerinin, **CreateOrUpdateAzLogicApp.ps1** dosyası Için tanımlanan *Sessionthresholdpercpu* parametresine göre mevcut oturumları destekleyebiliyor olup olmadığını hesaplamak için bu bilgileri kullanır. Oturum Ana bilgisayar VM 'Leri mevcut oturumları destekleyemiyorum, iş konak havuzundaki ek oturum ana bilgisayar VM 'lerini başlatır.

>[!NOTE]
>*Sessionthresholdpercpu* , sanal makine üzerindeki oturum sayısını kısıtlamaz. Bu parametre yalnızca yeni VM 'Lerin, bağlantıların yük dengelenmesi için ne zaman başlatılması gerektiğini belirler. Oturum sayısını kısıtlamak için, *Maxsessionlimit* parametresini uygun şekilde yapılandırmak için [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) yönergelerini izlemeniz gerekir.

Yoğun kullanım süresi boyunca iş, *Minimumnumberofrdsh* parametresine göre kaç tane oturum ana bilgisayar VM 'nin kapatılması gerektiğini belirler. *Limitsecondstoforcelogoffuser* parametresini sıfır olmayan pozitif bir değere ayarlarsanız, yeni oturumların konaklara bağlanmasını engellemek için, iş, oturum Konağı VM 'lerini boşaltma moduna ayarlar. Daha sonra iş, şu anda oturum açmış olan kullanıcılar işlerini kaydetmek, yapılandırılan süreyi beklemek ve sonra kullanıcıları oturumu kapatmaya zorlayacaktır. Oturum Ana bilgisayar VM 'si üzerindeki tüm Kullanıcı oturumları oturumu kapatıldıktan sonra, iş sanal makineyi kapatır. VM kapandıktan sonra iş, oturum ana bilgisayar boşaltma modunu sıfırlayacaktır.

>[!NOTE]
>Oturum Ana bilgisayar VM 'sini el ile boşaltma moduna ayarlarsanız, iş, oturum ana bilgisayarı VM 'sini yönetmez. Oturum Ana bilgisayar sanal makinesi çalışıyor ve boşalt moduna ayarlandıysa, yükü işlemek için işin ek VM 'Leri başlatmasını sağlayacak olan kullanım dışı olarak değerlendirilir. Tüm Azure VM 'lerini boşaltma moduna el ile ayarlamadan önce etiketleyeöneririz. Daha sonra Azure Logic App Scheduler oluştururken etiketi *MaintenanceTagName* parametresiyle adlandırın. Etiketler, ölçek aracının yönettiği bu VM 'Leri ayırt etmenize yardımcı olur. Bakım etiketinin ayarlanması, etiketi kaldırana kadar ölçeklendirme aracının VM 'de değişiklik yapmasını de önler.

*Limitsecondstoforcelogoffuser* parametresini sıfır olarak ayarlarsanız, iş, belirtilen grup ilkelerindeki oturum yapılandırma ayarının kullanıcı oturumlarını imzalamayı işlemesine izin verir. Bu grup ilkelerini görmek için **bilgisayar yapılandırma**  >  **ilkeleri**  >  **Yönetim Şablonları**  >  **Windows bileşenleri**  >  **Uzak Masaüstü Hizmetleri**  >  **Uzak Masaüstü oturumu ana bilgisayarı**  >  **oturum süresi sınırları**' na gidin. Bir oturum ana bilgisayar VM 'sinde etkin bir oturum varsa, iş, oturum ana bilgisayar VM 'sini çalışır durumda bırakır. Etkin bir oturum yoksa, iş, oturum ana bilgisayarı sanal makinesini kapatır.

Her zaman, geçerli oturum sayısının en yüksek kapasitenin %90 ' inden fazla olup olmadığını öğrenmek için, iş, ana bilgisayar havuzunun *Maxsessionlimit* değerini de hesaba götürür. Bu ise, iş ek oturum ana bilgisayar VM 'Leri başlatır.

İş düzenli aralıklarla ayarlanan yineleme aralığına göre çalışır. Windows sanal masaüstü ortamınızın boyutuna bağlı olarak bu aralığı değiştirebilirsiniz, ancak sanal makinelerin başlatılmasının ve kapanmasının biraz zaman alabilir ve bu nedenle gecikmeyi hesaba erteleyebilirsiniz. Yinelenme aralığını her 15 dakikada bir ayarlamanız önerilir.

Ancak, araç aşağıdaki sınırlamalara de sahiptir:

- Bu çözüm yalnızca havuza alınmış çok oturumlu oturum ana bilgisayar VM 'Leri için geçerlidir.
- Bu çözüm, herhangi bir bölgedeki VM 'Leri yönetir, ancak yalnızca Azure Otomasyonu hesabınız ve Azure Logic App ile aynı abonelikte kullanılabilir.
- Runbook 'taki bir işin en fazla çalışma zamanı 3 saattir. Konak havuzundaki VM 'Leri başlatmak veya durdurmak bundan daha uzun sürer, iş başarısız olur. Daha fazla ayrıntı için bkz. [paylaşılan kaynaklar](../../automation/automation-runbook-execution.md#fair-share).

>[!NOTE]
>Ölçeklendirme Aracı, şu anda ölçeklendirildiği konak havuzunun yük dengeleme modunu denetler. Araç, hem yoğun hem de yoğun olmayan saatlerde, birinci düzey Yük Dengeleme modunu kullanır.

## <a name="prerequisites"></a>Önkoşullar

Ölçeklendirme aracı 'nı ayarlamaya başlamadan önce, aşağıdaki şeyleri hazırlamış olduğunuzdan emin olun:

- Bir [Windows sanal masaüstü kiracısı ve konak havuzu](create-host-pools-arm-template.md)
- Windows sanal masaüstü hizmeti ile yapılandırılan ve kaydettirilen oturum ana bilgisayar havuzu VM 'Leri
- Azure aboneliğinde [katkıda bulunan erişimi](../../role-based-access-control/role-assignments-portal.md) olan bir Kullanıcı

Aracı dağıtmak için kullandığınız makine şunları içermelidir:

- Windows PowerShell 5,1 veya üzeri
- Microsoft az PowerShell modülü

Her şeyi hazırlayın ve kullanmaya başlayın.

## <a name="create-or-update-an-azure-automation-account"></a>Azure Otomasyonu hesabı oluşturma veya güncelleştirme

>[!NOTE]
>Ölçeklendirme betiğinin daha eski bir sürümünü çalıştıran bir runbook 'u zaten içeren bir Azure Automation hesabınız varsa, bu dosyanın güncelleştirildiğinden emin olmak için aşağıdaki yönergeleri izlemeniz gerekir.

İlk olarak, PowerShell runbook 'unu çalıştırmak için bir Azure Otomasyonu hesabınızın olması gerekir. PowerShell runbook 'unu ayarlamak için kullanmak istediğiniz mevcut bir Azure Otomasyonu hesabınız olsa bile bu bölümün açıkladığı işlem geçerlidir. Kurulumun nasıl yapılacağı ile ilgili bilgilere aşağıdan ulaşabilirsiniz:

1. Windows PowerShell'i açın.

2. Azure hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >Hesabınız, ölçeklendirme aracını dağıtmak istediğiniz Azure aboneliğinde katılımcı haklarına sahip olmalıdır.

3. Azure Otomasyonu hesabını oluşturmak için betiği indirmek üzere aşağıdaki cmdlet 'i çalıştırın:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Betiği yürütmek ve Azure Otomasyonu hesabını oluşturmak için aşağıdaki cmdlet 'i çalıştırın. Parametrelerin değerlerini doldurabilir ya da bunların varsayılan değerlerini kullanmak üzere yorum yapabilirsiniz.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. Cmdlet 'in çıktısı, bir Web kancası URI 'sini içerir. Azure mantıksal uygulaması için yürütme zamanlamasını ayarlarken bu parametreyi bir parametre olarak kullanacaksınız, URI 'nin bir kaydını kaydettiğinizden emin olun.

6. Log Analytics **için yeniden** çalışma parametresini belirttiyseniz, cmdlet 'in çıktısı, Log Analytics çalışma alanı kimliği ve birincil anahtarını da içerir. Azure mantıksal uygulaması için yürütme zamanlamasını ayarlarken bu parametreyi daha sonra bir parametre olarak yeniden kullanmanız gerektiğinden URI 'yi hatırlayın.

7. Azure Otomasyonu hesabınızı ayarladıktan sonra Azure aboneliğinizde oturum açın ve aşağıdaki görüntüde gösterildiği gibi Azure Otomasyonu hesabınızın ve ilgili runbook 'un belirtilen kaynak grubunda göründüğünden emin olun:

    >[!div class="mx-imgBorder"]
    >![Azure genel bakış sayfasının yeni oluşturulan Azure Otomasyonu hesabını ve Runbook 'u gösteren bir görüntüsü.](media/automation-account.png)

    Web kancası 'nizin nerede olması gerektiğini denetlemek için Runbook 'un adını seçin. Sonra, runbook 'un kaynaklar bölümüne gidin ve **Web kancaları**' nı seçin.

## <a name="create-an-azure-automation-run-as-account"></a>Azure Otomasyonu farklı çalıştır hesabı oluşturma

Artık bir Azure Otomasyonu hesabınız olduğuna göre, henüz bir tane yoksa bir Azure Otomasyonu farklı çalıştır hesabı da oluşturmanız gerekir. Bu hesap, aracın Azure kaynaklarınıza erişmesine izin verir.

Azure [Otomasyonu farklı çalıştır hesabı](../../automation/manage-runas-account.md) , Azure cmdlet 'leriyle Azure 'da kaynak yönetimine yönelik kimlik doğrulaması sağlar. Farklı Çalıştır hesabı oluşturduğunuzda, Azure Active Directory yeni bir hizmet sorumlusu kullanıcısı oluşturur ve katılımcı rolünü abonelik düzeyinde hizmet sorumlusu kullanıcısına atar. Azure farklı çalıştır hesabı, bir kimlik bilgisi nesnesinde Kullanıcı adı ve parola depolamaya gerek kalmadan sertifikalarla ve hizmet sorumlusu adıyla güvenli bir şekilde kimlik doğrulaması yapmanın harika bir yoludur. Farklı Çalıştır hesabı kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Farklı Çalıştır hesabı Izinlerini sınırlayın](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Abonelik yöneticileri rolünün üyesi olan bir Kullanıcı ve aboneliğin ortak Yöneticisi bir farklı çalıştır hesabı oluşturabilir.

Azure Otomasyonu hesabınızda bir farklı çalıştır hesabı oluşturmak için:

1. Azure portal, **tüm hizmetler**' i seçin. Kaynak listesinde **Otomasyon hesapları** girin ve seçin.

2. **Otomasyon hesapları** sayfasında, Azure Otomasyonu hesabınızın adını seçin.

3. Pencerenin sol tarafındaki bölmede **Hesap ayarları** bölümünde **Farklı Çalıştır hesapları** ' nı seçin.

4. **Azure farklı çalıştır hesabı**' nı seçin. **Azure farklı çalıştır hesabı ekle** bölmesi göründüğünde, genel bakış bilgilerini gözden geçirin ve ardından **Oluştur** ' u seçerek hesap oluşturma işlemini başlatın.

5. Azure 'un farklı çalıştır hesabını oluşturması için birkaç dakika bekleyin. Oluşturma ilerleme durumunu menüdeki Bildirimler bölümünde izleyebilirsiniz.

6. İşlem tamamlandığında, belirtilen Azure Otomasyonu hesabında **Azurerunasconnection** adlı bir varlık oluşturur. **Azure farklı çalıştır hesabı**' nı seçin. Bağlantı varlığı uygulama KIMLIĞI, kiracı KIMLIĞI, abonelik KIMLIĞI ve sertifika parmak izini barındırır. Daha sonra kullanacağınız için uygulama KIMLIĞINI unutmayın. Ayrıca, **Bağlantılar** sayfasında aynı bilgileri de bulabilirsiniz. Bu sayfaya gitmek için pencerenin sol tarafındaki bölmede, **paylaşılan kaynaklar** bölümünün altındaki **Bağlantılar** ' ı seçin ve **azurerunasconnection** adlı bağlantı varlığına tıklayın.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Windows sanal masaüstünde rol ataması oluşturma

Daha sonra, **Azurerunasconnection** 'un Windows sanal masaüstü ile etkileşime girebilmesi için bir rol ataması oluşturmanız gerekir. Rol atamaları oluşturma izinlerine sahip bir hesapla oturum açmak için PowerShell 'i kullandığınızdan emin olun.

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü](/powershell/windows-virtual-desktop/overview/) indirip içeri aktarın. Windows sanal masaüstüne bağlanmak ve Kiracılarınızı göstermek için aşağıdaki PowerShell cmdlet 'lerini çalıştırın.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

# If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
# Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"

Get-RdsTenant
```

Ölçeklendirmek istediğiniz konak havuzlarıyla kiracıyı bulduğunuzda, **Azurerunasconnection** uygulama kimliğini toplamak ve rol atamasını oluşturmak için aşağıdaki cmdlet 'teki önceki cmdlet 'Ten aldığınız Windows sanal masaüstü kiracı adını kullanmak Için [Azure Otomasyonu farklı çalıştır hesabı oluşturma](#create-an-azure-automation-run-as-account) bölümündeki yönergeleri izleyin:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId "<applicationid>" -TenantName "<tenantname>"
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Azure Logic App ve yürütme zamanlaması oluşturma

Son olarak, Azure Logic App 'i oluşturmanız ve yeni ölçeklendirme aracınız için bir yürütme zamanlaması ayarlamanız gerekir.

1. Windows PowerShell'i açın.

2. Azure hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın.

    ```powershell
    Login-AzAccount
    ```

3. Azure Logic App oluşturmak için betiği indirmek üzere aşağıdaki cmdlet 'i çalıştırın.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. RDS sahibi veya RDS katkıda bulunan izinleri olan bir hesapla Windows sanal masaüstü 'nde oturum açmak için aşağıdaki cmdlet 'i çalıştırın.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

    # If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
    # Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"
    ```

5. Konak havuzunuzun Azure Logic App ve yürütme zamanlamasını oluşturmak için aşağıdaki PowerShell betiğini çalıştırın

    >[!NOTE]
    >Bu betiği, otomatik ölçeklendirme yapmak istediğiniz her konak havuzu için çalıştırmanız gerekir, ancak yalnızca bir Azure Otomasyonu hesabınız olması gerekir.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $RDBrokerURL = (Get-RdsContext).DeploymentUrl
    $WVDTenant = Get-RdsTenant | Out-GridView -OutputMode:Single -Title "Select your WVD tenant"
    $WVDHostPool = Get-RdsHostPool -TenantName $WVDTenant.TenantName | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURI' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "RDBrokerURL"                   = $RDBrokerURL                             # Optional. Default: "https://rdbroker.wvd.microsoft.com"
         "TenantGroupName"               = $WVDTenant.TenantGroupName               # Optional. Default: "Default Tenant Group"
         "TenantName"                    = $WVDTenant.TenantName
         "HostPoolName"                  = $WVDHostPool.HostPoolName
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    Betiği çalıştırdıktan sonra, aşağıdaki görüntüde gösterildiği gibi Azure Logic App bir kaynak grubunda yer almalıdır.

    >[!div class="mx-imgBorder"]
    >![Örnek bir Azure mantıksal uygulaması için genel bakış sayfasının bir görüntüsü.](../media/logic-app.png)

    Yineleme zaman dilimini veya saat dilimini değiştirme gibi yürütme zamanlamasında değişiklik yapmak için Azure Logic app otomatik ölçeklendirme Scheduler ' a gidin ve **Düzenle** ' yi seçerek Azure Logic App Designer ' a gidin.

    >[!div class="mx-imgBorder"]
    >![Azure Logic App Designer 'ın bir görüntüsü. Kullanıcının yineleme sürelerini ve Web kancası dosyasını düzenlemesini sağlayan yineleme ve Web kancası menüleri.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Ölçeklendirme aracınızı yönetme

Ölçeklendirme aracınızı oluşturdığınıza göre, çıktısına erişebilirsiniz. Bu bölümde yararlı bulabileceğiniz bazı özellikler açıklanmaktadır.

### <a name="view-job-status"></a>İş durumunu görüntüleme

Tüm runbook işlerinin özetlenen durumunu görüntüleyebilir veya Azure portal belirli bir runbook işinin daha ayrıntılı bir durumunu görüntüleyebilirsiniz.

Seçtiğiniz Azure Otomasyonu hesabınızın sağında, "Iş Istatistikleri" bölümünde, tüm runbook işlerinin özetlerinin bir listesini görüntüleyebilirsiniz. Pencerenin sol tarafındaki **işler** sayfasını açmak geçerli iş durumlarını, başlangıç zamanlarını ve tamamlanma zamanlarını gösterir.

>[!div class="mx-imgBorder"]
>![İş durumu sayfasının ekran görüntüsü.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Günlükleri görüntüleme ve ölçeklendirme aracı çıktısı

Runbook 'unuzu açıp işi seçerek genişleme ve ölçek işlemleri işlemlerini izleyebilirsiniz.

Azure Otomasyonu hesabını barındıran kaynak grubunuzda runbook 'a gidin ve **Genel Bakış ' ı** seçin. Genel Bakış sayfasında, aşağıdaki görüntüde gösterildiği gibi, ölçek aracı çıktısını görüntülemek için **son işler** altında bir iş seçin.

>[!div class="mx-imgBorder"]
>![Ölçeklendirme aracı için çıkış penceresinin bir görüntüsü.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Runbook betiği sürüm numarasını denetleyin

Runbook dosyasını Azure Otomasyonu hesabınızda açıp **görüntüle**' ye tıklayarak runbook betiğinin hangi sürümünün kullandığınızı kontrol edebilirsiniz. Ekranın sağ tarafında runbook 'un bir betiği görüntülenir. Betikte, sürüm numarasını `v#.#.#` bölümünün altındaki biçimde görürsünüz `SYNOPSIS` . En son sürüm numarasını [buradan](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/basicScale.ps1#L1)bulabilirsiniz. Runbook betiğinizdeki bir sürüm numarası görmüyorsanız, bu betiğin önceki bir sürümünü çalıştırdığınız ve hemen güncelleştirmeniz gereken anlamına gelir. Runbook betiğinizi güncelleştirmeniz gerekiyorsa [Azure Otomasyonu hesabı oluşturma veya güncelleştirme](#create-or-update-an-azure-automation-account)bölümündeki yönergeleri izleyin.

### <a name="reporting-issues"></a>Raporlama sorunları

Bir sorunu bildirmenizde, sorun gidermenize yardımcı olması için aşağıdaki bilgileri sağlamanız gerekir:

- İşin, soruna neden olan **Tüm Günlükler** sekmesinden günlüğün tamamı. Günlüğü nasıl alabileceğinizi öğrenmek için, [günlükleri görüntüleme ve ölçeklendirme aracı çıkışını](#view-logs-and-scaling-tool-output)içindeki yönergeleri izleyin. Günlükte gizli veya özel bilgiler varsa, sorunu bize göndermeden önce kaldırabilirsiniz.

- Kullanmakta olduğunuz runbook betiği sürümü. Sürüm numarasını nasıl alacağınız hakkında bilgi edinmek için bkz [. runbook betiği sürüm numarasını denetleme](#check-the-runbook-script-version-number)

- Azure Otomasyonu hesabınızda yüklü olan aşağıdaki PowerShell modüllerinin her birinin sürüm numarası. Bu modülleri bulmak için Azure Otomasyonu hesabını açın, pencerenin sol tarafındaki bölmede bulunan **paylaşılan kaynaklar** bölümünde **modüller** ' i seçin ve ardından modülün adını arayın.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Microsoft. RDInfra. RDPowershell

- [Farklı Çalıştır hesabınız](#create-an-azure-automation-run-as-account)için sona erme tarihi. Bunu bulmak için Azure Otomasyonu hesabınızı açın ve pencerenin sol tarafındaki bölmede **Hesap ayarları** altında **Farklı Çalıştır hesapları** ' nı seçin. Sona erme tarihi, **Azure farklı çalıştır hesabı** altında olmalıdır.

### <a name="log-analytics"></a>Log Analytics

Log Analytics kullanmaya karar verdiyseniz, tüm günlük verilerini Log Analytics çalışma alanınızın **Günlükler** görünümündeki **özel Günlükler** altında **WVDTenantScale_CL** adlı özel bir günlükte görüntüleyebilirsiniz. Yararlı bulabileceğiniz bazı örnek sorgular listelendik.

- Bir konak havuzunun tüm günlüklerini görmek için aşağıdaki sorguyu girin

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Konak havuzunuzdaki Şu anda çalışmakta olan oturum ana bilgisayar VM 'lerinin ve etkin kullanıcı oturumlarının toplam sayısını görüntülemek için aşağıdaki sorguyu girin

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Bir konak havuzundaki tüm oturum ana bilgisayar VM 'lerinin durumunu görüntülemek için aşağıdaki sorguyu girin

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Herhangi bir hata ve uyarı görüntülemek için aşağıdaki sorguyu girin

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Sorun bildirme

Ölçeklendirme aracı için sorun raporları Şu anda Microsoft Desteği tarafından işleniyor. Bir sorun raporu oluştururken, [sorunları raporlama](#reporting-issues)konusundaki yönergeleri izlediğinizden emin olun. Araçla ilgili geri bildiriminiz varsa veya yeni özellikler istemek istiyorsanız, [RDS GitHub sayfasında](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool)"4-WVD-Ölçeklendirmesi-Tool" etiketli bir GitHub sorunu açın.
