---
title: 'Öğretici: Zscaler üç ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Zscaler arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 75d95ff77b48e7b1102900bc103e6930282e21e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726287"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Öğretici: Zscaler üç ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Zscaler ' ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Zscaler üçünü Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Zscaler 'ın üçüne erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler ' a otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Zscaler üç çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Zscaler, **SP** tarafından başlatılan SSO 'yu destekler

* Zscaler **, yalnızca zamanında** Kullanıcı sağlamayı destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-zscaler-three-from-the-gallery"></a>Galeriden Zscaler üç ekleme

Zscaler 'nın üçünü Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden Zscaler ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zscaler üç** yazın.
1. Sonuçlar panelinden **Zscaler** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-three"></a>Zscaler için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Zscaler Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile Zscaler içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Zscaler üç ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Zscaler üç SSO 'Yu yapılandırın](#configure-zscaler-three-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Zscaler 'da, kullanıcının Azure AD gösterimine bağlı olan Zscaler 'da B. Simon ' ın bir karşılığı olacak şekilde üç **[test kullanıcısı oluşturun](#create-zscaler-three-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zscaler üç** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://login.zscalerthree.net/sfc_sso`

1. Zscaler üç uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

6. Yukarıdaki Zscaler uygulamasının yanı sıra, aşağıda gösterilen SAML yanıtında daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksiniminize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği |
    | ---------| ------------ |
    | Üyesi | Kullanıcı. atandroles |

    > [!NOTE]
    > Azure AD 'de rolün nasıl yapılandırılacağını öğrenmek için lütfen [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) tıklayın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Zscaler** ' ı ayarlama bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Zscaler ' a erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Zscaler 3**' ü seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda listeden **Britta Simon** gibi bir kullanıcı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

    ![Ekran görüntüsü, bir kullanıcı seçebileceğiniz kullanıcılar ve gruplar iletişim kutusunu gösterir.](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. **Rol Seç** iletişim kutusunda listeden uygun Kullanıcı rolünü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

    ![Ekran görüntüsü, bir kullanıcı rolü seçebileceğiniz rol Seç iletişim kutusunu gösterir.](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

    ![Ekran görüntüsü ata ' yı seçebileceğiniz atama Ekle iletişim kutusunu gösterir.](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Zscaler üç SSO yapılandırma

1. Zscaler 'daki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımın güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, bu üç uygulamaya tıklayarak **Zscaler 3** ' e tıklayın. Buradan, Zscaler 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum](common/setup-sso.png)

3. Zscaler ' ı el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Zscaler şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. **Yönetim > kimlik doğrulaması > kimlik doğrulama ayarları** ' na gidin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, açıklanan adımları içeren Zscaler sitesini gösterir.](./media/zscaler-three-tutorial/ic800206.png "Yönetim")

    a. Kimlik doğrulama türü altında **SAML**' yi seçin.

    b. **SAML Yapılandır** öğesine tıklayın.

5. **SAML Düzenle** penceresinde, aşağıdaki adımları uygulayın: ve Kaydet ' e tıklayın.  

    ![Kullanıcı & kimlik doğrulaması yönetme](./media/zscaler-three-tutorial/ic800208.png "Kullanıcı & kimlik doğrulaması yönetme")

    a. **SAML PORTALı URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    b. **Oturum açma adı öznitelik** metin kutusuna **NameID** girin.

    c. **Ortak SSL sertifikasındaki** Azure Portal adresinden INDIRDIĞINIZ Azure SAML imzalama sertifikasını karşıya yüklemek Için **karşıya yükle**' ye tıklayın.

    d. **SAML otomatik sağlamayı etkinleştir**' i açın.

    e. **Kullanıcı görünen adı öznitelik** metin kutusunda, DisplayName ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **DisplayName** yazın.

    f. **Grup adı öznitelik** metin kutusunda, memberOf ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek Istiyorsanız, **memberOf** yazın.

    örneğin: Departman **adı özniteliğinde** departman ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **departmanı** girin.

    h. **Kaydet**’e tıklayın.

6. **Kullanıcı kimlik doğrulamasını Yapılandır** iletişim sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, etkinleştir seçili olan kullanıcı kimlik doğrulamasını Yapılandır iletişim kutusunu gösterir.](./media/zscaler-three-tutorial/ic800207.png)

    a. Sol alt kısımdaki **etkinleştirme** menüsünün üzerine gelin.

    b. **Etkinleştir**' e tıklayın.

## <a name="configuring-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer 'da proxy ayarlarını yapılandırmak için

1. **Internet Explorer 'ı** başlatın.

2. **Internet** seçenekleri iletişim kutusunu açmak için **Araçlar** menüsünden **Internet seçenekleri** ' ni seçin.   

     ![Internet seçenekleri](./media/zscaler-three-tutorial/ic769492.png "Internet seçenekleri")

3. **Bağlantılar** sekmesine tıklayın.   

     ![Bağlantılar](./media/zscaler-three-tutorial/ic769493.png "Bağlantılar")

4. **LAN ayarları iletişim kutusunu** açmak için **LAN ayarları** ' na tıklayın.

5. Proxy sunucusu bölümünde aşağıdaki adımları uygulayın:   

    ![Proxy sunucusu](./media/zscaler-three-tutorial/ic769494.png "Proxy sunucu")

    a. **LAN için bir proxy sunucusu kullan**' ı seçin.

    b. Adres metin kutusuna **ağ geçidi yazın. Zscaler Three.net**.

    c. Bağlantı noktası metin kutusuna **80** yazın.

    d. **Yerel adresler için proxy sunucusunu atla**' yı seçin.

    e. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

6. **Internet seçenekleri** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

### <a name="create-zscaler-three-test-user"></a>Zscaler üç test kullanıcısı oluşturma

Bu bölümde, Zscaler 'da B. Simon adlı bir Kullanıcı oluşturulur. Zscaler, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Zscaler 'da zaten mevcut değilse, Zscaler üçüne erişmeye çalıştığınızda yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa, [Zscaler 'ın üç destek ekibine](https://www.zscaler.com/company/contact)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Zscaler üç oturum açma URL 'sine yeniden yönlendirilir. 

* Zscaler ' a doğrudan üç oturum açma URL 'sine gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içinde Zscaler üç kutucuğa tıkladığınızda bu, Zscaler üç oturum açma URL 'sine yönlendirecektir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Zscaler 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
