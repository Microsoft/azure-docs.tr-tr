---
title: 'Öğretici: yaptığımız şekilde tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve yöntemi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 0205617198ec8e57cb891d2415d2b5413a945f1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608944"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile yaptığımız şekilde tümleştirin

Bu öğreticide, Azure Active Directory (Azure AD) ile yaptığımız şekilde nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile yaptığımız şekilde tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de yaptığımız şekilde erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla yaptığımız şekilde otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Çoklu oturum açma (SSO) özellikli abonelik yaptığımız bir yöntemdir.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* **SP** tarafından başlatılan SSO 'yu destekleme yöntemi
* **Yalnızca zamanında** Kullanıcı sağlamayı desteklediğimiz şekilde

## <a name="adding-way-we-do-from-the-gallery"></a>Galeriden yaptığımız gibi ekleme

Azure AD 'de yaptığımız yönteme ilişkin tümleştirmeyi yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize yaptığımız gibi bir yöntem eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusunda **yaptığımız şekilde** yazın.
1. Sonuçlar panelinden **yaptığımız yöntemi** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Azure AD SSO 'yu, **B. Simon** adlı bir test kullanıcısı kullandığımız şekilde yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında yaptığımız gibi bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu bunu yaptığımız şekilde yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[SSO yaptığımız yöntemi yapılandırın](#configure-way-we-do-sso)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlandığımızda yaptığımız gibi, Kullanıcı sınaması yaptığımız gibi, **[Kullanıcı sınama konusunda bir yol oluşturun](#create-way-we-do-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), uygulama tümleştirmesi **yaptığımız** gibi, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [istemci destek ekibi yaptığımız Iletişim gibi](mailto:support@waywedo.com) iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **sertifika (ham)** bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/certificateraw.png)

1. **Yaptığımız ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>SSO yaptığımız gibi yapılandırma

1. Yapılandırmayı yaptığımız şekilde otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya uzantı ekledikten sonra, bunu uygulama yaptığımız yönteme **yönlendirmemiz Için kurulum** 'a tıklayın. Buradan, yaptığımız şekilde oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. El ile yaptığımız şekilde ayarlama yapmak istiyorsanız, yeni bir Web tarayıcısı penceresi açın ve şirket sitesini yönetici olarak gerçekleştirdiğimiz şekilde oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Her bir sayfanın sağ üst köşesindeki **kişi simgesine** ve ardından açılan menüden **Hesap** ' a tıklayın.

    ![Hesap yaptığımız gibi](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Düğme gezintisi menüsünü açmak için **menü simgesine** tıklayın ve **Çoklu oturum aç**' a tıklayın.

    ![Tek yaptığımız gibi](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. **Çoklu oturum açma kurulumu** sayfasında, aşağıdaki adımları uygulayın:

    ![Kaydetme yöntemi](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    1. Çoklu oturum **açmayı etkinleştirmek için çoklu oturum açma aç** ' a tıklayarak **Evet** ' e tıklayın.

    1. **Çoklu oturum açma adı** metin kutusuna adınızı girin.

    1. **VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    1. **SAML SSO URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. **Sertifika**' nın yanındaki **Seç** düğmesine tıklayarak sertifikayı karşıya yükleyin.

    1. **İsteğe bağlı ayarlar** -

        * Parolaları etkinleştir-Bu seçenek devre dışı bırakıldığında, kullanıcıların yalnızca çoklu oturum açma özelliğini kullanmasını sağlayacak şekilde, normal parola işlevleri çalışır.

        * Otomatik sağlamayı etkinleştir-Bu etkinleştirildiğinde, oturum açmak için kullanılan e-posta adresi otomatik olarak, yaptığımız gibi kullanıcı listesiyle karşılaştırılır. E-posta adresi yaptığımız gibi etkin bir kullanıcıyla eşleşmezse, oturum açan kişi için otomatik olarak yeni bir kullanıcı hesabı ekler ve eksik bilgiler Ister.

          > [!NOTE]
          > Çoklu oturum açma yoluyla eklenen kullanıcılar genel kullanıcı olarak eklenir ve sistemde bir rol atanmaz. Yönetici, güvenlik rollerini bir düzenleyici veya yönetici olarak değiştirebilir ve aynı zamanda bir veya birkaç kuruluş grafiği rolü atayabilirler.

    1. Ayarlarınızı sürdürmek için **Kaydet** ' e tıklayın.

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

Bu bölümde, Azure çoklu oturum açmayı kullanarak, yaptığımız şekilde erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **yaptığımız gibi** seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-way-we-do-test-user"></a>Kullanıcı sınaması yaptığımız şekilde oluştur

Bu bölümde, Britta Simon adlı bir Kullanıcı yaptığımız şekilde oluşturulmuştur. Bu sayede, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekliyoruz. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı yaptığımız şekilde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!Note]
> Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [istemci destek ekibi yaptığımız gibi](mailto:support@waywedo.com)iletişim kurun.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde kutucuk yaptığımız yöntemi seçtiğinizde, SSO 'yu ayarladığınız şekilde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)