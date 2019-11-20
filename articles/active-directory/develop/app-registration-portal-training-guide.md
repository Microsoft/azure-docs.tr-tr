---
title: Uygulama kayıt portalından Azure portal yeni Uygulama kayıtları deneyimine geçme
titleSuffix: Microsoft identity platform
description: Azure portal yeni uygulama kayıt deneyimine giriş
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: aragra
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e9c09ce2d997c58e2725c411fff0b87a0f5071b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013019"
---
# <a name="transitioning-from-application-registration-portal-to-the-new-app-registrations-experience-in-the-azure-portal"></a>Uygulama kayıt portalından Azure portal yeni Uygulama kayıtları deneyimine geçme

Azure portal yeni [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminde birçok iyileştirme vardır. Eski deneyim olarak adlandırılan yakınsama uygulamalarını kaydetme veya yönetme ile ilgili uygulama kayıt portalı (apps.dev.microsoft.com) deneyimiyle daha fazla bilginiz varsa, bu eğitim kılavuzu yeni deneyimi kullanmaya başlamanızı sağlar.

## <a name="whats-not-changing"></a>Ne değiştirmiyor?

-   Uygulamalarınız ve ilgili yapılandırmalar yeni deneyimde olduğu gibi bulunabilir. Uygulamaları yeniden kaydetmeniz gerekmez ve uygulamalarınızın kullanıcılarının yeniden oturum açması gerekmez.

    > [!NOTE]
    > Azure portal bulmak için, uygulamaları kaydetmek üzere kullandığınız hesapla oturum açmanız gerekir. Azure portal oturum açmış kullanıcının, e-posta adresini profilinizden karşılaştırarak, uygulama kayıt portalı 'nda oturum açan kullanıcıyla aynı olduğunu kontrol etmenizi öneririz.
    > 
    > Bazı durumlarda, özellikle de bir Azure AD e-posta adresi ile kişisel Microsoft hesapları (örn. Outlook, canlı, Xbox vb.) kullanarak oturum açtığınızda, eski deneyimden Azure portal giderek, size aynı olan farklı bir hesaba kaydolduğumuzdan Azure AD kiracınızda e-posta. Uygulamalarınızın eksik olduğunu düşünüyorsanız, oturumunuzu kapatıp doğru hesapla oturum açın.

-   Kişisel Microsoft hesapları kullanılarak oluşturulan canlı SDK uygulamaları Azure portal henüz desteklenmemektedir ve yakın gelecekte eski deneyimde olmaya devam edecektir.

## <a name="key-changes"></a>Anahtar değişiklikleri

-   Eski deneyimde, uygulamalar varsayılan olarak, tüm kuruluş hesaplarını (çok kiracılı) ve kişisel Microsoft hesaplarını destekleyen yakınsanmış uygulamalar olarak kaydedilmıştı. Bu, eski deneyimle değiştirilemedi, yalnızca kurumsal hesapların (çok kiracılı veya tek bir kiracı) desteklendiği uygulamalar oluşturulmasını zorlaştırır.
    Yeni deneyim, tüm bu seçenekleri destekleyen uygulamaları kaydetmenizi sağlar. [Uygulama türleri hakkında daha fazla bilgi edinin](active-directory-v2-registration-portal.md).

-   Yeni deneyimde, kişisel Microsoft hesabı de bir Azure AD kiracısında varsa, Kiracıdaki tüm uygulamalar, Kiracıdaki uygulamaların yanı sıra kişisel hesabınızdaki uygulamalar da görürsünüz. Bu nedenle, kişisel Microsoft hesabı kayıtlı uygulamaların eksik olduğunu düşünüyorsanız, **kişisel hesap Sekmesinizdeki uygulamaları** kontrol edin.

-   Yeni deneyimde, profilinize gidip Dizin Değiştir ' i seçerek kiracılar arasında kolayca geçiş yapabilirsiniz.

## <a name="list-of-applications"></a>Uygulama listesi

-   Yeni uygulama listesi, Azure portal eski uygulama kayıtları deneyimiyle (yalnızca Azure AD hesaplarında oturum açma uygulamaları) ve [uygulama kayıt portalı](https://apps.dev.microsoft.com/) (hem Azure AD hem de kişisel Microsoft hesaplarında oturum açma uygulamaları) kayıtlı uygulamaları gösterir.

-   Yeni uygulama listesinde iki **ek sütun bulunur: uygulamada** kayıtlı olan kimlik bilgilerinin durumunu (geçerli, süresi yakında veya süresi dolan) gösteren sütunlar ve **Sertifikalar &** .

## <a name="new-app-registration"></a>Yeni uygulama kaydı

Eski deneyimde, Yakınsanan bir uygulamayı kaydetmek için yalnızca bir ad sağlamanız gerekir. Oluşturulan uygulamalar, tüm kuruluş dizinini (çok kiracılı) ve kişisel Microsoft hesaplarını destekleyen yakınsanmış uygulamalar olarak kaydedilmiş.  Bu, eski deneyimle değiştirilemedi, yalnızca kurumsal hesapların (çok kiracılı veya tek bir kiracı) desteklendiği uygulamalar oluşturulmasını zorlaştırır. [Desteklenen hesap türleri hakkında daha fazla bilgi edinin](v2-supported-account-types.md)

Yeni deneyimde uygulama için bir ad belirtmeniz ve desteklenen hesap türlerini seçmeniz gerekir. İsteğe bağlı olarak bir yeniden yönlendirme URI 'SI sağlayabilirsiniz.
Yeniden yönlendirme URI 'SI sağlarsanız, bunun Web/genel (yerel/mobil ve Masaüstü) olup olmadığını belirtmeniz gerekir. Yeni uygulama kaydı deneyimini kullanarak bir uygulamayı kaydetme hakkında daha fazla bilgi için [Bu hızlı başlangıç](quickstart-register-app.md)bölümüne bakın.

## <a name="app-management-page"></a>Uygulama Yönetimi sayfası

Eski deneyim, yakınsama uygulamaları için şu bölümlerle tek bir uygulama yönetimi sayfasına sahipti: özellikler, uygulama gizli dizileri, platformlar, sahipler Microsoft Graph Izinler, profil ve Gelişmiş Seçenekler.

Azure portal yeni deneyim, bu özellikleri ayrı sayfalarda temsil eder. Eşdeğer işlevselliği bulabileceğiniz yer:

-   Özellikler-ad ve uygulama KIMLIĞI genel bakış sayfasıdır.

-   Uygulama gizli dizileri, Sertifikalar & gizlilikler sayfasında bulunur

-   Platformlar yapılandırması, kimlik doğrulama sayfamı

-   Microsoft Graph izinler, diğer izinlerle birlikte API izinleri sayfasında bulunur

-   Profil marka sayfasında

-   Gelişmiş seçenek-Live SDK desteği, kimlik doğrulama sayfasıdır.

## <a name="application-secretscertificates--secrets"></a>Gizli dizi & uygulama gizli dizileri/sertifikaları

Yeni deneyimde, **uygulama gizli** dizileri **sertifikalarla &** yeniden adlandırıldı. Bunlara ek olarak, **ortak anahtarlar** **sertifika** ve **parolalar** **istemci gizli**dizileri olarak adlandırılır. Güvenlik nedenleriyle bu işlevselliği yeni deneyimde getirmemeyi seçtik, bu nedenle artık yeni bir anahtar çifti üretilemez.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformlar/kimlik doğrulaması: yanıt URL 'Leri/yeniden yönlendirme URI 'leri
Eski deneyimde bir uygulamada Web, yerel ve Web API 'SI için platformlar bölümü, yeniden yönlendirme URL 'Leri, oturum kapatma URL 'SI ve örtük akış yapılandırılması gerekir.

Yeni deneyimde, yanıt URL 'Leri bir uygulama\'s kimlik doğrulama bölümünde bulunabilir. Bunlara ek olarak, yeniden yönlendirme URI 'Leri olarak adlandırılır ve yeniden yönlendirme URI 'Leri biçimi değişmiştir. Bunların bir uygulama türüyle ilişkilendirilmesi gerekir (Web veya genel istemci-mobil ve Masaüstü). [Daha fazla bilgi](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Web API 'Leri bir API 'YI kullanıma sunma sayfasında yapılandırılır.

> [!NOTE] 
> Hedeflemek istediğiniz platforma veya cihaza göre uygulamanızın ayarlarını yapılandırabileceğiniz yeni kimlik doğrulama ayarları deneyimini deneyin. [Daha fazla bilgi](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Microsoft Graph izinleri/API izinleri

-   Eski deneyimde bir API seçerken yalnızca Microsoft Graph API 'Leri seçebilirsiniz. Yeni deneyimde Microsoft Graph, kuruluşunuzdaki ve API 'lerinizin API 'Leri gibi birçok Microsoft API arasından seçim yapabilirsiniz, bu üç sekmede sunulmaktadır: Microsoft API 'leri, Kuruluşumun kullandığı API 'ler veya API 'Lerim. API 'lerim üzerindeki arama çubuğu, Kiracıdaki hizmet sorumluları aracılığıyla sekme aramalarını kullanır.
    
    > [!NOTE] 
    > Uygulamanız bir kiracı ile ilişkili değilse bu sekmeyi görmezsiniz. Yeni deneyimi kullanarak izin isteme hakkında daha fazla bilgi için [Bu hızlı başlangıç](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)bölümüne bakın.

-   Eski deneyimde **Izin verme** düğmesi yoktu. Yeni deneyimde, bir uygulamanın API izinleri bölümünde **yönetici Izni verme** düğmesine sahip bir izin ver bölümü vardır. Yalnızca bir yönetici onay verebilir ve bu düğme yalnızca Yöneticiler için etkinleştirilmiştir. Bir yönetici **yönetici onayı ver** düğmesini seçtiğinde, istenen tüm izinlere yönetici izni verilir.

## <a name="profile"></a>Profil
Eski deneyimde profilde logo, giriş sayfası URL 'si, hizmet koşulları URL 'SI ve gizlilik bildirimi URL 'SI yapılandırması vardı. Yeni deneyimde, bu, marka sayfasında bulunabilir.

## <a name="application-manifest"></a>Uygulama bildirimi
Yeni deneyimde bildirim sayfası, uygulamanın özniteliklerini düzenlemenize ve güncelleştirmenize olanak tanır. Daha fazla bilgi için bkz. [uygulama bildirimi](reference-app-manifest.md).

## <a name="new-ui"></a>Yeni Kullanıcı arabirimi
Daha önce yalnızca bildirim Düzenleyicisi veya API kullanılarak ayarlanan özellikler için yeni kullanıcı arabirimi var veya yok.

-   Örtük verme akışı (oauth2AllowImplicitFlow) kimlik doğrulama sayfasında bulunabilir. Eski deneyimden farklı olarak, erişim belirteçlerini veya KIMLIK belirteçlerini veya her ikisini de etkinleştirebilirsiniz.

-   Bu API (oauth2Permissions) ve yetkili istemci uygulamaları (ön kimlik doğrulama) tarafından tanımlanan kapsamlar, bir API 'YI kullanıma sunma sayfasından yapılandırılabilir. Bir uygulamayı Web API 'SI olarak yapılandırma ve izinleri/kapsamları kullanıma sunma hakkında daha fazla bilgi için [Bu hızlı başlangıç](quickstart-configure-app-expose-web-apis.md)bölümüne bakın.

-   Yayımcı etki alanı ( [uygulama\'s onay isteminde](application-consent-experience.md)kullanıcılara gösterilir) marka dikey penceresinde bulunabilir. Yayımcı etki alanını yapılandırma hakkında daha fazla bilgi için, bkz. [nasıl yapılır](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Sınırlamalar

Yeni deneyim aşağıdaki sınırlamalara sahiptir:

-   Yeni deneyim, Azure AD B2C kiracılar için Uygulama kayıtları henüz desteklememektedir.

-   Yeni deneyim, kişisel Microsoft hesaplarıyla oluşturulan canlı SDK uygulamalarını henüz desteklememektedir.

-   Desteklenen hesapların değerini değiştirmek Kullanıcı arabiriminde desteklenmez. Azure AD tek kiracılı ve çok kiracılı arasında geçiş\'için, uygulama bildirimini kullanmanız gerekir.

   > [!NOTE]
   > Azure AD kiracısında kişisel Microsoft hesabı bir Kullanıcı ve kiracı yöneticisinin Azure portal erişimi kısıtlanmışsa, erişim reddedildi. Ancak, arama çubuğuna Uygulama kayıtları yazarak veya sabitleyerek kısayoldan geliyorsa yeni deneyimle erişebilirsiniz.
