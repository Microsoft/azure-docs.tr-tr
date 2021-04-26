---
title: 'Öğretici: Zscaler Zscyüksek ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Zscaler Zscyüksek arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 605f033ed48dd79fd164aabd95e326a6467d0ecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726252"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Öğretici: Zscaler Zscyüksek ile tümleştirme Azure Active Directory

Bu öğreticide, Zscaler Zscsesli Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Zscaler Zscı 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Zscaler Zscyüksek 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler Zscyüksek ' e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Zscaler Zscyüksek ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Zscaler Zscyüksek çoklu oturum açma aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Zscaler Zscyüksek, **SP** tarafından başlatılan SSO 'yu destekler

* Zscaler Zscyüksek **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Galeriden Zscaler Zscyüksek ekleme

Zscaler Zscyüksek ' i Azure AD 'ye tümleştirmeyi yapılandırmak için, galerisindeki Zscaler Zscyüksek 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zscaler zscyüksek** yazın.
1. Sonuçlar panelinden **Zscaler Zscyüksek** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-zscloud"></a>Zscaler Zscyüksek için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Zscaler Zscyüksek Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Zscaler Zscyüksek içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Zscaler Zscyüksek ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Zscaler Zscyüksek SSO 'Yu yapılandırın](#configure-zscaler-zscloud-sso)** .
    1. **[Zscaler zscsesli test kullanıcısı oluşturun](#create-zscaler-zscloud-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Zscaler Zscyüksek 'de B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zscaler zscyüksek** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    Oturum açma **URL 'si** metin kutusuna kullanıcılarınızın ZScaler Zscyüksek uygulamanızda oturum açmasını sağlamak için kullanılan URL 'yi yazın.

    > [!NOTE]
    > Değeri gerçek Sign-On URL 'siyle güncelleştirmeniz gerekir. Değeri almak için [Zscaler Zscyüksek istemci destek ekibine](https://help.zscaler.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. Zscaler Zscyüksek uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

6. Zscaler Zscı uygulaması, yukarıdakine ek olarak SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:
    
    | Name | Kaynak özniteliği |
    | ---------| ------------ |
    | Üyesi | Kullanıcı. atandroles |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![Ekran görüntüsü, yeni talep ekleme seçeneğiyle Kullanıcı taleplerini gösterir.](common/new-save-attribute.png)

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı taleplerini Yönet iletişim kutusunu gösterir.](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.
    
    f. **Kaydet**’e tıklayın.

    > [!NOTE]
    > Azure AD 'de rolün nasıl yapılandırılacağını öğrenmek için lütfen [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) tıklayın.

7. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

8. **Zscaler Zscı ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Zscaler Zscg 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Zscaler zscyüksek**' yı seçin.
2. Uygulamalar listesinde **Zscaler Zscyüksek**' yı seçin.
3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.
4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
5. **Kullanıcılar ve gruplar** iletişim kutusunda listeden **Britta Simon** gibi bir kullanıcı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

    ![Ekran görüntüsü, bir kullanıcı seçebileceğiniz kullanıcılar ve gruplar iletişim kutusunu gösterir.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. **Rol Seç** iletişim kutusunda listeden uygun Kullanıcı rolünü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

    ![Ekran görüntüsü, bir kullanıcı rolü seçebileceğiniz rol Seç iletişim kutusunu gösterir.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

    ![Ekran görüntüsü ata ' yı seçebileceğiniz atama Ekle iletişim kutusunu gösterir.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >Varsayılan erişim rolü, sağlama işlemini kesintiye uğramayacak ve bu nedenle Kullanıcı atanırken varsayılan rol seçilemeyecek şekilde desteklenmez.

## <a name="configure-zscaler-zscloud-sso"></a>Zscaler Zscyüksek SSO 'yu yapılandırma

1. Zscaler Zscyüksek içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek** üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **Zscaler zscı** ' ye tıklayın, sizi Zscaler zscyüksek uygulamasına yönlendirir. Buradan, Zscaler Zscyüksek 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Yükleme SSO 'su](common/setup-sso.png)

3. Zscaler Zscı 'yi el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Zscaler Zscı şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. **Yönetim > kimlik doğrulaması > kimlik doğrulama ayarları** ' na gidin ve aşağıdaki adımları gerçekleştirin:
   
    ![Ekran görüntüsü, belirtilen adımlarla Zscaler sitesini gösterir.](./media/zscaler-zscloud-tutorial/ic800206.png "Yönetim")

    a. Kimlik doğrulama türü altında **SAML**' yi seçin.

    b. **SAML Yapılandır** öğesine tıklayın.

5. **SAML Düzenle** penceresinde, aşağıdaki adımları uygulayın: ve Kaydet ' e tıklayın.  
            
    ![Kullanıcı & kimlik doğrulaması yönetme](./media/zscaler-zscloud-tutorial/ic800208.png "Kullanıcı & kimlik doğrulaması yönetme")
    
    a. **SAML PORTALı URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    b. **Oturum açma adı öznitelik** metin kutusuna **NameID** girin.

    c. **Ortak SSL sertifikasındaki** Azure Portal adresinden INDIRDIĞINIZ Azure SAML imzalama sertifikasını karşıya yüklemek Için **karşıya yükle**' ye tıklayın.

    d. **SAML otomatik sağlamayı etkinleştir**' i açın.

    e. **Kullanıcı görünen adı öznitelik** metin kutusunda, DisplayName ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **DisplayName** yazın.

    f. **Grup adı öznitelik** metin kutusunda, memberOf ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek Istiyorsanız, **memberOf** yazın.

    örneğin: Departman **adı özniteliğinde** departman ÖZNITELIKLERI için SAML otomatik sağlamayı etkinleştirmek istiyorsanız **departmanı** girin.

    h. **Kaydet**’e tıklayın.

6. **Kullanıcı kimlik doğrulamasını Yapılandır** iletişim sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, etkinleştir seçili olan kullanıcı kimlik doğrulamasını Yapılandır iletişim kutusunu gösterir.](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Sol alt kısımdaki **etkinleştirme** menüsünün üzerine gelin.

    b. **Etkinleştir**' e tıklayın.

## <a name="configuring-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer 'da proxy ayarlarını yapılandırmak için

1. **Internet Explorer 'ı** başlatın.

2. **Internet** seçenekleri iletişim kutusunu açmak için **Araçlar** menüsünden **Internet seçenekleri** ' ni seçin.   
    
     ![Internet seçenekleri](./media/zscaler-zscloud-tutorial/ic769492.png "Internet seçenekleri")

3. **Bağlantılar** sekmesine tıklayın.   
  
     ![Bağlantılar](./media/zscaler-zscloud-tutorial/ic769493.png "Bağlantılar")

4. **LAN ayarları iletişim kutusunu** açmak için **LAN ayarları** ' na tıklayın.

5. Proxy sunucusu bölümünde aşağıdaki adımları uygulayın:   
   
    ![Proxy sunucusu](./media/zscaler-zscloud-tutorial/ic769494.png "Proxy sunucu")

    a. **LAN için bir proxy sunucusu kullan**' ı seçin.

    b. Adres metin kutusuna **ağ geçidi yazın. Zscaler ZSCloud.net**.

    c. Bağlantı noktası metin kutusuna **80** yazın.

    d. **Yerel adresler için proxy sunucusunu atla**' yı seçin.

    e. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

6. **Internet seçenekleri** iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.


### <a name="create-zscaler-zscloud-test-user"></a>Zscaler Zscyüksek test kullanıcısı oluşturma

Bu bölümde, Zscaler Zscyüksek 'de Britta Simon adlı bir Kullanıcı oluşturulur. Zscaler Zscyüksek, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Zscaler Zscı 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa, [Zscaler Zscyüksek destek ekibine](https://help.zscaler.com/)başvurun.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz Zscaler Zscyüksek oturum açma URL 'sine yeniden yönlendirilir. 

* Zscaler Zscyüksek oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Zscaler Zscum kutucuğuna tıkladığınızda bu, Zscaler Zscyüksek oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Zscaler Zscı 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
