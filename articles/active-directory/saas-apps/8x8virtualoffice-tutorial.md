---
title: 'Öğretici: 8x8 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve 8x8 arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 81a7efea268600e661981b35f79149fe814ef084
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180685"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Öğretici: 8x8 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile 8x8 ' i tümleştirme hakkında bilgi edineceksiniz. 8x8 ' i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de 8x8 ' e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla 8x8 ' e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* 8x8 aboneliği.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* 8x8 **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-8x8-from-the-gallery"></a>Galeriden 8x8 ekleniyor

8x8 ' in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden 8x8 ' i yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **8x8** yazın.
1. Sonuçlar panelinden **8x8** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>8x8 için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak 8x8 Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve 8x8 ' deki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu 8x8 ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[8x8 SSO yapılandırma](#configure-8x8-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. 8x8 **[test kullanıcısı oluşturun](#create-8x8-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan 8x8 ' de B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **8x8** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusuna bir URL yazın:`https://sso.8x8.com/saml2`

    b. **Yanıt URL** 'si metin kutusuna bir URL yazın:`https://sso.8x8.com/saml2`

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin. Sertifikayı daha sonra öğreticide **yapılandırma 8x8 SSO** bölümünde kullanacaksınız.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **8x8** ' i ayarla bölümünde, URL 'leri kopyalayın ve bu URL değerlerini öğreticide daha sonra kullanacaksınız.

    ![Yapılandırma URL 'Lerini Kopyala](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

Bu bölümde, 8x8 ' e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **8x8**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-8x8-sso"></a>8x8 SSO yapılandırma

Öğreticinin sonraki bölümü, 8x8 ile sahip olduğunuz abonelik türüne bağlıdır.

* Yönetim için Configuration Manager kullanan 8x8 sürümleri ve X serisi müşterileri için, [8X8 Yönetici Konsolu yapılandırma](#configure-8x8-admin-console)konusuna bakın.
* Yönetim için hesap yöneticisi 'ni kullanan sanal Office müşterileri için, [bkz. 8x8 Hesap Yöneticisi 'Ni yapılandırma](#configure-8x8-account-manager).

### <a name="configure-8x8-admin-console"></a>8x8 yönetici konsolunu Yapılandır

1. Bu yapılandırmayı 8x8 ' de otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, sırasıyla, **8x8** ' e tıklayarak, sizi 8x8 uygulamasına yönlendirebilirsiniz. Buradan, 8x8 ' de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. 8x8 ' i el ile ayarlamak istiyorsanız, bir yönetici olarak 8x8 [yönetici konsolunda](https://admin.8x8.com/) oturum açın.

1. Giriş sayfasından **kimlik yönetimi**' ne tıklayın.

    ![Kimlik yönetimi kutucuğunu vurgulayan ekran görüntüsü.](./media/8x8virtualoffice-tutorial/configure1.png)

1. **Çoklu oturum açmayı (SSO)** denetleyip **Microsoft Azure AD** seçin.

    ![Çoklu oturum açma (SSO) ve Microsoft Azure AD seçeneklerini vurgulayan ekran görüntüsü.](./media/8x8virtualoffice-tutorial/configure2.png)

1. Azure AD 'de **SAML Ile tek Sign-On ayarla** sayfasından üç URL 'yi ve imzalama sertifikasını 8X8 yönetici KONSOLUNUN **Microsoft Azure AD SAML ayarları** bölümüne kopyalayın.

    ![8x8 Yönetici Konsolu](./media/8x8virtualoffice-tutorial/configure3.png)

    a. **IDP oturum açma URL**'Sine **oturum açma URL** 'sini kopyalayın.

    b. **Azure ad tanımlayıcısını** **ıDP veren URL/urn**'ye kopyalayın.

    c. **Oturum kapatma URL 'Sini** **ıDP oturum kapatma URL**'sine kopyalayın.

    d. **Sertifikayı indirin (base64)** ve **sertifikayı** karşıya yükleyin.

    e. **Kaydet**’e tıklayın.

### <a name="configure-8x8-account-manager"></a>8x8 hesap yöneticisini Yapılandır

1. 8x8 sanal Office kiracınızda yönetici olarak oturum açın.

1. Uygulama panelinde **sanal Office hesabı MGR** ' i seçin.

    ![Sanal Office hesabı MGR kutucuğunu vurgulayan ekran görüntüsü.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Yönetilecek **iş** hesabı ' nı seçin ve **oturum aç** düğmesine tıklayın.

    ![Iş seçeneğini ve oturum aç düğmesini vurgulayan ekran görüntüsü.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Menü listesinde **hesaplar** sekmesi ' ne tıklayın.

    ![Menü listesindeki hesaplar sekmesini vurgulayan ekran görüntüsü.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Hesap listesinde **Çoklu oturum açma** ' ya tıklayın.

    ![Çoklu oturum açma seçeneğini vurgulayan ekran görüntüsü.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Kimlik doğrulama yöntemlerinde **Çoklu oturum açma** ' yı seçin ve **SAML**' ye tıklayın.

    ![Çoklu oturum açma altındaki SAML 'Yi vurgulayan ekran görüntüsü.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. **SAML çoklu oturum açma** bölümünde aşağıdaki adımları uygulayın:

    ![Uygulama tarafında yapılandırma](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Oturumu Kapat URL** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    c. **Veren URL** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    d. Azure portal 'den indirdiğiniz sertifikayı karşıya yüklemek için, **Araştır** düğmesine tıklayın.

    e. **Kaydet** düğmesine tıklayın.

### <a name="create-8x8-test-user"></a>8x8 test kullanıcısı oluştur

Bu bölümde, 8x8 ' de Britta Simon adlı bir Kullanıcı oluşturacaksınız. 8x8 platformda kullanıcıları eklemek için [8x8 destek ekibi](https://www.8x8.com/about-us/contact-us) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz 8x8 oturum açma URL 'sine yeniden yönlendirilir.  

* 8x8 oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'yu ayarladığınız 8x8 ' de otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft Access panel ' i de kullanabilirsiniz. Erişim panelinde 8x8 kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız 8x8 ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

8x8 ' i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).