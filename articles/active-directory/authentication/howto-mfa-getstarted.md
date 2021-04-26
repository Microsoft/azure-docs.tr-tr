---
title: Azure AD Multi-Factor Authentication dağıtım konuları
description: Azure AD Multi-Factor Authentication başarılı bir şekilde uygulanması için dağıtım konuları ve stratejileri hakkında bilgi edinin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ee81abd7cd0268a7cbd6b16aa6065ec7b54bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861315"
---
# <a name="plan-an-azure-ad-multi-factor-authentication-deployment"></a>Azure AD Multi-Factor Authentication dağıtımını planlayın

İnsanlar, giderek daha karmaşık senaryolarda kurumsal kaynaklara bağlanıyor. Kullanıcılar, genellikle birden çok platformda akıllı telefonlar, tabletler, bilgisayarlar ve dizüstü bilgisayarlar ile kurumsal ağ üzerinde kuruluşa ait, kişisel ve kamu cihazlarından bağlanır. Bu her zaman bağlı, çok cihazlı ve çok platformlu dünyada, Kullanıcı hesaplarının güvenliği her zamankinden daha önemlidir. Parolalar,, özellikle de kullanıcılar hesapları arasında parolaları yeniden kullanmak için bir karmaşıklık ve cihazlar, ağlar ve platformlar genelinde kullanılan, Kullanıcı hesabının güvenliğini sağlamak için artık yeterli değildir. Gelişmiş kimlik avı ve diğer sosyal mühendislik saldırıları, Kullanıcı adları ve parolaların koyu Web üzerinden gönderilmesini ve satılmasını sağlayabilir.

[Azure AD Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) , verilere ve uygulamalara erişimi korumaya yardımcı olur. İkinci bir kimlik doğrulama biçimi kullanarak ek bir güvenlik katmanı sağlar. Kuruluşlar, çözümün belirli ihtiyaçlarını karşılayacak şekilde [koşullu erişimi](../conditional-access/overview.md) kullanabilir.

Bu dağıtım kılavuzunda, bir Azure AD Multi-Factor Authentication toplaması 'nın nasıl planlanacağı ve test yapılacağı gösterilir.

