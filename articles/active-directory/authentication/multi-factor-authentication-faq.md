---
title: Azure AD Multi-Factor Authentication SSS-Azure Active Directory
description: Azure AD Multi-Factor Authentication ile ilgili sık sorulan sorular ve yanıtlar.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58fbf874339bf287ce1af62e1edb39ec6227d99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257111"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication hakkında sık sorulan sorular

Bu SSS, Azure AD Multi-Factor Authentication ve Multi-Factor Authentication hizmetini kullanarak ilgili sık sorulan soruları yanıtlar. Genel, faturalandırma modelleri, kullanıcı deneyimleri ve sorun giderme hizmeti hakkındaki sorulara bölünmüştür.

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sunmaz. Kullanıcılardan Multi-Factor Authentication istemek isteyen yeni müşteriler bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır. MFA sunucusunu 1 Temmuz 'dan önce etkinleştiren mevcut müşteriler, en son sürümü ve gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.
>
> Azure Multi-Factor Authentication Sunucusu ile ilgili olarak aşağıda paylaşılan bilgiler yalnızca MFA sunucusuna zaten çalışan kullanıcılar için geçerlidir.
>
> Tüketim tabanlı lisanslama, 1 Eylül 2018 ' de geçerli olan yeni müşteriler için artık kullanılamaz.
> 1 Eylül 2018 ' den itibaren yeni kimlik doğrulama sağlayıcıları artık oluşturulmayabilir. Mevcut kimlik doğrulama sağlayıcıları çalışmaya ve güncelleştirilmesine devam edebilir. Multi-Factor Authentication, Azure AD Premium lisanlarında kullanılabilir bir özellik olmaya devam edecektir.

## <a name="general"></a>Genel

