---
title: Self servis parola sıfırlama ilkeleri-Azure Active Directory
description: Farklı Azure Active Directory self servis parola sıfırlama ilkesi seçenekleri hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 80be5ca22f3dfb673f09327108e66fccc9de6ddd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98918052"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Azure Active Directory 'deki parola ilkeleri ve hesap kısıtlamaları

Azure Active Directory (Azure AD) içinde parola karmaşıklığı, uzunluğu veya yaşı gibi ayarları tanımlayan bir parola ilkesi vardır. Ayrıca, Kullanıcı adları için kabul edilebilir karakterleri ve uzunluğu tanımlayan bir ilke de vardır.

Azure AD 'de bir parolayı değiştirmek veya sıfırlamak için self servis parola sıfırlama (SSPR) kullanıldığında, parola ilkesi denetlenir. Parola, ilke gereksinimlerini karşılamıyorsa, kullanıcıdan yeniden denemek istenir. Azure yöneticileri, SSPR 'yi kullanarak normal kullanıcı hesaplarına farklı bazı kısıtlamalar sağlar.

Bu makalede, Azure AD kiracınızdaki Kullanıcı hesaplarıyla ilişkili parola ilkesi ayarları ve karmaşıklık gereksinimleri ve parola süre sonu ayarlarını denetlemek veya ayarlamak için PowerShell 'i nasıl kullanabileceğiniz açıklanır.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Kullanıcı adı ilkeleri

Azure AD 'de oturum açan her hesabın, hesabıyla ilişkilendirilmiş benzersiz bir Kullanıcı asıl adı (UPN) özniteliği değeri olmalıdır. Şirket içi Active Directory Domain Services (AD DS) ortamı Azure AD Connect kullanılarak Azure AD ile eşitlenmiş karma ortamlarda, varsayılan olarak Azure AD UPN, şirket içi UPN olarak ayarlanır.

Aşağıdaki tabloda, Azure AD ile eşitlenen şirket içi AD DS hesapları için ve doğrudan Azure AD 'de oluşturulan yalnızca bulut Kullanıcı hesapları için uygulanan Kullanıcı adı ilkeleri özetlenmektedir:

| Özellik | UserPrincipalName gereksinimleri |
| --- | --- |
| İzin verilen karakterler |<ul> <li>A – Z</li> <li>a-z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Karakterlere izin verilmiyor |<ul> <li>Herhangi bir " \@ \" Kullanıcı adını etki alanından ayıran karakter.</li> <li>Nokta karakteri içeremez "." hemen önceki " \@ \" sembol</li></ul> |
| Uzunluk kısıtlamaları |<ul> <li>Toplam uzunluk 113 karakterden uzun olmamalıdır</li><li>"Simgesinden önce en fazla 64 karakter olabilir \@ \"</li><li>"Simgesinden sonra en fazla 48 karakter olabilir \@ \"</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD parola ilkeleri

Bir parola ilkesi, doğrudan Azure AD 'de oluşturulup yönetilen tüm kullanıcı hesaplarına uygulanır. Bu parola ilkesi ayarlarından bazıları değiştirilemez, ancak [Azure AD parola koruması veya hesap kilitleme parametreleri için özel yasaklanmış parolalar yapılandırabilirsiniz](tutorial-configure-custom-password-protection.md) .

Varsayılan olarak, bir hesap, yanlış parola ile 10 başarısız oturum açma denemesinden sonra kilitlenir. Kullanıcı bir dakika boyunca kilitlenmiş. Daha fazla hatalı oturum açma denemesi, Kullanıcı süresini artırmak için kullanıcıyı kilitler. [Akıllı kilitleme](howto-password-smart-lockout.md) , aynı parolanın kilitleme sayacını arttırmaktan kaçınmak için son üç hatalı parola karmalarını izler. Birisi aynı hatalı parolayı birden çok kez girerse, bu davranış hesabın kilitlenmesine neden olmaz. Akıllı kilitleme eşiğini ve süresini tanımlayabilirsiniz.

*Enforcecizpasswordpolicyforpasswordsyncedusers*'ı etkinleştirmediğiniz müddetçe, Azure AD parola ilkesi, Azure AD Connect kullanılarak şirket içi AD DS ortamından eşitlenen Kullanıcı hesaplarına uygulanmaz.

Aşağıdaki Azure AD parola ilkesi seçenekleri tanımlanmıştır. Aksi belirtilmedikçe, bu ayarları değiştiremezsiniz:

