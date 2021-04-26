---
title: Uygulama proxy 'Si kullanarak şirket içi bir uygulamayla çoklu oturum açmayı anlama
description: Uygulama proxy 'Si kullanarak şirket içi bir uygulamayla çoklu oturum açmayı anlayın.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kenwith
ms.reviewer: japere, asteen
ms.openlocfilehash: 10b722edbe8d70c92e617c78db3d2fb1d46da3a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254113"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Uygulama Ara Sunucusu uygulamasında çoklu oturum açmayı yapılandırma

Çoklu oturum açma (SSO), kullanıcılarınızın birden çok kez kimlik doğrulaması yapmadan bir uygulamaya erişmesine olanak sağlar. Tek kimlik doğrulamanın bulutta oluşmasına, Azure Active Directory karşı, hizmetin veya bağlayıcının uygulamadan ek kimlik doğrulama sorunlarını tamamlaması için kullanıcının kimliğine bürünmesine izin verir.

## <a name="how-to-configure-single-sign-on"></a>Çoklu oturum açmayı yapılandırma
SSO 'yu yapılandırmak için öncelikle uygulamanızın Azure Active Directory aracılığıyla ön kimlik doğrulaması için yapılandırıldığından emin olun. Bu yapılandırmayı yapmak için,   - &gt;   - &gt;   - &gt; uygulama **- &gt; uygulamanızın ara sunucusunu** Azure Active Directory Kurumsal uygulamalar tüm uygulamalar ' a gidin. Bu sayfada, "ön kimlik doğrulama" alanını görürsünüz ve "Azure Active Directory olarak ayarlanmış olduğundan emin olun. 

Ön kimlik doğrulama yöntemleri hakkında daha fazla bilgi için bkz. [uygulama yayımlama belgesi](application-proxy-add-on-premises-application.md)adım 4.

   ![Azure portal 'de ön kimlik doğrulama yöntemi](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Uygulama Proxy uygulamaları için çoklu oturum açma modlarını yapılandırma
Belirli bir çoklu oturum açma türünü yapılandırın. Oturum açma yöntemleri, arka uç uygulamasının kullandığı kimlik doğrulama türüne göre sınıflandırılır. Uygulama Proxy uygulamaları üç tür oturum açmayı destekler:

-   **Parola tabanlı oturum** açma: parola tabanlı oturum açma, oturum açmak için Kullanıcı adı ve parola alanlarını kullanan herhangi bir uygulama için kullanılabilir. Yapılandırma adımları, [bir Azure AD Galeri uygulaması için parola yapılandırma çoklu oturum açma](configure-password-single-sign-on-non-gallery-applications.md)bölümünde bulunur.

-   **Tümleşik Windows kimlik doğrulaması**: tümleşik Windows kimlik doğrulaması (IWA) kullanan uygulamalar için, tek oturum açma, Kerberos kısıtlanmış temsili (KCD) aracılığıyla etkinleştirilir. Bu yöntem, kullanıcıların kimliğine bürünmek ve adına belirteç göndermek ve almak için Active Directory 'de uygulama ara sunucu bağlayıcıları izni verir. KCD 'yi yapılandırmaya ilişkin ayrıntılar, [KCD belgelerinin bulunduğu tek Sign-On](application-proxy-configure-single-sign-on-with-kcd.md)bulunabilir.

-   **Üst bilgi tabanlı oturum** açma: üst bilgi tabanlı oturum açma, http üst bilgilerini kullanarak çoklu oturum açma özellikleri sağlamak için kullanılır. Daha fazla bilgi için bkz. [üst bilgi tabanlı çoklu oturum açma](application-proxy-configure-single-sign-on-with-headers.md).

-   **SAML çoklu oturum** açma: SAML çoklu oturum açma Ile Azure AD, kullanıcının Azure AD hesabını kullanarak uygulamanın kimliğini doğrular. Azure AD, oturum açma bilgilerini bir bağlantı protokolü aracılığıyla uygulamayla iletişim kurar. SAML tabanlı çoklu oturum açma sayesinde, SAML talepleriniz içinde tanımladığınız kurallara göre kullanıcıları belirli uygulama rolleriyle eşleyebilirsiniz. SAML çoklu oturum açmayı ayarlama hakkında daha fazla bilgi için bkz. [uygulama proxy 'si ile çoklu oturum açma Için SAML](application-proxy-configure-single-sign-on-on-premises-apps.md).

Bu seçeneklerin her biri, uygulamanıza "kurumsal uygulamalar" ile gidip Sol menüdeki **Çoklu oturum açma** sayfasını açıp bulabilirsiniz. Uygulamanız eski portalda oluşturulduysa, tüm bu seçenekleri görmeyebilirsiniz.

Bu sayfada Ayrıca bir ek Sign-On seçeneği görürsünüz: bağlantılı oturum açma. Bu seçenek, uygulama proxy 'Si tarafından da desteklenir. Ancak, bu seçenek uygulamaya çoklu oturum açma eklemez. Bu, uygulamanın Active Directory Federasyon Hizmetleri (AD FS) gibi başka bir hizmet kullanarak zaten çoklu oturum açma özellikli olabileceğini gösterebilir. 

Bu seçenek, yöneticinin uygulamaya erişirken kullanıcı tarafından ilk kez oturum açmış bir uygulamaya bağlantı oluşturmasını sağlar. Örneğin, Active Directory Federasyon Hizmetleri (AD FS) 2,0 kullanarak kullanıcıların kimliğini doğrulamak için yapılandırılmış bir uygulama varsa, yönetici uygulamamda buna bir bağlantı oluşturmak için "bağlantılı oturum açma" seçeneğini kullanabilir.

## <a name="next-steps"></a>Sonraki adımlar
- [Uygulama proxy 'Si ile çoklu oturum açma için parola oluşturma](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Uygulama proxy 'Si ile çoklu oturum açma için Kerberos kısıtlanmış temsili](application-proxy-configure-single-sign-on-with-kcd.md)
- [Uygulama proxy 'Si ile çoklu oturum açma için üst bilgi tabanlı kimlik doğrulaması](application-proxy-configure-single-sign-on-with-headers.md) 
- [Uygulama proxy 'si ile çoklu oturum açma Için SAML](application-proxy-configure-single-sign-on-on-premises-apps.md).
