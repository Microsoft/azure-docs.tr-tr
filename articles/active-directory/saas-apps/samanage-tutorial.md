---
title: 'Öğretici: SolarWinds Service Desk ile tümleştirme Azure Active Directory (daha önce Samanage) | Microsoft Docs'
description: Azure Active Directory ve SolarWinds Service Desk (daha önce Samanage) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 6dcd5612bd2c5957ae0a397c3463dbb42445a754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956052"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Öğretici: SolarWinds Service Desk ile tümleştirme Azure Active Directory (daha önce Samanage)

Bu öğreticide, SolarWinds 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SolarWinds 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SolarWinds 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SolarWinds otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SolarWinds çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SolarWinds, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-solarwinds-from-the-gallery"></a>Galeriden SolarWinds ekleme

SolarWinds tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden SolarWinds yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Solarwinds** yazın.
1. Sonuçlar panelinden **Solarwinds** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>SolarWinds için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Solarwinds ile yapılandırın ve test edin. SSO 'nun çalışması için, SolarWinds içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SolarWinds ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[SOLARWINDS SSO 'Yu yapılandırın](#configure-solarwinds-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan SolarWinds 'de B. Simon 'ya karşılık gelen bir **[Solarwinds test kullanıcısı oluşturun](#create-solarwinds-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Solarwinds** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Bu değerler gerçek değildir. Bu değerleri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek oturum açma URL 'SI ve tanımlayıcı ile güncelleştirin. Daha fazla ayrıntı için [bkz. istemci desteği ekibine](https://www.samanage.com/support)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Solarwinds ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, SolarWinds 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Solarwinds**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

<a name=&quot;configure-solarwinds-single-sign-on&quot;></a>

## <a name=&quot;configure-solarwinds-sso&quot;></a>SolarWinds SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, SolarWinds şirket sitenizde yönetici olarak oturum açın.

2. **Pano** ' ya tıklayın ve sol gezinti bölmesinde **Kurulum** ' u seçin.
   
    ![Pano](./media/samanage-tutorial/tutorial-samanage-1.png &quot;Pano")

3. **Çoklu oturum açma**' ya tıklayın.
   
    ![Çoklu oturum açma](./media/samanage-tutorial/tutorial-samanage-2.png "Çoklu Oturum Açma")

4. **SAML kullanarak oturum açma** bölümüne gidin, aşağıdaki adımları uygulayın:
   
    ![SAML kullanarak oturum açma](./media/samanage-tutorial/tutorial-samanage-3.png "SAML kullanarak oturum açma")
 
    a. **SAML Ile tek Sign-On etkinleştir**' e tıklayın.  
 
    b. **Kimlik sağlayıcısı URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın.    
 
    c. **Oturum açma URL** 'sini Azure Portal ' DEKI **temel SAML yapılandırması** 'nın **oturum açma URL 'si** ile eşleştiğini doğrulayın.
 
    d. **Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini girin.
 
    e. **SAML veren** metin kutusunda, kimlik sağlayıcınızda uygulama kimliği URI 'si kümesini yazın.
 
    f. Not defteri 'nde Azure portal indirilen Base-64 kodlu sertifikanızı açın, bu içeriği panonuza kopyalayın ve ardından **kimlik sağlayıcısını aşağıdaki** metin kutusuna yapıştırın.
 
    örneğin: **Solarwinds içinde yoksa kullanıcı oluştur**' a tıklayın.
 
    h. **Güncelleştir**’e tıklayın.

### <a name="create-solarwinds-test-user"></a>SolarWinds test kullanıcısı oluştur

Azure AD kullanıcılarının SolarWinds 'de oturum açmasını sağlamak için, SolarWinds ' a sağlanması gerekir.  
SolarWinds durumunda sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. SolarWinds şirket sitenizde yönetici olarak oturum açın.

2. **Pano** ' ya tıklayın ve sol gezinti kaydırma bölmesinde **Kurulum** ' u seçin.
   
    ![Kurulum](./media/samanage-tutorial/tutorial-samanage-1.png "Kurulum")

3. **Kullanıcılar** sekmesine tıklayın
   
    ![Kullanıcılar](./media/samanage-tutorial/tutorial-samanage-6.png "Kullanıcılar")

4. **Yeni Kullanıcı**' ya tıklayın.
   
    ![Yeni Kullanıcı](./media/samanage-tutorial/tutorial-samanage-7.png "Yeni Kullanıcı")

5. Sağlamak istediğiniz Azure Active Directory hesabının **adını** ve **e-posta adresini** yazın ve **Kullanıcı oluştur**' a tıklayın.
   
    ![Create User](./media/samanage-tutorial/tutorial-samanage-8.png "Kullanıcı Oluştur")
   
   >[!NOTE]
   >Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını onaylamak için bir bağlantıyı izler. Azure Active Directory Kullanıcı hesapları sağlamak için, SolarWinds tarafından sunulan diğer tüm SolarWinds Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz SolarWinds oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan SolarWinds oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki SolarWinds kutucuğuna tıkladığınızda bu, SolarWinds oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

SolarWinds yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).