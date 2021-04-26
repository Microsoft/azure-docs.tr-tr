---
title: Azure AD Multi-Factor Authentication için kimlik doğrulama yöntemlerini yönetme-Azure Active Directory
description: Azure AD Multi-Factor Authentication için Azure Active Directory Kullanıcı ayarlarını nasıl yapılandırabileceğinizi öğrenin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0be7e50c5cce511fafd7d8b407626bd57659bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183141"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication için Kullanıcı kimlik doğrulama yöntemlerini yönetme

Azure AD 'deki kullanıcıların iki farklı iletişim bilgisi kümesi vardır:  

- Kullanıcı profilinde yönetilen ve kuruluşunuzun üyelerine görünen genel profil iletişim bilgileri. Şirket içi Active Directory eşitlenen kullanıcılar için, bu bilgiler Şirket içi Windows Server Active Directory Domain Services yönetilir.
- Her zaman özel olarak tutulan ve Multi-Factor Authentication (MFA) dahil olmak üzere yalnızca kimlik doğrulama için kullanılan kimlik doğrulama yöntemleri. Yöneticiler, bu yöntemleri kullanıcının kimlik doğrulama yöntemi dikey penceresinde yönetebilir ve kullanıcılar kendi yöntemlerini MyAccount güvenlik bilgileri sayfasında yönetebilir.

Kullanıcılarınız için Azure AD Multi-Factor Authentication yöntemlerini yönetirken, kimlik doğrulama yöneticileri şunları yapabilir: 

1. MFA için kullanılan telefon numaraları dahil olmak üzere belirli bir kullanıcı için kimlik doğrulama yöntemleri ekleyin.
1. Kullanıcının parolasını sıfırlayın.
1. MFA için bir kullanıcının yeniden kaydolmesini gerektirir.
1. Mevcut MFA oturumlarını iptal edin.
1. Kullanıcının mevcut uygulama parolalarını silme  

## <a name="add-authentication-methods-for-a-user"></a>Bir kullanıcı için kimlik doğrulama yöntemleri ekleme 

Azure portal veya Microsoft Graph aracılığıyla bir kullanıcı için kimlik doğrulama yöntemleri ekleyebilirsiniz.  

> [!NOTE]
> Güvenlik nedenleriyle, genel kullanıcı iletişim bilgileri alanları MFA gerçekleştirmek için kullanılmamalıdır. Bunun yerine, kullanıcılar MFA için kullanılacak kimlik doğrulama yöntemi numaralarını doldurmalıdır.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Azure portal kimlik doğrulama yöntemleri ekleme":::

Azure portal aracılığıyla bir kullanıcıya yönelik kimlik doğrulama yöntemleri eklemek için:  

1. **Azure portal** oturum açın. 
1. **Azure Active Directory**  >  **kullanıcılara**  >  **tüm kullanıcılar**' a gidin. 
1. Kimlik doğrulama yöntemi eklemek istediğiniz kullanıcıyı seçin ve **kimlik doğrulama yöntemlerini** seçin.  
1. Pencerenin üst kısmında **+ kimlik doğrulama yöntemi ekle**' yi seçin.
   1. Bir Yöntem (telefon numarası veya e-posta) seçin. E-posta, kendi kendine parola sıfırlama için kullanılabilir ancak kimlik doğrulaması yok. Telefon numarası eklerken bir telefon türü seçin ve telefon numarasını geçerli biçime (ör. + 1 4255551234) girin.
   1. **Add (Ekle)** seçeneğini belirleyin.

> [!NOTE]
> Önizleme deneyimi, yöneticilerin kullanıcılar için kullanılabilir kimlik doğrulama yöntemleri eklemesine izin verir, ancak özgün deneyim yalnızca telefon ve alternatif telefon yöntemlerinin güncelleştirilmesini sağlar.

### <a name="manage-methods-using-powershell"></a>PowerShell kullanarak yöntemleri yönetme:  

Aşağıdaki komutları kullanarak Microsoft. Graph. Identity. Signins PowerShell modülünü yükler. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Belirli bir kullanıcı için telefon tabanlı kimlik doğrulama yöntemlerini listeleyin.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Belirli bir kullanıcı için bir mobil telefon kimlik doğrulama yöntemi oluşturun.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

Bir kullanıcı için belirli bir telefonu yöntemini kaldırma

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Kimlik doğrulama yöntemleri de Microsoft Graph API 'Leri kullanılarak yönetilebilir, belge [Azure AD kimlik doğrulama YÖNTEMLERI API genel bakış](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true) bölümünde daha fazla bilgi bulunabilir