* [Azure Multi-Factor Authentication Sunucusu Kullanıcı verilerini nasıl işler?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Kullanıcılarıma SMS iletileri göndermek için hangi SMS kısa kodları kullanılır?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Azure Multi-Factor Authentication Sunucusu Kullanıcı verilerini nasıl işler?

Multi-Factor Authentication Sunucusu, Kullanıcı verileri yalnızca şirket içi sunucularda depolanır. Kalıcı kullanıcı verileri bulutta depolanmaz. Kullanıcı iki adımlı doğrulama gerçekleştirdiğinde, Multi-Factor Authentication Sunucusu kimlik doğrulaması için Azure AD Multi-Factor Authentication bulut hizmetine veri gönderir. Multi-Factor Authentication Sunucusu ile Multi-Factor Authentication bulut hizmeti arasındaki iletişim 443 giden bağlantı noktası üzerinden Güvenli Yuva Katmanı (SSL) veya Aktarım Katmanı Güvenliği (TLS) kullanır.

Bulut hizmetine kimlik doğrulama istekleri gönderildiğinde, veriler kimlik doğrulama ve kullanım raporları için toplanır. Aşağıdaki veri alanları iki adımlı doğrulama günlüklerine dahildir:

* **BENZERSIZ kimlik** (Kullanıcı adı ya da şirket ıçı Multi-Factor Authentication sunucusu kimliği)
* **First ve Last adı** (isteğe bağlı)
* **E-posta adresi** (isteğe bağlı)
* **Telefon numarası** (bir sesli arama veya SMS kimlik doğrulaması kullanırken)
* **Cihaz belirteci** (mobil uygulama kimlik doğrulaması kullanılırken)
* **Kimlik doğrulama modu**
* **Kimlik doğrulama sonucu**
* **Multi-Factor Authentication Sunucusu adı**
* **Multi-Factor Authentication Sunucusu IP**
* **İstemci IP 'si** (varsa)

İsteğe bağlı alanlar Multi-Factor Authentication Sunucusu yapılandırılabilir.

Doğrulama sonucu (başarı veya reddetme) ve reddedilme nedeni kimlik doğrulama verileriyle birlikte depolanır. Bu veriler, kimlik doğrulama ve kullanım raporlarında kullanılabilir.

Daha fazla bilgi için bkz. [Azure AD Multi-Factor Authentication Için veri yerleşimi ve müşteri verileri](concept-mfa-data-residency.md).

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Kullanıcılarıma SMS iletileri göndermek için hangi SMS kısa kodları kullanılır?

Birleşik Devletler, aşağıdaki SMS kısa kodlarını kullanırız:

* *97671*
* *69829*
* *51789*
* *99399*

Kanada 'da aşağıdaki SMS kısa kodlarını kullanırız:

* *759731*
* *673801*

Aynı numarayla tutarlı SMS veya ses tabanlı Multi-Factor Authentication istemi teslimi garantisi yoktur. Kullanıcılarımıza ilişkin olarak, SMS teslimat yeteneğini geliştirmek üzere rota ayarlamaları yaptığımız için istediğiniz zaman kısa kodlar ekleyebilir veya kaldırabiliriz.

Birleşik Devletler ve Kanada yanı sıra ülkeler ve bölgeler için kısa kodları desteklemiyoruz.

## <a name="billing"></a>Faturalandırma

Çoğu faturalandırma sorusu, [Multi-Factor Authentication fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ya da [Azure AD Multi-Factor Authentication sürümleri ve tüketim planlarına](concept-mfa-licensing.md)yönelik belgelere başvurarak yanıtlanır.

* [Kuruluşum, kimlik doğrulaması için kullanılan telefon çağrılarını ve SMS iletilerini göndermek için ücretlendiriyor mu?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Kullanıcı başına faturalandırma modeli, etkin olan tüm kullanıcılar için mi, yoksa yalnızca iki adımlı doğrulama gerçekleştirenler için mi ücretlendirilir?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Multi-Factor Authentication Faturalandırma nasıl çalışır?](#how-does-multi-factor-authentication-billing-work)
* [Azure AD Multi-Factor Authentication 'nin ücretsiz bir sürümü var mı?](#is-there-a-free-version-of-azure-ad-multi-factor-authentication)
* [Kuruluşum, her zaman Kullanıcı başına ve kimlik doğrulaması başına tüketim faturalama modelleri arasında geçiş yapabilir mi?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Kuruluşum, tüketim tabanlı faturalandırma ve abonelikler arasında (lisans tabanlı bir model) istediğiniz zaman geçiş yapabilir mi?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Kuruluşumun Azure AD Multi-Factor Authentication kullanmak için kimlikleri kullanması ve eşitlenmesi gerekiyor mu?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Kuruluşum, kimlik doğrulaması için kullanılan telefon çağrılarını ve SMS iletilerini göndermek için ücretlendiriyor mu?

Hayır, Azure AD Multi-Factor Authentication aracılığıyla kullanıcılara gönderilen tek tek telefon aramaları veya SMS iletileri için ücret ödersiniz. Kimlik doğrulaması başına MFA sağlayıcısı kullanıyorsanız, her kimlik doğrulaması için faturalandırılırsınız, ancak kullanılan yöntem için bu değil.

Kullanıcılarınız, kendi kişisel telefon hizmetine göre aldıkları telefon aramaları veya mesaj iletileri için ücretlendirilir.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Kullanıcı başına faturalandırma modeli, etkin olan tüm kullanıcılar için mi, yoksa yalnızca iki adımlı doğrulama gerçekleştirenler için mi ücretlendirilir?

Faturalandırma, ayda iki adımlı doğrulama gerçekleştirdiklerinden bağımsız olarak Multi-Factor Authentication kullanmak üzere yapılandırılan kullanıcı sayısını temel alır.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Multi-Factor Authentication Faturalandırma nasıl çalışır?

Kullanıcı başına veya kimlik doğrulaması başına MFA sağlayıcısı oluşturduğunuzda, kuruluşunuzun Azure aboneliği kullanım için aylık olarak faturalandırılır. Bu Faturalandırma modeli, Azure 'un sanal makineler ve Web Apps kullanımı ile benzerdir.

Azure AD Multi-Factor Authentication için bir abonelik satın aldığınızda, kuruluşunuz yalnızca her bir kullanıcı için yıllık lisans ücretini ödersiniz. MFA lisansları ve Microsoft 365, Azure AD Premium veya Enterprise Mobility + Security paketleri bu şekilde faturalandırılır.

Daha fazla bilgi için bkz. [Azure AD Multi-Factor Authentication alma](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication 'nin ücretsiz bir sürümü var mı?

Güvenlik Varsayılanları Azure AD Ücretsiz katmanında etkinleştirilebilir. Güvenlik varsayılanları ile, tüm kullanıcılar Microsoft Authenticator uygulamasını kullanarak çok faktörlü kimlik doğrulaması için etkinleştirilir. Yalnızca Microsoft Authenticator uygulaması olan güvenlik varsayılanlarını kullanarak kısa mesaj veya telefon doğrulaması kullanabilme özelliği yoktur.

Daha fazla bilgi için bkz. [güvenlik Varsayılanları nelerdir?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Kuruluşum, her zaman Kullanıcı başına ve kimlik doğrulaması başına tüketim faturalama modelleri arasında geçiş yapabilir mi?

Kuruluşunuz, tüketim tabanlı faturalandırma ile tek başına bir hizmet olarak MFA satın alıyorsa, MFA sağlayıcısı oluştururken bir faturalandırma modeli seçersiniz. MFA sağlayıcısı oluşturulduktan sonra faturalandırma modelini değiştiremezsiniz. 

MFA sağlayıcınız bir Azure AD kiracısına bağlı *değilse* veya yeni MFA sağlayıcısını farklı bır Azure AD kiracısına bağlarsanız, Kullanıcı ayarları ve yapılandırma seçenekleri aktarılmaz. Ayrıca, yeni MFA Sağlayıcısı ile oluşturulan etkinleştirme kimlik bilgileri kullanılarak mevcut Azure MFA Sunucularının yeniden etkinleştirilmesi gerekir. MFA Sunucularını yeni MFA Sağlayıcısına bağlamak için yeniden etkinleştirmek, telefon çağrısı ve kısa mesaj kimlik doğrulamasını etkilemez, ancak mobil uygulama etkinleştirilinceye kadar tüm kullanıcılar için mobil uygulama bildirimleri çalışmaz.

[Azure Multi-Factor auth sağlayıcısı ile çalışmaya](concept-mfa-authprovider.md)başlama bölümünde MFA sağlayıcıları hakkında daha fazla bilgi edinin.

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Kuruluşum, tüketim tabanlı faturalandırma ve abonelikler arasında (lisans tabanlı bir model) istediğiniz zaman geçiş yapabilir mi?

Bazı örneklerde, evet.

Dizininizde *Kullanıcı başına* Azure Multi-Factor Authentication sağlayıcısı varsa MFA lisansları ekleyebilirsiniz. Lisansa sahip kullanıcılar, Kullanıcı başına tüketim tabanlı faturalandırma tarafından sayılmaz. Lisansları olmayan kullanıcılar MFA sağlayıcısı aracılığıyla MFA için de etkinleştirilebilir. Multi-Factor Authentication kullanmak üzere yapılandırılmış tüm kullanıcılarınız için lisans satın alıp atarsanız, Azure Multi-Factor Authentication sağlayıcısını silebilirsiniz. Gelecekte lisanslarından daha fazla kullanıcınız varsa, her zaman bir Kullanıcı başına MFA sağlayıcısı oluşturabilirsiniz.

Dizininizde bir *kimlik doğrulaması başına* Azure Multi-Factor Authentication sağlayıcısı varsa, MFA sağlayıcısı aboneliğinize bağlı olduğu sürece her bir kimlik doğrulaması için her zaman faturalandırılırsınız. Kullanıcılara MFA lisansları atayabilirsiniz, ancak her iki adımlı doğrulama isteği için faturalandırılırsınız ve bu, bir MFA lisansı atanmış birisinden gelir.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication"></a>Kuruluşumun Azure AD Multi-Factor Authentication kullanmak için kimlikleri kullanması ve eşitlenmesi gerekiyor mu?

Kuruluşunuz tüketim tabanlı bir faturalandırma modeli kullanıyorsa, Azure Active Directory isteğe bağlıdır, ancak gerekli değildir. MFA sağlayıcınız bir Azure AD kiracısına bağlı değilse, yalnızca şirket içi Azure Multi-Factor Authentication Sunucusu dağıtabilirsiniz.

Lisans modeli için Azure Active Directory gereklidir çünkü bu lisansları, satın alırken ve dizindeki kullanıcılara atadığınızda Azure AD kiracısına eklenir.

## <a name="manage-and-support-user-accounts"></a>Kullanıcı hesaplarını yönetme ve destekleme

* [Kullanıcılarım telefonlarına yanıt almadıklarında bana ne yapmam gerekir?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Kullanıcılarım hesabında yoksa ne yapmam gerekir?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Kullanıcılarımın biri uygulama parolalarını kullanan bir telefonu kaybedersem ne yapmam gerekir?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Kullanıcı tarayıcı olmayan uygulamalarda oturum açamaz ne olursa?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Kullanıcılarım bazen kısa mesaj almadıklarında veya doğrulama zaman aşımına uğrar.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Sistem zaman aşımına uğramadan önce kullanıcılarınızın bir SMS iletisinden doğrulama kodunu girmesi gereken süre miktarını değiştirebilir miyim?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Azure Multi-Factor Authentication Sunucusu ile donanım belirteçlerini kullanabilir miyim?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Terminal hizmetlerini güvenli hale getirmek için Azure Multi-Factor Authentication Sunucusu kullanabilir miyim?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [MFA sunucusunda arayan KIMLIĞINI yapılandırdım, ancak Kullanıcılarım anonim bir arayandan Multi-Factor Authentication çağrılar alıyor.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Kullanıcılardan neden güvenlik bilgilerini kaydetmesi istenir?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Kullanıcılarım telefonlarına yanıt almadıklarında bana ne yapmam gerekir?

Kullanıcılarınızın kimlik doğrulaması için telefon araması veya SMS almak için 5 dakika içinde en fazla beş kez deneme süresi vardır. Microsoft, çağrılar ve SMS iletileri göndermek için birden çok sağlayıcı kullanır. Bu yaklaşım işe yaramazsa, daha fazla sorun gidermek için bir destek talebi açın.

Üçüncü taraf güvenlik uygulamaları, doğrulama kodu metin iletisini veya telefon aramasını de engelleyebilir. Üçüncü taraf güvenlik uygulaması kullanıyorsanız, korumayı devre dışı bırakıp daha sonra başka bir MFA doğrulama kodu gönderilmesini isteyin.

Yukarıdaki adımlar çalışmazsa, kullanıcıların birden fazla doğrulama yöntemi için yapılandırılıp yapılandırılmadığını denetleyin. Yeniden oturum açmayı deneyin, ancak oturum açma sayfasında farklı bir doğrulama yöntemi seçin.

Daha fazla bilgi için bkz. [Son Kullanıcı sorun giderme kılavuzu](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Kullanıcılarım hesabında yoksa ne yapmam gerekir?

Kullanıcının hesabını, kayıt işlemini tekrar ilerleyerek sıfırlayabilirsiniz. [Bulutta Azure AD Multi-Factor Authentication ile Kullanıcı ve cihaz ayarlarını yönetme](howto-mfa-userdevicesettings.md)hakkında daha fazla bilgi edinin.

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Kullanıcılarımın biri uygulama parolalarını kullanan bir telefonu kaybedersem ne yapmam gerekir?

Yetkisiz erişimi engellemek için kullanıcının tüm uygulama parolalarını silin. Kullanıcı bir değiştirme cihazına sahip olduktan sonra parolaları yeniden oluşturabilir. [Bulutta Azure AD Multi-Factor Authentication ile Kullanıcı ve cihaz ayarlarını yönetme](howto-mfa-userdevicesettings.md)hakkında daha fazla bilgi edinin.

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Kullanıcı tarayıcı olmayan uygulamalarda oturum açamaz ne olursa?

Kuruluşunuz hala eski istemcileri kullanıyorsa ve [uygulama parolalarının kullanımına izin](howto-mfa-app-passwords.md)verildiyse, kullanıcılarınız Kullanıcı adı ve parolasıyla bu eski istemcilerde oturum açamaz. Bunun yerine, [uygulama parolalarını ayarlamaları](../user-help/multi-factor-authentication-end-user-app-passwords.md)gerekir. Kullanıcılarınızın kendi oturum açma bilgilerini temizleme (silme), uygulamayı yeniden başlatması ve sonra kendi Kullanıcı adı ve *uygulama parolalarıyla* normal parolaları yerine oturum açması gerekir.

Kuruluşunuzda eski istemciler yoksa, kullanıcılarınızın uygulama parolaları oluşturmalarına izin vermemelisiniz.

> [!NOTE]
> **Office 2013 istemcileri için modern kimlik doğrulaması**
>
> Uygulama parolaları yalnızca modern kimlik doğrulamayı desteklemeyen uygulamalar için gereklidir. Office 2013 istemcileri modern kimlik doğrulama protokollerini destekler, ancak yapılandırılması gerekir. Modern kimlik doğrulaması, Office 2013 için Mart 2015 veya sonraki bir güncelleştirmeyi çalıştıran tüm müşteriler tarafından kullanılabilir. Daha fazla bilgi için bkz. [Office 365 modern kimlik doğrulaması güncelleştirilmiş](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)Web günlüğü.

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Kullanıcılarım bazen kısa mesaj almadıklarında veya doğrulama zaman aşımına uğrar.

Hizmetin güvenilirliğini etkileyebilecek denetlenebilir faktörlerin bulunduğu için SMS iletilerinin teslimi garanti edilmez. Bu etkenlere, hedef ülke veya bölge, cep telefonu taşıyıcısı ve sinyal gücü dahildir.

Üçüncü taraf güvenlik uygulamaları, doğrulama kodu metin iletisini veya telefon aramasını de engelleyebilir. Üçüncü taraf güvenlik uygulaması kullanıyorsanız, korumayı devre dışı bırakıp daha sonra başka bir MFA doğrulama kodu gönderilmesini isteyin.

Kullanıcılarınız genellikle SMS mesajları güvenilir bir şekilde alırken sorun yaşıyorsanız, bunun yerine Microsoft Authenticator uygulama veya telefon araması yöntemini kullanmasını söyleyin. Microsoft Authenticator, her ikisi de hücresel ve Wi-Fi bağlantı üzerinden bildirim alabilir. Ayrıca, mobil uygulama, cihazın hiç sinyali olmadığında bile doğrulama kodları oluşturabilir. Microsoft Authenticator uygulaması [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) ve [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)'da kullanılabilir.

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Sistem zaman aşımına uğramadan önce kullanıcılarınızın bir SMS iletisinden doğrulama kodunu girmesi gereken süre miktarını değiştirebilir miyim?

Bazı durumlarda, evet.

Azure MFA Server v 7.0 veya üzeri bir tek yönlü SMS için bir kayıt defteri anahtarı ayarlayarak zaman aşımı ayarını yapılandırabilirsiniz. MFA bulut hizmeti SMS iletisini gönderdikten sonra, MFA sunucusuna doğrulama kodu (veya bir kerelik geçiş kodu) döndürülür. MFA sunucusu, kodu varsayılan olarak 300 saniye için belleğe depolar. Kullanıcı, 300 saniye geçtikten sonra kodu girmezse, kimlik doğrulaması reddedilir. Varsayılan zaman aşımı ayarını değiştirmek için bu adımları kullanın:

1. `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor` öğesine gidin.
2. *Pfsvc_pendingSmsTimeoutSeconds* adlı bir **DWORD** kayıt defteri anahtarı oluşturun ve Azure MFA sunucusunun bir kerelik geçiş kodlarını depolamasını istediğiniz süreyi saniye cinsinden ayarlayın.

>[!TIP]
>
> Birden çok MFA sunucunuz varsa, yalnızca özgün kimlik doğrulama isteğini işleyen bir Kullanıcı, kullanıcıya gönderilen doğrulama kodunu bilir. Kullanıcı koda girdiğinde, doğrulama isteğinin aynı sunucuya gönderilmesi gerekir. Kod doğrulaması farklı bir sunucuya gönderilirse, kimlik doğrulaması reddedilir.

Kullanıcılar, tanımlanan zaman aşımı süresi içinde SMS 'ye yanıt vermezse, kimlik doğrulaması reddedilir.

Bulutta Azure AD MFA ile tek yönlü bir SMS (AD FS bağdaştırıcısı veya ağ Ilkesi sunucu uzantısı dahil) için zaman aşımı ayarını yapılandıramazsınız. Azure AD, 180 saniyelik doğrulama kodunu depolar.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Sunucusu ile donanım belirteçlerini kullanabilir miyim?

Azure Multi-Factor Authentication Sunucusu kullanıyorsanız, üçüncü taraf açık kimlik doğrulaması (OATH) zaman tabanlı, bir kerelik parola (TOTP) belirteçlerini içeri aktarabilir ve ardından bunları iki adımlı doğrulama için kullanabilirsiniz.

Gizli anahtarı bir CSV dosyasına yerleştirip Azure Multi-Factor Authentication Sunucusu içeri aktarırsanız, OATH TOTP belirteçleri olan Activeıdentity belirteçlerini kullanabilirsiniz. İstemci sistem kullanıcı girişini kabul edebileceği sürece Active Directory Federasyon Hizmetleri (AD FS) (ADFS), Internet Information Server (IIS) form tabanlı kimlik doğrulaması ve Arayan Kullanıcının Uzaktan Kimliğini Doğrulama Hizmeti (RADIUS) ile OATH belirteçleri kullanabilirsiniz.

Üçüncü taraf OATH TOTP belirteçlerini aşağıdaki biçimleriyle içeri aktarabilirsiniz:  

- Taşınabilir simetrik anahtar kapsayıcısı (PSKC)
- Dosya bir seri numarası, temel 32 biçiminde bir gizli anahtar ve bir zaman aralığı içeriyorsa CSV

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Terminal hizmetlerini güvenli hale getirmek için Azure Multi-Factor Authentication Sunucusu kullanabilir miyim?

Evet, ancak Windows Server 2012 R2 veya sonraki bir sürümünü kullanıyorsanız, Terminal Hizmetleri 'ni yalnızca Uzak Masaüstü Ağ Geçidi (RD Ağ Geçidi) kullanarak güvenli hale getirebilirsiniz.

Windows Server 2012 R2 'deki güvenlik değişiklikleri, Azure Multi-Factor Authentication Sunucusu 'nin Windows Server 2012 ve önceki sürümlerde yerel güvenlik yetkilisi (LSA) Güvenlik paketine bağlanmasını değiştirdi. Windows Server 2012 veya önceki sürümlerde Terminal Hizmetleri sürümleri için [bir uygulamayı Windows kimlik doğrulaması ile güvenli hale](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)getirebilirsiniz. Windows Server 2012 R2 kullanıyorsanız, RD Ağ Geçidi gerekir.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>MFA sunucusunda arayan KIMLIĞINI yapılandırdım, ancak Kullanıcılarım anonim bir arayandan Multi-Factor Authentication çağrılar alıyor.

Multi-Factor Authentication çağrıları genel telefon ağı üzerinden yerleştirildiğinde, bazen arayan KIMLIĞINI desteklemeyen bir taşıyıcı aracılığıyla yönlendirilir. Bu taşıyıcı davranışı nedeniyle, Multi-Factor Authentication sistemi her zaman tarafından gönderilse de arayan KIMLIĞI garanti edilmez.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Kullanıcılardan neden güvenlik bilgilerini kaydetmesi istenir?

Kullanıcılardan güvenlik bilgilerini kaydetmesi istenmesinin çeşitli nedenleri vardır:

- Kullanıcı, Azure AD 'de yöneticileri tarafından MFA için etkinleştirildi, ancak henüz hesabı için güvenlik bilgilerine kaydolmadı.
- Kullanıcı, Azure AD 'de self servis parola sıfırlama için etkinleştirildi. Güvenlik bilgileri, daha sonra unutmaları durumunda parolalarını sıfırlamasına yardımcı olur.
- Kullanıcı MFA 'yı zorunlu kılmak için koşullu erişim ilkesine sahip bir uygulamaya erişti ve daha önce MFA için kaydedilmemiş.
- Kullanıcı Azure AD 'ye (Azure AD katılımı dahil) sahip bir cihaz kaydediyor ve kuruluşunuz cihaz kaydı için MFA gerektiriyor, ancak Kullanıcı daha önce MFA 'ya kaydolmadı.
- Kullanıcı Windows 10 ' da (MFA gerektirir) Iş için Windows Hello 'Yu oluşturuyor ve daha önce MFA için kaydedilmemiş.
- Kuruluş, kullanıcıya uygulanan bir MFA kayıt ilkesi oluşturup etkinleştirdi.
- Kullanıcı MFA için önceden kaydoldu, ancak yöneticinin devre dışı bırakılmasından sonra bir doğrulama yöntemi seçti. Bu nedenle, yeni bir varsayılan doğrulama yöntemi seçmek için kullanıcının MFA kaydını yeniden geçmesi gerekir.

## <a name="errors"></a>Hatalar

* [Mobil uygulama bildirimleri kullanılırken kullanıcıların "etkinleştirilmiş bir hesap için kimlik doğrulama isteği değildir" hata iletisiyle gördükleri ne yapması gerekir?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Tarayıcı olmayan bir uygulamada oturum açarken kullanıcılara bir 0x800434D4L hata iletisi görürseniz ne yapması gerekir?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Mobil uygulama bildirimleri kullanılırken kullanıcıların "etkinleştirilmiş bir hesap için kimlik doğrulama isteği değildir" hata iletisiyle gördükleri ne yapması gerekir?

Kullanıcıdan Microsoft Authenticator hesaplarını kaldırmak için aşağıdaki yordamı tamamlamasını isteyin, sonra yeniden ekleyin:

1. [Azure Portal profiline](https://account.activedirectory.windowsazure.com/profile/) gidin ve bir kuruluş hesabıyla oturum açın.
2. **Ek güvenlik doğrulaması**' nı seçin.
3. Microsoft Authenticator uygulamadan mevcut hesabı kaldırın.
4. **Yapılandır**' a tıklayın ve ardından Microsoft Authenticator yeniden yapılandırmak için yönergeleri izleyin.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Tarayıcı olmayan bir uygulamada oturum açarken kullanıcılara bir 0x800434D4L hata iletisi görürseniz ne yapması gerekir?

Bir yerel bilgisayara yüklenmiş ve iki adımlı doğrulama gerektiren hesaplarla çalışmayan bir tarayıcı olmayan uygulamada oturum açmaya çalıştığınızda *0X800434d4l* hatası oluşur.

Bu hata için geçici bir çözüm, yönetici ile ilgili ve yönetici olmayan işlemler için ayrı kullanıcı hesaplarına sahip olmalarıdır. Daha sonra, yönetici olmayan hesabınızı kullanarak Outlook 'ta oturum açabilmeniz için yönetici hesabınız ve yönetici olmayan hesap arasında posta kutuları bağlayabilirsiniz. Bu çözüm hakkında daha fazla ayrıntı için, yöneticiye bir [kullanıcının posta kutusunun içeriğini açma ve görüntüleme yeteneği verme](https://help.outlook.com/141/gg709759.aspx?sl=1)hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Sorunuz burada yanıtlanmazsa aşağıdaki destek seçenekleri mevcuttur:

* [Microsoft desteği bilgi bankasında](https://support.microsoft.com) , yaygın teknik sorunların çözümlerini arayın.
* Topluluğlardan Teknik sorular ve yanıtlar arayın ve bunları inceleyin veya [Azure Active Directory soru-cevap&bir](/answers/topics/azure-active-directory.html)soru sorun.
* [Azure Multi-Factor Authentication sunucusu desteği](https://support.microsoft.com/oas/default.aspx?prid=14947)aracılığıyla Microsoft Professional ile iletişim kurun. Bizimle iletişim kurarken, sorun hakkında mümkün olduğunca fazla bilgi dahil edebiliyorsanız bu yararlı olur. Sağlayabileceğiniz bilgiler, hatayı gördüğünüz sayfayı, belirli hata kodunu, belirli oturum KIMLIĞINI ve hatayı seçen kullanıcının KIMLIĞINI içerir.
* Eski bir PhoneFactor müşterisiyseniz ve sorularınız varsa veya bir parolayı sıfırlamayla ilgili yardıma ihtiyacınız varsa, [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) bir destek talebi açmak için e-posta adresini kullanın.
