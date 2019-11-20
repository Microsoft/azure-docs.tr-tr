---
title: PowerShell ile Azure uygulaması için kimlik oluşturma
titleSuffix: Microsoft identity platform
description: Azure PowerShell kullanarak Azure Active Directory uygulamasıyla hizmet sorumlusu oluşturma ve rol tabanlı erişim denetimi aracılığıyla bu uygulamaya kaynaklar için erişim verme işlemleri açıklanır. Bir sertifikayla uygulamanın kimliğinin nasıl doğrulandığı gösterilir.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/10/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcf52bc5a6d177c18d9ebb792d1a147d09746afd
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803827"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Nasıl yapılır: sertifika ile hizmet sorumlusu oluşturmak için Azure PowerShell kullanma

Kaynaklara erişmesi gereken bir uygulamanız veya betiğiniz olduğunda, uygulama için bir kimlik ayarlayabilir ve uygulamanın kimliğini kendi kimlik bilgileriyle doğrulayabilirsiniz. Bu kimlik, hizmet sorumlusu olarak bilinir. Bu yaklaşım şunları yapmanızı sağlar:

* Uygulama kimliğine kendi izinlerinizden farklı izinler atayabilirsiniz. Normalde, bu izinler tam olarak uygulamaya gereken izinlerle sınırlı olur.
* Katılımsız bir betik yürütürken kimlik doğrulaması için sertifika kullanabilirsiniz.

> [!IMPORTANT]
> Hizmet sorumlusu oluşturmak yerine, uygulama kimliğiniz için Azure kaynakları için Yönetilen kimlikler kullanmayı göz önünde bulundurun. Kodunuz yönetilen kimlikleri destekleyen ve Azure Active Directory (Azure AD) kimlik doğrulamasını destekleyen kaynaklara erişen bir hizmette çalışıyorsa, Yönetilen kimlikler sizin için daha iyi bir seçenektir. Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinmek için şu anda hangi hizmetleri desteklediği hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../managed-identities-azure-resources/overview.md).

Bu makalede, sertifikayla kimlik doğrulaması yapan bir hizmet sorumlusunun nasıl oluşturulduğu gösterilir. Parolası olan bir hizmet sorumlusu ayarlamak için bkz. [Azure PowerShell ile Azure hizmet sorumlusu oluşturma](/powershell/azure/create-azure-service-principal-azureps).

Bu makale için PowerShell'in [en son sürümünü](/powershell/azure/install-az-ps) kullanıyor olmalısınız.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Gerekli izinler

Bu makaleyi tamamlayabilmeniz için hem Azure AD hem de Azure aboneliğinizde yeterli izinlere sahip olmanız gerekir. Özellikle, Azure AD 'de bir uygulama oluşturabilmek ve hizmet sorumlusunu bir role atamanız gerekir.

