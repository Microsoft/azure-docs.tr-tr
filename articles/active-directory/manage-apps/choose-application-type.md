---
title: Uygulama eklerken hangi uygulama türünün kullanılacağını seçme | Microsoft Docs
description: Azure AD ile tümleştirebileceğinizi desteklenen uygulama türlerini ve bunlarla ilgili yapılandırma seçeneklerini anlayın
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: d5bd2397c345a4f670bde343f751cd69f825ecb9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056073"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Azure Active Directory bir uygulama eklerken uygulama türünü seçme

Azure Active Directory (Azure AD) için ekleyebileceğiniz dört tür uygulama hakkında bilgi edinin. Azure Active Directory bir uygulama eklerken, dört uygulama türünden birini seçmeniz istenir.

## <a name="what-are-the-types-of-applications"></a>Uygulama türleri nelerdir?

Azure AD, **Kurumsal uygulamalar**altında bulunan **Ekle** özelliğini kullanarak ekleyebileceğiniz dört ana uygulama türünü destekler. Bunlar:

- **Azure AD Galeri uygulamaları** – Azure AD ile çoklu oturum açma için önceden tümleştirilmiş bir uygulamadır.

- **Uygulama proxy 'Si uygulamaları** – dışarıdan güvenli çoklu oturum açma sağlamak istediğiniz şirket içi ortamınızda çalışan bir uygulamadır.

- **Özel geliştirmiş uygulamalar** : KURULUŞUNUZUN Azure AD uygulama geliştirme platformunda geliştirme istediği, ancak henüz mevcut olmayan bir uygulamadır.

- **Galeri olmayan uygulamalar** – kendi uygulamalarınızı getirin! İstediğiniz herhangi bir Web bağlantısı veya bir Kullanıcı adı ve parola içeren herhangi bir uygulama, SAML veya OpenID Connect protokollerini destekler ya da Azure AD ile çoklu oturum açma için tümleştirmesini istediğiniz SCıM 'yi destekler.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Uygulama türleri tarafından desteklenen özellikler ve yetenekler

Aşağıdaki özellikler, Azure AD 'de önceki dört uygulama türünden herhangi biri tarafından desteklenir:

- **Hızlı başlangıç** – [Basit Dağıtım adımlarını](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started) izleyerek bir uygulamayla hızla çalışmaya başlayın

- **Genel Özellikler yönetimi** – bir uygulamaya [doğrudan derin bir bağlantı](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) alın, bir uygulamanın [markasını özelleştirin](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) veya tüm kullanıcılar için [uygulamayı devre dışı bırakın](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) .

- **Kullanıcı ve Grup Yönetimi** – bir uygulamaya kullanıcılar ve gruplar [atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) veya [kaldırma](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) , isteğe bağlı olarak bu kullanıcılar ve grupların erişimi olan belirli uygulama rollerini atama

- **Self servis uygulama erişimi** – kullanıcılarınızın doğrudan bir uygulama ekleyerek veya self servis etkinleştirilmiş bir gruba katılarak uygulama erişim panellerinden bir uygulamaya [self servis uygulama erişimi](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) istemesine olanak tanır [ ](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), isteğe bağlı olarak bu şekilde iş onayı gerektirir

- **Oturum açma günlükleri** – [tüm oturum açma işlemlerini bir uygulamaya](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)veya tüm uygulamalarınıza bakın

- **Denetim günlükleri** – bir uygulamada veya tüm uygulamalarınızda [yapılan değişikliklerle ilgili ayrıntılı denetim günlüklerine](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)bakın

- **Koşullu ve risk tabanlı erişim** – kullanıcılar belirli bir uygulamada oturum açmaya çalıştığında zorlanan, izin tabanlı güçlü [erişim kuralları](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) ayarlayın

- **İzinler görünümü** – bir uygulamanın dizininizde tek bir yerden erişebileceği [OAuth2 izinlerinden](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) herhangi birini görüntüleyin

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Belirli uygulama türleri tarafından desteklenen çoklu oturum açma ve sağlama modları

