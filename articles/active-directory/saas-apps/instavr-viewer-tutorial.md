---
title: Öğretici:% ınvr Görüntüleyicisi ile tümleştirme Azure Active Directory | Microsoft Docs
description: Azure Active Directory ve ınvr Görüntüleyici arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de94f83c260a86f313a2dd04cdd5a7ae8fc1cda
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73155319"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Öğretici:% ınvr görüntüleyicisiyle Azure Active Directory tümleştirme

Bu öğreticide,% Azure Active Directory (Azure AD) ile bir ınvr görüntüleyicisini tümleştirmeyi öğreneceksiniz.
Azure AD ile bir sistem için Kararsızvr görüntüleyicisini tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, ınınvr görüntüleyicisine erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla% ınvr görüntüleyicisine (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini bir ıngıvr görüntüleyicisine göre yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* % Invr Görüntüleyicisi çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Inınvr Görüntüleyicisi **SP** tarafından başlatılan SSO 'yu destekler
* % Inınvr Görüntüleyicisi **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-instavr-viewer-from-the-gallery"></a>Galeriden bir sistem sunucusundan Kararsızvr görüntüleyicisini ekleme

% Inınvr görüntüleyicisinin Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize bir sunucudan% ınvr görüntüleyicisini eklemeniz gerekir.

**Galeriden ınınvr görüntüleyicisini eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, bir uygulama eklemek için, sonuç panelinden **bir sistem günlüğü** **görüntüleyicisini** seçin, ardından **Ekle** düğmesine tıklayın.

     ![Sonuçlar listesinde Sistem günlüğü Görüntüleyicisi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon**adlı bir test kullanıcısına dayalı olarak, Azure AD çoklu oturum açma 'yı, ınsıvr görüntüleyiciyle yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı,% ınvr Görüntüleyicisi ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında bir çoklu oturum açma ayarlarını yapılandırmak için,% **[ınvr görüntüleyicisini çoklu oturum açmayı yapılandırın](#configure-instavr-viewer-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcı Azure AD gösterimine bağlı olan bir ınsıvr görüntüleyicisinde Britta Simon 'a sahip olmak için bir ınsıs **[Görüntüleyici test kullanıcısı oluşturun](#create-instavr-viewer-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı,% ınvr görüntüleyicisinde yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Bu uygulama tümleştirme sayfasında,** **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![% Invr Görüntüleyicisi etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Oturum açma URL 'SI için sabit bir model yok. Bu, bir ınınsıs Görüntüleyici müşterisi Web paketlemesi yaparken oluşturulur. Her müşteri ve paket için benzersizdir. Tam oturum açma URL 'sini almak için, excepvr Görüntüleyici Örneğinizde oturum açmanız ve Web paketlemesi yapmanız gerekir.

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Tanımlayıcı değeri gerçek değil. Bu değeri, Bu öğreticinin ilerleyen kısımlarında açıklanan gerçek tanımlayıcı değeriyle güncelleştirin.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ve **Federasyon meta verileri dosyasını** , gereksiniminize göre verilen seçeneklerden indirmek için **İndir** ' e tıklayın ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/metadata-certificatebase64.png)

6. En uygun URL 'leri **Ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-instavr-viewer-single-sign-on"></a>% Invr görüntüleyicisini çoklu oturum açmayı yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve bir yönetici olarak,% ınvr görüntüleyicinizin şirket sitesinde oturum açın.

2. **Kullanıcı simgesine** tıklayın ve **Hesap**' ı seçin.

    ![% Invr Görüntüleyici yapılandırması](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. **SAML kimlik doğrulaması** ' na gidin ve aşağıdaki adımları gerçekleştirin:

    ![% Invr Görüntüleyici yapılandırması](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. **SSO URL** metin kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Logout URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    c. **VARLıK kimliği** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcı** değerini yapıştırın.

    d. İndirilen sertifika dosyanızı karşıya yüklemek için **Güncelleştir**' e tıklayın.

    e. İndirilen Federasyon meta veri dosyanızı karşıya yüklemek için **Güncelleştir**' e tıklayın.

    f. **VARLıK kimliği** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak ınsıvr görüntüleyicisine erişim izni vererek Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra da **Sistem günlüğü Görüntüleyicisi**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, yazın ve ardından bir **Sistem günlüğü görüntüleyicisini**seçin.

    ![Uygulamalar listesindeki bir sistem günlüğü Görüntüleyicisi bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-instavr-viewer-test-user"></a>% Invr Görüntüleyici test kullanıcısı oluştur

Bu bölümde, ıntıtta Simon adlı bir Kullanıcı, ınvr görüntüleyicisinde oluşturulmuştur. Bu, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı, bir kullanıcı için zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur. Herhangi bir sorun varsa, lütfen bu, lütfen [ınınvr Görüntüleyici destek ekibine](mailto:contact@instavr.co)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

1. Yeni bir Web tarayıcı penceresi açın ve bir yönetici olarak,% ınvr görüntüleyicinizin şirket sitesinde oturum açın.

2. Sol Gezinti panelinden **paket** ' i seçin ve **Web için paketi yap**' ı seçin.

    ![% Invr Görüntüleyici yapılandırması](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. **Download** (İndir) seçeneğini belirleyin.

    ![% Invr Görüntüleyici yapılandırması](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Oturum açmak için Azure AD 'ye yeniden yönlendirildikten sonra **barındırılan barındırılan sayfayı aç** ' ı seçin.

    ![% Invr Görüntüleyici yapılandırması](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Azure AD kimlik bilgilerinizi, SSO aracılığıyla Azure AD 'de başarıyla oturum açmak için girin.

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
