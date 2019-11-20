---
title: 'Öğretici: Clarizen ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Clarizen arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b34e6087f3a6e7f3cc0f46c53d4fe903838d210
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158605"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Öğretici: Clarizen ile tümleştirme Azure Active Directory

Bu öğreticide, Clarizen Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Clarizen ile Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Clarizen 'ya erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Clarizen (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Clarizen ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Clarizen çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Clarizen, **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-clarizen-from-the-gallery"></a>Galeriden Clarizen ekleme

Clarizen 'nın Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Clarizen ' i eklemeniz gerekir.

**Galeriden Clarizen ' i eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Clarizen**' yazın, sonuç panelinden **Clarizen** ' yı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde Clarizen](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre Clarizen ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı Clarizen ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Clarizen çoklu oturum açmayı yapılandırın](#configure-clarizen-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Clarizen 'da Britta Simon 'un bir karşılığı olan **[Clarizen test kullanıcısı oluşturun](#create-clarizen-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Clarizen ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Clarizen** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki adımları uygulayın:

    ![Clarizen etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna bir değer yazın: `Clarizen`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Bunlar gerçek değerler değildir. Gerçek tanımlayıcıyı ve yanıt URL 'sini kullanmanız gerekir. Burada, tanımlayıcı olarak bir dizenin benzersiz değerini kullanmanızı öneririz. Gerçek değerleri almak için, [Clarizen destek ekibine](https://success.clarizen.com/hc/en-us/requests/new)başvurun.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Clarizen** ' ı ayarlama bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-clarizen-single-sign-on"></a>Clarizen çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Clarizen şirket sitenizde yönetici olarak oturum açın.

1. Kullanıcı adına tıklayın ve ardından **Ayarlar**' a tıklayın.

    ![Kullanıcı adınızla "Ayarlar" a tıklanın](./media/clarizen-tutorial/tutorial_clarizen_001.png "Ayarlar")

1. **Genel ayarlar** sekmesine tıklayın. Ardından, **federal kimlik doğrulaması**' nın yanındaki **Düzenle**' ye tıklayın.

    !["Genel ayarlar" sekmesi](./media/clarizen-tutorial/tutorial_clarizen_002.png "Genel ayarlar")

1. **Federal kimlik doğrulaması** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    !["Federal kimlik doğrulaması" iletişim kutusu](./media/clarizen-tutorial/tutorial_clarizen_003.png "Federal kimlik doğrulaması")

    a. **Federal kimlik doğrulamasını etkinleştir**' i seçin.

    b. İndirilen sertifikanızı karşıya yüklemek için **karşıya yükle** ' ye tıklayın.

    c. **Oturum açma URL 'si** kutusunda, Azure AD uygulama yapılandırma penceresinde **oturum açma URL 'si** değerini girin.

    d. Oturum kapatma **URL 'si** kutusunda, Azure AD uygulama yapılandırma penceresinden **Logout URL 'si** değerini girin.

    e. **Gönderi kullan**' ı seçin.

    f. **Kaydet** düğmesine tıklayın.

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

Bu bölümde, Clarizen 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'ı etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Clarizen**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Clarizen**' yı seçin.

    ![Uygulamalar listesindeki Clarizen bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-clarizen-test-user"></a>Clarizen test kullanıcısı oluşturma

Bu bölümün amacı, Clarizen 'da Britta Simon adlı bir Kullanıcı oluşturmaktır.

**Kullanıcı el ile oluşturmanız gerekiyorsa lütfen aşağıdaki adımları gerçekleştirin:**

Azure AD kullanıcılarının Clarizen 'da oturum açmasını sağlamak için Kullanıcı hesapları sağlamalısınız. Clarizen, sağlama durumunda el ile gerçekleştirilen bir görevdir.

1. Clarizen şirket sitenizde yönetici olarak oturum açın.

2. **Kişiler**' e tıklayın.

    !["Kişiler" i](./media/clarizen-tutorial/create_aaduser_001.png "Kişiler")

3. **Kullanıcıyı davet et**' e tıklayın.

    !["Kullanıcı davet et" düğmesi](./media/clarizen-tutorial/create_aaduser_002.png "Kullanıcıları davet et")

1. **Kişileri davet et** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    !["Kişi davet et" iletişim kutusu](./media/clarizen-tutorial/create_aaduser_003.png "Kişileri davet et")

    a. **E-posta** kutusuna Britta Simon hesabının e-posta adresini yazın.

    b. **Davet et**' e tıklayın.

    > [!NOTE]
    > Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını onaylamak için bir bağlantıyı izler.


### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Clarizen kutucuğunu tıklattığınızda, SSO 'yu ayarladığınız Clarizen ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
