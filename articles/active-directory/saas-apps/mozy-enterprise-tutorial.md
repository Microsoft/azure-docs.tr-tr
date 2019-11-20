---
title: 'Öğretici: Mozy Kurumsal ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Mozy Enterprise arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 0f1aca75045f674fa45f5ad00c14a37c1abe7aea
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160420"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Öğretici: Mozy Enterprise ile Azure Active Directory tümleştirme

Bu öğreticide, Mozy Enterprise 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Mozy Enterprise 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Mozy Enterprise 'a erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Mozy Enterprise (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Mozy Enterprise ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Mozy Enterprise çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Mozy Enterprise, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Galeriden Mozy Enterprise ekleme

Mozy Enterprise 'ın Azure AD ile tümleştirilmesini yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize Mozy Enterprise eklemeniz gerekir.

**Galeriden Mozy Enterprise eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Mozy Enterprise**yazın, sonuç panelinden **Mozy Enterprise** ' u seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuç listesinde Mozy kurumsal](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre Mozy Enterprise ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve Mozy kurumsal içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı Mozy Enterprise ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. , Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Mozy Enterprise çoklu oturum açmayı yapılandırın](#configure-mozy-enterprise-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Mozy kurumsal test kullanıcısı oluşturun](#create-mozy-enterprise-test-user)** -Mozy Enterprise 'Ta kullanıcının Azure AD gösterimine bağlanmış Britta Simon 'un bir karşılığı vardır.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Mozy Enterprise ile birlikte yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Mozy kurumsal** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Mozy kurumsal etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Mozy Kurumsal istemci destek ekibine](https://support.mozy.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Mozy Enterprise 'ı ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-mozy-enterprise-single-sign-on"></a>Mozy Enterprise çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Mozy Kurumsal Şirket sitenizde yönetici olarak oturum açın.

2. **Yapılandırma** bölümünde **kimlik doğrulama ilkesi**' ne tıklayın.
   
    ![Kimlik doğrulama ilkesi](./media/mozy-enterprise-tutorial/ic777314.png "Kimlik doğrulama ilkesi")

3. **Kimlik doğrulama ilkesi** bölümünde aşağıdaki adımları uygulayın:
   
    ![Kimlik doğrulama ilkesi](./media/mozy-enterprise-tutorial/ic777315.png "Kimlik doğrulama ilkesi")
   
    a. **Sağlayıcı**olarak **Dizin hizmeti** seçin.
   
    b. **LDAP Push kullan**' ı seçin.
   
    c. **SAML kimlik doğrulaması** sekmesine tıklayın.
   
    d. Azure portal, **kimlik doğrulama URL 'si** metin kutusuna kopyaladığınız **oturum açma URL 'sini**yapıştırın.
   
    e. Azure portal 'den **SAML uç noktası** metin kutusuna KOPYALADıĞıNıZ **Azure ad tanımlayıcısını**yapıştırın.
   
    f. İndirilen Base-64 kodlu sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve ardından tüm sertifikayı **SAML sertifikası** metin kutusuna yapıştırın.
   
    g. **Yöneticilerin ağ kimlik bilgileriyle oturum açmasını sağlamak IÇIN SSO 'Yu etkinleştir '** i seçin.
   
    h. **Değişiklikleri Kaydet**' e tıklayın.

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

Bu bölümde, Mozy Enterprise 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Mozy Enterprise**' u seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Mozy Enterprise**' ı seçin.

    ![Uygulamalar listesindeki Mozy kurumsal bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-mozy-enterprise-test-user"></a>Mozy kurumsal test kullanıcısı oluştur

Azure AD kullanıcılarının Mozy Enterprise 'ta oturum açmasını sağlamak için, Mozy Enterprise 'a sağlanması gerekir. Mozy Enterprise söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

>[!NOTE]
>AAD Kullanıcı hesapları sağlamak için Mozy Enterprise tarafından sunulan başka bir Mozy kurumsal Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Mozy kurumsal** kiracınızda oturum açın.

2. **Kullanıcılar**' a ve ardından **Yeni Kullanıcı Ekle**' ye tıklayın.
   
    ![Kullanıcılar](./media/mozy-enterprise-tutorial/ic777317.png "Kullanıcılar")
   
    >[!NOTE]
    >**Yeni Kullanıcı Ekle** seçeneği yalnızca, **kimlik doğrulama ilkesi**altında sağlayıcı olarak **Mozy** seçilirse görüntülenir. SAML kimlik doğrulaması yapılandırıldıysa, kullanıcılar çoklu oturum açma yoluyla ilk oturum açma bilgilerini otomatik olarak eklenir.
    
3. Yeni Kullanıcı iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Kullanıcı Ekle](./media/mozy-enterprise-tutorial/ic777318.png "Kullanıcı Ekle")
   
    a. **Grup Seç** listesinden bir grup seçin.
   
    b. **Ne tür Kullanıcı** listesinden bir tür seçin.
   
    c. **Kullanıcı adı** metin kutusuna Azure AD kullanıcısının adını yazın.
   
    d. **E-posta** metin kutusuna Azure AD kullanıcısının e-posta adresini yazın.
   
    e. **Kullanıcı yönergesi e-postası gönder**' i seçin.
   
    f. **Kullanıcı Ekle**' ye tıklayın.

     >[!NOTE]
     > Kullanıcı oluşturulduktan sonra, Azure AD kullanıcısına, hesabı etkin hale gelmeden önce onaylanacak bir bağlantı içeren bir e-posta gönderilir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Mozy Enterprise kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Mozy kuruluşunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

