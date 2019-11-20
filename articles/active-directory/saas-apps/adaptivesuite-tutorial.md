---
title: 'Öğretici: Uyarlamalı Öngörüler ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Uyarlamalı Öngörüler arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad059e6047dd883c0a5aab0d714d999840050584
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70213623"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Öğretici: Uyarlamalı öngörüleri Azure Active Directory tümleştirin

Bu öğreticide, uyarlamalı öngörüleri Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Uyarlamalı öngörüleri Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Uyarlamalı Öngörüler 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Uyarlamalı Öngörüler 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Uyarlamalı Öngörüler çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Uyarlamalı Öngörüler **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-adaptive-insights-from-the-gallery"></a>Galeriden Uyarlamalı Öngörüler ekleme

Uyarlamalı Öngörüler 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Uyarlamalı Öngörüler eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Uyarlamalı Öngörüler** yazın.
1. Sonuçlar panelinden **Uyarlamalı Öngörüler** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak, uyarlamalı Öngörüler Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, uyarlamalı Öngörüler içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Uyarlamalı Öngörüler ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. **[Uyarlamalı Öngörüler SSO 'Yu yapılandırma](#configure-adaptive-insights-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. Uyarlamalı **[Öngörüler test kullanıcısı oluşturun](#create-adaptive-insights-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Uyarlamalı içgörüler 'de B. Simon 'a karşılık gelen bir.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Uyarlamalı Öngörüler** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Uyarlamalı Öngörüler 'in **SAML SSO ayarları** sayfasından tanımlayıcıyı (varlık kimliği) ve yanıt URL 'si değerlerini elde edebilirsiniz.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Uyarlamalı Öngörüler ayarlama** bölümünde, uygun URL 'leri gereksinime göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Uyarlamalı Öngörüler SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, uyarlamalı Öngörüler Şirket sitenizde yönetici olarak oturum açın.

2. **Yönetim**bölümüne gidin.

    ![Yöneticileri](./media/adaptivesuite-tutorial/ic805644.png "Yöneticileri")

3. **Kullanıcılar ve roller** bölümünde **SAML SSO ayarları**' na tıklayın.

    ![SAML SSO ayarlarını yönetme](./media/adaptivesuite-tutorial/ic805645.png "SAML SSO ayarlarını yönetme")

4. **SAML SSO ayarları** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![SAML SSO ayarları](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO ayarları")

    a. **Kimlik sağlayıcısı adı** metin kutusuna yapılandırmanız için bir ad yazın.

    b. Azure portal ' dan kopyalanmış **Azure AD tanımlayıcı** değerini **KIMLIK sağlayıcısı varlık kimliği** metin kutusuna yapıştırın.

    c. Azure portal ' dan kopyalanmış **oturum açma URL 'si** değerini **kimlik sağlayıcısı SSO URL 'si** metin kutusuna yapıştırın.

    d. Azure portal ' den kopyalanmış **oturum kapatma URL 'si** değerini **özel oturum kapatma URL 'si** metin kutusuna yapıştırın.

    e. İndirilen sertifikanızı karşıya yüklemek için **Dosya Seç**' e tıklayın.

    f. Aşağıdakiler için aşağıdakileri seçin:

     * **SAML Kullanıcı kimliği**, **kullanıcının Uyarlamalı Öngörüler Kullanıcı adını**seçin.

     * **SAML Kullanıcı kimliği konumu**, **ilgilinin NameID içinde Kullanıcı kimliği**' ni seçin.

     * **SAML NameID biçimi**, **e-posta adresi**seçin.

     * **SAML 'Yi etkinleştirin**, **SAML SSO ve doğrudan Uyarlamalı**içgörüler oturum açma seçeneğini belirleyin.

    g. **Uyarlamalı Öngörüler SSO URL 'sini** kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** ve **yanıt URL 'si** metin kutularına yapıştırın.

    h. **Kaydet** düğmesine tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, uyarlamalı Öngörüler 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Uyarlamalı Öngörüler**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-adaptive-insights-test-user"></a>Uyarlamalı Öngörüler test kullanıcısı oluşturma

Azure AD kullanıcılarının Uyarlamalı Öngörüler 'de oturum açmasını sağlamak için bunların Uyarlamalı Öngörüler 'e sağlanması gerekir. Uyarlamalı Öngörüler söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. **Uyarlamalı Öngörüler** şirket sitenizde yönetici olarak oturum açın.

2. **Yönetim**bölümüne gidin.

   ![Yöneticileri](./media/adaptivesuite-tutorial/IC805644.png "Yöneticileri")

3. **Kullanıcılar ve roller** bölümünde **Kullanıcılar**' a tıklayın.

   ![Kullanıcı Ekle](./media/adaptivesuite-tutorial/IC805648.png "Kullanıcı Ekleme")

4. **Yeni Kullanıcı** bölümünde aşağıdaki adımları gerçekleştirin:

   ![Gönder](./media/adaptivesuite-tutorial/IC805649.png "Gönder")

   a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure Active Directory kullanıcısına ilişkin **adı**, **Kullanıcı**adını, **e-postayı**ve **parolayı** yazın.

   b. Bir **rol**seçin.

   c. **Gönder**' e tıklayın.

> [!NOTE]
> AAD Kullanıcı hesaplarını sağlamak için, uyarlamalı içgörüler tarafından sunulan diğer Uyarlamalı içgörüler Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Uyarlamalı Öngörüler kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Uyarlamalı öngörülere otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