| Özellik | Gereksinimler |
| --- | --- |
| İzin verilen karakterler |<ul><li>A – Z</li><li>a-z</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \: ',. ? / \` ~ " ( ) ;</li> <li>boş alan</li></ul> |
| Karakterlere izin verilmiyor | Unicode karakterler. |
| Parola kısıtlamaları |<ul><li>En az 8 karakter ve en fazla 256 karakter.</li><li>Aşağıdakilerden üç tanesi olmalıdır:<ul><li>Küçük harfli karakterler.</li><li>Büyük harfli karakterler.</li><li>Sayılar (0-9).</li><li>Semboller (önceki parola kısıtlamalarına bakın).</li></ul></li></ul> |
| Parola süre sonu süresi (en fazla parola yaşı) |<ul><li>Varsayılan değer: **90** gün.</li><li>Değer, `Set-MsolPasswordPolicy` Windows PowerShell için Azure Active Directory modülünden cmdlet kullanılarak yapılandırılabilir.</li></ul> |
| Parola süre sonu bildirimi (kullanıcılara parola süre sonu bildirildiğinde) |<ul><li>Varsayılan değer: **14** gün (parolanın süresi dolmadan önce).</li><li>Değer, `Set-MsolPasswordPolicy` cmdlet 'i kullanılarak yapılandırılabilir.</li></ul> |
| Parola süre sonu (parolaların süresi dolmasın) |<ul><li>Varsayılan değer: **false** (parolanın bir sona erme tarihi olduğunu gösterir).</li><li>Değer, cmdlet 'i kullanılarak bireysel kullanıcı hesapları için yapılandırılabilir `Set-MsolUser` .</li></ul> |
| Parola değiştirme geçmişi | Kullanıcı bir parolayı değiştirdiğinde *son parola yeniden kullanılamaz.* |
| Parola sıfırlama geçmişi | Son parola, Kullanıcı unutulan bir parolayı sıfırladığında *yeniden kullanılabilir.* |

## <a name="administrator-reset-policy-differences"></a>Yönetici sıfırlama ilkesi farklılıkları

Varsayılan olarak, yönetici hesapları self servis parola sıfırlama için etkinleştirilmiştir ve güçlü bir varsayılan *iki ağ geçidi* parola sıfırlama ilkesi zorlanır. Bu ilke, kullanıcılarınız için tanımladığınız sunucudan farklı olabilir ve bu ilke değiştirilemez. Parola sıfırlama işlevini her zaman, hiçbir Azure yönetici rolü atanmadan Kullanıcı olarak sınamalısınız.

İki ağ geçidi ilkesiyle, yöneticilerin güvenlik sorularını kullanma yeteneği yoktur.

İki kapı ilkesi, e-posta adresi, kimlik doğrulayıcı uygulaması veya telefon numarası gibi iki kimlik doğrulama verisi gerektirir. İki kapı ilkesi aşağıdaki koşullarda geçerlidir:

* Aşağıdaki Azure Yönetici rollerinin tümü etkilenir:
  * Yardım Masası Yöneticisi
  * Hizmet desteği yöneticisi
  * Faturalama yöneticisi
  * Partner Katman1 desteği
  * Partner Katman2 desteği
  * Exchange yöneticisi
  * Posta kutusu Yöneticisi
  * Skype Kurumsal yöneticisi
  * Kullanıcı yöneticisi
  * Dizin yazarları
  * Genel yönetici veya Şirket Yöneticisi
  * SharePoint yöneticisi
  * Uyumluluk Yöneticisi
  * Uygulama Yöneticisi
  * Güvenlik yöneticisi
  * Ayrıcalıklı rol yöneticisi
  * Intune yöneticisi
  * Azure AD 'ye katılmış cihaz yerel Yöneticisi
  * Uygulama Proxy Hizmeti Yöneticisi
  * Dynamics 365 Yöneticisi
  * Power BI hizmet yöneticisi
  * Kimlik doğrulama Yöneticisi
  * Parola yöneticisi
  * Ayrıcalıklı kimlik doğrulama Yöneticisi

* Deneme aboneliğinde 30 gün geçtikten sonra veya
* Azure AD kiracınız için *contoso.com* gibi özel bir etki alanı yapılandırılmıştır. veya
* Azure AD Connect, şirket içi dizininizdeki kimlikler eşitleniyor

[Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings) PowerShell cmdlet 'ini kullanarak yönetici hesaplarının SSPR kullanımını devre dışı bırakabilirsiniz. `-SelfServePasswordResetEnabled $False`Parametresi, Yöneticiler IÇIN SSPR 'yi devre dışı bırakır.

### <a name="exceptions"></a>Özel durumlar

Tek bir geçit ilkesi, bir e-posta adresi veya telefon numarası gibi bir dizi kimlik doğrulama verisi gerektirir. Tek bir geçit ilkesi aşağıdaki koşullarda geçerlidir:

* Deneme aboneliğinin ilk 30 günü içinde olur; veya
* Azure AD kiracınız için özel bir etki alanı yapılandırılmamış, bu nedenle varsayılan **. onmicrosoft.com* kullanılıyor. Varsayılan **. onmicrosoft.com* etki alanı, üretim kullanımı için önerilmez; '
* Azure AD Connect kimlikleri eşitlemiyor

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Parola süre sonu ilkeleri

*Genel yönetici* veya *Kullanıcı Yöneticisi* , kullanıcı parolalarının kullanım süresini sona ermeyecek şekilde ayarlamak için [Windows PowerShell için Microsoft Azure ad modülü](/powershell/module/Azuread/) kullanabilir.

