---
title: 'Öğretici: Sauce Labs ile tümleştirme Azure Active Directory-mobil ve Web testi | Microsoft Docs'
description: Azure Active Directory ve Sauce Labs ile mobil ve Web testi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 087e2d7f2db8f27378b54675095e97256d6aae9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895135"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Öğretici: Sauce Labs ile tümleştirme Azure Active Directory-mobil ve Web testi

Bu öğreticide, Sauce Labs-mobil ve Web testi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Azure AD ile Sauce laboratuvarlarını tümleştirme-mobil ve Web testi aşağıdaki avantajları sağlar:

* Azure AD 'de, Sauce Labs 'e erişimi olan ve mobil ve Web testi 'nin denetimini yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Sauce Labs-mobil ve Web testi (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Sauce Labs ile, mobil ve Web testi ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Sauce Labs-mobil ve Web testi çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Sauce Labs-mobil ve Web testi **IDP** tarafından başlatılan SSO 'yu destekler
* Sauce Labs-mobil ve Web testi, **tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Galeri 'den Sauce Labs-mobil ve Web testi ekleme

Sauce Labs-mobil ve Web testi tümleştirmesini Azure AD 'ye göre yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Sauce Labs-mobil ve Web testi eklemeniz gerekir.

**Galeriden Sauce Labs-mobil ve Web testi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Sauce Labs-mobil ve Web testi** yazın, sonuç panelinden **Sauce Labs-mobil ve Web testi** ' ni seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Sauce Labs-sonuçlar listesinde mobil ve Web testi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açma 'yı, **Britta Simon** adlı bir test kullanıcısına bağlı olarak Sauce Labs-mobil ve Web testi ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilişkili kullanıcı arasındaki bir bağlantı ilişkisi ile Sauce Labs-mobil ve Web testi kurulması gerekir.

Azure AD çoklu oturum açma 'yı Sauce Labs ile birlikte yapılandırmak ve test etmek için, mobil ve Web testi için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Sauce laboratuvarlarını yapılandırın-mobil ve Web testi çoklu oturum açma](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Sauce **[Labs-mobil ve Web testi test kullanıcısı oluşturma](#create-sauce-labs---mobile-and-web-testing-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Sauce laboratuvarlarında Britta Simon 'A ve mobil ve Web testine yönelik bir karşılığına sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı Sauce Labs-mobil ve Web testi ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Sauce Labs-mobil ve Web testi** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

    ![Sauce Labs-mobil ve Web testi etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/preintegrated.png)

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Sauce Labs 'ı ayarlama-mobil ve Web testi** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Sauce Labs 'i yapılandırma-mobil ve Web testi tek Sign-On

1. Farklı bir Web tarayıcısı penceresinde, Sauce laboratuvarlarınızın ve mobil ve Web testi şirket sitenizde yönetici olarak oturum açın.

2. **Kullanıcı simgesine** tıklayın ve **Takım Yönetimi** sekmesini seçin.

    !["Kullanıcı" simgesini ve "ekip yönetimi" açılan ekranının seçili olduğunu gösteren ekran görüntüsü.](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Metin kutusuna **etki alanı adınızı** girin.

    ![Metin kutusunda örnek bir etki alanı adı gösteren ekran görüntüsü.](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. **Yapılandır** sekmesini tıklatın.

    !["Çoklu oturum açma etkinleştirildi" altında seçili "Yapılandır" sekmesini gösteren ekran görüntüsü.](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. **Çoklu oturum açmayı Yapılandır** bölümünde aşağıdaki adımları gerçekleştirin.

    ![Tek Sign-On yapılandırma](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. **Araştır** ' a tıklayın ve indirilen meta veri dosyasını Azure AD 'den karşıya yükleyin.

    b. **Tam ZAMANıNDA sağlamayı Izin ver** onay kutusunu seçin.

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
  
    b. **Kullanıcı adı** alan türü`brittasimon@yourcompanydomain.extension`  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Sauce Labs 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Sauce Labs-mobil ve Web testi**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Sauce Labs-mobil ve Web testi**' ni seçin.

    ![Uygulamalar listesindeki Sauce Labs-mobil ve Web testi bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Sauce Labs oluşturma-Mobil ve Web testi test kullanıcısı

Bu bölümde, Britta Simon adlı bir Kullanıcı, Sauce Labs-mobil ve Web testinde oluşturulur. Sauce Labs-mobil ve Web testi, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten Sauce Labs-mobil ve Web testinde yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!Note]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Sauce Labs-mobil ve Web testi destek ekibine](mailto:support@saucelabs.com)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Sauce Labs-mobil ve Web testi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Sauce laboratuvarları-mobil ve Web testinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)