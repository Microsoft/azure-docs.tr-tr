---
title: 'Öğretici: Replicon ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Replıon arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 8915d780e79fa219428c54bad5458ab5966df6c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688510"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile Replicon tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile Replicon tümleştirme hakkında bilgi edineceksiniz. Replicon 'u Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Replıon 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açabilmesi için etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Replicon çoklu oturum açma (SSO) etkin abonelik.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Replicon, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-replicon-from-the-gallery"></a>Galeriden Replicon ekleme

Replicon 'un Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Replicto ' ı eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Replicon** yazın.
1. Sonuçlar panelinden **Replicon** ' u seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Replicon ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında Replılicon bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Replicon ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Replıon SSO 'Yu yapılandırın](#configure-replicon-sso)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. Kullanıcı için Azure AD gösterimine bağlı olan, Replıon 'da B. Simon 'a sahip olmak için, **[test kullanıcısı oluşturun](#create-replicon-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **, uygulama tümleştirme** tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    1. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://global.replicon.com/!/saml2/<client name>`

    1. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için, [istemci desteği ekibine](https://www.replicon.com/customerzone/contact-support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Ayarları düzenlemek için **SAML Imzalama sertifikası** için Düzenle/kalem simgesine tıklayın.

    ![İmzalama algoritması](common/signing-algorithm.png)

    1. **Imza seçeneği** olarak **SAML onaylama imzasını imzala** ' yı seçin.

    1. **Imzalama algoritması** olarak **SHA-256** ' ı seçin.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Replılicon SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, şirket içinde bir yönetici olarak oturum açın.

2. SAML 2,0 'yi yapılandırmak için aşağıdaki adımları uygulayın:

    ![SAML kimlik doğrulamasını etkinleştir](./media/replicon-tutorial/ic777805.png "SAML kimlik doğrulamasını etkinleştir")

    a. **Enablesaml Authentication2** iletişim kutusunu göstermek için, aşağıdaki URL 'nize şirketinizin anahtarınızı ekleyin:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Aşağıda, URL 'nin tamamının şeması gösterilmektedir: `https://na2.replicon.com/<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. **+** **V20Configuration** bölümünü genişletmek için öğesine tıklayın.

   c. **+** **Metadataconfiguration** bölümünü genişletmek için öğesine tıklayın.

   d. XmlSignatureAlgorithm için **SHA256** seçin

   e. **Dosya Seç**' e tıklayarak kimlik sağlayıcısı meta veri XML dosyanızı seçin ve **Gönder**' e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Replicon 'a erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Replicon**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-replicon-test-user"></a>Test kullanıcısı oluşturma

Bu bölümün amacı, Replicon 'da B. Simon adlı bir Kullanıcı oluşturmaktır.

**Kullanıcı el ile oluşturmanız gerekiyorsa aşağıdaki adımları gerçekleştirin:**

1. Bir Web tarayıcısı penceresinde, şirket içinde bir yönetici olarak oturum açın.

2. **Yönetim \> kullanıcıları**' na gidin.

    ![Kullanıcılar](./media/replicon-tutorial/ic777806.png "Kullanıcılar")

3. **+ Kullanıcı Ekle**' ye tıklayın.

    ![Kullanıcı Ekle](./media/replicon-tutorial/ic777807.png "Kullanıcı Ekleme")

4. **Kullanıcı profili** bölümünde aşağıdaki adımları uygulayın:

    ![Kullanıcı profili](./media/replicon-tutorial/ic777808.png "Kullanıcı profili")

    a. **Oturum açma adı** metin kutusuna, sağlamak ISTEDIĞINIZ Azure AD KULLANıCıSıNıN Azure ad e-posta adresini yazın `B.Simon@contoso.com` .

    > [!NOTE]
    > Oturum açma adının, Azure AD 'de kullanıcının e-posta adresiyle eşleşmesi gerekir

    b. **Kimlik doğrulama türü** olarak **SSO**' yı seçin.

    c. Kimlik doğrulama KIMLIĞINI oturum açma adı ile aynı değere ayarla (kullanıcının Azure AD e-posta adresi)

    d. **Bölüm** metin kutusuna kullanıcının departmanını yazın.

    e. **Çalışan türü** olarak **yönetici**' yi seçin.

    f. **Kullanıcı profilini kaydet**' e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, Replıon tarafından sunulan başka bir Replicon Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Replicon kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Replıon 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)