---
title: 'Öğretici: Ziflow ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Ziflow arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e10ca98e0c9257187288d02483ed81915a7b321d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92894772"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Öğretici: Ziflow ile tümleştirme Azure Active Directory

Bu öğreticide, Ziflow 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Ziflow 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Ziflow erişimi olan ' i denetleyebilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Ziflow (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Ziflow ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Ziflow çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Ziflow, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-ziflow-from-the-gallery"></a>Galeriden Ziflow ekleme

Ziflow 'ın Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden Ziflow ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Ziflow eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **ziflow** yazın, sonuç panelinden **ziflow** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde ziflow](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre, Ziflow ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Ziflow içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı Ziflow ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Ziflow Single oturum açmayı yapılandırın](#configure-ziflow-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Ziflow 'da Britta Simon 'ın bir karşılığı olacak şekilde **[ziflow test kullanıcısı oluşturun](#create-ziflow-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı Ziflow ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **ziflow** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ziflow etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Yukarıdaki değerler gerçek değildir. Tanımlayıcıdaki benzersiz KIMLIK değerini güncelleştirirsiniz ve gerçek değer ile URL 'yi oturum açarak Öğreticinin ilerleyen kısımlarında açıklanmıştır.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Ziflow ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-ziflow-single-sign-on"></a>Ziflow Single Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir güvenlik yöneticisi olarak Ziflow 'da oturum açın.

2. Sağ üst köşedeki avatar öğesine tıklayın ve sonra **Hesabı Yönet**' e tıklayın.

    ![Ziflow yapılandırma yönetimi](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. Sol üst kısımdaki **Çoklu oturum açma**' ya tıklayın.

    ![Ziflow yapılandırma Işareti](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. **Çoklu oturum açma** sayfasında, aşağıdaki adımları uygulayın:

    ![Ziflow yapılandırma tek](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. **SAML 2.0** olarak **tür** ' ı seçin.

    b. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Azure portal indirmiş olduğunuz Base-64 kodlu sertifikayı **x509 Imzalama sertifikasına** yükleyin.

    d. Oturumu Kapat **URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    e. **Tanımlayıcı sağlayıcınız Için yapılandırma ayarlarından** , vurgulanan benzersiz kimlik değerini kopyalayın ve Azure Portal ' de **temel SAML yapılandırmasındaki** tanımlayıcıyı ve oturum açma URL 'sini ekleyin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına yazın brittasimon@yourcompanydomain.extension . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Ziflow 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **ziflow**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Ziflow**' ı seçin.

    ![Uygulamalar listesindeki Ziflow bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-ziflow-test-user"></a>Ziflow test kullanıcısı oluşturma

Azure AD kullanıcılarının Ziflow 'da oturum açmasını sağlamak için, bu kullanıcıların Ziflow 'a sağlanması gerekir. Ziflow içinde, sağlama elle gerçekleştirilen bir görevdir.

Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:

1. Bir güvenlik yöneticisi olarak Ziflow 'da oturum açın.

2. En üstteki **kişilere** gidin.

    ![Ziflow yapılandırma kişileri](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. **Ekle** ' ye ve ardından **Kullanıcı Ekle**' ye tıklayın.

    ![Ekran görüntüsü Kullanıcı Ekle seçeneğinin seçili olduğunu gösterir.](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. **Kullanıcı Ekle** açılır penceresinde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı Ekle iletişim kutusunu gösterir.](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. **E-posta** metin kutusuna kullanıcının e-postasını girin brittasimon@contoso.com .

    b. **Ad** metin kutusuna, ilk Kullanıcı adını Britta gibi girin.

    c. **Soyadı** metin kutusuna, Simon gibi kullanıcı adının soyadını girin.

    d. Ziflow rolünüzü seçin.

    e. **1 Kullanıcı Ekle**' ye tıklayın.

    > [!NOTE]
    > Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını doğrulamak için bir bağlantıyı izler.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Ziflow kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Ziflow 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)