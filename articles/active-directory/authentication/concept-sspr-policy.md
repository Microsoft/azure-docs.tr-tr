---
title: Azure AD self servis parola sıfırlama ilkeleri-Azure Active Directory
description: Azure AD self servis parola sıfırlama İlkesi seçeneklerini yapılandırma
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f5371334fb383b15514c879ceb262fa78d7fca
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084949"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory 'deki parola ilkeleri ve kısıtlamaları

Bu makalede, Azure Active Directory (Azure AD) kiracınızdaki Kullanıcı hesaplarıyla ilişkili parola ilkeleri ve karmaşıklık gereksinimleri açıklanmaktadır.

## <a name="administrator-reset-policy-differences"></a>Yönetici ilke farklarını sıfırlama

**Microsoft, herhangi bir Azure yönetici rolü için güçlü bir varsayılan *iki Gate* parola sıfırlama ilkesi uygular** ve bu ilke kullanıcılarınız için tanımladığınız bir kaynaktan farklı olabilir ve değiştirilemez. Parola sıfırlama işlevini her zaman, hiçbir Azure yönetici rolü atanmadan Kullanıcı olarak sınamalısınız.

İki ağ geçidi ilkesiyle, **Yöneticilerin güvenlik sorularını kullanma yeteneği yoktur**.

İki kapı ilkesi, **e-posta adresi**, **kimlik doğrulayıcı uygulaması**veya **telefon numarası**gibi iki kimlik doğrulama verisi gerektirir. İki kapı ilkesi aşağıdaki koşullarda geçerlidir:

* Aşağıdaki Azure Yönetici rollerinin tümü etkilenir:
  * Yardım Masası Yöneticisi
  * Hizmet desteği Yöneticisi
  * Faturalama yöneticisi
  * Partner Tier1 Desteği
  * Partner Tier2 Desteği
  * Exchange yöneticisi
  * Skype Kurumsal yöneticisi
  * Kullanıcı yöneticisi
  * Dizin yazıcıları
  * Genel yönetici veya Şirket Yöneticisi
  * SharePoint yöneticisi
  * Uyumluluk yöneticisi
  * Uygulama yöneticisi
  * Güvenlik yöneticisi
  * Ayrıcalıklı rol yöneticisi
  * Intune yöneticisi
  * Uygulama Proxy Hizmeti Yöneticisi
  * Dynamics 365 yöneticisi
  * Power BI hizmeti Yöneticisi
  * Kimlik doğrulaması yöneticisi
  * Ayrıcalıklı kimlik doğrulama Yöneticisi

* Deneme aboneliğinde 30 gün geçtikten sonra veya
* Contoso.com gibi bir gösterim etki alanı vardır. veya
* Azure AD Connect, şirket içi dizininizdeki kimlikler eşitleniyor

### <a name="exceptions"></a>Özel durumlar

Tek bir geçit ilkesi, bir e-posta adresi *veya* telefon numarası gibi bir dizi kimlik doğrulama verisi gerektirir. Tek bir geçit ilkesi aşağıdaki koşullarda geçerlidir:

* Deneme aboneliğinin ilk 30 günü içinde olur; veya
* Bir gösterim etki alanı yok (*. onmicrosoft.com); '
* Azure AD Connect kimlikleri eşitlemiyor

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Tüm Kullanıcı hesaplarına uygulanan UserPrincipalName ilkeleri

Azure AD 'de oturum açması gereken her kullanıcı hesabının, hesabıyla ilişkilendirilmiş benzersiz bir Kullanıcı asıl adı (UPN) özniteliği değeri olmalıdır. Aşağıdaki tabloda, hem bulut hem de yalnızca bulut Kullanıcı hesapları ile eşitlenen şirket içi Active Directory Kullanıcı hesapları için uygulanan ilkeler özetlenmektedir:

| Özellik | UserPrincipalName gereksinimleri |
| --- | --- |
| İzin verilen karakterler |<ul> <li>A – Z</li> <li>a-z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Karakterlere izin verilmiyor |<ul> <li>Herhangi bir\@ "\" Kullanıcı adını etki alanından ayıran karakter.</li> <li>Nokta karakteri içeremez "." hemen önceki "\@sembol \"</li></ul> |
| Uzunluk kısıtlamaları |<ul> <li>Toplam uzunluk 113 karakterden uzun olmamalıdır</li><li>\@ "\" Simgesinden önce en fazla 64 karakter olabilir</li><li>\@ "\" Simgesinden sonra en fazla 48 karakter olabilir</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Yalnızca bulut Kullanıcı hesapları için uygulanan parola ilkeleri

