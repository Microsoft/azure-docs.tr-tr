---
title: 'Öğretici: bağlantı noktası \ bağlantı ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve, Ntakct arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "73570533"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile yerleşik olarak tümleştirme

Bu öğreticide, u 'nin Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. U 'nin Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, o Taktacct erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla şirket içinde yerleşik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Ön koşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* , Tek oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Ise Intacct, **IDP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-sage-intacct-from-the-gallery"></a>Galeriden NtAcct ekleme

Nıntacct 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **bağla Intacct** yazın.
1. Sonuçlar panelinden, **It** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>U NtAcct için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak, Azure AD SSO 'Yu, It Intacct ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile bağlantılı Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, It Intacct ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, o **[Intacct SSO 'Yu yapılandırın](#configure-sage-intacct-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan, nıntacct 'da B. Simon 'a sahip olmak için, o şirket içi **[test kullanıcısı oluşturun](#create-sage-intacct-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **yerleşik uygulama tümleştirmesi** sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Yanıt URL** 'si metin kutusuna bir URL yazın: `https://www.intacct.com/ia/acct/sso_response.phtml`

1. E Intacct uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![image](common/edit-attribute.png)

1. Yukarıdaki ' a ek olarak, e Intacct uygulaması SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Ad  |  Kaynak özniteliği|
    | ---------------| --------------- |
    | Şirket Adı | **Şirket Içi şirket KIMLIĞI** |
    | ad | Değer, öğreticide daha sonra **açıklanacak olan IT Intacct test kullanıcısına**girdiğiniz **nıntacct kullanıcı kimliğiyle**aynı olmalıdır |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | Değer, öğreticide daha sonra **açıklanacak olan yerleşik bir test kullanıcısı Oluştur bölümünde**girdiğiniz, yerleşik BIR **Federasyon SSO kullanıcı kimliğiyle**aynı olmalıdır |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    g. **Kaydet** düğmesine tıklayın.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. J şirket içinde **Ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, B. Simon 'u, e-postayla erişim izni vererek Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **zu Intacct**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sage-intacct-sso"></a>Zu Intacct SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, şirket içi şirket sitenizde yönetici olarak oturum açın.

1. **Şirket** sekmesine tıklayın ve ardından **Şirket bilgileri**' ne tıklayın.

    ![Şirket](./media/intacct-tutorial/ic790037.png "şirketi")

1. **Güvenlik** sekmesine tıklayın ve ardından **Düzenle**' ye tıklayın.

    ![Güvenlik](./media/intacct-tutorial/ic790038.png "güvenliği")

1. **Çoklu oturum açma (SSO)** bölümünde aşağıdaki adımları uygulayın:

    ![Çoklu oturum]açma(./media/intacct-tutorial/ic790039.png "Çoklu oturum") açma

    a. **Çoklu oturum açmayı etkinleştir '** i seçin.

    b. **Kimlik sağlayıcısı türü**olarak **SAML 2,0**' i seçin.

    c. **Veren URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının**değerini yapıştırın.

    d. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si**değerini yapıştırın.

    e. **Base-64** kodlu sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve ardından **sertifika** kutusuna yapıştırın.

    f. **Kaydet** düğmesine tıklayın.

### <a name="create-sage-intacct-test-user"></a>It Intacct test kullanıcısı oluştur

Azure AD kullanıcılarını, o şekilde çalışır durumda oturum açabilirler. böylece, bu kişiler, It 'nin içine sağlanması gerekir. O Ntakct için, sağlama el ile gerçekleştirilen bir görevdir.

**Kullanıcı hesaplarını sağlamak için aşağıdaki adımları uygulayın:**

1. E- **Tacct** kiracınızda oturum açın.

1. **Şirket** sekmesine tıklayın ve ardından **Kullanıcılar**' a tıklayın.

    ![Kullanıcı](./media/intacct-tutorial/ic790041.png "kullanıcıları")

1. **Ekle** sekmesine tıklayın.

    ![](./media/intacct-tutorial/ic790042.png "Ekle") Ekle

1. **Kullanıcı bilgileri** bölümünde aşağıdaki adımları uygulayın:

    ![Kullanıcı bilgileri](./media/intacct-tutorial/ic790043.png "Kullanıcı bilgileri")

    a. Kullanıcı **kimliği**, **Soyadı**, **adı, ad**, **e-posta adresi**, **başlık**ve **Kullanıcı bilgileri** bölümüne sağlamak istediğiniz bir Azure AD hesabının **telefonunu** girin.

    > [!NOTE]
    > Yukarıdaki ekran görüntüsünde **Kullanıcı kimliğinin** ve Azure Portal Içindeki **Kullanıcı öznitelikleri** bölümünde **ad** özniteliğiyle eşlenen **kaynak öznitelik** değerinin aynı olması gerekir.

    b. Sağlamak istediğiniz bir Azure AD hesabının **yönetici ayrıcalıklarını** seçin.

    c. **Kaydet** düğmesine tıklayın. 
    
    d. Azure AD hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını doğrulamak için bir bağlantıyı izler.

1. **Çoklu oturum açma** sekmesine tıklayın ve aşağıda yer alan **Federasyon SSO kullanıcı kimliğinin** ve Azure Portal içindeki **Kullanıcı öznitelikleri** bölümündeki `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` eşlenen **kaynak öznitelik** değerinin aynı olması gerekir.

    ![Kullanıcı bilgileri](./media/intacct-tutorial/ic790044.png "Kullanıcı bilgileri")

> [!NOTE]
> Azure AD Kullanıcı hesaplarını sağlamak için, diğer şirket Içi Kullanıcı hesabı oluşturma araçlarını veya,,, m/

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde, bir e-nİç bağlantı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız yerleşik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

