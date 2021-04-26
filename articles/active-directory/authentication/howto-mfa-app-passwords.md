---
title: Azure AD Multi-Factor Authentication için uygulama parolalarını Yapılandırma-Azure Active Directory
description: Azure AD 'de eski uygulamalar için uygulama parolalarını Yapılandırma ve kullanma hakkında bilgi edinin Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743114"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Uygulama parolalarını kullanarak eski uygulamalarla Azure AD Multi-Factor Authentication'ı etkinleştirme ve kullanma

Office 2010 veya önceki bir sürümü ve iOS 11 ' den önceki Apple Mail gibi bazı eski, tarayıcı olmayan uygulamalar, kimlik doğrulama işleminde duraklamaları veya molaları anlamıyor. Bir Kullanıcı Azure AD Multi-Factor Authentication için etkinleştirilmişse ve bu eski, tarayıcı olmayan uygulamalardan birini kullanmaya çalışırsa, kimlik doğrulaması başarılı bir şekilde doğrulanamazlar. Bu uygulamaları, Azure AD Multi-Factor Authentication Kullanıcı hesapları için etkin olarak güvenli bir şekilde kullanmak için, uygulama parolaları ' nı kullanabilirsiniz. Bu uygulama parolaları, uygulamanın çok faktörlü kimlik doğrulamasını atlayıp doğru şekilde çalışmasını sağlamak için geleneksel parolanızı değiştirdi.

Modern kimlik doğrulaması Microsoft Office 2013 istemcileri ve sonraki sürümler için desteklenir. Outlook dahil Office 2013 istemcileri, modern kimlik doğrulama protokollerini destekler ve iki adımlı doğrulama ile çalışmak üzere etkinleştirilebilir. İstemci etkinleştirildikten sonra istemci için uygulama parolaları gerekli değildir.

Bu makalede, çok faktörlü kimlik doğrulama istemlerini desteklemeyen eski uygulamalar için uygulama parolalarının nasıl etkinleştirileceği ve kullanılacağı gösterilir.

>[!NOTE]
> Uygulama parolaları, koşullu erişim tabanlı Multi-Factor Authentication ilkeleriyle ve modern kimlik doğrulamasıyla çalışmaz.

## <a name="overview-and-considerations"></a>Genel bakış ve noktalar

Bir kullanıcı hesabı Azure AD Multi-Factor Authentication için etkinleştirildiğinde, normal oturum açma istemi ek doğrulama isteği tarafından kesintiye uğrar. Bazı eski uygulamalar, oturum açma sürecinde bu kesmeyi anlamıyor, kimlik doğrulaması başarısız olur. Kullanıcı hesabı güvenliğini sürdürmek ve Azure AD Multi-Factor Authentication 'yi etkin bırakmak için, kullanıcının normal Kullanıcı adı ve parolası yerine uygulama parolaları kullanılabilir. Oturum açma sırasında bir uygulama parolası kullanıldığında, ek bir doğrulama istemi yoktur, bu nedenle kimlik doğrulama başarılı olur.

Uygulama parolaları otomatik olarak oluşturulur, Kullanıcı tarafından belirtilmez. Otomatik olarak oluşturulan bu parola, bir saldırganın tahmin etmesini zorlaştırır, bu nedenle daha güvenlidir. Kullanıcılar, uygulama parolaları her uygulama için yalnızca bir kez girildiğinde parolaları izlemek veya her seferinde onları girmek zorunda kalmaz.

Uygulama parolaları kullandığınızda aşağıdaki noktalar geçerlidir:

