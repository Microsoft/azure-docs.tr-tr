---
title: Azure Active Directory Koşullu erişimle kimlik doğrulama oturumu yönetimini yapılandırma
description: Kullanıcı oturum açma sıklığı ve tarayıcı oturumu kalıcılığı dahil olmak üzere Azure AD kimlik doğrulaması oturumu yapılandırmasını özelleştirin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70de9da44d52bd6e93377776a2dbfc1b802dd412
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241702"
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

Bir kullanıcının yeniden oturum açmasını istememe konusunda bir sorun olabilir, çünkü gerçekte BT ilkelerinin ihlal edilmesi oturumu iptal eder. Bir parola değişikliği, uyumsuz bir cihaz veya hesabı devre dışı bırakmak için bazı örnekler vardır (ancak bunlarla sınırlı değildir). Ayrıca, [PowerShell kullanarak kullanıcıların oturumlarını açıkça iptal](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)edebilirsiniz. Azure AD varsayılan yapılandırması, "oturumlarından oluşan güvenlik durusız" kullanıcılardan kimlik bilgilerini sağlamasını isteme "olarak değişir.

Oturum açma sıklığı ayarı, standartlara göre OAUTH2 veya OıDC protokollerini uygulamış olan uygulamalarla birlikte çalışarak. Windows, Mac ve mobil için Microsoft Native uygulamalar, bu ayarla uyumlu.

## <a name="persistence-of-browsing-sessions"></a>Gözatma oturumlarının kalıcılığı

Kalıcı bir tarayıcı oturumu, kullanıcıların tarayıcı pencerelerini kapatıp yeniden açtıktan sonra oturum açmasına olanak tanır.

Tarayıcı oturumu kalıcılığı için Azure AD varsayılan değeri, kişisel cihazlardaki kullanıcıların "oturum açık kal mı?" göstererek oturumu kalıcı olarak belirleyip kapatmayacağını seçmesine olanak sağlar. başarılı kimlik doğrulamasından sonra sor. Tarayıcı kalıcılığı AD FS ' de [Çoklu oturum açma ayarları AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)makaledeki yönergeler kullanılarak yapılandırılmışsa, bu ilkeye uyum sağlayacak ve Azure AD oturumunun de kalıcı hale getirilecektir. Ayrıca kiracınızdaki kullanıcıların "oturum açmış durumda kal?" i görmesini da yapılandırabilirsiniz. [Azure AD oturum açma sayfanızı özelleştirme](../fundamentals/customize-branding.md)makalesindeki kılavuzu kullanarak Azure Portal içindeki şirket markası bölmesinde uygun ayarı değiştirerek istemde bulun.

## <a name="configuring-authentication-session-controls"></a>Kimlik doğrulama oturumu denetimlerini yapılandırma

