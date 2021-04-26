---
title: 'Öğretici: Appraisd ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Appraisd arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 79db0a7bb769d6138bfeeb4e765621a1cffb4d14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457869"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Öğretici: Appraisd ile tümleştirin Azure Active Directory

Bu öğreticide, Appraisd 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Appraisd 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Appraisd 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Appraisd otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Appraisd çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Appraisd **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-appraisd-from-the-gallery"></a>Galeriden Appraisd ekleme

Appraisd tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Appraisd yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Appraisd** yazın.
1. Sonuçlar panelinden **Appraisd** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Appraisd ile yapılandırın ve test edin. SSO 'nun çalışması için, Appraisd içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Appraisd ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[Appraisd öğesini yapılandırın](#configure-appraisd)** .
3. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı Appraisd 'de B. Simon 'ya karşılık gelen bir **[Appraisd test kullanıcısı oluşturun](#create-appraisd-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Appraisd** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının Kaydet düğmesine tıklayarak yapılandırmayı kaydetmesi ve aşağıdaki adımları gerçekleştirmesi gerekir:

    a. **Ek URL 'Ler ayarla**' ya tıklayın.

    b. **Geçiş durumu** metin kutusuna bir URL yazın:`<TENANTCODE>`

    c. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Isterseniz, **oturum açma URL 'si** metin kutusuna AŞAĞıDAKI kalıbı kullanarak bir URL yazın: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Appraisd SSO yapılandırma sayfasında, Öğreticinin ilerleyen kısımlarında açıklanan gerçek oturum açma URL 'SI ve geçiş durumu değerini alırsınız.

1. Appraisd uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName** ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. Appraisd uygulaması, **NameIdentifier** 'ın **User. Mail** ile eşlenmesini bekliyor, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![Ekran görüntüsü, düzenleme simgesi vurgulanmış şekilde Kullanıcı öznitelikleri bölmesini gösterir.](common/edit-attribute.png)

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Appraisd ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Appraisd yapılandırma

1. Yapılandırmayı Appraisd içinde otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra **Setup Appraisd** ' a tıklayın, sizi Appraisd uygulamasına yönlendirir. Buradan, Appraisd 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Appraisd 'i el ile kurmak istiyorsanız, yeni bir Web tarayıcısı penceresi açın ve Appraisd şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Sayfanın sağ üst kısmında, **Ayarlar** simgesine tıklayın ve ardından **yapılandırma**' ya gidin.

    ![Ekran görüntüsünde, çağrılan yapılandırma bağlantısı gösterilir.](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Menünün sol tarafında, **SAML çoklu oturum açma**' ya tıklayın.

    ![Ekran görüntüsü, SAML çoklu oturum açma seçeneği vurgulanmış şekilde yapılandırma seçeneklerini gösterir.](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. **SAML 2,0 tek Sign-On yapılandırma** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, varsayılan geçiş durumu ve hizmet tarafından başlatılan oturum açma U R L 'yi düzenleyebileceğiniz SAML 2,0 tek Sign-On yapılandırma sayfasını gösterir.](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. **Varsayılan geçiş durumu** değerini kopyalayın ve Azure Portal ÜZERINDE **temel SAML yapılandırmasındaki** **geçiş durumu** metin kutusuna yapıştırın.

    b. Hizmet tarafından **başlatılan oturum açma URL 'si** değerini kopyalayın ve Azure Portal ÜZERINDEKI **temel SAML yapılandırmasındaki** **oturum açma URL 'si** metin kutusuna yapıştırın.

7. **Kullanıcıları tanımlama** bölümündeki aynı sayfada aşağı doğru kaydırın, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımdan değer girebileceğiniz kullanıcıları tanımlamayı gösterir.](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. **Kimlik sağlayıcısı tek Sign-On URL** metin kutusunda, **oturum açma url**'sinin, Azure Portal kopyaladığınız değeri yapıştırın ve **Kaydet**' e tıklayın.

    b. **Kimlik sağlayıcısı veren URL** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısı**' nın değerini yapıştırın ve **Kaydet**' e tıklayın.

    c. Not defteri 'nde, Azure portal indirdiğiniz temel 64 kodlu sertifikayı açın, içeriğini kopyalayın ve sonra **X. 509.440 sertifikası** kutusuna yapıştırın ve **Kaydet**' e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B. Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B. Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Appraisd 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Appraisd**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-appraisd-test-user"></a>Appraisd test kullanıcısı oluştur

Azure AD kullanıcılarının Appraisd 'de oturum açmasını sağlamak için, Appraisd ' a sağlanması gerekir. Appraisd ' de, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Appraisd 'de güvenlik yöneticisi olarak oturum açın.

2. Sayfanın sağ üst kısmında, **Ayarlar** simgesine tıklayın ve ardından **Yönetim Merkezi**' ne gidin.

    ![Ekran görüntüsü, Yönetim Merkezi ' ni seçebileceğiniz Ayarlar seçeneklerini gösterir.](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Sayfanın üst kısmındaki araç çubuğunda **kişiler**' e ve ardından **Yeni Kullanıcı Ekle**' ye gidin.

    ![Ekran görüntüsü kişileri içeren Appraisd sayfasını gösterir ve adlı yeni bir kullanıcı ekler.](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. **Yeni Kullanıcı Ekle** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü Yeni Kullanıcı Ekle sayfasını gösterir.](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta** gibi girin.

    b. **Soyadı** metin kutusuna, **Simon** gibi kullanıcı adının soyadını girin.

    c. **E-posta** metin kutusuna kullanıcının e-postasını girin `B. Simon@contoso.com` .

    d. **Kullanıcı Ekle**' ye tıklayın.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Appraisd kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Appraisd için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)