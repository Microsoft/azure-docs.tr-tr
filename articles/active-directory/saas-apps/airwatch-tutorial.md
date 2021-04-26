---
title: 'Öğretici: AirWatch ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve AirWatch arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 4955062e6f0d0c231d09964c985df12284e3733c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653346"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Öğretici: AirWatch 'u Azure Active Directory tümleştirin

Bu öğreticide, AirWatch 'u Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. AirWatch 'u Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de AirWatch 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla AirWatch 'ta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:
 
* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* AirWatch çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 

* AirWatch, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-airwatch-from-the-gallery"></a>Galeriden AirWatch ekleme

AirWatch 'un Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetim SaaS uygulamaları listenize AirWatch eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **AirWatch** yazın.
1. Sonuçlar panelinden **AirWatch** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>AirWatch için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak AirWatch Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile AirWatch 'daki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu AirWatch ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[AirWatch SSO 'Yu yapılandırma](#configure-airwatch-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. AirWatch **[test kullanıcısı oluşturun](#create-airwatch-test-user)** -kullanıcının Azure AD gösterimine bağlı olan AirWatch 'da B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **AirWatch** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    1. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna değeri şöyle yazın:`AirWatch`

    > [!NOTE]
    > Bu değer gerçek değil. Bu değeri, gerçek oturum açma URL 'siyle güncelleştirin. Bu değeri almak için [AirWatch istemci destek ekibine](https://www.air-watch.com/company/contact-us/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. AirWatch uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile tek Sign-On ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![image](common/edit-attribute.png)

1. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, yukarıdaki görüntüde gösterildiği gibi, **Düzen simgesini** kullanarak talepleri DÜZENLEYIN veya aşağıdaki resimde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** ' yi kullanarak talepleri ekleyin ve aşağıdaki adımları gerçekleştirin:

    | Name |  Kaynak özniteliği|
    |---------------|----------------|
    | UID | User. UserPrincipalName |
    | | |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Tamam 'a** tıklayın

    örneğin: **Kaydet**’e tıklayın.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve meta veri XML 'Sini indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **AirWatch 'U ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, AirWatch 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **AirWatch**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-airwatch-sso"></a>AirWatch SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, AirWatch şirket sitenizde yönetici olarak oturum açın.

1. Ayarlar sayfasında. **> dizin hizmetleri kurumsal tümleştirme ayarları >** seçin.

   ![Ayarlar](./media/airwatch-tutorial/services.png "Ayarlar")

1. **Kullanıcı** sekmesine tıklayın, **Taban DN** metin kutusunda, etki alanı adınızı yazın ve ardından **Kaydet**' e tıklayın.

   ![Temel DN metin kutusunu vurgulayan ekran görüntüsü.](./media/airwatch-tutorial/user.png "Kullanıcı")

1. **Sunucu** sekmesine tıklayın.

   ![Sunucu](./media/airwatch-tutorial/directory.png "Sunucu")

1. **LDAP** bölümünde aşağıdaki adımları gerçekleştirin:

    ![LDAP bölümünde yapmanız gereken değişiklikleri gösteren ekran görüntüsü.](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. **Dizin türü** olarak, **hiçbiri**' ni seçin.

    b. **Kimlik doğrulaması IÇIN SAML kullan**' ı seçin.

1. **SAML 2,0** bölümünde, indirilen sertifikayı karşıya yüklemek Için **karşıya yükle**' ye tıklayın.

    ![Karşıya yükle](./media/airwatch-tutorial/uploads.png "Karşıya Yükleme")

1. **İstek** bölümünde aşağıdaki adımları uygulayın:

    ![İstek bölümü](./media/airwatch-tutorial/request.png "İstek")  

    a. **Istek bağlama türü** olarak **gönderi**' ı seçin.

    b. Azure portal, **AirWatch 'da çoklu oturum açmayı Yapılandır** iletişim sayfasında, **oturum açma URL 'si** değerini kopyalayın ve ardından **kimlik sağlayıcısı çoklu oturum açma URL 'si** metin kutusuna yapıştırın.

    c. **NameID biçimi** olarak **e-posta adresi**' ni seçin.

    d. **Kimlik doğrulama Isteği güvenliği** olarak **hiçbiri**' ni seçin.

    e. **Kaydet**’e tıklayın.

1. **Kullanıcı** sekmesine tekrar tıklayın.

    ![Kullanıcı](./media/airwatch-tutorial/users.png "Kullanıcı")

1. **Öznitelik** bölümünde aşağıdaki adımları uygulayın:

    ![Öznitelik](./media/airwatch-tutorial/attributes.png "Öznitelik")

    a. **Nesne tanımlayıcısı** metin kutusuna yazın `http://schemas.microsoft.com/identity/claims/objectidentifier` .

    b. **Kullanıcı adı** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    c. **Görünen ad** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    d. **Ilk ad** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    e. **Soyadı** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    f. **E-posta** metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    örneğin: **Kaydet**’e tıklayın.

### <a name="create-airwatch-test-user"></a>AirWatch test kullanıcısı oluşturma

Azure AD kullanıcılarının AirWatch 'da oturum açmasını sağlamak için, bu kullanıcıların AirWatch 'da sağlanması gerekir. AirWatch durumunda, sağlama el ile gerçekleştirilen bir görevdir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. **AirWatch** şirket sitenizde yönetici olarak oturum açın.

2. Sol taraftaki Gezinti bölmesinde **hesaplar**' a ve ardından **Kullanıcılar**' a tıklayın.
  
   ![Kullanıcılar](./media/airwatch-tutorial/accounts.png "Kullanıcılar")

3. **Kullanıcılar** menüsünde **liste görünümü**' ne ve ardından **Kullanıcı Ekle > Ekle**' ye tıklayın.
  
   ![Kullanıcı Ekle ve Ekle düğmelerini vurgulayan ekran görüntüsü.](./media/airwatch-tutorial/add.png "Kullanıcı Ekleme")

4. **Kullanıcı Ekle/Düzenle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

   ![Kullanıcı Ekle](./media/airwatch-tutorial/save.png "Kullanıcı Ekleme")

   a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure Active Directory hesabının **Kullanıcı** adı, **parola**, **parolayı onaylayın**, **adı**, **Soyadı**, **e-posta adresini** yazın.

   b. **Kaydet**’e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için AirWatch tarafından sunulan diğer AirWatch Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz AirWatch oturum açma URL 'sine yeniden yönlendirilir. 

* AirWatch oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki AirWatch kutucuğuna tıkladığınızda, bu, AirWatch oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

AirWatch 'u yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).