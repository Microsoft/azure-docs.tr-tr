---
title: 'Öğretici: artık kuruluş şemasına tümleştirin Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve kuruluş şeması arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6b90be8e951c0e30c99d0bb16f85e49cf991f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522334"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Öğretici: artık kuruluş şemasına tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile kuruluş şemasını nasıl tümleştirileceğini öğreneceksiniz.
Artık kuruluş şemasını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, artık kuruluş şemasına erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla artık kuruluş şemasına (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini artık kuruluş şemasına göre yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Kuruluş şeması artık çoklu oturum açma özellikli abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Kuruluş şeması artık **SP** ve **IDP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-orgchart-now-from-the-gallery"></a>Galeri 'den şimdi kuruluş şeması ekleme

Kuruluş şemasının tümleştirmesini şimdi Azure AD ile yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize şimdi bir kuruluş şeması eklemeniz gerekir.

**Galeri 'den şimdi bir kuruluş şeması eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Şimdi kuruluş şeması** yazın, sonuç panelinden **kuruluş şeması** ' nı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Artık sonuçlar listesinde kuruluş şeması](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açma 'yı artık **Britta Simon** adlı bir test kullanıcısına bağlı olarak, kuruluş şemasına yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilgili kullanıcının kuruluş şemasına ait bağlantı ilişkisinin artık kurulması gerekir.

Azure AD çoklu oturum açma 'yı kuruluş şemasına göre yapılandırmak ve test etmek için şu yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[kuruluş şemasını şimdi çoklu oturum açma yapılandırması](#configure-orgchart-now-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Kuruluş şeması oluşturma artık](#create-orgchart-now-test-user)** kullanıcının Azure AD gösterimine bağlı olan, artık kuruluş şemasına yönelik Britta Simon 'ın bir karşılığı olacak şekilde test edin.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı şimdi kuruluş şemasına göre yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **kuruluş şeması artık** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımı uygulayın:

    ![Kuruluş şeması artık etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-identifier.png)

    **Tanımlayıcı** metin kutusuna bir URL yazın:`https://sso2.orgchartnow.com`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![image](common/both-preintegrated-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>`**Azure AD tanımlayıcısı** , daha sonra öğretici ' de açıklanacak olan **kuruluş şemasına şimdi ayarla** bölümünde kopyalanır.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **Kuruluş şemasını şimdi ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-orgchart-now-single-sign-on"></a>Kuruluş şemasını şimdi tek Sign-On yapılandırma

**Artık kuruluş şemasına** yönelik çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta verileri XML** 'sini ve Azure Portal ' den uygun kopyalanmış URL 'leri [Şimdi kuruluş şemasına](mailto:ocnsupport@officeworksoftware.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

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

Bu bölümde, artık kuruluş şemasına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **kuruluş şeması**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Şimdi kuruluş şeması**' nı seçin.

    ![Artık kuruluş şeması uygulamalar listesinde bağlantı kuruyor](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-orgchart-now-test-user"></a>Kullanıcı şemasını şimdi test et

Azure AD kullanıcılarının şu anda kuruluş şemasına oturum açmasını sağlamak için, artık kuruluş şemasına sağlanması gerekir. 

1. Kuruluş şeması artık, varsayılan olarak etkinleştirilen tam zamanında sağlamayı desteklemektedir. Henüz yoksa, bir kuruluş şemasına erişme denemesi sırasında yeni bir Kullanıcı oluşturulur. Just-In-Time Kullanıcı sağlama özelliği, bir SSO isteği tanınan bir ıDP 'den geldiğinde ve SAML onaylama 'daki e-posta Kullanıcı listesinde bulunamadığında yalnızca **salt okunurdur** bir kullanıcı oluşturur. Bu otomatik sağlama özelliği için, artık kuruluş şeması 'nda **genel** başlıklı bir erişim grubu oluşturmanız gerekir. Lütfen bir erişim grubu oluşturmak için aşağıdaki adımları izleyin:

    a. Kullanıcı arabiriminin sağ üst köşesindeki **dişli** ' a tıkladıktan sonra **grupları yönet** seçeneğine gidin.

    ![Artık kuruluş şeması grupları](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. **Ekle** simgesini seçin ve grubu **genel** olarak adlandırın ve ardından **Tamam**' a tıklayın. 

    ![Artık kuruluş şemasını ekleyin](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Genel veya salt okuma kullanıcılarının erişebileceği klasörü (ler) i seçin:

    ![Artık kuruluş şeması klasörleri](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. Klasörleri **kilitleyerek** yalnızca yönetici kullanıcıların bunları değiştirebilmesini sağlayın. Ardından **Tamam**' a basın.

    ![Kuruluş şeması şimdi kilitle](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. **Yönetici** kullanıcılar oluşturmak ve kullanıcıları **okumak/yazmak** için, SSO aracılığıyla ayrıcalık düzeyine erişebilmek için bir kullanıcıyı el ile oluşturmanız gerekir. Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:

    a. Şimdi bir güvenlik yöneticisi olarak kuruluş şemasına oturum açın.

    b.  Sağ üst köşedeki **Ayarlar** ' a tıklayın ve ardından **Kullanıcıları Yönet**' e gidin.

    ![Artık kuruluş şeması ayarları](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. **Ekle** ' ye tıklayın ve aşağıdaki adımları uygulayın:

    ![Kuruluş şeması artık yönetme](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * **Kullanıcı kimliği** metin kutusunda, **brittasıon \@ contoso.com** gibi kullanıcı kimliğini girin.

    * **E-posta adresi** metin kutusuna, **brittasıon \@ contoso.com** gibi kullanıcının e-postasını girin.

    * **Ekle**'ye tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde şimdi kuruluş şeması kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız kuruluş şemasına otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)