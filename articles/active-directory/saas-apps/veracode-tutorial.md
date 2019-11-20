---
title: 'Öğretici: Veracode | ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ile Veracode arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Öğretici: Veracode ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Veracode 'u Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Veracode 'u Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Veracode erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bir Veracode çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Veracode, kimlik sağlayıcısı tarafından başlatılan SSO ve Just-In-Time Kullanıcı sağlamasını destekler.

## <a name="add-veracode-from-the-gallery"></a>Galeriden Veracode ekleyin

Veracode 'un Azure AD ile tümleştirilmesini yapılandırmak için Galeriden yönetilen SaaS uygulamaları listenize Veracode ekleyin.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna "Veracode" yazın.
1. Sonuçlar panelinden **Veracode** ' u seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Veracode için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Veracode ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bu kullanıcı ile ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Azure AD SSO 'yu Veracode ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    * B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    * Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Veracode SSO 'Yu yapılandırın](#configure-veracode-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan Veracode 'da B. Simon 'a karşılık gelen bir **[Veracode test kullanıcısı oluşturun](#create-veracode-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Veracode** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun. **Çoklu oturum açma**seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Kalem simgesi vurgulanmış şekilde SAML ile çoklu oturum açmayı ayarlama ekran görüntüsü](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. **Kaydet**’i seçin.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** . Sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Indirme bağlantısı vurgulanmış şekilde SAML Imzalama sertifikası bölümünün ekran görüntüsü](common/certificatebase64.png)

1. Veracode, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Kullanıcı özniteliklerinin & talepler bölümünün ekran görüntüsü](common/default-attributes.png)

1. Veracode, SAML yanıtına daha fazla özniteliğin geri geçirilmesini de bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre gözden geçirebilirsiniz.

    | Adı | Kaynak özniteliği|
    | ---------------| --------------- |
    | FirstName |Kullanıcı. |
    | Soyadı |User. soyadı |
    | e-posta |Kullanıcı. Mail |

1. **Kurulum Veracode** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Leri vurgulanmış şekilde ayarlama Veracode bölümünün ekran görüntüsü](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Veracode SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak Veracode şirket sitenizde oturum açın.

1. Üstteki menüden **ayarlar** > **yönetici**' yi seçin.
   
    ![Ayarlar simgesi ve yönetici vurgulanmış şekilde Veracode yönetiminin ekran görüntüsü](./media/veracode-tutorial/ic802911.png "Yönetim")

1. **SAML** sekmesini seçin.

1. **Kuruluş SAML ayarları** bölümünde aşağıdaki adımları uygulayın:

    ![Kuruluş SAML ayarları bölümünün ekran görüntüsü](./media/veracode-tutorial/ic802912.png "Yönetim")

    a.  **Veren**için, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    b. **Onaylama Imza sertifikası**için Azure Portal indirilen sertifikanızı karşıya yüklemek Için **Dosya Seç** ' i seçin.

    c. **Kendi kendine kayıt**Için, **kendinden kaydolmayı etkinleştir**' i seçin.

1. **Kendi kendine kayıt ayarları** bölümünde aşağıdaki adımları gerçekleştirin ve ardından **Kaydet**' i seçin:

    ![Çeşitli seçenekler vurgulanmış şekilde kendi kendine kayıt ayarları bölümünün ekran görüntüsü](./media/veracode-tutorial/ic802913.png "Yönetim")

    a. **Yeni Kullanıcı etkinleştirmesi**Için **etkinleştirme gerekli değil**' i seçin.

    b. **Kullanıcı veri güncelleştirmeleri**Için **tercih Veracode Kullanıcı verileri**' ni seçin.

    c. **SAML öznitelik ayrıntıları**için aşağıdakileri seçin:
      * **Kullanıcı rolleri**
      * **İlke Yöneticisi**
      * **Geçirenlerin**
      * **Güvenlik lideri**
      * **İdari**
      * **Gönderenin**
      * **Oluşturucu**
      * **Tüm tarama türleri**
      * **Takım üyelikleri**
      * **Varsayılan takım**

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden >**kullanıcılar** > **tüm kullanıcılar**' ı **Azure Active Directory** seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:

   1. **Ad**için `B.Simon`girin.  
   1. **Kullanıcı adı**için username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster**' i seçin ve ardından gösterilen değeri yazın.
   1. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Veracode 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon ' u etkinleştirin.

1. Azure portal, **tüm uygulamalar** > **Kurumsal uygulamalar** ' ı seçin.
1. Uygulamalar listesinde **Veracode**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   ![Kullanıcılar ve gruplar vurgulanmış şekilde Yönet bölümünün ekran görüntüsü](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin. **Atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcılar ve gruplar sayfasının ekran görüntüsü, Kullanıcı Ekle vurgulanmış olarak](common/add-assign-user.png)

1. Kullanıcılar **ve gruplar** iletişim kutusunda, **Kullanıcılar**' dan **B. Simon**' u seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-veracode-test-user"></a>Veracode test kullanıcısı oluşturma

Veracode 'da oturum açmak için Azure AD kullanıcılarının Veracode 'da sağlanması gerekir. Bu görev otomatikleştirilmiştir ve el ile herhangi bir şey yapmanız gerekmez. İlk çoklu oturum açma denemesi sırasında gerekirse kullanıcılar otomatik olarak oluşturulur.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için Veracode tarafından sunulan diğer herhangi bir diğer Veracode Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde **Veracode** ' u SEÇTIĞINIZDE, SSO 'Yu ayarladığınız Veracode 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Veracode 'u deneyin](https://aad.portal.azure.com/)