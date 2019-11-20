---
title: 'Öğretici: GitHub ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve GitHub arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5021d3a8bed48003f38f01c3d61eac4bdd96b0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159211"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Öğretici: GitHub ile Azure Active Directory tümleştirme

Bu öğreticide, GitHub 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
GitHub 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* GitHub 'a erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla GitHub (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini GitHub ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* GitHub Enterprise [Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)'da oluşturulmuş GitHub [Enterprise faturalandırma planını](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) gerektiren bir GitHub kuruluşu

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* GitHub, **SP** tarafından başlatılan SSO 'yu destekler

* GitHub [ **Otomatik** Kullanıcı sağlamayı destekler](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>Galeriden GitHub ekleme

GitHub 'ın Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden GitHub 'dan yönetilen SaaS uygulamaları listenize GitHub eklemeniz gerekir.

**Galeriden GitHub eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **GitHub**yazın, sonuç panelinden **GitHub.com** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde GitHub](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına dayanarak, GitHub ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve GitHub 'daki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı GitHub ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[GitHub çoklu oturum açmayı yapılandırma](#configure-github-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. GitHub 'da kullanıcının Azure AD gösterimine bağlı olan, GitHub 'da Britta Simon 'un bir karşılığı olacak şekilde **[GitHub test kullanıcısı oluşturun](#create-github-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı GitHub ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **GitHub** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![GitHub etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://github.com/orgs/<entity-id>/sso`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Bunların gerçek değerler olduğunu lütfen unutmayın. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirmeniz gerekir. Burada, Tanımlayıcıdaki benzersiz dize değerini kullanmanızı öneririz. Bu değerleri almak için GitHub Yöneticisi bölümüne gidin.

5. GitHub uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName**ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. GitHub uygulaması, **NameIdentifier** 'ın **User. Mail**ile eşlenmesini bekler, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

6. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. GitHub 'ı **Ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-github-single-sign-on"></a>GitHub çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, GitHub kuruluş sitenizde yönetici olarak oturum açın.

2. **Ayarlar** ' a gidin ve **güvenlik** ' e tıklayın.

    ![Ayarlar](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Çoklu oturum açma yapılandırma alanlarını işaret eden **SAML kimlik doğrulamasını etkinleştir** kutusunu işaretleyin. Daha sonra, Azure AD yapılandırmasındaki çoklu oturum açma URL 'sini güncelleştirmek için çoklu oturum açma URL 'SI değerini kullanın.

    ![Ayarlar](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Aşağıdaki alanları yapılandırın:

    ![Ayarlar](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Oturum açma **URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Veren** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. İndirilen sertifikayı not defteri 'nde Azure portal açın, içeriği **ortak sertifika** metin kutusuna yapıştırın.

    d. Şu şekilde gösterildiği gibi **RSA-SHA1** ve **SHA1** ile **RSA-SHA256** ve **SHA256** arasındaki **imza yöntemini** ve **Özet yöntemini** düzenlemek için **Düzenle** simgesine tıklayın.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. SSO sırasında doğrulama hatası veya hata olmadığını doğrulamak için **Test SAML yapılandırması** ' na tıklayın.

    ![Ayarlar](./media/github-tutorial/tutorial_github_config_github_06.png)

6. **Kaydet**’e tıklayın

> [!NOTE]
> GitHub 'daki çoklu oturum açma, GitHub 'daki belirli bir kuruluşun kimliğini doğrular ve GitHub 'ın kimlik doğrulamasını değiştirmez. Bu nedenle, kullanıcının github.com oturumunun süresi dolmuşsa, çoklu oturum açma işlemi sırasında GitHub 'ın KIMLIĞI/parolasıyla kimlik doğrulaması yapmanız istenebilir.

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

Bu bölümde, GitHub 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **GitHub**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **GitHub**' ı seçin.

    ![Uygulamalar listesindeki GitHub bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-github-test-user"></a>GitHub test kullanıcısı oluşturma

Bu bölümün amacı, GitHub 'da Britta Simon adlı bir Kullanıcı oluşturmaktır. GitHub, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamayı destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](github-provisioning-tutorial.md) .

**Kullanıcı el ile oluşturmanız gerekiyorsa aşağıdaki adımları gerçekleştirin:**

1. GitHub şirket sitenizde yönetici olarak oturum açın.

2. **Kişiler**' e tıklayın.

    ![Düzenliyor](./media/github-tutorial/tutorial_github_config_github_08.png "Kişiler")

3. **Üyeyi davet et**' e tıklayın.

    ![Kullanıcıları davet et](./media/github-tutorial/tutorial_github_config_github_09.png "Kullanıcıları davet et")

4. **Üye davet et** iletişim sayfasında, aşağıdaki adımları uygulayın:

    a. **E-posta** metin kutusunda Britta Simon hesabının e-posta adresini yazın.

    ![Kişileri davet et](./media/github-tutorial/tutorial_github_config_github_10.png "Kişileri davet et")

    b. **Davet gönder**' e tıklayın.

    ![Kişileri davet et](./media/github-tutorial/tutorial_github_config_github_11.png "Kişileri davet et")

    > [!NOTE]
    > Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını onaylamak için bir bağlantıyı izler.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde GitHub kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız GitHub 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
