---
title: Self servis parola sıfırlama derin bakış-Azure Active Directory
description: Self servis parola sıfırlama nasıl çalışır?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fcac058a510b8b7fed5b3967bbbf439dd4c0f71
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786745"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Nasıl kullanılır: Azure AD self servis parola sıfırlama

Self servis parola sıfırlama (SSPR) nasıl çalışır? Bu seçenek arabirimde ne anlama geliyor? Azure Active Directory (Azure AD) SSPR hakkında daha fazla bilgi edinmek için okumaya devam edin.

## <a name="how-does-the-password-reset-portal-work"></a>Parola sıfırlama portalı nasıl çalışır?

Bir kullanıcı parola sıfırlama portalına gittiğinde şunları öğrenmek için bir iş akışı açılır:

   * Sayfa nasıl Yerelleştirilecek?
   * Kullanıcı hesabı geçerli mi?
   * Kullanıcı hangi kuruluşa ait?
   * Kullanıcının parolası nerede yönetilir?
   * Kullanıcı, özelliği kullanmak için lisanslı mı?

Parola sıfırlama sayfasının arkasındaki mantık hakkında bilgi edinmek için aşağıdaki adımları okuyun:

1. Kullanıcı, **Hesap bağlantıhesabınıza erişemez** veya doğrudan [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)gider.
   * Tarayıcı yerel ayarlarına bağlı olarak deneyim uygun dilde işlenir. Parola sıfırlama deneyimi, Office 365 'nin desteklediği dillere yereldir.
   * Parola sıfırlama portalını farklı bir yerelleştirilmiş dilde görüntülemek için, parola sıfırlama URL 'sinin sonuna, Ispanyolca [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)yerelleştirilmesi gereken örnekle "? Mkt =" ekleyin.
