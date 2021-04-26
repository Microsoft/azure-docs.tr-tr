---
title: Kimlik doğrulama oturumu yönetimini Yapılandırma-Azure Active Directory
description: Kullanıcı oturum açma sıklığı ve tarayıcı oturumu kalıcılığı dahil olmak üzere Azure AD kimlik doğrulama oturumu yapılandırmasını özelleştirin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6116ab543d6dfc886e44206c2a60e4456b39fbc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558194"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Koşullu erişimle kimlik doğrulama oturumu yönetimini yapılandırma

Karmaşık dağıtımlarda, kuruluşların kimlik doğrulama oturumlarını kısıtlama gereksinimi olabilir. Bazı senaryolarda şunlar bulunabilir:

* Yönetilmeyen veya paylaşılan bir cihazdan kaynak erişimi
* Dış bir ağdan hassas bilgilere erişim
* Yüksek etki kullanıcıları
* Kritik iş uygulamaları

Koşullu erişim denetimleri, tüm kullanıcıları etkilemeden kuruluşunuzdaki belirli kullanım örneklerini hedefleyen ilkeler oluşturmanızı sağlar.

İlkenin nasıl yapılandırılacağı hakkında ayrıntılı bilgi almak için önce varsayılan yapılandırmayı inceleyelim.

## <a name="user-sign-in-frequency"></a>Kullanıcı oturum açma sıklığı

Oturum açma sıklığı, bir kullanıcıdan bir kaynağa erişmeye çalışırken yeniden oturum açması istenmeden önce geçen süreyi tanımlar.

Kullanıcı oturum açma sıklığı için Azure Active Directory (Azure AD) varsayılan yapılandırması, 90 günlük bir toplama penceresidir. Kimlik bilgileri için kullanıcılardan genellikle yapılacak bir şey gibi görünse de, bu durum geri yüklenebilir: kimlik bilgilerini düşünmeden girmek için eğitilen kullanıcılar, bunları istemeden kötü amaçlı bir kimlik bilgisi istemine girebilirler.

Bir kullanıcının yeniden oturum açmasını istememe konusunda bir sorun olabilir, çünkü gerçekte BT ilkelerinin ihlal edilmesi oturumu iptal eder. Bir parola değişikliği, uyumsuz bir cihaz veya hesabı devre dışı bırakmak için bazı örnekler vardır (ancak bunlarla sınırlı değildir). Ayrıca, [PowerShell kullanarak kullanıcıların oturumlarını açıkça iptal](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)edebilirsiniz. Azure AD varsayılan yapılandırması, "oturumlarından oluşan güvenlik durusız" kullanıcılardan kimlik bilgilerini sağlamasını isteme "olarak değişir.

Oturum açma sıklığı ayarı, standartlara göre OAUTH2 veya OıDC protokollerini uygulamış olan uygulamalarla birlikte kullanılır. Aşağıdaki Web uygulamaları dahil olmak üzere Windows, Mac ve mobil için Microsoft Native uygulamaların çoğu ayarla uyumlu değil.

- Word, Excel, PowerPoint online
- OneNote Online
- Office.com
- Yönetici portalı Microsoft 365
- Exchange Online
- SharePoint ve OneDrive
- Takımlar Web istemcisi
- Dynamics CRM Online
- Azure portalı

Oturum açma sıklığı ayarı, SAML uygulamalarıyla birlikte çalışarak kendi tanımlama bilgilerini düşürülemez ve düzenli olarak kimlik doğrulaması için Azure AD 'ye yeniden yönlendirilir.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Kullanıcı oturum açma sıklığı ve Multi-Factor Authentication

Daha önce Azure AD 'ye katılmış, hibrit Azure AD 'ye katılmış ve Azure AD 'ye kayıtlı cihazlarda yalnızca birinci faktör kimlik doğrulamasına uygulanan oturum açma sıklığı. Müşterilerimizin bu cihazlarda Multi Factor Authentication 'ı (MFA) yeniden zorlaması için kolay bir yol yoktu. Müşteri geri bildirimlerine dayalı olarak oturum açma sıklığı, MFA için de geçerlidir.

