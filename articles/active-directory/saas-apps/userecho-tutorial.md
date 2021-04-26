---
title: 'Öğretici: UserEcho ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve UserEcho arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: be52d220848a27c1307e71fdb7cc4dccfaeb9618
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92509492"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Öğretici: UserEcho ile Azure Active Directory tümleştirme

Bu öğreticide, UserEcho Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
UserEcho 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de UserEcho erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla UserEcho (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini UserEcho ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* UserEcho çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* UserEcho **SP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-userecho-from-the-gallery"></a>Galeriden UserEcho ekleme

UserEcho 'in Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden Kullanıcı yankısını yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden UserEcho eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **UserEcho** yazın, sonuç panelinden **UserEcho** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde UserEcho](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre UserEcho ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve UserEcho içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

UserEcho ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[UserEcho çoklu oturum açmayı yapılandırma](#configure-userecho-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan UserEcho ' de bir Britta Simon 'un bir karşılığı olacak şekilde **[UserEcho test kullanıcısı oluşturun](#create-userecho-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı UserEcho ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **UserEcho** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![UserEcho etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.userecho.com/`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [UserEcho istemci destek ekibine](https://feedback.userecho.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **UserEcho ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-userecho-single-sign-on"></a>UserEcho tek Sign-On yapılandırma

1. Başka bir tarayıcı penceresinde, UserEcho şirket sitenizde yönetici olarak oturum açın.

2. Üstteki araç çubuğunda, menüyü genişletmek için Kullanıcı adı ' na tıklayın ve ardından **Kurulum**' a tıklayın.
   
    ![Ekran görüntüsü UserEcho sitesinden seçilen kurulumu gösterir.](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. **Tümleştirmeler**' e tıklayın.
   
    ![Ekran görüntüsünde, Ayarlar menüsünden seçilen tümleştirmeler gösterilmektedir.](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. **Web sitesi**' ne ve ardından **Çoklu oturum açma (SAML2)** seçeneğine tıklayın.
   
    ![Ekran görüntüsü, tümleştirmeler menüsünden çoklu oturum açma SAML2 seçili olduğunu gösterir.](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. **Çoklu oturum açma (SAML)** sayfasında, aşağıdaki adımları gerçekleştirin:
   
    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz çoklu oturum açma SAML sayfasını gösterir.](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. **SAML** özellikli olarak **Evet**' i seçin.
    
    b. Azure portal **SAML SSO URL** metin kutusuna kopyaladığınız **oturum açma URL 'sini** yapıştırın.
    
    c. Azure portal 'den **uzak oturum kapatma URL 'si** metin kutusuna kopyaladığınız **oturum kapatma URL 'sini** yapıştırın.
    
    d. İndirilen sertifikanızı Not defteri 'nde açın, içeriği kopyalayın ve **X. 509.952 Certificate** metin kutusuna yapıştırın.
    
    e. **Kaydet**’e tıklayın.

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

Bu bölümde, UserEcho erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **UserEcho**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **UserEcho** öğesini seçin.

    ![Uygulamalar listesindeki UserEcho bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-userecho-test-user"></a>UserEcho test kullanıcısı oluştur

Bu bölümün amacı, UserEcho ' de Britta Simon adlı bir Kullanıcı oluşturmaktır.

**UserEcho 'de Britta Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

1. UserEcho şirket sitenizde yönetici olarak oturum açın.

2. Üstteki araç çubuğunda, menüyü genişletmek için Kullanıcı adı ' na tıklayın ve ardından **Kurulum**' a tıklayın.
   
    ![Ekran görüntüsü UserEcho sitesinden seçilen kurulumu gösterir.](./media/userecho-tutorial/tutorial_userecho_06.png)

3. **Kullanıcılar bölümünü genişletmek** için **Kullanıcılar**' a tıklayın.
   
    ![Ekran görüntüsü ayarlar menüsünden seçilen kullanıcıları gösterir.](./media/userecho-tutorial/tutorial_userecho_10.png)

4. **Kullanıcılar**’a tıklayın.
   
    ![Ekran görüntüsü kullanıcıların seçili olduğunu gösterir.](./media/userecho-tutorial/tutorial_userecho_11.png)

5. **Yeni Kullanıcı davet et**' e tıklayın.
   
    ![Ekran görüntüsü yeni kullanıcı denetimini davet et ' i gösterir.](./media/userecho-tutorial/tutorial_userecho_12.png)

6. **Yeni Kullanıcı davet et** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Ekran görüntüsü, Kullanıcı bilgilerini girebileceğiniz yeni kullanıcı davet et iletişim kutusunu gösterir.](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. **Ad** metin kutusuna, Britta Simon gibi kullanıcının adını yazın.
    
    b.  **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini yazın Brittasimon@contoso.com .
    
    c. **Davet et**' e tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde UserEcho kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız UserEcho ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)