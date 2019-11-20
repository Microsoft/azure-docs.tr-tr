---
title: 'Öğretici: yeni relik ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve yeni relik arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: fd75cc392bb30d15b665190eef8fbea39d692ee3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160329"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Öğretici: yeni relik ile Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile yeni bir relik tümleştirmeyi nasıl tümleştirileceğini öğreneceksiniz.
Yeni relik 'i Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de yeni relik erişimine erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla yeni bir relik (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini yeni relik ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Yeni relik çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Yeni relik, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-new-relic-from-the-gallery"></a>Galeriden yeni relik ekleme

Yeni relik 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize yeni bir relik eklemeniz gerekir.

**Galeriden yeni relik eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Yeni relik**yazın, sonuç panelinden **Yeni relik** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde yeni relik](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre yeni relik ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin yeni relik olarak oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı yeni relik ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Yeni relik çoklu oturum açmayı yapılandırma](#configure-new-relic-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Yeni relik test kullanıcısı oluşturun](#create-new-relic-test-user)** -kullanıcının Azure AD gösterimine bağlı yeni bir relik halinde Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı yeni relik ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Yeni relik** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Yeni relik etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login`-kendi yeni relik hesap kimliğinizi değiştirdiğinizden emin olun.

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir URL yazın: `rpm.newrelic.com`

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Yeni relik ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-new-relic-single-sign-on"></a>Yeni relik çoklu oturum açmayı Yapılandır

1. Farklı bir Web tarayıcısı penceresinde, **Yeni relik** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **Hesap ayarları**' na tıklayın.
   
    ![Hesap ayarları](./media/new-relic-tutorial/ic797036.png "Hesap ayarları")

3. **Güvenlik ve kimlik doğrulama** sekmesine tıklayın ve ardından **Çoklu oturum açma** sekmesine tıklayın.
   
    ![Çoklu oturum açma](./media/new-relic-tutorial/ic797037.png "Çoklu oturum açma")

4. SAML iletişim sayfasında, aşağıdaki adımları uygulayın:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. İndirilen Azure Active Directory sertifikanızı karşıya yüklemek için **Dosya Seç** ' e tıklayın.

    b. **Uzaktan oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si**değerini yapıştırın.
   
    c. **Oturum kapatma giriş URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si**değerini yapıştırın.

    d. **Değişikliklerimi kaydet**' e tıklayın.

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

Bu bölümde, yeni relik 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Yeni relik**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Yeni relik**' i seçin.

    ![Uygulamalar listesindeki yeni relik bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-new-relic-test-user"></a>Yeni relik test kullanıcısı oluştur

Azure Active Directory kullanıcıların yeni bir rehirde oturum açmasını sağlamak için bunların yeni relik 'e sağlanması gerekir. Yeni relik durumunda sağlama, el ile gerçekleştirilen bir görevdir.

**Yeni relik 'e bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Yeni relik** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **Hesap ayarları**' na tıklayın.
   
    ![Hesap ayarları](./media/new-relic-tutorial/ic797040.png "Hesap ayarları")

3. Sol taraftaki **Hesap** bölmesinde, **Özet**' e ve ardından **Kullanıcı Ekle**' ye tıklayın.
   
    ![Hesap ayarları](./media/new-relic-tutorial/ic797041.png "Hesap ayarları")

4. **Etkin kullanıcılar** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Etkin kullanıcılar](./media/new-relic-tutorial/ic797042.png "Etkin Kullanıcılar")
   
    a. **E-posta** metin kutusuna, sağlamak istediğiniz geçerli bir Azure Active Directory kullanıcısının e-posta adresini yazın.

    b. **Rol** olarak **Kullanıcı**' yı seçin.

    c. **Bu kullanıcıyı Ekle**' ye tıklayın.

>[!NOTE]
>AAD Kullanıcı hesapları sağlamak için yeni relik tarafından sunulan diğer yeni kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.
> 

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde yeni yeniden ayarla kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız yeni relik için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

