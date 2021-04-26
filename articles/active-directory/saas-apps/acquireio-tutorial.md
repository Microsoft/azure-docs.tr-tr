---
title: 'Öğretici Azure Active Directory: AcquireIO ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve AcquireIO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 5fe070bc1abe0592b3082c597c1812781335448a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673197"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Öğretici: AcquireIO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, AcquireIO 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. AcquireIO 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de AcquireIO erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak Acquireıo oturumu açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* AcquireIO çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* AcquireIO **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-acquireio-from-the-gallery"></a>Galeriden Acquireıo ekleme

Acquireıo 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden Acquireıo 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **acquireıo** yazın.
1. Sonuçlar panelinden **Acquireio** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>AcquireIO için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak AcquireIO Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve AcquireIO içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu AcquireIO ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[AcquireIO SSO 'Yu yapılandırma](#configure-acquireio-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * **[Acquireio test kullanıcısı oluşturun](#create-acquireio-test-user)** ; bu, kullanıcının Azure AD gösterimine bağlı olan acquireio 'de B. Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **acquireio** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Değer gerçek değil. Daha sonra öğreticinin **AcquireIO yapılandırma** bölümünde açıklanan gerçek yanıt URL 'sini alacaksınız. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **AcquireIO ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Acquireıo erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Acquireıo**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-acquireio-sso"></a>AcquireIO SSO 'yu yapılandırma

1. AcquireIO içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Uzantıyı tarayıcıya ekledikten sonra, acquireio ayarla ' ya tıklayarak Acquireıo uygulamasına yönlendiren bir **giriş** yapın. Buradan, Acquireıo 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. AcquireIO 'yi el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, Acquireıo ' da yönetici olarak oturum açın.

1. Menünün sol tarafında **Uygulama Mağazası**' na tıklayın.

    ![Uygulama mağazasını vurgulayan ekran görüntüsü.](./media/acquireio-tutorial/config01.png)

1. **Active Directory** için aşağı kaydırın ve **yüklemeye** tıklayın.

    ![Active Directory bölümünü ve Install düğmesini vurgulayan ekran görüntüsü.](./media/acquireio-tutorial/config02.png)

1. Active Directory açılır penceresinde aşağıdaki adımları uygulayın:

    ![Active Directory ekranını gösteren screnshot.](./media/acquireio-tutorial/config03.png)

    a. Örneğiniz için yanıt URL 'sini kopyalayıp Azure portal **temel SAML yapılandırması** bölümünde **yanıt URL** metin kutusuna yapıştırmak için **Kopyala** ' ya tıklayın.

    b. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Base64 Ile kodlanmış sertifikayı not defteri 'nde açın, içeriğini kopyalayın ve **X. 509.440 sertifikası** metin kutusuna yapıştırın.

    d. **Şimdi Bağlan**' a tıklayın.

### <a name="create-acquireio-test-user"></a>Acquireıo test kullanıcısı oluşturma

Azure AD kullanıcılarının Acquireıo 'da oturum açmasını sağlamak için, Acquireıo ' da sağlanması gerekir. AcquireIO içinde, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Farklı bir Web tarayıcısı penceresinde, Acquireıo ' da yönetici olarak oturum açın.

1. Menünün sol tarafında **profiller** ' e tıklayın ve **profil ekle**' ye gidin.

    ![Profil ekle seçeneğinin yanı sıra ekranın sol tarafındaki menüdeki profillerin vurgualdığı ekran görüntüsü.](./media/acquireio-tutorial/config04.png)

1. **Müşteri Ekle** açılır penceresinde aşağıdaki adımları uygulayın:

    ![Acquireıo yapılandırması](./media/acquireio-tutorial/config05.png)

    a. **Ad** metin kutusuna **B. Simon** gibi kullanıcının adını girin.

    b. **E-posta** metin kutusuna kullanıcının e-postasını girin **B.simon@contoso.com** .

    c. **Gönder**' e tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde AcquireIO kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız AcquireIO ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile AcquireIO 'yi deneyin](https://aad.portal.azure.com/)