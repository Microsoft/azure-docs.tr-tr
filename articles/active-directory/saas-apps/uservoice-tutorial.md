---
title: 'Öğretici: UserVoice ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile UserVoice arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 39b3104f9d6cbc004383bf2c50d07c4dd345fa3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353615"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Öğretici: UserVoice ile Azure Active Directory tümleştirme

Bu öğreticide UserVoice 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
UserVoice 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* UserVoice 'a erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla UserVoice (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini UserVoice ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* UserVoice çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* UserVoice, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-uservoice-from-the-gallery"></a>Galeriden UserVoice ekleme

UserVoice 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize UserVoice eklemeniz gerekir.

**Galeriden UserVoice eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **UserVoice** yazın, sonuç panelinden **UserVoice** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde UserVoice](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açma 'yı, **Britta Simon** adlı bir test kullanıcısına göre UserVoice ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve UserVoice 'daki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı UserVoice ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[UserVoice çoklu oturum açmayı yapılandırma](#configure-uservoice-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. UserVoice 'ın, kullanıcının Azure AD gösterimine bağlı olan, UserVoice 'ta Britta Simon 'a sahip olması için **[UserVoice test kullanıcısı oluşturun](#create-uservoice-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı UserVoice ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **UserVoice** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![UserVoice etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.UserVoice.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [UserVoice istemci desteği ekibine](https://www.uservoice.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

6. **SAML Imzalama sertifikası** bölümünde, **parmak izini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

7. **UserVoice ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-uservoice-single-sign-on"></a>UserVoice tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde UserVoice şirket sitenizde yönetici olarak oturum açın.

2. Üstteki araç çubuğunda, **Ayarlar**' a tıklayın ve ardından menüden **Web portalı** ' nı seçin.
   
    ![Uygulama tarafındaki ayarlar bölümü](./media/uservoice-tutorial/ic777519.png "Ayarlar")

3. **Web portalı** sekmesindeki **Kullanıcı kimlik doğrulaması** bölümünde **Düzenle** ' ye tıklayarak **Kullanıcı kimlik doğrulamasını Düzenle** iletişim kutusunu açın.
   
    ![Web portalı sekmesi](./media/uservoice-tutorial/ic777520.png "Web portalı")

4. **Kullanıcı kimlik doğrulamasını Düzenle** iletişim sayfasında, aşağıdaki adımları gerçekleştirin:
   
    ![Kullanıcı kimlik doğrulamasını Düzenle](./media/uservoice-tutorial/ic777521.png "Kullanıcı kimlik doğrulamasını Düzenle")
   
    a. **Tek Sign-On (SSO)** seçeneğine tıklayın.
 
    b. Azure portal kopyaladığınız **oturum açma URL 'si** değerini **SSO uzaktan oturum açma** metin kutusuna yapıştırın.

    c. Azure portal kopyaladığınız **Logout URL** değerini, **SSO uzak Sign-Out metin kutusuna** yapıştırın.
 
    d. Azure portal 'tan kopyaladığınız **parmak izi** değerini **geçerli sertifika SHA1 parmak izi** metin kutusuna yapıştırın.
    
    e. **Kimlik doğrulama ayarlarını kaydet**' e tıklayın.

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

Bu bölümde, UserVoice 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **UserVoice**' u seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **UserVoice**' ı seçin.

    ![Uygulamalar listesindeki UserVoice bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-uservoice-test-user"></a>UserVoice test kullanıcısı oluştur

Azure AD kullanıcılarının UserVoice 'da oturum açmasını sağlamak için bunların UserVoice 'a sağlanması gerekir. UserVoice durumunda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:

1. **UserVoice** kiracınızda oturum açın.

2. **Ayarlar**' a gidin.
   
    ![Ayarlar](./media/uservoice-tutorial/ic777811.png "Ayarlar")

3. **Genel**' e tıklayın.

4. **Aracılar ve izinler**' e tıklayın.
   
    ![Aracılar ve izinler](./media/uservoice-tutorial/ic777812.png "Aracılar ve izinler")

5. **Yönetici Ekle**' ye tıklayın.
   
    ![Yönetici Ekle](./media/uservoice-tutorial/ic777813.png "Yönetici Ekle")

6. **Yöneticileri davet et** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Yöneticileri davet et](./media/uservoice-tutorial/ic777814.png "Yöneticileri davet et")
   
    a. E-postalar metin kutusunda, sağlamak istediğiniz hesabın e-posta adresini yazın ve ardından **Ekle**' ye tıklayın.
   
    b. **Davet et**' e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için UserVoice tarafından sunulan diğer UserVoice Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde UserVoice kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız UserVoice 'ta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)