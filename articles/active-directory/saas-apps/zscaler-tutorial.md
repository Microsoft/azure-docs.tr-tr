---
title: 'Öğretici: Zscaler ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
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
ms.openlocfilehash: 84c74d5c1452cf5945aa4f66c3ed3c6af48681b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Öğretici: Zscaler ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Zscaler 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Zscaler 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Zscaler 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Zscaler çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Zscaler, **SP** tarafından başlatılan SSO 'yu destekliyor
* Zscaler **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-zscaler-from-the-gallery"></a>Galeriden Zscaler ekleme

Zscaler 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden Zscaler ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zscaler** yazın.
1. Sonuçlar panelinden **Zscaler** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-zscaler"></a>Zscaler için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Zscaler Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Zscaler içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Zscaler ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Zscaler SSO 'Yu yapılandırın](#configure-zscaler-sso)** .
    1. **[Zscaler test kullanıcısı oluşturun](#create-zscaler-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Zscaler 'da B. Simon 'a karşılık gelen bir buna sahip olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zscaler** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.zscaler.net`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [Zscaler istemci destek ekibine](https://www.zscaler.com/company/contact) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Zscaler uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

1. Zscaler uygulaması, yukarıdakine ek olarak SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Name | Kaynak özniteliği |
    | ---------| ------------ |
    | Üyesi | Kullanıcı. atandroles |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Kaydet**’e tıklayın.

    > [!NOTE]
    > Azure AD 'de rolün nasıl yapılandırılacağını öğrenmek için lütfen [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) tıklayın.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Zscaler ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Zscaler 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Zscaler**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Rolleri yukarıda açıklanan şekilde ayarlarsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-zscaler-sso"></a>Zscaler SSO 'yu yapılandırma

1. Zscaler içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek** üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, kurulum 'a tıkladığınızda **Zscaler** , sizi Zscaler uygulamasına yönlendirirler. Buradan, Zscaler 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Yükleme SSO 'SU](common/setup-sso.png)

1. Zscaler 'ı el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Zscaler şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. **Yönetim > kimlik doğrulaması > kimlik doğrulama ayarları** ' na gidin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, açıklanan adımları içeren Zscaler sitesini gösterir.](./media/zscaler-tutorial/ic800206.png "Yönetim")

    a. Kimlik doğrulama türü altında **SAML**' yi seçin.

    b. **SAML Yapılandır** öğesine tıklayın.

1. **SAML Düzenle** penceresinde, aşağıdaki adımları uygulayın: ve Kaydet ' e tıklayın.  

    ![Kullanıcı & kimlik doğrulaması yönetme](./media/zscaler-tutorial/ic800208.png "Kullanıcı & kimlik doğrulaması yönetme")

    a. **SAML PORTALı URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    b. **Oturum açma adı öznitelik** metin kutusuna **NameID** girin.

    c. **Ortak SSL sertifikasındaki** Azure Portal adresinden INDIRDIĞINIZ Azure SAML imzalama sertifikasını karşıya yüklemek Için **karşıya yükle**' ye tıklayın.

    d. **SAML otomatik sağlamayı etkinleştir**' i açın.

    e. **Kullanıcı görünen adı öznitelik** metin kutusunda, DisplayName ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **DisplayName** yazın.

    f. **Grup adı öznitelik** metin kutusunda, memberOf ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek Istiyorsanız, **memberOf** yazın.

    örneğin: Departman **adı özniteliğinde** departman ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **departmanı** girin.

    h. **Kaydet**’e tıklayın.

1. **Kullanıcı kimlik doğrulamasını Yapılandır** iletişim sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, etkinleştir seçili olan kullanıcı kimlik doğrulamasını Yapılandır iletişim kutusunu gösterir.](./media/zscaler-tutorial/ic800207.png)

    a. Sol alt kısımdaki **etkinleştirme** menüsünün üzerine gelin.

    b. **Etkinleştir**' e tıklayın.

## <a name="configuring-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer 'da proxy ayarlarını yapılandırmak için

1. **Internet Explorer 'ı** başlatın.

1. **Internet** seçenekleri iletişim kutusunu açmak için **Araçlar** menüsünden **Internet seçenekleri** ' ni seçin.

    ![Internet seçenekleri](./media/zscaler-tutorial/ic769492.png "Internet seçenekleri")

1. **Bağlantılar** sekmesine tıklayın.

    ![Bağlantılar](./media/zscaler-tutorial/ic769493.png "Bağlantılar")

1. **LAN ayarları iletişim kutusunu** açmak için **LAN ayarları** ' na tıklayın.

1. Proxy sunucusu bölümünde aşağıdaki adımları uygulayın:   

    ![Proxy sunucusu](./media/zscaler-tutorial/ic769494.png "Proxy sunucu")

    a. **LAN için bir proxy sunucusu kullan**' ı seçin.

    b. Adres metin kutusuna **Gateway.Zscaler.net** yazın.

    c. Bağlantı noktası metin kutusuna **80** yazın.

    d. **Yerel adresler için proxy sunucusunu atla**' yı seçin.

    e. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

1. **Internet seçenekleri** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

### <a name="create-zscaler-test-user"></a>Zscaler test kullanıcısı oluşturma

Bu bölümde, Zscaler içinde Britta Simon adlı bir Kullanıcı oluşturulur. Zscaler, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Zscaler 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!Note]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Zscaler destek ekibine](https://www.zscaler.com/company/contact)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Zscaler oturum açma URL 'sine yeniden yönlendirilir. 

* Zscaler oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Zscaler kutucuğuna tıkladığınızda, bu, Zscaler oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Zscaler 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
