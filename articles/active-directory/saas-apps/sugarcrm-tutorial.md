---
title: 'Öğretici: cukr CRM ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve cukr CRM arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 8c0bbebf9fdc9e8027b947beb037dde47b26b67b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644859"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Öğretici: cukr CRM ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, cukr CRM 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Cukr CRM 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de cukr CRM 'ye erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla cukr CRM 'de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Cukr CRM çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Cukr CRM **SP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-sugar-crm-from-the-gallery"></a>Galeriden cukr CRM ekleme

Cukr CRM 'nin tümleştirmesini Azure AD ile yapılandırmak için, Galeriden cukr CRM 'yi yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **cukr CRM** yazın.
1. Sonuçlar panelinden **CUKR CRM** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-sugar-crm"></a>Cukr CRM için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'YU cukr CRM ile yapılandırın ve test edin. SSO 'nun çalışması için, cukr CRM 'de bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu cukr CRM ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[CUKR CRM SSO 'Yu yapılandırın](#configure-sugar-crm-sso)** .
    1. Cukr CRM 'de kullanıcının Azure AD gösterimine bağlı olan B. Simon 'a sahip olmak için **[CUKR CRM test kullanıcısı oluşturun](#create-sugar-crm-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **CUKR CRM** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [CUKR CRM istemci destek ekibine](https://support.sugarcrm.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **CUKR CRM 'Yi ayarlama** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, cukr CRM 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **CUKR CRM**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-sugar-crm-sso&quot;></a>Cukr CRM SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, cukr CRM şirket sitenizde yönetici olarak oturum açın.

1. **Yönetici**'ye gidin.

    ![Yönetici](./media/sugarcrm-tutorial/ic795888.png &quot;Yönetici")

1. **Yönetim** bölümünde, **parola yönetimi**' ne tıklayın.

    ![Ekran görüntüsü, parola yönetimini seçebileceğiniz yönetim bölümünü gösterir.](./media/sugarcrm-tutorial/ic795889.png "Yönetim")

1. **SAML kimlik doğrulamasını etkinleştir**' i seçin.

    ![Ekran görüntüsü SAML kimlik doğrulaması seçme seçeneğini gösterir.](./media/sugarcrm-tutorial/ic795890.png "Yönetim")

1. **SAML kimlik doğrulaması** bölümünde aşağıdaki adımları uygulayın:

    ![SAML kimlik doğrulaması](./media/sugarcrm-tutorial/save.png "SAML kimlik doğrulaması")  

    a. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
  
    b. **SLO URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.
  
    c. Base-64 kodlu sertifikanızı Not defteri 'nde açın, bu içeriği panonuza kopyalayın ve ardından tüm sertifikayı **X. 509.440 sertifikası** metin kutusuna yapıştırın.
  
    d. **Kaydet**’e tıklayın.

### <a name="create-sugar-crm-test-user"></a>Cukr CRM test kullanıcısı oluşturma

Azure AD kullanıcılarının cukr CRM 'de oturum açmasını sağlamak için, cukr CRM 'ye sağlanması gerekir. Cukr CRM söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **CUKR CRM** şirket sitenizde yönetici olarak oturum açın.

1. **Yönetici**'ye gidin.

    ![Yönetici](./media/sugarcrm-tutorial/ic795888.png "Yönetici")

1. **Yönetim** bölümünde **Kullanıcı yönetimi**' ne tıklayın.

    ![Ekran görüntüsü, Kullanıcı yönetimini seçebileceğiniz yönetim bölümünü gösterir.](./media/sugarcrm-tutorial/ic795893.png "Yönetim")

1. **Kullanıcılar \> Yeni Kullanıcı oluştur**' a gidin.

    ![Yeni Kullanıcı Oluştur](./media/sugarcrm-tutorial/ic795894.png "Yeni Kullanıcı Oluştur")

1. **Kullanıcı profili** sekmesinde aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı Profili sekmesini gösterir.](./media/sugarcrm-tutorial/ic795895.png "Yeni Kullanıcı")

    * **Kullanıcı adını**, **soyadını** ve ilgili metin kutularına geçerli bir Azure Active Directory kullanıcının **e-posta adresini** yazın.
  
1. **Durum** olarak **etkin**' i seçin.

1. Parola sekmesinde aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz parolayı görebileceğiniz parola sekmesini gösterir.](./media/sugarcrm-tutorial/ic795896.png "Yeni Kullanıcı")

    a. Parolayı ilgili metin kutusuna yazın.

    b. **Kaydet**’e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için cukr CRM tarafından sunulan diğer tüm cukr CRM Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz cukr CRM oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan cukr CRM oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki cukr CRM kutucuğuna tıkladığınızda bu, cukr CRM oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Cukr CRM 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).