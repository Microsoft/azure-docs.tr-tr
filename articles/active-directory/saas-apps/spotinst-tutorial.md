---
title: 'Öğretici: Spotınst ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Spotinst arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 402b5a975b3ded8327edcea81b680e9990bac39e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686600"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Öğretici: Spotınst ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Spotinst 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Spotınst 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Spotınst 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Spotinst ' de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Spotınst çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Spotınst **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-spotinst-from-the-gallery"></a>Galeriden Spotınst ekleme

Spotınst 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Spotınst eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **spotınst** yazın.
1. Sonuçlar panelinden **Spotınst** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Spotinst için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, spotinst Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Spotinst içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Spotinst ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Spotinst SSO 'Yu yapılandırma](#configure-spotinst-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * Kullanıcının Azure AD gösterimine bağlı olan Spotınst 'de B. Simon 'a sahip olmak için **[spotinst test kullanıcısı oluşturun](#create-spotinst-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **spotınst** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, IDP tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki adımları izleyin:

   1. **Yanıt URL 'sinin** şu şekilde ayarlandığından emin olun: https://console.spotinst.com/auth/saml .
   1. **Geçiş durumunda**,, **SSO** sekmesinde de doğrulayabileceği spotinst kuruluş kimliğinizi girin.
   1. **Oturum açma URL 'si** boş olmalıdır.

1. **Kaydet**’e tıklayın.

1. Spotinst uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Spotinst uygulaması, yukarıdakine ek olarak aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | -----| --------------- |
    | E-posta | Kullanıcı. Mail |
    | FirstName | Kullanıcı. |
    | LastName | User. soyadı |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Spoti ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Spotınst 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Spotınst**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-spotinst-sso"></a>Spotınst SSO 'SU yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir güvenlik yöneticisi olarak Spotinst ' de oturum açın.

2. Ekranın sağ üst tarafındaki **Kullanıcı simgesine** tıklayın ve **Ayarlar**' a tıklayın.

    ![Ekran görüntüsü Kullanıcı simgesinden seçilen ayarları gösterir.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Üstteki **güvenlik** sekmesine tıklayın ve ardından **kimlik sağlayıcıları** ' nı seçin ve aşağıdaki adımları gerçekleştirin:

    ![Spotınst güvenliği](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Örneğiniz için **geçiş durumu** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **geçiş durumu** metin kutusuna yapıştırın.

    b. Azure portal indirdiğiniz meta veri xml dosyasını karşıya yüklemek için, **Araştır** ' a tıklayın.

    c. **Kaydet**' e tıklayın.

### <a name="create-spotinst-test-user"></a>Spotınst test kullanıcısı oluşturma

Bu bölümün amacı, Spotınst 'de Britta Simon adlı bir Kullanıcı oluşturmaktır.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırdıysanız, aşağıdaki adımları uygulayın:

   a. Farklı bir Web tarayıcısı penceresinde, bir güvenlik yöneticisi olarak Spotinst ' de oturum açın.

   b. Ekranın sağ üst tarafındaki **Kullanıcı simgesine** tıklayın ve **Ayarlar**' a tıklayın.

    ![Ekran görüntüsü Kullanıcı simgesinden seçilen ayarları gösterir.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. **Kullanıcılar** ' a tıklayın ve **Kullanıcı Ekle**' yi seçin.

    ![Ekran görüntüsü kullanıcılardan seçili Kullanıcı Ekle ' nin gösterir.](./media/spotinst-tutorial/adduser1.png)

    d. Kullanıcı Ekle bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı ekle bölümünü gösterir.](./media/spotinst-tutorial/adduser2.png)

    * **Tam ad** metin kutusuna, gibi kullanıcının tam adını girin `BrittaSimon` .

    * **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini girin `brittasimon@contoso.com` .

    * Kuruluş **rolü, hesap rolü ve hesaplar** için kuruluşa özgü ayrıntıları seçin.

2. Uygulamayı **IDP** tarafından başlatılan modda yapılandırdıysanız, bu bölümde sizin için herhangi bir eylem öğesi yoktur. Spotinst, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Henüz mevcut değilse, Spotinst 'a erişme girişimi sırasında yeni bir Kullanıcı oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Spotınst kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Spotinst öğesinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Spotınst 'yi deneyin](https://aad.portal.azure.com/)