[![Oturum açma sıklığı ve MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Kullanıcı oturum açma sıklığı ve cihaz kimlikleri

Azure AD 'ye katılmış, hibrit Azure AD 'ye katılmış veya Azure AD kayıtlı cihazlara sahipseniz, bir kullanıcı cihazlarından veya etkileşimli olarak oturum açtığında bu olay, oturum açma sıklığı ilkesini de karşılar. Aşağıdaki iki örnekte, Kullanıcı oturum açma sıklığı 1 saat olarak ayarlanır:

Örnek 1:

- 00:00 ' de, bir Kullanıcı Windows 10 Azure AD 'ye katılmış cihazından oturum açar ve SharePoint Online 'da depolanan bir belge üzerinde çalışmaya başlar.
- Kullanıcı, bir saat boyunca cihazındaki aynı belge üzerinde çalışmaya devam eder.
- 01:00 ' de, kullanıcıdan yönetici tarafından yapılandırılan koşullu erişim ilkesindeki oturum açma sıklığı gereksinimine göre yeniden oturum açması istenir.

Örnek 2:

- 00:00 ' de, bir Kullanıcı Windows 10 Azure AD 'ye katılmış cihazından oturum açar ve SharePoint Online 'da depolanan bir belge üzerinde çalışmaya başlar.
- 00:30 ' de, Kullanıcı kendi cihazını kilitlemesini alır ve keser.
- 00:45 ' de, Kullanıcı kesmesinden geri döner ve cihazın kilidini açar.
- 01:45 ' de, kullanıcıdan en son oturum açma işlemi 00:45 ' de gerçekleşmesinden bu yana yönetici tarafından yapılandırılan koşullu erişim ilkesindeki oturum açma sıklığı gereksinimine göre yeniden oturum açması istenir.

## <a name="persistence-of-browsing-sessions"></a>Gözatma oturumlarının kalıcılığı

Kalıcı bir tarayıcı oturumu, kullanıcıların tarayıcı pencerelerini kapatıp yeniden açtıktan sonra oturum açmasına olanak tanır.

Tarayıcı oturumu kalıcılığı için Azure AD varsayılan değeri, kişisel cihazlardaki kullanıcıların "oturum açık kal mı?" göstererek oturumu kalıcı olarak belirleyip kapatmayacağını seçmesine olanak sağlar. başarılı kimlik doğrulamasından sonra sor. Tarayıcı kalıcılığı AD FS, [tek Sign-On ayarları AD FS](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)makalesindeki kılavuz kullanılarak yapılandırılmışsa, bu ilkeye uyum sağlayacak ve Azure AD oturumunun de kalıcı hale getirilecektir. Ayrıca kiracınızdaki kullanıcıların "oturum açmış durumda kal?" i görmesini da yapılandırabilirsiniz. [Azure AD oturum açma sayfanızı özelleştirme](../fundamentals/customize-branding.md)makalesindeki kılavuzu kullanarak Azure Portal içindeki şirket markası bölmesinde uygun ayarı değiştirerek istemde bulun.

## <a name="configuring-authentication-session-controls"></a>Kimlik doğrulama oturumu denetimlerini yapılandırma

Koşullu erişim Azure AD Premium bir yetenektir ve Premium lisans gerektirir. Koşullu erişim hakkında daha fazla bilgi edinmek istiyorsanız bkz. [Azure Active Directory Koşullu erişim nedir?](overview.md#license-requirements)

> [!WARNING]
> Şu anda genel önizleme aşamasında olan [yapılandırılabilir belirteç yaşam süresi](../develop/active-directory-configurable-token-lifetimes.md) özelliğini kullanıyorsanız, lütfen aynı kullanıcı veya uygulama birleşimi için iki farklı ilke oluşturulmasını desteklemediğimiz, bu özelliğe ve diğeri yapılandırılabilir belirteç ömrü özelliğine sahip başka bir uygulamayla aynı şekilde desteklenmediğini unutmayın. Microsoft, 30 Ocak 2021 tarihinde yenileme ve oturum belirteci ömürleri için yapılandırılabilir belirteç yaşam süresi özelliğini kullanımdan kaldırdı ve koşullu erişim kimlik doğrulama oturumu Yönetimi özelliğiyle değiştirdi.  
>
> Oturum açma sıklığını etkinleştirmeden önce, kiracınızda diğer yeniden kimlik doğrulama ayarlarının devre dışı bırakıldığından emin olun. "Güvenilen cihazlarda MFA 'yı anımsa" etkinse, bu iki ayarı birlikte kullanmak kullanıcıların beklenmedik şekilde sorulmasına yol açacağından, oturum açma sıklığını kullanmadan önce devre dışı bırakmayı unutmayın. Yeniden kimlik doğrulama istemleri ve oturum ömrü hakkında daha fazla bilgi edinmek için, bkz. yeniden [kimlik doğrulama Istemlerini iyileştirme ve Azure AD Multi-Factor Authentication için oturum ömrünü anlama](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>İlke 1: oturum açma sıklığı denetimi

1. Yeni ilke oluştur
1. Hedef bulut uygulamaları dahil olmak üzere müşterinin ortamı için gerekli tüm koşulları seçin.

   > [!NOTE]
   > En iyi kullanıcı deneyimi için Exchange Online ve SharePoint Online gibi anahtar Microsoft Office uygulamalar için eşit kimlik doğrulama istem sıklığı ayarlamanız önerilir.

1. **Erişim denetimleri**  >  **oturumuna** gidin ve **oturum açma sıklığı** ' na tıklayın
1. İlk metin kutusuna gereken gün ve saat değerlerini girin
1. Açılan menüden **saat** veya **gün** değeri seçin
1. İlkenizi kaydetme

![Oturum açma sıklığı için yapılandırılmış koşullu erişim ilkesi](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Azure AD kayıtlı Windows cihazlarında cihazda oturum açın bir istem olarak kabul edilir. Örneğin, Office uygulamaları için oturum açma sıklığını 24 saate yapılandırdıysanız, Azure AD kayıtlı Windows cihazlarındaki kullanıcılar cihazda oturum açarak oturum açma sıklığı ilkesini karşılar ve Office uygulamaları açılırken bir daha sorulmayacaktır.

### <a name="policy-2-persistent-browser-session"></a>İlke 2: kalıcı tarayıcı oturumu

1. Yeni ilke oluştur
1. Tüm gerekli koşulları seçin.

   > [!NOTE]
   > Lütfen bu denetimin koşul olarak "tüm bulut uygulamaları" seçmesini gerektirdiğini unutmayın. Tarayıcı oturumu kalıcılığı, kimlik doğrulama oturumu belirteci tarafından denetlenir. Bir tarayıcı oturumundaki tüm sekmeler tek bir oturum belirtecini paylaşır ve bu nedenle tümünün kalıcılık durumunu paylaşması gerekir.

1. **Erişim denetimleri**  >  **oturumuna** gidin ve **kalıcı tarayıcı oturumu** ' na tıklayın.
1. Açılan listeden bir değer seçin
1. İlkenizi kaydetme

![Kalıcı tarayıcı için yapılandırılmış koşullu erişim ilkesi](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD koşullu erişim 'de kalıcı tarayıcı oturumu yapılandırması "oturum açmış durumda kalsın" üzerine yazılacak. Her iki ilkeyi de yapılandırdıysanız, aynı kullanıcı için Azure portal Şirket markası bölmesinde ayarlama.

## <a name="validation"></a>Doğrulama

İlkeyi nasıl yapılandırdığınıza bağlı olarak kullanıcıdan hedef uygulamaya ve diğer koşullara bir oturumun benzetimini yapmak için What-If aracını kullanın. Kimlik doğrulama oturumu yönetimi denetimleri, aracın sonucuna göre görünür.

![Koşullu erişim What If araç sonuçları](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>İlke dağıtımı

İlkenizin beklenildiği gibi çalıştığından emin olmak için önerilen en iyi yöntem, üretime geçmeden önce test sağlamaktır. İdeal olarak, yeni ilkenizin istendiği gibi çalışıp çalışmadığını doğrulamak için bir test kiracısı kullanın. Daha fazla bilgi için, [koşullu erişim dağıtımını planlayın](plan-conditional-access.md)makalesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Ortamınız için koşullu erişim ilkelerini yapılandırmaya hazırsanız, [koşullu erişim dağıtımını planlayın](plan-conditional-access.md)makalesine bakın.