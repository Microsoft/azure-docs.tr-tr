---
title: 'Öğretici: Bonbir ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve primi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: e93d03ac9ec404a18b480a0cd1bfe289944b60d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689122"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Öğretici: primi ile tümleştirme Azure Active Directory

Bu öğreticide, priminin Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Azure AD ile primi tümleştirme aşağıdaki avantajları sağlar:

* Azure AD 'de, primi erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla geçici olarak (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Pride ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Primi çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* **Bonıse, IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-bonusly-from-the-gallery"></a>Galeriden primi ekleme

Primi 'nın Azure AD 'ye tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize primi eklemeniz gerekir.

**Galeriden bir adım eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **bonikisi** yazın, sonuç panelinden **primi** ' yi seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Sonuçlar listesinde](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına bağlı olarak, Bon, ile birlikte yapılandırın ve test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilişkili kullanıcı arasındaki bir bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[primi çoklu oturum açmayı yapılandırın](#configure-bonusly-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimi ile bağlantılı olan Bontta Simon 'ın bir karşılığı olacak şekilde, **[primi test kullanıcısı oluşturun](#create-bonusly-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı primi ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **primi** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Primi etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-reply.png)

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek yanıt URL 'siyle güncelleştirin. Değeri almak için [primi istemci desteği ekibine](https://bonus.ly/contact) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

6. **SAML Imzalama sertifikası** bölümünde, **parmak izini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

7. **Primi ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-bonusly-single-sign-on&quot;></a>Primi tek Sign-On yapılandırma

1. Farklı bir tarayıcı penceresinde, **primi** kiracınızda oturum açın.

1. Üstteki araç çubuğunda, **Ayarlar** ' a tıklayın ve ardından **tümleştirmeler ve uygulamalar**' ı seçin.

    ![Bonalya sosyal bölüm](./media/bonus-tutorial/ic773686.png &quot;Bonusly")
1. **Çoklu oturum açma** altında **SAML**' yi seçin.

1. **SAML** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Bon, SAML Iletişim sayfası](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. **IDP SSO hedef URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **IDP oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **IDP veren** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.
    
    d. Azure portal ' den kopyalanmış **parmak izi** değerini **CERT parmak izi** metin kutusuna yapıştırın.
    
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

Bu bölümde, bonon 'un erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra da **primi**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **primi**' yi seçin.

    ![Uygulamalar listesindeki primi bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-bonusly-test-user&quot;></a>Primi test kullanıcısı oluştur

Azure AD kullanıcılarının primi 'da oturum açmasını sağlamak için, bunların primi sağlaması gerekir. Prime durumunda, sağlama el ile gerçekleştirilen bir görevdir.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, diğer herhangi bir diğer tüm diğer Kullanıcı hesabı oluşturma araçlarını ya da pribir şekilde sunulan API 'Leri kullanabilirsiniz. 

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. Bir Web tarayıcısı penceresinde, primi kiracınızda oturum açın.

1. **Ayarlar**'a tıklayın.

    ![Ayarlar](./media/bonus-tutorial/ic781041.png &quot;Ayarlar")

1. **Kullanıcılar ve bonkullanımlar** sekmesine tıklayın.

    ![Kullanıcılar ve bonkullanımları](./media/bonus-tutorial/ic781042.png "Kullanıcılar ve bonkullanımları")

1. **Kullanıcıları Yönet**' e tıklayın.

    ![Kullanıcıları yönetme](./media/bonus-tutorial/ic781043.png "Kullanıcıları Yönet")

1. **Kullanıcı Ekle**'ye tıklayın.

    ![Ekran görüntüsü, Kullanıcı Ekle ' yi seçebileceğiniz kullanıcıları yönetme ' yi gösterir.](./media/bonus-tutorial/ic781044.png "Kullanıcı Ekleme")

1. **Kullanıcı Ekle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu bilgileri girebileceğiniz Kullanıcı Ekle iletişim kutusunu gösterir.](./media/bonus-tutorial/ic781045.png "Kullanıcı Ekleme")  

    a. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta** gibi girin.

    b. **Soyadı** metin kutusunda, **Simon** gibi kullanıcı adının soyadını girin.

    c. **E-posta** metin kutusuna, gibi kullanıcının e-postasını girin `brittasimon@contoso.com` .

    d. **Kaydet**’e tıklayın.

    > [!NOTE]
    > Azure AD hesap tutucusu, hesabı etkin olmadan önce onaylamaya yönelik bir bağlantı içeren bir e-posta alır.  

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde primi Kutucuğa tıkladığınızda, SSO 'yu ayarladığınız Prikte otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)