---
title: 'Öğretici: Zscaler Beta ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Zscaler Beta arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735616"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Öğretici: Zscaler Beta ile tümleştirme Azure Active Directory

Bu öğreticide, Zscaler Beta Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Zscaler Beta 'yı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Zscaler Beta 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zscaler Beta sürümünde otomatik olarak oturum açmaya izin verin. Bu erişim denetimine çoklu oturum açma (SSO) adı verilir.
* Azure portal kullanarak hesaplarınızı tek bir merkezi konumda yönetin.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Zscaler Beta ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma kullanan bir Zscaler Beta aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Zscaler Beta, **SP** tarafından başlatılan SSO 'yu destekler.
* Zscaler Beta **, tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Galeriden Zscaler Beta ekleme

Zscaler Beta 'nın tümleştirmesini Azure AD 'ye yapılandırmak için, Galeriden Zscaler Beta sürümünü yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zscaler Beta** yazın.
1. Sonuçlar panelinden **Zscaler Beta** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Zscaler Beta için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Zscaler Beta Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Zscaler Beta sürümünde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Zscaler Beta ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:


1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Zscaler Beta SSO 'Yu yapılandırın](#configure-zscaler-beta-sso)** .
    1. **[Zscaler Beta test kullanıcısı oluşturun](#create-zscaler-beta-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Zscaler Beta sürümünde B. Simon 'un bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zscaler Beta** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** kutusunda, kullanıcılarınızın Zscaler Beta beta uygulamasında oturum açmak için kullandığı URL 'yi girin.

    > [!NOTE]
    > Değer gerçek değildir. Değeri, gerçek oturum açma URL 'SI değeriyle güncelleştirin. Değeri almak için [Zscaler Beta istemci destek ekibine](https://www.zscaler.com/company/contact)başvurun.

5. Zscaler Beta uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemeniz gerekir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** ' yi seçin.

    ![Kullanıcı öznitelikleri iletişim kutusu](common/edit-attribute.png)

6. Zscaler Beta uygulaması, daha az sayıda özniteliğin SAML yanıtına geri geçirilmesini bekliyor. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliğini eklemek için bu adımları izleyin.
    
    | Name | Kaynak özniteliği | 
    | ---------------| --------------- |
    | Üyesi  | Kullanıcı. atandroles |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' yi seçin.

    b. **Ad** kutusuna, bu satır için gösterilen öznitelik adını girin.

    c. **Ad alanı** kutusunu boş bırakın.

    d. **Kaynak** için **öznitelik**' i seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini girin.

    f. **Tamam**’ı seçin.

    örneğin: **Kaydet**’i seçin.

    > [!NOTE]
    > Azure AD 'de rolün nasıl yapılandırılacağını öğrenmek için lütfen [buraya](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) tıklayın.

7. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, sertifikayı indirmek için **Indir** ' i seçin **(base64)**. Bu dosyayı bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

8. **Zscaler Beta 'Yı ayarla** bölümünde, gereksinimleriniz Için gereken URL 'leri kopyalayın:

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

Bu bölümde, Zscaler Beta sürümüne erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Zscaler Beta**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Rolleri yukarıda açıklanan şekilde ayarlarsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-zscaler-beta-sso"></a>Zscaler Beta SSO 'yu yapılandırma

1. Zscaler Beta sürümünde yapılandırmayı otomatikleştirmek için, **uzantıyı yükler**' i seçerek **uygulamalarım güvenli oturum açma tarayıcı uzantısını** yükler.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Uzantıyı tarayıcıya ekledikten sonra, **set up Zscaler Beta** ' yı seçtiğinizde sizi Zscaler Beta uygulamasına yönlendirir. Buradan, Zscaler Beta sürümünde oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı uygulamayı sizin için otomatik olarak yapılandırır ve 3 ile 6 arasındaki adımları otomatikleştirir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Zscaler Beta 'yı el ile ayarlamak için yeni bir Web tarayıcısı penceresi açın. Zscaler Beta şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları izleyin.

4. **Yönetim**  >  **kimlik doğrulaması**  >  **kimlik doğrulama ayarları**' na gidin ve aşağıdaki adımları izleyin.
   
    ![Yönetim](./media/zscaler-beta-tutorial/ic800206.png "Yönetim")

    a. **Kimlik doğrulama türü** altında **SAML**' yi seçin.

    b. **SAML Yapılandır**' ı seçin.

5. **SAML Düzenle** penceresinde aşağıdaki adımları izleyin: 
            
    ![Kullanıcı & kimlik doğrulaması yönetme](./media/zscaler-beta-tutorial/ic800208.png "Kullanıcı & kimlik doğrulaması yönetme")
    
    a. **SAML portalı URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    b. **Oturum açma adı öznitelik** kutusuna **NameID** girin.

    c. **Ortak SSL sertifikası** kutusunda, Azure Portal INDIRDIĞINIZ Azure SAML imzalama sertifikasını karşıya yüklemek Için **karşıya yükle** ' yi seçin.

    d. **SAML otomatik sağlamayı etkinleştir**' i açın.

    e. **Kullanıcı görünen adı öznitelik** kutusunda, DisplayName ÖZNITELIKLERI için SAML oto sağlamayı etkinleştirmek istiyorsanız **DisplayName** yazın.

    f. **Grup adı öznitelik** kutusunda, memberOf ÖZNITELIKLERI için SAML oto sağlamayı etkinleştirmek Istiyorsanız, **memberOf** yazın.

    örneğin: Bölüm **adı öznitelik** kutusuna departman ÖZNITELIKLERI için SAML oto sağlamayı etkinleştirmek istiyorsanız **bölüm** girin.

    h. **Kaydet**’i seçin.

6. **Kullanıcı kimlik doğrulamasını Yapılandır** iletişim sayfasında, aşağıdaki adımları izleyin:

    ![Etkinleştirme menüsü ve Etkinleştir düğmesi](./media/zscaler-beta-tutorial/ic800207.png)

    a. Sol alt taraftaki **etkinleştirme** menüsünün üzerine gelin.

    b. **Etkinleştir**' i seçin.

## <a name="configure-proxy-settings&quot;></a>Ara sunucu ayarlarını yapılandırma
Internet Explorer 'da proxy ayarlarını yapılandırmak için aşağıdaki adımları izleyin.

1. **Internet Explorer 'ı** başlatın.

2. **Internet seçenekleri** iletişim kutusunu açmak için **Araçlar** menüsünde **Internet seçenekleri** ' ni seçin. 
    
     ![Internet Seçenekleri iletişim kutusu](./media/zscaler-beta-tutorial/ic769492.png &quot;Internet seçenekleri")

3. **Bağlantılar** sekmesini seçin. 
  
     ![Bağlantılar sekmesi](./media/zscaler-beta-tutorial/ic769493.png "Bağlantılar")

4. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu açmak için **LAN ayarları** ' nı seçin.

5. **Proxy sunucusu** bölümünde, şu adımları izleyin: 
   
    ![Proxy sunucusu bölümü](./media/zscaler-beta-tutorial/ic769494.png "Proxy sunucu")

    a. **LAN için bir proxy sunucusu kullan** onay kutusunu seçin.

    b. **Adres** kutusuna **ağ geçidi girin. Zscaler Beta.net**.

    c. **Bağlantı noktası** kutusuna **80** girin.

    d. **Yerel adresler için proxy sunucusunu atla** onay kutusunu seçin.

    e. **Yerel alan ağı (LAN) ayarları** iletişim kutusunu kapatmak için **Tamam ' ı** seçin.

6. **Internet seçenekleri** iletişim kutusunu kapatmak için **Tamam ' ı** seçin.

### <a name="create-zscaler-beta-test-user"></a>Zscaler Beta test kullanıcısı oluşturma

Bu bölümde, Cetta Simon kullanıcısı Zscaler Beta sürümünde oluşturulur. Zscaler Beta, varsayılan olarak etkinleştirilen **tam zamanında Kullanıcı sağlamayı** destekler. Bu bölümde yapmanız için bir şey yoktur. Zscaler Beta sürümünde bir kullanıcı zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>Bir kullanıcıyı el ile oluşturmak için [Zscaler Beta destek ekibine](https://www.zscaler.com/company/contact)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Zscaler Beta oturum açma URL 'sine yeniden yönlendirilir. 

* Zscaler Beta oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki Zscaler Beta kutucuğuna tıkladığınızda, bu, Zscaler Beta oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Zscaler Beta 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
