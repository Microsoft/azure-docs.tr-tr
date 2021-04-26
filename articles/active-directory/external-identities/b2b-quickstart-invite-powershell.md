---
title: 'Hızlı başlangıç: PowerShell ile Konuk Kullanıcı ekleme-Azure AD'
description: Bu hızlı başlangıçta, şirket dışından bir Azure AD B2B işbirliği kullanıcısına davet göndermek için PowerShell’in nasıl kullanılacağını öğreneceksiniz.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/28/2018
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-api
ms.collection: M365-identity-device-management
ms.openlocfilehash: 765d8ea99167512e553cef9cc6f7fed583eff469
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529818"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Hızlı Başlangıç: PowerShell ile konuk kullanıcı ekleme

Azure Active Directory B2B işbirliği ile uygulama ve hizmetlerinize şirket dışından ortaklar davet etmenin birçok yolu vardır. Önceki hızlı başlangıçta, doğrudan Azure Active Directory yönetim portalda nasıl konuk kullanıcı ekleneceğini gördünüz. Birer birer veya toplu olarak konuk kullanıcılar eklemek için de PowerShell’i kullanabilirsiniz. Bu hızlı başlangıçta, Azure kiracınıza bir konuk kullanıcı eklemek için New-AzureADMSInvitation komutunu kullanacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="prerequisites"></a>Önkoşullar

### <a name="install-the-latest-azureadpreview-module"></a>En son AzureADPreview modülünü yükleme
Graph modülü için Azure AD PowerShell’in en son sürümünü (AzureADPreview) yüklediğinizden emin olun. 

İlk olarak, hangi modülleri yüklediğinizi denetleyin. Windows PowerShell’i yükseltilmiş yönetici olarak açın (Yönetici olarak çalıştırın) ve aşağıdaki komutu çalıştırın:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

AzureADPreview modülü, sonraki bir sürüm olduğunu belirten bir ileti olmadan görüntülenirse hazırsınız demektir. Aksi takdirde, çıktıya bağlı olarak aşağıdakilerden birini yapın:

- Bir sonuç döndürülmezse, AzureADPreview modülünü yüklemek için aşağıdaki komutu çalıştırın:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Yalnızca sonuçlarda AzureAD modülü gösteriliyorsa, AzureADPreview modülünü yüklemek için aşağıdaki komutları çalıştırın: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Yalnızca sonuçlarda AzureADPreview modülü gösteriliyorsa, ancak sonraki bir sürümün olduğunu belirten bir ileti alırsanız modülü güncelleştirmek için aşağıdaki komutları çalıştırın: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Modülü güvenilir olmayan bir depodan yüklediğinizi belirten bir istem alabilirsiniz. Önceden PSGallery deposunu güvenilir depo olarak ayarlamadıysanız bu oluşur. Modülü yüklemek için **Y** tuşuna basın.

### <a name="get-a-test-email-account"></a>Test e-posta hesabı alma

Daveti gönderebileceğiniz bir test e-posta hesabı olması gerekir. Hesap, kuruluşunuzun dışından olmalıdır. Gmail.com veya outlook.com adresi gibi sosyal hesapları içeren herhangi bir hesap türü kullanabilirsiniz.

## <a name="sign-in-to-your-tenant"></a>Kiracınızda oturum açma

Kiracı etki alanına bağlanmak için aşağıdaki komutu çalıştırın:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Örneğin, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

İstendiğinde kimlik bilgilerinizi girin.

## <a name="send-an-invitation"></a>Davet gönderme

1. Test e-posta hesabınıza davetiye göndermek için aşağıdaki PowerShell komutunu çalıştırın ( **"Sanda"** ve **Sanda \@ fabrikam.com** öğesini test e-posta hesabınızın adı ve e-posta adresiniz ile değiştirin): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. Komut, belirtilen e-posta adresine bir davet gönderir. Çıktıyı denetleyin, çıktı aşağıdakine benzer görünmelidir:

   ![Kullanıcı kabulünün beklemede olduğunu gösteren PowerShell çıktısı](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Kullanıcının dizinde var olduğunu doğrulama

1. Davet edilen kullanıcının Azure AD’ye eklendiğini doğrulamak için aşağıdaki komutu çalıştırın:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Davet ettiğiniz kullanıcının, bir Kullanıcı asıl adı (UPN) ile *emadresi*#EXT # etki biçiminde listelendiğinden emin olmak için çıktıyı denetleyin \@ . Örneğin, *sanda_fabrikam. com # ext # \@ contoso.onmicrosoft.com*, burada contoso.onmicrosoft.com, davetleri gönderdiğiniz kuruluştur.

   ![Eklenen konuk kullanıcıyı gösteren PowerShell çıktısı](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse dizindeki test kullanıcı hesaplarını silebilirsiniz. Kullanıcı hesabını silmek için aşağıdaki komutu çalıştırın:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Örnek: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, PowerShell kullanarak dizininize tek bir konuk kullanıcıyı davet edip eklediniz. Şimdi, PowerShell kullanarak konuk kullanıcıların toplu olarak nasıl davet edileceğini öğreneceksiniz.

> [!div class="nextstepaction"]
> [Öğretici: Azure AD B2B işbirliği kullanıcılarını toplu davet etme](tutorial-bulk-invite.md)
