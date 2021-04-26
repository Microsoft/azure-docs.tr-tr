---
title: "Öğretici: Microsoft tarafından Confluence SAML SSO 'SU ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs"
description: Microsoft tarafından Azure Active Directory ve Confluence SAML SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2020
ms.author: jeedes
ms.openlocfilehash: 34365a8bd7a15f502aa89a966adb14807e802cc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737007"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Öğretici: Microsoft tarafından Confluence SAML SSO 'SU ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Microsoft tarafından Azure Active Directory (Azure AD) ile Confluence SAML SSO 'SU nasıl tümleştirileceğini öğreneceksiniz. Microsoft tarafından Confluence SAML SSO 'yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Microsoft tarafından Confluence SAML SSO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Microsoft tarafından belirtilen Luence SAML SSO 'SU için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="description"></a>Açıklama:

Çoklu oturum açmayı etkinleştirmek için, Atlasme Confluence Server ile Microsoft Azure Active Directory hesabınızı kullanın. Böylece, tüm kuruluşunuz kullanıcıları, Confluence uygulamasında oturum açmak için Azure AD kimlik bilgilerini kullanabilir. Bu eklenti, Federasyon için SAML 2,0 kullanır.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Microsoft tarafından Confluence SAML SSO 'SU ile yapılandırmak için aşağıdaki öğeler gereklidir:

- Azure AD aboneliği
- Windows 64-bit sunucusunda (Şirket içi veya bulut IaaS altyapısında) yüklü olan Confluence Server uygulaması
- Confluence sunucusu HTTPS etkin
- Aşağıdaki bölümde, Confluence eklentisi için Desteklenen sürümlerin bahsedildiğini aklınızda bulabilirsiniz.
- Kimlik doğrulaması için özellikle Azure AD oturum açma sayfasında, Confluence sunucusuna Internet üzerinden erişilebilir ve Azure AD 'den belirteci alabilmesi gerekir
- Yönetici kimlik bilgileri, Confluence içinde ayarlanır
- WebSudo, Confluence içinde devre dışı bırakıldı
- Confluence sunucu uygulamasında oluşturulan test kullanıcısı

> [!NOTE]
> Bu öğreticideki adımları test etmek için, Confluence 'ın üretim ortamını kullanmanızı önermiyoruz. Uygulamanın geliştirme veya hazırlama ortamında önce tümleştirmeyi test edin ve ardından üretim ortamını kullanın.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

Başlamak için aşağıdaki öğeler gereklidir:

* Gerekli olmadığı takdirde üretim ortamınızı kullanmayın.
* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Microsoft Çoklu oturum açma (SSO) etkin aboneliği tarafından sağlanan SAML SSO 'SU.

## <a name="supported-versions-of-confluence"></a>Desteklenen Confluence sürümleri

Şu andan itibaren, aşağıdaki Confluence sürümleri desteklenir:

- Confluence: 5,0-5,10
- Confluence: 6.0.1 to 6.15.9
- Confluence: 7.0.1 to 7.9.3

> [!NOTE]
> Lütfen Confluence denetimimizin Ubuntu sürüm 16,04 ' de çalışıp çalışmadığını unutmayın.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Microsoft tarafından desteklenen Confluence SAML SSO 'su **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Galeri 'den Microsoft tarafından Confluence SAML SSO 'SU ekleme