Ayrıca, herhangi bir zaman aşımı yapılandırmasını kaldırmak veya hangi kullanıcı parolalarının süresinin dolmamasını görmek için PowerShell cmdlet 'lerini kullanabilirsiniz.

Bu kılavuz, Intune ve Microsoft 365 gibi diğer sağlayıcılar için geçerlidir; bu da kimlik ve Dizin Hizmetleri için Azure AD 'ye bağımlıdır. Parola süre sonu, ilkenin değiştirilebilecek tek bölümüdür.

> [!NOTE]
> Yalnızca Azure AD Connect aracılığıyla eşitlenmemiş Kullanıcı hesapları için parolalar, kullanım süreleri dolacak şekilde yapılandırılabilir. Dizin eşitleme hakkında daha fazla bilgi için bkz. [Azure AD Ile ad bağlama](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>PowerShell kullanarak parola ilkelerini ayarlama ve denetleme

Başlamak için [Azure AD PowerShell modülünü indirip yükleyin](/powershell/module/Azuread/) ve [Azure AD kiracınıza bağlayın](/powershell/module/azuread/connect-azuread#examples).

Modül yüklendikten sonra, gerektiğinde her bir görevi gerçekleştirmek için aşağıdaki adımları kullanın.

### <a name="check-the-expiration-policy-for-a-password"></a>Parola için süre sonu ilkesini denetleme

1. Bir PowerShell istemi açın ve *genel yönetici* veya *Kullanıcı Yöneticisi* hesabı kullanarak [Azure AD kiracınıza bağlanın](/powershell/module/azuread/connect-azuread#examples) .
1. Tek bir kullanıcı veya tüm kullanıcılar için aşağıdaki komutlardan birini çalıştırın:

   * Tek bir kullanıcının parolasının süresiz olarak ayarlanmış olup olmadığını görmek için aşağıdaki cmdlet 'i çalıştırın. `<user ID>`Denetlemek istediğiniz kullanıcının kullanıcı kimliğiyle değiştirin, örneğin *drley \@ contoso.onmicrosoft.com* gibi:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Tüm kullanıcılar için **parola süresi dolmasın** ayarını görmek için aşağıdaki cmdlet 'i çalıştırın:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Bir parolayı sona ermek üzere ayarla

1. Bir PowerShell istemi açın ve *genel yönetici* veya *Kullanıcı Yöneticisi* hesabı kullanarak [Azure AD kiracınıza bağlanın](/powershell/module/azuread/connect-azuread#examples) .
1. Tek bir kullanıcı veya tüm kullanıcılar için aşağıdaki komutlardan birini çalıştırın:

   * Parolanın süresi dolmadan bir kullanıcının parolasını ayarlamak için aşağıdaki cmdlet 'i çalıştırın. `<user ID>`Denetlemek istediğiniz kullanıcının kullanıcı kimliğiyle değiştirin ( *drley \@ contoso.onmicrosoft.com* gibi)

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Kuruluştaki tüm kullanıcıların parolalarını süreleri dolacak şekilde ayarlamak için aşağıdaki cmdlet 'i kullanın:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Süresiz olarak bir parola ayarlayın

1. Bir PowerShell istemi açın ve *genel yönetici* veya *Kullanıcı Yöneticisi* hesabı kullanarak [Azure AD kiracınıza bağlanın](/powershell/module/azuread/connect-azuread#examples) .
1. Tek bir kullanıcı veya tüm kullanıcılar için aşağıdaki komutlardan birini çalıştırın:

   * Bir kullanıcının parolasını hiçbir zaman dolmayacak şekilde ayarlamak için aşağıdaki cmdlet 'i çalıştırın. `<user ID>`Denetlemek istediğiniz kullanıcının kullanıcı kimliğiyle değiştirin ( *drley \@ contoso.onmicrosoft.com* gibi)

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Bir kuruluştaki tüm kullanıcıların parolalarını hiçbir zaman sona ermeyecek şekilde ayarlamak için aşağıdaki cmdlet 'i çalıştırın:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > Parolalar `-PasswordPolicies DisablePasswordExpiration` , özniteliğe göre hala yaş olarak ayarlanmıştır `pwdLastSet` . Özniteliğini temel alarak `pwdLastSet` , süre sonunu olarak değiştirirseniz `-PasswordPolicies None` , 90 günden eski olan tüm parolalar kullanıcının bir `pwdLastSet` sonraki oturum açışlarında değiştirmesini gerektirir. Bu değişiklik, çok sayıda kullanıcıyı etkileyebilir.

## <a name="next-steps"></a>Sonraki adımlar

SSPR 'yi kullanmaya başlamak için bkz. [öğretici: kullanıcıların hesaplarının kilidini açma veya Azure Active Directory self servis parola sıfırlama kullanarak parolaları sıfırlamalarını sağlama](tutorial-enable-sspr.md).

Eğer veya kullanıcılarınızın SSPR ile ilgili sorunları varsa bkz. [self servis parola sıfırlama sorunlarını giderme](./troubleshoot-sspr.md)
