---
title: 'Öğretici: Riskware ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Riskware arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027106"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Öğretici: Riskware ile tümleştirme Azure Active Directory

Bu öğreticide, Riskware 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Riskware Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Riskware 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Riskware (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Riskware ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Riskware çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Riskware **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-riskware-from-the-gallery"></a>Galeriden Riskware ekleme

Riskware tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Riskware yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden Riskware eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Riskware**yazın, sonuç panelinden **Riskware** ' yi seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuç listesinde Riskware](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre Riskware ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Riskware 'deki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı Riskware ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Riskware çoklu oturum açmayı yapılandırın](#configure-riskware-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Riskware 'de Britta Simon 'ın bir karşılığı olacak şekilde **[Riskware test kullanıcısı oluşturun](#create-riskware-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Riskware ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Riskware** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Riskware etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:
    
    | Ortam| URL kalıbı|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | GRUBU| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | TANıTıM| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:
    
    | Ortam| URL kalıbı|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | GRUBU| `https://riskcloud.net/prod` |
    | TANıTıM| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Riskware istemci destek ekibine](mailto:support@pansoftware.com.au) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Riskware ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-riskware-single-sign-on"></a>Riskware çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Riskware şirket sitenizde yönetici olarak oturum açın.

1. Bakım sayfasını açmak için sağ üst köşedeki **bakım** ' a tıklayın.

    ![Riskware yapılandırma Bakımı](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Bakım sayfasında **kimlik doğrulama**' ya tıklayın.

    ![Riskware yapılandırması Authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. **Kimlik doğrulama yapılandırması** sayfasında, aşağıdaki adımları uygulayın:

    ![Riskware yapılandırması authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Kimlik doğrulaması için **SAML** olarak **tür** ' i seçin.

    b. **Kod** metın kutusuna AZURE_UAT gibi bir kod yazın.

    c. **Açıklama** metın kutusuna SSO Için Azure yapılandırması gibi bir açıklama yazın.

    d. **Çoklu oturum açma sayfası** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. **Oturumu Kapat sayfası** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    f. **Gönderi formu alanı** metin kutusuna, SAMLResponse gibi SAML Içeren Post yanıtında bulunan alan adını yazın

    g. **XML Identity Tag Name** metin kutusunda, NAMEıD gibi SAML yanıtında benzersiz tanımlayıcıyı içeren özniteliği yazın.

    h. İndirilen **meta veri XML** dosyasını Not defteri 'nde Azure Portal açın, meta veri dosyasından sertifikayı kopyalayın ve **sertifika** metin kutusuna yapıştırın

    i. **Tüketici URL 'si** metin kutusunda, destek ekibinden aldığınız **yanıt URL 'si**değerini yapıştırın.

    j. **Veren** metin kutusunda, destek ekibinden aldığınız **tanımlayıcının**değerini yapıştırın.

    > [!Note]
    > Bu değerleri almak için [Riskware istemci destek ekibine](mailto:support@pansoftware.com.au) başvurun

    k. **Gönderi kullan** onay kutusunu seçin.

    girişindeki. **SAML Isteği kullan** onay kutusunu seçin.

    m. **Kaydet** düğmesine tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alan türü ' nde `brittasimon@yourcompanydomain.extension`  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Riskware 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Riskware**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Riskware**' yi seçin.

    ![Uygulamalar listesindeki Riskware bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-riskware-test-user"></a>Riskware test kullanıcısı oluştur

Azure AD kullanıcılarının Riskware 'de oturum açmasını sağlamak için, Riskware ' a sağlanması gerekir. Riskware ' de, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Riskware 'de güvenlik yöneticisi olarak oturum açın.

1. Bakım sayfasını açmak için sağ üst köşedeki **bakım** ' a tıklayın. 

    ![Riskware yapılandırması korunur](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Bakım sayfasında, **kişiler**' e tıklayın.

    ![Riskware yapılandırma kişileri](./media/riskware-tutorial/tutorial_riskware_people.png)

1. **Ayrıntılar** sekmesini seçin ve aşağıdaki adımları gerçekleştirin:

    ![Riskware yapılandırma ayrıntıları](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Çalışan gibi **kişi türünü** seçin.

    b. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta**gibi girin.

    c. **Soyadı** metin kutusunda, **Simon**gibi kullanıcı adının soyadını girin.

1. **Güvenlik** sekmesinde, aşağıdaki adımları uygulayın:

    ![Riskware yapılandırma güvenliği](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. **Kimlik doğrulama** bölümünde, SSO Için Azure yapılandırması gibi kurulumunu yaptığınız **kimlik doğrulama** modunu seçin.

    b. **Oturum açma ayrıntıları** bölümünde, **Kullanıcı kimliği** metin kutusuna `brittasimon@contoso.com` gibi kullanıcının e-postasını girin.

    c. **Parola** metin kutusuna kullanıcının parolasını girin.

1. **Kuruluş** sekmesinde aşağıdaki adımları gerçekleştirin:

    ![Riskware Configuration org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. **Level1** organizasyonu seçeneğini belirleyin.

    b. **Kişinin birincil çalışma alanı** bölümünde **konum** metin kutusuna konumunuzu yazın.

    c. **Çalışan** bölümünde, normal gibi **çalışan durumu** ' nu seçin.

    d. **Kaydet** düğmesine tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Riskware kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Riskware için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
