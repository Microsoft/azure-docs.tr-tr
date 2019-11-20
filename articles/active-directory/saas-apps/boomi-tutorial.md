---
title: 'Öğretici: Boomı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Boomı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1044a78d9bf1b52a7b0dd9e47d7db6d98b45299c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081962"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Öğretici: Boomı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Boomı tümleştirme hakkında bilgi edineceksiniz. Boomı 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Boomı 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına izin vermek için etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Boomı çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Boomı, **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-boomi-from-the-gallery"></a>Galeriden Boomı ekleme

Boomı 'nın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize bir Boomı eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **boomı** yazın.
1. Sonuçlar panelinden **Boomı** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Azure AD çoklu oturum açmayı, Boomı için yapılandırın ve test edin

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu boomı ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Boomı içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Boomı ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Boomı SSO 'Yu yapılandırın](#configure-boomi-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan, Boomi 'da bir B. Simon 'ya sahip olmak için, **[boomı test kullanıcısı oluşturun](#create-boomi-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Boomi** uygulama tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna bir URL yazın: `https://platform.boomi.com/`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Değeri gerçek yanıt URL 'siyle güncelleştirin. Bu değeri almak için [Boomı istemci desteği ekibine](https://boomi.com/company/contact/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Boomı uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Daha fazlasına ek olarak, Boomı uygulaması aşağıda gösterilen SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Ad |  Kaynak özniteliği|
    | ---------------|  --------- |
    | FEDERATION_ID | Kullanıcı. Mail |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Boomi ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’ tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, B. Simon 'u, Boomi 'ya erişim vererek Azure çoklu oturum açma özelliğini etkinleştirecek şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Boomi**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-boomi-sso"></a>Boomı SSO yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Boomı şirket sitenizde yönetici olarak oturum açın.

1. **Şirket adı** ' na gidin ve **Ayarla**' ya gidin.

1. **SSO seçenekleri** sekmesine tıklayın ve aşağıdaki adımları gerçekleştirin.

    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. **SAML çoklu oturum açmayı etkinleştir** onay kutusunu işaretleyin.

    b. Azure AD 'den **kimlik sağlayıcısı sertifikasına**indirilen sertifikayı yüklemek Için **içeri aktar** ' a tıklayın.

    c. **Kimlik sağlayıcısı oturum açma URL 'si** metin kutusunda, **oturum açma URL 'SI** değerini Azure AD uygulama yapılandırma penceresinden koyun.

    d. **Federasyon kimliği konumu**olarak, **FEDERATION_ID öznitelik öğesi radyo düğmesinde federasyon kimliği** ' ni seçin.

    e. Tıklayın **Kaydet** düğmesi.

### <a name="create-boomi-test-user"></a>Boomı test kullanıcısı oluştur

Azure AD kullanıcılarının, Boomı 'da oturum açmasını etkinleştirmek için, bu kullanıcıların Boomı 'da sağlanması gerekir. Boomi durumunda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:

1. Bir yönetici olarak Boomı şirket sitenizde oturum açın.

1. Oturum açtıktan sonra **Kullanıcı yönetimi** ' ne gidin ve **Kullanıcılar**' a gidin.

    ![Kullanıcılar](./media/boomi-tutorial/tutorial_boomi_001.png "Kullanıcılar")

1. **+** simgesine tıklayın ve **Kullanıcı rolleri ekle/koru** iletişim kutusu açılır.

    ![Kullanıcılar](./media/boomi-tutorial/tutorial_boomi_002.png "Kullanıcılar")

    ![Kullanıcılar](./media/boomi-tutorial/tutorial_boomi_003.png "Kullanıcılar")

    a. **Kullanıcı e-posta adresi** metin kutusuna, B.Simon@contoso.comgibi kullanıcının e-postasını yazın.

    b. **Ad** metin kutusuna B gibi ilk Kullanıcı adını yazın.

    c. **Soyadı** metin kutusunda, Simon gibi kullanıcı adının soyadını yazın.

    d. Kullanıcının **Federasyon kimliğini**girin. Her kullanıcının hesabı içinde kullanıcıyı benzersiz bir şekilde tanımlayan bir Federasyon KIMLIĞI olmalıdır.

    e. Kullanıcıya **Standart Kullanıcı** rolünü atayın. Yönetici rolünü atamayın çünkü bu, bunlara normal Atmosphere erişimine ek olarak çoklu oturum açma erişimi de verecektir.

    f. **OK (Tamam)** düğmesine tıklayın.

    > [!NOTE]
    > Kullanıcı, parolaları kimlik sağlayıcısı aracılığıyla yönetildiğinden, AtomSphere hesabında oturum açmak için kullanılabilecek bir parola içeren bir hoş geldiniz bildirim e-postası almaz. AAD Kullanıcı hesapları sağlamak için, Boomı tarafından sunulan diğer bir Boomı Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde Boomı kutucuğunu tıkladığınızda, SSO 'yu ayarladığınız Boomı 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Boomı 'yi deneyin](https://aad.portal.azure.com/)