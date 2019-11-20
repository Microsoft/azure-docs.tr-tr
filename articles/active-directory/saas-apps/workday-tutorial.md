---
title: 'Öğretici: Workday ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Workday arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fe63ba810724216b1b356896b621f1e5b021bbf
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "69891973"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Öğretici: Workday ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Workday 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Workday 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Workday 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Workday 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Workday çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Workday, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-workday-from-the-gallery"></a>Galeriden Workday ekleme

Workday 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden iş Workday 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Workday** yazın.
1. Sonuçlar panelinden **Workday** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Workday için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Workday Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Workday 'de bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Workday ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[Workday 'ı yapılandırın](#configure-workday)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Workday 'de B. Simon 'a karşılık gelen bir iş **[günü test kullanıcısı oluşturun](#create-workday-test-user)** .
3. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Workday** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `http://www.workday.com`

    c. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve yanıt URL 'SI ile güncelleştirin. Yanıt URL 'niz için bir alt etki alanı olmalıdır; örneğin: www, WD2, WD3, WD3-Impl, wd5, wd5-Impl).
    > @No__t_0 gibi bir şey kullanılması, ancak `http://myworkday.com` değildir. Bu değerleri almak için [Workday istemci destek ekibine](https://www.workday.com/en-us/partners-services/services/support.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. Workday uygulamanız belirli bir biçimde SAML onayları bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName**ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. Workday uygulaması, **NameIdentifier** 'ın **User. Mail**, **UPN**vb. ile eşlenmesini bekliyor, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Burada ad KIMLIĞINI UPN (User. UserPrincipalName) ile varsayılan olarak eşleştirdik. SSO 'nun başarıyla çalışması için, ad KIMLIĞINI Workday hesabınızdaki (e-postanız, UPN, vb.) gerçek Kullanıcı KIMLIĞIYLE eşlemeniz gerekir.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **İmzalama** seçeneklerini gereksiniminize göre değiştirmek Için, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Imza seçin **onay IÇIN SAML yanıtı ve onayını Imzala** **seçeneğini**belirleyin.

    b. **Kaydet**’e tıklayın

1. Workday 'yi **Ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Workday 'e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Workday**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-workday"></a>Workday yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, iş günü Şirket sitenizde yönetici olarak oturum açın.

2. **Arama kutusunda** , ana sayfanın sol üst tarafında bulunan **kiracı kurulumunu Düzenle – güvenlik** adlı arama yapın.

    ![Kiracı güvenliğini Düzenle](./media/workday-tutorial/IC782925.png "Kiracı güvenliğini Düzenle")

3. **Yeniden yönlendirme URL 'leri** bölümünde aşağıdaki adımları uygulayın:

    ![Yeniden yönlendirme URL 'Leri](./media/workday-tutorial/IC7829581.png "Yeniden yönlendirme URL 'Leri")

    a. **Satır ekle**' ye tıklayın.

    b. **Oturum açma yeniden yönlendirme URL 'si**, **zaman AŞıMı yeniden yönlendirme URL** 'Si ve **Mobil yeniden yönlendirme url 'si** metin kutusunda, Azure Portal Iş **günü ayarlama** bölümünde kopyaladığınız **oturum açma URL 'sini** yapıştırın.

    c. **Logout REDIRECT URL** metin kutusuna, Azure Portal Iş **günü ayarlama** bölümünde kopyaladığınız **oturum kapatma URL 'sini** yapıştırın.

    d. **Ortamlar Için kullanılan** metin kutusunda ortam adını seçin.  

   > [!NOTE]
   > Ortam özniteliğinin değeri, kiracı URL 'sinin değerine bağlıdır:  
   > -Workday kiracı URL 'sinin etki alanı adı Impl ile başlıyorsa örneğin: *https: \//impl.workday.com/\<tenant \>/Login-SAML2,Flex*), **ortam** özniteliği uygulama olarak ayarlanmalıdır.  
   > -Etki alanı adı başka bir şeyle başlıyorsa, eşleşen **ortam** değerini almak Için [Workday istemci destek ekibine](https://www.workday.com/en-us/partners-services/services/support.html) başvurmanız gerekir.

4. **SAML kurulumu** bölümünde aşağıdaki adımları uygulayın:

    ![SAML kurulumu](./media/workday-tutorial/IC782926.png "SAML kurulumu")

    a.  **SAML kimlik doğrulamasını etkinleştir**' i seçin.

    b.  **Satır ekle**' ye tıklayın.

5. **SAML kimlik sağlayıcıları** bölümünde aşağıdaki adımları uygulayın:

    ![SAML kimlik sağlayıcıları](./media/workday-tutorial/IC7829271.png "SAML kimlik sağlayıcıları")

    a. **Kimlik sağlayıcısı adı** metin kutusuna bir sağlayıcı adı yazın (örneğin: *Spinitilik SSO*).

    b. Azure portal, **Workday 'Yi ayarlama** bölümünde, **Azure AD tanımlayıcı** değerini kopyalayın ve ardından **sertifikayı verenin** metin kutusuna yapıştırın.

    ![SAML kimlik sağlayıcıları](./media/workday-tutorial/IC7829272.png "SAML kimlik sağlayıcıları")

    c. Azure portal, **Workday 'Yi ayarlama** bölümünde, **oturum kapatma URL** 'si değerini kopyalayın ve ardından bunu **Logout Response URL** metin kutusuna yapıştırın.

    d. Azure portal, **Workday 'Yi ayarlama** bölümünde, **oturum açma URL 'si** değerini kopyalayın ve sonra **IDP SSO hizmeti URL 'si** metin kutusuna yapıştırın.

    e. **Ortamlar Için kullanılan** metin kutusunda ortam adını seçin.

    f. **Kimlik sağlayıcısı ortak anahtar sertifikası**' na tıklayın ve ardından **Oluştur**' a tıklayın.

    ![Oluşturma](./media/workday-tutorial/IC782928.png "Create")

    g. **X509 ortak anahtarı oluştur**' a tıklayın.

    ![Oluşturma](./media/workday-tutorial/IC782929.png "Create")

6. **X509 ortak anahtarını görüntüle** bölümünde aşağıdaki adımları uygulayın:

    ![X509 ortak anahtarını görüntüle](./media/workday-tutorial/IC782930.png "X509 ortak anahtarını görüntüle")

    a. **Ad** metin kutusuna sertifikanız için bir ad yazın (örneğin: *PPE \_SP*).

    b. **Geçerli from** metin kutusuna sertifikanızın geçerli öznitelik değerini yazın.

    c.  **Geçerli** metin kutusuna sertifikanızın geçerli öznitelik değerini yazın.

    > [!NOTE]
    > İndirilen sertifikadan geçerli başlama tarihi ve geçerli bitiş tarihi ' ni çift tıklayarak alabilirsiniz.  Tarihler, **Ayrıntılar** sekmesinin altında listelenir.
    >
    >

    d.  Base-64 kodlu sertifikanızı Not defteri 'nde açın ve ardından içeriğini kopyalayın.

    e.  **Sertifika** metin kutusunda, panonuzun içeriğini yapıştırın.

    f.  **Tamam**’a tıklayın.

7. Aşağıdaki adımları uygulayın:

    ![SSO yapılandırması](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO yapılandırması")

    a.  **Hizmet sağlayıcı kimliği** metin kutusuna **http://www.workday.com** yazın.

    b. **SP tarafından başlatılan kimlik doğrulama Isteğini söndür**' ı seçin.

    c. **Kimlik doğrulama Isteği Imza yöntemi**olarak **SHA256**' yi seçin.

    ![Kimlik doğrulama Isteği Imza yöntemi](./media/workday-tutorial/WorkdaySSOConfiguration.png "Kimlik doğrulama Isteği Imza yöntemi") 

    d. **Tamam**’a tıklayın.

    ![Tamam](./media/workday-tutorial/IC782933.png "TAMAM")

    > [!NOTE]
    > Lütfen bir çoklu oturum açmayı doğru şekilde ayarlamış olduğunuzdan emin olun. Yanlış kurulumla çoklu oturum açmayı etkinleştirdiğinizde, uygulamayı kimlik bilgilerinizle giremeyebilirsiniz ve kilitlenmeyebilirsiniz. Bu durumda, Workday, kullanıcıların normal Kullanıcı adı ve parolasını şu biçimde kullanarak oturum açabilbileceği bir yedekleme günlüğü URL 'si sağlar: [Workday URL 'SI]/Login.Flex? Redirect = n

### <a name="create-workday-test-user"></a>Workday test kullanıcısı oluşturma

Bu bölümde, Workday 'de B. Simon adlı bir Kullanıcı oluşturacaksınız. Workday platformunda kullanıcıları eklemek için [Workday istemci destek ekibi](https://www.workday.com/en-us/partners-services/services/support.html) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Workday kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Workday 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Workday 'i deneyin](https://aad.portal.azure.com)
