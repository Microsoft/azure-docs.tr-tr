---
title: 'Öğretici: Merkezi Masaüstü ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve merkezi masaüstü arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 36ba61c86082e191831c2c890de4466181f1a4db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674217"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Öğretici: Merkezi Masaüstü ile tümleştirme Azure Active Directory

Bu öğreticide, merkezi masaüstünü Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Merkezi masaüstünü Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Merkezi masaüstüne erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Merkezi Masaüstü (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini merkezi masaüstü ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Merkezi Masaüstü çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Merkezi Masaüstü **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-central-desktop-from-the-gallery"></a>Galeriden Merkezi Masaüstü ekleme

Merkezi Masaüstü 'nün Azure AD ile tümleştirilmesini yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize Merkezi Masaüstü eklemeniz gerekir.

**Galeriden Merkezi Masaüstü eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Merkezi Masaüstü** yazın, sonuç panelinden **Merkezi Masaüstü** ' nü seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuç listesinde merkezi masaüstü](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre merkezi masaüstü ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve merkezi masaüstündeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı merkezi masaüstü ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Merkezi Masaüstü çoklu oturum açmayı yapılandırma](#configure-central-desktop-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Merkezi Masaüstü **[test kullanıcısı oluşturun](#create-central-desktop-test-user)** -merkezi masaüstünde kullanıcının Azure AD gösterimine bağlı olan Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı merkezi masaüstü ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Merkezi Masaüstü** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Merkezi Masaüstü etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.centraldesktop.com`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

    ```http
    https://<companyname>.centraldesktop.com/saml2-metadata.php
    https://<companyname>.imeetcentral.com/saml2-metadata.php
    ```

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Merkezi Masaüstü istemci desteği ekibine](https://imeetcentral.com/contact-us) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (ham)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

6. **Merkezi masaüstünü ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-central-desktop-single-sign-on"></a>Merkezi Masaüstü tek Sign-On yapılandırma

1. **Merkezi Masaüstü** kiracınızda oturum açın.

2. **Ayarlar**' a gidin. **Gelişmiş**' i seçin ve **Çoklu oturum açma**' yı seçin.

    ![Kurulum-Gelişmiş](./media/central-desktop-tutorial/ic769563.png "Kurulum-Gelişmiş")

3. **Çoklu oturum açma ayarları** sayfasında, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açma ayarları](./media/central-desktop-tutorial/ic769564.png "Çoklu oturum açma ayarları")

    a. **SAML V2 çoklu oturum açmayı etkinleştir '** i seçin.

    b. **SSO URL 'si** kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcı** değerini yapıştırın.

    c. **SSO oturum açma URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. **SSO oturumu kapatma URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

4. **Ileti Imzası doğrulama yöntemi** bölümünde aşağıdaki adımları uygulayın:

    ![İleti imzası doğrulama yöntemi](./media/central-desktop-tutorial/ic769565.png "İleti Imzası doğrulama yöntemi")

    a. **Sertifika**’yı seçin.

    b. **SSO sertifikası** LISTESINDE **rsh SHA256**' ı seçin.

    c. İndirilen sertifikanızı Not defteri 'nde açın. Sonra sertifikanın içeriğini kopyalayıp **SSO sertifikası** alanına yapıştırın.

    d. **SAMLv2 oturum açma sayfanız için bir bağlantı görüntüle**' yi seçin.

    e. **Güncelleştir**’i seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.

    b. **Kullanıcı adı** alanına **\@ bricompansıon yourcompanydomain. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, merkezi masaüstüne erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Merkezi Masaüstü**' nü seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Merkezi Masaüstü**' nü seçin.

    ![Uygulamalar listesindeki Merkezi Masaüstü bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-central-desktop-test-user"></a>Merkezi Masaüstü test kullanıcısı oluştur

Azure AD kullanıcılarının oturum açabilmeleri için merkezi masaüstü uygulamasında sağlanması gerekir. Bu bölümde, merkezi masaüstünde Azure AD Kullanıcı hesaplarının nasıl oluşturulacağı açıklanmaktadır.

> [!NOTE]
> Azure AD Kullanıcı hesaplarını sağlamak için, Merkezi Masaüstü tarafından sağlanan diğer tüm merkezi masaüstü Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

**Merkezi masaüstüne Kullanıcı hesapları sağlamak için:**

1. Merkezi Masaüstü kiracınızda oturum açın.

2. **Kişiler** ' i seçin ve ardından **iç üye Ekle**' yi seçin.

    ![People](./media/central-desktop-tutorial/ic781051.png "People")

3. **Yeni Üyeler e-posta adresi** kutusuna, sağlamak Istediğiniz BIR Azure AD hesabı yazın ve ardından **İleri**' yi seçin.

    ![Yeni üyelerin e-posta adresleri](./media/central-desktop-tutorial/ic781052.png "Yeni üyelerin e-posta adresleri")

4. **İç üye Ekle**' yi seçin.

    ![İç üye Ekle](./media/central-desktop-tutorial/ic781053.png "İç üye Ekle")

   > [!NOTE]
   > Eklediğiniz kullanıcılar hesaplarının etkinleştirilmesi için onay bağlantısı içeren bir e-posta alır.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Merkezi Masaüstü kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız merkezi masaüstünde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)