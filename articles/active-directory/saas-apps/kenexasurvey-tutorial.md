---
title: 'Öğretici: IBM Kenexa anketi Kurumsal ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve IBM Kenexa araştırma kuruluşu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: faea5fdc7fdab11c630f24dbb8527e5bf198765c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459161"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Öğretici: IBM Kenexa araştırma Enterprise ile Azure Active Directory tümleştirme

Bu öğreticide, IBM Kenexa araştırma kurumsal Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
IBM Kenexa araştırma kurumsal 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de IBM Kenexa araştırma kurumsal erişimine sahip olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla IBM Kenexa araştırma kurumsal (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini IBM Kenexa araştırma Kurumsal ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* IBM Kenexa anketi Kurumsal Çoklu oturum açma özellikli abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* IBM Kenexa anketi kurumsal **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Galeriden IBM Kenexa araştırma kurumsal ekleme

IBM Kenexa anketi Enterprise 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden IBM Kenexa araştırma Enterprise 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden IBM Kenexa araştırma kurumsal eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **IBM Kenexa anket kurumsal** yazın, bir sonuç panelinden **IBM Kenexa anket Enterprise** ' ı seçin ve ardından uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![IBM Kenexa anket kurumsal sonuç listesi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına bağlı olarak IBM Kenexa araştırma Kurumsal ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve IBM Kenexa anketi kurumsal içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisi kurulması gerekir.

Azure AD çoklu oturum açma 'yı IBM Kenexa anket Kurumsal ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[IBM Kenexa anketi Kurumsal Çoklu oturum açma yapılandırma](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. IBM **[Kenexa anketi kurumsal test kullanıcısı oluşturun](#create-ibm-kenexa-survey-enterprise-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan IBM Kenexa araştırma kuruluşunda Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı IBM Kenexa araştırma Enterprise ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **IBM Kenexa anketi kurumsal** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![IBM Kenexa anketi kurumsal etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://surveys.kenexa.com/<companycode>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [IBM Kenexa anketi Kurumsal istemci destek ekibine](https://www.ibm.com/support/home/?lnk=fcw) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. IBM Kenexa anketi kurumsal uygulaması, belirli bir biçimde güvenlik onaylama işlemi Işaretleme dili (SAML) onayları almayı bekler, bu da SAML belirteci özniteliklerinizin yapılandırmasına özel öznitelik eşlemeleri eklemenizi gerektirir. Yanıttaki Kullanıcı tanımlayıcısı talebinin değeri, Kenexa sisteminde yapılandırılmış olan SSO KIMLIĞI ile aynı olmalıdır. Kuruluşunuzdaki uygun kullanıcı tanımlayıcısını SSO Internet Datagram Protokolü (ıDP) olarak eşlemek için, [IBM Kenexa anketi kurumsal destek ekibi](https://www.ibm.com/support/home/?lnk=fcw)ile çalışın.

    Varsayılan olarak, Azure AD kullanıcı tanımlayıcısını Kullanıcı asıl adı (UPN) değeri olarak ayarlar. Aşağıdaki ekran görüntüsünde gösterildiği gibi **Kullanıcı öznitelikleri** sekmesinde bu değeri değiştirebilirsiniz. Tümleştirme yalnızca eşlemeyi doğru bir şekilde tamamladıktan sonra işe yarar.

    ![image](common/edit-attribute.png)

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **IBM Kenexa anket kurumsal 'ı ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>IBM Kenexa anketi Kurumsal tek Sign-On yapılandırma

**IBM Kenexa anketi kurumsal** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' den uygun kopyalanmış URL 'Leri [IBM Kenexa anketi kurumsal destek ekibine](https://www.ibm.com/support/home/?lnk=fcw)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

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

Bu bölümde, IBM Kenexa araştırma kurumlarına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra **IBM Kenexa anket kurumsal**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **IBM Kenexa anket Enterprise**' ı seçin.

    ![Uygulamalar listesindeki IBM Kenexa anketi kurumsal bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>IBM Kenexa anketi kurumsal test kullanıcısı oluşturma

Bu bölümde, IBM Kenexa araştırma kuruluşunda Britta Simon adlı bir Kullanıcı oluşturacaksınız.

IBM Kenexa anketi kurumsal sisteminde kullanıcı oluşturmak ve bunların SSO KIMLIĞINI eşlemek için, [IBM Kenexa anketi kurumsal destek ekibi](https://www.ibm.com/support/home/?lnk=fcw)ile çalışabilirsiniz. Bu SSO KIMLIĞI değeri ayrıca Azure AD 'nin Kullanıcı tanımlayıcısı değerine eşlenmelidir. Bu varsayılan ayarı **öznitelik** sekmesinde değiştirebilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde IBM Kenexa anketi kurumsal kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız IBM Kenexa araştırma kuruluşunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)