---
title: 'Öğretici: a-Files ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve d dosyaları arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 680380722746522a0eb3fe6518452472be952075
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482850"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Öğretici: d-dosyalarla Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile e-dosyaları tümleştirmeyi öğreneceksiniz. D dosyalarını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, e-dosyalara erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla, e-dosyalarda otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* A-dosyalar çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* D-dosyalar **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-m-files-from-the-gallery"></a>Galeriden z dosyaları ekleyin

E-dosyaların Azure AD ile tümleştirilmesini yapılandırmak için Galeriden, yönetilen SaaS uygulamaları listenize e-dosyaları eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **z-dosyaları** yazın.
1. Sonuçlar panelinden **e-dosyaları** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>D dosyaları için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak M DOSYALARıYLA Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve a-Files içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu e-dosyalarla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[D-FILES SSO yapılandırma](#configure-m-files-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. M- **[Files test kullanıcısı oluşturma](#create-m-files-test-user)** -kullanıcının Azure AD gösterimine bağlı m dosyalarında B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, ı **-Files** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için, [e-Files istemci destek ekibine](mailto:support@m-files.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **K-Files ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, M dosyalarına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, ı **-Files**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-m-files-sso"></a>D-Files SSO yapılandırma

1. Uygulamanız için yapılandırılmış SSO 'yu almak için, [e-Files destek ekibine](mailto:support@m-files.com) başvurun ve Indirilen meta verileri sağlayın.
   
    >[!NOTE]
    >D-dosya masaüstü uygulamanız için SSO 'yu yapılandırmak istiyorsanız sonraki adımları izleyin. Yalnızca e-Files Web sürümü için SSO yapılandırmak istiyorsanız ek adım gerekmez.  

1. Azure AD ile SSO 'yu etkinleştirmek üzere, e-dosya masaüstü uygulamasını yapılandırmak için sonraki adımları izleyin. E-dosyaları indirmek için, ı [-Files indirme](https://www.m-files.com/customers/product-downloads/download-update-links/) sayfasına gidin.

1. **D-Files masaüstü ayarları** penceresini açın. Ardından **Ekle**' ye tıklayın.
   
    ![Ekran görüntüsü, Ekle ' yi seçebileceğiniz, a-Files masaüstü ayarlarını gösterir.](./media/m-files-tutorial/settings.png)

1. **Belge Kasası bağlantısı özellikleri** penceresinde, aşağıdaki adımları uygulayın:
   
    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz belge Kasası bağlantı özelliklerini gösterir.](./media/m-files-tutorial/general.png)  

    Sunucu bölümünün altında, aşağıdaki gibi değerleri:  

    a. **Ad** için yazın `<tenant-name>.cloudvault.m-files.com` . 
 
    b. **Bağlantı noktası numarası** için **4466** yazın. 

    c. **Protokol** için **https**' yi seçin. 

    d. **Kimlik doğrulama** alanında, **belirli Windows kullanıcısı**' nı seçin. Ardından, sizden bir imzalama sayfası istenir. Azure AD kimlik bilgilerinizi ekleyin. 

    e. **Sunucu üzerindeki kasa** için, sunucuda ilgili kasayı seçin.
 
    f. **Tamam**'a tıklayın.

### <a name="create-m-files-test-user"></a>A-Files test kullanıcısı oluştur

Bu bölümün amacı, a-dosyalarında Britta Simon adlı bir Kullanıcı oluşturmaktır. E-dosyalarda kullanıcıları eklemek için,  [e-Files destek](mailto:support@m-files.com) ekibiyle çalışın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz e-Files oturum açma URL 'sine yeniden yönlendirilir. 

* E-Files oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki M-Files kutucuğuna tıkladığınızda bu, M-Files oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

E-dosyaları yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
