---
title: 'Öğretici: Capriza platformu ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Capriza platformu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 29c21fa422d7b5f4b5cca449c9c8b4833f867856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456526"
---
# <a name="tutorial-azure-active-directory-integration-with-capriza-platform"></a>Öğretici: Capriza platformu ile tümleştirme Azure Active Directory

Bu öğreticide, Capriza platformunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Capriza platformunu Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Capriza platformuna erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesapları ile Capriza platformu (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Capriza platformu ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Capriza platformu çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Capriza platformu **SP** tarafından başlatılan SSO 'yu destekler
* Capriza platformu **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-capriza-platform-from-the-gallery"></a>Galeriden Capriza platformu ekleme

Capriza platformunun Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Capriza platformu eklemeniz gerekir.

**Galeriden Capriza platformu eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Capriza platformu** yazın, sonuç panelinden **Capriza platformu** ' nu seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Capriza platformu](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına bağlı olarak Capriza platformu Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Capriza platformunda ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı Capriza platformu ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Capriza platformu çoklu oturum açmayı yapılandırma](#configure-capriza-platform-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Capriza Platform test kullanıcısı oluşturun](#create-capriza-platform-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Capriza platformunda Britta Simon 'un bir karşılığı olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Capriza platformu ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Capriza Platform** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Capriza Platform etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.capriza.com/<tenantid>`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [Capriza Platform istemci destek ekibine](mailto:support@capriza.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Capriza platformunu ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-capriza-platform-single-sign-on"></a>Capriza platformu tek Sign-On yapılandırma

**Capriza Platform** tarafında çoklu oturum açmayı yapılandırmak Için indirilen **sertifikayı (Base64)** ve Azure Portal ' den uygun kopyalanmış URL 'Leri [Capriza Platform destek ekibine](mailto:support@capriza.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

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

Bu bölümde, Capriza platformuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Capriza platformu**' nu seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Capriza platformu**' nu seçin.

    ![Uygulamalar listesindeki Capriza Platform bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-capriza-platform-test-user"></a>Capriza Platform test kullanıcısı oluşturma

Bu bölümün amacı, Capriza 'da Britta Simon adlı bir Kullanıcı oluşturmaktır. Capriza, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. **Lütfen etki alanı adınızın Kullanıcı sağlaması için Capriza ile yapılandırıldığından emin olun. Bundan sonra yalnızca tam zamanında Kullanıcı sağlama çalışacaktır.**

Bu bölümde sizin için herhangi bir eylem öğesi yok. Henüz yoksa Capriza 'ya erişme girişimi sırasında yeni bir Kullanıcı oluşturulacaktır.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Capriza Platform kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Capriza platformunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)