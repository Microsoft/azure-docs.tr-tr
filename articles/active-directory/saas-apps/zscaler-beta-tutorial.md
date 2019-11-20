---
title: 'Öğretici: Zscaler Beta ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Zscaler Beta arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68943282"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Öğretici: Zscaler Beta ile tümleştirme Azure Active Directory

Bu öğreticide, Zscaler Beta Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Zscaler Beta 'yı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Zscaler Beta 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler Beta sürümünde otomatik olarak oturum açmaya izin verin. Bu erişim denetimine çoklu oturum açma (SSO) adı verilir.
* Azure portal kullanarak hesaplarınızı tek bir merkezi konumda yönetin.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Zscaler Beta ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma kullanan bir Zscaler Beta aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Zscaler Beta, SP tarafından başlatılan SSO 'yu destekler.
* Zscaler Beta tam zamanında Kullanıcı sağlamayı destekler.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Azure Marketi 'nden Zscaler Beta ekleme

Zscaler Beta tümleştirmesini Azure AD 'ye yapılandırmak için, Azure Marketi 'nden, yönetilen SaaS uygulamaları listenize Zscaler Beta ekleyin.

Azure Marketi 'nden Zscaler Beta eklemek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com), sol gezinti bölmesinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için iletişim kutusunun üst kısmındaki **Yeni uygulama** ' yı seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Zscaler Beta**yazın. Sonuç panelinden **Zscaler Beta** ' yı seçin ve ardından **Ekle**' yi seçin.

     ![Sonuçlar listesinde Zscaler Beta](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açma 'yı, Zscaler Beta ile birlikte kullanarak, Britta Simon test kullanıcısına göre yapılandırır ve test edersiniz.
Çoklu oturum açma için, Zscaler Beta sürümünde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturun.

Azure AD çoklu oturum açma 'yı Zscaler Beta ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

- Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on) .
- Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [Zscaler Beta çoklu oturum açmayı yapılandırın](#configure-zscaler-beta-single-sign-on) .
- Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
- Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
- [Bir Zscaler Beta test kullanıcısı oluşturun](#create-a-zscaler-beta-test-user) Zscaler Beta sürümünde kullanıcının Azure AD gösterimine bağlanmış Britta Simon 'un bir karşılığı.
- Yapılandırmanın çalışıp çalışmadığını doğrulamak için [Çoklu oturum açmayı test](#test-single-sign-on) edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı Zscaler Beta ile yapılandırmak için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Zscaler Beta** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. **Çoklu oturum açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** ' yi seçin.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde bu adımı izleyin:

    ![Zscaler Beta etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-intiated.png)

    - **Oturum açma URL 'si** kutusuna kullanıcılarınız tarafından Zscaler Beta uygulamanızda oturum açmak için kullanılan URL 'yi girin.

    > [!NOTE]
    > Değer gerçek değildir. Değeri, gerçek oturum açma URL 'SI değeriyle güncelleştirin. Değeri almak için [Zscaler Beta istemci destek ekibine](https://www.zscaler.com/company/contact)başvurun.

5. Zscaler Beta uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemeniz gerekir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** ' yi seçin.

    ![Kullanıcı öznitelikleri iletişim kutusu](common/edit-attribute.png)

6. Zscaler Beta uygulaması, daha az sayıda özniteliğin SAML yanıtına geri geçirilmesini bekliyor. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliğini eklemek için bu adımları izleyin.
    
    | Adı | Kaynak özniteliği | 
    | ---------------| --------------- |
    | Üyesi  | Kullanıcı. atandroles |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' yi seçin.

    ![Kullanıcı talepleri iletişim kutusu](common/new-save-attribute.png)

    ![Kullanıcı taleplerini Yönet iletişim kutusu](common/new-attribute-details.png)

    b. **Ad** kutusuna, bu satır için gösterilen öznitelik adını girin.

    c. **Ad alanı** kutusunu boş bırakın.

    d. **Kaynak**için **öznitelik**' i seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini girin.

    f. **Tamam**’ı seçin.

    g. **Kaydet**’i seçin.

    > [!NOTE]
    > Azure AD 'de rolleri yapılandırma hakkında bilgi edinmek için bkz. [rol talebini yapılandırma](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, sertifikayı indirmek için **Indir** ' i seçin **(base64)** . Bu dosyayı bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

8. **Zscaler Beta 'Yı ayarla** bölümünde, gereksinimleriniz Için gereken URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    - Oturum açma URL 'SI
    - Azure AD tanımlayıcısı
    - Oturum kapatma URL 'SI

### <a name="configure-zscaler-beta-single-sign-on"></a>Zscaler Beta çoklu oturum açmayı yapılandırma

1. Zscaler Beta sürümünde yapılandırmayı otomatikleştirmek için, **uzantıyı yükler**' i seçerek **uygulamalarım güvenli oturum açma tarayıcı uzantısını** yükler.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Uzantıyı tarayıcıya ekledikten sonra, **set up Zscaler Beta** ' yı seçtiğinizde sizi Zscaler Beta uygulamasına yönlendirir. Buradan, Zscaler Beta sürümünde oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı uygulamayı sizin için otomatik olarak yapılandırır ve 3 ile 6 arasındaki adımları otomatikleştirir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Zscaler Beta 'yı el ile ayarlamak için yeni bir Web tarayıcısı penceresi açın. Zscaler Beta şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları izleyin.

4. **Yönetim**  > **kimlik doğrulaması**  > **kimlik doğrulama ayarları**' na gidin ve aşağıdaki adımları izleyin.
   
    ![Yönetimine](./media/zscaler-beta-tutorial/ic800206.png "Yönetim")

    a. **Kimlik doğrulama türü**altında **SAML**' yi seçin.

    b. **SAML Yapılandır**' ı seçin.

5. **SAML Düzenle** penceresinde aşağıdaki adımları izleyin: 
            
    ![Kullanıcı & kimlik doğrulaması yönetme](./media/zscaler-beta-tutorial/ic800208.png "Kullanıcı & kimlik doğrulaması yönetme")
    
    a. **SAML portalı URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    b. **Oturum açma adı öznitelik** kutusuna **NameID**girin.

    c. **Ortak SSL sertifikası** kutusunda, Azure Portal INDIRDIĞINIZ Azure SAML imzalama sertifikasını karşıya yüklemek Için **karşıya yükle** ' yi seçin.

    d. **SAML otomatik sağlamayı etkinleştir**' i açın.

    e. **Kullanıcı görünen adı öznitelik** kutusunda, DisplayName ÖZNITELIKLERI için SAML oto sağlamayı etkinleştirmek istiyorsanız **DisplayName** yazın.

    f. **Grup adı öznitelik** kutusunda, memberOf ÖZNITELIKLERI için SAML oto sağlamayı etkinleştirmek Istiyorsanız, **memberOf** yazın.

    g. Bölüm **adı öznitelik** kutusuna departman ÖZNITELIKLERI için SAML oto sağlamayı etkinleştirmek istiyorsanız **bölüm** girin.

    h. **Kaydet**’i seçin.

6. **Kullanıcı kimlik doğrulamasını Yapılandır** iletişim sayfasında, aşağıdaki adımları izleyin:

    ![Etkinleştirme menüsü ve Etkinleştir düğmesi](./media/zscaler-beta-tutorial/ic800207.png)

    a. Sol alt taraftaki **etkinleştirme** menüsünün üzerine gelin.

    b. **Etkinleştir**' i seçin.

## <a name="configure-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma
Internet Explorer 'da proxy ayarlarını yapılandırmak için aşağıdaki adımları izleyin.

1. **Internet Explorer 'ı**başlatın.

2. **Internet seçenekleri** iletişim kutusunu açmak için **Araçlar** menüsünde **Internet seçenekleri** ' ni seçin. 
    
     ![Internet Seçenekleri iletişim kutusu](./media/zscaler-beta-tutorial/ic769492.png "Internet seçenekleri")

3. **Bağlantılar** sekmesini seçin. 
  
     ![Bağlantılar sekmesi](./media/zscaler-beta-tutorial/ic769493.png "Bağlantılar")

4. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu açmak için **LAN ayarları** ' nı seçin.

5. **Proxy sunucusu** bölümünde, şu adımları izleyin: 
   
    ![Proxy sunucusu bölümü](./media/zscaler-beta-tutorial/ic769494.png "Proxy sunucusu")

    a. **LAN için bir proxy sunucusu kullan** onay kutusunu seçin.

    b. **Adres** kutusuna **ağ geçidi girin. Zscaler Beta.net**.

    c. **Bağlantı noktası** kutusuna **80**girin.

    d. **Yerel adresler için proxy sunucusunu atla** onay kutusunu seçin.

    e. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu kapatmak için **Tamam ' ı** seçin.

6. **Internet seçenekleri** iletişim kutusunu kapatmak için **Tamam ' ı** seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Azure portal Britta Simon adlı bir test kullanıcısı oluşturun.

1. Azure portal sol bölmedeki  > **kullanıcılar**  > **tüm kullanıcılar**' ı **Azure Active Directory** seçin.

    ![Kullanıcılar ve tüm kullanıcılar bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. **Kullanıcı** iletişim kutusunda aşağıdaki adımları izleyin:

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** kutusuna **Brittasıon**yazın.
  
    b. **Kullanıcı adı** kutusuna `brittasimon@yourcompanydomain.extension` girin. BrittaSimon@contoso.com bunun bir örneğidir.

    c. **Parolayı göster** onay kutusunu seçin. **Parola** kutusunda görüntülenen değeri yazın.

    d. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Zscaler Beta sürümüne erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal,**tüm uygulamalar**  > **Zscaler Beta**'  >  **Kurumsal uygulamalar** ' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Zscaler Beta**' yı girin ve seçin.

    ![Uygulamalar listesinde Zscaler Beta bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcılar ve gruplar bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin. **Atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcı Ekle düğmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda listeden **Britta Simon** gibi bir kullanıcı seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.

    ![Kullanıcılar ve gruplar iletişim kutusu](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. **Rol Seç** iletişim kutusunda, listeden uygun Kullanıcı rolünü seçin. Ardından ekranın alt kısmında **Seç** ' i seçin.

    ![Rol Seç iletişim kutusu](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

    ![Atama Ekle iletişim kutusu](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Zscaler Beta test kullanıcısı oluşturma

Bu bölümde, Cetta Simon kullanıcısı Zscaler Beta sürümünde oluşturulur. Zscaler Beta, varsayılan olarak etkinleştirilen **tam zamanında Kullanıcı sağlamayı**destekler. Bu bölümde yapmanız için bir şey yoktur. Zscaler Beta sürümünde bir kullanıcı zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>Bir kullanıcıyı el ile oluşturmak için [Zscaler Beta destek ekibine](https://www.zscaler.com/company/contact)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde Zscaler Beta kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Zscaler Beta sürümünde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

