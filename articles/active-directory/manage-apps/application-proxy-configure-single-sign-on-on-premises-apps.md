---
title: Azure AD Uygulaması Proxy 'Si ile şirket içi uygulamalar için SAML çoklu oturum açma | Microsoft Docs
description: SAML kimlik doğrulamasıyla güvenliği sağlanan şirket içi uygulamalar için çoklu oturum açma sağlamayı öğrenin. Uygulama proxy 'Si ile şirket içi uygulamalara uzaktan erişim sağlama.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d004d1183c9f5c1aec2dae7ab2d638528f164cd9
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062508"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Uygulama proxy 'Si ile şirket içi uygulamalar için SAML çoklu oturum açma

SAML kimlik doğrulamasıyla güvenliği sağlanan şirket içi uygulamalara çoklu oturum açma (SSO) ve uygulama proxy 'Si aracılığıyla bu uygulamalara uzaktan erişim sağlama sağlayabilirsiniz. SAML çoklu oturum açma ile Azure Active Directory (Azure AD) kullanıcının Azure AD hesabını kullanarak uygulamanın kimliğini doğrular. Azure AD, oturum açma bilgilerini bir bağlantı protokolü aracılığıyla uygulamayla iletişim kurar. Ayrıca, SAML talepleriniz içinde tanımladığınız kurallara göre kullanıcıları belirli uygulama rollerine eşleyebilirsiniz. SAML SSO 'ya ek olarak uygulama proxy 'Si etkinleştirilerek, kullanıcılarınız uygulamaya dış erişime ve sorunsuz bir SSO deneyimine sahip olur.

