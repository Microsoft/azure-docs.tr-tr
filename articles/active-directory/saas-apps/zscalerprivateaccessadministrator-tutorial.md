---
title: 'Öğretici: Zscaler özel erişim yöneticisiyle Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Zscaler özel erişim Yöneticisi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 803f5c25429b33e6d1af130196d38e125b410eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891117"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Öğretici: Zscaler özel erişim yöneticisiyle Azure Active Directory tümleştirme

Bu öğreticide, Zscaler özel erişim yöneticisini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Zscaler özel erişim yöneticisini Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Zscaler özel erişim yöneticisine erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler özel erişim yöneticisine (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Zscaler özel erişim yöneticisiyle yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Zscaler özel erişim Yöneticisi çoklu oturum açma etkin aboneliği

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Zscaler özel erişim Yöneticisi **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Galeriden Zscaler özel erişim Yöneticisi ekleme

Zscaler özel erişim Yöneticisi 'nin tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Zscaler özel erişim Yöneticisi ' ni, Galeri 'den yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden Zscaler özel erişim yöneticisi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Zscaler özel erişim Yöneticisi** yazın, sonuç panelinden **Zscaler özel erişim Yöneticisi** ' ni seçin, sonra da uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Zscaler özel erişim Yöneticisi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, Zscaler özel erişim yöneticisiyle, **Britta Simon** adlı bir test kullanıcısına göre yapılandırır ve test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Zscaler özel erişim Yöneticisi içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı Zscaler özel erişim yöneticisiyle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Zscaler özel erişim yöneticisini yapılandırma çoklu oturum açma](#configure-zscaler-private-access-administrator-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Zscaler özel erişim Yöneticisi test kullanıcısına](#create-zscaler-private-access-administrator-test-user)** , Zscaler özel erişim Yöneticisi 'Nde kullanıcının Azure AD gösterimine bağlanmış bir Britta Simon 'un bir karşılığı olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı Zscaler özel erişim yöneticisiyle yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Zscaler özel erişim Yöneticisi** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz temel SAML yapılandırmasını gösterir.](common/idp-relay.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.private.zscaler.com/auth/sso`

    c. **Ek URL 'Ler ayarla**' ya tıklayın.

    d. **Geçiş durumu** metin kutusuna bir URL yazın:`idpadminsso`

5.  Uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımı uygulayın:

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/both-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Zscaler özel erişim Yöneticisi istemci destek ekibine](https://help.zscaler.com/zpa-submit-ticket) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **Zscaler özel erişim yöneticisini ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Zscaler özel erişim Yöneticisi tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak Zscaler özel erişim Yöneticisi ' ne oturum açın.

2. Üstte, **Yönetim** ' e tıklayın ve **kimlik doğrulama** bölümüne gidin **IDP yapılandırması**' na tıklayın.

    ![Zscaler özel erişim yönetici Yöneticisi](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Sağ üst köşede **IDP Yapılandırması Ekle**' ye tıklayın. 

    ![Zscaler özel erişim Yöneticisi ek DP](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. **IDP Yapılandırması Ekle** sayfasında, aşağıdaki adımları uygulayın:
 
    ![Zscaler özel erişim Yöneticisi ıdpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. **IDP meta veri dosyası yükleme** alanına Indirilen meta veri dosyasını Azure AD 'den karşıya yüklemek Için **Dosya Seç** ' e tıklayın.

    b. **IDP meta verilerini** Azure AD 'den okur ve tüm alan bilgilerini aşağıda gösterildiği gibi doldurur.

    ![Zscaler özel erişim Yöneticisi ıdpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. **Yönetici** olarak **Çoklu oturum açma** seçeneğini belirleyin.

    d. Etki **alanları** alanından etki alanınızı seçin.
    
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
  
    b. **Kullanıcı adı** alanına yazın `brittasimon@yourcompanydomain.extension` . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Zscaler özel erişim yöneticisine erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Zscaler özel erişim Yöneticisi**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Zscaler özel erişim Yöneticisi**' ni seçin.

    ![Uygulamalar listesinde Zscaler özel erişim Yöneticisi bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Zscaler özel erişim Yöneticisi test kullanıcısı oluşturma

Azure AD kullanıcılarının Zscaler özel erişim Yöneticisi 'nde oturum açmasını etkinleştirmek için, bu kullanıcıların Zscaler özel erişim Yöneticisi ' nde sağlanması gerekir. Zscaler özel erişim Yöneticisi söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Zscaler özel erişim Yöneticisi şirket sitenizde yönetici olarak oturum açın.

2. Üstte, **Yönetim** ' e tıklayın ve **kimlik doğrulama** bölümüne gidin **IDP yapılandırması**' na tıklayın.

    ![Zscaler özel erişim yönetici Yöneticisi](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Menünün sol tarafındaki **Yöneticiler** ' e tıklayın.

    ![Zscaler özel erişim yönetici Yöneticisi](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Sağ üst köşede **yönetici Ekle**' ye tıklayın:

    ![Zscaler özel erişim Yöneticisi Yönetici Ekle](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. **Yönetici Ekle** sayfasında, aşağıdaki adımları uygulayın:

    ![Zscaler özel erişim Yöneticisi Kullanıcı Yöneticisi](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Kullanıcı **adı** metin kutusuna, gibi kullanıcının e-postasını girin BrittaSimon@contoso.com .

    b. **Parola** metin kutusuna parolayı yazın.

    c. **Parolayı Onayla** metin kutusuna parolayı yazın.

    d. **Zscaler özel erişim Yöneticisi** olarak **rol** ' i seçin.

    e. **E-posta** metin kutusuna, gibi kullanıcının e-postasını girin BrittaSimon@contoso.com .

    f. **Telefon** metin kutusuna telefon numarasını yazın.

    örneğin: **Saat dilimi** metin kutusunda, saat dilimini seçin.

    h. **Kaydet**’e tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Zscaler özel erişim Yöneticisi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Zscaler özel erişim yöneticisinin oturumunu otomatik olarak açmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)