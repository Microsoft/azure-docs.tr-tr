---
title: 'Öğretici: Adobe Experience Manager ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Adobe Experience Manager arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154107"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Öğretici: Adobe Experience Manager ile tümleştirme Azure Active Directory

Bu öğreticide, Adobe Experience Manager 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Adobe Experience Manager 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Adobe Experience Manager 'a erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak Adobe Experience Manager 'da (çoklu oturum açma) oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Adobe Experience Manager ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Adobe Experience Manager çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Adobe Experience Manager **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

* Adobe Experience Manager **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Galeriden Adobe Experience Manager ekleme

Adobe Experience Manager Tümleştirmesini Azure AD ile yapılandırmak için galerideki Adobe Experience Manager 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Adobe Experience Manager eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Adobe Experience Manager**yazın, sonuç panelinden **Adobe Experience Manager** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde Adobe Experience Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına bağlı olarak [uygulama adı] ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve [uygulama adı] içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı [uygulama adı] ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Adobe Experience Manager çoklu oturum açmayı yapılandırın](#configure-adobe-experience-manager-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Adobe Experience Manager **[test kullanıcısı oluşturun](#create-adobe-experience-manager-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Adobe Experience Manager 'Da Britta Simon 'a sahip olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı [uygulama adı] ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Adobe Experience Manager** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Adobe Experience Manager etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, AEM sunucunuzda de tanımladığınız benzersiz bir değer yazın.

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Yanıt URL 'SI değerini gerçek yanıt URL 'siyle güncelleştirin. Bu değeri almak için, bu değeri almak üzere [Adobe Experience Manager istemci destek ekibine](https://helpx.adobe.com/support/experience-manager.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Adobe Experience Manager etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusuna Adobe Experience Manager sunucu URL 'nizi yazın.

6. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Adobe Experience Manager 'ı ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Adobe Experience Manager çoklu oturum açmayı yapılandırma

1. Başka bir tarayıcı penceresinde **Adobe Experience Manager** Yönetici portalı ' nı açın.

2. **Ayarlar** > **güvenlik** > **Kullanıcılar**' ı seçin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. **Yönetici** veya diğer ilgili kullanıcıları seçin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. **TrustStore ' u yönetmek** > **Hesap ayarları** ' nı seçin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. **Cer dosyasından sertifika ekle**' nin altında, **sertifika dosyası seç**' e tıklayın. Azure portal zaten indirdiğiniz sertifika dosyasına gidin ve seçin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Sertifika, TrustStore 'a eklenir. Sertifikanın diğer adını aklınızda edin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. **Kullanıcılar** sayfasında **kimlik doğrulama-hizmet**' i seçin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. **Anahtar deposu oluştur/yönet** > **Hesap ayarları** ' nı seçin. Bir parola sağlayarak anahtar deposu oluşturun.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Yönetici ekranına geri dönün. Ardından **ayarlar** > **Işlemler** > **Web Konsolu**' nu seçin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Bu, yapılandırma sayfasını açar.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. **Adobe Granite SAML 2,0 kimlik doğrulama işleyicisini**bulun. Sonra **Ekle** simgesini seçin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Bu sayfada aşağıdaki eylemleri gerçekleştirin.

    ![Çoklu oturum açma Kaydet düğmesini yapılandırma](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. **Yol** kutusuna **/** girin.

    b. **IDP URL 'si** kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini girin.

    c. **IDP sertifika diğer adı** kutusuna truststore ' da eklediğiniz **sertifika diğer adı** değerini girin.

    d. Güvenlik tarafından **sağlanmış VARLıK kimliği** kutusuna, Azure Portal yapılandırdığınız benzersiz **Azure AD tanımlayıcı** değerini girin.

    e. **Onaylama tüketici hizmeti URL 'si** kutusuna, Azure Portal yapılandırdığınız **yanıt URL 'si** değerini girin.

    f. **Anahtar deposu parolası** kutusuna keystore 'Da ayarladığınız **parolayı** girin.

    g. **Kullanıcı ÖZNITELIĞI kimliği** kutusuna, büyük/küçük harfe uygun **ad kimliğini** veya başka bir kullanıcı kimliğini girin.

    h. **CRX kullanıcılarını oto oluştur**' u seçin.

    i. **Oturum kapatma URL 'si** kutusuna, Azure Portal aldığınız benzersiz **oturum kapatma URL 'si** değerini girin.

    j. **Kaydet**’i seçin.

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

Bu bölümde, Adobe Experience Manager 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Adobe deneyim Yöneticisi**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Adobe Experience Manager**' ı seçin.

    ![Uygulamalar listesindeki Adobe Experience Manager bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager test kullanıcısı oluştur

Bu bölümde, Adobe Experience Manager 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. **Otomatik oluştur CRX kullanıcıları** seçeneğini belirlediyseniz, kullanıcılar başarıyla kimlik doğrulamasından sonra otomatik olarak oluşturulur.

Kullanıcıları el ile oluşturmak istiyorsanız, Adobe Experience Manager platformunda kullanıcıları eklemek için [Adobe Experience Manager Destek ekibi](https://helpx.adobe.com/support/experience-manager.html) ile çalışın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Adobe deneyim Yöneticisi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Adobe Experience Manager 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
