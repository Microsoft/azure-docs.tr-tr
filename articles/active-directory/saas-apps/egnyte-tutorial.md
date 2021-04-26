---
title: 'Öğretici: Egnyut ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Egnde arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 2662b686102a1a4f6aa6db0f7a4052de329def60
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519844"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Öğretici: Egnyut ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Egnylei tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Egnde erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* EGN, çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Egnse, **SP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-egnyte-from-the-gallery"></a>Galeriden Egni ekleme

Egnyleyler 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Egnler eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **egnlele,** yazın.
1. Sonuçlar panelinden **Egnut** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Azure AD SSO 'yu Egni için yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu form.com ile yapılandırın ve test edin. SSO 'nun çalışması için, Form.com içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Form.com ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[EGN, SSO 'Yu yapılandırın](#configure-egnyte-sso)** .
    1. **[Egnlarca test kullanıcısı oluşturun](#create-egnyte-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan, Egnlarca 'da B. Simon 'ın bir karşılığı olacak şekilde.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **EGN,** uygulama tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.egnyte.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Bu değerler gerçek değildir. Değeri gerçek Sign-On URL 'SI ve yanıt URL 'SI ile güncelleştirin. Değeri almak için [EGN, istemci destek ekibine](https://www.egnyte.com/corp/contact_egnyte.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

5. **Egnlarca ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Cgnlarca erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Egnde**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-egnyte-sso&quot;></a>EGN, SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Egnyut şirket sitenizde yönetici olarak oturum açın.

2. **Ayarlar**'a tıklayın.
   
    ![Ayarlar 1](./media/egnyte-tutorial/settings-tab.png &quot;Ayarlar")

3. Menüsünde, **Ayarlar**' a tıklayın.

    ![Menü 1](./media/egnyte-tutorial/menu-tab.png "Menü")

4. **Yapılandırma** sekmesine tıklayın ve ardından **güvenlik**' e tıklayın.

    ![Güvenlik](./media/egnyte-tutorial/configuration.png "Güvenlik")

5. **Tek Sign-On kimlik doğrulaması** bölümünde aşağıdaki adımları uygulayın:

    ![Çoklu oturum açma kimlik doğrulaması](./media/egnyte-tutorial/authentication.png "Çoklu oturum açma kimlik doğrulaması")   
    
    a. **Çoklu oturum açma kimlik doğrulaması** olarak **SAML 2,0**' i seçin.
   
    b. **Kimlik sağlayıcısı** olarak **azuread**' i seçin.
   
    c. Azure portal 'den kopyalanmış **oturum açma URL** 'sini **kimlik sağlayıcısı oturum açma URL 'si** metin kutusuna kopyalayın.
   
    d. Azure portal kopyaladığınız **Azure ad tanımlayıcısını** **KIMLIK sağlayıcısı varlık kimliği** metin kutusuna yapıştırın.
      
    e. Azure portal 'den indirilen Not defteri 'nde Base-64 kodlu sertifikanızı açın, içeriğini panonuza kopyalayın ve ardından **kimlik sağlayıcısı sertifikası** metin kutusuna yapıştırın.
   
    f. **Varsayılan kullanıcı eşlemesi** olarak **e-posta adresi**' ni seçin.
   
    örneğin: **Etki alanına özgü veren değerini kullanmak** için **devre dışı**' yı seçin.
   
    h. **Kaydet**’e tıklayın.

### <a name="create-egnyte-test-user"></a>EGN, test kullanıcısı oluşturma

Azure AD kullanıcılarının, Egnler 'de oturum açmasını sağlamak için, bunların Egnda sağlanması gerekir. Bu durumda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Egnyi** şirket sitenizde yönetici olarak oturum açın.

2. **Ayarlar \> Kullanıcılar & gruplar**' a gidin.

3. **Yeni Kullanıcı Ekle**' ye tıklayın ve sonra eklemek istediğiniz kullanıcı türünü seçin.
   
    ![Kullanıcılar](./media/egnyte-tutorial/add-user.png "Kullanıcılar")

4. **Yeni Power User** bölümünde aşağıdaki adımları uygulayın:
    
    ![Yeni standart Kullanıcı](./media/egnyte-tutorial/new-user.png "Yeni standart Kullanıcı")   

    a. **E-posta** metin kutusuna, **Brittasıon \@ contoso.com** gibi kullanıcının e-postasını girin.

    b. Kullanıcı **adı** metin kutusuna, **Brittasıon** gibi kullanıcının Kullanıcı adını girin.

    c. **Kimlik doğrulama türü** olarak **Çoklu oturum açma** seçeneğini belirleyin.
   
    d. **Kaydet**’e tıklayın.
    
    >[!NOTE]
    >Azure Active Directory hesap sahibi bir bildirim e-postası alır.
    >

>[!NOTE]
>Azure AD Kullanıcı hesaplarını sağlamak için, EGNX 'Ler tarafından sunulan diğer tüm Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.
>

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz EGN, oturum açma URL 'sine yönlendirecektir. 

* Doğrudan oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarımın içindeki Egni kutucuğunu tıklattığınızda, bu, EGN, oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Egnorut 'ları yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
