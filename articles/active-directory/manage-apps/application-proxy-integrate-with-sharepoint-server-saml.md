---
title: Uygulama proxy 'Si ile şirket içi SharePoint 'te yayımlama-Azure AD
description: ', Şirket içi SharePoint sunucusunu SAML için Azure AD Uygulama Ara Sunucusu tümleştirme hakkında temel bilgileri içerir.'
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34aaafcd03e737b1e59529f8001e0c008bd39b70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104888881"
---
# <a name="integrate-with-sharepoint-saml"></a>SharePoint ile tümleştirme (SAML)

Bu adım adım kılavuzda, kuruluşunuzdaki kullanıcıların (Azure AD, B2B) Internet üzerinden SharePoint 'e bağlanacağı Azure AD Uygulama Ara Sunucusu kullanarak [Azure Active Directory tümleştirilmiş şirket Içi SharePoint 'e (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) erişimin nasıl güvenliği sağlayabileceğiniz açıklanmaktadır.

> [!NOTE] 
> Azure AD Uygulama Ara Sunucusu yeni başladıysanız ve daha fazla bilgi edinmek istiyorsanız bkz. [azure ad uygulama ara sunucusu aracılığıyla şirket içi uygulamalara uzaktan erişim](./application-proxy.md).

Bu kurulumun başlıca üç avantajı vardır:

- Azure AD Uygulama Ara Sunucusu, kimliği doğrulanmış trafiğin iç ağınıza ve SharePoint sunucusuna ulaşmasını sağlar.
- Kullanıcılarınız SharePoint sitelerine VPN kullanmadan her zamanki gibi erişebilir.
- Azure AD Uygulama Ara Sunucusu düzeyinde Kullanıcı atamasına göre erişimi denetleyebilir ve koşullu erişim ve Multi-Factor Authentication (MFA) gibi Azure AD özellikleriyle güvenliği artırabilirsiniz.

Bu işlem iki kurumsal uygulama gerektirir. Bunlardan biri, Galeriden yönetilen SaaS uygulamaları listenize yayımladığınız SharePoint şirket içi bir örneğidir. İkincisi, ilk kurumsal Galeri uygulamasını yayımlamak için kullanacağınız şirket içi bir uygulamadır (Galeri olmayan bir uygulamadır).

## <a name="prerequisites"></a>Önkoşullar

Bu yapılandırmayı tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:
 - SharePoint 2013 grubu veya daha yeni bir sürüm. SharePoint grubu [Azure AD ile tümleşik](../saas-apps/sharepoint-on-premises-tutorial.md)olmalıdır.
 - Uygulama proxy 'Si içeren bir plana sahip bir Azure AD kiracısı. [Azure AD planları ve fiyatlandırma](https://azure.microsoft.com/pricing/details/active-directory/)hakkında daha fazla bilgi edinin.
 - Azure AD kiracısında [özel, doğrulanmış bir etki alanı](../fundamentals/add-custom-domain.md) . Doğrulanan etki alanı SharePoint URL sonekine uymalıdır.
 - SSL sertifikası gerekiyor. [Özel etki alanı yayımlamayla](./application-proxy-configure-custom-domain.md)ayrıntılara bakın.
 - Şirket içi Active Directory kullanıcıların Azure AD Connect ile eşitlenmesi ve [Azure 'da oturum açmak](../hybrid/plan-connect-user-signin.md)için yapılandırılması gerekir. 
 - Yalnızca bulut ve B2B Konuk kullanıcıları için [Azure Portal şirket Içi SharePoint 'e bir Konuk hesabına erişim vermeniz](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)gerekir.
 - Şirket etki alanındaki bir makineye yüklenen ve çalıştırılan bir uygulama proxy Bağlayıcısı.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>1. Adım: SharePoint şirket içi Azure AD ile tümleştirme 

1. SharePoint şirket içi uygulamasını yapılandırın. Daha fazla bilgi için bkz. [öğretici: SharePoint şirket içi ile çoklu oturum açma tümleştirmesi Azure Active Directory](../saas-apps/sharepoint-on-premises-tutorial.md).
2. Sonraki adıma geçmeden önce yapılandırmayı doğrulayın. Doğrulamak için şirket içi SharePoint 'e iç ağdan erişmeyi deneyin ve şirket içinde erişilebilir olduğunu onaylayın. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>2. Adım: SharePoint şirket içi uygulamasını uygulama proxy 'Si ile yayımlama

Bu adımda, Azure AD kiracınızda uygulama proxy 'Si kullanan bir uygulama oluşturacaksınız. Dış URL 'yi ayarlarsınız ve ikisi de SharePoint 'te daha sonra kullanılan iç URL 'YI belirtirsiniz.

> [!NOTE] 
> Iç ve dış URL 'Ler, 1. adımdaki SAML tabanlı uygulama yapılandırmasındaki **oturum açma URL 'siyle** eşleşmelidir.

   ![Oturum açma URL 'SI değerini gösteren ekran görüntüsü.](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Özel etki alanı ile yeni bir Azure AD Uygulama Ara Sunucusu uygulaması oluşturun. Adım adım yönergeler için bkz. [Azure AD uygulama ara sunucusu özel etki alanları](./application-proxy-configure-custom-domain.md).

    - İç URL: ' https://portal.contoso.com/ '
    - Dış URL: ' https://portal.contoso.com/ '
    - Ön kimlik doğrulaması: Azure Active Directory
    - Üst bilgilerdeki URL 'Leri çevir: Hayır
    - Uygulama gövdesinde URL 'Leri çevir: Hayır

        ![Uygulamayı oluşturmak için kullandığınız seçenekleri gösteren ekran görüntüsü.](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Şirket içi SharePoint Galeri uygulamasına atadığınız [grupları](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) atayın.

3. Son olarak, **Özellikler** bölümüne gidin ve **kullanıcılar için görünür** yapın, **Hayır** olarak ayarlayın. Bu seçenek, uygulamalar portalında yalnızca ilk uygulamanın simgesinin görünmesini sağlar ( https://myapplications.microsoft.com) .

   ![Kullanıcılara görünür olarak ayarlananın nerede ayarlanacağını gösteren ekran görüntüsü seçeneği.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>3. Adım: uygulamanızı test etme

Dış ağdaki bir bilgisayardan tarayıcı kullanarak, yayımlama adımı sırasında yapılandırdığınız bağlantıya gidin. Ayarladığınız test hesabıyla oturum açabilmeniz için emin olun.