---
title: 'Öğretici: Tableau Server ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Tableau Server arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 810f57afceda10c10cf5919a0c50ca6be2950703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650610"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Öğretici: Tableau Server ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Tableau Server 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Tableau Server 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Tableau Server erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Tableau Server 'a otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Tableau sunucusu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Tableau sunucusu **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-tableau-server-from-the-gallery"></a>Galeriden Tableau sunucusu ekleme

Tableau Server tümleştirmesini Azure AD ile yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Tableau sunucusu eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Tableau Server** yazın.
1. Sonuçlar panelinden **Tableau Server** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>Tableau Server için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Tableau Server ile yapılandırın ve test edin. SSO 'nun çalışması için, Tableau sunucusundaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Tableau Server ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Tableau Server SSO 'Yu yapılandırın](#configure-tableau-server-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Tableau Server 'da B. Simon 'a karşılık gelen bir **[Tableau Server test kullanıcısı oluşturun](#create-tableau-server-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Tableau Server** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://azure.<domain name>.link`

    b. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://azure.<domain name>.link`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Yukarıdaki değerler gerçek değer değildir. Değerleri, Öğreticinin ilerleyen kısımlarında açıklanan Tableau Server yapılandırma sayfasından gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'SI ile güncelleştirin.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Tableau sunucusunu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Tableau sunucusuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Tableau Server**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-tableau-server-sso"></a>Tableau Server SSO 'yu yapılandırma

1. Uygulamanız için yapılandırılmış SSO 'yu almak için, Tableau sunucu kiracınızda yönetici olarak oturum açmanız gerekir.

2. **Yapılandırma** sekmesinde, **Kullanıcı kimliği & erişim**' i seçin ve ardından **kimlik doğrulama** yöntemi sekmesini seçin.

    ![Ekran görüntüsü Kullanıcı kimliği & erişimi ' nden seçilen kimlik doğrulamasını gösterir.](./media/tableauserver-tutorial/auth.png)

3. **Yapılandırma** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz yapılandırma sayfasını gösterir.](./media/tableauserver-tutorial/config.png)

    a. **Kimlik doğrulama yöntemi** için SAML ' yi seçin.

    b. **Sunucu IÇIN SAML kimlik doğrulamasını etkinleştir** onay kutusunu seçin.

    c. Tableau sunucusu dönüş URL 'SI — gibi Tableau Server kullanıcılarına erişim verilecek URL `http://tableau_server` . Kullanılması `http://localhost` önerilmez. Sondaki eğik çizgiyle (örneğin,) bir URL kullanılması `http://tableau_server/` desteklenmez. **Tableau sunucusu geri dönüş URL 'sini** kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde URL metin kutusunu **oturum** açmak için yapıştırın.

    d. SAML varlık KIMLIĞI — varlık KIMLIĞI, Tableau sunucu yüklemenizi IDP 'ye benzersiz şekilde tanımlar. İsterseniz Tableau sunucu URL 'nizi buraya girebilirsiniz, ancak Tableau Server URL 'niz olması gerekmez. **SAML VARLıK kimliğini** kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı** metin kutusuna yapıştırın.

    e. **XML meta verilerini Indir dosyasına** tıklayın ve metin düzenleyici uygulamasında açın. Onaylama tüketici hizmeti URL 'sini http post ve Index 0 ile bulun ve URL 'YI kopyalayın. Şimdi de Azure portal **temel SAML yapılandırması** BÖLÜMÜNDEKI **yanıt URL** metin kutusuna yapıştırın.

    f. Azure portal 'ten indirilen Federasyon meta veri dosyanızı bulun ve **SAML IDP meta veri dosyasına** yükleyin.

    örneğin: IDP 'nin Kullanıcı adlarını, görünen adları ve e-posta adreslerini tutmak için kullandığı özniteliklerin adlarını girin.

    h. **Kaydet**’e tıklayın.

    > [!NOTE]
    > Müşterinin bir. CRT uzantılı bir pek kodlu x509 sertifika dosyasını ve. Key uzantısına sahip bir RSA veya DSA özel anahtar dosyasını sertifika anahtarı dosyası olarak yüklemesi gerekir. Sertifika dosyası ve sertifika anahtar dosyası hakkında daha fazla bilgi için lütfen [Bu](https://help.tableau.com/current/server/en-us/saml_requ.htm) belgeye başvurun. Tableau sunucusunda SAML yapılandırması için yardıma ihtiyacınız varsa lütfen [sunucu GENELINDEKI SAML 'Yi yapılandırma](https://help.tableau.com/current/server/en-us/config_saml.htm)makalesine başvurun.

### <a name="create-tableau-server-test-user"></a>Tableau Server test kullanıcısı oluştur

Bu bölümün amacı, Tableau Server 'da B. Simon adlı bir Kullanıcı oluşturmaktır. Tableau sunucusundaki tüm kullanıcıları sağlamanız gerekir.

Kullanıcının Kullanıcı adı, Kullanıcı **adının** Azure AD özel özniteliğinde yapılandırdığınız değerle eşleşmelidir. Doğru eşleme ile tümleştirme, Azure AD çoklu oturum açma yapılandırması ile çalışmalıdır.

> [!NOTE]
> Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, kuruluşunuzda Tableau sunucu yöneticisiyle iletişim kurmanız gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Tableau sunucu oturum açma URL 'sine yönlendirecektir. 

* Doğrudan Tableau Server oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Tableau Server kutucuğuna tıkladığınızda bu, Tableau Server oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Tableau Server 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).