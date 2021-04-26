---
title: 'Öğretici Azure Active Directory: ContractSafe SAML2 SSO ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve ContractSafe SAML2 SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 2d236b9910e2eda8e574d020544d625a68aefb81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455330"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Öğretici: ContractSafe SAML2 SSO ile çoklu oturum açma (SSO) Azure Active Directory tümleştirme

Bu öğreticide, ContractSafe SAML2 SSO 'yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. ContractSafe SAML2 SSO 'yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de ContractSafe SAML2 SSO 'ya kimlerin erişimi olduğunu denetleyin.
* Kullanıcılarınızın Azure AD hesaplarıyla ContractSafe SAML2 SSO 'da otomatik olarak oturum açmasını sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için gerekli olanlar:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SSO özellikli bir ContractSafe SAML2 SSO aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. ContractSafe SAML2 SSO, **IDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Galeriden ContractSafe SAML2 SSO 'SU ekleme

ContractSafe SAML2 SSO 'SU ile Azure AD arasında tümleştirmeyi yapılandırmak için, galerisindeki ContractSafe SAML2 SSO 'yu, yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **CONTRACTSAFE SAML2 SSO** yazın.
1. Sonuçlar panelinden **Contractsafe SAML2 SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>ContractSafe SAML2 SSO için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Contractsafe SAML2 SSO ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ContractSafe SAML2 SSO içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu ContractSafe SAML2 SSO ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
   * **B. Simon** hesabını kullanarak Azure AD SSO 'yu test etmek Için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
   * Azure AD SSO 'yu kullanmak için **B. Simon** 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .

1. Uygulama tarafında SSO ayarlarını yapılandırmak için [ContractSafe SAML2 SSO 'Yu yapılandırın](#configure-contractsafe-saml2-sso) .
   * ContractSafe SAML2 SSO 'da kullanıcının Azure AD gösterimine bağlı olan **B. Simon** 'a karşılık gelen bir [contractsafe SAML2 SSO test kullanıcısı oluşturun](#create-a-contractsafe-saml2-sso-test-user) .
2. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), **CONTRACTSAFE SAML2 SSO** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle (kalem) simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, karşılık gelen alanlara aşağıdaki değerleri girin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki biçimi kullanarak bir URL girin:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki biçimi kullanarak bir URL girin:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Contractsafe SAML2 SSO istemci destek ekibine](mailto:support@contractsafe.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen biçimlere de başvurabilirsiniz.

1. ContractSafe SAML2 SSO 'SU, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Ortak varsayılan öznitelikler](common/default-attributes.png)

1. Varsayılan özniteliklerin yanı sıra, ContractSafe SAML2 SSO uygulaması SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. Bu öznitelikler önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz. Aşağıdaki listede ek öznitelikler gösterilmektedir.

    | Name | Kaynak özniteliği|
    | ---------------| --------------- |
    | EmailName | User. UserPrincipalName |
    | e-posta | User. onpremisesuserprincipalname |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**'i bulun. Sertifikayı indirmek için **İndir** ' i seçin ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Contract Safe SAML2 SSO ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, **B. Simon** adlı Azure Portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin. **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, biçiminde bir e-posta adresi girin `username@companydomain.extension` . `B.Simon@contoso.com` bunun bir örneğidir.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

## <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, ContractSafe SAML2 SSO 'ya erişim vererek **B. Simon** 'U Azure SSO 'yu kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Contractsafe SAML2 SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve ardından **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

   ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon** öğesini seçin. Ardından, ekranın alt kısmındaki **Seç** düğmesini seçin.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından, ekranın alt kısmındaki **Seç** düğmesini seçin.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

## <a name="configure-contractsafe-saml2-sso"></a>ContractSafe SAML2 SSO yapılandırma

**Contractsafe SAML2 SSO** tarafında SSO 'yu yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal [contractsafe SAML2 SSO destek ekibine](mailto:support@contractsafe.com)göndermeniz gerekir. Ekip, SAML SSO bağlantısının her iki tarafında da düzgün şekilde kurulmasından sorumludur.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>ContractSafe SAML2 SSO test kullanıcısı oluşturma

ContractSafe SAML2 SSO 'da B. Simon adlı bir kullanıcı oluşturun. ContractSafe SAML2 SSO platformunda kullanıcıları eklemek için [Contractsafe SAML2 SSO destek ekibi](mailto:support@contractsafe.com) ile çalışın. SSO kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Erişim panelini kullanarak Azure AD SSO yapılandırmanızı test edin. Erişim panelinde ContractSafe SAML2 SSO kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız ContractSafe SAML2 SSO 'su için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile ContractSafe SAML2 SSO 'yu deneyin](https://aad.portal.azure.com/)