---
title: 'Öğretici: TOPdesk ile güvenli Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve TOPdesk ile güvenli arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3125f5b9075a69289bd6a44d62c3d06d5e6d910d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161115"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Öğretici: Topmasa güvenliğine Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile TOPdesk ile güvenli tümleştirme hakkında bilgi edineceksiniz.
Azure AD ile TOPdesk ile güvenli bir şekilde tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Topmasa ile güvenli erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak güvenli bir şekilde (çoklu oturum açma) oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini TOPdesk ile güvenli bir şekilde yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Topmasa-güvenli çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* TOPdesk-Secure **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-topdesk---secure-from-the-gallery"></a>Topolojinin, Galeriden güvenli şekilde eklenmesi

TOPdesk ile güvenli hale getirmek için Azure AD ile güvenli bir şekilde yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Topmasa-Secure ' ı eklemeniz gerekir.

**Galeriden Topmasa-Secure eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **TopDesk-Secure**yazın, sonuç panelinden **TopDesk-Secure** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![TOPdesk-sonuçlar listesinde güvenli](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre TopDesk ile güvenli bir şekilde yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilgili Kullanıcı ile TOPdesk-Secure arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, TOPdesk ile güvenli bir şekilde yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[TOPdesk-Secure çoklu oturum açmayı yapılandırın](#configure-topdesk---secure-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Topmasa **[-Secure test kullanıcısı oluşturun](#create-topdesk---secure-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan Topmasa 'da güvenli bir Britta Simon 'a sahip olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı TOPdesk ile güvenli olarak yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **TopDesk-Secure** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Topmasa-güvenli etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<companyname>.topdesk.net`

    b. **Tanımlayıcı URL 'si** kutusunda, TopDesk yapılandırmasından alabileceğiniz TopDesk meta veri URL 'sini girin. Şu stili kullanmalıdır: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Topmasa-güvenli istemci desteği ekibine](https://www.topdesk.com/us/support/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **TOPdesk-Secure ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-topdesk---secure-single-sign-on"></a>TOPdesk-Secure çoklu oturum açmayı yapılandırma

1. **Topoloji güvenli** şirket sitenizde yönetici olarak oturum açın.

2. **TopDesk** menüsünde **Ayarlar**' a tıklayın.

    ![Ayarlar](./media/topdesk-secure-tutorial/ic790598.png "Ayarlar")

3. **Oturum açma ayarları**' na tıklayın.

    ![Oturum açma ayarları](./media/topdesk-secure-tutorial/ic790599.png "Oturum açma ayarları")

4. **Oturum açma ayarları** menüsünü genişletin ve ardından **genel**' e tıklayın.

    ![Genel](./media/topdesk-secure-tutorial/ic790600.png "Genel")

5. **SAML oturum açma** Yapılandırması bölümünün **güvenli** bölümünde aşağıdaki adımları uygulayın:

    ![Teknik ayarlar](./media/topdesk-secure-tutorial/ic790855.png "Teknik ayarlar")

    a. Ortak meta veri dosyasını indirmek için **İndir** ' e tıklayın ve ardından bilgisayarınıza yerel olarak kaydedin.

    b. Meta veri dosyasını açın ve ardından **Assertionconsumerservice** düğümünü bulun.

    ![Onaylama tüketici hizmeti](./media/topdesk-secure-tutorial/ic790856.png "Onaylama tüketici hizmeti")

    c. **Assertionconsumerservice** değerini kopyalayın, bu değeri, **TopDesk-Secure etki alanı ve URL 'ler** bölümündeki Yanıt URL metin kutusuna yapıştırın.

6. Bir sertifika dosyası oluşturmak için aşağıdaki adımları gerçekleştirin:

    ![Sertifika](./media/topdesk-secure-tutorial/ic790606.png "Sertifika")

    a. İndirilen meta veri dosyasını Azure portal açın.

    b. **Xsi: Type türünde** **Besleyicisi: Applicationservicetype**olan **RoleDescriptor** düğümünü genişletin.

    c. **X509Certificate** düğümünün değerini kopyalayın.

    d. Kopyalanmış **X509Certificate** değerini bilgisayarınıza yerel olarak bir dosyaya kaydedin.

7. **Genel** bölümünde, **Ekle**' ye tıklayın.

    ![Ekleyemiyorum](./media/topdesk-secure-tutorial/ic790607.png "Ekle")

8. **SAML yapılandırma Yardımcısı** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![SAML yapılandırma Yardımcısı](./media/topdesk-secure-tutorial/ic790608.png "SAML yapılandırma Yardımcısı")

    a. İndirilen meta veri dosyanızı Azure portal karşıya yüklemek için, **Federasyon meta verileri**altında, **Araştır**' a tıklayın.

    b. Sertifika dosyanızı karşıya yüklemek için, **sertifika (RSA)** altında, **Araştır**' a tıklayın.

    c. **Özel anahtar (RSA, PKCS8, der)** için kendi özel anahtarınızı karşıya yükleyebilir veya özel anahtarı almak Için [TopDesk-Secure istemci destek ekibine](https://www.topdesk.com/us/support) başvurabilirsiniz.

    d. TOPdesk destek ekibinden aldığınız logo dosyasını karşıya yüklemek için, **logo simgesi**altında, **Araştır**' a tıklayın.

    e. **Kullanıcı adı öznitelik** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`yazın.

    f. **Görünen ad** metin kutusuna yapılandırmanız için bir ad yazın.

    g. **Kaydet** düğmesine tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Topmasa ile güvenli erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **TopDesk-Secure**' u seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Topmasa-Secure**' ı yazın ve seçin.

    ![Uygulamalar listesindeki Topmasa-Secure bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-topdesk---secure-test-user"></a>TOPdesk-Secure test kullanıcısı oluşturma

Azure AD kullanıcılarının Topmasa ile güvenli oturum açmasını sağlamak için, bu kullanıcıların Topmasa-Secure ' a sağlanması gerekir.  
Topmasa-Secure durumunda sağlama, el ile gerçekleştirilen bir görevdir.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:

1. **Topoloji güvenli** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde, **yeni \> destek dosyaları \> işleci \> TopDesk ' e**tıklayın.

    ![İşlecinde](./media/topdesk-secure-tutorial/ic790610.png "İşleç")

3. **Yeni operatör** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![New Işleci](./media/topdesk-secure-tutorial/ic790611.png "New Işleci")

    a. **Genel** sekmesine tıklayın.

    b. **Soyadı** metin kutusunda, **Simon**gibi kullanıcının soyadı yazın.

    c. **Konum** bölümünde hesap Için bir **site** seçin.

    d. **TopDesk oturum** açma bölümünün **oturum açma adı** metin kutusunda, Kullanıcı için bir oturum açma adı yazın.

    e. **Kaydet** düğmesine tıklayın.

> [!NOTE]
> AAD Kullanıcı hesapları sağlamak için TOPdesk-Secure tarafından sunulan diğer tüm Topmasa-güvenli Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde topoloji güvenli Kutucuğa tıkladığınızda, SSO 'yu ayarladığınız en güvenli topoloji için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

