---
title: 'Öğretici: Zscaler One ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Zscaler arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: d23fc94fba9a9b750f2977fd9c5d8297e4da8d5c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68825906"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Öğretici: Zscaler One ile tümleştirme Azure Active Directory

Bu öğreticide, Zscaler 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Zscaler 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Zscaler 'ya erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler 'da (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Zscaler ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Zscaler tek bir oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Zscaler, **SP** tarafından başlatılan SSO 'yu destekler

* Zscaler bir **kez yalnızca zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-zscaler-one-from-the-gallery"></a>Galeriden Zscaler ekleme

Zscaler 'nun bir Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden Zscaler ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Zscaler eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Zscaler One**yazın, sonuç panelinden **Zscaler** ' ı seçin ve sonra uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuç listesinde Zscaler](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre Zscaler ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Zscaler içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı Zscaler ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Zscaler 'ı bir çoklu oturum açma yapılandırması](#configure-zscaler-one-single-sign-on)** , uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Zscaler bir test kullanıcısı oluşturun](#create-zscaler-one-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan Zscaler 'Da Britta Simon 'ın bir karşılığı olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Zscaler One ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Zscaler** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Zscaler bir etki alanı ve URL çoklu oturum açma bilgileri](common/sp-signonurl.png)

    Oturum açma **URL 'si** metin kutusuna kullanıcılarınız tarafından, Zscaler uygulamanızda oturum açmak için kullanılan URL 'yi yazın.

    > [!NOTE]
    > Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Zscaler istemci destek ekibine](https://www.zscaler.com/company/contact) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. Zscaler uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![image](common/edit-attribute.png)

6. Zscaler, yukarıdakine ek olarak, SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:
    
    | Adı | Kaynak özniteliği |
    | ---------| ------------ |
    | Üyesi     | Kullanıcı. atandroles |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.
    
    f. **Kaydet** düğmesine tıklayın.

    > [!NOTE]
    > Azure AD 'de rolün nasıl yapılandırılacağını öğrenmek için lütfen [buraya](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) tıklayın

7. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

8. **Zscaler 'Yı ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-zscaler-one-single-sign-on"></a>Zscaler tek bir oturum açmayı yapılandırma

1. Zscaler 'daki yapılandırmayı otomatikleştirebilmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, bu uygulamayı bir tane, tek bir uygulamaya yönlendirecek şekilde **ayarlayın** . Buradan, Zscaler 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Yükleme SSO 'su](common/setup-sso.png)

3. Zscaler 'ı bir el ile kurmak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Zscaler şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. **Yönetim > kimlik doğrulaması > kimlik doğrulama ayarları** ' na gidin ve aşağıdaki adımları gerçekleştirin:
   
    ![Yönetimine](./media/zscaler-one-tutorial/ic800206.png "Yönetim")

    a. Kimlik doğrulama türü altında **SAML**' yi seçin.

    b. **SAML Yapılandır**öğesine tıklayın.

5. **SAML Düzenle** penceresinde, aşağıdaki adımları uygulayın: ve Kaydet ' e tıklayın.  
            
    ![Kullanıcı & kimlik doğrulaması yönetme](./media/zscaler-one-tutorial/ic800208.png "Kullanıcı & kimlik doğrulaması yönetme")
    
    a. **SAML PORTALı URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    b. **Oturum açma adı öznitelik** metin kutusuna **NameID**girin.

    c. **Ortak SSL sertifikasındaki**Azure Portal adresinden INDIRDIĞINIZ Azure SAML imzalama sertifikasını karşıya yüklemek Için **karşıya yükle**' ye tıklayın.

    d. **SAML otomatik sağlamayı etkinleştir**' i açın.

    e. **Kullanıcı görünen adı öznitelik** metin kutusunda, DisplayName ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **DisplayName** yazın.

    f. **Grup adı öznitelik** metin kutusunda, memberOf ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek Istiyorsanız, **memberOf** yazın.

    g. Departman **adı özniteliğinde** departman ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **departmanı** girin.

    h. **Kaydet** düğmesine tıklayın.

6. **Kullanıcı kimlik doğrulamasını Yapılandır** iletişim sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Yönetim](./media/zscaler-one-tutorial/ic800207.png)

    a. Sol alt kısımdaki **etkinleştirme** menüsünün üzerine gelin.

    b. **Etkinleştir**' e tıklayın.

## <a name="configuring-proxy-settings"></a>Proxy ayarlarını yapılandırma
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer 'da proxy ayarlarını yapılandırmak için

1. **Internet Explorer 'ı**başlatın.

2. **Internet** seçenekleri iletişim kutusunu açmak için **Araçlar** menüsünden **Internet seçenekleri** ' ni seçin.   
    
     ![Internet seçenekleri](./media/zscaler-one-tutorial/ic769492.png "Internet seçenekleri")

3. **Bağlantılar** sekmesine tıklayın.   
  
     ![Bağlantılar](./media/zscaler-one-tutorial/ic769493.png "Bağlantılar")

4. **LAN ayarları iletişim kutusunu** açmak için **LAN ayarları** ' na tıklayın.

5. Proxy sunucusu bölümünde aşağıdaki adımları uygulayın:   
   
    ![Proxy sunucusu](./media/zscaler-one-tutorial/ic769494.png "Proxy sunucusu")

    a. **LAN için bir proxy sunucusu kullan**' ı seçin.

    b. Adres metin kutusuna **ağ geçidi yazın. Zscaler One.net**.

    c. Bağlantı noktası metin kutusuna **80**yazın.

    d. **Yerel adresler için proxy sunucusunu atla**' yı seçin.

    e. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

7. **Internet seçenekleri** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanına brittasimon@yourcompanydomain.extension yazın. Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Zscaler 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Zscaler birini**seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Zscaler One**öğesini seçin.

    ![Uygulamalar listesinde Zscaler bir bağlantı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda listeden **Britta Simon** gibi bir kullanıcı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_users.png)

6. **Rol Seç** iletişim kutusunda listeden uygun Kullanıcı rolünü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_roles.png)

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_assign.png)

### <a name="create-zscaler-one-test-user"></a>Zscaler bir test kullanıcısı oluşturma

Bu bölümde, Zscaler 'da Britta Simon adlı bir Kullanıcı oluşturulur. Zscaler bir, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Zscaler 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa, [Zscaler One destek ekibine](https://www.zscaler.com/company/contact)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Zscaler kutucuğunu tıklattığınızda, SSO 'yu ayarladığınız Zscaler 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

