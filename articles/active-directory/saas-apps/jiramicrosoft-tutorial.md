---
title: "Öğretici: Microsoft tarafından JıRA SAML SSO 'SU ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs"
description: Microsoft tarafından Azure Active Directory ve JIRA SAML SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 75b8230a1027bbf3ff3d73fb35ce65107c2db7e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730680"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Öğretici: Microsoft tarafından JıRA SAML SSO 'SU ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Microsoft tarafından Azure Active Directory (Azure AD) ile JıRA SAML SSO 'SU nasıl tümleştirileceğini öğreneceksiniz. JIRA SAML SSO 'yu Microsoft tarafından Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Microsoft tarafından JıRA SAML SSO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Microsoft tarafından kullanılabilen BIR SAML SSO 'SU için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="description"></a>Description

Çoklu oturum açmayı etkinleştirmek için Microsoft Azure Active Directory hesabınızı Atlaseli JIRA sunucusu ile kullanın. Bu sayede, tüm kuruluşunuz kullanıcıları JIRA uygulamasında oturum açmak için Azure AD kimlik bilgilerini kullanabilir. Bu eklenti, Federasyon için SAML 2,0 kullanır.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Microsoft tarafından JıRA SAML SSO 'SU ile yapılandırmak için aşağıdaki öğeler gereklidir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
- JIRA Core ve Software 6,4 for 8.14.0 veya JIRA Service Desk 3,0 to 4.11.1 Windows 64-bit sürümünde yüklenip yapılandırılmalıdır
- JıRA sunucusu HTTPS etkin
- Aşağıdaki bölümde, JIRA eklentisinin desteklenen sürümlerinin bahsedildiğini aklınızda bulabilirsiniz.
- JıRA sunucusuna, özellikle kimlik doğrulaması için Azure AD oturum açma sayfasında Internet üzerinden erişilebilir ve belirteci Azure AD 'den alabilmelidir
- Yönetim kimlik bilgileri JIRA 'da ayarlanır
- WebSudo, JIRA 'da devre dışı
- JIRA sunucu uygulamasında oluşturulan test kullanıcısı

> [!NOTE]
> Bu öğreticideki adımları test etmek için JIRA üretim ortamının kullanılmasını önermiyoruz. Uygulamanın geliştirme veya hazırlama ortamında önce tümleştirmeyi test edin ve ardından üretim ortamını kullanın.

Başlamak için aşağıdaki öğeler gereklidir:

* Gerekli olmadığı takdirde üretim ortamınızı kullanmayın.
* JIRA SAML SSO 'SU Microsoft Çoklu oturum açma (SSO) özellikli abonelik.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="supported-versions-of-jira"></a>Desteklenen JIRA sürümleri

* JIRA Core ve Software: 6,4 ile 8.14.0
* JIRA Service Desk 3.0.0 to 4.11.1
* JIRA Ayrıca 5,2 'yi destekler. Daha fazla ayrıntı için [Microsoft Azure Active Directory JIRA 5,2 için çoklu oturum açma](jira52microsoft-tutorial.md) 'yı tıklatın

> [!NOTE]
> JıRA eklentisinin Ubuntu sürüm 16,04 ve Linux üzerinde de çalıştığından emin olun.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Microsoft tarafından desteklenen JIRA SAML SSO, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Galeri 'den Microsoft tarafından JıRA SAML SSO 'SU ekleme

