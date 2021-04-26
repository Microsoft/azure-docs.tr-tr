---
title: 'Öğretici Azure Active Directory: Comm100 Live Chat ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Comm100 canlı sohbet arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 9636c8cbb517c7aece450f53cfc37e4ddd9803b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455502"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Öğretici: Comm100 Canlı sohbetle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Comm100 Live Chat 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Comm100 Live Chat 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Comm100 Live Chat 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla canlı sohbet Comm100 için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Comm100 canlı sohbet çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Comm100 canlı sohbeti **SP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Galeriden Comm100 canlı sohbet ekleme

Comm100 Live Chat 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için galerideki Comm100 Live Chat 'i yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Comm100 Live Chat** yazın.
1. Sonuçlar panelinden **Comm100 canlı sohbet** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Comm100 Live Chat için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Comm100 Live Chat ile yapılandırın ve test edin. SSO 'nun çalışması için, Comm100 Live Chat 'teki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Comm100 Canlı sohbetle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Comm100 Live Chat SSO 'Yu yapılandırın](#configure-comm100-live-chat-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Comm100 canlı sohbetinde B. Simon 'a sahip olmak için **[Comm100 Live Chat test kullanıcısı oluşturun](#create-comm100-live-chat-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Comm100 canlı sohbet** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Oturum açma URL 'SI değeri gerçek değil. Oturum açma URL 'si değerini, öğreticide daha sonra açıklanacak olan gerçek oturum açma URL 'SI ile güncelleştirirsiniz.

1. Comm100 Live Chat uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/edit-attribute.png)

1. Yukarıdaki Comm100 canlı sohbet uygulaması, daha fazla özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksiniminize göre bunları gözden geçirebilirsiniz.

    | Name |  Kaynak özniteliği|
    | ---------------| --------------- |
    |   e-posta    | Kullanıcı. Mail |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Comm100 canlı sohbetini ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Comm100 Live Chat 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Comm100 Live Chat**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-comm100-live-chat-sso"></a>Comm100 Live Chat SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Comm100 canlı sohbette güvenlik yöneticisi olarak oturum açın.

1. Sayfanın sağ üst kısmında **Hesabım**' ı tıklatın.

   ![Comm100 canlı sohbet myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. Menünün sol tarafında **güvenlik** ' e ve ardından **Aracı çoklu oturum açma**' ya tıklayın.

   !["Güvenlik" ve "Aracı çoklu oturum açma" vurgulanmış şekilde sol taraftaki hesap menüsünü gösteren ekran görüntüsü.](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. **Aracı çoklu oturum açma** sayfasında, aşağıdaki adımları uygulayın:

   ![Comm100 canlı sohbet güvenliği](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. İlk vurgulanan bağlantıyı kopyalayıp Azure portal **temel SAML yapılandırması** bölümünde **oturum açma URL 'si** metin kutusuna yapıştırın.

   b. **SAML SSO URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

   c. **Uzaktan oturum kapatma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

   d. Azure portal indirdiğiniz Base-64 kodlu sertifikayı **sertifikaya** yüklemek Için **Dosya Seç** ' e tıklayın.

   e. **Değişiklikleri Kaydet**’e tıklayın.

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

    a. a. **E-posta** metin kutusuna **B. Simon \@ contoso.com** gibi kullanıcının e-postasını girin.

    b. **Ad** metin kutusuna **B** gibi kullanıcının adını girin.

    c. **Soyadı** metin kutusuna, **Simon** gibi kullanıcı adının soyadını girin.

    d. **Görünen ad** metin kutusuna **B. Simon** gibi kullanıcının görünen adını girin

    e. **Parola** metin kutusuna parolanızı yazın.

    f. **Kaydet**’e tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Comm100 Live Chat kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Comm100 canlı sohbetinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Comm100 canlı sohbet 'i deneyin](https://aad.portal.azure.com/)