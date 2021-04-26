---
title: "Öğretici: Microsoft tarafından JıRA SAML SSO 'SU ile tümleştirme Azure Active Directory (V 5.2) | Microsoft Docs"
description: Azure Active Directory ve JIRA SAML SSO arasında Microsoft (V 5.2) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 7b85cc064babf44b14e80abc02669573b4730da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736915"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Öğretici: Microsoft tarafından JıRA SAML SSO 'SU ile tümleştirme Azure Active Directory (V 5.2)

Bu öğreticide, JIRA SAML SSO 'yu Microsoft (V 5.2) ile Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. JıRA SAML SSO 'yu Microsoft (V 5.2) ile Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Microsoft (V 5.2) ile JIRA SAML SSO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Microsoft (V 5.2) ile JıRA SAML SSO 'SU için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="description"></a>Description

Çoklu oturum açmayı etkinleştirmek için Microsoft Azure Active Directory hesabınızı Atlaseli JIRA sunucusu ile kullanın. Bu sayede, tüm kuruluşunuz kullanıcıları JIRA uygulamasında oturum açmak için Azure AD kimlik bilgilerini kullanabilir. Bu eklenti, Federasyon için SAML 2,0 kullanır.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Microsoft (V 5.2) ile JIRA SAML SSO 'SU ile yapılandırmak için aşağıdaki öğeler gereklidir:

- Azure AD aboneliği
- JIRA Core ve Software 5,2 Windows 64 bit sürümüne yüklenip yapılandırılmalıdır
- JıRA sunucusu HTTPS etkin
- Aşağıdaki bölümde, JIRA eklentisinin desteklenen sürümlerinin bahsedildiğini aklınızda bulabilirsiniz.
- JıRA sunucusuna, kimlik doğrulaması için özellikle Azure AD oturum açma sayfasında internet 'ten erişilebilir ve bu belirteci Azure AD 'den alabilmelidir
- Yönetim kimlik bilgileri JIRA 'da ayarlanır
- WebSudo, JIRA 'da devre dışı
- JIRA sunucu uygulamasında oluşturulan test kullanıcısı

> [!NOTE]
> Bu öğreticideki adımları test etmek için JIRA üretim ortamının kullanılmasını önermiyoruz. Uygulamanın geliştirme veya hazırlama ortamında önce tümleştirmeyi test edin ve ardından üretim ortamını kullanın.

Bu öğreticideki adımları test etmek için aşağıdaki önerileri izlemeniz gerekir:

- Gerekli olmadığı takdirde üretim ortamınızı kullanmayın.
- Bir Azure AD deneme ortamınız yoksa, buradan bir aylık deneme sürümü edinebilirsiniz: [deneme teklifi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Desteklenen JIRA sürümleri

* JIRA Core ve Software: 5,2
* JIRA Ayrıca 6,0 7,12 ' i destekler. Daha fazla ayrıntı için, [Microsoft tarafından JIRA SAML SSO 'su](jiramicrosoft-tutorial.md) ' ne tıklayın

> [!NOTE]
> JıRA eklentisinin Ubuntu sürüm 16,04 ' de çalıştığından emin olun.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Microsoft (V 5.2) tarafından desteklenen JIRA SAML SSO, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Galeriden Microsoft (V 5.2) ile JıRA SAML SSO 'SU ekleme

