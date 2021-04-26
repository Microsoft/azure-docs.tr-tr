---
title: 'Öğretici: Displayr ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Displayr arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3cb6ee3162c70d2d07c4868ae90ecc54bd489966
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622499"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Öğretici: Displayr 'yi Azure Active Directory tümleştirin

Bu öğreticide, Displayr 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Displayr 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Displayr 'ye erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Displayr için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Displayr çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Displayr **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-displayr-from-the-gallery"></a>Galeriden Displayr ekleme

Displayr 'nin tümleştirmesini Azure AD 'ye göre yapılandırmak için galerideki Displayr 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **displayr** yazın.
1. Sonuçlar panelinden **Displayr** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Azure AD SSO 'yu, **Britta Simon** adlı bir test kullanıcısı kullanarak displayr ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Displayr içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Displayr ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[Displayr öğesini yapılandırın](#configure-displayr)** .
3. Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcı Azure AD gösterimine bağlı olan Displayr 'de Britta Simon 'a sahip olacak şekilde **[displayr test kullanıcısı oluşturun](#create-displayr-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **displayr** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarla** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları uygulayın:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YOURDOMAIN>.displayr.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`<YOURDOMAIN>.displayr.com`
    
    c. **Yanıt URL 'si** metin kutusuna yazın `https://app.displayr.com/Login/ProcessSamlResponse` .
    
    d. **Kaydet**’e tıklayın.

    >[!NOTE]
    >Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Displayr istemci desteği ekibine](mailto:support@displayr.com) başvurun. Ayrıca, Azure portal temel SAML yapılandırması bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tek Sign-On ayarla** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. Displayr uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

   !["Düzenle" simgesi vurgulanmış "Kullanıcı öznitelikleri" bölümünü gösteren ekran görüntüsü.](common/edit-attribute.png)

1. Yukarıdaki ' a ek olarak, Displayr uygulaması SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. **Grup talepleri (Önizleme)** Iletişim kutusundaki **Kullanıcı öznitelikleri & talepler** bölümünde aşağıdaki adımları uygulayın:

   a. **Grup talebi ekle**' ye tıklayın.

      !["Talepler (Önizleme) penceresini ayarlar seçiliyken gösteren ekran görüntüsü.](./media/displayr-tutorial/config05.png)

   b. Radyo listesinden **tüm gruplar** ' ı seçin.

   c. **Grup Kimliği**'Nin **kaynak özniteliğini** seçin.

   d. **Grup talebinin adını özelleştirmeyi** denetleyin.

   e. **Grup gruplarını rol talepleri olarak** kontrol edin.

   f. **Kaydet**’e tıklayın.

1. **Kurulum Displayr** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Displayr 'yi yapılandırma

1. Displayr 'deki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **displayr** ' ye tıklayın, sizi displayr uygulamasına yönlendirir. Buradan, Displayr 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Displayr 'yi el ile ayarlamak isterseniz, yeni bir Web tarayıcı penceresi açın ve Displayr şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. **Ayarlar** ' a tıklayın ve ardından **Hesap**' a gidin.

    !["Ayarlar" simgesini ve "hesabı" seçili olduğunu gösteren ekran görüntüsü.](./media/displayr-tutorial/config01.png)

5. Üstteki menüden **Ayarlar** ' a geçin ve sayfayı kaydırarak **Çoklu oturum AÇMAYı Yapılandır (SAML)** seçeneğine tıklayın.

    !["Ayarlar" sekmesinin seçili olduğunu ve "çoklu oturum açmayı Yapılandır (S A M L)" eyleminin seçildiği ekran görüntüsü.](./media/displayr-tutorial/config02.png)

6. **Çoklu oturum açma (SAML)** sayfasında, aşağıdaki adımları uygulayın:

    ![Yapılandırma](./media/displayr-tutorial/config03.png)

    a. **Çoklu oturum açma (SAML) kutusunu Etkinleştir** kutusunu işaretleyin.

    b. Azure AD 'nin **temel SAML yapılandırması** bölümünden gerçek **tanımlayıcı** değerini kopyalayın ve **sertifikayı veren** metin kutusuna yapıştırın.

    c. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. **Logout URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    e. Sertifika (base64) Not defteri 'nde açın, içeriğini kopyalayın ve **sertifikayı sertifika** metin kutusuna yapıştırın.

    f. **Grup eşlemeleri** isteğe bağlıdır.

    örneğin: **Kaydet**’e tıklayın.  

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Displayr 'ye erişim izni vererek Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Displayr**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-displayr-test-user"></a>Displayr test kullanıcısı oluştur

Azure AD kullanıcılarını etkinleştirmek için, Displayr 'de oturum açın ve Displayr 'de sağlanması gerekir. Displayr 'de, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Yönetici olarak Displayr ' de oturum açın.

2. **Ayarlar** ' a tıklayın ve ardından **Hesap**' a gidin.

    !["Account" işaretli "ayarlar (COG)" simgesini gösteren ekran görüntüsü.](./media/displayr-tutorial/config01.png)

3. Üstteki menüden **Ayarlar** ' a geçin ve sayfayı aşağı kaydırarak, **Kullanıcılar** bölümünde **Yeni Kullanıcı**' ya tıklayın.

    !["Kullanıcılar" vurgulanmış ve "Yeni Kullanıcı" düğmesi seçili "Ayarlar" sekmesini gösteren ekran görüntüsü.](./media/displayr-tutorial/config07.png)

4. **Yeni Kullanıcı** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Displayr yapılandırması](./media/displayr-tutorial/config06.png)

    a. **Ad** metin kutusuna, **Brittasıon** gibi kullanıcının adını girin.

    b. **E-posta** metin kutusuna kullanıcının e-postasını girin `Brittasimon@contoso.com` .

    c. Uygun **Grup üyeliğinizi** seçin.

    d. **Kaydet**’e tıklayın.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Displayr kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Displayr 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)
