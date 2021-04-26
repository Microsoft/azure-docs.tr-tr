---
title: Azure AD Multi-Factor Authentication istemler ve oturum ömrü
description: Azure AD Multi-Factor Authentication ve oturum ömrünün nasıl uygulandığı ile ilgili yeniden kimlik doğrulama istemlerine yönelik önerilen yapılandırma hakkında bilgi edinin.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0033b6985313de351ffdaf7028d2c1e9bd76ba72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963528"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication için yeniden kimlik doğrulama istemlerini iyileştirin ve oturum ömrünü anlayın

Azure Active Directory (Azure AD), kullanıcıların hangi sıklıkta yeniden kimlik doğrulaması yapması gerektiğini tespit eden birden çok ayarı vardır. Bu yeniden kimlik doğrulaması, parola, FIDO veya passwordless Microsoft Authenticator gibi bir ilk faktörle veya Multi-Factor Authentication (MFA) gerçekleştirmeye yönelik olabilir. Bu yeniden kimlik doğrulama ayarlarını kendi ortamınız ve istediğiniz kullanıcı deneyimi için gerektiği şekilde yapılandırabilirsiniz.

Kullanıcı oturum açma sıklığı için Azure AD varsayılan yapılandırması, 90 günlük bir toplama penceresidir. Kimlik bilgileri için kullanıcılara genellikle yapılacak bir şey gibi görünse de, bu durum geri yüklenebilir. Kullanıcılar kimlik bilgilerini düşünmeden girmeye eğitilerse, bunları istemeden kötü amaçlı bir kimlik bilgisi istemine girebilirler.

Bir kullanıcının yeniden oturum açmasını istememekle birlikte, BT ilkelerinin herhangi bir ihlali oturumu iptal eder. Bir parola değişikliği, uyumsuz bir cihaz ya da hesap devre dışı bırakma işlemi bazı örneklere dahildir. Ayrıca, [PowerShell kullanarak kullanıcıların oturumlarını açıkça iptal](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)edebilirsiniz.

Bu makalede önerilen yapılandırma ve farklı ayarların nasıl çalıştığı ve birbirleriyle etkileşim kurduğu açıklanır.

## <a name="recommended-settings"></a>Önerilen ayarlar

Kullanıcılarınıza doğru sıklıkta oturum açmasını isteyerek kullanıcılarınıza güvenlik ve kullanım kolaylığını sağlamak için aşağıdaki konfigürasyonları öneririz:

* Azure AD Premium sahipseniz:
    * [Yönetilen cihazları](../devices/overview.md) veya [sorunsuz SSO](../hybrid/how-to-connect-sso.md)'yu kullanarak uygulamalar arasında çoklu oturum açma (SSO) özelliğini etkinleştirin.
    * Yeniden kimlik doğrulaması gerekliyse, koşullu erişim [oturum açma sıklığı ilkesini](../conditional-access/howto-conditional-access-session-lifetime.md)kullanın.
    * Yönetilmeyen cihazlardan veya mobil cihaz senaryolarından oturum açma kullanıcıları için, kalıcı tarayıcı oturumlarını ve oturum açma sıklığı ilkelerini etkinleştirmek üzere koşullu erişim kullanın.
* Microsoft 365 Apps lisanslarınız veya ücretsiz Azure AD katmanınız varsa:
    * [Yönetilen cihazları](../devices/overview.md) veya [sorunsuz SSO](../hybrid/how-to-connect-sso.md)'yu kullanarak uygulamalar arasında çoklu oturum açma (SSO) özelliğini etkinleştirin.
    * Açık *oturum açmış durumda kalır* seçeneğinin etkin kalmasını sağlayın ve kullanıcılarınıza bunu kabul etmesine kılavuzluk edin.
* Mobil cihaz senaryolarında, kullanıcılarınızın Microsoft Authenticator uygulamasını kullanmasına emin olun. Bu uygulama, diğer Azure AD federasyon uygulamalarına yönelik bir aracı olarak kullanılır ve cihazdaki kimlik doğrulama istemlerini azaltır.

