---
title: 'Öğretici: satıcılarla yol ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ile satıcılarla yolu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.openlocfilehash: 82fd49f8cb488a77ad379a4bcbffca154cd7610a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454907"
---
# <a name="tutorial-azure-active-directory-integration-with-dealpath"></a>Öğretici: satıcılarla yol ile tümleştirme Azure Active Directory

Bu öğreticide, satıcılarla yolunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Satıcılarla yolunu Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, satıcılarla yola erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla, satıcılarınızın (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini satıcılarla yol ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Satıcılarla yol çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Satıcılarla yol **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-dealpath-from-the-gallery"></a>Galeriden satıcılarla yol ekleme

Satıcılarla Path 'in Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize bir anlaşana yol eklemeniz gerekir.

**Galeriden satıcılarla yolu eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **satıcılarla yolu** yazın, sonuç panelinden **satıcılarla yolu** seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesindeki satıcılarla yolu](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına bağlı olarak, satıcıyolu ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve satıcılarla yolundaki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı, satıcılarla birlikte yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurmanız gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[Satıcıyolu çoklu oturum açmayı yapılandırın](#configure-dealpath-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcı Azure AD gösterimi ile bağlantılı olan satıcılarla yolunda Britta Simon 'a sahip olmak için, **[satıcıyolu test kullanıcısı oluşturun](#create-dealpath-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, Satıcıyolu ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **satıcılarla yol** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Satıcılarla yol etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://app.dealpath.com/account/login`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://api.dealpath.com/saml/metadata/<ID>`

    > [!NOTE]
    > Tanımlayıcı değeri gerçek değil. Değeri gerçek tanımlayıcıyla güncelleştirin. Bu değerleri almak için [satıcılarla yol istemci destek ekibine](mailto:kenter@dealpath.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i, gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. Satıcıyolunu **Ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-dealpath-single-sign-on"></a>Sign-On, tek başına satıcılarla yolunu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak satıcılarla yolunda oturum açın.

2. Sağ üst köşedeki **yönetici araçları** ' na tıklayın ve **tümleştirmelere** gidin **SAML 2,0 kimlik doğrulaması** bölümünde **güncelleştirme ayarları**' na tıklayın:

    !["A M L 2,0 kimlik doğrulaması" ve "güncelleştirme ayarları" seçiliyken "yönetici araçları-tümleştirmeler" sayfasını gösteren ekran görüntüsü.](./media/dealpath-tutorial/tutorial_dealpath_admin.png)

3. **SAML 2,0 kimlik doğrulamasını ayarlama** sayfasında, aşağıdaki adımları uygulayın:

    ![Satıcılarla yol yapılandırması](./media/dealpath-tutorial/tutorial_dealpath_saml.png) 

    a. **SAML SSO URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Kimlik sağlayıcısı veren** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın.

    c. İndirilen **sertifika (base64)** dosyasının içeriğini Not defteri 'nde kopyalayın ve sonra **genel sertifika** metin kutusuna yapıştırın.

    d. **Güncelleştirme ayarları**' na tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** yazın.
  
    b. **Kullanıcı adı** alanına **brittasıon \@ yourcompanydomain. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, satıcılarla yola erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **satıcılarla yolu**' nu seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **satıcılarla yolu**' nu seçin.

    ![Uygulamalar listesindeki satıcılarla yol bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-dealpath-test-user"></a>Satıcılarla yol test kullanıcısı oluştur

Bu bölümde, satıcılarla yolunda Britta Simon adlı bir Kullanıcı oluşturacaksınız. Satıcılarla yol platformunda kullanıcıları eklemek için, [satıcılarla Path istemci destek ekibi](mailto:kenter@dealpath.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde satıcılarla yolu kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız satıcılarla yolunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)