Koşullu erişim Azure AD Premium bir yetenektir ve Premium lisans gerektirir. Koşullu erişim hakkında daha fazla bilgi edinmek istiyorsanız bkz. [Azure Active Directory Koşullu erişim nedir?](overview.md#license-requirements)

> [!WARNING]
> Şu anda genel önizleme aşamasında olan [yapılandırılabilir belirteç ömrü](../develop/active-directory-configurable-token-lifetimes.md) özelliğini kullanıyorsanız, lütfen aynı kullanıcı veya uygulama birleşimi için iki farklı ilke oluşturulmasını desteklemediğimiz, bu özelliğe ve diğeri yapılandırılabilir belirtece sahip başka bir uygulamayla ömür özelliği. Microsoft, yapılandırılabilir belirteç yaşam süresi özelliğini 1 Mayıs 2020 ' de devre dışı bırakmayı planlıyor ve koşullu erişim kimlik doğrulama oturumu Yönetimi özelliğiyle değiştirebilir.  

### <a name="policy-1-sign-in-frequency-control"></a>İlke 1: oturum açma sıklığı denetimi

1. Yeni ilke oluştur
1. Hedef bulut uygulamaları dahil olmak üzere müşterinin ortamı için gerekli tüm koşulları seçin.

   > [!NOTE]
   > En iyi kullanıcı deneyimi için Exchange Online ve SharePoint Online gibi anahtar Microsoft Office uygulamalar için eşit kimlik doğrulama istem sıklığı ayarlamanız önerilir.

1. **Erişim denetimlerine** > **oturum** ' ye gidin ve **oturum açma sıklığı** ' na tıklayın
1. İlk metin kutusuna gereken gün ve saat değerlerini girin
1. Açılan menüden **saat** veya **gün** değeri seçin
1. İlkenizi kaydetme

![Oturum açma sıklığı için yapılandırılmış koşullu erişim ilkesi](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Azure AD kayıtlı Windows cihazlarında cihazda oturum açın bir istem olarak kabul edilir. Örneğin, Office uygulamaları için oturum açma sıklığını 24 saat olarak yapılandırdıysanız, Azure AD kayıtlı Windows cihazlarındaki kullanıcılar cihazda oturum açarak oturum açma sıklığı ilkesini karşılar ve Office uygulamaları açılırken bir daha sorulmayacaktır.

Aynı tarayıcı oturumunda çalışan farklı Web uygulamaları için farklı oturum açma sıklığı yapılandırdıysanız, aynı tarayıcı oturumunda çalışan tüm uygulamalar tek bir oturum belirtecini paylaştığından, her iki uygulama için de en katı ilkesi uygulanır.

### <a name="policy-2-persistent-browser-session"></a>İlke 2: kalıcı tarayıcı oturumu

1. Yeni ilke oluştur
1. Tüm gerekli koşulları seçin.

   > [!NOTE]
   > Lütfen bu denetimin koşul olarak "tüm bulut uygulamaları" seçmesini gerektirdiğini unutmayın. Tarayıcı oturumu kalıcılığı, kimlik doğrulama oturumu belirteci tarafından denetlenir. Bir tarayıcı oturumundaki tüm sekmeler tek bir oturum belirtecini paylaşır ve bu nedenle tümünün kalıcılık durumunu paylaşması gerekir.

1. **Erişim denetimleri** > **oturum** ' ye gidin ve **kalıcı tarayıcı oturumu** ' na tıklayın.
1. Açılan listeden bir değer seçin
1. İlkenizi kaydetme

![Kalıcı tarayıcı için yapılandırılmış koşullu erişim ilkesi](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD koşullu erişim 'de kalıcı tarayıcı oturumu yapılandırması "oturum açmış durumda kalsın" üzerine yazılacak. Her iki ilkeyi de yapılandırdıysanız, aynı kullanıcı için Azure portal Şirket markası bölmesinde ayarlama.

## <a name="validation"></a>Doğrulama

İlkeyi nasıl yapılandırdığınıza bağlı olarak kullanıcıdan hedef uygulamaya ve diğer koşullara bir oturum açma benzetimi yapmak için ne yapılır aracını kullanın. Kimlik doğrulama oturumu yönetimi denetimleri, aracın sonucuna göre görünür.

![Koşullu erişim What If araç sonuçları](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>İlke dağıtımı

İlkenizin beklenildiği gibi çalıştığından emin olmak için önerilen en iyi yöntem, üretime geçmeden önce test sağlamaktır. İdeal olarak, yeni ilkenizin istendiği gibi çalışıp çalışmadığını doğrulamak için bir test kiracısı kullanın. Daha fazla bilgi için [Azure Active Directory Koşullu erişim Için en iyi yöntemler](best-practices.md)makalesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Koşullu erişim ilkesini nasıl yapılandıracağınızı öğrenmek isterseniz, [koşullu erişim Azure Active Directory belirli uygulamalar IÇIN MFA gerektirme](app-based-mfa.md)makalesine bakın.
* Ortamınız için koşullu erişim ilkelerini yapılandırmaya hazırsanız, [Azure Active Directory Koşullu erişim Için en iyi yöntemler](best-practices.md)makalesine bakın.
