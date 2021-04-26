---
title: Mobility hizmetinin Azure Site Recovery otomatik güncelleştirmesi
description: Azure Site Recovery kullanarak Azure VM 'Leri çoğaltılırken Mobility hizmetinin otomatik güncelleştirmesine genel bakış.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: sideeksh
ms.openlocfilehash: 42d3c74229ab7eeec0ac716073a9e631775fd002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187349"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure 'dan Azure 'a çoğaltma için Mobility hizmetini otomatik güncelleştirme

Azure Site Recovery, herhangi bir sorunu gidermek ve mevcut özellikleri geliştirmek ya da yenilerini eklemek için aylık bir sürüm temposunda kullanır. Hizmetle güncel kalmasını sağlamak için her ay yama dağıtımını planlamanız gerekir. Her yükseltmeyle ilişkili ek yükün önüne geçmek için Site Recovery bileşen güncelleştirmelerini yönetmesine izin verebilirsiniz.

[Azure 'Dan Azure 'a olağanüstü durum kurtarma mimarisinde](azure-to-azure-architecture.md)belirtildiği gibi, Mobility hizmeti bir Azure bölgesinden diğerine çoğaltma özelliği etkinleştirilmiş olan tüm Azure sanal makinelerine (VM) yüklenir. Otomatik güncelleştirmeleri kullandığınızda, her yeni sürüm Mobility hizmeti uzantısını güncelleştirir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Otomatik güncelleştirmeler nasıl çalışır?

Güncelleştirmeleri yönetmek için Site Recovery kullandığınızda, kasa ile aynı abonelikte oluşturulan bir Otomasyon hesabı aracılığıyla küresel bir runbook (Azure hizmetleri tarafından kullanılır) dağıtır. Her kasa bir Otomasyon hesabı kullanır. Bir kasadaki her VM için Runbook etkin otomatik güncelleştirmeleri denetler. Mobility hizmeti uzantısının daha yeni bir sürümü varsa, güncelleştirme yüklenir.

Varsayılan runbook zamanlaması, çoğaltılan sanal makinenin coğrafi bölge saat diliminde 12:00 ile günde bir kez gerçekleşir. Ayrıca, Otomasyon hesabı aracılığıyla runbook zamanlamasını değiştirebilirsiniz.

