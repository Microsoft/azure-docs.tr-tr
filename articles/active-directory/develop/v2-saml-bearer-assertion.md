---
title: Microsoft Identity platform ve SAML taşıyıcı onaylama akışı | Mavisi
description: SAML taşıyıcı onaylama akışını kullanarak kullanıcıdan kimlik bilgilerini istemeden Microsoft Graph verileri nasıl alabileceğinizi öğrenin.
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13b316568ba555de764c1aaa4ddf0e72d25cf24f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990955"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft Identity platform ve OAuth 2,0 SAML taşıyıcı onaylama akışı
OAuth 2,0 SAML taşıyıcı onaylama akışı, bir istemcinin var olan bir güven ilişkisini kullanması gerektiğinde bir SAML onaylama işlemi kullanarak bir OAuth erişim belirteci istemesini sağlar. SAML onaylaması 'na uygulanan imza, yetkili uygulamanın kimlik doğrulamasını sağlar. SAML onaylama, bir kimlik sağlayıcısı tarafından verilen ve bir hizmet sağlayıcısı tarafından tüketilen bir XML güvenlik belirtecidir. Hizmet sağlayıcı, güvenlikle ilgili amaçlar için onay konusunun konusunu belirlemek üzere içeriğine bağımlıdır.

SAML onaylama, OAuth belirteci uç noktasına gönderilir.  Uç nokta onay işlemini işler ve uygulamanın önceki onayına göre bir erişim belirteci yayınlar. İstemcinin bir yenileme belirteci olması veya depolaması veya belirteç uç noktasına geçirilmesi gereken istemci sırrı olması gerekmez.

SAML taşıyıcı onaylama akışı, kullanıcıdan kimlik bilgileri istemeden Microsoft Graph API 'lerden (yalnızca temsilci izinleri destekler) veri getirilirken faydalıdır. Bu senaryoda, arka plan işlemlerinde tercih edilen istemci kimlik bilgileri verme işlemi çalışmaz.

Bir SAML onaylama işlemi almak ve ardından OAuth korumalı bir API 'ye (Microsoft Graph gibi) erişim eklemek için etkileşimli tarayıcı tabanlı oturum açma kullanan uygulamalar için, API için bir erişim belirteci almak üzere bir OAuth isteği yapabilirsiniz. Tarayıcı, kullanıcının kimliğini doğrulamak için Azure AD 'ye yeniden yönlendirildiğinde, tarayıcı, SAML oturum açma işleminden oturumu seçer ve kullanıcının kimlik bilgilerini girmesi gerekmez.

OAuth SAML taşıyıcı onaylama akışı, Azure Active Directory Federal Active Directory Federasyon Hizmetleri (AD FS) (ADFS) gibi kimlik sağlayıcılarıyla kimlik doğrulaması yapan kullanıcılar için de desteklenir.  ADFS 'den edinilen SAML onaylama işlemi, kullanıcının kimliğini doğrulamak için bir OAuth akışında kullanılabilir.

