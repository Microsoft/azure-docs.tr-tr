---
title: 'Öğretici: akıllı Işe Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve akıllı Işe ıcılar arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: b7de874cc074faa63203c58638fb04cc5cb2282b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514937"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Öğretici: akıllı Işe ıcılar Azure Active Directory tümleştirme

Bu öğreticide, akıllı makineleri Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Akıllı makineleri Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, akıllı Işe Içeri erişimi olan erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesapları ile akıllı Işe (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini akıllı Işe yönelik olarak yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Smartişveren çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Smartişveren **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-smartrecruiters-from-the-gallery"></a>Galeriden akıllı Işe ıcılar ekleme

Akıllı Işveren 'nın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden akıllı Işe 'Ler, yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden akıllı makineleri eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **smartişveren** yazın, sonuç panelinden **akıllı işe ıcılar** ' nı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde akıllı Işe ıcılar](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre smartişveren ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve akıllı iş içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı akıllı Işe yönelik olarak yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[akıllı Işe Içeri çoklu oturum açmayı yapılandırın](#configure-smartrecruiters-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcı Azure AD gösterimi ile bağlantılı Smartişveren 'da Britta Simon 'ın bir karşılığı olacak şekilde **[akıllı işe yönelik test kullanıcısı oluşturun](#create-smartrecruiters-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı akıllı Işe yönelik olarak yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **smartişveren** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü; tanımlayıcı girebileceğiniz, yanıt U R L ve Kaydet ' i seçebileceğiniz temel SAML yapılandırmasını gösterir.](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [akıllı işe yönelik istemci desteği ekibine](https://www.smartrecruiters.com/about-us/contact-us/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Akıllı Işe Içeri ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-smartrecruiters-single-sign-on"></a>Smartişveren tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, akıllı Işveren şirket sitenizde yönetici olarak oturum açın.

1. **Ayarlar/yönetici** bölümüne gidin.

    ![Ekran görüntüsünde bir menüden Ayarlar/yönetici seçili görüntülenir.](./media/smartrecruiters-tutorial/configure.png)

1. **Yapılandırma** bölümünde **Web SSO**' ya tıklayın.

    ![Ekran görüntüsü, yapılandırmadan seçilen Web S S 'yi gösterir.](./media/smartrecruiters-tutorial/configure1.png)

1. **Web SSO etkinleştirme**' ye geçiş yapın.

    ![Ekran görüntüsü Web S O denetimini etkinleştir ' i gösterir.](./media/smartrecruiters-tutorial/configure2.png)

1. **Kimlik sağlayıcısı yapılandırması**' nda aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz kimlik sağlayıcısı yapılandırmasını gösterir.](./media/smartrecruiters-tutorial/configure4.png)

    a. **Kimlik sağlayıcısı URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. Not defteri 'nde Azure portal indirdiğiniz açık **sertifika (base64)** , içeriğini kopyalayın ve **kimlik sağlayıcısı sertifikası** metin kutusuna yapıştırın.

1. **Web SSO yapılandırmasını kaydet**' e tıklayın.

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

Bu bölümde, akıllı Işe erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **akıllı işe ıcılar**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **akıllı Işe iciler**' ı seçin.

    ![Uygulamalar listesindeki akıllı Işe Içeri bağlantıları](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-smartrecruiters-test-user"></a>Akıllı Işe Içeri test kullanıcısı oluşturma

Bu bölümde, Smartişveren 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Smartişveren platformu 'ndaki kullanıcıları eklemek için akıllı iş ve [destek ekibi](https://www.smartrecruiters.com/about-us/contact-us/) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Akıllı Işveren kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız akıllı Işe Içeri otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)