> [!NOTE]
> [Güncelleştirme paketi 35](site-recovery-whats-new.md#updates-march-2019)' den başlayarak, güncelleştirmeler için kullanılacak mevcut bir Otomasyon hesabı seçebilirsiniz. Güncelleştirme paketi 35 ' dan önce varsayılan olarak otomasyon hesabını Site Recovery. Yalnızca bir VM için çoğaltmayı etkinleştirdiğinizde bu seçeneği belirleyebilirsiniz. Zaten çoğaltma özelliği etkin olan bir VM için kullanılamaz. Seçtiğiniz ayar aynı kasada korunan tüm Azure sanal makineleri için geçerlidir.

Otomatik güncelleştirmeleri açmak, Azure sanal makinelerinizin yeniden başlatılmasını gerektirmez veya devam eden çoğaltmayı etkiler.

Otomasyon hesabı 'nda iş faturalaması, bir ayda kullanılan iş çalışma zamanı dakikalarının sayısına bağlıdır. İş yürütme, her gün yaklaşık bir dakika boyunca birkaç saniye sürer ve serbest birimler olarak ele alınmıştır. Varsayılan olarak, aşağıdaki tabloda gösterildiği gibi, 500 dakika bir Otomasyon hesabı için boş birimler olarak dahil edilir:

| Dahil edilen ücretsiz birimler (her ay) | Fiyat |
|---|---|
| İş çalışma zamanı 500 dakika | ₹ 0.14/dakika

## <a name="enable-automatic-updates"></a>Otomatik güncelleştirmeleri etkinleştir

Site Recovery uzantı güncelleştirmelerini yönetebilmenin birkaç yolu vardır:

- [Çoğaltmayı etkinleştir adımının bir parçası olarak yönetin](#manage-as-part-of-the-enable-replication-step)
- [Kasa içindeki uzantı güncelleştirme ayarlarını değiştirin](#toggle-the-extension-update-settings-inside-the-vault)
- [Güncelleştirmeleri el ile yönetin](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Çoğaltmayı etkinleştir adımının bir parçası olarak yönetin

VM veya [Kurtarma Hizmetleri kasasından](azure-to-azure-how-to-enable-replication.md) [başlayan bir](azure-to-azure-quickstart.md) VM için çoğaltmayı etkinleştirdiğinizde, Site Recovery Site Recovery uzantısı için güncelleştirmeleri yönetmesine veya el ile yönetmeye izin verebilirsiniz.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Uzantı ayarları":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Kasa içindeki uzantı güncelleştirme ayarlarını değiştirin

1. Kurtarma Hizmetleri kasasından   >  **Site Recovery altyapıyı** Yönet ' e gidin.
1. **Azure sanal makineler**  >  **uzantı güncelleştirme ayarları**' nın altında  >  **Site Recovery yönetmesine izin ver**' i seçin.

   Uzantıyı el ile yönetmek için **kapalı**' yı seçin.

1. **Kaydet**’i seçin.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Uzantı güncelleştirme ayarları":::

> [!IMPORTANT]
> **Yönetilecek Site Recovery Izin ver**' i seçtiğinizde, bu ayar kasadaki tüm sanal makinelere uygulanır.

> [!NOTE]
> Her iki seçenek de güncelleştirmeleri yönetmek için kullanılan Otomasyon hesabını bilgilendirir. Bu özelliği bir kasada ilk kez kullanıyorsanız, varsayılan olarak yeni bir Otomasyon hesabı oluşturulur. Alternatif olarak, ayarı özelleştirebilir ve var olan bir Otomasyon hesabı seçebilirsiniz. Tanımlandıktan sonra, aynı kasada çoğaltmayı etkinleştirmeye yönelik tüm sonraki eylemler, seçilen Otomasyon hesabını kullanır. Şu anda, açılan menü yalnızca kasala aynı kaynak grubundaki Otomasyon hesaplarını listeler.

> [!IMPORTANT]
> Aşağıdaki betiğin bir Otomasyon hesabı bağlamında çalıştırılması gerekir.
Özel bir Otomasyon hesabı için aşağıdaki betiği kullanın:

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
$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
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

1. Sanal makinelerinize yüklü Mobility hizmeti için yeni güncelleştirmeler varsa, aşağıdaki bildirimi görürsünüz: **yeni Site Recovery çoğaltma Aracısı güncelleştirmesi var. Yüklemek için tıklayın.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Çoğaltılan Öğeler penceresi":::

1. VM seçim sayfasını açmak için bildirimi seçin.
1. Yükseltmek istediğiniz VM 'Leri seçin ve ardından **Tamam**' ı seçin. Mobility hizmetini Güncelleştir seçili her VM için başlatılır.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Çoğaltılan öğeler VM listesi":::

## <a name="common-issues-and-troubleshooting"></a>Yaygın sorunlar ve sorun giderme

Otomatik güncelleştirmelerle ilgili bir sorun varsa, kasa panosundaki **yapılandırma sorunları** altında bir hata bildirimi görürsünüz.

Otomatik güncelleştirmeleri etkinleştiremediğimiz takdirde aşağıdaki yaygın hatalara ve önerilen eylemlere bakın:

- **Hata**: Azure farklı çalıştır hesabı (hizmet sorumlusu) oluşturma ve hizmet sorumlusuna katkıda bulunan rolü verme izinleriniz yok.

  **Önerilen eylem**: oturum açan hesabın katkı olarak atandığından emin olun ve yeniden deneyin. İzinleri atama hakkında daha fazla bilgi için bkz. [nasıl yapılır: Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Otomatik güncelleştirmeleri etkinleştirdikten sonra birçok sorunu gidermek için, **Onar**' ı seçin. Onar düğmesi kullanılamıyorsa, uzantı güncelleştirme ayarları bölmesinde hata iletisine bakın.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Uzantı güncelleştirme ayarlarındaki Site Recovery hizmeti onarma düğmesi":::

- **Hata**: farklı çalıştır hesabının kurtarma hizmetleri kaynağına erişim izni yok.

  **Önerilen eylem**: [Farklı Çalıştır hesabını silin ve yeniden oluşturun](../automation/manage-runas-account.md). Ya da Otomasyon farklı çalıştır hesabının Azure Active Directory uygulamasının kurtarma hizmetleri kaynağına erişebildiğinizden emin olun.

- **Hata**: Run as hesabı bulunamadı. Bunlardan biri silinmiş veya oluşturulmamış-Azure Active Directory uygulama, hizmet sorumlusu, rol, Otomasyon sertifikası varlığı, Otomasyon bağlantı varlığı veya Parmak Izi sertifika ile bağlantı arasında aynı değil.

  **Önerilen eylem**: [Farklı Çalıştır hesabını silin ve yeniden oluşturun](../automation/manage-runas-account.md).

- **Hata**: Otomasyon hesabı tarafından kullanılan Azure farklı çalıştır sertifikası sona ermek üzere.

  Farklı Çalıştır hesabı için oluşturulan otomatik olarak imzalanan sertifika, oluşturma tarihinden itibaren bir yıl dolar. Sertifikayı süresi dolmadan önce herhangi bir zamanda yenileyebilirsiniz. E-posta bildirimlerine kaydolduysanız, sizin tarafınızdan bir eylem gerektiğinde e-postalar da alacaksınız. Bu hata, sona erme tarihinden iki ay önce gösterilir ve sertifikanın süresi dolmuşsa kritik bir hataya değişir. Sertifikanın süresi dolduktan sonra, aynı şekilde yenileene kadar otomatik güncelleştirme işlevsel olmayacaktır.

  **Önerilen eylem**: Bu sorunu çözmek için **Onar** ve **Sertifikayı Yenile**' yi seçin.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="Yenile-CERT":::

  > [!NOTE]
  > Sertifikayı yeniledikten sonra, geçerli durumu göstermek için sayfayı yenileyin.
