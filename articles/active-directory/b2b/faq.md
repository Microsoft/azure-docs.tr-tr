---
title: B2B işbirliği SSS-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B işbirliği hakkında sık sorulan soruların yanıtlarını alın.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 11/07/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd72aea9087b03dcd5c6072676e8f98e7cfc1ee
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816458"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B işbirliği SSS

Azure Active Directory (Azure AD) işletmeden işletmeye (B2B) işbirliğiyle ilgili sık sorulan sorular (SSS), yeni konuları içerecek şekilde düzenli olarak güncelleştirilir.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>, B2B işbirliği Konuk kullanıcılarımız için daha sezgisel olması için oturum açma sayfamızı özelleştirebiliriz.
Kesinlikle! [Bu özellik hakkında blog gönderimize](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)bakın. Kuruluşunuzun oturum açma sayfasını özelleştirme hakkında daha fazla bilgi için bkz. [oturum açma ve erişim paneli sayfalarına Şirket markası ekleme](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B işbirliği kullanıcılarına SharePoint Online ve OneDrive erişimi verebilir mi?
Evet. Ancak, kişiler seçiciyi kullanarak SharePoint Online 'da mevcut Konuk kullanıcıları arama özelliği varsayılan olarak **kapalıdır** . Mevcut Konuk kullanıcıları arama seçeneğini açmak için **ShowPeoplePickerSuggestionsForGuestUsers** **olarak ayarlayın.** Bu ayarı kiracı düzeyinde ya da site koleksiyonu düzeyinde açabilirsiniz. Bu ayarı, set-SPOTenant ve set-SPOSite cmdlet 'lerini kullanarak değiştirebilirsiniz. Bu cmdlet 'lerle, Üyeler dizindeki tüm mevcut Konuk kullanıcıları arayabilir. Kiracı kapsamındaki değişiklikler zaten sağlanmış olan SharePoint Online sitelerini etkilemez.

### <a name="is-the-csv-upload-feature-still-supported"></a>CSV karşıya yükleme özelliği hala destekleniyor mu?
Evet. . Csv dosyasını karşıya yükleme özelliğini kullanma hakkında daha fazla bilgi için [Bu PowerShell örneğine](code-samples.md)bakın.

### <a name="how-can-i-customize-my-invitation-emails"></a>Davet e-postalarımı nasıl özelleştirebilirim?
[B2B davetiyesi API 'lerini](customize-invitation-api.md)kullanarak, davet eden işlemle ilgili neredeyse her şeyi özelleştirebilirsiniz.

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Konuk kullanıcılar Multi-Factor Authentication metodunu sıfırlayabilirler mi?
Evet. Konuk kullanıcılar Multi-Factor Authentication yöntemini normal kullanıcılarla aynı şekilde sıfırlayabilirler.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Multi-Factor Authentication lisanslarından hangi kuruluşa sorumlu?
Davet eden kuruluş çok faktörlü kimlik doğrulaması gerçekleştirir. Davet edilen kuruluş, kuruluşun Multi-Factor Authentication kullanan B2B kullanıcıları için yeterli sayıda lisansa sahip olduğundan emin olmalıdır.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Bir iş ortağı kuruluşu zaten Multi-Factor Authentication ayarlandıysa ne olursa? Multi-Factor Authentication 'a güvenebilir ve kendi Multi-Factor Authentication 'umuzu kullanmıyor musunuz?
Bu özellik şu anda desteklenmiyor. Kuruluşunuzun kaynaklarına erişim çok faktörlü kimlik doğrulaması gerektiriyorsa, iş ortağı kuruluşun Multi-Factor Authentication 'a (davet etme) kuruluşunuzda kaydolması gerekir.

### <a name="how-can-i-use-delayed-invitations"></a>Gecikmeli davetleri nasıl kullanabilirim?
Kuruluş, B2B işbirliği kullanıcıları eklemek, bunları gerektiği gibi uygulamalara sağlamak ve ardından davetiye göndermek isteyebilir. Ekleme iş akışını özelleştirmek için B2B işbirliği davetiyesi API 'sini kullanabilirsiniz.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Konuk kullanıcıları Exchange genel adres listesinde görünür yapabilir miyim?
Evet. Konuk nesneler, kuruluşunuzun genel adres listesinde (GAL) varsayılan olarak görünmez, ancak Azure Active Directory PowerShell kullanarak bunları görünür hale getirebilirsiniz. Bkz [. genel adres listesinde Konuk nesneleri görünür yapabilir miyim?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#can-i-make-guest-objects-visible-in-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Konuk kullanıcıyı sınırlı bir yönetici yapabilir miyim?
Kesinlikle. Daha fazla bilgi için bkz. [bir role Konuk kullanıcılar ekleme](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B işbirliği, B2B kullanıcılarının Azure portal erişmesine izin veriyor mu?
Kullanıcılara sınırlı yönetici rolü atanmamışsa, B2B işbirliği kullanıcıları Azure portal erişim gerektirmez. Ancak, sınırlı yönetici rolüne atanan B2B işbirliği kullanıcıları portala erişebilir. Ayrıca, bu yönetici rollerinden birine atanmamış bir Konuk Kullanıcı portala erişebilse, Kullanıcı deneyimin belirli bölümlerine erişebiliyor olabilir. Konuk Kullanıcı rolü, dizinde bazı izinlere sahiptir.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Konuk kullanıcılar için Azure portal erişimi engelleyebilir miyim?

Evet! Tüm konuk ve dış kullanıcıların Azure portal erişimini engelleyen bir koşullu erişim ilkesi oluşturabilirsiniz. Bu ilkeyi yapılandırırken, yanlışlıkla üyelere ve yöneticilere erişimi engellemeyi unutmaktan emin olun.

1. [Azure Portal](https://portal.azure.com/) Güvenlik Yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
2. Azure portalında **Azure Active Directory** seçeneğini belirleyin. 
3. **Yönet**altında **güvenlik**' i seçin.
4. **Koru**altında **koşullu erişim**' i seçin. **Yeni ilke**' yi seçin.
5. **Yeni** sayfada, **ad** metin kutusuna ilke için bir ad girin (örneğin, "konukların portala erişimini engelle").
6. **Atamalar**' ın altında **Kullanıcılar ve gruplar**' ı seçin.
7. **Dahil et** sekmesinde **kullanıcıları ve grupları seç**' i seçin ve ardından **tüm konuk ve dış kullanıcılar ' ı (Önizleme)** seçin.
9. **Done** (Bitti) öğesini seçin.
10. **Yeni** sayfada, **atamalar** bölümünde, **bulut uygulamaları veya eylemler**' i seçin.
11. **Bulut uygulamaları veya eylemler** sayfasında, **uygulamaları seç**' i seçin ve ardından **Seç**' i seçin.
12. **Seç** sayfasında **Microsoft Azure Management**’ı ve sonra **Seç** öğesini seçin.
13. **Bulut uygulamaları veya eylemler** sayfasında **bitti**' yi seçin.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Azure AD B2B işbirliği, Multi-Factor Authentication ve müşteri e-posta hesaplarını destekliyor mu?
Evet. Multi-Factor Authentication ve tüketici e-posta hesaplarının her ikisi de Azure AD B2B işbirliği için desteklenir.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Azure AD B2B işbirliği kullanıcıları için parola sıfırlamayı destekliyor musunuz?
Azure AD kiracınız bir kullanıcının giriş dizinidir ve [Kullanıcı parolasını Azure Portal sıfırlayabilirsiniz](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) . Ancak, başka bir Azure AD dizini veya dış kimlik sağlayıcısı tarafından yönetilen bir hesapla oturum açan bir Konuk kullanıcının parolasını doğrudan sıfırlayamazsınız. Yalnızca kullanıcının giriş dizinindeki Konuk Kullanıcı veya yönetici parolayı sıfırlayabilir. Aşağıda, parola sıfırlamanın Konuk kullanıcılar için nasıl çalıştığı hakkında bazı örnekler verilmiştir:
 
* Microsoft hesabı ile oturum açan Konuk kullanıcılar (örneğin guestuser@live.com), Microsoft hesabı self servis parola sıfırlama (SSPR) kullanarak kendi parolalarını sıfırlayabilir. [Microsoft hesabı parolanızı sıfırlama](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password)bölümüne bakın.
* Bir Google hesabı veya başka bir dış kimlik sağlayıcısı ile oturum açan Konuk kullanıcılar, kimlik sağlayıcısının SSPR metodunu kullanarak kendi parolalarını sıfırlayabilir. Örneğin, Google hesabı guestuser@gmail.com Konuk bir kullanıcı parolanızı [değiştirme veya sıfırlama](https://support.google.com/accounts/answer/41078)bölümündeki yönergeleri izleyerek parolalarını sıfırlayabilir.
* Kimlik kiracısı tam zamanında (JıT) veya "viral" kiracınız (yani ayrı, yönetilmeyen bir Azure kiracısı) ise, yalnızca Konuk Kullanıcı parolasını sıfırlayabilir. Bazen bir kuruluş, çalışanlar hizmetlere kaydolmak için iş e-posta adreslerini kullandıklarında oluşturulan [viral kiracılarının yönetimini ele geçirebilir](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) . Kuruluş bir viral kiracısı gerçekleştirdikten sonra, yalnızca o kuruluştaki bir yönetici kullanıcı parolasını sıfırlayabilir veya SSPR 'yi etkinleştirebilir. Gerekirse, kuruluş olarak, Konuk Kullanıcı hesabını dizininizden kaldırabilir ve bir daveti yeniden gönderebilirsiniz.
* Konuk kullanıcının ana dizini Azure AD kiracınız ise, kullanıcının parolasını sıfırlayabilirsiniz. Örneğin, bir Kullanıcı oluşturmuş veya şirket içi Active Directory bir kullanıcıyı eşitledi ve Kullanıcıtürünü Konuk olarak ayarlamış olabilirsiniz. Bu Kullanıcı dizininizde bulunduğundan, Azure portal parolalarını sıfırlayabilirsiniz.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics 365, Azure AD B2B işbirliği için çevrimiçi destek sağlar mi?
Evet, Dynamics 365 (çevrimiçi) Azure AD B2B işbirliğini destekler. Daha fazla bilgi için bkz. Dynamics 365 makalesine [kullanıcıları Azure AD B2B işbirliği Ile davet etme](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Yeni oluşturulan B2B işbirliği kullanıcısı için başlangıç parolasının yaşam süresi nedir?
Azure AD 'nin, tüm Azure AD bulut Kullanıcı hesaplarına eşit olarak uygulanan sabit bir karakter, parola gücü ve hesap kilitleme gereksinimleri kümesi vardır. Bulut Kullanıcı hesapları, başka bir kimlik sağlayıcısı ile federasyon olmayan, örneğin 
* Microsoft hesabı
* Facebook
* Active Directory Federasyon Hizmetleri
* Başka bir bulut kiracısı (B2B işbirliği için)

Federasyon hesapları için parola ilkesi, şirket içi kiralamanın ve kullanıcının Microsoft hesabı ayarlarında uygulanan ilkeye bağlıdır.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Kuruluş, kiracı kullanıcıları ve Konuk kullanıcılar için uygulamalarında farklı deneyimler olmasını isteyebilir. Bunun için standart rehberlik var mı? Kimlik sağlayıcısı talebi kullanılacak doğru modeli mi var?
Konuk Kullanıcı, kimlik doğrulamak için herhangi bir kimlik sağlayıcısını kullanabilir. Daha fazla bilgi için bkz. [B2B işbirliği kullanıcısının özellikleri](user-properties.md). Kullanıcı deneyimini öğrenmek için **UserType** özelliğini kullanın. **UserType** talebi şu anda belirtece dahil değildir. Uygulamalar, kullanıcının dizinini sorgulamak ve UserType 'ı almak için Graph API kullanmalıdır.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Çözümleri paylaşmak ve fikir göndermek için bir B2B işbirliği topluluğunu nereden bulabilirim?
B2B işbirliğinin geliştirilmesi için geri bildirimlerinizi sürekli olarak dinliyoruz. Lütfen Kullanıcı senaryolarınızı, en iyi uygulamalarınızı ve Azure AD B2B işbirliği hakkında beğendiklerinizi paylaşabilirsiniz. [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)'deki tartışmaya katın.
 
Ayrıca, [B2B Işbirliği fikirleri](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas)' nde fikirlerinizi göndermek ve gelecekteki özellikler için oy vermek üzere davet ediyoruz.

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Otomatik olarak kullanılan bir davet gönderebiliriz, böylece Kullanıcı yalnızca "başlamaya hazırlanıyor" olur mu? Ya da Kullanıcı, kullanım URL 'sine her zaman tıklasın mı?
Kullanıcı arabirimi, PowerShell betikleri veya API 'Leri kullanarak iş ortağı kuruluştaki diğer kullanıcıları davet edebilirsiniz. Daha sonra, Konuk kullanıcıyı paylaşılan bir uygulamaya doğrudan bir bağlantı gönderebilirsiniz. Çoğu durumda, artık e-posta davetini açmaya gerek kalmaz ve bir kullanım URL 'sine tıklayabilirsiniz. Bkz. [Azure ACTIVE DIRECTORY B2B işbirliği daveti](redemption-experience.md)kullanım.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Davet edilen iş ortağı, kendi şirket içi kimlik doğrulamasını eklemek için Federasyon kullanıyorsa B2B işbirliği nasıl çalışır?
Ortağın Şirket içi kimlik doğrulama altyapısına federal bir Azure AD kiracısı varsa, şirket içi çoklu oturum açma (SSO) otomatik olarak sağlanır. Ortağın bir Azure AD kiracısı yoksa, yeni kullanıcılar için bir Azure AD hesabı oluşturulur. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Azure AD B2B 'nin gmail.com ve outlook.com e-posta adreslerini kabul etmediğinizi ve bu B2C 'nin bu tür hesaplar için kullanıldığını sanıyordum mi?
B2B ile işletmeden müşteriye (B2C) işbirliği arasındaki farkları, hangi kimliklerin desteklendiğiyle kaldırıyoruz. Kullanılan kimlik, B2B kullanma veya B2C kullanma arasında seçim yapmak için iyi bir neden değil. İşbirliği seçeneğinizi seçme hakkında daha fazla bilgi için bkz. [Azure ACTIVE DIRECTORY B2B işbirliğini ve B2C 'Yi karşılaştırın](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Azure B2B Konuk kullanıcılarını hangi uygulamalar ve hizmetler destekler?
Azure AD ile tümleştirilen tüm uygulamalar, Azure B2B Konuk kullanıcılarını destekleyebilir, ancak konuk kullanıcıların kimliğini doğrulamak için kiracı olarak ayarlanmış bir uç nokta kullanmaları gerekir. Ayrıca, bir Konuk kullanıcı uygulamanın kimliğini doğruladığında verilen SAML belirtecindeki [talepleri özelleştirmeniz](claims-mapping.md) gerekebilir. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>İş ortaklarımızın Multi-Factor Authentication yoksa, B2B Konuk kullanıcıları için Multi-Factor Authentication 'ı zorlayabilir miyim?
Evet. Daha fazla bilgi için bkz. [B2B işbirliği kullanıcıları Için koşullu erişim](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>SharePoint 'te, dış kullanıcılar için "izin ver" veya "Reddet" listesini tanımlayabilirsiniz. Azure 'da bunu yapabilir miyim?
Evet. Azure AD B2B işbirliği, liste ve reddetme listelerine izin vermeyi destekler. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Azure AD B2B 'yi kullanmak için hangi lisansları gerekir?
Kuruluşunuzun Azure AD B2B kullanması gereken lisanslar hakkında daha fazla bilgi için, [Azure ACTIVE DIRECTORY B2B işbirliği Lisanslama Kılavuzu](licensing-guidance.md)' na bakın.

### <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)