## <a name="manage-user-authentication-options"></a>Kullanıcı kimlik doğrulama seçeneklerini yönetme

*Kimlik doğrulama Yöneticisi* rolünü atadıysanız, kullanıcıların kendi parolalarını SıFıRLAMALARıNı, MFA için yeniden kaydolmayı veya mevcut MFA oturumlarını kullanıcı nesnesinden iptal etmeyi zorunlu kılabilirsiniz. Kullanıcı ayarlarını yönetmek için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **Azure Active Directory** > **Kullanıcılar** > **Tüm kullanıcılar**'ı seçin.
1. Üzerinde bir eylem gerçekleştirmek istediğiniz kullanıcıyı seçin ve **kimlik doğrulama yöntemleri**' ni seçin. Pencerenin üst kısmında, Kullanıcı için aşağıdaki seçeneklerden birini belirleyin:
   - **Parola sıfırlama** Kullanıcı parolasını sıfırlar ve sonraki oturum açma sırasında değiştirilmesi gereken geçici bir parola atar.
   - **MFA 'Yı yeniden kaydettirme** , Kullanıcı bir sonraki sefer oturum açtığında yenı bir MFA kimlik doğrulama yöntemi ayarlaması istenmesini sağlar.
   
      > [!NOTE]
      > Yönetici MFA için yeniden kayıt gerektirdiğinde kullanıcının şu anda kayıtlı kimlik doğrulama yöntemleri silinmez. Bir Kullanıcı MFA için yeniden kaydolduktan sonra, güvenlik bilgilerini gözden geçirmelerini ve artık kullanılamayan önceden kaydedilmiş kimlik doğrulama yöntemlerini silmenizi öneririz.
   
   - **MFA oturumlarını Iptal etme** , kullanıcının HATıRLANAN MFA oturumlarını temizler ve cihazdaki ilke için bir sonraki ihtiyaç DUYULıŞıNDA MFA gerçekleştirmesini gerektirir.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Azure portal kimlik doğrulama yöntemlerini yönetin":::

## <a name="delete-users-existing-app-passwords"></a>Kullanıcıların mevcut uygulama parolalarını silme

Yönetici, uygulama parolaları olan kullanıcılar için bu parolaları silmeyi de seçebilir ve bu uygulamalarda eski kimlik doğrulamanın başarısız olmasına neden olabilir. Bu eylemler, bir kullanıcıya yardım sağlamanız veya kimlik doğrulama yöntemlerini sıfırlamanız gerektiğinde gerekli olabilir. Bu uygulama parolalarıyla ilişkilendirilen tarayıcı olmayan uygulamalar, yeni bir uygulama parolası oluşturuluncaya kadar çalışmayı durdurur. 

Bir kullanıcının uygulama parolalarını silmek için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **Azure Active Directory**  >  **Kullanıcılar**  >  **tüm kullanıcılar**' ı seçin.
1. **Multi-Factor Authentication**'ı seçin. Bu menü seçeneğini görmek için sağa kaydırmanız gerekebilir. Tam Azure portal pencere ve menü konumunu görmek için aşağıdan örnek ekran görüntüsünü seçin: [ ![ Azure AD 'de kullanıcılar penceresinden Multi-Factor Authentication seçin.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Yönetmek istediğiniz kullanıcı veya kullanıcıların yanındaki kutuyu işaretleyin. Sağ tarafta hızlı adım seçeneklerinin bir listesi görüntülenir.
1. **Kullanıcı ayarlarını yönet**' i seçin, sonra aşağıdaki örnekte gösterildiği gibi **Seçilen kullanıcılar tarafından oluşturulan mevcut tüm uygulama parolalarını Sil** kutusunu işaretleyin. ![ var olan tüm uygulama parolalarını Sil](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. **Kaydet**' i ve ardından **Kapat**' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bireysel kullanıcı ayarlarını nasıl yapılandıracağınız açıklanır. Genel Azure AD Multi-Factor Authentication hizmet ayarlarını yapılandırmak için bkz. [Azure ad Multi-Factor Authentication ayarlarını yapılandırma](howto-mfa-mfasettings.md).

Kullanıcılarınızın yardıma ihtiyacı varsa bkz. [Azure AD Multi-Factor Authentication Için Kullanıcı Kılavuzu](../user-help/multi-factor-authentication-end-user-first-time.md).
