---
title: 'Öğretici: Akamai ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Akamai arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: a22a214104357b9ad99238a8db157839a1c9fd46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591954"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Öğretici: Akamai ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Akamai 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Akamai 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Akamai 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Akamai otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure Active Directory ve Akamai kurumsal uygulama erişimi tümleştirmesi, bulutta veya şirket içinde barındırılan eski uygulamalara sorunsuz erişim sağlar. Tümleşik çözüm, [Azure AD koşullu erişim](../conditional-access/overview.md), [Azure AD kimlik koruması](../identity-protection/overview-identity-protection.md) ve [Azure AD Identity Governance](../governance/identity-governance-overview.md) uygulama değişiklikleri veya aracı yüklemesi olmadan eski uygulama erişimi için Azure Active Directory tüm modern olanaklarından faydalanmasıdır.

Aşağıdaki görüntüde, Akamai EAA 'nın daha geniş karma güvenli erişim senaryosuna uygun olduğu açıklanmaktadır.

![Akamai EAA, daha geniş karma güvenli erişim senaryosuna uyar](./media/header-akamai-tutorial/introduction-1.png)

### <a name="key-authentication-scenarios"></a>Anahtar kimlik doğrulama senaryoları

Açık KIMLIK Connect, SAML ve WS-Besde gibi modern kimlik doğrulama protokolleri için Azure Active Directory yerel tümleştirme desteğini kullanarak, Azure AD ile hem iç hem de dış erişim için eski tabanlı kimlik doğrulama uygulamalarına yönelik güvenli erişimi genişleterek bu uygulamalara modern senaryolar (örneğin, parola-daha az erişim) sağlar. Şunları içerir:

* Üst bilgi tabanlı kimlik doğrulama uygulamaları
* Uzak Masaüstü
* SSH (Secure Shell)
* Kerberos kimlik doğrulama uygulamaları
* VNC (sanal ağ bilgi Işlem)
* Anonim kimlik doğrulama veya yerleşik kimlik doğrulaması uygulamaları
* NTLM kimlik doğrulama uygulamaları (Kullanıcı için çift istemlerle koruma)
* Forms-Based uygulaması (Kullanıcı için çift istemlerle koruma)

### <a name="integration-scenarios"></a>Tümleştirme senaryoları

Microsoft ve Akamai EAA ortaklığı, iş gereksinimlerinize göre birden çok tümleştirme senaryosunu destekleyerek iş gereksinimlerinizi karşılamak için esneklik sağlar. Bunlar tüm uygulamalarda sıfır günlük kapsam sağlamak için kullanılabilir ve uygun ilke sınıflandırmalarını aşamalı olarak sınıflandırabilir ve yapılandırır.

#### <a name="integration-scenario-1"></a>Tümleştirme senaryosu 1

Akamai EAA, Azure AD 'de tek bir uygulama olarak yapılandırılır. Yönetici, uygulama üzerinde koşullu erişim ilkesini yapılandırabilir ve koşullar karşılandıktan sonra kullanıcılar Akamai EAA portalına erişim kazanabilirler.

**Uzmanları**:

* IDP 'yi yalnızca bir kez yapılandırmanız gerekir.

**Dezavantajlarını**:

* Kullanıcıların iki uygulama portalı vardır.

* Tüm uygulamalar için tek bir genel koşullu erişim ilkesi kapsamı.

