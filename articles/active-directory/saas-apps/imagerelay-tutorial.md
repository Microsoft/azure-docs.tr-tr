---
title: 'Öğretici: görüntü geçişine Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve görüntü geçişi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd0637a632b277eae019ac4aebfbc7cdb87e8e2
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158979"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Öğretici: görüntü geçişiyle Azure Active Directory tümleştirme

Bu öğreticide, görüntü geçişini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Resim geçişini Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Görüntü geçişine erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla görüntü geçişine (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini görüntü geçişi ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Görüntü Geçişi çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Görüntü Geçişi **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-image-relay-from-the-gallery"></a>Galeriden görüntü geçişi ekleme

Görüntü geçişinin Azure AD ile tümleştirilmesini yapılandırmak için galerideki görüntü geçişini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden görüntü geçişi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **görüntü geçişi**yazın, sonuç panelinden **görüntü geçişi** ' ni seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Sonuçlar listesinde görüntü geçişi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre görüntü geçişi ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve görüntü geçişi 'ndeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı görüntü geçişi ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurmanız gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Görüntü geçişi çoklu oturum açmayı yapılandırma](#configure-image-relay-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Görüntü **[geçişi test kullanıcısı oluşturma](#create-image-relay-test-user)** -kullanıcının Azure AD gösterimine bağlı olan görüntü geçişi 'Nde Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı görüntü geçişi ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **görüntü geçişi** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Görüntü Geçişi etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<companyname>.imagerelay.com/`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [görüntü geçişi istemci desteği ekibine](http://support.imagerelay.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Görüntü geçişini ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-image-relay-single-sign-on"></a>Görüntü Geçişi çoklu oturum açmayı yapılandırma

1. Başka bir tarayıcı penceresinde, görüntü geçişi şirket sitenizde yönetici olarak oturum açın.

2. Üstteki araç çubuğunda, **kullanıcılar & izinleri** iş yüküne tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. **Yeni Izin oluştur**' a tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. **Çoklu oturum açma ayarları** iş yükünde, **Bu grup yalnızca çoklu oturum açma aracılığıyla oturum açabilir** onay kutusunu seçin ve ardından **Kaydet**' e tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. **Hesap ayarları**' na gidin.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. **Çoklu oturum açma ayarları** iş yüküne gidin.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. **SAML ayarları** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    c. **Ad kimliği biçimi**olarak **urn: oasu: adlar: TC: SAML: 1.1: NameID-Format: emapostaadı**' nı seçin.

    d. **Hizmet sağlayıcısından (görüntü geçişi) gelen istekler Için bağlama seçenekleri**olarak, **bağlamayı gönder**' i seçin.

    e. **X. 509.440 sertifikası**altında **sertifikayı Güncelleştir**' e tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. İndirilen sertifikayı not defteri 'nde açın, içeriği kopyalayın ve **x. 509.952 Certificate** metin kutusuna yapıştırın.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Anında **Kullanıcı sağlama** bölümünde, **tam zamanında Kullanıcı sağlamayı etkinleştir**' i seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Yalnızca çoklu oturum açma ile oturum açmasına izin verilen izin grubunu (örneğin, **SSO temel**) seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. **Kaydet** düğmesine tıklayın.

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

Bu bölümde, görüntü geçişine erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **görüntü geçişi**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **görüntü geçişi**' ni seçin.

    ![Uygulamalar listesindeki görüntü geçişi bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-image-relay-test-user"></a>Görüntü Geçişi test kullanıcısı oluştur

Bu bölümün amacı, görüntü geçişi 'nde Britta Simon adlı bir Kullanıcı oluşturmaktır.

**Görüntü Geçişi 'nde Britta Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

1. Görüntü Geçişi şirket sitenizde yönetici olarak oturum açın.

2. **Kullanıcılar & izinleri** ' ne gıdın ve **SSO kullanıcısı oluştur**' u seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Sağlamak istediğiniz kullanıcının **e-postasını**, **adını**, **soyadını**ve **Şirket** adını girin ve yalnızca çoklu oturum açma ile oturum açmak için BIR grup olan izin grubunu (örneğin, SSO temel) seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. **Oluştur**’a tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde görüntü geçişi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız görüntü geçişine otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)