JıRA SAML SSO 'yu Microsoft tarafından Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize JıRA SAML SSO 'SU eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Microsoft tarafından JıRA SAML SSO** yazın.
1. **Microsoft tarafından sonuçlar panelinden JIRA SAML SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft"></a>Microsoft tarafından JıRA SAML SSO 'SU için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu JIRA SAML SSO 'Su ile Microsoft tarafından yapılandırın ve test edin. SSO 'nun çalışması için, Microsoft tarafından bir Azure AD kullanıcısı ve JIRA SAML SSO 'SU içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Microsoft tarafından JIRA SAML SSO 'SU ile yapılandırmak ve test etmek için aşağıdaki adımları uygulayın:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[MICROSOFT SSO Ile JıRA SAML SSO 'Yu yapılandırın](#configure-jira-saml-sso-by-microsoft-sso)** .
    1. Microsoft **[test kullanıcısı tarafından JIRA SAML SSO 'Su oluşturun](#create-jira-saml-sso-by-microsoft-test-user)** . Bu, Microsoft tarafından sunulan ve kullanıcının Azure AD gösterimine BAĞLANMıŞ bir SAML SSO 'su olan bir B. Simon için bir
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, Microsoft uygulama tümleştirmesi **tarafından Jira SAML SSO 'su** sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/plugins/servlet/saml/auth`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'siyle güncelleştirin. Adlandırılmış bir URL olması durumunda bağlantı noktası isteğe bağlıdır. Bu değerler, Öğreticinin ilerleyen kısımlarında açıklanan Jira eklentisinin yapılandırması sırasında alınır.

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

Bu bölümde, Microsoft tarafından JıRA SAML SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Microsoft tarafından JIRA SAML SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>JIRA SAML SSO 'yu Microsoft SSO ile yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, JIRA örneğiniz için yönetici olarak oturum açın.

2. Dişli üzerine gelin ve **eklentilere** tıklayın.

    ![Ekran görüntüsü ayarlar menüsünden Seçili eklentileri gösterir.](./media/jiramicrosoft-tutorial/addon1.png)

3. Eklentiyi [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=56506)' nden indirin. **Yükleme** eklentisi menüsünü kullanarak Microsoft tarafından sunulan eklentiyi el ile karşıya yükleyin. Eklenti indirmesi, [Microsoft hizmet sözleşmesi](https://www.microsoft.com/servicesagreement/)kapsamında ele alınmıştır.

    ![Ekran görüntüsünde karşıya yükleme eklentisi bağlantısı ile çağrılan eklentilerin yönetilmesi gösterilmektedir.](./media/jiramicrosoft-tutorial/addon12.png)

4. JIRA ters proxy senaryosu veya yük dengeleyici senaryosunu çalıştırmak için aşağıdaki adımları uygulayın:

    > [!NOTE]
    > Önce aşağıdaki yönergelerle sunucuyu yapılandırmanız ve ardından eklentiyi yüklemeniz gerekir.

    a. JIRA sunucu uygulamasının **server.xml** dosyasına **bağlayıcı** bağlantı noktasına aşağıdaki özniteliği ekleyin.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Ekran görüntüsü, yeni satır eklenen bir düzenleyicide sunucu noktası x m l dosyasını gösterir.](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. **Sistem ayarlarındaki** **temel URL 'yi** proxy/yük dengeleyiciye göre değiştirin.

    ![Ekran görüntüsü, temel U R L 'yi değiştirebileceğiniz yönetim ayarlarını gösterir.](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Eklenti yüklendikten sonra, **Eklentiyi Yönet** bölümünün Kullanıcı tarafından **yüklenen** eklentiler bölümünde görüntülenir. Yeni eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın.

    ![Ekran görüntüsünde, Yapılandır seçili olan Jira bölümü için Azure A D SAML çoklu oturum açma gösterilmektedir.](./media/jiramicrosoft-tutorial/addon14.png)

6. Yapılandırma sayfasında aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsünde, Jira yapılandırma sayfası için Microsoft Azure Active Directory çoklu oturum açma gösterilmektedir.](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Meta verileri çözümlemede bir hata olmadığından, uygulamaya yönelik yalnızca bir sertifika eşlendiğinden emin olun. Birden çok sertifika varsa, meta veriler çözümlendikten sonra yönetici bir hata alır.

    a. **Meta veri URL 'si** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın ve **Çözümle** düğmesine tıklayın. IDP meta veri URL 'sini okur ve tüm alan bilgilerini doldurur.

    b. Azure portal üzerindeki **Microsoft etki alanı ve URL 'ler bölümünde bulunan Jira SAML SSO 'su** Içinde, tanımlayıcıyı kopyalayın, URL 'yi **yanıtlayın ve URL** değerlerini **tanımlayıcı, Yanıtla URL 'SI ve oturum açma URL 'si** metin kutularına yapıştırın.

    c. **Oturum açma düğmesi adı** ' nda, kuruluşunuzun oturum açma ekranında görmesini istediği düğmenin adını yazın.
    
    d. **Oturum aç düğmesi açıklaması** ' nda, kuruluşunuzun oturum açma ekranında görmesini istediği düğmenin açıklaması yazın.

    e. **SAML Kullanıcı kimliği konumlarında** , **Konu bildiriminin NameIdentifier öğesinde kullanıcı kimliği '** ni veya **Kullanıcı kimliği ' ni bir öznitelik öğesinde** seçin.  Bu KIMLIK, JIRA Kullanıcı KIMLIĞI olmalıdır. Kullanıcı KIMLIĞI eşleşmiyorsa, sistem kullanıcıların oturum açmalarına izin vermez.

    > [!Note]
    > Varsayılan SAML Kullanıcı KIMLIĞI konumu ad tanımlayıcısıdır. Bunu bir öznitelik seçeneği olarak değiştirebilir ve uygun öznitelik adını girebilirsiniz.

    f. **Bir öznitelik öğesi seçeneğinde Kullanıcı kimliği** ' ni seçerseniz, **öznitelik adı** metin kutusuna kullanıcı kimliğinin beklenildiği özniteliğin adını yazın.

    örneğin: Federasyon etki alanını (örneğin, ADFS vb.) Azure AD ile kullanıyorsanız, **giriş bölgesi bulmayı etkinleştir** seçeneğini tıklayın ve **etki alanı adını** yapılandırın.

    h. **Etki alanı adı** ' nda, ADFS tabanlı oturum açma durumunda etki alanı adını buraya yazın.

    i. Kullanıcı JIRA oturumunu kapattığınızda Azure AD 'den oturumu kapatmak istiyorsanız **Çoklu oturum açmayı etkinleştir** ' i işaretleyin.
    
    j. Yalnızca Azure AD kimlik bilgileri aracılığıyla oturum açmak istiyorsanız **Azure oturum açmaya zorla** onay kutusunu etkinleştirin.
    
    > [!Note]
    > Oturum açma sayfasında Azure oturum açma özelliği etkinken yönetici oturumu açma için varsayılan oturum açma formunu etkinleştirmek üzere tarayıcı URL 'sinde sorgu parametresini ekleyin.
    > `https://<domain:port>/login.jsp?force_azure_login=false`

    k. Ayarları kaydetmek için **Kaydet** düğmesine tıklayın.

    > [!NOTE]
    > Yükleme ve sorun giderme hakkında daha fazla bilgi için, [MS JIRA SSO Bağlayıcısı yönetici kılavuzunu](./ms-confluence-jira-plugin-adminguide.md)ziyaret edin. Ayrıca, yardımınız için bir [SSS](./ms-confluence-jira-plugin-adminguide.md) de vardır.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Microsoft test kullanıcısı tarafından JıRA SAML SSO 'SU oluşturma

Azure AD kullanıcılarının JIRA şirket içi sunucusunda oturum açmasını sağlamak için, Microsoft tarafından JıRA SAML SSO 'SU için sağlanması gerekir. Microsoft tarafından oluşturulan JIRA SAML SSO 'SU için el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. JIRA şirket içi sunucunuzda yönetici olarak oturum açın.

2. Dişli 'ye gelin ve **Kullanıcı yönetimine** tıklayın.

    ![Ekran görüntüsü, Ayarlar menüsünden seçilen kullanıcı yönetimini gösterir.](./media/jiramicrosoft-tutorial/user1.png)

3. **Parola** girmek Için yönetici erişimi sayfasına yönlendirilirsiniz ve **Onayla** düğmesine tıklayın.

    ![Ekran görüntüsü, kimlik bilgilerinizi girdiğiniz yönetici erişimi sayfasını gösterir.](./media/jiramicrosoft-tutorial/user2.png)

4. **Kullanıcı yönetimi** sekmesi bölümünde **Kullanıcı oluştur**' a tıklayın.

    ![Ekran görüntüsü, Kullanıcı oluşturabileceğiniz Kullanıcı Yönetimi sekmesini gösterir.](./media/jiramicrosoft-tutorial/user3.png) 

5. **"Yeni Kullanıcı Oluştur"** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımda bilgi girebileceğiniz yeni kullanıcı oluştur iletişim kutusunu gösterir.](./media/jiramicrosoft-tutorial/user4.png) 

    a. **E-posta adresi** metin kutusuna, gibi kullanıcının e-posta adresini yazın B.simon@contoso.com .

    b. **Tam ad** metin kutusuna B. Simon gibi kullanıcının tam adını yazın.

    c. Kullanıcı **adı** metin kutusuna, gibi kullanıcının e-postasını yazın B.simon@contoso.com .

    d. **Parola** metin kutusuna kullanıcının parolasını yazın.

    e. **Kullanıcı oluştur**' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz Microsoft oturum açma URL 'SI tarafından JıRA SAML SSO 'ya yönlendirilir. 

* Doğrudan Microsoft oturum açma URL 'sine sahip JIRA SAML SSO 'SU ' ne gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamamda Microsoft kutucuğunda JIRA SAML SSO 'SU ' ne tıkladığınızda, bu, Microsoft oturum açma URL 'SI tarafından JıRA SAML SSO 'ya yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

JıRA SAML SSO 'yu Microsoft tarafından yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve geri alımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)