---
title: 'Öğretici: Igloo yazılımıyla tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Igloo yazılım arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 033561ac3c4a510927691dc8db4f61196f54ec2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460419"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Öğretici: Igloo yazılımıyla tümleştirme Azure Active Directory

Bu öğreticide, Igloo yazılımlarını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Igloo yazılımlarını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Igloo yazılımına erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Igloo yazılım (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Igloo yazılımıyla yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Igloo yazılım çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Igloo yazılımı **SP** tarafından başlatılan SSO 'yu destekler
* Igloo yazılımı **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-igloo-software-from-the-gallery"></a>Galeriden Igloo yazılım ekleme

Igloo yazılımının tümleştirmesini Azure AD ile yapılandırmak için galerideki Igloo yazılımını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Igloo yazılım eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Igloo Software** yazın, sonuç panelinden **Igloo Software** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde Igloo yazılımı](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına bağlı olarak, Igloo YAZıLıMıYLA Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Igloo yazılım içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı Igloo yazılımıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Igloo Software çoklu oturum açmayı yapılandırın](#configure-igloo-software-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Igloo Software 'te Britta Simon 'a sahip olmak için **[Igloo yazılım test kullanıcısı oluşturun](#create-igloo-software-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Igloo yazılımıyla yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Igloo yazılım** uygulaması tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Igloo yazılım etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.igloocommmunities.com`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.igloocommmunities.com/saml.digest`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Igloo yazılım istemci desteği ekibine](https://www.igloosoftware.com/services/support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Igloo yazılımını ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-igloo-software-single-sign-on"></a>Tek Sign-On Igloo yazılım yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Igloo yazılım şirket sitenizde yönetici olarak oturum açın.

2. **Denetim Masası**'na gidin.

     ![Denetim Masası](./media/igloo-software-tutorial/ic799949.png "Denetim Masası")

3. **Üyelik** sekmesinde, **oturum açma ayarları**' na tıklayın.

    ![Oturum açma ayarları](./media/igloo-software-tutorial/ic783968.png "Oturum açma ayarları")

4. SAML yapılandırması bölümünde **SAML kimlik doğrulamasını Yapılandır**' a tıklayın.

    ![SAML yapılandırması](./media/igloo-software-tutorial/ic783969.png "SAML yapılandırması")

5. **Genel yapılandırma** bölümünde aşağıdaki adımları uygulayın:

    ![Genel Yapılandırma](./media/igloo-software-tutorial/ic783970.png "Genel Yapılandırma")

    a. **Bağlantı adı** metin kutusuna yapılandırmanız için bir özel ad yazın.

    b. **IDP oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **IDP oturum kapatma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    d. **Oturum kapatma yanıtı ' nı seçin ve http türünü** **Post** olarak isteyin.

    e. Azure portal 'den indirilen Not defteri 'nde **Base-64** kodlu sertifikanızı açın, içeriğini panonuza kopyalayın ve **ortak sertifika** metin kutusuna yapıştırın.

6. **Yanıt ve kimlik doğrulama yapılandırmasında** aşağıdaki adımları gerçekleştirin:

    ![Yanıt ve kimlik doğrulama yapılandırması](./media/igloo-software-tutorial/IC783971.png "Yanıt ve kimlik doğrulama yapılandırması")
  
    a. **Kimlik sağlayıcısı** olarak **Microsoft ADFS**' yi seçin.

    b. **Tanımlayıcı türü** olarak **e-posta adresi**' ni seçin. 

    c. **E-posta özniteliği** metin kutusuna **emapostaadresi** yazın.

    d. **First Name öznitelik** metin kutusuna, **1 yazın.**

    e. **Son ad öznitelik** metin kutusuna **Soyadı** yazın.

7. Yapılandırmayı tamamlamak için aşağıdaki adımları gerçekleştirin:

    ![Oturum açarken Kullanıcı oluşturma](./media/igloo-software-tutorial/IC783972.png "Oturum açarken Kullanıcı oluşturma") 

    a. **Oturum açma sırasında Kullanıcı oluşturma** sırasında, **sitenizde oturum açtıklarında yeni kullanıcı oluştur**' u seçin.

    b. **Oturum açma ayarları** olarak, **"oturum aç" ekranında SAML kullan düğmesini** seçin.

    c. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alan türü **brittasimon@yourcompanydomain.extension**  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Igloo yazılımına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Igloo Software**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Igloo Software**' i seçin.

    ![Uygulamalar listesindeki Igloo yazılım bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-igloo-software-test-user"></a>Igloo yazılım test kullanıcısı oluşturma

Kullanıcı sağlamayı Igloo yazılımına yapılandırabileceğiniz bir eylem öğesi yoktur.  

Atanan bir Kullanıcı erişim panelini kullanarak Igloo yazılımda oturum açmaya çalıştığında, Igloo Software kullanıcının mevcut olup olmadığını denetler.  Henüz kullanılabilir bir kullanıcı hesabı yoksa, Igloo yazılımı tarafından otomatik olarak oluşturulur.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Igloo yazılım kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Igloo yazılımda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)