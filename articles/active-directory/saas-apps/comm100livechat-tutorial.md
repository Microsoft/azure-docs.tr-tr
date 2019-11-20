---
title: 'Öğretici: Comm100 Canlı sohbetle tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Comm100 canlı sohbet arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91a7bf535a4afe0d46a13bbdc5643da7d5ce3334
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157334"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Öğretici: Comm100 Canlı sohbetle tümleştirme Azure Active Directory

Bu öğreticide, Comm100 Live Chat 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Comm100 Live Chat 'i Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Comm100 Live Chat 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Comm100 canlı sohbet (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Comm100 Canlı sohbetle yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Comm100 canlı sohbet çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Comm100 canlı sohbeti **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Galeriden Comm100 canlı sohbet ekleme

Comm100 Live Chat 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için galerideki Comm100 Live Chat 'i yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden Comm100 canlı sohbet eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Comm100 canlı sohbet**yazın, sonuç panelinden **Comm100 canlı sohbet** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

     ![Sonuç listesinde Comm100 canlı sohbet](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon**adlı bir test kullanıcısına göre Comm100 Canlı sohbetle Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Comm100 Live Chat içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Comm100 Canlı sohbetle Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Comm100 Live Chat çoklu oturum açmayı yapılandırın](#configure-comm100-live-chat-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Comm100 Live Chat 'te Britta Simon 'a sahip olmak için **[Comm100 Live Chat test kullanıcısı oluşturun](#create-comm100-live-chat-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Comm100 Canlı sohbetle yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Comm100 canlı sohbet** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Comm100 canlı sohbet etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Oturum açma URL 'SI değeri gerçek değil. Oturum açma URL 'si değerini, öğreticide daha sonra açıklanacak olan gerçek oturum açma URL 'SI ile güncelleştirirsiniz.

5. Comm100 canlı sohbet uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![image](common/edit-attribute.png)

6. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, yukarıdaki görüntüde gösterildiği gibi, **Düzen simgesini** kullanarak talepleri DÜZENLEYIN veya aşağıdaki resimde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** ' yi kullanarak talepleri ekleyin ve aşağıdaki adımları gerçekleştirin: 

    | Adı |  Kaynak özniteliği|
    | ---------------| --------------- |
    |   e-posta    | Kullanıcı. Mail |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    g. **Kaydet** düğmesine tıklayın.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Comm100 canlı sohbetini ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-comm100-live-chat-single-sign-on"></a>Comm100 Live Chat çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, güvenlik yöneticisi olarak Comm100 canlı sohbette oturum açın.

1. Sayfanın sağ üst kısmında **Hesabım**' ı tıklatın.

   ![Comm100 canlı sohbet myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. Menünün sol tarafında **güvenlik** ' e ve ardından **Aracı çoklu oturum açma**' ya tıklayın.

   ![Comm100 canlı sohbet güvenliği](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. **Aracı çoklu oturum açma** sayfasında, aşağıdaki adımları uygulayın:

   ![Comm100 canlı sohbet güvenliği](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. İlk vurgulanan bağlantıyı kopyalayın ve Azure portal **oturum açma URL 'si** metin kutusuna **Comm100 Live Chat etki alanı ve URL 'leri** bölümüne yapıştırın.

   b. **SAML SSO URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si**değerini yapıştırın.

   c. **Uzaktan oturum kapatma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si**değerini yapıştırın.

   d. Azure portal indirdiğiniz Base-64 kodlu sertifikayı **sertifikaya**yüklemek Için **Dosya Seç** ' e tıklayın.

   e. **Değişiklikleri Kaydet** 'e tıklayın

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

Bu bölümde, Comm100 Live Chat 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Comm100 canlı sohbet**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Comm100 Live Chat**' i seçin.

    ![Uygulamalar listesindeki Comm100 Live Chat bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-comm100-live-chat-test-user"></a>Comm100 Live Chat test kullanıcısı oluşturma

Azure AD kullanıcılarının Comm100 canlı sohbette oturum açmasını sağlamak için, Comm100 Live Chat 'e sağlanması gerekir. Comm100 canlı sohbette, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Comm100 canlı sohbette güvenlik yöneticisi olarak oturum açın.

2. Sayfanın sağ üst kısmında **Hesabım**' ı tıklatın.

    ![Comm100 canlı sohbet myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Menünün sol tarafında **aracılar** ' a ve ardından **Yeni Aracı**' e tıklayın.

    ![Comm100 canlı sohbet Aracısı](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. **Yeni Aracı** sayfasında, aşağıdaki adımları uygulayın:

    ![Comm100 canlı sohbet yeni aracı](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. **E-posta** metin kutusuna, **\@contoso.com**gibi kullanıcının e-postasını girin.

    b. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta**gibi girin.

    c. **Soyadı** metin kutusuna, **Simon**gibi kullanıcı adının soyadını girin.

    d. **Görünen ad** metin kutusuna, **Britta Simon** gibi kullanıcının görünen adını girin

    e. **Parola** metin kutusuna parolanızı yazın.

    f. **Kaydet** düğmesine tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Comm100 Live Chat kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Comm100 canlı sohbetinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

