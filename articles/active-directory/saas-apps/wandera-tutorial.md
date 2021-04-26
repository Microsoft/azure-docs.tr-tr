---
title: 'Öğretici: Wandera RADAR Yöneticisi ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Wandera RADAR Yöneticisi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 383b49a7363fdca9327d419f5bed092d489a95d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951819"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Öğretici: Wandera RADAR yöneticisini Azure Active Directory ile tümleştirme

Bu öğreticide, Wandera RADAR yöneticisini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Wandera RADAR yöneticisini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Wandera RADAR yöneticisine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla bir RADAR yönetici için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Wandera RADAR yönetici çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Wandera RADAR Yöneticisi **IDP** tarafından başlatılan SSO 'yu destekler
* Wandera RADAR yöneticisini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>Galeriden Wandera RADAR Yöneticisi ekleme

Wandera RADAR yöneticisinin tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Wandera RADAR Yöneticisi eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **WANDERA radar Yöneticisi** yazın.
1. Sonuçlar panelinden **Wandera radar Yöneticisi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Wandera radar Yöneticisi Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bir RADAR yönetici ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Wandera RADAR Yöneticisi ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
   * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
   * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Wandera radar yönetıcı SSO 'Yu yapılandırın](#configure-wandera-radar-admin-sso)** .
   * Kullanıcı Azure AD gösterimi ile bağlantılı bir RADAR yönetici olan Wandera radar yönetici **[test kullanıcısı oluşturun](#create-wandera-radar-admin-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **WANDERA radar yönetici** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek yanıt URL 'siyle güncelleştirin. Değeri almak için [Wandera radar yönetici istemci destek ekibine](https://www.wandera.com/about-wandera/contact/#supportsection) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz. <tenant id>Yukarıdaki URL 'nin bir kısmını,   >    >  wandera hesabınızın içindeki ayarlar yönetimi **Çoklu oturum açma** sayfasında gösterilen kiracı kimliğiyle birlikte dikkatle değiştirin.


1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek Için **SAML imzalama sertifikası** için Düzenle/kalem simgesine tıklayın.

    ![İmzalama seçeneği](common/signing-option.png)

    1. **Imza seçeneğini** belirtin **SAML yanıtı ve onaylama olarak imzala**.

    1. **Imzalama algoritmasını** **SHA-256** olarak seçin.

1. **BIR radar yönetici ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Wandera RADAR yöneticisine erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Wandera radar Yöneticisi**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-wandera-radar-admin-sso"></a>Wandera RADAR Yöneticisi SSO 'yu yapılandırma

1. Wandera RADAR Yöneticisi içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, Kurulum ' a tıklayın. **BIR radar yönetici** , sizi Wandera radar yönetici uygulamasına yönlendirdirecektir. Buradan, bir RADAR yönetici için oturum açmak üzere yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-4 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Wandera RADAR yöneticisini el ile kurmak isterseniz, yeni bir Web tarayıcısı penceresi açın ve Wandera RADAR yönetici şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Sayfanın sağ üst köşesinde, **Ayarlar**  >  **yönetimi**  >  **Çoklu oturum açma** ' ya tıklayın ve ardından aşağıdaki adımları gerçekleştirmek için **SAML 2,0** ' i etkinleştir seçeneğini işaretleyin.

    ![Wandera RADAR yönetici yapılandırması](./media/wandera-tutorial/config01.png)

    a. **Gerekli alanları seçin veya el ile girin**.

    b. **IDP EntityId** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. Federasyon meta veri XML dosyasını Not defteri 'nde açın, içeriğini kopyalayın ve **IDP genel X. 509.440 sertifikası** metin kutusuna yapıştırın.

    d. **Kaydet**’e tıklayın.

### <a name="create-wandera-radar-admin-test-user"></a>Wandera RADAR yönetici test kullanıcısı oluşturma

Bu bölümde, Wandera RADAR Yöneticisi 'nde B. Simon adlı bir Kullanıcı oluşturursunuz. wandera radar yönetici platformunda kullanıcıları eklemek için [Wandera radar yönetici destek ekibi](https://www.wandera.com/about-wandera/contact/#supportsection) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Wandera RADAR Yöneticisi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Wandera RADAR yöneticisinin içinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)