* Kullanıcı başına 40 uygulama parolalarından oluşan bir sınır vardır.
* Uygulama parolası iş veya okul hesabı dışında olmadığı için parolaları önbelleğe alan ve bunları şirket içi senaryolarda kullanan uygulamalar başarısız olabilir. Bu senaryoya bir örnek, şirket içi Exchange e-postalarının bir örneğidir, ancak Arşivlenmiş posta bulutta yer alır. Bu senaryoda, aynı parola çalışmaz.
* Azure AD Multi-Factor Authentication bir kullanıcının hesabında etkinleştirildikten sonra, uygulama parolaları Outlook ve Microsoft Skype Kurumsal gibi tarayıcı olmayan birçok istemci ile birlikte kullanılabilir. Ancak, Windows PowerShell gibi tarayıcı olmayan uygulamalar aracılığıyla Uygulama parolaları kullanılarak yönetim eylemleri gerçekleştirilemez. Kullanıcı bir yönetici hesabına sahip olsa bile eylemler gerçekleştirilemez.
    * PowerShell betikleri çalıştırmak için güçlü parolalı bir hizmet hesabı oluşturun ve iki adımlı doğrulama için hesabı etkinleştirmeyin.
* Bir kullanıcı hesabının güvenliğinin aşıldığını ve hesap parolasının iptal/sıfırlama olduğunu düşünüyorsanız, uygulama parolalarının de güncelleştirilmeleri gerekir. Kullanıcı hesabı parolası iptal edildiğinde/sıfırlandığında, uygulama parolaları otomatik olarak iptal edilmez. Kullanıcı var olan uygulama parolalarını silip yenilerini oluşturmalıdır.
   * Daha fazla bilgi için bkz. [ek güvenlik doğrulama sayfasından uygulama parolaları oluşturma ve silme](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

>[!WARNING]
> Uygulama parolaları, istemcilerin hem şirket içi hem de bulut otomatik bulma uç noktaları ile iletişim kurduğu karma ortamlarda çalışmaz. Şirket içinde kimlik doğrulaması yapmak için etki alanı parolaları gereklidir. Bulutta kimlik doğrulaması yapmak için uygulama parolaları gereklidir.

### <a name="app-password-names"></a>Uygulama parolası adları

Uygulama parolası adları, kullanıldıkları aygıtı yansıtmalıdır. Outlook, Word ve Excel gibi tarayıcı olmayan uygulamalar içeren bir dizüstü bilgisayarınız varsa, bu uygulamalar için **dizüstü bilgisayar** adlı bir uygulama parolası oluşturun. Masaüstü bilgisayarınızda çalışan uygulamalar için **Masaüstü** adlı başka bir uygulama parolası oluşturun.

Her uygulama için bir uygulama parolası yerine her cihaz için bir uygulama parolası oluşturmanız önerilir.

## <a name="federated-or-single-sign-on-app-passwords"></a>Federasyon veya çoklu oturum açma uygulama parolaları

Azure AD, Federasyon veya çoklu oturum açma (SSO) ile şirket içi Active Directory Domain Services (AD DS) destekler. Kuruluşunuz Azure AD ile federe ise ve Azure AD Multi-Factor Authentication kullanıyorsanız, aşağıdaki uygulama parolası konuları geçerlidir:

>[!NOTE]
> Aşağıdaki noktaları yalnızca Federal (SSO) müşteriler için geçerlidir.

* Uygulama parolaları Azure AD tarafından doğrulanır ve bu nedenle, Federasyonu atlayın. Federasyon yalnızca uygulama parolaları ayarlanırken etkin bir şekilde kullanılır.
* Pasif akışın aksine, kimlik sağlayıcısı (IDP), Federasyon (SSO) kullanıcıları için bağlantı kurulmadı. Uygulama parolaları iş veya okul hesabında depolanır. Bir kullanıcı şirketten ayrılırsa, kullanıcının bilgileri, **DirSync** 'i gerçek zamanlı olarak kullanarak iş veya okul hesabına akar. Hesabı devre dışı bırakma/silme işlemi üç saate kadar sürebilir ve bu, Azure AD 'de uygulama parolasının devre dışı/silinmesini erteleyebilir.
* Şirket içi istemci Access Control ayarları uygulama parolaları özelliği tarafından kabul edilmez.
* Uygulama parolaları özelliği ile şirket içi kimlik doğrulaması günlüğü veya denetim yeteneği kullanılamaz.

Bazı gelişmiş mimariler, istemcilerle Multi-Factor Authentication için kimlik bilgilerinin bir birleşimini gerektirir. Bu kimlik bilgileri, iş veya okul hesabı Kullanıcı adı ve parolaları ile uygulama parolalarını içerebilir. Gereksinimler, kimlik doğrulamanın nasıl gerçekleştirilebileceğini temel alır. Şirket içi altyapıya karşı kimlik doğrulaması yapan istemciler için bir iş veya okul hesabı Kullanıcı adı ve parola gerekir. Azure AD kimlik doğrulaması yapan istemciler için bir uygulama parolası gerekir.

Örneğin, aşağıdaki mimarinin olduğunu varsayalım:

* Şirket içi Active Directory örneğiniz Azure AD ile federe olarak sağlanır.
* Exchange Online 'ı kullanıyorsunuz.
* Skype Kurumsal Şirket içi 'ı kullanıyorsunuz.
* Azure AD Multi-Factor Authentication kullanırsınız.

Bu senaryoda, aşağıdaki kimlik bilgilerini kullanırsınız:

* Skype Kurumsal 'da oturum açmak için iş veya okul hesabınızın Kullanıcı adı ve parolasını kullanın.
* Exchange Online 'a bağlanan bir Outlook istemcisinden adres defterine erişmek için bir uygulama parolası kullanın.

## <a name="allow-users-to-create-app-passwords"></a>Kullanıcıların uygulama parolaları oluşturmalarına izin ver

Varsayılan olarak, kullanıcılar uygulama parolaları oluşturamaz. Kullanıcıların bunları kullanabilmesi için uygulama parolaları özelliğinin etkinleştirilmesi gerekir. Kullanıcılara uygulama parolaları oluşturma yeteneği vermek için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Azure Active Directory** arayıp seçin ve ardından **Kullanıcılar**' ı seçin.
3. Gezinti çubuğundan *Kullanıcılar* penceresinin üst kısmında **Multi-Factor Authentication** ' yi seçin.
4. Multi-Factor Authentication altında **hizmet ayarları**' nı seçin.
5. **Hizmet ayarları** sayfasında, **kullanıcıların tarayıcı olmayan uygulamalara oturum açmak için uygulama parolaları oluşturmasına izin ver** seçeneğini belirleyin.

    ![Uygulama parolalarının kullanıcısına izin vermek için çok faktörlü kimlik doğrulamasının hizmet ayarlarını gösteren Azure portal ekran görüntüsü](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> Kullanıcıların uygulama parolaları oluşturma yeteneğini devre dışı bıraktığınızda, mevcut uygulama parolaları çalışmaya devam eder. Ancak, bu özelliği devre dışı bıraktığınızda kullanıcılar mevcut uygulama parolalarını yönetemez veya silemez.
>
> Uygulama parolaları oluşturma özelliğini devre dışı bıraktığınızda, [eski kimlik doğrulamasının kullanımını devre dışı bırakmak için bir koşullu erişim ilkesi oluşturmanız](../conditional-access/block-legacy-authentication.md)da önerilir. Bu yaklaşım, mevcut uygulama parolalarının çalışmasını önler ve modern kimlik doğrulama yöntemlerinin kullanımını zorlar.

## <a name="create-an-app-password"></a>Uygulama parolası oluşturma

Kullanıcılar Azure AD Multi-Factor Authentication ilk kayıtlarını tamamlarından, kayıt işleminin sonunda uygulama parolaları oluşturma seçeneği vardır.

Kullanıcılar, kayıt sonrasında Uygulama parolaları da oluşturabilir. Kullanıcılarınıza yönelik daha fazla bilgi ve ayrıntılı adımlar için bkz. [Azure AD Multi-Factor Authentication 'de uygulama parolaları nelerdir?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Sonraki adımlar

Kullanıcıların Azure AD Multi-Factor Authentication hızlı bir şekilde kaydetmesine izin verme hakkında daha fazla bilgi için bkz. [Birleşik güvenlik bilgileri kaydına genel bakış](concept-registration-mfa-sspr-combined.md).
