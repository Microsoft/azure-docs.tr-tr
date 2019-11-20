---
title: 'Öğretici: Mimecast kişisel portalı ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Mimecast kişisel portalı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259635613855e4d7687cf569c94bbd3dd04027fe
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160617"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Öğretici: Mimecast kişisel portalı ile Azure Active Directory tümleştirme

Bu öğreticide, Mimecast kişisel portalını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Mimecast kişisel portalını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Mimecast kişisel portalına erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Mimecast kişisel portalı (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Mimecast kişisel portalı ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Mimecast kişisel portalı çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Mimecast kişisel portalı **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Galeriden Mimecast kişisel portalı ekleme

Mimecast kişisel portalının tümleştirmesini Azure AD 'ye yapılandırmak için, Galeriden Mimecast kişisel portalı 'nı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Mimecast kişisel portalı eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Mimecast Personal Portal**yazın, sonuç panelinden **Mimecast kişisel portalı** ' nı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuç listesinde Mimecast kişisel portalı](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon**adlı bir test kullanıcısına bağlı olarak Mimecast kişisel portalı Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Mimecast kişisel portalındaki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Mimecast kişisel portalı ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Mimecast kişisel portalı çoklu oturum açma yapılandırma](#configure-mimecast-personal-portal-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Mimecast kişisel **[portalı test kullanıcısı oluşturun](#create-mimecast-personal-portal-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Mimecast kişisel portalında Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Mimecast kişisel portalı ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Mimecast kişisel portalı** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Mimecast kişisel Portal etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusuna bir URL yazın: 

    | Bölge  |  Değer | 
    | --------------- | --------------- | 
    | Avrupa          | `https://eu-api.mimecast.com/login/saml`|
    | Birleşik Devletler   | `https://us-api.mimecast.com/login/saml`|
    | Güney Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Avustralya       | `https://au-api.mimecast.com/login/saml`|
    | Yurtdışında        | `https://jer-api.mimecast.com/login/saml`|

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    | Bölge  |  Değer | 
    | --------------- | --------------- |
    | Avrupa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Birleşik Devletler   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Güney Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Avustralya       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Yurtdışında        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. **Yanıt URL** 'si metin kutusuna bir URL yazın: 

    | Bölge  |  Değer | 
    | --------------- | --------------- | 
    | Avrupa          | `https://eu-api.mimecast.com/login/saml`|
    | Birleşik Devletler   | `https://us-api.mimecast.com/login/saml`|
    | Güney Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Avustralya       | `https://au-api.mimecast.com/login/saml`|
    | Yurtdışında        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Tanımlayıcı değeri gerçek değil. Değeri gerçek tanımlayıcıyla güncelleştirin. Değeri almak için [Mimecast kişisel portalı istemci desteği ekibine](https://www.mimecast.com/customer-success/technical-support/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Mimecast kişisel portalını ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Mimecast kişisel portalı çoklu oturum açmayı Yapılandır

1. Farklı bir Web tarayıcısı penceresinde, Mimecast kişisel portalınızdaki bir yönetici olarak oturum açın.

2. **Hizmetler \> uygulamalar**' a gidin.
   
    ![Uygulamalar](./media/mimecast-personal-portal-tutorial/ic794998.png "Uygulamalar")

3. **Kimlik doğrulama profilleri**' ne tıklayın.
   
    ![Kimlik doğrulama profilleri](./media/mimecast-personal-portal-tutorial/ic794999.png "Kimlik doğrulama profilleri")

4. **Yeni kimlik doğrulama profili**' ne tıklayın.
   
    ![Yeni kimlik doğrulama profili](./media/mimecast-personal-portal-tutorial/ic795000.png "Yeni kimlik doğrulama profili")

5. **Kimlik doğrulama profili** bölümünde aşağıdaki adımları uygulayın:
   
    ![Kimlik doğrulama profili](./media/mimecast-personal-portal-tutorial/ic795001.png "Kimlik doğrulama profili")
   
    a. **Açıklama** metin kutusuna yapılandırmanız için bir ad yazın.
   
    b. **Mimecast kişisel portalı IÇIN SAML kimlik doğrulamasını zorla**' yı seçin.
   
    c. **Sağlayıcı**olarak **Azure Active Directory**' yi seçin.
   
    d. **Veren URL** metin kutusuna, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının**değerini yapıştırın.
   
    e. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si**değerini yapıştırın.
   
    f. **Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si**değerini yapıştırın.

    g. Azure portal 'den indirilen Not defteri 'nde **Base-64** kodlu sertifikanızı açın, bu içeriği panonuza kopyalayın ve ardından **kimlik sağlayıcısı sertifikası (meta veriler)** metin kutusuna yapıştırın.

    h. **Çoklu oturum açmaya Izin ver '** i seçin.
   
    i. **Kaydet** düğmesine tıklayın.

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

Bu bölümde, Mimecast kişisel portalına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra **Mimecast kişisel portalı**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Mimecast kişisel portalı**yazın ve seçin.

    ![Uygulamalar listesindeki Mimecast kişisel portalı bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-mimecast-personal-portal-test-user"></a>Mimecast kişisel portalı test kullanıcısı oluştur

Azure AD kullanıcılarının Mimecast kişisel portalında oturum açmasını sağlamak için, bu kullanıcıların Mimecast kişisel portalı 'nda sağlanması gerekir. Mimecast kişisel portalı durumunda, sağlama el ile gerçekleştirilen bir görevdir.

Kullanıcı oluşturabilmeniz için önce bir etki alanı kaydetmeniz gerekir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. **Mimecast kişisel portalında** yönetici olarak oturum açın.

2. **\> Iç dizinlere**gidin.
   
    ![Dizinler](./media/mimecast-personal-portal-tutorial/ic795003.png "Dizinler")

3. **Yeni etki alanını Kaydet**' e tıklayın.
   
    ![Yeni etki alanını Kaydet](./media/mimecast-personal-portal-tutorial/ic795004.png "Yeni etki alanını Kaydet")

4. Yeni etki alanınız oluşturulduktan sonra **Yeni adres**' e tıklayın.
   
    ![Yeni Adres](./media/mimecast-personal-portal-tutorial/ic795005.png "Yeni Adres")

5. Yeni adres iletişim kutusunda, sağlamak istediğiniz geçerli bir Azure AD hesabı için aşağıdaki adımları gerçekleştirin:
   
    ![Kaydet](./media/mimecast-personal-portal-tutorial/ic795006.png "Kaydet")
   
    a. **E-posta adresi** metin kutusuna, kullanıcının **e-posta adresini** **brittasıon\@contoso.com**olarak yazın.
    
    b. **Genel ad** metin kutusunda, **Kullanıcı adını** **brittasıon**olarak yazın.

    c. **Parola**' ya ve **Parolayı Onayla** metin kutularına Kullanıcı **parolasını** yazın.
   
    b. **Kaydet** düğmesine tıklayın.

>[!NOTE]
>Azure AD Kullanıcı hesapları sağlamak için, Mimecast kişisel portalı tarafından sunulan diğer bir Mimecast kişisel Portal Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Mimecast kişisel portalı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Mimecast kişisel portalında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

