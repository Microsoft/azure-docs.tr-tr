---
title: 'Öğretici: Lenses.io ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ile Lenses.io arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Öğretici: Lenses.io DataOps portalı ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, [lenses.io](https://lenses.io/) dataops portalını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Lenses.io 'i Azure AD ile tümleştirdikten sonra şunları yapabilirsiniz:

* Azure AD 'de Lenses.io portalına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure AD ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bir mercekler portalının örneği. Çeşitli [dağıtım seçenekleri](https://lenses.io/product/deployment/)arasından seçim yapabilirsiniz.
* Çoklu oturum açmayı (SSO) destekleyen bir Lenses.io [lisansı](https://lenses.io/product/pricing/) .

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edeceksiniz.

* Lenses.io, hizmet sağlayıcısı (SP) tarafından başlatılan SSO 'yu destekler.

* Lenses.io yapılandırdıktan sonra oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi, kuruluşunuzun hassas verilerinin, gerçek zamanlı olarak ayıklanma ve savunma korumasını korur. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Galeriden Lenses.io ekleme

Lenses.io tümleştirmesini Azure AD 'ye yapılandırmak için, yönetilen SaaS uygulamaları listenize Lenses.io ekleyin:

1. İş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. **Yeni uygulama**’yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **lenses.io** girin.
1. Sonuçlar panelinden **lenses.io**' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Lenses.io için Azure AD SSO 'yu yapılandırma ve test etme

Lenses.io portalınızla Azure AD SSO 'yu yapılandırmak ve test etmek için *B. Simon* adlı bir test kullanıcısı oluşturacaksınız. SSO 'nun çalışması için, Lenses.io içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Aşağıdaki adımları tamamlayın:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. Azure AD SSO 'yu B. Simon ile test etmek için [bir Azure AD test kullanıcısı ve grubu oluşturun](#create-an-azure-ad-test-user-and-group) .
    1. Azure AD SSO 'yu kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında SSO ayarlarını yapılandırmak için [LENSES.IO SSO 'Yu yapılandırın](#configure-lensesio-sso) .
    1. B. Simon 'ın Lenses.io 'ye (yetkilendirme) erişip erişebileceklerini denetlemek için [lenses.io test grubu Izinleri oluşturun](#create-lensesio-test-group-permissions) .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), **lenses.io** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesini seçin.

   ![Temel SAML yapılandırmasını düzenlemenin simgesini gösteren ekran görüntüsü.](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki metin girişi kutularına değerler girin:

    a. **Oturum açma URL 'si**: Şu düzene sahıp bir URL girin: `https://<CUSTOMER_LENSES_BASE_URL>` . `https://lenses.my.company.com` bunun bir örneğidir.

    b. **Tanımlayıcı (VARLıK kimliği)**: Şu düzene sahıp bir URL girin: `https://<CUSTOMER_LENSES_BASE_URL>` . `https://lenses.my.company.com` bunun bir örneğidir.

    c. **Yanıt URL 'si**: Şu düzene sahıp bir URL girin: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` . `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client` bunun bir örneğidir.

    > [!NOTE]
    > Bu değerler gerçek değildir. Bunları gerçek oturum açma URL 'SI, yanıt URL 'si ve mercekler portalı örneğinizin temel URL 'SI ile güncelleştirin. Daha fazla bilgi için bkz. [LENSES.IO SSO belgeleri](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) .

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümüne gidin. **Federasyon meta VERI XML**'i bulun ve ardından sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısını gösteren ekran görüntüsü.](common/metadataxml.png)

1. **Lenses.io ayarla** bölümünde, Azure SSO 'nuzu yapılandırmak IÇIN indirdiğiniz XML dosyasını kullanın.

### <a name="create-an-azure-ad-test-user-and-group"></a>Azure AD test kullanıcısı ve grubu oluşturma

Azure portal, B. Simon adlı bir test kullanıcısı oluşturacaksınız. Daha sonra, B. Simon 'un lense erişimini denetleyen bir test grubu oluşturacaksınız.

Mercekler 'in, [merme SSO belgelerinde](https://docs.lenses.io/install_setup/configuration/security.html#id3)yetkilendirme için Grup üyeliği eşlemesini nasıl kullandığını öğrenebilirsiniz.

**Test kullanıcısını oluşturmak için:**

1. Azure portal sol bölmesinde **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmında **Yeni Kullanıcı**' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** kutusuna **B. Simon** girin.  
   1. **Kullanıcı adı** kutusuna, girin username@companydomain.extension . Örneğin, B.Simon@contoso.com.
   1. **Parolayı göster** onay kutusunu seçin. **Parola kutusunda gösterilen** parolayı yazın.
   1. **Oluştur**’u seçin.

**Grubu oluşturmak için:**

1. **Azure Active Directory**' e gidin ve ardından **gruplar**' ı seçin.
1. Ekranın üst kısmında **Yeni Grup**' u seçin.
1. **Grup özelliklerinde** şu adımları izleyin:
   1. **Grup türü** kutusunda **güvenlik**' i seçin.
   1. **Grup adı** kutusuna **lensesusers** yazın.
   1. **Oluştur**’u seçin.
1. **Mersesusers** grubunu seçin ve **nesne kimliğini** (örneğin, f8b5c1ec-45de-4ABD-AF5C-e874091fb5f7) kopyalayın. Bu KIMLIĞI, Grup kullanıcılarını [doğru izinlerle](https://docs.lenses.io/install_setup/configuration/security.html#id3)eşlemek Için merler içinde kullanacaksınız.  

**Grubu test kullanıcısına atamak için:**

1. **Azure Active Directory**' e gidin ve ardından **Kullanıcılar**' ı seçin.
1. **B. Simon** test kullanıcısını seçin.
1. **Grupları** seçin.
1. Ekranın üst kısmında, **Üyelik Ekle**' yi seçin.
1. **Merseskullanıcıları** için arama yapın ve seçin.
1. **Seç**’e tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Lenses.io 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **lenses.io**' yi seçin.
1. Uygulamaya Genel Bakış sayfasında, **Yönet** bölümünde **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısını gösteren ekran görüntüsü.](common/users-groups-blade.png)

1. **Kullanıcı ekle**'yi seçin.

   ![Kullanıcı Ekle bağlantısını gösteren ekran görüntüsü.](common/add-assign-user.png)

1. **Atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

## <a name="configure-lensesio-sso"></a>Lenses.io SSO 'yu yapılandırma

**Lenses.io** portalında SSO 'yu yapılandırmak için, Indirilen **Federasyon meta veri XML** 'sini mercekler ÖRNEĞINIZDE yükleyip, [SSO 'yu etkinleştirmek için merler 'i yapılandırın](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Lenses.io test grubu izinleri oluştur

1. Merceklerde bir grup oluşturmak için **Lensesusers** grubunun **nesne kimliğini** kullanın. Bu, Kullanıcı [oluşturma bölümünde](#create-an-azure-ad-test-user-and-group)kopyaladığınız kimliğidir.
1. B. Simon için istenen izinleri atayın.

Daha fazla bilgi için bkz. [Azure-lenses grubu eşleme](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD SSO yapılandırmanızı test edin.

Erişim panelinde Lenses.io kutucuğunu seçtiğinizde, Lenses.io portalınıza otomatik olarak oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [Erişim Paneli'ne Giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [Lenses.io Örneğinizde SSO 'yu ayarlama](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [SaaS uygulamalarını Azure AD ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure AD ile uygulama erişimi ve SSO nedir?](../manage-apps/what-is-single-sign-on.md)

- [Azure AD 'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Lenses.io deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle Lenses.io koruma](/cloud-app-security/proxy-intro-aad)