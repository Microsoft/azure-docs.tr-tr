---
title: 'Öğretici: Pingboard ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Pingboard arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d7b090eb5228a449212ceebd6cd299c5f8860089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520804"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Öğretici: Pingboard ile Azure Active Directory tümleştirme

Bu öğreticide, Pingboard 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Pingboard 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Pingboard erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Pingboard 'e (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Pingboard ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Pingboard çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Pingboard **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

* Pingboard [Otomatik Kullanıcı sağlamayı](./pingboard-provisioning-tutorial.md) destekler 

## <a name="adding-pingboard-from-the-gallery"></a>Galeriden Pingboard ekleme

Pingboard 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden Pingboard ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Pingboard eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **pingboard** yazın, sonuç panelinden **pingboard** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde pingboard](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre pingboard ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Pingboard içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD 'yi Pingboard ile birlikte yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Pingboard çoklu oturum açma yapılandırma](#configure-pingboard-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimi ile bağlantılı bağlantı panosu 'nda Britta Simon 'a sahip olmak için **[pingboard test kullanıcısı oluşturun](#create-pingboard-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD 'yi Pingboard ile çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **pingboard** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    !["Tanımlayıcı" ve "Yanıtla U R L" metin kutuları vurgulanmış ve "Kaydet" düğmesi seçili olan "temel S A M L yapılandırmasını" gösteren ekran görüntüsü.](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna bir URL yazın:`http://app.pingboard.com/sp`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<entity-id>.pingboard.com/auth/saml/consume`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Pingboard etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Pingboard istemci destek ekibine](https://support.pingboard.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **Pingboard ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-pingboard-single-sign-on"></a>Pingboard tek Sign-On yapılandırma

1. Pingboard tarafında SSO 'yu yapılandırmak için yeni bir tarayıcı penceresi açın ve Pingboard hesabınızda oturum açın. Çoklu oturum açmayı ayarlamak için bir Pingboard yöneticisi olmanız gerekir.

2. Üst menüden **uygulamalar > tümleştirmeler** ' i seçin

    ![Tek Sign-On yapılandırma](./media/pingboard-tutorial/Pingboard_integration.png)

3. **Tümleştirmeler** sayfasında, **"Azure Active Directory"** kutucuğunu bulun ve tıklatın.

    ![Pingboard tek Sign-On tümleştirme](./media/pingboard-tutorial/Pingboard_aad.png)

4. Aşağıdaki kalıcı bölümünde **"Yapılandır"** a tıklayın

    ![Pingboard yapılandırma düğmesi](./media/pingboard-tutorial/Pingboard_configure.png)

5. Aşağıdaki sayfada, "Azure SSO tümleştirmesinin etkin" olduğunu fark edersiniz. İndirilen meta veri XML dosyasını bir not defteri içinde açın ve içeriği **IDP meta verilerinde** yapıştırın.

    ![Pingboard SSO yapılandırma ekranı](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. Dosya doğrulanıp, her şeyin doğru olması halinde çoklu oturum açma özelliği de etkinleştirilir.

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

Bu bölümde, Pingboard 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve **pingboard**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Pingboard**' ı seçin.

    ![Uygulamalar listesindeki Pingboard bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-pingboard-test-user"></a>Pingboard test kullanıcısı oluştur

Bu bölümün amacı, Pingboard 'de Britta Simon adlı bir Kullanıcı oluşturmaktır. Pingboard, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamasını destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](pingboard-provisioning-tutorial.md) .

**Kullanıcı el ile oluşturmanız gerekiyorsa aşağıdaki adımları gerçekleştirin:**

1. Pingboard şirket sitenizde yönetici olarak oturum açın.

2. **Dizin** sayfasında **"çalışan Ekle"** düğmesine tıklayın.

    ![Çalışan Ekle](./media/pingboard-tutorial/create_testuser_add.png)

3. **"Çalışan Ekle"** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Kişileri davet et](./media/pingboard-tutorial/create_testuser_name.png)

    a. **Tam ad** metin kutusuna, **Britta Simon** gibi kullanıcının tam adını yazın.

    b. **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini yazın **brittasimon@contoso.com** .

    c. **Iş unvanı** metin kutusunda Britta Simon iş unvanını yazın.

    d. **Konum** açılır listesinde Britta Simon konumunu seçin.

    e. **Ekle**'ye tıklayın.

4. Kullanıcının eklenmesini onaylamak için bir onay ekranı gönderilir.

    ![göründüğünü](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını doğrulamak için bir bağlantıyı izler.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Pingboard kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Pingboard ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)

- [Kullanıcı sağlamayı yapılandırma](./pingboard-provisioning-tutorial.md)