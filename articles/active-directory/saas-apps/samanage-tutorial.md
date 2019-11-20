---
title: 'Öğretici: Samanage ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Samanage arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 741efbae84e18c811beb6c0579c1949c5ddf619c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160106"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Öğretici: Samanage ile tümleştirme Azure Active Directory

Bu öğreticide, Samanage 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Samanage 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Samanage erişimi olan ' i denetleyebilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Samanage ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Çoklu oturum açma etkin aboneliğini sayönet

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Samanage **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-samanage-from-the-gallery"></a>Galeriden Samanage ekleme

Samanage 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Sayönet eklemeniz gerekir.

**Galeriden Samanage eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Samanage**yazın, sonuç panelinden **sayönet** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

     ![Sonuçlar listesinde Samanage](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre Samanage ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı Samanage ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Çoklu oturum açmayı yapılandırma](#configure-samanage-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırma.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcı Azure AD gösterimine bağlı olan Samanage 'da Britta Simon 'a sahip olmak için **[Samanage test kullanıcısı oluşturun](#create-samanage-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Samanage ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **sayönet** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Etki alanı ve URL 'Leri çoklu oturum açma bilgilerini sayönet](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Bu değerler gerçek değildir. Bu değerleri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek oturum açma URL 'SI ve tanımlayıcı ile güncelleştirin. Daha fazla ayrıntı için [bkz. istemci desteği ekibine](https://www.samanage.com/support)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Sayönet ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-samanage-single-sign-on"></a>Sayönet çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Samanage şirket sitenizde yönetici olarak oturum açın.

2. **Pano** ' ya tıklayın ve sol gezinti bölmesinde **Kurulum** ' u seçin.
   
    ![Pano](./media/samanage-tutorial/tutorial_samanage_001.png "Pano")

3. **Çoklu oturum açma**' ya tıklayın.
   
    ![Çoklu oturum açma](./media/samanage-tutorial/tutorial_samanage_002.png "Çoklu oturum açma")

4. **SAML kullanarak oturum açma** bölümüne gidin, aşağıdaki adımları uygulayın:
   
    ![SAML kullanarak oturum açma](./media/samanage-tutorial/tutorial_samanage_003.png "SAML kullanarak oturum açma")
 
    a. **SAML Ile çoklu oturum açmayı etkinleştir**' e tıklayın.  
 
    b. **Kimlik sağlayıcısı URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın.    
 
    c. **Oturum açma URL** 'sini Azure Portal ' DEKI **temel SAML yapılandırması** 'nın **oturum açma URL 'si** ile eşleştiğini doğrulayın.
 
    d. **Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini girin.
 
    e. **SAML veren** metin kutusunda, kimlik sağlayıcınızda uygulama kimliği URI 'si kümesini yazın.
 
    f. Not defteri 'nde Azure portal indirilen Base-64 kodlu sertifikanızı açın, bu içeriği panonuza kopyalayın ve ardından **kimlik sağlayıcısını aşağıdaki** metin kutusuna yapıştırın.
 
    g. **Samanage 'de yoksa kullanıcı oluştur**' a tıklayın.
 
    h. **Güncelleştir**’e tıklayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Samanage 'a erişim izni vererek Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **sayönet**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Sayönet**' i seçin.

    ![Uygulamalar listesindeki Samanage bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-samanage-test-user"></a>Samanage test kullanıcısı oluştur

Azure AD kullanıcılarının Samanage 'ta oturum açmasını sağlamak için bunların Samanage 'a sağlanması gerekir.  
Samanage, sağlama durumunda el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Samanage şirket sitenizde yönetici olarak oturum açın.

2. **Pano** ' ya tıklayın ve sol gezinti kaydırma bölmesinde **Kurulum** ' u seçin.
   
    ![Kurulum](./media/samanage-tutorial/tutorial_samanage_001.png "Kurulum")

3. **Kullanıcılar** sekmesine tıklayın
   
    ![Kullanıcılar](./media/samanage-tutorial/tutorial_samanage_006.png "Kullanıcılar")

4. **Yeni Kullanıcı**' ya tıklayın.
   
    ![Yeni Kullanıcı](./media/samanage-tutorial/tutorial_samanage_007.png "Yeni Kullanıcı")

5. Sağlamak istediğiniz Azure Active Directory hesabının **adını** ve **e-posta adresini** yazın ve **Kullanıcı oluştur**' a tıklayın.
   
    ![Kullanıcı Oluştur](./media/samanage-tutorial/tutorial_samanage_008.png "Kullanıcı Oluştur")
   
   >[!NOTE]
   >Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını onaylamak için bir bağlantıyı izler. Azure Active Directory Kullanıcı hesapları sağlamak için Samanage tarafından sunulan diğer tüm Samanage Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Samanage kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Samanage ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

