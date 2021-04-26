---
title: PowerShell ile özel ilkeleri yönetme
titleSuffix: Azure AD B2C
description: Azure AD B2C özel ilkelerinizin programlı yönetimi için Azure Active Directory (Azure AD) PowerShell cmdlet 'ini kullanın. PowerShell ile özel ilkeler oluşturun, okuyun, güncelleştirin ve silin.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbe465c41363e8c0779440affeae78f999b4632d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257441"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Azure PowerShell ile Azure AD B2C özel ilkeleri yönetme

Azure PowerShell, Azure AD B2C kiracınızda komut satırı ve betik tabanlı özel ilke yönetimi için çeşitli cmdlet 'ler sağlar. Azure AD PowerShell modülünü kullanarak şunları yapmayı öğrenin:

* Azure AD B2C kiracısındaki özel ilkeleri listeleme
* Bir kiracıdan bir ilke indirin
* Mevcut bir ilkeyi içeriğini üzerine yazarak Güncelleştir
* Azure AD B2C kiracınıza yeni bir ilke yükleme
* Bir kiracıdan özel bir ilke silme

## <a name="prerequisites"></a>Önkoşullar

* [B2C ıEF Ilke Yöneticisi](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) rolüyle dizindeki bir kullanıcı için [Azure AD B2C kiracı](tutorial-create-tenant.md)ve kimlik bilgileri
* Kiracınıza yüklenen [özel ilkeler](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
* [Grafik **Önizleme modülü** IÇIN Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>PowerShell oturumunu B2C kiracısına bağlama

Azure AD B2C kiracınızdaki özel ilkelerle çalışmak için, önce [Connect-AzureAD][Connect-AzureAD] komutunu kullanarak PowerShell oturumunuzu kiracıya bağlamanız gerekir.

`{b2c-tenant-name}`Azure AD B2C kiracınızın adıyla değiştirerek aşağıdaki komutu yürütün. Dizinde [B2C ıEF Ilke Yöneticisi](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) rolüne atanan bir hesapla oturum açın.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Başarılı bir oturum açma gösteren örnek komut çıktısı:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Kiracıdaki tüm özel ilkeleri listeleme

Özel ilkeleri keşfetmek, bir Azure AD B2C yöneticisinin işlemlerine iş mantığını gözden geçirmesine, yönetmesine ve eklemesini sağlar. Bir Azure AD B2C kiracısındaki özel ilkelerin kimliklerinin bir listesini döndürmek için [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] komutunu kullanın.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Örnek komut çıktısı:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Bir ilke indirin

İlke kimliklerinin listesini inceledikten sonra, içeriğini indirmek için [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] ile belirli bir ilkeyi hedefleyebilirsiniz.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

Bu örnekte, *B2C_1A_SIGNUP_SIGNIN* kimlikli ilke indirilir:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

İlke içeriğini yerel olarak düzenlemek için komut çıkışını bağımsız değişkenle bir dosyaya boru yapın `-OutputFilePath` ve ardından dosyayı en sevdiğiniz düzenleyicide açın.

Bir dosyaya çıkış gönderen örnek komut:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Mevcut bir ilkeyi güncelleştirme

Oluşturduğunuz veya indirdiğiniz bir ilke dosyasını düzenledikten sonra, [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] komutunu kullanarak güncelleştirilmiş ilkeyi Azure AD B2C olarak yayımlayabilirsiniz.

`Set-AzureADMSTrustFrameworkPolicy`Komutu Azure AD B2C kiracınızda zaten bulunan bir ILKENIN kimliğiyle verirseniz, bu ilkenin içeriğinin üzerine yazılır.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Örnek komut:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Daha fazla örnek için bkz. [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] komut başvurusu.

## <a name="upload-a-new-policy"></a>Yeni bir ilkeyi karşıya yükle

Üretimde çalışan bir özel ilkede değişiklik yaptığınızda, geri dönüş veya A/B testi senaryolarında ilkenin birden çok sürümünü yayımlamak isteyebilirsiniz. Ya da, var olan bir ilkenin kopyasını oluşturmak, birkaç küçük değişiklikle değiştirmek ve sonra farklı bir uygulama tarafından kullanılmak üzere yeni bir ilke olarak yüklemek isteyebilirsiniz.

Yeni bir ilkeyi karşıya yüklemek için [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] komutunu kullanın:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Örnek komut:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Özel bir ilkeyi silme

Temiz bir işlem yaşam döngüsünü sürdürmek için kullanılmayan özel ilkeleri düzenli olarak kaldırmanızı öneririz. Örneğin, yeni bir ilke kümesine geçiş gerçekleştirdikten ve yeni ilkelerin işlevselliğini doğruladıktan sonra eski ilke sürümlerini kaldırmak isteyebilirsiniz. Ayrıca, bir dizi özel ilke yayımlamaya ve bir hata almaya çalışırsanız, başarısız olan yayının bir parçası olarak oluşturulan ilkelerin kaldırılması mantıklı olabilir.

Kiracınızdan bir ilkeyi silmek için [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] komutunu kullanın.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Örnek komut:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Karşıya ilke yükleme sorunlarını giderme

Yeni bir özel ilke yayımlamaya veya mevcut bir ilkeyi güncelleştirmeye çalıştığınızda, ilke dosyası devralma zincirindeki hatalı XML biçimlendirmesi ve hataları doğrulama hatalarına neden olabilir.

Örneğin, bir ilkeyi hatalı biçimlendirilmiş XML içeren içerikle güncelleştirme girişiminde bulunuldu (çıktı, breçekimi için kesilir):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Özel ilkelerin sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure AD B2C özel ilkeler ve kimlik deneyimi çerçevesi](./troubleshoot-custom-policies.md)sorun giderme.

## <a name="next-steps"></a>Sonraki adımlar

Bir sürekli tümleştirme/sürekli teslim (CI/CD) işlem hattının parçası olarak özel ilkeleri dağıtmak üzere PowerShell kullanma hakkında bilgi için bkz. [Azure DevOps işlem hattından özel Ilkeler dağıtma](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