Uygulamalar, **Azure Active Directory**tarafından verilen SAML belirteçlerini tüketebilmelidir. Bu yapılandırma, şirket içi kimlik sağlayıcısı kullanan uygulamalar için uygulanmaz. Bu senaryolar için, [uygulamaları Azure AD 'ye geçirmeye yönelik kaynakları](migration-resources.md)gözden geçirmeyi öneririz.

Uygulama proxy 'Si ile SAML SSO, SAML belirteci şifreleme özelliği ile de kullanılabilir. Daha fazla bilgi için bkz. [Azure AD SAML belirteci şifrelemesini yapılandırma](howto-saml-token-encryption.md).

Aşağıdaki protokol diyagramlarında, hem hizmet sağlayıcısı tarafından başlatılan (SP-başlatılan) Flow hem de kimlik sağlayıcısı tarafından başlatılan (IDP-başlatılan) akış için çoklu oturum açma sırası açıklanır. Uygulama proxy 'Si, SAML isteği ve şirket içi uygulamaya gelen yanıtı önbelleğe alarak SAML SSO ile birlikte çalışarak.

  ![SAML SP akışı](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP akışı](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Uygulama oluşturma ve SAML SSO 'yu ayarlama

1. Azure portal **Kurumsal uygulamaları > Azure Active Directory** seçin ve **Yeni uygulama**' yı seçin.

2. Şirket **içi uygulamalar** bölümünde Şirket **içi uygulama ekle**' yi seçin.

3. Yeni uygulamanız için görünen adı girin ve ardından **Ekle**' yi seçin.

4. Uygulamanın **genel bakış** sayfasında, **Çoklu oturum açma**' yı seçin.

5. Çoklu oturum açma yöntemi olarak **SAML** ' yi seçin.

6. Önce SAML SSO 'yu kurumsal ağ üzerinde çalışırken çalışacak şekilde ayarlayın. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** başlığına gidin ve **düzenleme** simgesini (bir kurşun kalem) seçin. Uygulamanın SAML tabanlı kimlik doğrulamasını yapılandırmak için [temel SAML yapılandırması girme](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) bölümündeki adımları izleyin.

7. Uygulamaya en az bir kullanıcı ekleyin ve test hesabının uygulamaya erişimi olduğundan emin olun. Şirket ağına bağlıyken, uygulamada çoklu oturum açma olup olmadığını görmek için test hesabını kullanın. 

   > [!NOTE]
   > Uygulama ara sunucusunu ayarladıktan sonra geri dönüp SAML **yanıt URL**'sini güncelleştirebilirsiniz.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Şirket içi uygulamayı uygulama proxy 'Si ile yayımlama

Şirket içi uygulamalar için SSO sağlayabilmeniz için önce uygulama ara sunucusunu etkinleştirmeniz ve bir bağlayıcı yüklemeniz gerekir. Şirket içi ortamınızı hazırlamaya, bir bağlayıcıyı yüklemeye ve kaydetmeye ve bağlayıcıyı test etme hakkında bilgi edinmek için [Azure AD 'de uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md) öğreticisine bakın. Ardından, uygulama proxy 'Si ile yeni uygulamanızı yayımlamak için aşağıdaki adımları izleyin. Aşağıda belirtilmeyen diğer ayarlar için öğreticideki [Şirket içi uygulamayı Azure AD 'ye ekleme](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) bölümüne bakın.

1. Uygulama Azure portal hala açıkken **uygulama proxy 'si**' ni seçin. Uygulamanın **Iç URL** 'sini sağlayın. Özel bir etki alanı kullanıyorsanız, uygulamanız için SSL sertifikasını da yüklemeniz gerekir. 
   > [!NOTE]
   > En iyi uygulama olarak, en iyi duruma getirilmiş bir kullanıcı deneyimi için mümkün olduğunda özel etki alanları kullanın. [Azure AD uygulama ara sunucusu özel etki alanlarıyla çalışma](application-proxy-configure-custom-domain.md)hakkında daha fazla bilgi edinin.

2. Uygulamanız için **ön kimlik doğrulama** yöntemi olarak **Azure Active Directory** ' yi seçin.

3. Uygulamanın **dış URL** 'sini kopyalayın. SAML yapılandırmasını tamamlayabilmeniz için bu URL 'ye ihtiyacınız olacak.

4. Test hesabını kullanarak, uygulama proxy 'sinin doğru şekilde ayarlandığını doğrulamak için uygulamayı **dış URL** ile açmayı deneyin. Sorun varsa bkz. [uygulama proxy 'si sorunlarını ve hata Iletilerini sorun giderme](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>SAML yapılandırmasını güncelleştirme

1. Uygulama Azure portal hala açıkken **Çoklu oturum açma**' yı seçin. 

2. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** başlığına gidin ve **düzenleme** simgesini (bir kurşun kalem) seçin. Uygulama ara sunucusu 'nda yapılandırdığınız **dış URL** , **tanımlayıcı**, **yanıt URL 'SI**ve **oturum kapatma URL 'si** alanlarını otomatik olarak doldurur. Uygulama proxy 'Sinin doğru çalışması için gerekli olduklarından, bu URL 'Leri düzenlemeyin.

3. Daha önce yapılandırılan **yanıt URL** 'sini, etki alanına uygulama proxy 'si tarafından erişilebilmeleri için düzenleyin. Örneğin, **dış URL** 'niz `https://contosotravel-f128.msappproxy.net` ve özgün **yanıt URL 'si** `https://contosotravel.com/acs`, `https://contosotravel-f128.msappproxy.net/acs`için özgün **yanıt URL** 'sini güncelleştirmeniz gerekir. 

    ![Temel SAML yapılandırma verilerini girin](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Varsayılan olarak işaretlemek için güncelleştirilmiş **yanıt URL 'sinin** yanındaki onay kutusunu işaretleyin.

   * Gerekli **yanıt URL 'si** zaten listeleniyorsa, bu **yanıt URL 'sini** varsayılan olarak Işaretleyin ve önceden yapılandırılmış **yanıt URL**'sini silin.

   * SP tarafından başlatılan bir akış için, arka uç uygulamasının kimlik doğrulama belirtecini almak için doğru **yanıt URL** 'Sini veya onaylama tüketici hizmeti URL 'sini belirttiğinden emin olun.

    > [!NOTE]
    > Arka uç uygulaması, **yanıt URL** 'SININ iç URL olmasını bekliyorsa, iç ve dış URL 'leri eşleştirmek için [özel etki alanları](application-proxy-configure-custom-domain.md) kullanmanız ya da kullanıcıların cihazlarında uygulamalarımın güvenli oturum açma uzantısını yüklemeniz gerekir. Bu uzantı, uygun uygulama proxy 'Si hizmetine otomatik olarak yönlendirilir. Uzantıyı yüklemek için bkz. [uygulamalarımın güvenli oturum açma uzantısı](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Uygulamanızı test etme

Tüm bu adımları tamamladığınızda, uygulamanız çalışır duruma gelmelidir. Uygulamayı test etmek için:

1. Bir tarayıcı açın ve uygulamayı yayımladığınızda oluşturduğunuz **dış URL** 'ye gidin. 
1. Uygulamaya atadığınız test hesabıyla oturum açın. Uygulamayı yükleyebilir ve uygulamanın içinde SSO 'SU olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Uygulama Ara Sunucusu çoklu oturum açma nasıl sağlar?](application-proxy-single-sign-on.md)
- [Uygulama ara sunucusu sorunlarını giderme](application-proxy-troubleshoot.md)