![Tümleştirme senaryosu 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>Tümleştirme senaryosu 2

Akamai EAA uygulaması, Azure AD portalında ayrı ayrı ayarlanır. Yönetici, uygulamalar üzerinde tek tek koşullu erişim ilkesini yapılandırabilir ve koşullar karşılandıktan sonra kullanıcılar doğrudan belirli bir uygulamaya yeniden yönlendirilebilir.

**Uzmanları**:

* Ayrı CA Ilkeleri tanımlayabilirsiniz.

* Tüm uygulamalar 0365 waffle ve myApps.microsoft.com panelinde temsil edilir.


**Dezavantajlarını**:

* Birden çok ıDP 'yi yapılandırmanız gerekir.

![Tümleştirme senaryosu 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Akamai çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

- Akamai, ıDP tarafından başlatılan SSO 'yu destekler.

#### <a name="important"></a>Önemli

Aşağıda listelenen tüm kurulum, **tümleştirme senaryosu 1** ve **Senaryo 2** için aynıdır. **Tümleştirme senaryosu 2** Için, Akamai EAA 'da tek BIR IDP ayarlamanız gerekır ve URL ÖZELLIĞININ uygulama URL 'sine işaret etmek için değiştirilmesi gerekir.

![Akamai Enterprise Application Access 'te AZURESSO-SP için Genel sekmesinin ekran görüntüsü. Kimlik doğrulama yapılandırma URL 'SI alanı vurgulanır.](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>Galeriden Akamai ekleme

Akamai tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Akamai yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Akamai** yazın.
1. Sonuçlar panelinden **Akamai** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Akamai için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Akamai ile yapılandırın ve test edin. SSO 'nun çalışması için, Akamai içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Akamai ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[AKAMAI SSO 'Yu yapılandırın](#configure-akamai-sso)** .
    * **[IDP 'yi ayarlama](#setting-up-idp)**
    * **[Üst bilgi tabanlı kimlik doğrulaması](#header-based-authentication)**
    * **[Uzak Masaüstü](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos Kimlik Doğrulaması](#kerberos-authentication)**
    * Kullanıcının Azure AD gösterimine bağlı olan Akamai 'de B. Simon 'ya karşılık gelen bir **[Akamai test kullanıcısı oluşturun](#create-akamai-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Akamai** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Akamai istemci destek ekibine](https://www.akamai.com/us/en/contact-us/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Akamai ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Akamai 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Akamai**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-akamai-sso"></a>Akamai SSO 'yu yapılandırma

### <a name="setting-up-idp"></a>IDP 'yi ayarlama

**AKAMAI EAA ıDP yapılandırması**

1. **Akamai kurumsal uygulama erişim** konsolunda oturum açın.
1. **Akamai EAA konsolunda** **kimlik**  >  **kimlik sağlayıcıları** ' nı seçin ve **kimlik sağlayıcısı ekle**' ye tıklayın.

    ![Akamai EAA konsolu kimlik sağlayıcıları penceresinin ekran görüntüsü. Kimlik menüsünde kimlik sağlayıcıları ' nı seçin ve kimlik sağlayıcısı ekle ' yi seçin.](./media/header-akamai-tutorial/configure-1.png)

1. **Yeni kimlik sağlayıcısı oluştur** sayfasında aşağıdaki adımları uygulayın:

    ![Akamai EAA konsolundaki yeni kimlik sağlayıcıları oluştur iletişim kutusunun ekran görüntüsü.](./media/header-akamai-tutorial/configure-2.png)

    a. **Benzersiz adı** belirtin.

    b. **Üçüncü taraf SAML** ' yi seçin ve **kimlik sağlayıcısı oluştur ve Yapılandır**' a tıklayın.

### <a name="general-settings"></a>Genel ayarlar

1. **Kimlik kesme noktası** -(SP temel URL 'Si – Azure AD yapılandırması için kullanılacak) adını belirtin.

    > [!NOTE]
    > Kendi özel etki alanınızı (bir DNS girişi ve bir sertifika gerektirir) seçebilirsiniz. Bu örnekte Akamai etki alanını kullanacağız.

1. **Akamai bulut bölgesi** -uygun bulut bölgesini seçin.
1. **Sertifika doğrulama** -Akamai belgelerini denetleyin (isteğe bağlı).

    ![Kimlik kesme, Akamai bulut bölgesi ve sertifika doğrulama ayarlarını gösteren Akamai EAA konsolunun Genel sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>Kimlik doğrulama yapılandırması

1. URL – kimlik kesme noktası ile aynı URL 'YI belirtin (Bu, kullanıcıların kimlik doğrulamasından sonra yeniden yönlendirileceği yerdir).
2. Oturum kapatma URL 'SI: oturum kapatma URL 'sini güncelleştirin.
3. SAML Isteği imzala: varsayılan seçilmemiş.
4. IDP meta veri dosyası için, uygulamayı Azure AD konsoluna ekleyin.

    ![URL, oturum kapatma URL 'si, Imza SAML Isteği ve ıDP meta veri dosyası için ayarları gösteren Akamai EAA konsolu kimlik doğrulama yapılandırması ekran görüntüsü.](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>Oturum ayarları

Ayarları varsayılan olarak bırakın.

![Akamai EAA Konsolu oturum ayarları iletişim kutusunun ekran görüntüsü.](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>Dizinler

Dizin yapılandırmasını atlayın.

![Akamai EAA konsol dizinleri sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Özelleştirme Kullanıcı arabirimi

IDP 'ye özelleştirme ekleyebilirsiniz.

![Kullanıcı arabirimi, dil ayarları ve temalara yönelik ayarları gösteren Akamai EAA konsolu özelleştirme sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>Gelişmiş Ayarlar

Daha fazla bilgi için Gelişmiş ayarları atlayın/Akamai belgelerine başvurun.

![EAA Istemcisi, gelişmiş ve OıDC 'in SAML köprülemesi için ayarları gösteren Akamai EAA konsolu Gelişmiş ayarları sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>Dağıtım

1. Kimlik sağlayıcısını Dağıt ' a tıklayın.

    ![Identity sağlayıcısını dağıt düğmesini gösteren Akamai EAA konsolunun dağıtım sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/deployment.png)

2. Dağıtımın başarılı olduğunu doğrulayın.

### <a name="header-based-authentication"></a>Üst bilgi tabanlı kimlik doğrulaması

Akamai üst bilgi tabanlı kimlik doğrulaması

1. **Özel http** formunu uygulama ekleme Sihirbazı ' nı seçin.

    ![Access Apps bölümünde listelenen CustomHTTP 'yi gösteren Akamai EAA konsolunun uygulama ekleme Sihirbazı ekran görüntüsü.](./media/header-akamai-tutorial/configure-5.png)

2. **Uygulama adı** ve **açıklamasını** girin.

    ![Uygulama adı ve açıklama ayarlarını gösteren özel bir HTTP uygulaması iletişim kutusunun ekran görüntüsü.](./media/header-akamai-tutorial/configure-6.png)

    ![MYHEADERAPP için genel ayarları gösteren Akamai EAA konsolunun Genel sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-7.png)

    ![Sertifika ve konum ayarlarını gösteren Akamai EAA konsolunun ekran görüntüsü.](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>Kimlik Doğrulaması

1. **Kimlik doğrulama** sekmesini seçin.

    ![Kimlik doğrulaması sekmesi seçili Akamai EAA konsolunun ekran görüntüsü.](./media/header-akamai-tutorial/configure-9.png)

2. **Kimlik sağlayıcısını** atayın.

    ![Azure AD SSO 'ya ayarlanmış kimlik sağlayıcısını gösteren MYHEADERAPP için Akamai EAA konsolunun kimlik doğrulama sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>Hizmetler

Kaydet ' e tıklayın ve kimlik doğrulaması ' na gidin.

![Sağ alt köşedeki escedsettings 'e Kaydet ve git 'e Git düğmesinin gösterildiği MYHEADERAPP için Akamai EAA konsol Hizmetleri sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>Gelişmiş Ayarlar

1. **MÜŞTERI http üstbilgileri** altında **Customerheader** ve **SAML özniteliğini** belirtin.

    ![Kimlik doğrulaması altında vurgulanan SSO kayıtlı URL alanını gösteren Akamai EAA konsolu Gelişmiş ayarları sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-12.png)

1. **Kaydet ve dağıtıma git** düğmesine tıklayın.

    ![Sağ alt köşedeki kaydet ve dağıtıma Git düğmesine gösteren Akamai EAA konsolu Gelişmiş ayarları sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>Uygulamayı dağıtma

1. **Uygulamayı dağıt** düğmesine tıklayın.

    ![Uygulamayı dağıt düğmesini gösteren Akamai EAA konsolunun dağıtım sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-14.png)

1. Uygulamanın başarıyla dağıtıldığını doğrulayın.

    ![Akamai EAA konsolunun dağıtım sekmesinin ekran görüntüsü: "uygulama başarıyla dağıtıldı".](./media/header-akamai-tutorial/configure-15.png)

1. End-User deneyimi.

    ![Arka plan görüntüsü ve oturum açma iletişim kutusuyla myapps.microsoft.com için açılış ekranının ekran görüntüsü.](./media/header-akamai-tutorial/end-user-1.png)

    ![Eklenti, HRWEB, Akamai-CorpApps, harcama, Grup ve erişim gözden geçirmeleri simgeleri içeren bir uygulamalar penceresinin parçasını gösteren ekran görüntüsü. ](./media/header-akamai-tutorial/end-user-2.png)

1. Koşullu erişim.

    ![İleti ekran görüntüsü: oturum açma isteğini onaylama. Mobil cihazınıza bir bildirim gönderdik. Lütfen devam etmek için yanıtlayın.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![MyHeaderApp simgesini gösteren bir uygulamalar ekranının ekran görüntüsü.](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>Uzak Masaüstü

1. Uygulama ekleme sihirbazından **RDP** ' yi seçin.

    ![Erişim uygulamaları bölümündeki uygulamalar arasında listelenen RDP 'yi gösteren Akamai EAA konsolunun uygulama ekleme Sihirbazı ekran görüntüsü.](./media/header-akamai-tutorial/configure-16.png)

1. **Uygulama adı** ve **açıklamasını** girin.

    ![Uygulama adı ve açıklamasına yönelik ayarları gösteren bir RDP uygulaması iletişim kutusunun ekran görüntüsü.](./media/header-akamai-tutorial/configure-17.png)

    ![SECRETRDPAPP için uygulama kimliği ayarlarını gösteren Akamai EAA konsolunun Genel sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-18.png)

1. Buna hizmet edecek bağlayıcıyı belirtin.

    ![Sertifika ve konum ayarlarını gösteren Akamai EAA konsolunun ekran görüntüsü. İlişkili bağlayıcılar USWST-CON1 olarak ayarlanmıştır.](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>Kimlik Doğrulaması

**Kaydet ' e tıklayın ve hizmetler 'e gidin**.

![SECRETRDPAPP için Akamai EAA konsolunun kimlik doğrulama sekmesinin ekran görüntüsü, Kaydet ve hizmetlere Git düğmesinin sağ alt köşesinde olduğunu gösterir.](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>Hizmetler

**Kaydet ' e tıklayın ve Gelişmiş ayarlar**' a gidin.

![Sağ alt köşedeki go Cedpapp için Kaydet ve git ' i gösteren Akamai EAA konsol Hizmetleri sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>Gelişmiş Ayarlar

1. **Kaydet ' e tıklayın ve dağıtıma gidin**.

    ![Uzak Masaüstü yapılandırması ayarlarını gösteren SECRETRDPAPP için Akamai EAA konsolunun Gelişmiş ayarlar sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-22.png)

    ![SECRETRDPAPP için Akamai EAA konsolunun Gelişmiş ayarlar sekmesinin ekran görüntüsü, kimlik doğrulama ve sistem durumu denetimi yapılandırması ayarlarını gösterir.](./media/header-akamai-tutorial/configure-23.png)

    ![Sağ alt köşedeki ve dağıtıma Git düğmesine sahip SECRETRDPAPP için özel HTTP üstbilgileri ayarlarının Akamai EAA konsolunun ekran görüntüsü.](./media/header-akamai-tutorial/configure-24.png)

1. End-User deneyim

    ![Arka plan resmi ve oturum açma iletişim kutusuyla myapps.microsoft.com penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/end-user-3.png)

    ![Eklenti, HRWEB, Akamai-CorpApps, harcama, Grup ve erişim gözden geçirmeleri simgeleri içeren myapps.microsoft.com apps penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/end-user-2.png)

1. Koşullu Erişim

    ![Koşullu erişim iletisinin ekran görüntüsü: oturum açma isteğini onaylayın. Mobil cihazınıza bir bildirim gönderdik. Lütfen devam etmek için yanıtlayın.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp ve SecretRDPApp simgelerini gösteren bir uygulamalar ekranının ekran görüntüsü.](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Genel Kullanıcı simgelerini gösteren Windows Server 2012 RS ekranının ekran görüntüsü. Yönetici, user0 ve Kullanıcı1 simgeleri oturum açmış olduklarını gösterir.](./media/header-akamai-tutorial/conditional-access-6.png)

1. Alternatif olarak, RDP uygulama URL 'sini de doğrudan yazabilirsiniz.

#### <a name="ssh"></a>SSH

1. Uygulama Ekle ' ye gidin, **SSH**' ı seçin.

    ![Akamai EAA konsolunun uygulama ekleme Sihirbazı 'nın, erişim uygulamaları bölümündeki uygulamalar arasında listelenen SSH 'yi gösteren ekran görüntüsü.](./media/header-akamai-tutorial/configure-25.png)

1. **Uygulama adı** ve **açıklamasını** girin.

    ![Uygulama adı ve açıklamasına yönelik ayarları gösteren bir SSH uygulaması iletişim kutusunun ekran görüntüsü.](./media/header-akamai-tutorial/configure-26.png)

1. Uygulama kimliğini yapılandırın.

    ![SSH-SECURE için uygulama kimliği ayarlarını gösteren Akamai EAA konsolunun Genel sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-27.png)

    a. Ad/açıklama belirtin.

    b. SSH için uygulama sunucusu IP/FQDN ve bağlantı noktası belirtin.

    c. SSH kullanıcı adını/parolasını belirtin * Check Akamai EAA.

    d. Dış ana bilgisayar adını belirtin.

    e. Bağlayıcının konumunu belirtin ve bağlayıcıyı seçin.

#### <a name="authentication"></a>Kimlik Doğrulaması

Kaydet ' **e tıklayın ve hizmetlere gidin**.

![SSH-SECURE için Akamai EAA konsol kimlik doğrulama sekmesinin ekran görüntüsü, Kaydet ve hizmetlere Git düğmesinin sağ alt köşesinde olduğunu gösterir.](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>Hizmetler

**Kaydet ' e tıklayın ve Gelişmiş ayarlar**' a gidin.

![SSH-SECURE için Akamai EAA konsol Hizmetleri sekmesinin ekran görüntüsü-sağ alt köşedeki kaydet ve gelişmiş git Cedsettings düğmesine bakın.](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>Gelişmiş Ayarlar

Kaydet ve dağıtıma git ' e tıklayın.

![SSH-SECURE için Akamai EAA konsolunun Gelişmiş ayarlar sekmesinin ekran görüntüsü, kimlik doğrulama ve sistem durumu denetimi yapılandırması ayarlarını gösterir.](./media/header-akamai-tutorial/configure-30.png)

![Sağ alt köşedeki kaydet ve dağıtıma Git düğmesi ile SSH ile GÜVENLI için Akamai EAA konsolunun özel HTTP üstbilgileri ayarlarının ekran görüntüsü.](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>Dağıtım

1. **Uygulamayı dağıt**' a tıklayın.

    ![Uygulama dağıt düğmesini gösteren SSH-SECURE için Akamai EAA konsolunun dağıtım sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-32.png)

1. End-User deneyim

    ![Myapps.microsoft.com penceresi oturum açma iletişim kutusunun ekran görüntüsü.](./media/header-akamai-tutorial/end-user-3.png)

    ![Eklenti, HRWEB, Akamai-CorpApps, harcama, Grup ve erişim gözden geçirmeleri için simgeler gösteren myapps.microsoft.com için uygulamalar penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/end-user-4.png)

1. Koşullu Erişim

    ![İletiyi gösteren ekran görüntüsü: oturum açma isteğini onaylama. Mobil cihazınıza bir bildirim gönderdik. Lütfen devam etmek için yanıtlayın.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp, SSH Secure ve SecretRDPApp simgelerini gösteren bir uygulamalar ekranının ekran görüntüsü.](./media/header-akamai-tutorial/conditional-access-7.png)

    ![Parola istemi gösteren ssh-secure-go.akamai-access.com için bir komut penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/conditional-access-8.png)

    ![Uygulama hakkındaki bilgileri gösteren ve komut istemi görüntüleyen ssh-secure-go.akamai-access.com için bir komut penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Kerberos Kimlik Doğrulaması

Aşağıdaki örnekte, bir Iç Web sunucusu yayımlayacağız <code>http://frp-app1.superdemo.live</code> ve KCD kullanarak SSO 'yu etkinleştireceğiz.

#### <a name="general-tab"></a>Genel Sekmesi

![MYKERBOROSAPP için Akamai EAA konsolunun Genel sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>Kimlik doğrulama sekmesi

Kimlik sağlayıcısını atayın.

![Azure AD SSO 'ya ayarlanmış kimlik sağlayıcısını gösteren MYKERBOROSAPP için Akamai EAA konsolunun kimlik doğrulama sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>Hizmetler sekmesi

![MYKERBOROSAPP için Akamai EAA konsol Hizmetleri sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>Gelişmiş Ayarlar

![Ilgili uygulamalar ve kimlik doğrulaması için ayarları gösteren MYKERBOROSAPP için Akamai EAA konsolunun Gelişmiş ayarlar sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> Web sunucusu için SPN, SPN@Domain `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` Bu tanıtım için: EX biçimindedir. Ayarların geri kalanını varsayılan olarak bırakın.

#### <a name="deployment-tab"></a>Dağıtım sekmesi

![Uygulamayı dağıt düğmesini gösteren MYKERBOROSAPP için Akamai EAA konsolunun dağıtım sekmesinin ekran görüntüsü.](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>Dizin ekleniyor

1. Açılan listeden **ad** ' ı seçin.

    ![Dizin türünün açılan kutusunda AD seçiliyken yeni dizin oluştur iletişim kutusunu gösteren Akamai EAA konsol dizinleri penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-33.png)

1. Gerekli verileri sağlayın.

    ![DirectoryName, dizin hizmeti, bağlayıcı ve öznitelik eşleme ayarlarına sahip Akamai EAA konsol üst DEMOETKIN penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/configure-34.png)

1. Dizin oluşturmayı doğrulayın.

    ![Dizin superdemo.live eklendiğini gösteren Akamai EAA konsol dizinleri penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/directory-domain.png)

1. Erişim gerektirecek grupları/OU 'Ları ekleyin.

    ![Superdemo.live dizini için ayarların ekran görüntüsü. Grup veya OU eklemek için seçtiğiniz simge vurgulanır.](./media/header-akamai-tutorial/add-group.png)

1. Aşağıdaki grupta EAAGroup adı verilir ve 1 üyesi vardır.

    ![Akamai EAA konsolunun ETKIN DIZIN penceresinde bulunduğu ekran görüntüsü. 1 kullanıcısı olan EAAGroup, gruplar altında listelenmiştir.](./media/header-akamai-tutorial/eaagroup.png)

1. **Kimlik**  >  **kimlik sağlayıcıları** ' na tıklayıp **dizinler** sekmesine tıklayın ve **Dizin ata**' ya tıklayın.

    ![Azure AD SSO için Akamai EAA konsol dizinleri sekmesinin ekran görüntüsü, şu anda atanmış olan dizinler listesinde superdemo.live gösterir.](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>HER adım için KCD temsilcisini yapılandırma

#### <a name="step-1-create-an-account"></a>1. Adım: hesap oluşturma 

1. Örnekte **Eaatemsili** adlı bir hesap kullanacağız. Bunu, **Kullanıcı ve bilgisayar** Snappin Active Directory kullanarak yapabilirsiniz.

    ![Azure AD SSO için Akamai EAA konsol dizinleri sekmesinin ekran görüntüsü. Dizin superdemo.live, atanmış olan dizinler altında listelenir.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > Kullanıcı adının **kimlik kesme adına** göre belirli bir biçimde olması gerekebilir. Şekil 1 ' den **corpapps.Login.go.Akamai-Access.com** olduğunu görtik

1. Kullanıcı oturum açma adı şu şekilde olacaktır:`HTTP/corpapps.login.go.akamai-access.com`

    ![Ilk adı "Eaatemsili" olarak ayarlanan ve Kullanıcı oturum açma adı HTTP/corpapps. Login. go. Akamai-Access. com olarak ayarlanan Eaatemsili özelliklerini gösteren ekran görüntüsü.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>2. Adım: SPN 'YI bu hesap için yapılandırma

1. Bu örneğe göre SPN aşağıdaki gibi olacaktır.

2. Setspn-s **http/corpapps. Login. go. Akamai-Access. com eaatemsili**

    ![Setspn-s http/corpapps. Login. go. Akamai-Access. com eaatemsili komutunun sonuçlarını gösteren bir yönetici komut Isteminin ekran görüntüsü.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>3. Adım: temsilciyi yapılandırma

1. Eaatemsili hesabı için, temsili sekmesine tıklayın.

    ![SPN 'YI yapılandırma komutunu gösteren yönetici komut Isteminin ekran görüntüsü.](./media/header-akamai-tutorial/spn.png)

    * Herhangi bir kimlik doğrulama protokolünü kullan öğesini belirtin.
    * Ekle ' ye tıklayın ve Kerberos Web sitesi için uygulama havuzu hesabını ekleyin. Doğru yapılandırılmışsa doğru SPN 'ye otomatik olarak çözümlenmelidir.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>4. Adım: AKAMAI EAA için bir keytab dosyası oluşturma

1. Genel sözdizimi aşağıda verilmiştir.

1. Ktpass/Out ActiveDirectorydomain. keytab/Princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /Pass + rdnPass/şifre All/ptype KRB5_NT_PRINCIPAL

1. Örnek açıklanacak

    | Kod Parçacığı | Açıklama |
    | - | - |
    | Ktpass/Out EAADemo. keytab | Çıkış keytab dosyasının adı |
    | /Princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | EAA temsili hesabı |
    | /Pass RANDOMPASS | EAA temsili hesap parolası |
    | /şifre tüm pType KRB5_NT_PRINCIPAL | Akamai EAA belgelerine başvurun |
    | | |

1. Ktpass/Out EAADemo. keytab/Princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /Pass RANDOMPASS/şifre tüm ptype KRB5_NT_PRINCIPAL

    ![AKAMAI EAA için bir keytab dosyası oluşturma komutunun sonuçlarını gösteren bir yönetici komut Isteminin ekran görüntüsü.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>5. Adım: AKAMAI EAA konsolundaki keytab 'ı Içeri aktarma

1. **Sistem**  >  **keysekmeleri**' ne tıklayın.

    ![Akamai EAA konsolunun, sistem menüsünden Seçili olan anahtar sekmelerini gösteren ekran görüntüsü.](./media/header-akamai-tutorial/keytabs.png)

1. Keytab türünde **Kerberos temsili**' ni seçin.

    ![Keytab ayarlarını gösteren Akamai EAA konsolunun EAAKEYTAB ekranının ekran görüntüsü. Keytab türü, Kerberos temsili olarak ayarlanır.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Keytab 'ın dağıtıldığından ve doğrulandığından emin olun.

    ![Akamai EAA konsolunun anahtar sekmeleri ekranının ekran görüntüsü, "keytab dağıtıldı ve doğrulandı" olarak EAA keytab.](./media/header-akamai-tutorial/keytabs-2.png)

1. Kullanıcı Deneyimi

    ![Myapps.microsoft.com adresindeki oturum açma iletişim kutusunun ekran görüntüsü. ](./media/header-akamai-tutorial/end-user-3.png)

    ![Uygulama simgelerini gösteren myapps.microsoft.com için uygulamalar penceresinin ekran görüntüsü.](./media/header-akamai-tutorial/end-user-4.png)

1. Koşullu Erişim

    ![Bir onaylama oturum açma isteği iletisini gösteren ekran görüntüsü. ileti.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp, SSH Secure, SecretRDPApp ve myKerberosApp için simgeler gösteren bir uygulamalar ekranının ekran görüntüsü.](./media/header-akamai-tutorial/conditional-access-10.png)

    ![MyKerberosApp için giriş ekranının ekran görüntüsü. "Hoş geldiniz superdemo\user1" iletisi bir arka plan görüntüsü üzerinde görüntülenir.](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Akamai test kullanıcısı oluştur

Bu bölümde, Akamai içinde B. Simon adlı bir Kullanıcı oluşturacaksınız. Kullanıcıları Akamai platformunda eklemek için [Akamai istemci desteği ekibi](https://www.akamai.com/us/en/contact-us/) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. 

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Akamai otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Akamai kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Akamai için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Akamai yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).