---
title: 'Öğretici: Citrix ADC ile çoklu oturum açma tümleştirmesi Azure Active Directory (üst bilgi tabanlı kimlik doğrulaması) | Microsoft Docs'
description: Üst bilgi tabanlı kimlik doğrulaması kullanarak Azure Active Directory ve Citrix ADC arasında çoklu oturum açma (SSO) yapılandırma hakkında bilgi edinin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 9cab0597aeb3bc28f391de558240e5d894f5a49c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735256"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-header-based-authentication"></a>Öğretici: Citrix ADC ile çoklu oturum açma tümleştirmesi Azure Active Directory (üst bilgi tabanlı kimlik doğrulaması)

Bu öğreticide Citrix ADC 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Citrix ADC 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Citrix ADC erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Citrix ADC 'ye otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Citrix ADC çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Öğreticide şu senaryolar yer almaktadır:

* **SP tarafından başlatılan** Citrix ADC için SSO

* Citrix ADC için **tam zamanında** Kullanıcı sağlama

* [Citrix ADC için üst bilgi tabanlı kimlik doğrulaması](#publish-the-web-server)

* [Citrix ADC için Kerberos tabanlı kimlik doğrulaması](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-adc-from-the-gallery"></a>Galeriden Citrix ADC ekleme

Citrix ADC 'yi Azure AD ile tümleştirmek için, önce galerideki yönetilen SaaS uygulamaları listenize Citrix ADC 'yi ekleyin:

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.

1. Sol menüden **Azure Active Directory**' yi seçin.

1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.

1. **Galeriden Ekle** bölümünde, arama kutusuna **Citrix ADC** girin.

1. Sonuçlarda **CITRIX ADC**' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Citrix ADC için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Citrix ADC Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Citrix ADC içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Citrix ADC ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.

    1. B. Simon ile Azure AD SSO 'yu test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .

    1. Azure AD [Test kullanıcısına atama](#assign-the-azure-ad-test-user) -Azure AD SSO 'yu kullanmak için B. Simon 'u etkinleştirmek için.

1. Uygulama tarafında SSO ayarlarını yapılandırmak için [CITRIX ADC SSO 'Yu yapılandırın](#configure-citrix-adc-sso) .

    * Kullanıcının Azure AD gösterimine bağlı olan Citrix ADC 'de B. Simon 'ya karşılık gelen bir [CITRIX ADC test kullanıcısı oluşturun](#create-a-citrix-adc-test-user) .

1. [Test SSO](#test-sso) -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure AD SSO 'yu Azure portal kullanarak etkinleştirmek için şu adımları uygulayın:

1. Azure portal, **CITRIX ADC** uygulama tümleştirme bölmesindeki **Yönet** altında, **Çoklu oturum açma**' yı seçin.

1. **Çoklu oturum açma yöntemi seç** bölmesinde **SAML**' yi seçin.

1. **SAML Ile tek Sign-On ayarla** bölmesinde, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem **düzenleme** simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP tarafından başlatılan** modda yapılandırmak için:

    1. **Tanımlayıcı** metin kutusuna aşağıdaki düzene sahıp bir URL girin:`https://<Your FQDN>`

    1. **Yanıt URL 'si** metin kutusuna aşağıdaki düzene sahıp bir URL girin:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Uygulamayı **SP tarafından başlatılan** modda yapılandırmak Için **ek URL 'ler ayarla** ' yı seçin ve aşağıdaki adımı doldurun:

    * **Oturum açma URL 'si** metin kutusunda, aşağıdaki düzene sahıp bir URL girin:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Bu bölümde kullanılan URL 'Ler gerçek değer değildir. Bu değerleri tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI için gerçek değerlerle güncelleştirin. Bu değerleri almak için [CITRIX ADC istemci destek ekibine](https://www.citrix.com/contact/technical-support.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.
    > * SSO 'yu ayarlamak için, URL 'Lerin genel Web sitelerinden erişilebilir olması gerekir. Bir güvenlik duvarını veya Citrix ADC tarafında bulunan güvenlik ayarlarını, belirteci yapılandırılmış URL 'de göndermek için Azure AD 'ye uygun hale getirmek üzere etkinleştirmeniz gerekir.

1. **SAML Ile tek Sign-On ayarla** bölmesinde, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'si** Için, URL 'yi kopyalayın ve Not defteri 'ne kaydedin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. Citrix ADC uygulaması SAML onayları 'nin belirli bir biçimde olmasını bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Düzenle** simgesini seçin ve öznitelik eşlemelerini değiştirin.

    ![SAML öznitelik eşlemesini Düzenle](common/edit-attribute.png)

1. Citrix ADC uygulaması, SAML yanıtına daha fazla özniteliğin geri geçirilmesini de bekler. **Kullanıcı öznitelikleri** iletişim kutusunda, **Kullanıcı talepleri**' ın altında, SAML belirteci özniteliklerini tabloda gösterildiği gibi eklemek için aşağıdaki adımları izleyin:

    | Name | Kaynak özniteliği|
    | ---------------| --------------- |
    | Mysecretıd  | User. UserPrincipalName |
    
    1. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' yi seçin.

    1. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını girin.

    1. **Ad alanını** boş bırakın.

    1. **Öznitelik** için **kaynak**' ı seçin.

    1. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini girin.

    1. **Tamam**’ı seçin.

    1. **Kaydet**’i seçin.

1. **CITRIX ADC 'Yi ayarla** bölümünde, gereksinimlerinize göre Ilgili URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol menüsünde **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

1. Bölmenin en üstünde bulunan **Yeni Kullanıcı** ' yı seçin.

1. **Kullanıcı** özellikleri ' nde şu adımları uygulayın:

   1. **Ad** için girin `B.Simon` .  

   1. **Kullanıcı adı** için girin _username@companydomain.extension_ . Örneğin, `B.Simon@contoso.com`.

   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola**' ya, sonra da görüntülenen değeri yazın veya kopyalayın.

   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Kullanıcı, Citrix ADC 'ye erişim izni vererek, B. Simon kullanıcısını Azure SSO 'yu kullanacak şekilde etkinleştirecektir.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.

1. Uygulamalar listesinde **CITRIX ADC**' yi seçin.

1. Uygulamaya genel bakış bölümünde, **Yönet** altında **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon** öğesini seçin. **Seç**’i seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-citrix-adc-sso"></a>Citrix ADC SSO 'yu yapılandırma

Yapılandırmak istediğiniz kimlik doğrulaması türü için adımlar için bir bağlantı seçin:

- [Üst bilgi tabanlı kimlik doğrulaması için Citrix ADC SSO 'yu yapılandırma](#publish-the-web-server)

- [Kerberos tabanlı kimlik doğrulaması için Citrix ADC SSO 'yu yapılandırma](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Web sunucusunu yayımlama 

Bir sanal sunucu oluşturmak için:

1. **Trafik yönetimi**  >  **Yük Dengeleme**  >  **Hizmetleri**' ni seçin.
    
1. **Add (Ekle)** seçeneğini belirleyin.

    ![Citrix ADC yapılandırma-hizmetler bölmesi](./media/header-citrix-netscaler-tutorial/web01.png)

1. Uygulamaları çalıştıran Web sunucusu için aşağıdaki değerleri ayarlayın:

   * **Hizmet adı**
   * **Sunucu IP/varolan sunucu**
   * **Protokol**
   * **Bağlantı noktası**

     ![Citrix ADC yapılandırma bölmesi](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>Yük dengeleyiciyi yapılandırma

Yük dengeleyiciyi yapılandırmak için:

1. **Trafik yönetimi**  >  **Yük Dengeleme**  >  **sanal sunucularına** gidin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. Aşağıdaki değerleri aşağıdaki ekran görüntüsünde açıklandığı gibi ayarlayın:

    * **Ad**
    * **Protokol**
    * **IP Adresi**
    * **Bağlantı noktası**

1. **Tamam**’ı seçin.

    ![Citrix ADC yapılandırması-temel ayarlar bölmesi](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Sanal sunucuyu bağlama

Yük dengeleyiciyi sanal sunucuyla bağlamak için:

1. **Hizmetler ve hizmet grupları** bölmesinde, **Yük Dengeleme sanal sunucu hizmeti bağlamayı yok**' u seçin.

   ![Citrix ADC yapılandırması-sanal sunucu hizmeti bağlama bölmesi yük dengelemesi](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Aşağıdaki ekran görüntüsünde gösterildiği gibi ayarları doğrulayın ve ardından **Kapat**' ı seçin.

   ![Citrix ADC yapılandırması-Virtual Server Hizmetleri bağlamayı doğrulama](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Sertifikayı bağlama

Bu hizmeti TLS olarak yayımlamak için sunucu sertifikasını bağlayın ve ardından uygulamanızı test edin:

1. **Sertifika** altında **sunucu sertifikası yok**' u seçin.

   ![Citrix ADC yapılandırması-sunucu sertifikası bölmesi](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Aşağıdaki ekran görüntüsünde gösterildiği gibi ayarları doğrulayın ve ardından **Kapat**' ı seçin.

   ![Citrix ADC yapılandırması-sertifikayı doğrulama](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML profili

Citrix ADC SAML profilini yapılandırmak için aşağıdaki bölümleri doldurun:

### <a name="create-an-authentication-policy"></a>Kimlik doğrulama ilkesi oluşturma

Kimlik doğrulama ilkesi oluşturmak için:

1. **Güvenlik**  >  **aaa – uygulama trafik**  >  **ilkeleri**  >  **kimlik** doğrulama  >  **ilkeleri**' ne gidin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. **Kimlik doğrulama Ilkesi oluştur** bölmesinde, aşağıdaki değerleri girin veya seçin:

    * **Ad**: kimlik doğrulama ilkeniz için bir ad girin.
    * **Eylem**: **SAML** yazın ve ardından **Ekle**' yi seçin.
    * **İfade**: **true** girin.     
    
    ![Citrix ADC yapılandırması-kimlik doğrulama Ilkesi oluştur bölmesi](./media/header-citrix-netscaler-tutorial/policy01.png)

1. **Oluştur**’u seçin.

### <a name="create-an-authentication-saml-server"></a>Kimlik doğrulama SAML sunucusu oluşturma

Bir kimlik doğrulama SAML sunucusu oluşturmak için, **kimlik doğrulaması SAML sunucusu oluştur** bölmesine gidin ve aşağıdaki adımları tamamlayın:

1. **Ad** için, KIMLIK doğrulaması SAML sunucusu için bir ad girin.

1. **SAML meta verilerini dışarı aktar** altında:

   1. **Meta verileri Içeri aktar** onay kutusunu seçin.

   1. Daha önce kopyaladığınız Azure SAML kullanıcı arabiriminden Federasyon meta verileri URL 'sini girin.
    
1. **Verenin adı** için ilgili URL 'yi girin.

1. **Oluştur**’u seçin.

![Citrix ADC yapılandırması-kimlik doğrulama SAML sunucu bölmesi oluştur](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Kimlik doğrulama sanal sunucusu oluşturma

Kimlik doğrulama sanal sunucusu oluşturmak için:

1.  **Güvenlik**  >  **aaa-uygulama trafik**  >  **ilkeleri**  >  **kimlik** doğrulama  >  **sanal sunucuları**' na gidin.

1.  **Ekle**' yi seçin ve ardından aşağıdaki adımları uygulayın:

    1. **Ad** için, kimlik doğrulama sanal sunucusu için bir ad girin.

    1. **Adreslenebilir olmayan** onay kutusunu seçin.

    1. **Protokol** için **SSL**' yi seçin.

    1. **Tamam**’ı seçin.

    ![Citrix ADC yapılandırması-kimlik doğrulama sanal sunucu bölmesi](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Kimlik doğrulama sanal sunucusunu Azure AD kullanacak şekilde yapılandırma

Kimlik doğrulama sanal sunucusu için iki bölümü değiştirin:

1.  **Gelişmiş kimlik doğrulama ilkeleri** bölmesinde **kimlik doğrulama ilkesi yok**' u seçin.

    ![Citrix ADC yapılandırması-Gelişmiş kimlik doğrulama Ilkeleri bölmesi](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. **Ilke bağlama** bölmesinde, kimlik doğrulama ilkesini seçin ve ardından **bağla**' yı seçin.

    ![Citrix ADC yapılandırma-Ilke bağlama bölmesi](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. **Form tabanlı sanal sunucular** bölmesinde, **Yük Dengeleme sanal sunucusu yok**' u seçin.

    ![Citrix ADC yapılandırma-form tabanlı sanal sunucular bölmesi](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. **Kimlik doğrulama FQDN 'si** için tam etki alanı adı (FQDN) (gerekli) girin.

1. Azure AD kimlik doğrulamasıyla korumak istediğiniz yük dengeleme sanal sunucusunu seçin.

1. **Bağla**' yı seçin.

    ![Citrix ADC yapılandırması-sanal sunucu bağlama bölmesinin yükünü dengeleme](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > **Kimlik doğrulama sanal sunucu yapılandırma** bölmesinde **bitti** ' yi seçtiğinizden emin olun.

1. Değişikliklerinizi doğrulamak için, bir tarayıcıda uygulama URL 'sine gidin. Daha önce gördüğünüz kimliği doğrulanmamış erişim yerine kiracı oturum açma sayfanızı görmeniz gerekir.

    ![Citrix ADC yapılandırması-bir Web tarayıcısında oturum açma sayfası](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-header-based-authentication"></a>Üst bilgi tabanlı kimlik doğrulaması için Citrix ADC SSO 'yu yapılandırma

### <a name="configure-citrix-adc"></a>Citrix ADC 'yi yapılandırma

Üst bilgi tabanlı kimlik doğrulaması için Citrix ADC yapılandırmak için aşağıdaki bölümleri doldurun.

#### <a name="create-a-rewrite-action"></a>Yeniden yazma eylemi oluştur

1. **Appexpert**  >  **yeniden** yazma  >  **eylemleri**' ne gidin.
 
    ![Citrix ADC yapılandırması-yeniden yazma eylemleri bölmesi](./media/header-citrix-netscaler-tutorial/header01.png)

1.  **Ekle**' yi seçin ve ardından aşağıdaki adımları uygulayın:

    1. **Ad** için, yeniden yazma eylemi için bir ad girin.

    1. **Tür** için **INSERT_HTTP_HEADER** girin.

    1. **Üst bilgi adı** için bir başlık adı girin (Bu örnekte, _secretıd_ kullanırız).

    1. **İfade** için AAA girin **. Kullanıcısını. ÖZNITELIĞI ("Mysecretıd")**, burada **Mysecretıd** , Citrix ADC 'ye GÖNDERILEN Azure AD SAML talebi.

    1. **Oluştur**’u seçin.

    ![Citrix ADC yapılandırması-yeniden yazma eylem bölmesi oluştur](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Yeniden yazma ilkesi oluşturma

1.  **Appexpert**  >  **yeniden** yazma  >  **ilkelerine** gidin.
 
    ![Citrix ADC yapılandırması-Ilke bölmesini yeniden yaz](./media/header-citrix-netscaler-tutorial/header03.png)

1.  **Ekle**' yi seçin ve ardından aşağıdaki adımları uygulayın:

    1. **Ad** için, yeniden yazma ilkesi için bir ad girin.

    1. **Eylem** için, önceki bölümde oluşturduğunuz yeniden yazma eylemini seçin.

    1. **İfade** için **true** girin.

    1. **Oluştur**’u seçin.

    ![Citrix ADC yapılandırması-yeniden yazma Ilkesi bölmesi oluştur](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Yeniden yazma ilkesini bir sanal sunucuya bağlama

GUI kullanarak bir yeniden yazma ilkesini bir sanal sunucuya bağlamak için:

1. **Trafik yönetimi**  >  **Yük Dengeleme**  >  **sanal sunucularına** gidin.

1. Sanal sunucular listesinde, yeniden yazma ilkesini bağlamak istediğiniz sanal sunucuyu seçin ve **Aç**' ı seçin.

1. **Yük Dengeleme sanal sunucusu** bölmesinde, **Gelişmiş ayarlar** altında **ilkeler**' i seçin. NetScaler örneğiniz için yapılandırılmış tüm ilkeler listede görüntülenir.
 
    !["Ad", "eylem" ve "Ifade" alanları vurgulanmış ve "Oluştur" düğmesi seçili olan "yapılandırma" sekmesini gösteren ekran görüntüsü.](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix ADC yapılandırması-sanal sunucu bölmesini yükle](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Bu sanal sunucuya bağlamak istediğiniz ilkenin adının yanındaki onay kutusunu işaretleyin.
 
    ![Citrix ADC yapılandırması-sanal sunucu trafik Ilkesi bağlama bölmesini yükle](./media/header-citrix-netscaler-tutorial/header08.png)

1. **Tür Seç** iletişim kutusunda:

    1. **Ilke Seç** için **trafik**' i seçin.

    1. **Tür Seç** için **istek**' ı seçin.

    ![Citrix ADC yapılandırma Ilkeleri iletişim kutusu](./media/header-citrix-netscaler-tutorial/header07.png)

1.  **Tamam**’ı seçin. Durum çubuğundaki bir ileti, ilkenin başarıyla yapılandırıldığını gösterir.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Bir talepten öznitelikleri ayıklamak için SAML sunucusunu değiştirin

1.  **Güvenlik**  >  **aaa-uygulama trafik**  >  **ilkeleri**  >  **kimlik doğrulama**  >  **Gelişmiş İlkeler**  >  **Eylemler**  >  **sunucular**' a gidin.

1.  Uygulama için uygun kimlik doğrulama SAML sunucusunu seçin.
 
    ![Citrix ADC yapılandırması-kimlik doğrulama SAML sunucu bölmesini yapılandırma](./media/header-citrix-netscaler-tutorial/header09.png)

1. **Özniteliklerde** sorun, AYıKLAMAK istediğiniz SAML özniteliklerini virgülle ayırarak girin. Örneğimizde özniteliği giriyoruz `mySecretID` .
 
    ![Citrix ADC yapılandırması-Öznitelikler bölmesi](./media/header-citrix-netscaler-tutorial/header10.png)

1. Erişimi doğrulamak için, bir tarayıcıdaki URL 'de, **üstbilgiler koleksiyonu** altında saml özniteliği ' ni arayın.

    ![Citrix ADC yapılandırması-URL 'de üst bilgi koleksiyonu](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-adc-test-user"></a>Citrix ADC test kullanıcısı oluşturma

Bu bölümde, Citrix ADC 'de B. Simon adlı bir Kullanıcı oluşturulur. Citrix ADC, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde gerçekleştirmeniz gereken bir işlem yok. Bir Kullanıcı Citrix ADC 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!NOTE]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [CITRIX ADC istemci desteği ekibine](https://www.citrix.com/contact/technical-support.html)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Citrix ADC oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Citrix ADC oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Citrix ADC kutucuğuna tıkladığınızda bu işlem Citrix ADC oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Citrix ADC 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).