Araştırmamız bu ayarların çoğu kiracılar için doğru olduğunu gösterir. Bu ayarların *MFA* ve *MAED olmaya devam ediyor* gibi bazı birleşimleri, kullanıcılarınızın çok sık kimlik doğrulamasına neden olabilir. Normal yeniden kimlik doğrulama istemleri, kullanıcı üretkenliği için hatalı ve saldırılara karşı daha savunmasız hale getirebilir.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD oturum ömür yapılandırması ayarları

Kullanıcılarınıza yönelik kimlik doğrulama istemlerinin sıklığını iyileştirmek için Azure AD oturum ömür seçeneklerini yapılandırabilirsiniz. İşletmenizin ve kullanıcılarınızın ihtiyaçlarını anlayın ve ortamınız için en iyi dengeyi sağlayan ayarları yapılandırın.

### <a name="evaluate-session-lifetime-policies"></a>Oturum ömür ilkelerini değerlendir

Herhangi bir oturum ömür ayarı olmadan, tarayıcı oturumunda kalıcı tanımlama bilgileri yoktur. Kullanıcı tarayıcıyı her kapattığında ve açtığında, yeniden kimlik doğrulama istemi alırlar. Office istemcilerinde, varsayılan zaman aralığı 90 günlük bir sıralı penceredir. Bu varsayılan Office yapılandırmasıyla, Kullanıcı parolasını sıfırlarsa veya 90 günden fazla kullanım dışı bir işlem yapılmışsa, kullanıcının tüm gerekli faktörlerle yeniden kimlik doğrulaması yapması gerekir (ilk ve ikinci faktör).

Kullanıcı, Azure AD 'de kimliği olmayan bir cihazda birden fazla MFA istemi görebilir. Birden çok istem, her bir uygulama diğer istemci uygulamalarıyla paylaşılmayan kendi OAuth yenileme belirtecine sahip olduğunda sonuç olarak sonuçlanır. Bu senaryoda, her bir uygulama MFA ile doğrulanacak bir OAuth yenileme belirteci istediğinde MFA birden çok kez istemde bulunur.

Azure AD 'de oturum ömrü için en kısıtlayıcı ilke, kullanıcının yeniden kimlik doğrulaması yapması gerekip gerekmediğini belirler. Şu senaryoyu göz önünde bulundurun:

* Kalıcı bir tarayıcı tanımlama bilgisi kullanan *oturum açmış durumda kalır* ve
* Ayrıca *14 gün IÇIN MFA 'Yı hatırla* özelliğini de etkinleştirmeniz gerekir

Bu örnek senaryoda, kullanıcının her 14 günde bir yeniden kimlik doğrulaması yapması gerekir. Bu davranış en kısıtlayıcı ilkeyi izler, ancak *benimle oturum açanlar* Kullanıcı tarafından tarayıcıda yeniden kimlik doğrulaması yapılmasını gerektirmez.

### <a name="managed-devices"></a>Yönetilen cihazlar

Azure AD JOIN veya hibrit Azure AD JOIN kullanılarak Azure AD 'ye katılmış cihazlar, uygulamalar arasında çoklu oturum açma (SSO) kullanmak için [birincil yenileme belirteçleri (PRT)](../devices/concept-primary-refresh-token.md) alır. Bu PRT, kullanıcının cihazda bir kez oturum açmasını sağlar ve BT personelinin güvenlik ve uyumluluk standartlarının karşılandığından emin olmasını sağlar. Bir kullanıcının, bazı uygulamalar veya senaryolar için katılmış bir cihazda daha sık oturum açması istenirse, bu, [koşullu erişim oturum açma sıklığı](../conditional-access/howto-conditional-access-session-lifetime.md)kullanılarak elde edilebilir.

### <a name="show-option-to-remain-signed-in"></a>Oturum açmış olmaya devam etmek için seçeneği göster

Kullanıcı oturum açık *kalsın* sayfasında **Evet** ' i seçtiğinde, tarayıcıda kalıcı bir tanımlama bilgisi ayarlanır. Bu kalıcı tanımlama bilgisi hem birinci hem de ikinci faktörü anımsar ve yalnızca tarayıcıdaki kimlik doğrulama istekleri için geçerlidir.

