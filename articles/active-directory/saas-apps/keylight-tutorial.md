---
title: 'Öğretici: LockPath Keylight ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile LockPath Keylight arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 81949736603d22cac779d08d14bd6db65065d730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459096"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Öğretici: LockPath Keylight ile tümleştirme Azure Active Directory

Bu öğreticide, LockPath Keylight 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
LockPath Keylight 'i Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de LockPath Keylight 'e erişimi olan denetimi yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla LockPath Keylight (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini LockPath Keylight ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* LockPath Keylight çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* LockPath Keylight, **SP** tarafından başlatılan SSO 'yu destekler
* LockPath Keylight **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Galeriden LockPath Keylight ekleme

LockPath Keylight 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden LockPath Keylight öğesini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden LockPath Keylight eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusunda, **LockPath Keylight** yazın, sonuç panelinden **LockPath Keylight** öğesini seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuçlar listesinde LockPath Keylight](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre LockPath Keylight ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve LockPath Keylight içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı LockPath Keylight ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Kilit yolunu yapılandırma Keylight çoklu oturum açma](#configure-lockpath-keylight-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan LockPath Keylight öğesinde Britta Simon 'ın bir karşılığı olacak şekilde **[LockPath Keylight test kullanıcısı oluşturun](#create-lockpath-keylight-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı LockPath Keylight ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **LockPath Keylight** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![LockPath Keylight etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.keylightgrc.com/`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.keylightgrc.com`

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [LockPath Keylight istemci destek ekibine](https://www.lockpath.com/contact/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (ham)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

6. **Kilit yolunu ayarla Keylight** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-lockpath-keylight-single-sign-on"></a>LockPath Keylight tek Sign-On yapılandırma

1. LockPath Keylight içinde SSO 'yu etkinleştirmek için aşağıdaki adımları uygulayın:

    a. Kilit yolu Keylight hesabınızda Yönetici olarak oturum açın.

    b. Üstteki menüde **kişi**' ye tıklayın ve **Keylight kurulum**' u seçin.

    !["Kişi" simgesinin seçili olduğunu ve açılan listeden "Keylight kurulum" seçeneğini gösteren ekran görüntüsü.](./media/keylight-tutorial/401.png)

    c. Soldaki TreeView 'da **SAML**' ye tıklayın.

    ![Ağaç görünümünde "S A M L" seçiliyken görüntülenen ekran görüntüsü.](./media/keylight-tutorial/402.png)

    d. **SAML ayarları** iletişim kutusunda, **Düzenle**' ye tıklayın.

    !["Düzenle" düğmesinin seçili olduğu "S A M L ayarları" penceresini gösteren ekran görüntüsü.](./media/keylight-tutorial/404.png)

1. **SAML ayarlarını Düzenle** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Tek Sign-On yapılandırma](./media/keylight-tutorial/405.png)

    a. **SAML kimlik doğrulamasını** **etkin** olarak ayarlayın.

    b. **Kimlik sağlayıcısı oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Kimlik sağlayıcısı oturum kapatma URL 'si** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    d. İndirilen LockPath Keylight sertifikanızı seçmek için **Dosya Seç** ' e tıklayın ve ardından sertifikayı karşıya yüklemek için **Aç** ' a tıklayın.

    e. **SAML Kullanıcı kimliği konumunu** **Konu bildiriminin NameIdentifier öğesine** ayarlayın.

    f. Aşağıdaki kalıbı kullanarak **Keylight hizmet sağlayıcısını** sağlayın: `https://<CompanyName>.keylightgrc.com` .

    örneğin: **Kullanıcıları otomatik sağlamayı** **etkin** olarak ayarlayın.

    h. **Otomatik sağlama hesap türünü** **tam Kullanıcı** olarak ayarlayın.

    i. **Otomatik sağlama güvenlik rolünü** AYARLAYıN, **SAML ile standart Kullanıcı**' yı seçin.

    j. **Otomatik sağlama güvenlik** yapılandırmasını ayarlayın, **Standart Kullanıcı Yapılandırması**' nı seçin.

    k. **E-posta özniteliği** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    l. **First Name öznitelik** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    m. **Soyadı özniteliği** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    n. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına yazın `brittasimon@yourcompanydomain.extension` . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, LockPath Keylight 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **LockPath Keylight**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **LockPath Keylight** öğesini seçin.

    ![Uygulamalar listesindeki LockPath Keylight bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-lockpath-keylight-test-user"></a>LockPath Keylight test kullanıcısı oluştur

Bu bölümde, LockPath Keylight içinde Britta Simon adlı bir Kullanıcı oluşturulur. LockPath Keylight, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı LockPath Keylight içinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur. Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [LockPath Keylight istemci destek ekibine](https://www.lockpath.com/contact/)başvurmanız gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde LockPath Keylight kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız LockPath Keylight öğesinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)