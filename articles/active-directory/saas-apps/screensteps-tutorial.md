---
title: 'Öğretici: ScreenSteps ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ekran adımları arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 3816239798290318404980ded388b726d8134395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999799"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Öğretici: ekran adımlarıyla Azure Active Directory tümleştirme

Bu öğreticide, ScreenSteps Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Ekran adımlarını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, ScreenSteps erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla ekran adımlarında (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini ekran adımlarıyla yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* ScreenSteps çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Ekran adımları **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-screensteps-from-the-gallery"></a>Galeriden ekran adımları ekleme

ScreenSteps 'ın Azure AD ile tümleştirilmesini yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize ScreenSteps eklemeniz gerekir.

**Galeriden ekran adımları eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **ScreenSteps** yazın, sonuç panelinden **ScreenSteps** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesindeki ekran adımları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına bağlı olarak, ScreenSteps ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve ekran adımlarında ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, ScreenSteps ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[ScreenSteps çoklu oturum açmayı yapılandırın](#configure-screensteps-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan ekran adımlarında bir Britta Simon 'a sahip olmak için, **[ScreenSteps test kullanıcısı oluşturun](#create-screensteps-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, ScreenSteps ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **ekran adımları** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ekran adımları etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Bu değer gerçek değil. Bu değeri, Bu öğreticinin ilerleyen kısımlarında açıklanan gerçek Sign-On URL 'siyle güncelleştirin.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Ekran adımlarını ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-screensteps-single-sign-on"></a>Tek Sign-On ScreenSteps 'ı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, ekran adımları şirket sitenizde yönetici olarak oturum açın.

1. **Hesap ayarları**' na tıklayın.

    ![Hesap yönetimi](./media/screensteps-tutorial/ic778523.png "Hesap yönetimi")

1. **Çoklu oturum açma**' ya tıklayın.

    !["Çoklu oturum açma" seçili gösteren ekran görüntüsü.](./media/screensteps-tutorial/ic778524.png "Uzaktan kimlik doğrulaması")

1. **Çoklu oturum açma uç noktası oluştur**' a tıklayın.

    ![Uzaktan kimlik doğrulaması](./media/screensteps-tutorial/ic778525.png "Uzaktan kimlik doğrulaması")

1. **Çoklu oturum açma uç noktası oluştur** bölümünde aşağıdaki adımları uygulayın:

    ![Kimlik doğrulama uç noktası oluşturma](./media/screensteps-tutorial/ic778526.png "Kimlik doğrulama uç noktası oluşturma")

    a. **Title** metin kutusuna bir başlık yazın.

    b. **Mod** listesinden **SAML**' yi seçin.

    c. **Oluştur**’a tıklayın.

1. Yeni uç noktayı **düzenleyin** .

    ![Uç noktayı Düzenle](./media/screensteps-tutorial/ic778528.png "Uç noktayı Düzenle")

1. **Çoklu oturum açma uç noktası düzenleme** bölümünde aşağıdaki adımları uygulayın:

    ![Uzaktan kimlik doğrulama uç noktası](./media/screensteps-tutorial/ic778527.png "Uzaktan kimlik doğrulama uç noktası")

    a. **Yenı SAML sertifika dosyasını karşıya yükle**' ye tıklayın ve ardından Azure Portal ' den indirdiğiniz sertifikayı karşıya yükleyin.

    b. Azure portal **Uzaktan oturum açma URL** metin kutusuna kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Azure portal 'tan **Çıkış DıŞı URL** metin kutusuna kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    d. Kullanıcıları sağlandıklarında atamak için bir **Grup** seçin.

    e. **Güncelleştir**’e tıklayın.

    f. **SAML TÜKETICISI URL** 'sini panoya kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **oturum açma URL 'si** metin kutusuna yapıştırın.

    örneğin: **Çoklu oturum açma uç noktasını Düzenle '** ye dönün.

    h. Bu uç noktayı, ekran adımlarında oturum açan tüm kullanıcılar için kullanmak üzere **hesabı için varsayılan yap** düğmesine tıklayın. Alternatif olarak, bu uç noktayı **ekran adımlarında** belirli siteler için kullanmak üzere **siteye Ekle** düğmesine tıklayabilirsiniz.

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

Bu bölümde, ScreenSteps 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra da **ekran adımları**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **ekran adımları**' nı seçin.

    ![Uygulamalar listesinde ScreenSteps bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-screensteps-test-user"></a>ScreenSteps test kullanıcısı oluşturma

Bu bölümde, ekran adımlarında Britta Simon adlı bir Kullanıcı oluşturacaksınız. ScreenSteps platformunda kullanıcıları eklemek için, [ScreenSteps istemci destek ekibi](https://www.screensteps.com/contact) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde ekran adımları kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız ekran adımlarında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)