![Oturum açmış durumda kalması için örnek ekran görüntüsü](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Azure AD Premium 1 lisansınız varsa *kalıcı tarayıcı oturumu* Için koşullu erişim ilkesi kullanmanızı öneririz. Bu ilke, *oturum açık kal mı?* ayarının üzerine yazar ve geliştirilmiş bir kullanıcı deneyimi sağlar. Azure AD Premium 1 lisansınız yoksa kullanıcılarınız için oturum açmış kalma ayarını etkinleştirmenizi öneririz.

Kullanıcıların oturum açmasını sağlamak için seçeneğini yapılandırma hakkında daha fazla bilgi için bkz. [Azure AD oturum açma sayfanızı özelleştirme](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication anımsa  

Bu ayar, bir Kullanıcı oturum açma sırasında **X gün için yeniden sorma** seçeneğini seçtiğinde tarayıcıda 1-365 gün arasında değer yapılandırmanıza ve kalıcı bir tanımlama bilgisi ayarlamanıza olanak sağlar.

![Bir oturum açma isteğini onaylama isteminin ekran görüntüsü](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Bu ayar Web Apps 'teki kimlik doğrulama sayısını azalttığında, Office istemcileri gibi modern kimlik doğrulama istemcilerinin kimlik doğrulama sayısını artırır. Bu istemciler normalde yalnızca parola sıfırlama veya 90 günlük eylemsizlik sonrasında uyarır. Ancak, bu değerin 90 günden az olması, Office istemcilerine yönelik varsayılan MFA istemlerini kısaltır ve yeniden kimlik doğrulama sıklığını artırır. İle birlikte kullanıldığında, **oturum açmış** veya koşullu erişim ilkelerine devam edildiğinde, kimlik doğrulama isteklerinin sayısını artırabilir.

*MFA 'Yı hatırla* ve Azure AD Premium 1 lisansı varsa, bu ayarları koşullu erişim oturum açma sıklığına geçirmeyi göz önünde bulundurun. Aksi takdirde, *Oturumumu Açık tut* seçeneğini kullanmayı düşünün.

Daha fazla bilgi için bkz. [hatırlayın Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>Koşullu erişimle kimlik doğrulama oturumu yönetimi

**Oturum açma sıklığı** , yöneticinin hem istemci hem de tarayıcıda hem birinci hem de ikinci faktör için geçerli olan oturum açma sıklığını seçmesine olanak sağlar. Kritik iş uygulamaları gibi kimlik doğrulama oturumunu kısıtlama gereksinimimiz olduğunda, bu ayarları, yönetilen cihazları kullanma ile birlikte kullanmanızı öneririz.

**Kalıcı tarayıcı oturumu** , kullanıcıların tarayıcı pencerelerini kapatıp yeniden açtıktan sonra oturum açmasına olanak tanır. *Oturum açmış durumda kalma* ayarlarına benzer şekilde, tarayıcıda kalıcı bir tanımlama bilgisi ayarlar. Bununla birlikte, yönetici tarafından yapılandırıldığından, *oturum açmış kalmak* Için kullanıcının **Evet** seçeneğini seçmesini gerektirmez. bu nedenle, daha iyi bir kullanıcı deneyimi sağlar. *Oturum açmış durumda kalmış mi?* seçeneğini kullanırsanız, bunun yerine **kalıcı tarayıcı oturumu** ilkesini etkinleştirmenizi öneririz.

Daha fazla bilgi için. bkz. [koşullu erişimle kimlik doğrulama oturumu yönetimini yapılandırma](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Yapılandırılabilir belirteç yaşam süreleri

Bu ayar Azure Active Directory tarafından verilen belirtecin ömür yapılandırmasına izin verir. Bu ilke, *koşullu erişimle kimlik doğrulama oturumu yönetimi ile* değiştirilmiştir. Günümüzde *yapılandırılabilir belirteç yaşam süreleri* kullanıyorsanız, geçiş Için koşullu erişim ilkelerine başlamasını öneririz.

## <a name="review-your-tenant-configuration"></a>Kiracı yapılandırmanızı gözden geçirin  

Farklı ayarların nasıl çalıştığını ve önerilen yapılandırmayı anladığınıza göre, kiracılar yapılandırmanızı denetleyip buna göre değişiklikler yapmanız zaman alabilir:

*Kalan oturum açmış* seçeneği yapılandırmak veya gözden geçirmek için aşağıdaki adımları izleyin:

1. Azure AD portalında *Azure Active Directory* arayın ve öğesini seçin.
1. **Şirket markasını** seçin ve ardından her yerel ayar için, **oturum açmış olarak kalmak için seçeneği göster**' i seçin.
1. *Evet*' i seçin, sonra **Kaydet**' i seçin.

Güvenilen cihazlarda Multi-Factor Authentication ayarlarını hatırlamak için aşağıdaki adımları izleyin:

1. Azure AD portalında *Azure Active Directory* arayın ve öğesini seçin.
1. **Güvenlik** ve **MFA**' yı seçin.
1. **Yapılandır** altında **bulut tabanlı ek MFA ayarları**' nı seçin.
1. *Multi-Factor Authentication hizmet ayarları* sayfasında **Multi-Factor Authentication ayarlarını anımsa**' ya gidin. Onay kutusunun işaretini kaldırarak ayarı devre dışı bırakın.

Oturum açma sıklığı ve kalıcı tarayıcı oturumu için koşullu erişim ilkelerini yapılandırmak için aşağıdaki adımları izleyin:

1. Azure AD portalında *Azure Active Directory* arayın ve öğesini seçin.
1. **Güvenlik** ve **koşullu erişim**' i seçin.
1. Bu makalede ayrıntılı olarak önerilen oturum yönetimi seçeneklerini kullanarak bir ilke yapılandırın.

Belirteç ömrünü gözden geçirmek için Azure AD [PowerShell 'i kullanarak Azure AD ilkelerini sorgulayın](../develop/configure-token-lifetimes.md#get-started). Yerinde olan tüm ilkeleri devre dışı bırakın.

Kiracınızda birden fazla ayar etkinse, sizin için kullanılabilir lisanslama temelinde ayarlarınızı güncelleştirmenizi öneririz. Örneğin, Azure AD Premium lisanslarınız varsa, yalnızca *oturum açma sıklığı* ve *kalıcı tarayıcı oturumunun* koşullu erişim ilkesini kullanmanız gerekir. Microsoft 365 uygulamalarınız veya Azure AD ücretsiz lisanslarınız varsa, *oturum açmış durumda kaldı?* yapılandırmasını kullanmanız gerekir.

Yapılandırılabilir belirteç yaşam sürelerini etkinleştirdiyseniz, bu özellik yakında kaldırılacaktır. Koşullu erişim ilkesine geçiş planlayın.

Aşağıdaki tabloda lisanslarına göre öneriler özetlenmektedir:

|              | Azure AD Ücretsiz ve Microsoft 365 uygulamalar | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | [Azure AD JOIN](../devices/concept-azure-ad-join.md) veya [hibrit Azure AD JOIN](../devices/concept-azure-ad-join-hybrid.md)veya YÖNETILMEYEN cihazlar için [sorunsuz SSO](../hybrid/how-to-connect-sso.md) . | Azure AD'ye katılım<br />Hibrit Azure AD'ye katılım |
| **Yeniden kimlik doğrulama ayarları** | Oturum açmış durumda kalır                  | Oturum açma sıklığı ve kalıcı tarayıcı oturumu için koşullu erişim ilkelerini kullanın |

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için, [Azure ad Multi-Factor Authentication ile Kullanıcı oturum açma olaylarını güvenli hale getirme](tutorial-enable-azure-mfa.md) veya [Kullanıcı oturum açma işlemlerini Azure AD Multi-Factor Authentication tetiklemek için risk algılamalarını kullanma](tutorial-risk-based-sspr-mfa.md)öğreticisini doldurun.