JıRA SAML SSO 'yu Microsoft (V 5.2) ile Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Microsoft (V 5.2) ile JıRA SAML SSO 'SU eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **JıRA SAML SSO 'Su Microsoft (v 5.2)** yazın.
1. Sonuçlar panelinden **Microsoft (v 5.2) Ile JIRA SAML SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Microsoft tarafından belirtilen JIRA SAML SSO 'SU için Azure AD SSO 'yu yapılandırma ve test etme (V 5.2)

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına dayalı olarak Microsoft (v 5.2) Ile JIRA SAML SSO 'Su Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz. Çoklu oturum açma için, Microsoft (V 5.2) ile JIRA SAML SSO 'SU içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Microsoft (V 5.2) ile JIRA SAML SSO 'SU ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[Microsoft (v 5.2) SSO 'su tarafından JIRA SAML SSO 'Yu yapılandırın](#configure-jira-saml-sso-by-microsoft-v52-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Microsoft (v 5.2) ile Jira SAML SSO 'SU için Microsoft **[(v 5.2) Ile JıRA SAML SSO 'Su oluşturun](#create-jira-saml-sso-by-microsoft-v52-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

1. Azure portal, **Jira SAML SSO 'su Ile Microsoft (v 5.2)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/plugins/servlet/saml/auth`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'siyle güncelleştirin. Adlandırılmış bir URL olması durumunda bağlantı noktası isteğe bağlıdır. Bu değerler, Öğreticinin ilerleyen kısımlarında açıklanan Jira eklentisinin yapılandırması sırasında alınır.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

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

Bu bölümde, Microsoft 'a (V 5.2) göre JIRA SAML SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **JIRA SAML SSO 'Su Microsoft (v 5.2)** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>JIRA SAML SSO 'yu Microsoft (V 5.2) SSO 'SU ile yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, JIRA örneğiniz için yönetici olarak oturum açın.

2. Dişli üzerine gelin ve **eklentilere** tıklayın.

    ![Ekran görüntüsü ayarlar menüsünden Seçili eklentileri gösterir.](./media/jira52microsoft-tutorial/addon1.png)

3. Eklentiler sekmesi bölümünde eklentileri **Yönet**' e tıklayın.

    ![Ekran görüntüsü Eklentiler sekmesinde seçilen Eklentileri Yönet ' i gösterir.](./media/jira52microsoft-tutorial/addon7.png)

4. Eklentiyi [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=56521)' nden indirin. **Yükleme** eklentisi menüsünü kullanarak Microsoft tarafından sunulan eklentiyi el ile karşıya yükleyin. Eklenti indirmesi, [Microsoft hizmet sözleşmesi](https://www.microsoft.com/servicesagreement/)kapsamında ele alınmıştır.

    ![Ekran görüntüsünde karşıya yükleme eklentisi bağlantısı ile çağrılan eklentilerin yönetilmesi gösterilmektedir.](./media/jira52microsoft-tutorial/addon12.png)

5. Eklenti yüklendikten sonra, **Kullanıcı tarafından yüklenen** eklentiler bölümünde görünür. Yeni eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın.

    ![Ekran görüntüsünde, Yapılandır seçili olan Jira bölümü için Azure A D SAML çoklu oturum açma gösterilmektedir.](./media/jira52microsoft-tutorial/addon13.png)

6. Yapılandırma sayfasında aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü Microsoft Jira S S O bağlayıcı yapılandırma sayfasını gösterir.](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Meta verileri çözümlemede bir hata olmadığından, uygulamaya yönelik yalnızca bir sertifika eşlendiğinden emin olun. Birden çok sertifika varsa, meta veriler çözümlendikten sonra yönetici bir hata alır.

    a. **Meta veri URL 'si** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın ve **Çözümle** düğmesine tıklayın. IDP meta veri URL 'sini okur ve tüm alan bilgilerini doldurur.

    b. Azure portal ' daki **temel SAML yapılandırması** bölümünde tanımlayıcıyı kopyalayın, URL 'yi **yanıtlayın ve URL** değerlerini **tanımlayıcı, Yanıtla URL 'si ve URL** metin kutularına yapıştırın.

    c. **Oturum açma düğmesi adı** ' nda, kuruluşunuzun oturum açma ekranında görmesini istediği düğmenin adını yazın.

    d. **SAML Kullanıcı kimliği konumlarında** , **Konu bildiriminin NameIdentifier öğesinde kullanıcı kimliği '** ni veya **Kullanıcı kimliği ' ni bir öznitelik öğesinde** seçin.  Bu KIMLIK, JIRA Kullanıcı KIMLIĞI olmalıdır. Kullanıcı KIMLIĞI eşleşmiyorsa, sistem kullanıcıların oturum açmalarına izin vermez.

    > [!Note]
    > Varsayılan SAML Kullanıcı KIMLIĞI konumu ad tanımlayıcısıdır. Bunu bir öznitelik seçeneği olarak değiştirebilir ve uygun öznitelik adını girebilirsiniz.

    e. **Bir öznitelik öğesi seçeneğinde Kullanıcı kimliği** ' ni seçerseniz, **öznitelik adı** metin kutusuna kullanıcı kimliğinin beklenildiği özniteliğin adını yazın. 

    f. Federasyon etki alanını (örneğin, ADFS vb.) Azure AD ile kullanıyorsanız, **giriş bölgesi bulmayı etkinleştir** seçeneğini tıklayın ve **etki alanı adını** yapılandırın.

    örneğin: **Etki alanı adı** ' nda, ADFS tabanlı oturum açma durumunda etki alanı adını buraya yazın.

    h. Kullanıcı JIRA oturumunu kapattığında Azure AD 'den oturumu kapatmak istiyorsanız **Çoklu oturum açmayı etkinleştir** ' i işaretleyin. 

    i. Ayarları kaydetmek için **Kaydet** düğmesine tıklayın.

    > [!NOTE]
    > Yükleme ve sorun giderme hakkında daha fazla bilgi için, [MS JIRA SSO Bağlayıcısı yönetici kılavuzunu](./ms-confluence-jira-plugin-adminguide.md) ziyaret edin ve yardım için [SSS](./ms-confluence-jira-plugin-adminguide.md) de vardır.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Microsoft (V 5.2) test kullanıcısı tarafından JıRA SAML SSO 'SU oluşturma

Azure AD kullanıcılarının JIRA şirket içi sunucusunda oturum açmasını sağlamak için, bu kullanıcıların JIRA on-premises Server 'a sağlanması gerekir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. JIRA şirket içi sunucunuzda yönetici olarak oturum açın.

2. Dişli 'ye gelin ve **Kullanıcı yönetimine** tıklayın.

    ![Ekran görüntüsü, Ayarlar menüsünden seçilen kullanıcı yönetimini gösterir.](./media/jira52microsoft-tutorial/user1.png)

3. **Parola** girmek Için yönetici erişimi sayfasına yönlendirilirsiniz ve **Onayla** düğmesine tıklayın.

    ![Ekran görüntüsü, kimlik bilgilerinizi girdiğiniz yönetici erişimi sayfasını gösterir.](./media/jira52microsoft-tutorial/user2.png)

4. **Kullanıcı yönetimi** sekmesi bölümünde **Kullanıcı oluştur**' a tıklayın.

    ![Ekran görüntüsü, Kullanıcı oluşturabileceğiniz Kullanıcı Yönetimi sekmesini gösterir.](./media/jira52microsoft-tutorial/user3.png) 

5. **"Yeni Kullanıcı Oluştur"** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımda bilgi girebileceğiniz yeni kullanıcı oluştur iletişim kutusunu gösterir.](./media/jira52microsoft-tutorial/user4.png)

    a. **E-posta adresi** metin kutusuna, gibi kullanıcının e-posta adresini yazın Brittasimon@contoso.com .

    b. **Tam ad** metin kutusuna, Britta Simon gibi kullanıcının tam adını yazın.

    c. Kullanıcı **adı** metin kutusuna, gibi kullanıcının e-postasını yazın Brittasimon@contoso.com .

    d. **Parola** metin kutusuna kullanıcının parolasını yazın.

    e. **Kullanıcı oluştur**' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum akışını başlatabileceğiniz Microsoft (V 5.2) oturum açma URL 'sine göre JIRA SAML SSO 'ya yönlendirilir. 

* Microsoft (V 5.2) oturum açma URL 'sine doğrudan JIRA SAML SSO 'SU ' ne gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım ' da Microsoft (V 5.2) ile JIRA SAML SSO kutucuğuna tıkladığınızda, bu, Microsoft (V 5.2) oturum açma URL 'SI tarafından JıRA SAML SSO 'SU 'ne yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

JIRA SAML SSO 'yu Microsoft (V 5.2) ile yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve geri alımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad).