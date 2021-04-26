---
title: 'Öğretici: TOPdesk-public ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve TOPdesk-public arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: b787102065f93588cd796027123d61072e9d5aea
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601024"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Öğretici: Topmasa-genel ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile TOPdesk-public tümleştirme hakkında bilgi edineceksiniz. TOPdesk-public 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Topmasa-herkese erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla genel kullanıma açık olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Topmasa-genel çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Topmasa-genel **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-topdesk---public-from-the-gallery"></a>Galeriden TOPdesk-public ekleme

Topmasa-genel ' i Azure AD ile tümleştirmeyi yapılandırmak için, Galeriden Topmasa-Public ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **TopDesk-public** yazın.
1. Sonuçlar panelinden **Topmasa-genel** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Azure AD SSO 'yu TOPdesk-public için yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu TopDesk-public ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve TOPdesk-public içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Topmasa-public ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[TOPdesk-PUBLIC SSO 'Yu yapılandırma](#configure-topdesk---public-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Topmasa **[-genel test kullanıcısı oluşturun](#create-topdesk---public-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Topmasa-genel 'de B. Simon 'ın bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Topmasa-genel** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4.  **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa, aşağıdaki adımları uygulayın:

    >[!NOTE]
    >**Hizmet sağlayıcı meta veri dosyasını** , Öğreticinin ilerleyen kısımlarında açıklanan, **Topmasa genel çoklu oturum açma** bölümünde bulabilirsiniz.

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.
    
    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL** değerleri temel SAML yapılandırması bölümünde otomatik olarak doldurulur.

    d. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.topdesk.net`

    e. **Tanımlayıcı URL** metin kutusunda, TopDesk yapılandırmasından alabileceğiniz TopDesk meta veri URL 'sini girin. Bu, aşağıdaki kalıbı kullanmalıdır: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak doldurulmazsa, bunları el ile girmeniz gerekir. Tanımlayıcı için yukarıda belirtilen kalıbı izleyin ve öğreticide daha sonra açıklanan **Topmasa genel çoklu oturum açma** bölümünden yanıt URL 'si değerini alın. **Oturum açma URL 'si** değeri gerçek değil, bu değeri gerçek Sign-On URL 'siyle güncelleştirmeniz gerekiyor. Değeri almak için [Topmasa-genel istemci desteği ekibine](https://my.topdesk.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **TOPdesk-public ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD test kullanıcısı oluşturma 

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD test kullanıcısını atama

Bu bölümde, Topmasa-genel erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Topmasa-genel**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-topdesk---public-sso&quot;></a>Topmasa-genel SSO 'yu yapılandırma

1. **Topoloji-ortak** şirket sitenizde yönetici olarak oturum açın.

2. **TopDesk** menüsünde **Ayarlar**' a tıklayın.
   
    ![Ayarlar](./media/topdesk-public-tutorial/menu.png &quot;Ayarlar")

3. **Oturum açma ayarları**' na tıklayın.
   
    ![Oturum açma ayarları](./media/topdesk-public-tutorial/login.png "Oturum açma ayarları")

4. **Oturum açma ayarları** menüsünü genişletin ve ardından **genel**' e tıklayın.
   
    ![Genel ayarlar](./media/topdesk-public-tutorial/general.png "Genel ayarlar")

5. **SAML oturum açma** Yapılandırması bölümünün **genel** bölümünde aşağıdaki adımları uygulayın:
   
    ![Teknik ayarlar](./media/topdesk-public-tutorial/public.png "Teknik ayarlar")
   
    a. Ortak meta veri dosyasını indirmek için **İndir** ' e tıklayın ve ardından bilgisayarınıza yerel olarak kaydedin.
   
    b. İndirilen meta veri dosyasını açın ve ardından **Assertionconsumerservice** düğümünü bulun.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. **Assertionconsumerservice** değerini kopyalayın, bu DEĞERI **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** metin kutusuna yapıştırın.      
   
6. Bir sertifika dosyası oluşturmak için aşağıdaki adımları gerçekleştirin:
    
    ![Sertifika](./media/topdesk-public-tutorial/certificate-file.png "Sertifika")
    
    a. İndirilen meta veri dosyasını Azure portal açın.
    
    b. **Xsi: Type türünde** **Besleyicisi: Applicationservicetype** olan **RoleDescriptor** düğümünü genişletin.
    
    c. **X509Certificate** düğümünün değerini kopyalayın.
    
    d. Kopyalanmış **X509Certificate** değerini bilgisayarınıza yerel olarak bir dosyaya kaydedin.

7. **Genel** bölümünde, **Ekle**' ye tıklayın.
    
    ![SAML oturum açma](./media/topdesk-public-tutorial/add.png "SAML oturum açma")

8. **SAML yapılandırma Yardımcısı** iletişim kutusunda, aşağıdaki adımları uygulayın:
    
    ![SAML yapılandırma Yardımcısı](./media/topdesk-public-tutorial/configuration.png "SAML yapılandırma Yardımcısı")
    
    a. İndirilen meta veri dosyanızı Azure portal karşıya yüklemek için, **Federasyon meta verileri** altında, **Araştır**' a tıklayın.

    b. Sertifika dosyanızı karşıya yüklemek için, **sertifika (RSA)** altında, **Araştır**' a tıklayın.

    c. TOPdesk destek ekibinden aldığınız logo dosyasını karşıya yüklemek için, **logo simgesi** altında, **Araştır**' a tıklayın.

    d. **Kullanıcı adı öznitelik** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    e. **Görünen ad** metin kutusuna yapılandırmanız için bir ad yazın.

    f. **Kaydet**’e tıklayın.

### <a name="create-topdesk---public-test-user"></a>Topmasa-ortak test kullanıcısı oluşturma

Azure AD kullanıcılarının Topmasa-genel ' te oturum açmasını sağlamak için, bu kullanıcıların Topmasa-genel ' e sağlanması gerekir. Topmasa-genel durumunda, sağlama el ile gerçekleştirilen bir görevdir.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:

1. **Topoloji-ortak** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **TopDesk \> Yeni \> destek dosyaları \> kişisi**' ne tıklayın.
   
    ![Kişi](./media/topdesk-public-tutorial/files.png "Kişi")

3. Yeni kişi iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Yeni kişi](./media/topdesk-public-tutorial/new.png "Yeni kişi")
   
    a. Genel sekmesine tıklayın.

    b. **Soyadı** metin kutusunda, Simon gibi kullanıcının soyadı yazın
 
    c. Hesap için bir **site** seçin.
 
    d. **Kaydet**’e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, TOPdesk-public tarafından sunulan diğer tüm Topmasa-genel kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Topmasa genel oturum açma URL 'sine yeniden yönlendirilir. 

* Topoloji-genel oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Topmasa-genel kutucuğuna tıkladığınızda, bu, Topmasa-genel oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Topmasa-genel ' i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