2. Kullanıcı bir kullanıcı KIMLIĞI girer ve bir CAPTCHA geçirir.
3. Azure AD, kullanıcının bu özelliği aşağıdaki denetimleri yaparak kullanabildiğini doğrular:
   * Kullanıcının bu özelliğin etkin olduğunu ve atanmış bir Azure AD lisansının olduğunu denetler.
     * Kullanıcıya bu özellik etkinleştirilmemişse veya atanmış bir lisans yoksa, kullanıcıdan parolasını sıfırlaması için yöneticisiyle iletişim kurabilmesi istenir.
   * Kullanıcının, yönetici ilkesine uygun olarak hesabında tanımlı doğru kimlik doğrulama yöntemlerine sahip olduğunu denetler.
     * İlke yalnızca bir yöntem gerektiriyorsa, kullanıcının yönetici ilkesi tarafından etkinleştirilen kimlik doğrulama yöntemlerinden en az biri için tanımlı uygun verileri olmasını sağlar.
       * Kimlik doğrulama yöntemleri yapılandırılmamışsa, kullanıcının parolasını sıfırlaması için yöneticisiyle iletişim kurabilmesi önerilir.
     * İlke iki yöntem gerektiriyorsa, kullanıcının yönetici ilkesi tarafından etkinleştirilen kimlik doğrulama yöntemlerinin en az ikisi için tanımlanmış uygun verileri olmasını sağlar.
       * Kimlik doğrulama yöntemleri yapılandırılmamışsa, kullanıcının parolasını sıfırlaması için yöneticisiyle iletişim kurabilmesi önerilir.
     * Kullanıcıya bir Azure yönetici rolü atanırsa, güçlü iki geçit parola ilkesi zorlanır. Bu ilkeyle ilgili daha fazla bilgi, [yönetici ilke farklılıklarını sıfırlama](concept-sspr-policy.md#administrator-reset-policy-differences)bölümünde bulunabilir.
   * Kullanıcının parolasının şirket içinde yönetilip yönetilmediğini (Federe, geçişli kimlik doğrulaması veya parola karması eşitlenmiş) görmek için denetler.
     * Geri yazma dağıtılırsa ve kullanıcının parolası şirket içinde yönetiliyorsa, kullanıcının kimlik doğrulamasına ve parolalarını sıfırlamasına izin verilir.
     * Geri yazma dağıtılırsa ve kullanıcının parolası şirket içinde yönetiliyorsa, kullanıcıdan parolasını sıfırlaması için yöneticisiyle iletişim kurabilmesi istenir.
4. Kullanıcının parolasını başarıyla sıfırladığını tespit ediyorsanız, Kullanıcı sıfırlama işlemini kolaylaştırır.

## <a name="authentication-methods"></a>Kimlik doğrulama yöntemleri

SSPR etkinse, kimlik doğrulama yöntemleri için aşağıdaki seçeneklerden en az birini seçmeniz gerekir. Bazen "kapıları" olarak adlandırılan bu seçenekleri duydunuz. Kullanıcılarınızın ihtiyaç duydukları bir birine erişememesi durumunda daha fazla esneklik sağlamak için **iki veya daha fazla kimlik doğrulama yöntemi seçmenizi** kesinlikle öneririz. Aşağıda listelenen yöntemlerle ilgili ek ayrıntılar, [kimlik doğrulama yöntemleri nelerdir?](concept-authentication-methods.md)makalesinde bulunabilir.

* Mobil uygulama bildirimi
* Mobil uygulama kodu
* E-posta
* Cep telefonu
* Ofis telefonu
* Güvenlik soruları

Kullanıcılar, yalnızca yöneticinin etkinleştirildiği kimlik doğrulama yöntemlerinde veriler varsa, parolalarını sıfırlayabilir.

> [!IMPORTANT]
> Mart 2019 ' den itibaren telefon araması seçenekleri ücretsiz/deneme Azure AD kiracılarında MFA ve SSPR kullanıcıları tarafından kullanılamaz. SMS iletileri bu değişiklikten etkilenmez. Telefon araması, ücretli Azure AD kiracılarındaki kullanıcılar için kullanılabilir olmaya devam edecektir. Bu değişiklik yalnızca ücretsiz/deneme Azure AD kiracılarını etkiler.

> [!WARNING]
> Azure yönetici rolleri atanan hesaplara, [yönetici ilke farklılıklarını sıfırlama](concept-sspr-policy.md#administrator-reset-policy-differences)bölümünde tanımlandığı şekilde yöntemler kullanılması gerekir.

![Azure portal, kimlik doğrulama yöntemleri seçimi][Authentication]

### <a name="number-of-authentication-methods-required"></a>Gereken kimlik doğrulama yöntemi sayısı

Bu seçenek, bir kullanıcının parolasını sıfırlamak veya kilidini açmak için geçmesi gereken en az kullanılabilir kimlik doğrulama yöntemleri veya kapıları sayısını belirler. Bir ya da iki olarak ayarlanabilir.

Yönetici bu kimlik doğrulama yöntemini etkinleştirmesine izin verirseniz, kullanıcılar daha fazla kimlik doğrulama yöntemi sağlamayı tercih edebilir.

Bir Kullanıcı en düşük gerekli yöntemlere kayıtlı değilse, bunları yöneticinin parolasını sıfırlamasına izin veren bir hata sayfası görürler.

#### <a name="mobile-app-and-sspr"></a>Mobil uygulama ve SSPR

Microsoft Authenticator uygulaması gibi bir mobil uygulama kullanırken, parola sıfırlama yöntemi olarak, aşağıdaki uyarıları bilmelisiniz:

* Yöneticiler bir parolayı sıfırlamak için bir yöntem kullanılmasını gerektirdiğinde, doğrulama kodu kullanılabilir tek seçenektir.
* Yöneticiler bir parolayı sıfırlamak için iki yöntem gerektirdiğinde, kullanıcılar herhangi bir etkin yönteme ek olarak bildirim **ya** **da** doğrulama kodunu kullanabilir.

| Sıfırlamak için gereken Yöntem sayısı | Biriyle | İki |
| :---: | :---: | :---: |
| Mobil uygulama özellikleri kullanılabilir | Kodlayın | Kod veya bildirim |

Kullanıcıların [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)' dan self servis parola sıfırlama kaydı sırasında mobil uygulamalarını kaydetme seçeneği yoktur. Kullanıcılar mobil uygulamalarını [https://aka.ms/mfasetup](https://aka.ms/mfasetup)'e veya [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)yeni güvenlik bilgileri kayıt önizlemeye kaydedebilir.

> [!WARNING]
> Kullanıcıların [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)yeni deneyimle erişebilmeleri için, [self servis parola sıfırlama ve Azure Multi-Factor Authentication (Genel Önizleme) için yakınsama kaydını](concept-registration-mfa-sspr-converged.md) etkinleştirmeniz gerekir.

> [!IMPORTANT]
> Kimlik doğrulayıcı uygulaması, bir 1-Gate ilkesi yapılandırılırken tek kimlik doğrulama yöntemi olarak seçilemez. Benzer şekilde, bir 2 kapıları ilkesi yapılandırılırken kimlik doğrulayıcı uygulaması ve yalnızca bir ek yöntem seçilemez.
> Daha sonra, bir yöntem olarak Authenticator uygulamasını içeren SSPR ilkelerini yapılandırırken, bir 1-Gate ilkesi yapılandırılırken en az bir ek yöntem seçilmelidir ve 2 kapıları ilkesi yapılandırılırken en az iki ek yöntem seçilmelidir.
> Bu gereksinimin nedeni, geçerli SSPR kayıt deneyiminin kimlik doğrulayıcı uygulamasını kaydetme seçeneğini içermediğinden oluşur. Kimlik doğrulayıcı uygulamasını kaydetme seçeneği, [self servis parola sıfırlama ve Azure Multi-Factor Authentication (Genel Önizleme) için yeni Yakınsanan kayda](concept-registration-mfa-sspr-converged.md)dahildir.
> Yalnızca Authenticator uygulamasını (1 kapı ilkeleri için) veya kimlik doğrulayıcı uygulamasını (2 kapıları ilkeleri için) kullanan ilkelere izin vermek, kullanıcıların, yeni bir tane kullanmak üzere yapılandırılıncaya kadar SSPR için kaydolmaları engellenmesine neden olabilir kayıt deneyimi.

### <a name="change-authentication-methods"></a>Kimlik doğrulama yöntemlerini Değiştir

Sıfırlama veya kilit açma için yalnızca bir gerekli kimlik doğrulama yöntemine sahip bir ilkeyle başlatırsanız ve bu, iki yönteme göre değişiklik yaparsanız, ne olur?

| Kayıtlı yöntemlerin sayısı | Gereken Yöntem sayısı | Sonuç |
| :---: | :---: | :---: |
| 1 veya daha fazla | 1 | **Sıfırlayabiliyor** veya kilit açıldı |
| 1 | 2 | Sıfırlanamıyor veya kilit **açılamıyor** |
| 2 veya daha fazla | 2 | **Sıfırlayabiliyor** veya kilit açıldı |

Bir kullanıcının kullanabileceği kimlik doğrulama yöntemlerinin türlerini değiştirirseniz, kullanıcıların kullanılabilir en az miktarda veri yoksa SSPR 'yi kullanmalarını istemeden durdurabilirsiniz.

Örnek:
1. Özgün ilke, gereken iki kimlik doğrulama yöntemi ile yapılandırılır. Yalnızca ofis telefon numarasını ve güvenlik sorularını kullanır. 
2. Yönetici, ilkeyi artık güvenlik sorularını kullanmayacak şekilde değiştirir, ancak cep telefonu ve alternatif e-posta kullanımına izin verir.
3. Mobil telefon veya alternatif e-posta alanları doldurulmuş kullanıcılar parolalarını sıfırlayamaz.

## <a name="registration"></a>Kayıt

### <a name="require-users-to-register-when-they-sign-in"></a>Kullanıcıların oturum açtıklarında kaydolmaları iste

Bu seçeneğin etkinleştirilmesi, kullanıcının Azure AD kullanan herhangi bir uygulamada oturum açtıklarında parola sıfırlama kaydını tamamlamasını gerektirir. Bu iş akışı aşağıdaki uygulamaları içerir:

* Office 365
* Azure portalı
* Erişim Paneli
* Federasyon uygulamaları
* Azure AD kullanarak özel uygulamalar

Kayıt gereksinimi devre dışı bırakıldığında, kullanıcılar el ile kaydobilirler. [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ziyaret edebilir veya erişim panelindeki **profil** sekmesinde **parola sıfırlama için kaydol** bağlantısını seçebilir.

> [!NOTE]
> Kullanıcılar, **iptal** ' i seçerek veya pencereyi kapatarak parola sıfırlama kayıt portalını kapatabilir. Ancak kaydolmaları tamamlanana kadar her oturum açtıklarında kaydolmaları istenir.
>
> Bu kesme, kullanıcının zaten oturum açmış olmaları durumunda bağlantısını kesmez.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Kullanıcılardan kimlik doğrulama bilgilerini yeniden onaylamasını istemeden önce geçen gün sayısını ayarlayın

Bu seçenek, kimlik doğrulama bilgilerini ayarlama ve yeniden onaylama arasındaki süreyi belirler ve yalnızca **kullanıcıların oturum açarken kaydolmasını gerektir** seçeneğini etkinleştirdiğinizde kullanılabilir.

Geçerli değerler 0 ile 730 gün, "0" anlamına gelir ve kullanıcılardan kimlik doğrulama bilgilerini yeniden onaylamasını hiçbir şekilde istenmez.

## <a name="notifications"></a>Bildirimler

### <a name="notify-users-on-password-resets"></a>Parola sıfırlamayı kullanıcılara bildir

Bu seçenek **Evet**olarak ayarlanırsa, parolalarını sıfırlayan kullanıcılar, parolasının değiştiğini bildiren bir e-posta alır. E-posta, SSPR portalı aracılığıyla Azure AD 'deki dosyada bulunan birincil ve alternatif e-posta adreslerine gönderilir. Sıfırlama olayının başka birine bildirim alınmaz.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Diğer yöneticiler parolalarını sıfırladıktan sonra tüm yöneticilere bildir

Bu seçenek **Evet**olarak ayarlanırsa, *Tüm Yöneticiler* Azure AD 'de dosya üzerinde birincil e-posta adresine bir e-posta alır. E-posta, SSPR kullanarak başka bir yöneticinin parolalarını değiştirdiğinizi bildirir.

Örnek: bir ortamda dört yönetici vardır. Yönetici A, SSPR kullanarak parolalarını sıfırlar. Yöneticiler B, C ve D bir e-posta göndererek parola sıfırlamasının uyarı almasını ister.

## <a name="on-premises-integration"></a>Şirket içi tümleştirme

Azure AD Connect yükler, yapılandırır ve etkinleştirirseniz, şirket içi Tümleştirmeler için aşağıdaki ek seçeneklere sahip olursunuz. Bu seçenekler gri ise, geri yazma düzgün şekilde yapılandırılmadı. Daha fazla bilgi için bkz. [parola geri yazmayı yapılandırma](howto-sspr-writeback.md).

![Parola geri yazma özelliğinin etkin ve çalışır olduğu doğrulanıyor][Writeback]

Bu sayfa, şirket içi geri yazma istemcisinin hızlı bir durumunu sağlar, geçerli yapılandırmaya göre aşağıdaki iletilerden biri görüntülenir:

* Şirket içi geri yazma istemciniz çalışıyor ve çalışıyor.
* Azure AD çevrimiçi ve şirket içi geri yazma istemcinizi bağlamış. Ancak, Azure AD Connect yüklü sürümü güncel değil gibi görünüyor. En son bağlantı özelliklerine ve önemli hata düzeltmelerine sahip olduğunuzdan emin olmak için [Azure AD Connect yükseltmeyi](../hybrid/how-to-upgrade-previous-version.md) düşünün.
* Ne yazık ki, Azure AD Connect yüklü sürümü güncel olmadığından şirket içi geri yazma istemci durumunuzu denetleyemiyorum. Bağlantı durumunuzu denetlemek için [Azure AD Connect yükseltin](../hybrid/how-to-upgrade-previous-version.md) .
* Ne yazık ki, şu anda şirket içi geri yazma istemcimize bağlanamıyoruz. Bağlantıyı geri yüklemek için [Azure AD Connect sorunlarını giderin](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) .
* Ne yazık ki, parola geri yazma düzgün yapılandırılmadığından şirket içi geri yazma istemcinizi bağlayamıyoruz. Bağlantıyı geri yüklemek için [parola geri yazma 'Yı yapılandırın](howto-sspr-writeback.md) .
* Ne yazık ki, şu anda şirket içi geri yazma istemcimize bağlanamıyoruz. Bu, bizim sonunda geçici sorunlardan kaynaklanıyor olabilir. Sorun devam ederse, bağlantıyı geri yüklemek için [Azure AD Connect sorun giderin](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) .

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Parolaları şirket içi dizininize geri yazma

Bu denetim, bu dizin için parola geri yazma özelliğinin etkinleştirilip etkinleştirilmediğini belirler. Geri yazma açık ise, şirket içi geri yazma hizmetinin durumunu gösterir. Azure AD Connect yeniden yapılandırmak zorunda kalmadan parola geri yazmayı geçici olarak devre dışı bırakmak istiyorsanız bu denetim yararlı olur.

* Anahtar **Evet**olarak ayarlanırsa, geri yazma etkinleştirilir, Federasyon, geçişli kimlik doğrulama veya parola karması eşitlenmiş kullanıcılar parolalarını sıfırlayabiliyor.
* Anahtar **Hayır**olarak ayarlanırsa, geri yazma devre dışı bırakılır, Federasyon, geçişli kimlik doğrulama veya parola karması eşitlenmiş kullanıcılar parolalarını sıfırlayamaz.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Kullanıcıların, parolasını sıfırlamadan hesapların kilidini açmalarına izin ver

Bu denetim, parola sıfırlama portalını ziyaret eden kullanıcılara parolasını sıfırlamak zorunda kalmadan şirket içi Active Directory hesaplarının kilidini açma seçeneği verilmesini belirler. Varsayılan olarak, Azure AD, parola sıfırlama gerçekleştirdiğinde hesapların kilidini açar. Bu iki işlemi ayırmak için bu ayarı kullanabilirsiniz.

* **Evet**olarak ayarlanırsa, kullanıcılara parolasını sıfırlama ve hesabın kilidini açma veya parolayı sıfırlamak zorunda kalmadan hesabının kilidini açma seçeneği verilir.
* **Hayır**olarak ayarlanırsa, kullanıcılar yalnızca bir birleştirilmiş parola sıfırlama ve hesap kilit açma işlemi gerçekleştirebilir.

### <a name="on-premises-active-directory-password-filters"></a>Şirket içi Active Directory parola filtreleri

Azure AD self servis parola sıfırlama, Active Directory ' de yönetici tarafından başlatılan parola sıfırlamasının eşdeğerini uygular. Özel parola kurallarını zorlamak için bir üçüncü taraf parola filtresi kullanıyorsanız ve bu parola filtresinin Azure AD self servis parola sıfırlama sırasında denetlenmesi gerekiyorsa, üçüncü taraf parola filtresi çözümünün şu şekilde yapılandırıldığından emin olun Yönetici parolası sıfırlama senaryosu. [Windows Server Active Directory Için Azure AD parola koruması](concept-password-ban-bad-on-premises.md) varsayılan olarak desteklenir.

## <a name="password-reset-for-b2b-users"></a>B2B kullanıcıları için parola sıfırlama

Parola sıfırlama ve değiştirme, tüm işletmeler arası (B2B) yapılandırmalarda tam olarak desteklenmektedir. B2B Kullanıcı parolası sıfırlama, aşağıdaki üç durumda desteklenir:

* **Mevcut bir Azure AD kiracısına sahip bir iş ortağı kuruluştan kullanıcılar**: BIR Azure AD kiracısında ortaklığı varsa, *Bu kiracıda hangi parola sıfırlama ilkelerinin etkin olduğunu dikkate*aldık. Parola sıfırlamanın çalışması için, iş ortağı kuruluşun yalnızca Azure AD SSPR 'nin etkinleştirildiğinden emin olması gerekir. Office 365 müşterileri için ek ücret alınmaz ve [parola yönetimi ile çalışmaya başlama](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) Kılavuzu ' nda bulunan adımları izleyerek etkinleştirilebilir.
* Self Servis kaydolma **ile kaydolan kullanıcılar** : bir kiracıya ulaşmak üzere [self servis kaydolma](../users-groups-roles/directory-self-service-signup.md) özelliğini kullandıysanız, kendilerine kaydoldukları e-posta ile parolayı sıfırlayacağız.
* **B2B kullanıcıları**: yenı [Azure AD B2B özellikleri](../active-directory-b2b-what-is-azure-ad-b2b.md) KULLANıLARAK oluşturulan tüm yeni B2B kullanıcıları, parolaları, davet işlemi sırasında kayıtlı olan e-postalarla sıfırlayabilecektir.

Bu senaryoyu test etmek için, bu iş ortağı kullanıcılarından birine sahip https://passwordreset.microsoftonline.com gidin. Alternatif bir e-posta veya kimlik doğrulama e-postası tanımlanmışsa, parola sıfırlama beklendiği gibi çalışmaktadır.

> [!NOTE]
> Hotmail.com, Outlook.com veya diğer kişisel e-posta adreslerinden gelen Azure AD kiracınıza konuk erişimi verilen Microsoft hesapları Azure AD SSPR 'yi kullanamaz. Microsoft hesabı makalemize [oturum açabilmeniz](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) için içinde bulunan bilgileri kullanarak parolalarını sıfırlamaları gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makaleler, Azure AD aracılığıyla parola sıfırlama konusunda ek bilgiler sağlar:

* [SSPR’yi başarılı bir şekilde nasıl piyasaya çıkarabilirim?](howto-sspr-deployment.md)
* [Parolanızı sıfırlama veya değiştirme](../user-help/active-directory-passwords-update-your-own-password.md)
* [Self servis parola sıfırlama için kaydolma](../user-help/active-directory-passwords-reset-register.md)
* [Lisansla ilgili bir sorunuz mu var?](concept-sspr-licensing.md)
* [SSPR hangi verileri kullanır ve kullanıcılarınız için hangi verileri doldurmanız gerekir?](howto-sspr-authenticationdata.md)
* [Kullanıcılar hangi kimlik doğrulama yöntemlerini kullanabilir?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR ile kullanılabilen ilke seçenekleri nelerdir?](concept-sspr-policy.md)
* [Parola geri yazma nedir ve neden önemlidir?](howto-sspr-writeback.md)
* [SSPR’de etkinliği nasıl bildirebilirim?](howto-sspr-reporting.md)
* [SSPR’deki tüm seçenekler nelerdir ve ne anlama gelir?](concept-sspr-howitworks.md)
* [Bir şeyin bozuk olduğunu düşünüyorum. SSPR sorunlarını gidermek Nasıl yaparım??](active-directory-passwords-troubleshoot.md)
* [Başka bir yerde ele alınmayan bir sorum var](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Kullanılabilir Azure AD kimlik doğrulama yöntemleri ve gereken miktar"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Şirket içi tümleştirme parolası geri yazma yapılandırması ve sorun giderme bilgileri"