Azure AD Multi-Factor Authentication hızlı bir şekilde görmek ve daha sonra ek dağıtım konularını anlamak için geri dönüp:

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication’ı etkinleştirme](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Önkoşullar

Azure AD Multi-Factor Authentication dağıtımına başlamadan önce göz önünde bulundurmanız gereken önkoşul öğeleri vardır.

| Senaryo | Önkoşul |
| --- | --- |
| Modern kimlik doğrulaması ile **yalnızca bulutta** bulunan kimlik ortamı | **Ek önkoşul görevi yok** |
| **Karma** kimlik senaryoları | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) dağıtılır ve kullanıcı kimlikleri Azure Active Directory ile şirket içi Active Directory Domain Services ile eşitlenir veya Federe olur. |
| Bulut erişimi için yayımlanan şirket içi eski uygulamalar | Azure AD [uygulama proxy 'si](../manage-apps/application-proxy.md) dağıtıldı. |
| Azure AD MFA 'yı RADIUS kimlik doğrulamasıyla kullanma | Bir [ağ Ilkesi sunucusu (NPS)](howto-mfa-nps-extension.md) dağıtılır. |
| Kullanıcılar Microsoft Office 2010 veya daha önceki bir sürümü veya iOS 11 veya daha önceki bir sürümü için Apple Mail | İOS 12 veya üzeri için [Microsoft Office 2013 veya sonraki bir sürüme](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) ve Apple Mail 'e yükseltin. Koşullu erişim eski kimlik doğrulama protokolleri tarafından desteklenmez. |

## <a name="plan-user-rollout"></a>Kullanıcı dağıtımını planlayın

MFA Dağıtım planınız, bir pilot dağıtımı ve ardından destek kapasiteniz dahilinde olan dağıtım dalgalarını içermelidir. Koşullu erişim ilkelerinizi küçük bir pilot kullanıcı grubuna uygulayarak, piyasaya çıkmayı başlatın. Pilot kullanıcılar, kullanılan işlem ve kayıt davranışları üzerindeki etkiyi değerlendirdikten sonra, ilkeye daha fazla grup ekleyebilir veya var olan gruplara daha fazla kullanıcı ekleyebilirsiniz.

### <a name="user-communications"></a>Kullanıcı iletişimleri

Kullanıcılara, planlı iletişimler, yaklaşan değişiklikler, Azure AD MFA kayıt gereksinimleri ve gerekli Kullanıcı eylemleri hakkında bilgi vermek önemlidir. İletişim, değişiklik yönetimi veya Insan kaynakları departmanları gibi, kuruluşunuzun içinden temsilcileriyle iletişim kurmanızı öneririz.

Microsoft, iletişimlerinizin taslağını oluşturmanıza yardımcı olmak için [iletişim şablonları](https://aka.ms/mfatemplates) ve [Son Kullanıcı belgeleri](../user-help/security-info-setup-signin.md) sağlar. Kullanıcıları [https://myprofile.microsoft.com](https://myprofile.microsoft.com) , bu sayfadaki **güvenlik bilgileri** bağlantılarını seçerek doğrudan kaydettirmek üzere gönderebilirsiniz.

## <a name="deployment-considerations"></a>Dağıtma konuları

Azure AD Multi-Factor Authentication, ilkeleri koşullu erişimle zorlayarak dağıtılır. Koşullu erişim ilkesi, bazı ölçütlere göre karşılandığında kullanıcıların Multi-Factor Authentication gerçekleştirmesini gerektirebilir:

* Tüm kullanıcılar, belirli bir Kullanıcı, bir grubun üyesi veya atanmış rol
* Erişildiği belirli bulut uygulamasına
* Cihaz platformu
* Cihazın durumu
* Ağ konumu veya coğrafi konumda bulunan IP adresi
* İstemci uygulamaları
* Oturum açma riski (kimlik koruması gerekir)
* Uyumlu cihaz
* Karma Azure AD 'ye katılmış cihaz
* Onaylanan istemci uygulaması

Multi-Factor Authentication [dağıtım malzemelerinden](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) özelleştirilebilir posterler ve e-posta şablonlarını kullanarak kuruluşunuza Multi-Factor Authentication 'ı kullanın.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Koşullu erişimle Multi-Factor Authentication etkinleştirme

Koşullu erişim ilkeleri kayıt uygulanmasını zorlar, kayıtsız kullanıcıların kaydı ilk oturum açma sırasında tamamlamaları gerekir, önemli bir güvenlik değerlendirmesi.

[Azure AD kimlik koruması](../identity-protection/howto-identity-protection-configure-risk-policies.md) , hem bir kayıt ilkesini hem de otomatik risk algılama ve düzeltme ILKELERINI Azure AD Multi-Factor Authentication hikayesine katkıda bulunur. İlkeler, güvenliği tehlikeye giren bir kimlik tehditleri olduğunda veya bir oturum açma, aşağıdaki [Olaylar](../identity-protection/overview-identity-protection.md)riskli olarak kabul edildiğinde MFA gerektirirken parola değişikliklerini zorlamak için oluşturulabilir:

* Sızdırılan kimlik bilgileri
* Anonim IP adreslerinden oturum açma işlemleri
* Alışılmadık konumlara imkansız seyahat
* Alışılmadık konumlardan oturum açma işlemleri
* Bulaşma olan cihazlardan oturum açma işlemleri
* Şüpheli etkinliklerle IP adreslerinden oturum açma işlemleri

Azure Active Directory Kimlik Koruması tarafından algılanan bazı risk algılamaları gerçek zamanlı olarak oluşur ve bazıları çevrimdışı işleme gerektirir. Yöneticiler, riskli davranışlar gösteren ve el ile düzeltebilen, parola değişikliği gerektiren veya koşullu erişim ilkelerinin bir parçası olarak çok faktörlü kimlik doğrulaması gerektiren kullanıcıları engellemeyi seçebilirler.

## <a name="define-network-locations"></a>Ağ konumlarını tanımlama

Kuruluşların, [adlandırılmış konumları](../conditional-access/location-condition.md#named-locations)kullanarak ağını tanımlamak Için koşullu erişim kullanmalarını öneririz. Kuruluşunuz kimlik koruması kullanıyorsa, adlandırılmış konumlar yerine risk tabanlı ilkeler kullanmayı göz önünde bulundurun.

### <a name="configuring-a-named-location"></a>Adlandırılmış bir konum yapılandırma

1. Azure portal **Azure Active Directory** açın
2. **Güvenlik** seçin
3. **Yönet** altında, **adlandırılmış konumlar** ' ı seçin.
4. **Yeni konum** Seç
5. **Ad** alanında anlamlı bir ad sağlayın
6. *IP aralıklarını* veya *ülkeleri/bölgeleri* kullanarak konum tanımlamayı seçin
   1. *IP aralıklarını* kullanıyorsanız
      1. *Güvenilen konum olarak işaretistemediğinize* karar verin. Güvenilir bir konumdan oturum açılması, kullanıcının oturum açma riskini azaltır. Bu konumu yalnızca, girilen IP aralıklarının kuruluşunuzda kurulu olduğunu ve güvenilir olduğunu biliyorsanız güvenilir olarak işaretleyin.
      2. IP aralıklarını belirtin
   2. *Ülke/bölge* kullanılıyorsa
      1. Açılan menüyü genişletin ve bu adlandırılmış konum için tanımlamak istediğiniz ülkeleri veya bölgeleri seçin.
      2. *Bilinmeyen alanların* eklenip eklenmeyeceğini belirleyin. Bilinmeyen alan, bir ülkeye/bölgeye eşlenemeyen IP adresleridir.
7. **Oluştur**’u seçin

## <a name="plan-authentication-methods"></a>Kimlik doğrulama yöntemlerini planlayın

Yöneticiler, kullanıcılar için kullanılabilir hale getirmek istedikleri [kimlik doğrulama yöntemlerini](../authentication/concept-authentication-methods.md) seçebilirler. Birincil yöntemi kullanılamaz durumda olduğunda kullanıcıların bir yedekleme yöntemine sahip olması için birden fazla kimlik doğrulama yöntemine izin vermek önemlidir. Yöneticilerin etkinleştiremeleri için aşağıdaki yöntemler kullanılabilir:

> [!TIP]
> Microsoft, daha güvenli ve geliştirilmiş bir kullanıcı deneyimi için Azure AD Multi-Factor Authentication birincil yöntemi olarak Microsoft Authenticator (mobil uygulama) kullanılmasını önerir. Microsoft Authenticator uygulaması, ulusal standartlar ve teknoloji Authenticator güvence seviyelerini de [karşılar](https://azure.microsoft.com/resources/microsoft-nist/) . 

### <a name="notification-through-mobile-app"></a>Mobil uygulama aracılığıyla bildirim

Mobil cihazınızda Microsoft Authenticator uygulamasına anında iletme bildirimi gönderilir. Kullanıcı bildirimi görüntüler ve doğrulamayı tamamlamaya yönelik **onaylamayı** seçer. Mobil uygulama aracılığıyla anında iletme bildirimleri, kullanıcılar için en az zorive seçeneğini sağlar. Bunlar ayrıca, telefon yerine bir veri bağlantısı kullandıkları için en güvenilir ve güvenli seçenektir.

> [!NOTE]
> Kuruluşunuzda Çin 'de çalışan veya Çin 'e geçiş yapan personel varsa, **Android cihazlarda** **mobil uygulama yöntemi aracılığıyla yapılan bildirim** söz konusu ülkede/bölgede çalışmaz. Bu kullanıcılar için alternatif yöntemler kullanılabilir hale gelmelidir.

### <a name="verification-code-from-mobile-app"></a>Mobil uygulamadaki doğrulama kodu

Microsoft Authenticator uygulaması gibi bir mobil uygulama, her 30 saniyede yeni bir OATH doğrulama kodu oluşturur. Kullanıcı doğrulama kodunu oturum açma arabirimine girer. Mobil uygulama seçeneği, telefonun bir veri ya da hücresel sinyal içerip içermediğini veya bu şekilde kullanılabilir.

### <a name="call-to-phone"></a>Telefon çağrısı

Kullanıcıya otomatik bir sesli çağrı konur. Kullanıcı, **#** kimlik doğrulamasını onaylamak için çağrıyı yanıtlar ve telefon tuş takımında basar. Telefon çağrısı, bir mobil uygulamadan bildirim veya doğrulama kodu için harika bir yedekleme yöntemidir.

### <a name="text-message-to-phone"></a>Telefona kısa mesaj

Kullanıcıya doğrulama kodu içeren bir SMS mesajı gönderilir, kullanıcıdan doğrulama kodunu oturum açma arabirimine girmesi istenir.

### <a name="choose-verification-options"></a>Doğrulama seçeneklerini belirleyin

1. **Azure Active Directory**, **Kullanıcılar**, **Multi-Factor Authentication** gidin.

   ![Azure portal içindeki Azure AD kullanıcıları dikey penceresinden Multi-Factor Authentication portalına erişme](media/howto-mfa-getstarted/users-mfa.png)

1. Yeni sekmede, **hizmet ayarlarına** gidin ' i açar.
1. **Doğrulama seçenekleri** altında, kullanıcılara sunulan yöntemlerin tüm kutularını işaretleyin.

   ![Multi-Factor Authentication hizmeti ayarları sekmesinde doğrulama yöntemlerini yapılandırma](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. **Kaydet**'e tıklayın.
1. **Hizmet ayarları** sekmesini kapatın.

## <a name="plan-registration-policy"></a>Kayıt ilkesini planla

Yöneticiler, kullanıcıların yöntemlerini nasıl kaydedeceğini belirlememelidir. Kuruluşlar, Azure AD MFA ve self servis parola sıfırlama (SSPR) için [Yeni Birleşik kayıt deneyimini etkinleştirmelidir](howto-registration-mfa-sspr-combined.md) . SSPR, kullanıcıların, çok faktörlü kimlik doğrulaması için kullandıkları yöntemleri kullanarak parolalarını güvenli bir şekilde sıfırlamasına olanak sağlar. Bu Birleşik kayıt, her iki hizmet için bir kez kayıt yeteneğine sahip olan kullanıcılar için harika bir deneyim olduğu için önerilir. SSPR ve Azure AD MFA için aynı yöntemlerin etkinleştirilmesi, kullanıcılarınızın her iki özelliği de kullanmak üzere kaydollemelerine izin verir.

### <a name="registration-with-identity-protection"></a>Kimlik koruması ile kaydolma

Kuruluşunuz Azure Active Directory Kimlik Koruması kullanıyorsa, kullanıcılarınıza etkileşimli olarak oturum açtıklarında kaydolmaları için, [MFA kayıt ilkesini yapılandırın](../identity-protection/howto-identity-protection-configure-mfa-policy.md) .

### <a name="registration-without-identity-protection"></a>Kimlik koruması olmadan kayıt

Kuruluşunuzun kimlik Korumasını etkinleştiren lisansı yoksa, kullanıcılardan oturum açma sırasında MFA 'nın bir sonraki sefer yapması istenir. MFA ile korunan uygulamaları kullanmayan kullanıcılar MFA için kaydedilmemiş olabilir. Kötü aktörlerin Kullanıcı parolasını tahmin edemeyeceği ve kendi adına MFA 'ya kaydolmasının yanı sıra hesabın denetimini etkili bir şekilde ele alması için tüm kullanıcıların kaydolmasını sağlamak önemlidir.

#### <a name="enforcing-registration"></a>Kayıt zorlama

Aşağıdaki adımları kullanarak, koşullu erişim ilkesi kullanıcıları Multi-Factor Authentication kaydolmaya zorlayabilir

1. Bir grup oluşturun, şu anda kayıtlı olmayan tüm kullanıcıları ekleyin.
2. Koşullu erişimi kullanarak, tüm kaynaklara erişmek için bu grup için Multi-Factor Authentication 'ı zorlayın.
3. Düzenli aralıklarla, Grup üyeliğini yeniden değerlendirin ve gruptan kaydolan kullanıcıları kaldırın.

Kayıtlı ve kayıtlı olmayan Azure AD MFA kullanıcılarını [MSOnline PowerShell modülünü](/powershell/azure/active-directory/install-msonlinev1)kullanan PowerShell komutlarıyla belirleyebilirsiniz.

#### <a name="identify-registered-users"></a>Kayıtlı kullanıcıları tanımla

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Kayıtlı olmayan kullanıcıları tanımla

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Kullanıcı başına MFA 'dan koşullu erişim tabanlı MFA 'ya Kullanıcı dönüştürme

Kullanıcılarınız Kullanıcı başına etkin ve zorunlu kılınan Azure Multi-Factor Authentication AD kullanılarak etkinleştirilmişse, aşağıdaki PowerShell, koşullu erişim tabanlı Azure AD Multi-Factor Authentication dönüştürmeyi yaparken size yardımcı olabilir.

Bu PowerShell 'i bir ıSE penceresinde çalıştırın veya yerel olarak `.PS1` çalıştırmak için bir dosya olarak kaydedin.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Kısa süre önce yukarıdaki davranışı ve PowerShell betiğini değiştirdik. Daha önce, komut dosyası MFA yöntemlerine kaydedilir, MFA 'yı devre dışı bırakmış ve yöntemleri geri yükledi. Artık devre dışı bırakma için varsayılan davranış metotları temizlemez.

## <a name="plan-conditional-access-policies"></a>Koşullu erişim ilkelerini planlayın

MFA ve diğer denetimlerin ne zaman gerekli olacağını belirleyen koşullu erişim ilkesi stratejinizi planlamak için, [Genel koşullu erişim ilkelerine](../conditional-access/concept-conditional-access-policy-common.md)bakın.

Azure AD kiracınızdan yanlışlıkla kilitlenmesini engellemeniz önemlidir. [Kiracınızda iki veya daha fazla acil durum erişim hesabı oluşturarak](../roles/security-emergency-access.md) ve bunları koşullu erişim ilkenize dışlayarak, bu yanlışlıkla yönetici erişimi eksikliğinden etkilerini azaltabilirsiniz.

### <a name="create-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

1. [Azure Portal](https://portal.azure.com) bir genel yönetici hesabı kullanarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
   ![Pilot grubundaki Azure portal kullanıcılar için MFA 'yı etkinleştirmek üzere koşullu erişim ilkesi oluşturma](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. İlkeniz için anlamlı bir ad girin.
1. **Kullanıcılar ve gruplar** altında:
   * **Dahil et** sekmesinde, **tüm kullanıcılar** radyo düğmesini seçin.
   * **Dışla** sekmesinde, **Kullanıcılar ve gruplar** kutusunu işaretleyin ve acil durum erişim hesaplarınızı seçin.
   * **Bitti**’ye tıklayın.
1. **Bulut uygulamaları** altında **tüm bulut uygulamaları** radyo düğmesini seçin.
   * Isteğe bağlı: **Dışla** sekmesinde, kuruluşunuzun MFA gerektirmeyen bulut uygulamaları ' nı seçin.
   * **Bitti**’ye tıklayın.
1. **Koşullar** bölümünde:
   * Isteğe bağlı: Azure kimlik koruması 'nı etkinleştirdiyseniz, ilkenin bir parçası olarak oturum açma riskini değerlendirmeyi seçebilirsiniz.
   * Isteğe bağlı: güvenilen konumlar veya adlandırılmış konumlar yapılandırdıysanız, bu konumları ilke içinden dahil etmek veya hariç tutmak için belirtebilirsiniz.
1. **İzin** ver ' in altında, **erişim verme** radyo düğmesinin seçili olduğundan emin olun.
    * **Multi-Factor Authentication gerektir** kutusunu işaretleyin.
    * **Seç**’e tıklayın.
1. **Oturum** bölümünü atlayın.
1. **Ilkeyi etkinleştir** **' i açık** yap olarak ayarlayın.
1. **Oluştur**’a tıklayın.

## <a name="plan-integration-with-on-premises-systems"></a>Şirket içi sistemlerle tümleştirmeyi planlayın

Doğrudan Azure AD 'de kimlik doğrulaması olmayan bazı eski ve şirket içi uygulamalar, MFA 'yı kullanmak için ek adımlar gerektirir:

* Uygulama proxy 'si kullanması gereken eski şirket içi uygulamalar.
* NPS sunucusu ile MFA bağdaştırıcısını kullanması gereken şirket içi RADIUS uygulamaları.
* AD FS 2016 veya daha yeni bir sürümü ile MFA bağdaştırıcısını kullanması gereken şirket içi AD FS uygulamalar.

Doğrudan Azure AD ile kimlik doğrulaması yapan ve modern kimlik doğrulamasına (WS-beslenir, SAML, OAuth, OpenID Connect) sahip olan uygulamalar, koşullu erişim ilkelerinin doğrudan kullanılabilmesini sağlayabilir.

### <a name="use-azure-ad-mfa-with-azure-ad-application-proxy"></a>Azure AD MFA 'yı Azure AD Uygulama Ara Sunucusu kullanma

Şirket içinde bulunan uygulamalar Azure [ad uygulama ara sunucusu](../manage-apps/application-proxy.md) aracılığıyla Azure AD kiracınıza yayımlanabilir ve Azure AD ön kimlik doğrulaması kullanacak şekilde YAPıLANDıRıLDıYSA azure ad Multi-Factor Authentication yararlanabilir.

Bu uygulamalar, Azure AD Multi-Factor Authentication uygulayan koşullu erişim ilkelerine tabidir, tıpkı diğer Azure AD ile tümleşik uygulamalar gibi.

Benzer şekilde, Azure AD Multi-Factor Authentication tüm Kullanıcı oturum açma işlemleri için zorlanırsa, Azure AD Uygulama Ara Sunucusu ile yayınlanan şirket içi uygulamalar korunacaktır.

### <a name="integrating-azure-ad-multi-factor-authentication-with-network-policy-server"></a>Azure AD Multi-Factor Authentication 'yi ağ Ilkesi sunucusu ile tümleştirme

Azure AD MFA için ağ Ilkesi sunucusu (NPS) uzantısı, mevcut sunucularınızı kullanarak kimlik doğrulama altyapınıza bulut tabanlı MFA özellikleri ekler. NPS uzantısıyla, mevcut kimlik doğrulama akışınıza telefon araması, SMS mesajı veya telefon uygulaması doğrulaması ekleyebilirsiniz. Bu tümleştirme aşağıdaki sınırlamalara sahiptir:

* CHAPv2 protokolüyle, yalnızca Authenticator uygulama anında iletme bildirimleri ve sesli arama desteklenir.
* Koşullu erişim ilkeleri uygulanamıyor.

NPS uzantısı, [VPN](howto-mfa-nps-extension-vpn.md), [Uzak Masaüstü Ağ Geçidi BAĞLANTıLARı](howto-mfa-nps-extension-rdg.md)veya diğer RADIUS özellikli uygulamaları korumak için ikinci bir kimlik doğrulama faktörü sağlamak üzere RADIUS ve bulut tabanlı Azure AD MFA arasında bir bağdaştırıcı işlevi görür. Bu ortamda Azure AD MFA için kaydolulmuş kullanıcılar tüm kimlik doğrulama girişimleri için gerekli olacaktır, koşullu erişim ilkelerinin olmaması MFA her zaman gereklidir.

#### <a name="implementing-your-nps-server"></a>NPS sunucunuzu uygulama

Dağıtılmış ve zaten kullanımda olan bir NPS örneğiniz varsa, [mevcut NPS altyapınızı Azure AD Multi-Factor Authentication tümleştirin](howto-mfa-nps-extension.md). NPS 'YI ilk kez ayarlıyorsanız yönergeler için [ağ Ilkesi sunucusu 'na (NPS)](/windows-server/networking/technologies/nps/nps-top) bakın. Sorun giderme kılavuzu, [Azure AD Multi-Factor Authentication IÇIN NPS uzantısından hata Iletilerini çözümleme](howto-mfa-nps-extension-errors.md)makalesinde bulunabilir.

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>MFA için kayıtlı olmayan kullanıcılar için NPS 'YI hazırlama

MFA ile kayıtlı olmayan kullanıcılar kimlik doğrulamaya çalıştığında ne olacağını seçin. `REQUIRE_USER_MATCH`Özellik davranışını denetlemek için kayıt defteri yolundaki kayıt defteri ayarını kullanın `HKLM\Software\Microsoft\AzureMFA` . Bu ayarın tek bir yapılandırma seçeneği vardır.

| Anahtar | Değer | Varsayılan |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | DOĞRU/YANLıŞ | Ayarlanmadı (TRUE değerine denktir) |

Bu ayarın amacı, bir Kullanıcı MFA 'ya kaydolmadıysa ne yapılacağını belirlemektir. Bu ayarı değiştirmenin etkileri aşağıdaki tabloda listelenmiştir.

| Ayarlar | Kullanıcı MFA durumu | Etkiler |
| --- | --- | --- |
| Anahtar yok | Kaydedilmedi | MFA sınaması başarısız |
| Değer true olarak ayarlandı/ayarlanmadı | Kaydedilmedi | MFA sınaması başarısız |
| Anahtar false olarak ayarlandı | Kaydedilmedi | MFA olmadan kimlik doğrulama |
| Anahtar yanlış veya doğru olarak ayarlandı | Kaydedildi | MFA ile kimlik doğrulaması yapılmalıdır |

### <a name="integrate-with-active-directory-federation-services"></a>Active Directory Federasyon Hizmetleri (AD FS) ile tümleştirin

Kuruluşunuz Azure AD ile federe ise, Azure AD Multi-Factor Authentication kullanarak hem şirket içinde hem de bulutta [AD FS kaynakları güvenli hale](multi-factor-authentication-get-started-adfs.md)getirebilirsiniz. Azure AD MFA, parolaları azaltmanıza ve kimlik doğrulaması için daha güvenli bir yol sağlamanıza olanak sağlar. Windows Server 2016 ' den itibaren, artık birincil kimlik doğrulaması için Azure AD MFA 'yı yapılandırabilirsiniz.

Windows Server 2012 R2 'deki AD FS farklı olarak, AD FS 2016 Azure AD MFA bağdaştırıcısı doğrudan Azure AD ile tümleşir ve şirket içi Azure MFA sunucusu gerektirmez. Azure AD MFA bağdaştırıcısı Windows Server 2016 ' de yerleşik olarak bulunur ve ek yükleme için gerekli değildir.

AD FS 2016 ile Azure AD MFA kullanırken ve hedef uygulama koşullu erişim ilkesine tabidir, ek hususlar vardır:

* Uygulama Azure AD 'ye bağlı olan bir taraf olan AD FS 2016 veya daha yeni bir sürümü ile federe olduğunda koşullu erişim kullanılabilir.
* Uygulama bir bağlı olan taraf AD FS 2016 veya 2019 AD FS ve AD FS 2016 veya AD FS 2019 ile yönetilmiyorsa koşullu erişim kullanılamaz.
* AD FS 2016 veya AD FS 2019, birincil kimlik doğrulama yöntemi olarak Azure AD MFA kullanacak şekilde yapılandırıldığında koşullu erişim de kullanılamaz.

#### <a name="ad-fs-logging"></a>Günlüğe kaydetme AD FS

Hem Windows Güvenlik günlüğünde hem de AD FS Yönetim günlüğünde standart AD FS 2016 ve 2019 günlük kaydı, kimlik doğrulama istekleri ve bunların başarısı veya başarısızlığı hakkında bilgiler içerir. Bu olaylar içindeki olay günlüğü verileri, Azure AD MFA 'nın kullanılıp kullanılmadığını gösterir. Örneğin, AD FS bir denetim olay KIMLIĞI 1200 şunları içerebilir:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Sertifikaları yenileme ve yönetme

Her bir AD FS sunucusunda, yerel bilgisayar My deposunda, sertifika sona erme tarihini içeren OU = Microsoft AD FS Azure MFA başlıklı, kendinden imzalı bir Azure AD MFA sertifikası olacaktır. Son kullanma tarihini öğrenmek için, her bir AD FS sunucuda bu sertifikanın geçerlilik süresini denetleyin.

Sertifikalarınızın geçerlilik süresi dolmak üzere ise, [her bir AD FS sunucuda yeni BIR MFA sertifikası oluşturun ve doğrulayın](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Aşağıdaki kılavuzda AD FS sunucularınızda Azure AD MFA sertifikalarını yönetme ayrıntıları verilmiştir. Azure AD MFA ile AD FS yapılandırdığınızda, `New-AdfsAzureMfaTenantCertificate` PowerShell cmdlet 'i aracılığıyla oluşturulan sertifikalar iki yıl için geçerlidir. MFA hizmetindeki ovoid kesintilerini sona ermeden önce yenilenen sertifikaları yenileyip yükler.

## <a name="implement-your-plan"></a>Planınızı uygulayın

Çözümünüzü planladığınıza göre aşağıdaki adımları izleyerek uygulamasını uygulayabilirsiniz:

1. Gerekli önkoşulları karşılayın
   1. Karma senaryolar için [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) dağıtma
   1. Bulut erişimi için yayımlanan şirket içi uygulamalarda [Azure AD uygulama ara sunucusu](../manage-apps/application-proxy.md) dağıtma
   1. Herhangi bir RADIUS kimlik doğrulaması için [NPS](/windows-server/networking/technologies/nps/nps-top) dağıtma
   1. Kullanıcıların modern kimlik doğrulaması etkinken Microsoft Office desteklenen sürümlerine yükseltildiğinden emin olun
1. Seçilen [kimlik doğrulama yöntemlerini](#choose-verification-options) Yapılandır
1. [Adlandırılmış ağ konumlarınızı](../conditional-access/location-condition.md#named-locations) tanımlayın
1. MFA kullanıma sunulmaya başlamak için grupları seçin.
1. [Koşullu erişim ilkelerinizi](#create-conditional-access-policy) yapılandırın
1. MFA kayıt ilkenizi yapılandırma
   1. [Birleşik MFA ve SSPR](howto-registration-mfa-sspr-combined.md)
   1. [Kimlik koruması](../identity-protection/howto-identity-protection-configure-mfa-policy.md) ile
1. Kullanıcı iletişimleri gönder ve kaydolmalarını kullanıcılara al [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Kimin kaydolduğunu takip edin](#identify-non-registered-users)

> [!TIP]
> Kamu Bulutu kullanıcıları şurada kaydedebilir: [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Çözümünüzü yönetin

Azure AD MFA raporları

Azure AD Multi-Factor Authentication Azure portal aracılığıyla rapor sağlar:

| Rapor | Konum | Description |
| --- | --- | --- |
| Kullanım ve sahtekarlık uyarıları | Azure AD > oturum açma işlemleri | Genel kullanım, Kullanıcı Özeti ve kullanıcı ayrıntıları hakkında bilgi sağlar; Ayrıca, belirtilen tarih aralığı boyunca gönderilen sahtekarlık uyarılarının geçmişi. |

## <a name="troubleshoot-mfa-issues"></a>MFA sorunlarını giderme

Microsoft Desteği merkezindeki [Azure ad Multi-Factor Authentication sorun giderme makalesinde](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) Azure AD MFA ile ilgili sık karşılaşılan sorunlara yönelik çözümler bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD Multi-Factor Authentication 'yi eylemde görmek için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication’ı etkinleştirme](tutorial-enable-azure-mfa.md)
