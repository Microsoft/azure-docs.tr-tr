---
title: 'Öğretici: Cisco şemsiye ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Cisco şemsiye arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee91ca53e32bfdc387dc20054493d02d506a75da
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158633"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Öğretici: Cisco şemsiye ile tümleştirme Azure Active Directory

Bu öğreticide Cisco şemsiye 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Cisco şemsiye 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Cisco şemsiye 'e erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Cisco şemsiye (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Cisco şemsiye ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Cisco şemsiye çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Cisco şemsiye **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Galeriden Cisco şemsiye ekleme

Cisco şemsiye 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Cisco şemsiye eklemeniz gerekir.

**Galeriden Cisco şemsiye eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Cisco şemsiye**yazın, sonuç panelinden **Cisco şemsiye** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde Cisco şemsiye](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına bağlı olarak [uygulama adı] ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve [uygulama adı] içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı [uygulama adı] ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Cisco şemsiye çoklu oturum açma yapılandırma](#configure-cisco-umbrella-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Cisco şemsiye 'da Britta Simon 'a sahip olmak için **[Cisco şemsiye test kullanıcısı oluşturun](#create-cisco-umbrella-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı [uygulama adı] ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Cisco şemsiye** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

    ![Cisco şemsiye etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/both-preintegrated-signon.png)

    a. Uygulamayı **SP** başlatma modunda yapılandırmak istiyorsanız, aşağıdaki adımları uygulayın:

    b. **Ek URL 'Ler ayarla**' ya tıklayın.

    c. **Oturum açma URL 'si** metin kutusuna bir URL yazın: `https://login.umbrella.com/sso`

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Cisco şemsiye ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-cisco-umbrella-single-sign-on"></a>Cisco şemsiye çoklu oturum açmayı yapılandırma

1. Farklı bir tarayıcı penceresinde, Cisco şemsiye şirket sitenizde yönetici olarak oturum açın.

2. Menünün sol tarafında, **yönetici** ' ye tıklayın ve **kimlik doğrulaması** ' na gidin ve **SAML**' ye tıklayın.

    ![Yönetici](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. **Diğer** ' i seçin ve **İleri**' ye tıklayın.

    ![Diğer](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. **Cisco şemsiye meta verileri**sayfasında, **İleri**' ye tıklayın.

    ![Meta veriler](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. **Meta verileri karşıya yükle** sekmesinde, önceden yapılandırılmış SAML varsa, **bunları değiştirmek için buraya tıklayın** seçeneğini belirleyin ve aşağıdaki adımları izleyin.

    ![Sonraki](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. **A: XML dosyasını karşıya yükle seçeneğinde**, Azure Portal Indirdiğiniz **Federasyon meta veri XML** dosyasını karşıya yükleyin ve meta verileri karşıya yükledikten sonra otomatik olarak otomatik olarak doldurulur ve **İleri**' ye tıklayın.

    ![Choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. **SAML yapılandırmasını Doğrula** bölümünde **SAML yapılandırmanızı test et**' e tıklayın.

    ![Test](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. **KAYDET**'e tıklayın.

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

Bu bölümde, Cisco şemsiye 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Cisco şemsiye**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Cisco şemsiye**yazın ve seçin.

    ![Uygulamalar listesindeki Cisco şemsiye bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-cisco-umbrella-test-user"></a>Cisco şemsiye test kullanıcısı oluşturma

Azure AD kullanıcılarının Cisco şemsiye 'da oturum açmasını sağlamak için, Cisco şemsiye 'e sağlanması gerekir.  
Cisco şemsiye durumunda sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Farklı bir tarayıcı penceresinde, Cisco şemsiye şirket sitenizde yönetici olarak oturum açın.

2. Menünün sol tarafında, **yönetici** ' ye tıklayın ve **hesaplar**' a gidin.

    ![Hesap](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. **Hesaplar** sayfasında, sayfanın sağ üst tarafındaki **Ekle** ' ye tıklayın ve aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. **Ad** alanına **Britta**gibi bir ad girin.

    b. **Soyadı** alanına **Simon**gibi soyadı girin.

    c. **Yönetici temsilcisi rolü**Seç ' de rolünüzü seçin.
  
    d. **E-posta adresi** alanına, kullanıcı **\@contoso.com**gibi kullanıcının EMAA adresini girin.

    e. **Parola** alanına parolanızı girin.

    f. **Parolayı Onayla** alanına parolanızı yeniden girin.

    g. **Oluştur**' a tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Cisco şemsiye kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Cisco şemsiye ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
