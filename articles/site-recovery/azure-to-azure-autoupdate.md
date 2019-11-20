---
title: Mobility hizmetinin Azure Site Recovery otomatik güncelleştirmesi
description: Azure Site Recovery kullanarak Azure VM 'Leri çoğaltılırken Mobility hizmetinin otomatik güncelleştirmesine genel bakış.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/24/2019
ms.author: rajanaki
ms.openlocfilehash: 9479ccce534f9c9d48a0aa08d4ea887bc4f30acb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078855"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure 'dan Azure 'a çoğaltma için Mobility hizmetini otomatik güncelleştirme

Azure Site Recovery, herhangi bir sorunu gidermek ve mevcut özellikleri geliştirmek ya da yenilerini eklemek için aylık bir sürüm temposunda kullanır. Hizmetle güncel kalmasını sağlamak için her ay yama dağıtımını planlamanız gerekir. Her yükseltmeyle ilişkili ek yükün oluşmasını önlemek için Site Recovery bileşen güncelleştirmelerini yönetmesine izin verebilirsiniz.

[Azure 'Dan Azure 'a olağanüstü durum kurtarma mimarisi](azure-to-azure-architecture.md)bölümünde belirtildiği gibi Mobility hizmeti, çoğaltmanın etkinleştirildiği tüm Azure sanal makinelerine (VM) yüklenir ve VM 'Leri bir Azure bölgesinden diğerine çoğaltırken. Otomatik güncelleştirmeleri kullandığınızda, her yeni sürüm Mobility hizmeti uzantısını güncelleştirir.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Otomatik güncelleştirmeler nasıl çalışır?

Güncelleştirmeleri yönetmek için Site Recovery kullandığınızda, kasa ile aynı abonelikte oluşturulan bir Otomasyon hesabı aracılığıyla küresel bir runbook (Azure hizmetleri tarafından kullanılır) dağıtır. Her kasa bir Otomasyon hesabı kullanır. Runbook, etkin otomatik güncelleştirmeler için bir kasadaki her bir VM 'yi denetler ve daha yeni bir sürüm varsa Mobility hizmeti uzantısını yükseltir.

Varsayılan runbook zamanlaması, çoğaltılan VM 'nin coğrafi bölge saat diliminde 12:00 ' de günlük olarak yinelenir. Ayrıca, Otomasyon hesabı aracılığıyla runbook zamanlamasını değiştirebilirsiniz.

> [!NOTE]
> Güncelleştirme paketi 35 ' den başlayarak, güncelleştirmeler için kullanılacak mevcut bir Otomasyon hesabı seçebilirsiniz. Bu güncelleştirmeden önce, Site Recovery varsayılan olarak bu hesabı oluşturdunuz. Yalnızca bir sanal makine için çoğaltmayı etkinleştirdiğinizde bu seçeneği belirleyebilirsiniz. Bu, çoğaltılan bir VM için kullanılamaz. Seçtiğiniz ayar aynı kasada korunan tüm Azure VM 'Leri için geçerlidir.
 
> Otomatik güncelleştirmeleri açmak, Azure sanal makinelerinizin yeniden başlatılmasını gerektirmez veya devam eden çoğaltmayı etkiler.

> Otomasyon hesabı 'nda iş faturalaması, bir ayda kullanılan iş çalışma zamanı dakikalarının sayısına bağlıdır. Varsayılan olarak, 500 dakika bir Otomasyon hesabı için serbest birimler olarak dahil edilir. İş yürütme, her gün yaklaşık bir dakika boyunca birkaç saniye sürer ve serbest birimler olarak ele alınmıştır.

| Dahil edilen ücretsiz birimler (her ay) | Fiyat |
|---|---|
| İş çalışma zamanı 500 dakika | ₹ 0.14/dakika

## <a name="enable-automatic-updates"></a>Otomatik güncelleştirmeleri etkinleştir

Site Recovery güncelleştirmeleri aşağıdaki yollarla yönetmesine izin verebilirsiniz.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Çoğaltmayı etkinleştir adımının bir parçası olarak yönetin

VM veya [Kurtarma Hizmetleri kasasından](azure-to-azure-how-to-enable-replication.md) [başlayan bir](azure-to-azure-quickstart.md) VM için çoğaltmayı etkinleştirdiğinizde, Site Recovery Site Recovery uzantısı için güncelleştirmeleri yönetmesine veya el ile yönetmeye izin verebilirsiniz.