Aşağıdaki tabloda, Azure AD 'de oluşturulup yönetilen Kullanıcı hesaplarına uygulanan parola ilkesi ayarları açıklanmaktadır:

| Özellik | Gereksinimler |
| --- | --- |
| İzin verilen karakterler |<ul><li>A – Z</li><li>a-z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \`~ " ( ) ;</li> <li>boş alan</li></ul> |
| Karakterlere izin verilmiyor | Unicode karakterler. |
| Parola kısıtlamaları |<ul><li>En az 8 karakter ve en fazla 256 karakter.</li><li>Aşağıdakilerden üç tanesi olmalıdır:<ul><li>Küçük harfli karakterler.</li><li>Büyük harfli karakterler.</li><li>Sayılar (0-9).</li><li>Semboller (önceki parola kısıtlamalarına bakın).</li></ul></li></ul> |
| Parola süre sonu süresi (en fazla parola yaşı) |<ul><li>Varsayılan değer: **90** gün.</li><li>Değer, Windows PowerShell için Azure Active Directory modülünden `Set-MsolPasswordPolicy` cmdlet kullanılarak yapılandırılabilir.</li></ul> |
| Parola süre sonu bildirimi (kullanıcıların parola süre sonu hakkında bilgilendirmesi durumunda) |<ul><li>Varsayılan değer: **14** gün (parolanın süresi dolmadan önce).</li><li>Değer, `Set-MsolPasswordPolicy` cmdlet 'i kullanılarak yapılandırılabilir.</li></ul> |
| Parola süre sonu (parolaların süresi doluyor) |<ul><li>Varsayılan değer: **false** gün (parola süre sonu etkin olduğunu gösterir).</li><li>Değer, `Set-MsolUser` cmdlet 'i kullanılarak bireysel kullanıcı hesapları için yapılandırılabilir.</li></ul> |
| Parola değiştirme geçmişi | Kullanıcı bir parolayı değiştirdiğinde son parola yeniden kullanılamaz. |
| Parola sıfırlama geçmişi | Son parola, Kullanıcı unutulan bir parolayı sıfırladığında yeniden kullanılabilir. |
| Hesap kilitleme | 10 başarısız oturum açma denemesi yanlış parola ile çalıştıktan sonra, Kullanıcı bir dakika boyunca kilitlenir. Daha fazla hatalı oturum açma denemesi, Kullanıcı süresini artırmak için kullanıcıyı kilitler. [Akıllı kilitleme](howto-password-smart-lockout.md) , aynı parolanın kilitleme sayacını arttırmaktan kaçınmak için son üç hatalı parola karmalarını izler. Birisi aynı hatalı parolayı birden çok kez girerse, bu davranış hesabın kilitlenmesine neden olmaz. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Azure AD 'de parola süre sonu ilkelerini ayarlama

Bir Microsoft bulut hizmeti için genel yönetici veya Kullanıcı Yöneticisi, kullanıcı parolalarının kullanım süresini sona ermeyecek şekilde ayarlamak için Windows PowerShell için Microsoft Azure AD Modülü kullanabilir. Windows PowerShell cmdlet 'lerini, hiçbir zaman sona erme yapılandırmasını kaldırmak veya hangi kullanıcı parolalarının süresi dolmayacak şekilde ayarlandığını görmek için de kullanabilirsiniz. 

Bu kılavuz, Intune ve Office 365 gibi diğer sağlayıcılar için geçerlidir; bu da kimlik ve Dizin Hizmetleri için Azure AD 'ye bağımlıdır. Parola süre sonu, ilkenin değiştirilebilecek tek bölümüdür.

> [!NOTE]
> Yalnızca dizin eşitleme üzerinden eşitlenmemiş Kullanıcı hesapları için parolalar sona ermeyecek şekilde yapılandırılabilir. Dizin eşitleme hakkında daha fazla bilgi için bkz. [Azure AD Ile ad bağlama](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>PowerShell kullanarak parola ilkelerini ayarlama veya denetleme

Başlamak için [Azure AD PowerShell modülünü indirip yüklemeniz](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)gerekir. Yüklendikten sonra, her alanı yapılandırmak için aşağıdaki adımları kullanabilirsiniz.

### <a name="check-the-expiration-policy-for-a-password"></a>Parola için süre sonu ilkesini denetleme

1. Kullanıcı yöneticinize veya Şirket Yöneticisi kimlik bilgilerinizi kullanarak Windows PowerShell 'e bağlanın.
1. Aşağıdaki komutlardan birini yürütün:

   * Tek bir kullanıcının parolasının süresiz olarak ayarlanmış olup olmadığını görmek için, UPN 'yi (örneğin, *aprilr\@contoso.onmicrosoft.com*) veya denetlemek istediğiniz kullanıcının kullanıcı kimliğini kullanarak aşağıdaki cmdlet 'i çalıştırın:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Tüm kullanıcılar için **parola süresi dolmasın** ayarını görmek için aşağıdaki cmdlet 'i çalıştırın:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Bir parolayı sona ermek üzere ayarla

1. Kullanıcı yöneticinize veya Şirket Yöneticisi kimlik bilgilerinizi kullanarak Windows PowerShell 'e bağlanın.
1. Aşağıdaki komutlardan birini yürütün:

   * Parolanın süresi dolmadan bir kullanıcının parolasını ayarlamak için, UPN veya kullanıcının kullanıcı KIMLIĞINI kullanarak aşağıdaki cmdlet 'i çalıştırın:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Kuruluştaki tüm kullanıcıların parolalarını süreleri dolacak şekilde ayarlamak için aşağıdaki cmdlet 'i kullanın:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Süresiz olarak bir parola ayarlayın

1. Kullanıcı yöneticinize veya Şirket Yöneticisi kimlik bilgilerinizi kullanarak Windows PowerShell 'e bağlanın.
1. Aşağıdaki komutlardan birini yürütün:

   * Bir kullanıcının parolasını hiçbir zaman dolmayacak şekilde ayarlamak için, UPN 'yi veya kullanıcının kullanıcı KIMLIĞINI kullanarak aşağıdaki cmdlet 'i çalıştırın:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Bir kuruluştaki tüm kullanıcıların parolalarını hiçbir zaman sona ermeyecek şekilde ayarlamak için aşağıdaki cmdlet 'i çalıştırın:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Parolalar, `-PasswordPolicies DisablePasswordExpiration` `pwdLastSet` özniteliğe göre hala yaş olarak ayarlanmıştır. Kullanıcı parolalarını hiçbir zaman sona ermez ve 90 + gün sonra, parolaların süre sonu olarak ayarlayabilirsiniz. `pwdLastSet` Özniteliğini temel alarak, süre sonunu olarak `-PasswordPolicies None`değiştirirseniz, 90 günden `pwdLastSet` eski olan tüm parolalar kullanıcının bir sonraki oturum açışlarında değiştirmesini gerektirir. Bu değişiklik, çok sayıda kullanıcıyı etkileyebilir.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde, Azure AD ile parola sıfırlama hakkında ek bilgiler sağlanmaktadır:

* [SSPR’yi başarılı bir şekilde nasıl piyasaya çıkarabilirim?](howto-sspr-deployment.md)
* [Parolanızı sıfırlama veya değiştirme](../user-help/active-directory-passwords-update-your-own-password.md).
* [Self servis parola sıfırlama için kaydolma](../user-help/active-directory-passwords-reset-register.md).
* [Lisansla ilgili bir sorunuz mu var?](concept-sspr-licensing.md)
* [SSPR hangi verileri kullanır ve kullanıcılarınız için hangi verileri doldurmanız gerekir?](howto-sspr-authenticationdata.md)
* [Kullanıcılar hangi kimlik doğrulama yöntemlerini kullanabilir?](concept-sspr-howitworks.md#authentication-methods)
* [Parola geri yazma nedir ve neden önemlidir?](howto-sspr-writeback.md)
* [SSPR’de etkinliği nasıl bildirebilirim?](howto-sspr-reporting.md)
* [SSPR’deki tüm seçenekler nelerdir ve ne anlama gelir?](concept-sspr-howitworks.md)
* [Bir arıza olduğunu düşünüyorum. SSPR’de nasıl sorun giderebilirim?](active-directory-passwords-troubleshoot.md)
* [Başka bir yerde ele alınmayan bir sorum var](active-directory-passwords-faq.md)
