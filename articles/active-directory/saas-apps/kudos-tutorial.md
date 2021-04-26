---
title: 'Öğretici: Kudos ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Kudos arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 656c7467507c105f492f266f06c05a9f41e8f76a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458872"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Öğretici: Kudos ile tümleştirme Azure Active Directory

Bu öğreticide Kudos 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Kudos 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Kudos erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Kudos (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Kudos ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Kudos çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Kudos, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-kudos-from-the-gallery"></a>Galeriden Kudos ekleme

Kudos 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden Kudos 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Kudos eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Kudos** yazın, sonuç panelinden **Kudos** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde Kudos](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına bağlı olarak Kudos Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Kudos 'daki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı Kudos ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Kudos çoklu oturum açmayı yapılandırma](#configure-kudos-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Kudos test kullanıcısı oluşturma](#create-kudos-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Kudos 'Ta Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Kudos ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Kudos** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Kudos etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company>.kudosnow.com`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [Kudos istemci desteği ekibine](http://success.kudosnow.com/home) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Kudos ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-kudos-single-sign-on"></a>Kudos tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Kudos şirket sitenizde yönetici olarak oturum açın.

1. Üstteki menüde, **Ayarlar simgesi**' ne tıklayın.

    ![Ayarlar](./media/kudos-tutorial/ic787806.png "Ayarlar")

1. **SSO > tümleştirme** ' e tıklayın ve aşağıdaki adımları uygulayın:

    ![SSO](./media/kudos-tutorial/ic787807.png "SSO")

    a. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. Base-64 kodlu sertifikanızı Not defteri 'nde açın, bu içeriği panonuza kopyalayın ve **X. 509.440 sertifikası** metin kutusuna yapıştırın

    c. **URL 'Yi kapatma** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    d. **Kudos URL 'si** metin kutusunda, şirketinizin adını yazın.

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
  
    b. **Kullanıcı adı** alan türü`brittasimon@yourcompanydomain.extension`  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Kudo 'a erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra **Kudos**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Kudos**' ı seçin.

    ![Uygulamalar listesindeki Kudos bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-kudos-test-user"></a>Kudos test kullanıcısı oluşturma

Azure AD kullanıcılarının Kudos 'da oturum açmasını sağlamak için, Kudos 'a sağlanması gerekir. Kudos söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Kudos** şirket sitenizde yönetici olarak oturum açın.

1. Üstteki menüde, **Ayarlar simgesi**' ne tıklayın.

   ![Ayarlar](./media/kudos-tutorial/ic787806.png "Ayarlar")

1. **Kullanıcı Yöneticisi**' ne tıklayın.

1. **Kullanıcılar** sekmesine ve ardından **Kullanıcı Ekle**' ye tıklayın.

   ![Kullanıcı Yöneticisi](./media/kudos-tutorial/ic787809.png "Kullanıcı Yöneticisi")

1. **Kullanıcı Ekle** bölümünde aşağıdaki adımları uygulayın:

    ![Kullanıcı ekleme](./media/kudos-tutorial/ic787810.png "Kullanıcı ekleme")

    a. **Ilk adı**, **Soyadı**, **e-postayı** ve ilgili metin kutularına sağlamak istediğiniz geçerli bir Azure Active Directory hesabının diğer ayrıntılarını yazın.

    b. **Kullanıcı oluştur**' a tıklayın.

> [!NOTE]
> Kudos tarafından sunulan diğer Kudos Kullanıcı hesabı oluşturma araçlarını veya API 'Leri, Azure AD Kullanıcı hesapları sağlamak için kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Kudos kutucuğunu tıklattığınızda, SSO 'yu ayarladığınız Kudos 'lar için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)