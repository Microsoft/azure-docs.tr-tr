---
title: 'Öğretici: Dmarcian ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile dmarcıa arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823704"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Öğretici: Dmarcıa 'yu Azure Active Directory ile tümleştirme

Bu öğreticide, dmarcıa 'yı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Dmarcıa 'yı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de dmarcıa 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla dmarcıa 'ya otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* dmarcian çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* dmarcıa **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-dmarcian-from-the-gallery"></a>Galeriden dmarcıa ekleme

Dmarcıa 'nın tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden dmarcıa 'yı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **dmarcıa** yazın.
1. Sonuçlar panelinden **dmarcıa** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

**B. Simon**adlı bir test kullanıcısı kullanarak dmarcıa Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, dmarcıa 'da bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Dmarcıa ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. **[Dmarcıa SSO](#configure-dmarcian-sso)** 'yu, uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için yapılandırın.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. Dmarcıa **[test kullanıcısı oluşturun](#create-dmarcian-test-user)** ; bu, kullanıcının Azure AD gösterimine bağlı olan dmarcıa 'da B. Simon 'ın bir karşılığı olacak.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **dmarcıa** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Bu değerler gerçek değildir. Bu değerleri, öğreticide daha sonra açıklanan gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleşceksiniz.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Dmarcia SSO 'yu yapılandırma

1. Dmarcıa içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek**üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **dmarcıa** ' ya tıklayarak, sizi dmarcıa uygulamasına yönlendirirsiniz. Buradan, dmarcıa 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Dmarcıa 'yı el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve dmarcıa şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Sağ üst köşedeki **profil** ' e tıklayın ve **Tercihler**' e gidin.

    ![Tercihler](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Aşağı kaydırın ve **Çoklu oturum** açma bölümüne tıkladıktan sonra **Yapılandır**' a tıklayın.

    ![Tek](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. **SAML çoklu oturum açma** sayfasında, **durumu** **etkin** olarak ayarlayın ve aşağıdaki adımları gerçekleştirin:

    ![Kimlik doğrulaması](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * **Kimlik sağlayıcınızda dmarcıa ekleyin** bölümünde **Kopyala** ' ya tıklayarak örneğiniz için **onaylama tüketici hizmeti URL 'SINI** kopyalayın ve Azure Portal **temel SAML yapılandırması bölümündeki** **yanıt URL 'si** metin kutusuna yapıştırın.

    * **Kimlik sağlayıcınıza dmarcıa ekleyin** bölümünde **Kopyala** ' ya tıklayarak örneğinizin **varlık KIMLIĞINI** kopyalayın ve Azure Portal **temel SAML yapılandırması bölümünde** **tanımlayıcı** metin kutusuna yapıştırın.

    * **Kimlik doğrulamasını ayarla** bölümünde, **kimlik sağlayıcısı meta verileri** metin kutusuna, Azure Portal kopyaladığınız **uygulama Federasyon meta verileri URL**'sini yapıştırın.

    * **Kimlik doğrulamasını ayarla** bölümünde, **öznitelik deyimleri** metin kutusuna URL 'yi yapıştırın`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * **Oturum açma URL 'Sini ayarla** bölümünde, örneğiniz Için **oturum açma** url 'Sini KOPYALAYıN ve Azure Portal **temel SAML yapılandırması bölümünde** **oturum açma URL 'si** metin kutusuna yapıştırın.

        > [!Note]
        > **Oturum açma URL 'sini** kuruluşunuza göre değiştirebilirsiniz.

    * **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, dmarcıa 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **dmarcıa**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-dmarcian-test-user"></a>Dmarcıa test kullanıcısı oluşturma

Azure AD kullanıcılarının dmarcıa 'da oturum açmasını sağlamak için, dmarcıa 'ya sağlanması gerekir. Dmarcıa 'da, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Dmarcıa 'da güvenlik yöneticisi olarak oturum açın.

2. Sağ üst köşedeki **profile** tıklayın ve **Kullanıcıları Yönet**' e gidin.

    ![Kullanıcı](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. **SSO kullanıcıları** bölümünün sağ tarafında **Yeni Kullanıcı Ekle**' ye tıklayın.

    ![Kullanıcı Ekle](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. **Yeni Kullanıcı Ekle** açılır penceresinde aşağıdaki adımları uygulayın:

    ![Yeni Kullanıcı](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. **Yeni Kullanıcı e-posta** metin kutusunda, kullanıcının e-postasını, **brittasıon\@contoso.com**gibi girin.

    b. Kullanıcıya yönetici hakları vermek istiyorsanız, **kullanıcıyı yönetici yap**' ı seçin.

    c. Tıklayın **kullanıcı ekleme**.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde dmarcia kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız dmarcıa 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

