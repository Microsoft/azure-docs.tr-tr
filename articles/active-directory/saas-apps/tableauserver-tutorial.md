---
title: 'Öğretici: Tableau Server ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Tableau Server arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b07c6d6317d8ca284008d271f507b965414bfbc1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257510"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Öğretici: Tableau Server ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Tableau Server 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Tableau Server 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Tableau Server erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Tableau Server 'a otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Tableau sunucusu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Tableau sunucusu **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-tableau-server-from-the-gallery"></a>Galeriden Tableau Server ekleme

Tableau Server tümleştirmesini Azure AD ile yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Tableau sunucusu eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Tableau Server** yazın.
1. Sonuçlar panelinden **Tableau Server** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Tableau Server için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Tableau Server ile yapılandırın ve test edin. SSO 'nun çalışması için, Tableau sunucusundaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Tableau Server ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Tableau Server SSO 'Yu yapılandırın](#configure-tableau-server-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Tableau Server 'da B. Simon 'a karşılık gelen bir **[Tableau Server test kullanıcısı oluşturun](#create-tableau-server-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Tableau Server** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://azure.<domain name>.link`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://azure.<domain name>.link`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Yukarıdaki değerler gerçek değer değildir. Değerleri, Öğreticinin ilerleyen kısımlarında açıklanan Tableau sunucu yapılandırma sayfasından gerçek URL ve tanımlayıcıyla güncelleştirin.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Tableau sunucusunu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Tableau sunucusuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Tableau Server**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-tableau-server-sso"></a>Tableau Server SSO 'yu yapılandırma

1. Uygulamanız için yapılandırılmış SSO 'yu almak için, Tableau sunucu kiracınızda yönetici olarak oturum açmanız gerekir.

2. **Yapılandırma** sekmesinde, **Kullanıcı kimliği & erişim**' i seçin ve ardından **kimlik doğrulama** yöntemi sekmesini seçin.

    ![Çoklu oturum açmayı yapılandırın](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. **Yapılandırma** sayfasında, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı yapılandırın](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. **Kimlik doğrulama yöntemi**için SAML ' yi seçin.

    b. **Sunucu IÇIN SAML kimlik doğrulamasını etkinleştir**onay kutusunu seçin.

    c. Tableau sunucusu dönüş URL 'SI — gibi Tableau Server kullanıcılarına erişim <http://tableau_server>verilecek URL. Kullanılması `http://localhost` önerilmez. Sondaki eğik çizgiyle (örneğin, `http://tableau_server/`) bir URL kullanılması desteklenmez. **Tableau sunucusu geri dönüş URL 'sini** kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde URL metin kutusunu **oturum** açmak için yapıştırın

    d. SAML varlık KIMLIĞI — varlık KIMLIĞI, Tableau sunucu yüklemenizi IDP 'ye benzersiz şekilde tanımlar. İsterseniz Tableau sunucu URL 'nizi buraya girebilirsiniz, ancak Tableau Server URL 'niz olması gerekmez. **SAML VARLıK kimliğini** kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı** metin kutusuna yapıştırın

    e. **XML meta verilerini Indir dosyasına** tıklayın ve metin düzenleyici uygulamasında açın. Onaylama tüketici hizmeti URL 'sini http post ve Index 0 ile bulun ve URL 'YI kopyalayın. Şimdi bunu, Azure portal **temel SAML yapılandırması** bölümünde **yanıt URL** metin kutusuna yapıştırın

    f. Azure portal 'ten indirilen Federasyon meta veri dosyanızı bulun ve **SAML IDP meta veri dosyasına**yükleyin.

    g. IDP 'nin Kullanıcı adlarını, görünen adları ve e-posta adreslerini tutmak için kullandığı özniteliklerin adlarını girin.

    h. **Kaydet**'e tıklayın.

    > [!NOTE]
    > Müşterinin, Tableau Server SAML SSO yapılandırmasındaki herhangi bir sertifikayı karşıya yüklemesi gerekir ve bu, SSO akışında yok sayılır. Tableau sunucusunda SAML yapılandırması için yardıma ihtiyacınız varsa lütfen [SAML 'Yi yapılandırma](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm)makalesine başvurun.

### <a name="create-tableau-server-test-user"></a>Tableau Server test kullanıcısı oluştur

Bu bölümün amacı, Tableau Server 'da B. Simon adlı bir Kullanıcı oluşturmaktır. Tableau sunucusundaki tüm kullanıcıları sağlamanız gerekir.

Kullanıcının Kullanıcı adı, Kullanıcı **adının**Azure AD özel özniteliğinde yapılandırdığınız değerle eşleşmelidir. Doğru eşleme ile tümleştirme, Azure AD çoklu oturum açma yapılandırması ile çalışmalıdır.

> [!NOTE]
> Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, kuruluşunuzda Tableau sunucu yöneticisiyle iletişim kurmanız gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde Tableau sunucu kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Tableau sunucusunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Tableau Server 'ı Azure AD ile deneyin](https://aad.portal.azure.com/)