![OAuth akışı](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>SAML taşıyıcı onaylama kullanarak Graph çağırma
Artık SAML onaylama program aracılığıyla 'ı gerçekten nasıl getirdiğimiz hakkında anladık. Bu yaklaşım, ADFS ile test edilmiştir. Ancak, bu, SAML onaylama program aracılığıyla dönüşü destekleyen herhangi bir kimlik sağlayıcısıyla birlikte kullanılır. Temel süreç: bir SAML onaylama işlemi alın, erişim belirteci alın ve Microsoft Graph erişin.

### <a name="prerequisites"></a>Önkoşullar

Yetkilendirme sunucusu/ortam (Microsoft 365) ile kimlik sağlayıcısı ya da SAML 2,0 taşıyıcı onaylama (ADFS) veren arasında bir güven ilişkisi oluşturun. ADFS 'yi çoklu oturum açma ve kimlik sağlayıcısı olarak yapılandırmak için [Bu makaleye](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)başvurabilirsiniz.

Uygulamayı [portala](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)kaydedin:
1. [Portalın uygulama kaydı dikey penceresinde](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) oturum açın (lütfen Graph API için v 2.0 uç noktalarını kullandığınızı ve bu nedenle uygulamayı Bu portalda kaydetmesi gerektiğini unutmayın. Aksi halde Azure Active Directory 'de kayıtları kullandık. 
1. **Yeni kayıt**seçeneğini belirleyin.
1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin: 
    1. **Ad** - Uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin.
    1. **Desteklenen hesap türleri** - Uygulamanızın desteklemesini istediğiniz hesapları seçin.
    1. **Yeniden yönlendirme URI 'si (isteğe bağlı)** -oluşturmakta olduğunuz uygulamanın türünü, Web veya ortak istemciyi (mobil & Masaüstü) seçin ve ardından uygulamanızın YENIDEN yönlendirme URI 'sini (veya yanıt URL 'si) girin.
    1. Bittiğinde **Kaydet**’i seçin.
1. Uygulama (istemci) KIMLIĞINI bir yere getirin.
1. Sol bölmede **sertifikalar & gizlilikler**' ı seçin. **İstemci** gizli dizileri bölümünde **yeni istemci parolası** ' na tıklayın. Yeni istemci parolasını kopyalayarak dikey pencereyi bıraktığınızda alamazsınız.
1. Sol bölmede, **API izinleri** ' ni seçin ve **izin ekleyin**. **Microsoft Graph**ve ardından **temsilci izinleri**' ni seçin ve ardından **Görevler. read** ' i seçerek Outlook Graph API kullanmayı planladık. 

Örnek istekleri test etmek için gereken bir araç olan [Postman](https://www.getpostman.com/)'ı yükleme.  Daha sonra, istekleri koda dönüştürebilirsiniz.

### <a name="get-the-saml-assertion-from-adfs"></a>ADFS 'den SAML onaylama işlemi al
SAML onaylama işlemini getirmek için SOAP Zarfı kullanarak ADFS uç noktasına bir POST isteği oluşturun:

![SAML onaylama al](./media/v2-saml-bearer-assertion/2.png)

Üst bilgi değerleri:

![Üst bilgi değerleri](./media/v2-saml-bearer-assertion/3.png)

ADFS istek gövdesi:

![ADFS istek gövdesi](./media/v2-saml-bearer-assertion/4.png)

Bu istek başarıyla gönderildikten sonra, ADFS 'den bir SAML onaylama işlemi almalısınız. Yalnızca **saml: onaylama** etiketi verileri gereklidir, daha fazla istekte kullanmak için Base64 kodlamaya dönüştürün.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>SAML onaylama işlemi kullanarak OAuth2 belirtecini alın 
Bu adımda, ADFS onaylama yanıtını kullanarak bir OAuth2 belirteci getirin.

1. Aşağıda gösterildiği gibi, üst bilgi değerleriyle bir POST isteği oluşturun:

    ![POST isteği](./media/v2-saml-bearer-assertion/5.png)
1. İsteğin gövdesinde, **client_id**, **client_secret**ve **assertion** (ÖNCEKI adımı elde edilen base64 kodlu SAML onaylama) öğesini değiştirin:

    ![İstek gövdesi](./media/v2-saml-bearer-assertion/6.png)
1. İstek başarıyla tamamlandığında, Azure Active Directory 'den bir erişim belirteci alacaksınız.

### <a name="get-the-data-with-the-oauth-token"></a>OAuth belirteci ile verileri al

Erişim belirtecini aldıktan sonra, Graph API 'Lerini (Bu örnekteki Outlook görevleri) çağırın. 

1. Önceki adımda getirilen erişim belirtecine sahip bir GET isteği oluşturun:

    ![İsteği al](./media/v2-saml-bearer-assertion/7.png)

1. Başarılı istek üzerine bir JSON yanıtı alırsınız.

## <a name="next-steps"></a>Sonraki adımlar

Farklı [kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md)hakkında bilgi edinin.