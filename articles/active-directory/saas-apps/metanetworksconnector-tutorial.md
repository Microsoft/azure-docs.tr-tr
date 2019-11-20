---
title: 'Öğretici: meta ağlar Bağlayıcısı ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve meta ağları bağlayıcı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: a09eda25e8c7cc087770210cdfbe7e2bc9832acf
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160648"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Öğretici: meta ağlar Bağlayıcısı ile Azure Active Directory tümleştirme

Bu öğreticide, meta Networks bağlayıcısını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Meta Networks bağlayıcısını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de meta ağları bağlayıcıya erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla meta ağlar Bağlayıcısı 'nda (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini meta Networks Bağlayıcısı ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Meta Networks Bağlayıcısı çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Meta Networks Bağlayıcısı **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler
 
* Meta Networks Bağlayıcısı **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Galeriden meta Networks Bağlayıcısı ekleme

Meta Networks bağlayıcısının Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden meta ağlar bağlayıcısını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden meta ağlar Bağlayıcısı eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **meta ağlar Bağlayıcısı**yazın, sonuç panelinden **meta ağlar Bağlayıcısı** ' nı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuçlar listesindeki meta ağlar Bağlayıcısı](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına bağlı olarak meta ağlar bağlayıcısıyla yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve meta ağlar bağlayıcısında ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı meta Networks Bağlayıcısı ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Meta Networks bağlayıcısını yapılandırma çoklu](#configure-meta-networks-connector-single-sign-on)** oturum açma, uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan meta ağlar bağlayıcısında Britta Simon 'a sahip olmak için **[meta Networks Bağlayıcısı test kullanıcısı oluşturun](#create-meta-networks-connector-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı meta Networks Bağlayıcısı ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **meta ağlar Bağlayıcısı** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Meta Networks bağlayıcı etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Meta Networks bağlayıcı etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/both-advanced-urls.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. **Geçiş durumu** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirmek Öğreticinin ilerleyen kısımlarında açıklanmıştır.

6. Meta Networks bağlayıcı uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![image](common/edit-attribute.png)
    
7. Meta Networks bağlayıcı uygulaması, yukarıdakine ek olarak, SAML yanıtına daha fazla öznitelik geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:
    
    | Adı | Kaynak özniteliği | uzayına|
    | ---------------| --------------- | -------- |
    | FirstName | Kullanıcı. | |
    | Soyadı | User. soyadı | |
    | EmailAddress| Kullanıcı. Mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | ad | User. UserPrincipalName| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | Numarası | Kullanıcı. telephoneNumber | |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    g. **Kaydet** düğmesine tıklayın.

8. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

9. **Meta ağları ayarla Bağlayıcısı** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-meta-networks-connector-single-sign-on"></a>Meta Networks bağlayıcısını çoklu oturum açmayı yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve meta ağları bağlayıcı yönetici hesabınızda oturum açın.
    
    > [!NOTE]
    > Meta Networks Bağlayıcısı, güvenli bir sistemdir. Bu nedenle portala erişmeden önce, genel IP adresinizi kendi tarafında bir izin verilenler listesine eklemiş olmanız gerekir. Genel IP adresinizi almak için [burada](https://whatismyipaddress.com/)belirtilen aşağıdaki bağlantıyı izleyin. IP adresinizi bir izin verilenler listesine eklemek için, IP adresinizi [meta Networks Bağlayıcısı istemci destek ekibine](mailto:support@metanetworks.com) gönderin.
    
2. **Yönetici** ' ye gidin ve **Ayarlar**' ı seçin.
    
    ![Çoklu oturum açmayı yapılandırma](./media/metanetworksconnector-tutorial/configure3.png)
    
3. **Internet trafiğini günlüğe kaydet** ve **VPN MFA 'yı zorla** olarak ayarlandığından emin olun.
    
    ![Çoklu oturum açmayı yapılandırma](./media/metanetworksconnector-tutorial/configure1.png)
    
4. **Yönetici** ' ye gidin ve **SAML**' yi seçin.
    
    ![Çoklu oturum açmayı yapılandırma](./media/metanetworksconnector-tutorial/configure4.png)
    
5. **Ayrıntılar** sayfasında aşağıdaki adımları gerçekleştirin:
    
    ![Çoklu oturum açmayı yapılandırma](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. **SSO URL 'si** değerini kopyalayın ve **meta Networks Bağlayıcısı etki alanı ve URL 'Leri** bölümünde **oturum açma URL 'si** metin kutusuna yapıştırın.
    
    b. **Alıcı URL 'si** değerini kopyalayın ve **meta Networks Bağlayıcısı etki alanı ve URL 'leri** bölümündeki **yanıt URL 'si** metin kutusuna yapıştırın.
    
    c. **Hedef kitle URI 'si (SP VARLıK kimliği)** değerini kopyalayın ve **meta Networks bağlayıcı etki alanı ve URL 'leri** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.
    
    d. SAML 'yi etkinleştirme
    
6. **Genel** sekmesinde. aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırma](./media/metanetworksconnector-tutorial/configure5.png)

    a. **Kimlik sağlayıcısı çoklu oturum açma URL**'sinde, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Kimlik sağlayıcısı yayımcısı**' nda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. İndirilen sertifikayı not defteri 'nde Azure portal açın, **X. 509.952 Certificate** metin kutusuna yapıştırın.

    d. **Tam zamanında sağlamayı**etkinleştirin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**yazın.
  
    b. **Kullanıcı adı** alanında, **\@yourcompansıon** ' yazın.  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak meta Networks bağlayıcısına erişim izni vererek Britta Simon 'u etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **meta ağlar Bağlayıcısı**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **meta ağlar Bağlayıcısı**' nı seçin.

    ![Uygulamalar listesindeki meta ağları bağlayıcı bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-meta-networks-connector-test-user"></a>Meta Networks Bağlayıcısı test kullanıcısı oluştur

Bu bölümde, meta ağlar Bağlayıcısı 'nda Britta Simon adlı bir Kullanıcı oluşturulur. Meta ağlar Bağlayıcısı, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı meta Networks bağlayıcısında zaten mevcut değilse, meta ağları bağlayıcıya erişmeye çalıştığınızda yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa, [meta Networks Bağlayıcısı istemci destek ekibine](mailto:support@metanetworks.com)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde meta ağları bağlayıcı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız meta ağları bağlayıcıya otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