Microsoft tarafından Azure AD 'de Confluence SAML SSO 'SU tümleştirmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Microsoft tarafından Confluence SAML SSO 'SU eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Microsoft tarafından Confluence SAML SSO** yazın.
1. **Microsoft tarafından sonuçlar panelinden Confluence SAML SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-confluence-saml-sso-by-microsoft"></a>Microsoft tarafından Confluence SAML SSO 'SU için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Microsoft tarafından Confluence SAML SSO 'Su Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Microsoft tarafından Confluence SAML SSO 'SU içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Microsoft tarafından Confluence SAML SSO 'SU ile yapılandırmak ve test etmek için aşağıdaki adımları uygulayın:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[MICROSOFT SSO Ile Confluence SAML SSO 'Yu yapılandırın](#configure-confluence-saml-sso-by-microsoft-sso)** .
    1. Microsoft **[test kullanıcısı tarafından Confluence SAML SSO 'Su oluşturun](#create-confluence-saml-sso-by-microsoft-test-user)** ; Microsoft tarafından, kullanıcının Azure AD gösterimine bağlanan, Confluence SAML SSO 'su ile Ilgili olarak B. Simon ' a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, Microsoft uygulama tümleştirmesi **tarafından Confluence SAML SSO** sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/plugins/servlet/saml/auth`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'siyle güncelleştirin. Adlandırılmış bir URL olması durumunda bağlantı noktası isteğe bağlıdır. Bu değerler, Öğreticinin ilerleyen kısımlarında açıklanan Confluence eklentisinin yapılandırması sırasında alınır.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

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

Bu bölümde, Microsoft tarafından Confluence SAML SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Microsoft tarafından Confluence SAML SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Microsoft SSO ile Confluence SAML SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak Confluence örneğiniz için oturum açın.

1. Dişli üzerine gelin ve **eklentilere** tıklayın.

    !["COG" simgesinin seçili olduğunu ve açılan menüde "Eklentiler" vurgulanmış ekran görüntüsü.](./media/confluencemicrosoft-tutorial/addon1.png)

1. Eklentiyi [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=56503)' nden indirin. **Yükleme** eklentisi menüsünü kullanarak Microsoft tarafından sunulan eklentiyi el ile karşıya yükleyin. Eklenti indirmesi, [Microsoft hizmet sözleşmesi](https://www.microsoft.com/servicesagreement/)kapsamında ele alınmıştır.

    !["Eklentiyi karşıya yükle" eylemi seçili olan "Eklentileri Yönet" sayfasını gösteren ekran görüntüsü.](./media/confluencemicrosoft-tutorial/addon12.png)

1. Confluence ters proxy senaryosunu veya yük dengeleyici senaryosunu çalıştırmak için aşağıdaki adımları uygulayın:

    > [!NOTE]
    > Önce aşağıdaki yönergelerle sunucuyu yapılandırmanız ve ardından eklentiyi yüklemeniz gerekir.

    a. JIRA sunucu uygulamasının **server.xml** dosyasına **bağlayıcı** bağlantı noktasına aşağıdaki özniteliği ekleyin.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    !["Bağlayıcı" bağlantı noktasına eklenen özniteliğe sahip "server.xml" dosyasını gösteren ekran görüntüsü.](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. **Sistem ayarlarındaki** **temel URL 'yi** proxy/yük dengeleyiciye göre değiştirin.

    !["Temel URL" vurgulanmış "yönetim ayarları" sayfasını gösteren ekran görüntüsü.](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Eklenti yüklendikten sonra, **Eklentiyi Yönet** bölümünün Kullanıcı tarafından **yüklenen** eklentiler bölümünde görüntülenir. Yeni eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın.

    !["Yapılandır" düğmesinin vurgulandığı "Kullanıcı yüklendi" bölümünü gösteren ekran görüntüsü.](./media/confluencemicrosoft-tutorial/addon15.png)

1. Yapılandırma sayfasında aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açma yapılandırma sayfasını gösteren ekran görüntüsü.](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Meta verileri çözümlemede bir hata olmadığından, uygulamaya yönelik yalnızca bir sertifika eşlendiğinden emin olun. Birden çok sertifika varsa, yönetici meta verileri çözümlerken bir hata alır.

    1. **Meta veri URL 'si** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın ve **Çözümle** düğmesine tıklayın. IDP meta veri URL 'sini okur ve tüm alan bilgilerini doldurur.

    1. Azure portal ' daki **temel SAML yapılandırması** bölümünde tanımlayıcıyı kopyalayın, URL 'yi **yanıtlayın ve URL** değerlerini **tanımlayıcı, Yanıtla URL 'si ve URL** metin kutularına yapıştırın.

    1. **Oturum açma düğmesi adı** ' nda, kuruluşunuzun oturum açma ekranında görmesini istediği düğmenin adını yazın.

    1. **Oturum aç düğmesi açıklaması** ' nda, kuruluşunuzun oturum açma ekranında görmesini istediği düğmenin açıklaması yazın.

    1. **SAML Kullanıcı kimliği konumlarında**, **Konu ifadesinin NameIdentifier öğesinde kullanıcı kimliği** ' ni veya **Kullanıcı kimliği bir öznitelik öğesinde olduğunu** seçin.  Bu KIMLIK, Confluence Kullanıcı KIMLIĞI olmalıdır. Kullanıcı KIMLIĞI eşleşmiyorsa, sistem kullanıcıların oturum açmalarına izin vermez. 

       > [!Note]
       > Varsayılan SAML Kullanıcı KIMLIĞI konumu ad tanımlayıcısıdır. Bunu bir öznitelik seçeneği olarak değiştirebilir ve uygun öznitelik adını girebilirsiniz.

    1. **Bir öznitelik öğesi seçeneğinde Kullanıcı kimliği** ' ni seçerseniz, **öznitelik adı** metin kutusuna kullanıcı kimliğinin beklenildiği özniteliğin adını yazın. 

    1. Federasyon etki alanını (örneğin, ADFS vb.) Azure AD ile kullanıyorsanız, **giriş bölgesi bulmayı etkinleştir** seçeneğini tıklayın ve **etki alanı adını** yapılandırın.

    1. **Etki alanı adı** ' nda, ADFS tabanlı oturum açma durumunda etki alanı adını buraya yazın.

    1. Kullanıcı yapılandırmalardan oturumu kapattığında Azure AD 'den oturumu kapatmak istiyorsanız **Çoklu oturum açmayı etkinleştir** ' i işaretleyin. 

    1. Yalnızca Azure AD kimlik bilgileri aracılığıyla oturum açmak istiyorsanız **Azure oturum açmaya zorla** onay kutusunu etkinleştirin.

       > [!Note]
       > Oturum açma sayfasında, Azure oturum açma özelliği etkinken yönetici oturumu açma için varsayılan oturum açma formunu etkinleştirmek üzere tarayıcı URL 'sinde sorgu parametresini ekleyin.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Ayarları kaydetmek için **Kaydet** düğmesine tıklayın.

       > [!NOTE]
       > Yükleme ve sorun giderme hakkında daha fazla bilgi için [MS Confluence SSO Bağlayıcısı Yönetici Kılavuzu](./ms-confluence-jira-plugin-adminguide.md)' nu ziyaret edin. Ayrıca, yardımınız için bir [SSS](./ms-confluence-jira-plugin-adminguide.md) de vardır.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Microsoft test kullanıcısı tarafından Confluence SAML SSO 'SU oluşturma

Azure AD kullanıcılarının, bir şirket içi sunucuda oturum açmasını sağlamak için, Microsoft tarafından Confluence SAML SSO 'SU için sağlanması gerekir. Microsoft tarafından yapılan Confluence SAML SSO 'SU için, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Yönetici olarak, Confon 'un Şirket içi sunucunuzda oturum açın.

1. Dişli 'ye gelin ve **Kullanıcı yönetimine** tıklayın.

    ![Çalışan Ekle](./media/confluencemicrosoft-tutorial/user1.png)

1. Kullanıcılar bölümünde, **Kullanıcı Ekle** sekmesini tıklatın. **Kullanıcı Ekle** iletişim sayfasında, aşağıdaki adımları uygulayın:

    !["Kullanıcı Ekle" sekmesi seçiliyken ve "Kullanıcı Ekle" bilgilerinin girildiği ekran görüntüsü.](./media/confluencemicrosoft-tutorial/user2.png)

    a. Kullanıcı **adı** metin kutusuna B. Simon gibi kullanıcının e-postasını yazın.

    b. **Tam ad** metin kutusuna B. Simon gibi kullanıcının tam adını yazın.

    c. **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini yazın B.Simon@contoso.com .

    d. **Parola** metin kutusuna B. Simon parolasını yazın.

    e. Parolayı **Onayla** ' ya tıklayarak parolayı yeniden girin.

    f. **Ekle** düğmesine tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz Microsoft oturum açma URL 'SI ile Confluence SAML SSO 'ya yönlendirilir. 

* Microsoft oturum açma URL 'sine doğrudan Confluence SAML SSO 'SU ' ne gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarımın Microsoft kutucuğunda Confluence SAML SSO 'SU ' ne tıkladığınızda, bu, Microsoft oturum açma URL 'SI ile Confluence SAML SSO 'ya yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Microsoft tarafından Confluence SAML SSO 'yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)