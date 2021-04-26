---
title: 'Öğretici: Zscaler One ile tümleştirme Azure Active Directory | Microsoft Docs'
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
ms.openlocfilehash: 3f825e247aff5c8fc53eb8610f33bd8e0b7fce3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735650"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Öğretici: Zscaler One ile tümleştirme Azure Active Directory

Bu öğreticide, Zscaler 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Zscaler 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Zscaler 'ya erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler 'da (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.


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

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zscaler** yazın.
1. Sonuçlar panelinden **Zscaler** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-one"></a>Zscaler için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Zscaler ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Zscaler içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Zscaler ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. **[Zscaler SSO 'Yu yapılandırma](#configure-zscaler-one-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    1. **[Zscaler bir test kullanıcısı oluşturun](#create-zscaler-one-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan Zscaler 'Da Britta Simon 'ın bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zscaler** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    Oturum açma **URL 'si** metin kutusuna kullanıcılarınız tarafından, Zscaler uygulamanızda oturum açmak için kullanılan URL 'yi yazın.

    > [!NOTE]
    > Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [Zscaler istemci destek ekibine](https://www.zscaler.com/company/contact) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. Zscaler uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

6. Zscaler, yukarıdakine ek olarak, SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:
    
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

7. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

8. **Zscaler 'Yı ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Zscaler 'ya erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Zscaler One** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Rolleri yukarıda açıklanan şekilde ayarlarsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-zscaler-one-sso"></a>Zscaler bir SSO yapılandırma

1. Zscaler 'daki yapılandırmayı otomatikleştirebilmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, bu uygulamayı bir tane, tek bir uygulamaya yönlendirecek şekilde **ayarlayın** . Buradan, Zscaler 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Yükleme SSO 'su](common/setup-sso.png)

3. Zscaler 'ı bir el ile kurmak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Zscaler şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. **Yönetim > kimlik doğrulaması > kimlik doğrulama ayarları** ' na gidin ve aşağıdaki adımları gerçekleştirin:
   
    ![Ekran görüntüsü, açıklanan adımları içeren Zscaler sitesini gösterir.](./media/zscaler-one-tutorial/ic800206.png "Yönetim")

    a. Kimlik doğrulama türü altında **SAML**' yi seçin.

    b. **SAML Yapılandır** öğesine tıklayın.

5. **SAML Düzenle** penceresinde, aşağıdaki adımları uygulayın: ve Kaydet ' e tıklayın.  
            
    ![Kullanıcı & kimlik doğrulaması yönetme](./media/zscaler-one-tutorial/ic800208.png "Kullanıcı & kimlik doğrulaması yönetme")
    
    a. **SAML PORTALı URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    b. **Oturum açma adı öznitelik** metin kutusuna **NameID** girin.

    c. **Ortak SSL sertifikasındaki** Azure Portal adresinden INDIRDIĞINIZ Azure SAML imzalama sertifikasını karşıya yüklemek Için **karşıya yükle**' ye tıklayın.

    d. **SAML otomatik sağlamayı etkinleştir**' i açın.

    e. **Kullanıcı görünen adı öznitelik** metin kutusunda, DisplayName ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **DisplayName** yazın.

    f. **Grup adı öznitelik** metin kutusunda, memberOf ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek Istiyorsanız, **memberOf** yazın.

    örneğin: Departman **adı özniteliğinde** departman ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **departmanı** girin.

    h. **Kaydet**’e tıklayın.

6. **Kullanıcı kimlik doğrulamasını Yapılandır** iletişim sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, etkinleştir seçili olan kullanıcı kimlik doğrulamasını Yapılandır iletişim kutusunu gösterir.](./media/zscaler-one-tutorial/ic800207.png)

    a. Sol alt kısımdaki **etkinleştirme** menüsünün üzerine gelin.

    b. **Etkinleştir**' e tıklayın.

## <a name="configuring-proxy-settings&quot;></a>Ara sunucu ayarlarını yapılandırma
### <a name=&quot;to-configure-the-proxy-settings-in-internet-explorer&quot;></a>Internet Explorer 'da proxy ayarlarını yapılandırmak için

1. **Internet Explorer 'ı** başlatın.

2. **Internet** seçenekleri iletişim kutusunu açmak için **Araçlar** menüsünden **Internet seçenekleri** ' ni seçin.   

    ![Internet seçenekleri](./media/zscaler-one-tutorial/ic769492.png &quot;Internet seçenekleri")

3. **Bağlantılar** sekmesine tıklayın.   

    ![Bağlantılar](./media/zscaler-one-tutorial/ic769493.png "Bağlantılar")

4. **LAN ayarları iletişim kutusunu** açmak için **LAN ayarları** ' na tıklayın.

5. Proxy sunucusu bölümünde aşağıdaki adımları uygulayın:   
   
    ![Proxy sunucusu](./media/zscaler-one-tutorial/ic769494.png "Proxy sunucu")

    a. **LAN için bir proxy sunucusu kullan**' ı seçin.

    b. Adres metin kutusuna **ağ geçidi yazın. Zscaler One.net**.

    c. Bağlantı noktası metin kutusuna **80** yazın.

    d. **Yerel adresler için proxy sunucusunu atla**' yı seçin.

    e. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

7. **Internet seçenekleri** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

### <a name="create-zscaler-one-test-user"></a>Zscaler bir test kullanıcısı oluşturma

Bu bölümde, Zscaler 'da Britta Simon adlı bir Kullanıcı oluşturulur. Zscaler bir, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Zscaler 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa, [Zscaler One destek ekibine](https://www.zscaler.com/company/contact)başvurun.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Zscaler bir oturum açma URL 'sine yönlendirecektir. 

* Doğrudan Zscaler oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Zscaler kutucuğunu tıklattığınızda, bu, Zscaler bir oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Zscaler 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