Hesabınızın yeterli izinlere sahip olup olmadığını denetlemenin en kolay yolu portalı kullanmaktır. Bkz. [Gerekli izinleri denetleme](howto-create-service-principal-portal.md#required-permissions).

## <a name="assign-the-application-to-a-role"></a>Uygulamayı bir role atama
Aboneliğinizdeki kaynaklara erişmek için uygulamayı bir role atamanız gerekir. Hangi rolün uygulama için doğru izinleri sunduğunu belirleyin. Kullanılabilir roller hakkında daha fazla bilgi edinmek için bkz. [RBAC: yerleşik roller](/azure/role-based-access-control/built-in-roles).

Kapsamı, abonelik, kaynak grubu veya kaynak düzeyinde ayarlayabilirsiniz. İzinler, daha düşük kapsam düzeylerine devralınır. Örneğin, bir kaynak grubu için *okuyucu* rolüne bir uygulama eklemek, kaynak grubunu ve içerdiği kaynakları okuyabileceği anlamına gelir. Uygulamanın yeniden başlatma, örnekleri başlatma ve durdurma gibi eylemleri yürütmesine izin vermek için, *katkıda bulunan* rolünü seçin.

## <a name="create-service-principal-with-self-signed-certificate"></a>Otomatik olarak imzalanan bir sertifikayla hizmet sorumlusu oluşturma

Aşağıdaki örnekte basit bir senaryo ele alınmıştır. Otomatik olarak imzalanan bir sertifika ile hizmet sorumlusu oluşturmak için [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) kullanır ve [Yeni-Azurermroleatamasını](/powershell/module/az.resources/new-azroleassignment) kullanarak [okuyucu](/azure/role-based-access-control/built-in-roles#reader) rolünü hizmet sorumlusuna atar. Rol atamasının kapsamı şu anda seçili olan Azure aboneliğinizdir. Farklı bir abonelik seçmek için [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)' i kullanın.

> [!NOTE]
> New-SelfSignedCertificate cmdlet 'i ve PKI modülü şu anda PowerShell Core 'da desteklenmiyor. 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

Örnek, yeni hizmet sorumlusu tarafından Azure AD genelinde yayılır. Betiğiniz yeteri kadar uzun beklemiyorsa, "{DIR-ID} dizininde {ID} sorumlusu yok" hatasını görürsünüz. Bu hatayı çözmek için bir süre bekleyip **New-Azroleatama** komutunu yeniden çalıştırın.

**ResourceGroupName** parametresini kullanıp rol atamasının kapsamı olarak belirli bir kaynak grubunu belirtebilirsiniz. Ayrıca kapsam olarak belirli bir kaynağı belirtmek için **ResourceType** ve **ResourceName** parametrelerini kullanabilirsiniz. 

**Windows 10'unuz veya Windows Server 2016'nız yoksa**, Microsoft Betik Merkezi'nden [Otomatik olarak imzalanan sertifika oluşturucusunu](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) indirmeniz gerekir. İçindekileri ayıklayın ve ihtiyacınız olan cmdlet'i içeri aktarın.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Sertifikayı oluşturmak için betikte aşağıdaki iki satırı değiştirin.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Otomatik PowerShell betiği aracılığıyla sertifika sağlama

Hizmet sorumlusu olarak her oturum açtığınızda, AD uygulamanız için dizinin kiracı kimliğini sağlamanız gerekir. Kiracı, Azure AD 'nin bir örneğidir.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Sertifika Yetkilisinin sertifikasıyla hizmet sorumlusu oluşturma

Aşağıdaki örnekte, hizmet sorumlusu oluşturmak için bir Sertifika Yetkilisinin verdiği sertifika kullanılır. Atamanın kapsamı belirtilen Azure aboneliğidir. Hizmet sorumlusunu [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolüne ekler. Rol ataması sırasında hata oluştursa, atamayı yeniden dener.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Otomatik PowerShell betiği aracılığıyla sertifika sağlama
Hizmet sorumlusu olarak her oturum açtığınızda, AD uygulamanız için dizinin kiracı kimliğini sağlamanız gerekir. Kiracı, Azure AD 'nin bir örneğidir.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Uygulama kimliği ve kiracı kimliği büyük/küçük harfe duyarlı değildir, dolayısıyla bunları doğrudan betiğinize ekleyebilirsiniz. Kiracı kimliğini almanız gerekiyorsa şunu kullanın:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Uygulama kimliğini almanız gerekiyorsa şunu kullanın:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Kimlik bilgilerini değiştirme

Bir güvenlik güvenliği tehlikeye veya kimlik bilgisi süre sonu nedeniyle bir AD uygulamasının kimlik bilgilerini değiştirmek için, [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) ve [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) cmdlet 'lerini kullanın.

Uygulamanın tüm kimlik bilgilerini kaldırmak için şunu kullanın:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Sertifika değeri eklemek için, bu makalede gösterildiği gibi otomatik olarak imzalanan bir sertifika oluşturun. Ardından şunu kullanın:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Hata ayıklama

Hizmet sorumlusu oluştururken şu hataları alabilirsiniz:

* **"Authentication_Unauthorized"** veya **"Bağlamda hiç abonelik bulunamadı."** -Hesabınız, Azure AD 'de bir uygulamayı kaydetmek için [gerekli izinlere](#required-permissions) sahip olmadığında bu hatayı görürsünüz. Genellikle, yalnızca Azure Active Directory yönetici kullanıcılar uygulamaları kaydedebilmeniz ve hesabınız yönetici değilse bu hatayı görürsünüz. Yöneticinizden sizi bir yönetici rolüne atamasını ya da kullanıcıların uygulama kaydetmesini etkinleştirmesini isteyin.

* Hesabınız **"'/Subscriptions/{Guid} ' kapsamı üzerinde ' Microsoft. Authorization/Roleatamalar/Write ' işlemini gerçekleştirme yetkisi yok."** -hesabınız bir rolü bir rol atamak için yeterli izinlere sahip olmadığında bu hatayı görürsünüz. IDENTITY. Abonelik yöneticinizden sizi Kullanıcı Erişimi Yöneticisi rolüne atamasını isteyin.

## <a name="next-steps"></a>Sonraki adımlar

* Parolası olan bir hizmet sorumlusu ayarlamak için bkz. [Azure PowerShell ile Azure hizmet sorumlusu oluşturma](/powershell/azure/create-azure-service-principal-azureps).
* Uygulamaların ve hizmet sorumlularının daha ayrıntılı açıklaması için bkz. [Uygulama Nesneleri ve Hizmet Sorumlusu Nesneleri](app-objects-and-service-principals.md).
* Azure AD kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları](authentication-scenarios.md).
