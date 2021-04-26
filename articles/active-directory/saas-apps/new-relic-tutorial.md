---
title: 'Öğretici: hesaba göre yeni relik ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Hesaba göre Azure Active Directory ve yeni yeniden oturum açma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: a2c149bfdf79102779abf7544fed9fb78796a50e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649988"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Öğretici: hesaba göre yeni relik ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile yeni bir relik hesabını nasıl tümleştirileceğini öğreneceksiniz. Yeni relik hesabını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de hesaba göre yeni relik erişimine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla hesaba göre otomatik olarak oturum açmaya olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Hesap çoklu oturum açma (SSO) etkin abonelik tarafından yeni relik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Yeni relik hesabı, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-new-relic-by-account-from-the-gallery"></a>Galeriden yeni bir relik hesabı ekleyin

Yeni relik hesabı ile Azure AD arasında tümleştirmeyi yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize yeni relik hesabı ekleyerek hesap eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Yeni bir relik hesabı** yazın.
1. Sonuçlar panelinden **Hesap bazında yeni relik** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>Yeni relik hesabı için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu yeni relik hesabıyla yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında yeni relik hesabı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu yeni relik hesabıyla yapılandırmak ve test etmek için aşağıdaki adımları uygulayın:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Hesap SSO 'su Ile yeni relik ayarlarını yapılandırma](#configure-new-relic-by-account-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * **[Hesap testi kullanıcısına göre yeni bir relik oluşturun](#create-new-relic-by-account-test-user)** ; bu, kullanıcının Azure AD gösterimine bağlı olan hesaba göre yeni relik 'e göre B. Simon 'ya sahip olacak şekilde
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Yeni hesap** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)
   
1. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak URL 'yi yazın:

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize` - `acc_id` Hesaba göre yeni relik Hesap Kimliğiniz ile değiştirdiğinizden emin olun.

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`rpm.newrelic.com`

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Hesaba göre yeni relik ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, yeni hesaba göre yeni relik erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Hesap bazında yeni relik**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-new-relic-by-account-sso&quot;></a>Hesap SSO 'SU ile yeni relik yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, hesap şirket sitesinde yönetici olarak **Yeni relik** ortamınızda oturum açın.

2. Üstteki menüde **Hesap ayarları**' na tıklayın.
   
    ![Ekran görüntüsü, hesap ayarları seçili olan karşılama sayfasını gösterir.](./media/new-relic-tutorial/settings.png &quot;Hesap ayarları")

3. **Güvenlik ve kimlik doğrulama** sekmesine tıklayın ve ardından **Çoklu oturum açma** sekmesine tıklayın.
   
    ![Çoklu oturum açma](./media/new-relic-tutorial/single-sign-on-tab.png "Çoklu Oturum Açma")

4. SAML iletişim sayfasında, aşağıdaki adımları uygulayın:
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. İndirilen Azure Active Directory sertifikanızı karşıya yüklemek için **Dosya Seç** ' e tıklayın.

    b. **Uzaktan oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
   
    c. **Oturum kapatma giriş URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    d. **Değişikliklerimi kaydet**' e tıklayın.

### <a name="create-new-relic-by-account-test-user"></a>Hesap testi kullanıcısına göre yeni relik oluştur

1. Hesap şirket sitesinde yönetici olarak **Yeni relik** ortamınızda oturum açın.

2. Üstteki menüde **Hesap ayarları**' na tıklayın.
   
    ![Ekran görüntüsü, hoş geldiniz sayfasından seçilen hesap ayarlarını gösterir.](./media/new-relic-tutorial/account.png "Hesap ayarları")

3. Sol taraftaki **Hesap** bölmesinde, **Özet**' e ve ardından **Kullanıcı Ekle**' ye tıklayın.
   
    ![Ekran görüntüsü, Kullanıcı Ekle ' yi seçebileceğiniz Özet bölmesini gösterir.](./media/new-relic-tutorial/add.png "Hesap ayarları")

4. **Etkin kullanıcılar** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Etkin Kullanıcılar](./media/new-relic-tutorial/user.png "Etkin Kullanıcılar")
   
    a. **E-posta** metin kutusuna, sağlamak istediğiniz geçerli bir Azure Active Directory kullanıcısının e-posta adresini yazın.

    b. **Rol** olarak **Kullanıcı**' yı seçin.

    c. **Bu kullanıcıyı Ekle**' ye tıklayın.

> [!NOTE]
> Hesap Kullanıcı hesabı oluşturma araçları veya Azure AD Kullanıcı hesaplarını sağlamak için yeni relik hesabı tarafından sunulan API 'Leri kullanarak başka bir yeni relik kullanmayı kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz hesap oturum açma URL 'sine göre yeni relik 'a yönlendirilir. 

* Hesap oturum açma URL 'sine doğrudan yeni relik 'e gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım ' da yeni relik ölçütü hesap kutucuğuna tıkladığınızda bu işlem, hesap oturum açma URL 'sine göre yeni relik 'e yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Hesap bazında yeni relik yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).