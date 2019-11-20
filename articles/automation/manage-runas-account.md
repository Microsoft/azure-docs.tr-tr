---
title: Azure Otomasyonu farklı çalıştır hesaplarını yönetme
description: Bu makalede, farklı çalıştır hesaplarınızın PowerShell ile veya portaldan nasıl yönetileceği açıklanır.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fd7e94261d8302224b0e31e5f4ac46978dfa812f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690871"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Otomasyonu farklı çalıştır hesaplarını yönetme

Azure Otomasyonu 'nda farklı çalıştır hesapları Azure cmdlet 'leriyle Azure 'da kaynak yönetimine yönelik kimlik doğrulaması sağlamak için kullanılır.

Farklı Çalıştır hesabı oluşturduğunuzda, Azure Active Directory yeni bir hizmet sorumlusu kullanıcısı oluşturur ve bu kullanıcıya abonelik düzeyinde katılımcı rolü atar. Azure sanal makinelerinde karma runbook çalışanları kullanan runbook 'lar için Azure kaynaklarınızın kimliğini doğrulamak üzere farklı çalıştır hesapları yerine [Azure kaynakları için Yönetilen kimlikler](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) kullanabilirsiniz.

İki farklı çalıştır hesabı türü vardır:

* **Azure farklı çalıştır hesabı** -bu hesap, [Kaynak Yöneticisi dağıtım modeli](../azure-resource-manager/resource-manager-deployment-model.md) kaynaklarını yönetmek için kullanılır.
  * Otomatik olarak imzalanan bir sertifika ile Azure AD uygulaması oluşturur, Azure AD’de bu uygulama için bir hizmet sorumlusu hesabı oluşturur ve geçerli aboneliğinizde hesap için Katkıda Bulunan rolünü atar. Bu ayarı Sahip veya başka bir rolle değiştirebilirsiniz. Daha fazla bilgi için bkz. [Azure Otomasyonu’nda rol tabanlı erişim denetimi](automation-role-based-access-control.md).
  * Belirtilen Otomasyon hesabında *AzureRunAsCertificate* adlı bir Otomasyon sertifikası varlığı oluşturur. Sertifika varlıkları, Azure AD uygulaması tarafından kullanılan sertifika özel anahtarını içerir.
  * Belirtilen Otomasyon hesabında *AzureRunAsConnection* adlı bir Otomasyon bağlantı varlığı oluşturur. Bağlantı varlığı applicationId, tenantId, subscriptionId ve sertifika parmak izini içerir.

