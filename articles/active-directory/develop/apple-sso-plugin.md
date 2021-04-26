---
title: Apple cihazlar için Microsoft Enterprise SSO eklentisi
titleSuffix: Microsoft identity platform | Azure
description: İOS, ıpados ve macOS cihazları için Azure Active Directory SSO eklentisi hakkında bilgi edinin.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 05bfcc86c72d9eb393da919035ce198948b943f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559137"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple cihazları için Microsoft Enterprise SSO eklentisi (Önizleme)

>[!IMPORTANT]
> Bu özellik [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Apple cihazları Için Microsoft ENTERPRISE SSO eklentisi* , Apple 'ın [Kurumsal Çoklu oturum açma](https://developer.apple.com/documentation/authenticationservices) özelliğini destekleyen tüm uygulamalarda MacOS, iOS ve ıpados 'LARDA Azure Active Directory (Azure AD) hesapları için çoklu oturum açma (SSO) sağlar. Eklenti, işletmenizin bağlı olabileceği, ancak henüz en son kimlik kitaplıklarını veya protokolleri desteklemeyen eski uygulamalar için SSO sağlar. Microsoft, en iyi korumayı sağlamak için uygulamanızın kullanılabilirliğini artırmak üzere bu eklentiyi geliştirmek üzere Apple ile yakından birlikte çalıştı.

Enterprise SSO eklentisi şu anda aşağıdaki uygulamaların yerleşik bir özelliğidir:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): IOS, ıpados
* Microsoft Intune [Şirket portalı](/mem/intune/apps/apps-company-portal-macos): MacOS

## <a name="features"></a>Özellikler

Apple cihazları için Microsoft Enterprise SSO eklentisi aşağıdaki avantajları sunar:

- Apple Enterprise SSO özelliğini destekleyen tüm uygulamalarda Azure AD hesapları için SSO sağlar.
- Bu, herhangi bir mobil cihaz yönetimi (MDM) çözümü tarafından etkinleştirilebilir.
- SSO 'yu henüz Microsoft Identity platform kitaplıklarını kullanmayan uygulamalarla genişletir.
- SSO 'yu OAuth 2, OpenID Connect ve SAML kullanan uygulamalarla genişletir.

## <a name="requirements"></a>Gereksinimler

Apple cihazları için Microsoft Enterprise SSO eklentisini kullanmak için:

- Cihaz, Apple cihazları için Microsoft Enterprise SSO eklentisinin yüklü olduğu bir uygulamayı *desteklemelidir* ve yüklemiş olmalıdır:
  - iOS 13,0 ve üzeri: [Microsoft Authenticator uygulaması](../user-help/user-help-auth-app-overview.md)
  - Idos 13,0 ve üzeri: [Microsoft Authenticator uygulaması](../user-help/user-help-auth-app-overview.md)
  - macOS 10,15 ve üzeri: [Intune şirket portalı uygulaması](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Cihazın *MDM 'ye kayıtlı* olması gerekir, örneğin, Microsoft Intune.
- Enterprise SSO eklentisini etkinleştirmek için yapılandırma *cihaza itilmiş* olmalıdır. Apple bu güvenlik kısıtlamasını gerektirir.

### <a name="ios-requirements"></a>iOS gereksinimleri:
- cihazda iOS 13,0 veya üzeri yüklü olmalıdır.
- Apple cihazları için Microsoft Enterprise SSO eklentisini sağlayan bir Microsoft uygulamasının cihazda yüklü olması gerekir. Genel önizleme için bu uygulamalar [Microsoft Authenticator uygulamasıdır](/azure/active-directory/user-help/user-help-auth-app-overview).


### <a name="macos-requirements"></a>macOS gereksinimleri:
- macOS 10,15 veya üzeri cihazda yüklü olmalıdır. 
- Apple cihazları için Microsoft Enterprise SSO eklentisini sağlayan bir Microsoft uygulamasının cihazda yüklü olması gerekir. Genel önizleme için bu uygulamalar [Intune şirket portalı uygulamasını](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)içerir.

## <a name="enable-the-sso-plug-in"></a>SSO eklentisini etkinleştirme

MDM kullanarak SSO eklentisini etkinleştirmek için aşağıdaki bilgileri kullanın.
### <a name="microsoft-intune-configuration"></a>Microsoft Intune yapılandırması

MDM hizmetiniz olarak Microsoft Intune kullanıyorsanız, Microsoft Enterprise SSO eklentisini etkinleştirmek için yerleşik yapılandırma profili ayarlarını kullanabilirsiniz:

1. Yapılandırma profilinin [SSO uygulama uzantısı](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) ayarlarını yapılandırın. 
1. Profil zaten atanmamışsa, [profili bir kullanıcı veya cihaz grubuna atayın](/mem/intune/configuration/device-profile-assign).

SSO eklentisini etkinleştiren profil ayarları, her cihaz Intune ile ilk kez iade ettiğinde grubun cihazlarına otomatik olarak uygulanır.

### <a name="manual-configuration-for-other-mdm-services"></a>Diğer MDM Hizmetleri için el ile yapılandırma

MDM için Intune kullanmıyorsanız, Apple cihazları için Microsoft Enterprise SSO eklentisini yapılandırmak için aşağıdaki parametreleri kullanın.

iOS ayarları:

- **UZANTı kimliği**: `com.microsoft.azureauthenticator.ssoextension`
- **Takım Kimliği**: Bu alan iOS için gerekli değildir.

macOS ayarları:

- **UZANTı kimliği**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Takım Kimliği**: `UBF8T346G9`

Ortak ayarlar:

- **Tür**: Redirect
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="more-configuration-options"></a>Daha fazla yapılandırma seçeneği
SSO işlevselliğini diğer uygulamalara genişletmek için daha fazla yapılandırma seçeneği ekleyebilirsiniz.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Microsoft Identity platform kitaplığı kullanmayan uygulamalar için SSO 'yu etkinleştirme

SSO eklentisi, Microsoft kimlik doğrulama kitaplığı (MSAL) gibi bir Microsoft SDK kullanılarak geliştirmediyse bile herhangi bir uygulamanın SSO 'ya katılmasını sağlar.

SSO eklentisi şu olan cihazlar tarafından otomatik olarak yüklenir:
* İOS veya ıpados üzerinde Authenticator uygulaması indirildi veya macOS 'ta Intune Şirket Portalı uygulaması indirildi.
* Cihazlarını kuruluşunuzla kaydettiniz. 

Kuruluşunuz muhtemelen çok faktörlü Authentication (MFA), passwordless kimlik doğrulaması ve koşullu erişim gibi senaryolar için kimlik doğrulayıcı uygulamasını kullanır. Bir MDM sağlayıcısı kullanarak, uygulamalarınız için SSO eklentisini açabilirsiniz. Microsoft, eklentiyi Intune 'daki Microsoft Uç Nokta Yöneticisi içinde yapılandırmayı kolay hale yaptı. Bu uygulamaları SSO eklentisini kullanacak şekilde yapılandırmak için bir izin kullanılır.

>[!IMPORTANT]
> Microsoft Enterprise SSO eklentisi yalnızca yerel Apple ağ teknolojilerini veya Web görünümlerini kullanan uygulamaları destekler. Kendi ağ katmanı uygulamasını veren uygulamaları desteklemez.  

Microsoft Identity platform kitaplığı kullanmayan uygulamalar için Microsoft Enterprise SSO eklentisini yapılandırmak için aşağıdaki parametreleri kullanın.

Belirli uygulamaların bir listesini sağlamak için şu parametreleri kullanın:

- **Anahtar**: `AppAllowList`
- **Şunu yazın**: `String`
- **Değer**: SSO 'ya katılmasına izin verilen uygulamalar için uygulama paket kimliklerinin virgülle ayrılmış listesi.
- **Örnek**: `com.contoso.workapp, com.contoso.travelapp`

Ön eklerin bir listesini sağlamak için şu parametreleri kullanın:
- **Anahtar**: `AppPrefixAllowList`
- **Şunu yazın**: `String`
- **Değer**: SSO 'ya katılmasına izin verilen uygulamalar için uygulama paket kimliği öneklerinin virgülle ayrılmış listesi. Bu parametre, belirli bir önekle başlayan tüm uygulamaların SSO 'ya katılmasını sağlar.
- **Örnek**: `com.contoso., com.fabrikam.`

MDM yöneticisinin SSO 'ya katılmasına izin verdiği [uygulamalar](./application-consent-experience.md) , son kullanıcının sessizce bir belirtecini alabilir. Bu nedenle, yalnızca güvenilen uygulamaları allowlist öğesine ekleyin. 

>[!NOTE]
> SSO 'ya katılabilen uygulamalar listesine MSAL veya ASWebAuthenticationSession kullanan uygulamalar eklemeniz gerekmez. Bu uygulamalar varsayılan olarak etkinleştirilmiştir. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>İOS cihazlarında uygulama paketi tanımlayıcıları bulma

Apple, uygulama mağazasından paket kimlikleri almanın kolay bir yolunu sağlamaz. SSO için kullanmak istediğiniz uygulamaların paket kimliklerini almanın en kolay yolu, satıcınız veya uygulama geliştiricinizi ister. Bu seçenek kullanılamıyorsa, paket kimliklerini bulmak için MDM yapılandırmanızı kullanabilirsiniz: 

1. MDM yapılandırmanızda aşağıdaki bayrağı geçici olarak etkinleştirin:

    - **Anahtar**: `admin_debug_mode_enabled`
    - **Şunu yazın**: `Integer`
    - **Değer**: 1 veya 0
1. Bu bayrak açık olduğunda, paket KIMLIĞINI bildirmek istediğiniz cihazdaki iOS uygulamalarında oturum açın. 
1. Doğrulayıcı uygulamasında **Yardım**  >  **günlükleri**  >  **görüntüleme günlükleri**' ni seçin. 
1. Günlük dosyasında aşağıdaki satırı arayın: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Bu satır, SSO uzantısına görünür olan tüm uygulama paketi kimliklerini yakalamamalıdır. 

Uygulamalar için SSO 'yu yapılandırmak üzere paket kimliklerini kullanın. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Kullanıcıların bilinmeyen uygulamalardan ve Safari tarayıcısından oturum açmalarına izin ver

Varsayılan olarak, Microsoft Kurumsal SSO eklentisi, yalnızca bir Kullanıcı MSAL veya Azure Active Directory kimlik doğrulaması kitaplığı (ADAL) gibi bir Microsoft Identity platform kitaplığı kullanan bir uygulamadan oturum açmış olduğunda, yetkili uygulamalar için SSO sağlar. Microsoft Enterprise SSO eklentisi, yeni bir belirteç alımı sırasında Microsoft Identity platform kitaplığı kullanan başka bir uygulama tarafından çağrıldığında paylaşılan bir kimlik bilgisi de alabilir.

`browser_sso_interaction_enabled`Bayrağını etkinleştirdiğinizde, bir Microsoft Identity platform kitaplığı kullanmayan uygulamalar ilk önyükleme yapabilir ve paylaşılan bir kimlik bilgisi alabilir. Safari tarayıcısı, ilk önyüklemeyi de yapabilir ve paylaşılan bir kimlik bilgisi alabilir. 

Microsoft Enterprise SSO eklentisinin paylaşılan bir kimlik bilgisi yoksa, Safari tarayıcısı, ASWebAuthenticationSession, SafariViewController veya başka bir izin verilen yerel uygulama içindeki bir Azure AD URL 'sinden her oturum açma işlemi yapıldığında bir tane almaya çalışır. 

Bayrağı etkinleştirmek için bu parametreleri kullanın: 

- **Anahtar**: `browser_sso_interaction_enabled`
- **Şunu yazın**: `Integer`
- **Değer**: 1 veya 0

macOS bu ayarı gerektirir, bu nedenle tüm uygulamalar üzerinde tutarlı bir deneyim sağlayabilir. Çoğu uygulama oturum açma için kimlik doğrulayıcı uygulamasını kullandığından iOS ve ıpados bu ayarı gerektirmez. Ancak, uygulamalarınızdan bazıları iOS veya ıpados üzerinde Authenticator uygulamasını kullanmadığında bu ayarı etkinleştirmenizi öneririz, bu bayrak deneyimi geliştirir. Ayar varsayılan olarak devre dışıdır.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>İlk önyükleme sırasında MFA sorulmayı devre dışı bırak

Varsayılan olarak, Microsoft Enterprise SSO eklentisi ilk önyükleme sırasında ve paylaşılan bir kimlik bilgisi alırken kullanıcıyı her zaman MFA 'ya ister. Kullanıcının açtığı uygulama için gerekli olmasa bile MFA sorulur. Bu davranış, MFA 'nın daha sonra gerekli olup olmadığını kullanıcıya istemek zorunda kalmadan, paylaşılan kimlik bilgisinin diğer tüm uygulamalarda kolayca kullanılmasını sağlar. Kullanıcı genel olarak daha az istem aldığından, bu kurulum genellikle iyi bir karardır.

Etkinleştirme `browser_sso_disable_mfa` , ilk önyükleme SıRASıNDA MFA 'yı devre dışı bırakır ve paylaşılan kimlik bilgileri alınıyor. Bu durumda, kullanıcıya yalnızca MFA bir uygulama veya kaynak için gerekli olduğunda sorulur. 

Bayrağı etkinleştirmek için şu parametreleri kullanın:

- **Anahtar**: `browser_sso_disable_mfa`
- **Şunu yazın**: `Integer`
- **Değer**: 1 veya 0

Kullanıcının kaç kez oturum açması gerektiğini azalttığından, bu bayrağın devre dışı kalmasını öneririz. Kuruluşunuz MFA 'yı nadiren kullanıyorsa, bayrağını etkinleştirmek isteyebilirsiniz. Ancak bunun yerine MFA 'yı daha sık kullanmanızı öneririz. Bu nedenle, bayrak varsayılan olarak devre dışıdır.

#### <a name="disable-oauth-2-application-prompts"></a>OAuth 2 uygulama istemlerini devre dışı bırak

Microsoft Enterprise SSO eklentisi, izin verilen uygulamalardan gelen ağ isteklerine paylaşılan kimlik bilgilerini ekleyerek SSO sağlar. Ancak, bazı OAuth 2 uygulamaları, protokol katmanında Son Kullanıcı komut istemlerini yanlış bir şekilde uygulayabilir. Bu sorunu görürseniz, paylaşılan kimlik bilgilerinin bu uygulamalar için yoksayıldığını da görürsünüz. Microsoft Enterprise SSO eklentisi diğer uygulamalar için çalışsa bile, kullanıcıdan oturum açması istenir.  

Bayrağın etkinleştirilmesi, `disable_explicit_app_prompt` hem yerel uygulamaların hem de Web uygulamalarının protokol katmanında Son Kullanıcı istemi zorlaması ve SSO atlama yeteneğini kısıtlar. Bayrağı etkinleştirmek için şu parametreleri kullanın:

- **Anahtar**: `disable_explicit_app_prompt`
- **Şunu yazın**: `Integer`
- **Değer**: 1 veya 0

Tüm uygulamalar üzerinde tutarlı bir deneyim almak için bu bayrağın etkinleştirilmesini öneririz. Varsayılan olarak devre dışıdır. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Belirli bir uygulama için tanımlama bilgileri aracılığıyla SSO 'yu etkinleştirme

Birkaç uygulama SSO uzantısıyla uyumsuz olabilir. Özellikle, Gelişmiş ağ ayarlarına sahip uygulamalar SSO için etkinleştirildiklerinde beklenmedik sorunlarla karşılaşabilir. Örneğin, ağ isteğinin iptal edildiğini veya kesintiye uğradığını belirten bir hata görebilirsiniz. 

[Msal bölümünü kullanmayan uygulamalarda](#applications-that-dont-use-msal) açıklanan yöntemi kullanarak oturum açmada sorun yaşıyorsanız, alternatif bir yapılandırma deneyin. Eklentiyi yapılandırmak için bu parametreleri kullanın:

- **Anahtar**: `AppCookieSSOAllowList`
- **Şunu yazın**: `String`
- **Değer**: SSO 'ya katılmasına izin verilen uygulamalar için uygulama paket kimliği öneklerinin virgülle ayrılmış listesi. Listelenen öneklerle başlayan tüm uygulamaların SSO 'ya katılmasına izin verilir.
- **Örnek**: `com.contoso.myapp1, com.fabrikam.myapp2`

Bu kurulum kullanılarak SSO için etkinleştirilen uygulamaların hem hem de ' a eklenmesi gerekir `AppCookieSSOAllowList` `AppPrefixAllowList` .

Bu yapılandırmayı yalnızca beklenmedik oturum açma hatalarıyla ilgili uygulamalar için deneyin. 

#### <a name="use-intune-for-simplified-configuration"></a>Basitleştirilmiş yapılandırma için Intune 'U kullanma

Microsoft Enterprise SSO eklentisinin yapılandırılmasını kolaylaştırmak için Intune 'U MDM hizmeti olarak kullanabilirsiniz. Örneğin, eklentiyi etkinleştirmek ve SSO almak için bir izin 'e eski uygulamalar eklemek üzere Intune 'u kullanabilirsiniz. 

Daha fazla bilgi için bkz. [Intune yapılandırma belgeleri](/intune/configuration/ios-device-features-settings).

## <a name="use-the-sso-plug-in-in-your-application"></a>Uygulamanızdaki SSO eklentisini kullanın

[Apple cihazları Için msal](https://github.com/AzureAD/microsoft-authentication-library-for-objc) sürümleri 1.1.0 ve üzeri, Apple cihazları Için MICROSOFT Enterprise SSO eklentisini destekler. Microsoft Enterprise SSO eklentisi desteği eklemek için önerilen yoldur. Microsoft Identity platform 'un tüm yeteneklerini almanızı sağlar.

Frontline çalışan senaryolar için bir uygulama oluşturuyorsanız, kurulum bilgileri için [iOS cihazları Için paylaşılan cihaz modu](msal-ios-shared-devices.md) ' na bakın.

## <a name="understand-how-the-sso-plug-in-works"></a>SSO eklentisinin nasıl çalıştığını anlayın

Microsoft Enterprise SSO eklentisi [Apple ENTERPRISE SSO çatısını](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)kullanır. Çerçeveye eklenen kimlik sağlayıcıları, etki alanları için ağ trafiğini yakalayabilir ve bu isteklerin işlenme biçimini artırabilir veya değiştirebilir. Örneğin, SSO eklentisi Son Kullanıcı kimlik bilgilerini güvenli bir şekilde toplamak, MFA istemek veya sessizce uygulamaya belirteç sağlamak için daha fazla UII gösterebilir.

Yerel uygulamalar aynı zamanda özel işlemler uygulayabilir ve doğrudan SSO eklentisiyle iletişim kurabilir. Daha fazla bilgi için bkz. [Apple 'Dan dünya genelinde 2019 Geliştirici Konferansı videosu](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>MSAL kullanan uygulamalar

[Apple cihazları Için msal](https://github.com/AzureAD/microsoft-authentication-library-for-objc) sürümleri 1.1.0 ve üzeri, Apple cihazları Için MICROSOFT Enterprise SSO eklentisini yerel olarak iş ve okul hesaplarında destekler. 

[Önerilen tüm adımları](./quickstart-v2-ios.md) izlediyseniz ve varsayılan [yeniden yönlendirme URI biçimini](./redirect-uris-ios.md)kullandıysanız özel bir yapılandırmaya gerek yoktur. SSO eklentisinin bulunduğu cihazlarda, MSAL otomatik olarak tüm etkileşimli ve sessiz Belirteç istekleri için çağırır. Hesap numaralandırması ve hesap kaldırma işlemleri için de bu uygulamayı çağırır. MSAL özel işlemlere dayanan bir yerel SSO eklentisi Protokolü uyguladığından, bu kurulum son kullanıcıya en iyi yerel deneyimi sağlar. 

SSO eklentisi MDM tarafından etkinleştirilmemişse ancak cihazda Microsoft Authenticator uygulama mevcutsa, bunun yerine MSAL, herhangi bir etkileşimli belirteç isteği için Authenticator uygulamasını kullanır. SSO eklentisi, kimlik doğrulayıcı uygulamasıyla SSO 'yu paylaşır.

### <a name="applications-that-dont-use-msal"></a>MSAL kullanmayan uygulamalar

Bir yönetici bu uygulamaları allowlist 'e eklerse, MSAL gibi bir Microsoft Identity platform kitaplığı kullanmayan uygulamalar yine de SSO alabilir. 

Aşağıdaki koşullar karşılanmadığı sürece bu uygulamalardaki kodu değiştirmeniz gerekmez:

- Uygulama, ağ isteklerini çalıştırmak için Apple çerçeveleri kullanır. Bu çerçeveler, örneğin [Wkwebview](https://developer.apple.com/documentation/webkit/wkwebview) ve [Nsurlsession](https://developer.apple.com/documentation/foundation/nsurlsession)' u içerir. 
- Uygulama, Azure AD ile iletişim kurmak için standart protokolleri kullanır. Bu protokoller, örneğin, OAuth 2, SAML ve WS-Federation gibi.
- Uygulama, yerel kullanıcı arabiriminde düz metin Kullanıcı adları ve parolalar toplanmaz.

Bu durumda, uygulama bir ağ isteği oluşturduğunda ve Kullanıcı oturumu açmak üzere bir Web tarayıcısı açtığında SSO sağlanır. Bir Kullanıcı Azure AD oturum açma URL 'sine yeniden yönlendirildiğinde, SSO eklentisi URL 'YI doğrular ve bu URL için SSO kimlik bilgilerini denetler. Kimlik bilgisini bulursa, SSO eklentisi uygulamayı Azure AD 'ye geçirir ve bu da uygulamanın, kullanıcıdan kimlik bilgilerini girmesini istemeden ağ isteğini tamamlamasını yetkilendirir. Ayrıca, cihazın Azure AD tarafından bilinirse, SSO eklentisi cihaz sertifikasını cihaz tabanlı koşullu erişim denetimini karşılamak üzere geçirir. 

MSAL olmayan uygulamalarda SSO 'yu desteklemek için, SSO eklentisi, [birincil yenileme belirteci nedir?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)bölümünde açıklanan Windows tarayıcı eklentisine benzer bir protokol uygular. 

MSAL tabanlı uygulamalarla karşılaştırıldığında, SSO eklentisi MSAL olmayan uygulamalar için daha saydam bir şekilde davranır. Uygulamaların sağladığı mevcut tarayıcı oturum açma deneyimiyle tümleştirilir. 

Son Kullanıcı tanıdık deneyimi görür ve her uygulamada yeniden oturum açmak zorunda değildir. Örneğin, yerel hesap seçiciyi göstermek yerine, SSO eklentisi Web tabanlı hesap seçici deneyimine SSO oturumları ekler. 

## <a name="next-steps"></a>Sonraki adımlar

[İOS cihazları Için paylaşılan cihaz modu](msal-ios-shared-devices.md)hakkında bilgi edinin.