Aşağıdaki tabloda, bir önceki uygulama türlerinin her biri tarafından desteklenen farklı çoklu oturum açma ve sağlama modları açıklanmaktadır. Bu tabloyu, belirli bir hedefi desteklemek için hangi uygulamayı eklemeniz gerektiğini anlamanıza yardımcı olması için kullanabilirsiniz.

  ![Tablosundan Her uygulama türü tarafından desteklenen farklı SSO ve sağlama modları](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Çoklu oturum açma modunu seçme

Azure AD uygulamaları için desteklenen **Çoklu oturum açma** modları aşağıda verilmiştir.

- **Azure AD çoklu oturum açma devre dışı** – bu uygulamayı Azure AD ile çoklu oturum açma ile tümleştirmeye hazır değilseniz veya yalnızca test etmeyi bilmiyorsanız, Azure AD çoklu oturum açma devre dışı **Çoklu oturum açma modunu** seçin.

- **Bağlantılı oturum açma** – mevcut bir çoklu oturum açma çözümüyle zaten bağlı olan bir uygulamanız varsa veya kullanıcılarınız [](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) [için yalnızca basit bir bağlantı yayımlamak istiyorsanız, bağlantılı oturum açma çoklu oturum açma modunu seçin. Uygulama erişim paneli](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) veya [Office 365 uygulama başlatıcısı](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Parola tabanlı oturum açma** – UYGULAMANıZ bir HTML Kullanıcı adı ve parola alanı işliyorsa ve bu kullanıcı adını ve parolayı, daha sonra yeniden çalınabilecek şekilde depolamak Istiyorsanız, [parola tabanlı oturum](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) açmayı **Çoklu oturum açma modunu** seçin. uygulama daha sonra

- **SAML tabanlı oturum açma** – uygulamanız SAML veya OpenID Connect protokollerini destekliyorsa veya Kullanıcı SAML ortamınızda tanımladığınız kurallara göre belirli uygulama rollerine eşlemek Istiyorsanız, [SAML tabanlı oturum](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) açma modunu seçin. belirt

  >[!NOTE]
  >Uygulama proxy 'si bir uygulama için yapılandırıldığında bu seçenek kullanılamaz.

- **Üst bilgi tabanlı oturum** açma – tek oturum açma işlemini GERÇEKLEŞTIRMEK istediğiniz http üst bilgi tabanlı kimlik doğrulamasını destekleyen pingaccess kullanan bir uygulamanız varsa bu [üst bilgi tabanlı oturum](application-proxy-configure-single-sign-on-with-ping-access.md) açma modunu seçin.

  >[!NOTE]
  >Bu seçenek yalnızca uygulama proxy 'si ve PingAccess bir uygulama için yapılandırıldığında kullanılabilir.

- **Tümleşik Windows kimlik doğrulaması** – tek oturum açma işlemini gerçekleştirmek istediğiniz şirket ıçı bir WIA uygulamasını kullanıma sundığınızda, [Tümleşik Windows kimlik doğrulaması](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) çoklu oturum açma modunu seçin

  >[!NOTE]
  >Bu seçenek yalnızca uygulama proxy 'si bir uygulama için yapılandırıldığında kullanılabilir.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Özel olarak geliştirilen uygulamalar için çoklu oturum açma modları

Özel geliştirmiş uygulama deneyimi aracılığıyla özel olarak geliştirilen uygulamalar, daha önce listelenmeyen ek çoklu oturum açma modlarını da destekler ve şunları içerir:

- [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) tabanlı oturum açma

- [OpenID Connect 1,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) tabanlı oturum açma

- [WS-Federation 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) tabanlı oturum açma

- [SAML 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) tabanlı oturum açma

Bu çoklu oturum açma modlarını destekleyen özel olarak geliştirilmiş bir uygulama oluşturma hakkında daha fazla bilgi edinmek için [Azure Active Directory geliştirici kılavuzunu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) okuyun.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Uygulamanın çoklu oturum açma modunu ayarlama

Bir uygulamanın çoklu oturum açma modunu ayarlamak için aşağıdaki yönergeleri izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.
1. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.
1. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.
1. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.
1. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

1. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin.
1. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** ' ya tıklayın.

## <a name="how-to-choose-a-provisioning-mode"></a>Sağlama modu seçme

- **El Ile sağlama** – mevcut hesaplarınız varsa [el ile](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) sağlama modunu seçin ya da bu uygulama için Azure AD dışında hesapları yönetmek isteyebilirsiniz.

- **Otomatik sağlama** – Kullanıcı HESAPLARıNıN otomatik API tabanlı sağlamasını ve/veya bu uygulamaya serbest sağlamasını etkinleştirmek istiyorsanız [Otomatik](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **sağlama modunu** seçin 

- **SCIM tabanlı otomatik sağlama** – uygulamanız, kullanıcılar ve gruplar üzerinde yapılan değişiklikleri algılamak için SCIM protokolünü destekliyorsa, bu otomatik [sağlamayı](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) kullanın. Azure AD ile 

  >[!NOTE]
  >Bu seçenek belirli bir sağlama modu olarak listelenmez, ancak Azure AD ile tümleştirilen tüm uygulamalar için varsayılan olarak etkindir.

## <a name="how-to-set-an-applications-provisioning-mode"></a>Uygulamanın sağlama modunu ayarlama

Bir uygulamanın **sağlama** modunu ayarlamak için aşağıdaki yönergeleri izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.
1. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.
1. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.
1. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.
1. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

1. Sağlamayı yapılandırmak istediğiniz uygulamayı seçin.
1. Uygulama yüklendikten sonra uygulamanın sol taraftaki gezinti menüsünden **sağlama** ' ya tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[Uygulamaları Azure Active Directory ile yönetme](what-is-application-management.md)