* **Azure klasik farklı çalıştır hesabı** -bu hesap [klasik dağıtım modeli](../azure-resource-manager/resource-manager-deployment-model.md) kaynaklarını yönetmek için kullanılır.
  * Abonelikte bir yönetim sertifikası oluşturur
  * Belirtilen Otomasyon hesabında *AzureClassicRunAsCertificate* adlı bir Otomasyon sertifikası varlığı oluşturur. Sertifika varlığı, yönetim sertifikası tarafından kullanılan sertifika özel anahtarını içerir.
  * Belirtilen Otomasyon hesabında *AzureClassicRunAsConnection* adlı bir Otomasyon bağlantı varlığı oluşturur. Bağlantı varlığı; abonelik adı, subscriptionId ve sertifika varlık adını içerir.
  * Oluşturmak veya yenilemek için abonelikte ortak yönetici olması gerekir

  > [!NOTE]
  > Azure bulut çözümü sağlayıcısı (Azure CSP) abonelikleri yalnızca Azure Resource Manager modelini destekler, Azure Resource Manager olmayan hizmetler programda kullanılamaz. CSP aboneliği kullanılırken, Azure klasik farklı çalıştır hesabı oluşturulmaz. Azure farklı çalıştır hesabı hala oluşturulmuştur. CSP abonelikleri hakkında daha fazla bilgi edinmek için bkz. [CSP aboneliklerinde kullanılabilir hizmetler](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

  > [!NOTE]
  > Farklı Çalıştır hesabı için hizmet sorumlusu, varsayılan olarak Azure Active Directory okuma iznine sahip değildir. Azure Active Directory 'yi okuma veya yönetme izinleri eklemek istiyorsanız, **API izinleri**altında hizmet sorumlusu üzerinde bu izni vermeniz gerekir. Daha fazla bilgi için bkz. [Web API 'lerine erişim Izinleri ekleme](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Farklı Çalıştır hesaplarını yapılandırma izinleri

Farklı Çalıştır hesabı oluşturmak veya güncelleştirmek için, belirli ayrıcalıklara ve izinlere sahip olmanız gerekir. Azure Active Directory ve bir abonelikteki bir sahip uygulama Yöneticisi tüm görevleri tamamlayabilir. Görevlerinin ayrılmanız durumunda aşağıdaki tabloda, görevlerin bir listesi, eşdeğer cmdlet ve gerekli izinler gösterilmektedir:

|Görev|Cmdlet  |Minimum Izinler  |İzinleri ayarladığınız yer|
|---|---------|---------|---|
|Azure AD uygulaması oluşturma|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Uygulama geliştirici rolü<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Ana > Azure Active Directory > uygulama kayıtları |
|Uygulamaya bir kimlik bilgisi ekleyin.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Uygulama Yöneticisi veya genel yönetıcı<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Ana > Azure Active Directory > uygulama kayıtları|
|Azure AD hizmet sorumlusu oluşturma ve edinme|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Uygulama Yöneticisi veya genel yönetıcı<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Ana > Azure Active Directory > uygulama kayıtları|
|Belirtilen sorumlu için RBAC rolünü ata veya al|[New-Azurermroleatama](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-Azurermroleatama](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Aşağıdaki izinlere sahip olmanız gerekir:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Ya da şunları yapın:</br></br>Kullanıcı erişimi Yöneticisi veya sahibi        | [Abonelik](../role-based-access-control/role-assignments-portal.md)</br>Ana > abonelikleri > \<subscription adı \>-Access Control (ıAM)|
|Otomasyon sertifikası oluşturma veya kaldırma|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Kaynak grubunda katkıda bulunan         |Otomasyon hesabı kaynak grubu|
|Otomasyon bağlantısı oluşturma veya kaldırma|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Kaynak grubunda katkıda bulunan |Otomasyon hesabı kaynak grubu|

<sup>1</sup> Azure AD kiracınızdaki yönetici olmayan kullanıcılar, **Kullanıcı ayarları** sayfasında **uygulamalar kaydedebiliyorsanız** , [ad uygulamalarını kayıt](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) **edebilir.** Uygulama kayıtları ayarı **Hayır**olarak ayarlanırsa, bu eylemi gerçekleştiren kullanıcının önceki tabloda tanımlanmaları gerekir.

Aboneliğin **genel yönetici** rolüne eklenmeden önce aboneliğin Active Directory örneğinin bir üyesi değilseniz, konuk olarak eklendiniz. Bu durumda, **Otomasyon hesabı ekle** sayfasında bir `You do not have permissions to create…` uyarısı alırsınız. Önce **genel yönetici** rolüne eklenen kullanıcılar, aboneliğin Active Directory örneğinden kaldırılabilir ve Active Directory bir tam Kullanıcı haline getirmek için yeniden eklenebilir. Bu durumu doğrulamak için Azure portalındaki **Azure Active Directory** bölmesinde **Kullanıcılar ve gruplar**’ı, **Tüm kullanıcılar**’ı seçin ve belirli bir kullanıcıyı seçtikten sonra **Profil**’i seçin. Kullanıcı profili altındaki **Kullanıcı türü** özniteliğinin **Konuk** olmaması gerekir.

## <a name="permissions-classic"></a>Klasik farklı çalıştır hesaplarını yapılandırma izinleri

Klasik farklı çalıştır hesaplarını yapılandırmak veya yenilemek için, abonelik düzeyinde **ortak yönetici** rolüne sahip olmanız gerekir. Klasik izinler hakkında daha fazla bilgi için bkz. [Azure klasik abonelik yöneticileri](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Portalda bir farklı çalıştır hesabı oluşturma

Bu bölümde, Azure portalında Azure Otomasyonu hesabınızı güncelleştirmek için aşağıdaki adımları uygulayın. Farklı Çalıştır ve Klasik Farklı Çalıştır hesaplarını ayrı ayrı oluşturabilirsiniz. Klasik kaynak oluşturmanıza gerek yoksa yalnızca Azure Farklı Çalıştır hesabını oluşturabilirsiniz.

1. Azure portalında Abonelik Yöneticileri rolünün üyesi ve aboneliğin ortak yöneticisi olan bir hesapla oturum açın.
2. Azure portalında **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Otomasyon** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Automation Hesapları**’nı seçin.
3. **Otomasyon Hesapları** sayfasındaki Otomasyon hesapları listesinden Otomasyon hesabınızı seçin.
4. Sol bölmedeki **Hesap Ayarları** bölümünde **Farklı Çalıştır Hesapları**'nı seçin.
5. Gereken hesaba bağlı olarak **Azure Farklı Çalıştır Hesabı**’nı veya **Azure Klasik Farklı Çalıştır Hesabı**’nı seçin. Seçim sonrasında **Azure Farklı Çalıştır Ekle** veya **Azure Klasik Farklı Çalıştır Hesabı Ekle** bölmesi görüntülenir ve genel bakış bilgilerini gözden geçirdikten sonra Farklı Çalıştır hesabı oluşturma işlemine devam etmek için **Oluştur**’a tıklamanız gerekir.
6. Azure Farklı Çalıştır hesabını oluşturduğu sırada menünün **Bildirimler** öğesi altında ilerleme durumunu izleyebilirsiniz. Hesabın oluşturulduğunu belirten bir başlık da gösterilir. Bu işlemin tamamlanması birkaç dakika sürebilir.

## <a name="create-run-as-account-using-powershell"></a>PowerShell kullanarak farklı çalıştır hesabı oluşturma

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki listede, PowerShell 'de farklı çalıştır hesabı oluşturma gereksinimleri verilmiştir:

* Windows 10 veya Windows Server 2016 Azure Resource Manager modüller 3.4.1 ve üzeri. PowerShell betiği önceki Windows sürümlerini desteklemez.
* Azure PowerShell 1.0 ve üzeri. PowerShell 1.0 sürümü hakkında bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azureps-cmdlets-docs).
* *–AutomationAccountName* ve *-ApplicationDisplayName* parametrelerinin değeri olarak başvurulan bir Otomasyon hesabı.
* [Farklı Çalıştır hesaplarını yapılandırmak Için gerekli izinler](#permissions) bölümünde listelenenlerin eşdeğeri olan izinler

Betik için gerekli parametreler olan *SubscriptionID*, *ResourceGroup*ve *automationaccountname*değerlerini almak için aşağıdaki adımları izleyin:

1. Azure portalında **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Otomasyon** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Automation Hesapları**’nı seçin.
1. Otomasyon hesap sayfasında Otomasyon hesabınızı seçin ve ardından **Hesap Ayarları** altında **Özellikler**’i seçin.
1. **Özellikler** SAYFASıNDAKI **abonelik kimliği**, **adı**ve **kaynak grubu** değerlerini göz önünde edin.

   ![Otomasyon hesabı "Özellikler" sayfası](media/manage-runas-account/automation-account-properties.png)

Bu PowerShell betiği aşağıdaki yapılandırmalar için destek içerir:

* Otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı oluşturun.
* Otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturun.
* Kuruluş sertifika yetkiliniz (CA) tarafından verilen bir sertifika kullanarak bir Farklı Çalıştır ve Klasik Farklı Çalıştır hesabı oluşturun.
* Azure Kamu bulutunda otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturun.

>[!NOTE]
> Klasik Farklı Çalıştır hesabı oluşturma seçeneğini belirlerseniz, betik yürütüldükten sonra Otomasyon hesabının oluşturulduğu abonelik için yönetim deposuna ortak sertifikayı (.cer dosya adı uzantısı) yükleyin.

1. Aşağıdaki betiği bilgisayarınıza kaydedin. Bu örnekte *New-RunAsAccount.ps1* dosya adıyla kaydedin.

   Betik, kaynak oluşturmak için birden çok Azure Resource Manager cmdlet 'i kullanır. Yukarıdaki [izinler](#permissions) tablosu cmdlet 'leri ve gereken izinlerini gösterir.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

    > [!IMPORTANT]
    > **Add-azurermaccount** artık **Connect-azurermaccount**için bir diğer addır. Kitaplık öğelerinizi ararken, **Connect-AzureRMAccount**' u görmüyorsanız **Add-azurermaccount**komutunu kullanabilir veya bir Otomasyon hesabınızda [modüllerinizi güncelleştirebilirsiniz](automation-update-azure-modules.md) .

1. Bilgisayarınızda **Windows PowerShell**’i yükseltilmiş kullanıcı haklarına sahip **Başlat** ekranından başlatın.
1. Yükseltilmiş komut satırı kabuğundan, 1. adımda oluşturduğunuz komut dosyasını içeren klasöre gidin.
1. İstediğiniz yapılandırmanın parametre değerlerini kullanarak betiği yürütün.

    **Otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı oluşturma**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturma**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Kurumsal sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturma**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Azure Kamu bulutunda otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturma**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Betik yürütüldükten sonra Azure kimlik doğrulamasını yapmanız istenecektir. Aboneliğin yöneticiler rolünün üyesi ve aboneliğin ortak yöneticisi olan bir hesapla oturum açın.

Betik başarıyla yürütüldükten sonra aşağıdakilere dikkat edin:

* Otomatik olarak imzalanan bir ortak sertifika (.cer dosyası) ile Klasik Farklı Çalıştır hesabı oluşturduysanız, betik bu hesabı oluşturup bilgisayarınızdaki geçici dosya klasörüne, PowerShell oturumunu yürütmek için kullandığınız *%USERPROFILE%\AppData\Local\Temp* kullanıcı profili altında kaydeder.

* Kurumsal ortak sertifika (.cer file) ile bir Klasik Farklı Çalıştır hesabı oluşturduysanız bu sertifikayı kullanın. [Azure Portal bir yönetim API 'si sertifikası yükleme](../azure-api-management-certs.md)yönergelerini izleyin.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silme

Bu bölümde bir Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silip yeniden oluşturma işlemi açıklamaktadır. Bu eylemi gerçekleştirdiğinizde Otomasyon hesabı korunur. Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını sildikten sonra Azure portalında yeniden oluşturabilirsiniz.

1. Azure portalında Otomasyon hesabınızı açın.

2. **Otomasyon hesabı** sayfasında **Farklı Çalıştır Hesapları**'nı seçin.

3. **Farklı Çalıştır Hesapları** özellikleri sayfasında silmek istediğiniz Farklı Çalıştır Hesabını veya Klasik Farklı Çalıştır Hesabını seçin. Ardından, seçili hesabın **Özellikler** bölmesinde **Sil**'e tıklayın.

   ![Farklı Çalıştır hesabını silme](media/manage-runas-account/automation-account-delete-runas.png)

1. Hesap silinirken menünün **Bildirimler** öğesi altında ilerleme durumunu izleyebilirsiniz.

1. Hesap silindikten sonra **Farklı Çalıştır Hesapları** özellikler sayfasında **Azure Farklı Çalıştır Hesabı** seçeneğini belirleyerek hesabı yeniden oluşturabilirsiniz.

   ![Otomasyon Farklı Çalıştır hesabını yeniden oluşturma](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Otomatik olarak imzalanan sertifika yenileme

Farklı Çalıştır hesabınızın süresi dolmadan önce sertifikayı yenilemeniz gerekir. Farklı Çalıştır hesabının tehlikede olduğunu düşünüyorsanız, hesabı silip yeniden oluşturabilirsiniz. Bu bölümde bu işlemlerin nasıl gerçekleştirileceği ele alınmaktadır.

Farklı Çalıştır hesabı için oluşturduğunuz otomatik olarak imzalanan sertifikanın süresi, oluşturulduktan bir yıl sonra dolar. Sertifikayı süresi dolmadan önce herhangi bir zamanda yenileyebilirsiniz. Bunu yenilediğinizde geçerli geçerli sertifika, kuyruğa alınan veya etkin olarak çalışan ve farklı çalıştır hesabıyla kimlik doğrulayan tüm runbook 'ların olumsuz şekilde etkilenmemesini sağlamak için tutulur. Sertifika, sona erme tarihine kadar geçerliliğini sürdürür.

> [!NOTE]
> Otomasyon Farklı Çalıştır hesabınızı, kuruluş sertifika yetkiliniz tarafından yayınlanan bir sertifika kullanmak üzere yapılandırdıysanız ve bu seçeneği kullanırsanız, kurumsal sertifika otomatik olarak imzalanan bir sertifikayla değiştirilir.

Sertifikayı yenilemek için aşağıdakileri yapın:

1. Azure portalında Otomasyon hesabınızı açın.

1. **Hesap ayarları**altında **Farklı Çalıştır hesapları** ' nı seçin.

    ![Otomasyon hesabı özellikleri bölmesi](media/manage-runas-account/automation-account-properties-pane.png)

1. **Farklı Çalıştır Hesapları** özellikleri sayfasında sertifikasını yenilemek istediğiniz Farklı Çalıştır Hesabını veya Klasik Farklı Çalıştır Hesabını seçin.

1. Seçili hesabın **Özellikler** bölmesinde **Sertifikayı yenile**'ye tıklayın.

    ![Farklı Çalıştır hesabının sertifikasını yenileme](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Sertifika yenilenirken menünün **Bildirimler** öğesi altında ilerleme durumunu izleyebilirsiniz.

## <a name="auto-cert-renewal"></a>Otomasyon Runbook 'u ile otomatik sertifika yenilemeyi ayarlama

Sertifikaları otomatik olarak yenilemek için bir Otomasyon Runbook 'unu kullanabilirsiniz. [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) 'daki aşağıdaki betik Otomasyon hesabınızda bu işlevselliği sunar.

- @No__t_0 betiği, farklı çalıştır hesabı sertifikalarını yenilemek için haftalık bir zamanlama oluşturur.
- Betik, Otomasyon hesabınıza bir **Update-AutomationRunAsCredential** runbook 'u ekler.
  - Ayrıca, betik: [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1)Içinde, GitHub 'daki runbook kodunu görüntüleyebilirsiniz.
  - Sertifikaları gerektiğinde el ile yenilemek için, dosyadaki PowerShell kodunu da kullanabilirsiniz.

Yenileme işlemini hemen test etmek için aşağıdaki adımları kullanın:

1. **Update-AutomationRunAsCredential** runbook 'unu düzenleyin ve aşağıda gösterildiği gibi, `Exit(1)` komutunun önüne 122. satıra bir açıklama karakteri (`#`) koyun.

   ```powershell
   #Exit(1)
   ```

2. Runbook 'u yayımlayın.
3. Runbook 'u başlatın.
4. Aşağıdaki kodla başarılı yenilemeyi doğrulayın:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Testten sonra, runbook 'u düzenleyin ve **Adım 1**' de eklediğiniz açıklama karakterini kaldırın.
6. Runbook 'u **yayımlayın** .

> [!NOTE]
> Betiği yürütmek için Azure Active Directory bir **genel yönetici** veya **Şirket Yöneticisi** olmanız gerekir.

## <a name="limiting-run-as-account-permissions"></a>Farklı Çalıştır hesabı izinlerini sınırlandırma

Azure 'daki kaynaklara karşı Otomasyon hedefini denetlemek için, mevcut farklı çalıştır hesabı hizmet sorumlunuzu özel bir rol oluşturmak ve kullanmak üzere değiştirmek üzere PowerShell galerisinde [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) betiğini çalıştırabilirsiniz tanımı. Bu rolün [Key Vault](https://docs.microsoft.com/azure/key-vault/)hariç tüm kaynaklar için izinleri olacaktır.

> [!IMPORTANT]
> @No__t_0 betiğini çalıştırdıktan sonra, RunAs hesaplarının kullanımı üzerinden Keykasasına erişen runbook 'lar artık çalışmayacaktır. Azure Anahtar Kasası 'na çağrılar için hesabınızdaki runbook 'ları incelemeniz gerekir.
>
> Azure Otomasyonu runbook 'lardan Keykasasına erişimi etkinleştirmek için [, runas hesabını keykasasının izinlerine eklemeniz](#add-permissions-to-key-vault)gerekir.

RunAs hizmeti sorumlusunun daha fazla neler yapabileceğini kısıtlamak isterseniz, özel rol tanımının `NotActions` diğer kaynak türlerini ekleyebilirsiniz. Aşağıdaki örnek `Microsoft.Compute` erişimi kısıtlar. Bunu rol tanımının **NotActions** ' e eklerseniz, bu rol herhangi bir işlem kaynağına erişemez. Rol tanımları hakkında daha fazla bilgi edinmek için bkz. [Azure kaynakları için rol tanımlarını anlama](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Farklı Çalıştır hesabınız tarafından kullanılan hizmet sorumlusunun **katkıda** bulunduğu veya özel bir rol tanımında olup olmadığını belirlemek Için Otomasyon hesabınıza gidin ve **Hesap ayarları**altında Azure farklı çalıştır**hesabı** >  **Farklı Çalıştır hesapları** ' nı seçin. **Rol** altında, kullanılmakta olan rol tanımını bulabilirsiniz.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Birden çok abonelik veya Otomasyon hesabı için Otomasyon farklı çalıştır hesapları tarafından kullanılan rol tanımını öğrenmek için PowerShell Galerisi [Check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) betiğini kullanabilirsiniz.

### <a name="add-permissions-to-key-vault"></a>Key Vault izinler ekleme

Azure Automation 'ın Key Vault yönetmesine izin vermek istiyorsanız ve farklı çalıştır hesabı hizmet sorumlusu özel bir rol tanımı kullanıyorsa, bu davranışa izin vermek için ek adımlar gerçekleştirmeniz gerekir:

* Key Vault izin verin
* Erişim Ilkesini ayarlama

Anahtar Kasası 'na farklı çalıştır hesabı izinleri vermek için PowerShell Galerisi [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) betiğini kullanabilir veya ayarlar hakkında daha fazla bilgi için [uygulamalara bir Anahtar Kasası erişimi verme](../key-vault/key-vault-group-permissions-for-apps.md) sayfasını bulabilirsiniz Anahtar Kasası izinleri.

## <a name="misconfiguration"></a>Yanlış yapılandırma

İlk kurulum sırasında, Farklı Çalıştır veya Klasik Farklı Çalıştır hesabının düzgün çalışması için gerekli olan bazıları silinmiş veya düzgün oluşturulmamış olabilir. Bu öğeler şunlardır:

* Sertifika varlığı
* Bağlantı varlığı
* Farklı Çalıştır hesabının katkıda bulunan rolünden kaldırılması
* Azure AD'de hizmet sorumlusu veya uygulama

Yanlış yapılandırmanın önceki ve diğer örneklerinde, Otomasyon hesabı değişiklikleri algılar ve hesabın **Farklı Çalıştır Hesapları** özellikleri sayfasında *Tamamlanmadı* durumunu gösterir.

![Tamamlanmamış Farklı Çalıştır hesabı yapılandırma durumu](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Farklı Çalıştır hesabını seçtiğinizde hesabın **Özellikler** bölmesinde aşağıdaki hata iletisi görüntülenir:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Hesabı silip yeniden oluşturarak Farklı Çalıştır hesabıyla ilgili bu sorunları hızlı bir şekilde çözebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Hizmet sorumluları hakkında daha fazla bilgi için bkz. [uygulama nesneleri ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md).
* Sertifikalar ve Azure hizmetleri hakkında daha fazla bilgi için bkz. [azure Cloud Services sertifikalara genel bakış](../cloud-services/cloud-services-certs-create.md).
