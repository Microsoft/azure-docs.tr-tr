---
title: 'Öğretici: Genesys tarafından Purecg ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Genesys tarafından Azure Active Directory ve Purecg arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 779328f4c21afb4392663e6f8840749ea505c529
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242430"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Öğretici: Genesys tarafından Purecg ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Genesys tarafından Purecre 'yi tümleştirmeyi öğreneceksiniz. Bunu yaptıktan sonra şunları yapabilirsiniz:

* Genesys tarafından Purecgen 'a erişebilecek kullanıcıları denetlemek için Azure AD 'yi kullanın.
* Kullanıcılarınızın Azure AD hesaplarıyla Genesys tarafından otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir [hesabınız yoksa ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Genesys çoklu oturum açma (SSO) özellikli aboneliğe göre Purecg.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Genesys tarafından Purecm **, SP ve ıDP**– başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın KIMLIĞI sabit dize değeri olduğundan, tek bir kiracıda yalnızca bir örnek yapılandırılabilir.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Galeriden Genesys 'e göre Purecg ekleme

Genesys tarafından Azure AD 'ye göre purecı tümleştirmesini yapılandırmak için, Genesys tarafından yönetilen SaaS uygulamaları listenize, Genesys 'e göre pureci 'yi eklemeniz gerekir. Bunu yapmak için şu adımları uygulayın:

1. Bir iş veya okul hesabı kullanarak veya kişisel bir Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Genesys öğesine göre purecyüksek** yazın.
1. Sonuçlar panelinden **Genesys tarafından Purecı** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Genesys 'e göre Porecg için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Genesys tarafından Porecm Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Genesys ile ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Genesys tarafından Purecı ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Genesys SSO 'su tarafından Purecg 'ı yapılandırın](#configure-purecloud-by-genesys-sso)** .
    1. **[Genesys test kullanıcısına](#create-purecloud-by-genesys-test-user)** , kullanıcının Azure AD gösterimine bağlı olan Genesys 'e göre purecm 'ye kadar bir Pureck oluşturun.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), **Genesys** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP**tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** kutusuna bölgenize karşılık gelen bir URL girin:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. **Yanıt URL 'si** kutusuna bölgenize karşılık gelen bir URL girin:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' yı seçin ve aşağıdaki adımı uygulayın:

    **Oturum açma URL 'si** kutusuna bölgenize karşılık gelen bir URL girin:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. Genesys uygulamasına göre Purecyüksek, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir:

    ![image](common/default-attributes.png)

1. Ayrıca, Genesys uygulamasına göre Purecı, aşağıdaki tabloda gösterildiği gibi SAML yanıtına birkaç özniteliğin geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gerektiğinde bunları gözden geçirebilirsiniz.

    | Adı | Kaynak özniteliği|
    | ---------------| --------------- |
    | E-posta | User. userprinicipalname |
    | © | `Your organization name` |

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Genesys tarafından Pureck ayarla** bölümünde, gereksinimlerinize göre uygun URL 'yi (veya URL 'leri) kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız:

1. Azure portal sol bölmesinde **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, Kullanıcı adını şu biçimde girin: username@companydomain.extension. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri unutmayın.
   1. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Genesys tarafından Purecg 'ye erişim izni vererek Azure çoklu oturum açma 'yı kullanmak için B. Simon 'u ayarlayacaksınız.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Genesys tarafından Purecı**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın altındaki **Seç** düğmesini seçin.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesini seçin.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

## <a name="configure-purecloud-by-genesys-sso"></a>Genesys SSO 'SU tarafından Purecre 'yi yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Genesys tarafından yönetici olarak Purecg 'de oturum açın.

1. Üstte **yönetici** ' yi seçin ve ardından **tümleştirmeler**altında **Çoklu oturum açma '** ya gidin.

    ![Çoklu oturum açmayı yapılandırma](./media/purecloud-by-genesys-tutorial/configure01.png)

1. **ADFS/Azure AD (Premium)** sekmesine geçin ve aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı yapılandırma](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Azure portal indirdiğiniz Base-64 kodlu sertifikayı **ADFS sertifikasına**yüklemek için **Araştır** ' ı seçin.

    b. **ADFS ıSSUER URI** kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    c. **Hedef URI** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. **Bağlı olan taraf tanımlayıcı** değeri için Azure Portal gidin ve **Genesys tarafından poreck** uygulama tümleştirmesi sayfasında, **ÖZELLIKLER** sekmesini seçin ve **uygulama kimliği** değerini kopyalayın. **Bağlı olan taraf tanımlayıcısı** kutusuna yapıştırın.

    ![Çoklu oturum açmayı yapılandırma](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. **Kaydet**’i seçin.

### <a name="create-purecloud-by-genesys-test-user"></a>Genesys test kullanıcısına göre Purecsesli oluştur

Azure AD kullanıcılarının Genesys tarafından purecg 'de oturum açmasını sağlamak için, Genesys tarafından purecg 'ye sağlanması gerekir. Genesys tarafından Pureci tarafından sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için şu adımları izleyin:**

1. Genesys tarafından yönetici olarak Pureci 'de oturum açın.

1. Üstte **yönetici** ' yi seçin ve **kişiler & izinler**' in altındaki **kişilere** gidin.

    ![Çoklu oturum açmayı yapılandırma](./media/purecloud-by-genesys-tutorial/configure03.png)

1. **Kişiler** sayfasında **kişi ekle**' yi seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/purecloud-by-genesys-tutorial/configure04.png)

1. **Kuruluşa kişi ekle** iletişim kutusunda aşağıdaki adımları izleyin:

    ![Çoklu oturum açmayı yapılandırma](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. **Tam ad** kutusuna bir kullanıcının adını girin. Örneğin: **B. Simon**.

    b. **E-posta** kutusuna kullanıcının e-postasını girin. Örneğin: **b. Simon\@contoso.com**.

    c. **Oluştur**'u seçin.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde **Genesys** kutucuğunu seçtiğinizde, için SSO 'Yu ayarladığınız Genesys hesabına göre otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure AD ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure AD 'de uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure AD 'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Genesys tarafından Purecre 'yi deneyin](https://aad.portal.azure.com/)