![Uzantı ayarları](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Kasa içindeki uzantı güncelleştirme ayarlarını değiştirin

1. Kasanın içinde > **Site Recovery altyapısını** **Yönet** ' e gidin.
2. **Azure sanal makineler** > **uzantı güncelleştirme ayarları**' nın altında, geçişi **yönetmeye izin ver Site Recovery** açın. El ile yönetmek için devre dışı bırakın. 
3. **Kaydet**’i seçin.

![Uzantı güncelleştirme ayarları](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> **Yönetilecek Site Recovery Izin ver**' i seçtiğinizde, bu ayar ilgili kasadaki tüm sanal makinelere uygulanır.


> [!Note]
> Her iki seçenek de güncelleştirmeleri yönetmek için kullanılan Otomasyon hesabını bilgilendirir. Bu özelliği bir kasada ilk kez kullanıyorsanız, varsayılan olarak yeni bir Otomasyon hesabı oluşturulur. Alternatif olarak, ayarı özelleştirebilir ve var olan bir Otomasyon hesabı seçebilirsiniz. Aynı kasadaki tüm sonraki etkin çoğaltmalar, daha önce oluşturulmuş olanı kullanır. Şu anda açılan listede yalnızca kasada aynı kaynak grubundaki Otomasyon hesapları listelenir.  

> [!IMPORTANT]
> Aşağıdaki betiğin özel bir Otomasyon hesabı Için bir Otomasyon hesabı bağlamında çalıştırılması gerekir, aşağıdaki betiği kullanın:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name Az.Accounts | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Güncelleştirmeleri el ile yönetin

1. Sanal makinelerinize yüklü Mobility hizmeti için yeni güncelleştirmeler varsa, şu bildirimi görürsünüz: "Yeni Site Recovery çoğaltma Aracısı güncelleştirmesi var. Yüklemek için tıklayın "

     ![Çoğaltılan Öğeler penceresi](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. VM seçim sayfasını açmak için bildirimi seçin.
3. Yükseltmek istediğiniz VM 'Leri seçin ve ardından **Tamam**' ı seçin. Mobility hizmetini Güncelleştir seçili her VM için başlatılır.

     ![Çoğaltılan öğeler VM listesi](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Yaygın sorunlar ve sorun giderme

Otomatik güncelleştirmelerle ilgili bir sorun varsa, kasa panosundaki **yapılandırma sorunları** altında bir hata bildirimi görürsünüz.

Otomatik güncelleştirmeleri etkinleştiremediğimiz takdirde aşağıdaki yaygın hatalara ve önerilen eylemlere bakın:

- **Hata**: Azure farklı çalıştır hesabı (hizmet sorumlusu) oluşturma ve hizmet sorumlusuna katkıda bulunan rolü verme izinleriniz yok.

   **Önerilen eylem**: oturum açan hesabın katkı olarak atandığından emin olun ve yeniden deneyin. İzinleri atama hakkında daha fazla bilgi için [kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) konusundaki gerekli izinler bölümüne bakın.
 
   Otomatik güncelleştirmeleri etkinleştirdikten sonra birçok sorunu gidermek için, **Onar**' ı seçin. Onar düğmesi kullanılamıyorsa, uzantı güncelleştirme ayarları bölmesinde hata iletisine bakın.

   ![Uzantı güncelleştirme ayarlarındaki Site Recovery hizmeti onarma düğmesi](./media/azure-to-azure-autoupdate/repair.png)

- **Hata**: farklı çalıştır hesabının kurtarma hizmetleri kaynağına erişim izni yok.

    **Önerilen eylem**: [Farklı Çalıştır hesabını silin ve yeniden oluşturun](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Ya da Otomasyon farklı çalıştır hesabının Azure Active Directory uygulamasının kurtarma hizmetleri kaynağına erişimi olduğundan emin olun.

- **Hata**: Run as hesabı bulunamadı. Bunlardan biri silinmiş veya oluşturulmamış-Azure Active Directory uygulama, hizmet sorumlusu, rol, Otomasyon sertifikası varlığı, Otomasyon bağlantı varlığı veya Parmak Izi sertifika ile bağlantı arasında aynı değil. 

    **Önerilen eylem**: [Farklı Çalıştır hesabını silin ve yeniden oluşturun](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

-  **Hata**: Otomasyon hesabı tarafından kullanılan Azure farklı çalıştır sertifikası sona ermek üzere. 

    Farklı Çalıştır hesabı için oluşturulan otomatik olarak imzalanan sertifika, oluşturma tarihinden itibaren bir yıl dolar. Sertifikayı süresi dolmadan önce herhangi bir zamanda yenileyebilirsiniz. E-posta bildirimlerine kaydolduysanız, sizin tarafınızdan bir eylem gerektiğinde e-postalar da alacaksınız. Bu hata, sona erme tarihinden iki ay önce gösterilir ve sertifikanın süresi dolmuşsa kritik bir hataya değişir. Sertifikanın süresi dolduktan sonra, aynı şekilde yenileene kadar otomatik güncelleştirme işlevsel olmayacaktır.

   **Önerilen eylem**: Bu sorunu çözmek Için ' Onar ' ve ardından ' Sertifikayı Yenile ' seçeneğine tıklayın.
    
   ![Yenile-CERT](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> Sertifikayı yeniledikten sonra, geçerli durumun güncelleştirilmesini sağlamak için lütfen sayfayı yenileyin.
