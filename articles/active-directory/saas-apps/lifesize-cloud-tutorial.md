---
title: 'Öğretici: Lifesize bulutu ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Lifesize bulutu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e886a1fa3f590ac94dbf088520e6770690ee21ac
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159563"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Öğretici: Lifesize bulutu ile Azure Active Directory tümleştirme

Bu öğreticide, Lifesize bulutunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Lifesize bulutunu Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Lifesize bulutuna erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla buluta (çoklu oturum açma) izin vermek için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini, Lifesize bulutu ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Etkin çoklu oturum açma aboneliğinden sonra buluta esize

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Lifesize bulutu, **SP** tarafından başlatılan SSO 'yu destekler

* Lifesize bulutu **Otomatik** Kullanıcı sağlamayı destekliyor

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Galeriden Lifesize bulutu ekleme

Fiesize bulutu 'nın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Fiesize bulutu eklemeniz gerekir.

**Galeriden Lifesize bulutu eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **Lifesize bulutu**yazın, sonuç panelinden **ömrü bulut** ' ü seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde bulutu yeniden esize](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına bağlı olarak, SIG bulutu ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin, bu bulutta ilgili bir ilişki kurulması gerekir.

Azure AD çoklu oturum açma 'yı ömrü bulutla birlikte yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[Lifesize bulutu çoklu oturum açmayı yapılandırın](#configure-lifesize-cloud-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcı Azure AD gösterimine bağlı olan, Lifesize bulutu 'nda Britta Simon 'ın bir karşılığı olacak şekilde, **[Lifesize bulut test kullanıcısı oluşturun](#create-lifesize-cloud-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, ' Lifesize bulutu ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **ömrü bulut** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Bulut etki alanı ve URL 'Ler çoklu oturum açma bilgilerini lifler](common/sp-identifier-relay.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://login.lifesizecloud.com/ls/?acs`

    b. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://login.lifesizecloud.com/<companyname>`

    c. **Ek URL 'ler ayarla**' ya tıklayın.

    d. **Geçiş durumu** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI, tanımlayıcı ve geçiş durumuyla güncelleştirin. Oturum açma URL 'SI ve tanımlayıcı değerleri almak için [bulut istemci desteği ekibine](https://www.lifesize.com/en/support) başvurun ve öğreticide daha sonra açıklanan SSO yapılandırmasından geçiş durumu değeri alabilirsiniz. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. Kullanım **ömrü bulutu ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-lifesize-cloud-single-sign-on"></a>Lifesize bulutu çoklu oturum açmayı yapılandırma

1. Uygulamanız için yapılandırılmış SSO 'yu almak için, yönetici ayrıcalıklarıyla, Lifesize bulut uygulamasında oturum açın.

2. Sağ üst köşedeki ad ' a tıklayın ve ardından **Gelişmiş ayarlar**' a tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Gelişmiş ayarlar ' da artık **SSO yapılandırma** bağlantısına tıklayın. Bu işlem, örneğiniz için SSO yapılandırma sayfasını açar.

    ![Çoklu oturum açmayı yapılandırma](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Şimdi SSO yapılandırma Kullanıcı arabiriminde aşağıdaki değerleri yapılandırın.

    ![Çoklu oturum açmayı yapılandırma](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. **Kimlik sağlayıcısı veren** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın.

    b.  **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Azure portal 'den indirilen Not defteri 'nde Base-64 kodlu sertifikanızı açın, içeriğini panonuza kopyalayın ve **X. 509.440 sertifika** metin kutusuna yapıştırın.
  
    d. Ad metin kutusuna ait SAML öznitelik eşlemelerinde değeri `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` olarak girin.

    e. **Son ad** metin kutusuna Ilişkin SAML öznitelik eşlemesinde değeri `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` olarak girin.

    f. **E-posta** metin kutusu Için SAML öznitelik eşlemesinde değeri `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` olarak girin.

5. Yapılandırmayı denetlemek için, **Test** düğmesine tıklayabilirsiniz.

    >[!NOTE]
    >Başarılı bir test için Azure AD 'de Yapılandırma Sihirbazı 'nı doldurmanız ve ayrıca testi gerçekleştirebilen kullanıcılara veya gruplara erişim sağlamanız gerekir.

6. **SSO 'Yu etkinleştir** DÜĞMESINI işaretleyerek SSO 'yu etkinleştirin.

7. Şimdi, tüm ayarların kaydedilmesi için **Güncelleştir** düğmesine tıklayın. Bu, RelayState değerini oluşturur. Metin kutusunda oluşturulan RelayState değerini kopyalayın, bunu, **bulut etki alanı ve URL 'ler** bölümündeki **geçiş durumu** metin kutusuna yapıştırın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Lifesize bulutuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra da **ömrü bulutu**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Lifesize bulutu**' nı seçin.

    ![Uygulamalar listesinde Lifesize bulut bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-lifesize-cloud-test-user"></a>Lifesize bulut testi Kullanıcı Oluştur

Bu bölümde, Lifesize bulutu 'nda Britta Simon adlı bir Kullanıcı oluşturacaksınız. Lifesize bulutu otomatik Kullanıcı sağlamayı destekler. Azure AD 'de başarılı kimlik doğrulamasından sonra, Kullanıcı uygulamada otomatik olarak temin edilir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde, Lifesize bulut kutucuğuna tıkladığınızda, o bulut uygulamasının oturum açma sayfasını almalısınız. Burada, Kullanıcı adınızı girmeniz ve sonra uygulama giriş sayfasına yönlendirilmeniz gerekir.

Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
