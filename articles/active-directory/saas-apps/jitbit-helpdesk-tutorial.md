---
title: 'Öğretici: Jbıt yardım masası ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Jbıt yardım masası arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: b3fbf73ab51092f614a416355477fc552a404fd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581822"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Öğretici: Jbıt yardım masanızla Azure Active Directory tümleştirme

Bu öğreticide, Jbıt yardım masanızla Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Jbıt yardım masasına Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Jbıt yardım masasına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Jbıt yardım masasına otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Jbıbit yardım masası çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Jbıt Yardım Masası **SP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Galeriden Jbıt yardım masası ekleme

Jbıt yardım masasının Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Jbıt yardım masası eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Jbıt yardım masası** yazın.
1. Sonuçlar panelinden **Jbıt yardım masası** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Jbıt yardım masası için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Jbım yardım MASANıZLA Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Jbıt yardım masasının bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Jbıt yardım masası ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Jbıt yardım masası SSO 'Yu yapılandırma](#configure-jitbit-helpdesk-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[Jbım yardım masası test kullanıcısı oluşturma](#create-jitbit-helpdesk-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Jbım yardım masanızla birlikte B. Simon ' a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Jbıt yardım masası** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak URL 'lerden birini yazın:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Bu değer gerçek değil. Bu değeri gerçek Sign-On URL 'siyle güncelleştirin. Bu değeri almak için, [Jbıt yardım masası istemci destek ekibine](https://www.jitbit.com/support/) başvurun.

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`https://www.jitbit.com/web-helpdesk/`

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Jbit yardım masası 'Nı ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Jon bit yardım masasına erişim izni vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Jbıt yardım masası**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-jitbit-helpdesk-sso&quot;></a>Jbıt yardım masası SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Jbıt yardım masası şirket sitenizde yönetici olarak oturum açın.

1. Üstteki araç çubuğunda **Yönetim**' e tıklayın.

    ![Yönetim](./media/jitbit-helpdesk-tutorial/settings.png &quot;Yönetim")

1. **Genel ayarlar**' a tıklayın.

    ![Ekran görüntüsü Genel Ayarlar bağlantısını gösterir.](./media/jitbit-helpdesk-tutorial/general.png "Kullanıcılar, şirketler ve izinler")

1. **Kimlik doğrulama ayarları** yapılandırması bölümünde aşağıdaki adımları uygulayın:

    ![Kimlik doğrulaması ayarları](./media/jitbit-helpdesk-tutorial/authentication.png "Kimlik doğrulaması ayarları")

    a. **Onelogin** ile tek Sign-On (SSO) kullanarak oturum açmak için **SAML 2,0 çoklu oturum açmayı etkinleştir**' i seçin.

    b. **Uç nokta URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Base-64** kodlu sertifikanızı Not defteri 'nde açın, bu içeriği panonuza kopyalayın ve **X. 509.440 sertifikası** metin kutusuna yapıştırın

    d. **Değişiklikleri Kaydet**' e tıklayın.

### <a name="create-jitbit-helpdesk-test-user"></a>Jbıt yardım masası test kullanıcısı oluşturma

Azure AD kullanıcılarının, Jbıt yardım masasına oturum açmasını sağlamak için, bu kullanıcıların Jbıt yardım masasına sağlanması gerekir. Jbıt yardım masasının sağlanması, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Jbıt yardım masası** kiracınızda oturum açın.

1. Üstteki menüde **Yönetim**' e tıklayın.

    ![Yönetim](./media/jitbit-helpdesk-tutorial/settings.png "Yönetim")

1. **Kullanıcılar, şirketler ve izinler**' e tıklayın.

    ![Kullanıcılar, şirketler ve izinler](./media/jitbit-helpdesk-tutorial/users.png "Kullanıcılar, şirketler ve izinler")

1. **Kullanıcı Ekle**' ye tıklayın.

    ![Kullanıcı ekle](./media/jitbit-helpdesk-tutorial/add.png "Kullanıcı ekleme")

1. Oluştur bölümünde, sağlamak istediğiniz Azure AD hesabının verilerini aşağıdaki gibi yazın:

    ![Oluştur](./media/jitbit-helpdesk-tutorial/create-section.png "Oluştur")

   a. Kullanıcı **adı** metin kutusuna, **Brittasıon** gibi kullanıcının Kullanıcı adını yazın.

   b. **E-posta** metin kutusuna, gibi kullanıcının e-postasını yazın **BrittaSimon@contoso.com** .

   c. **Ilk ad** metin kutusuna, **Britta** gibi kullanıcının adını yazın.

   d. **Soyadı** metin kutusunda, **Simon** adlı kullanıcının soyadını yazın.

   e. **Oluştur**’a tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesaplarını sağlamak için, jbıx yardım masası tarafından sunulan diğer bir Jbım Yardım Masası Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Jbıt yardım masası oturum açma URL 'sine yeniden yönlendirilir. 

* Jbıt yardım masası oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Jbım yardım masası kutucuğuna tıkladığınızda bu işlem, Jbım yardım masası oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Jbıt yardım masası 'nı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).