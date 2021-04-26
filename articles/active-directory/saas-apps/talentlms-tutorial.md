---
title: 'Öğretici: TalentLMS ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve TalentLMS arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 84d50d19a8356418a5cbf1f93784e5b8816d4be5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689360"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Öğretici: TalentLMS ile tümleştirme Azure Active Directory

Bu öğreticide, TalentLMS 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. TalentLMS 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de TalentLMS 'ye erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla TalentLMS 'ye otomatik olarak kaydolmalarına olanak tanır.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini TalentLMS ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* TalentLMS çoklu oturum açma etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* TalentLMS, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-talentlms-from-the-gallery"></a>Galeriden TalentLMS ekleme

TalentLMS 'nin tümleştirmesini Azure AD ile yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize TalentLMS eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **TalentLMS** yazın.
1. Sonuçlar panelinden **TalentLMS** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>TalentLMS için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak TalentLMS Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile TalentLMS içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu TalentLMS ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[TalentLMS SSO 'Yu yapılandırın](#configure-talentlms-sso)** .
    1. Kullanıcı Azure AD gösterimi ile bağlantılı olan TalentLMS 'de B. Simon 'a sahip olmak için **[TalentLMS test kullanıcısı oluşturun](#create-talentlms-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **TalentLMS** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![TalentLMS etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.TalentLMSapp.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [TalentLMS istemci destek ekibine](https://www.talentlms.com/contact) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

6. **SAML Imzalama sertifikası** bölümünde, **parmak izini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

7. **TalentLMS ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, TalentLMS 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **TalentLMS**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name=&quot;configure-talentlms-sso&quot;></a>TalentLMS SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, TalentLMS şirket sitenizde yönetici olarak oturum açın.

1. **Hesap & ayarları** bölümünde, **Kullanıcılar** sekmesine tıklayın.

    ![Hesap & ayarları](./media/talentlms-tutorial/IC777296.png &quot;Hesap & ayarları")

1. **Tek Sign-On (SSO)** öğesine tıklayın,

1. Tek Sign-On bölümünde aşağıdaki adımları uygulayın:

    ![Çoklu oturum açma](./media/talentlms-tutorial/saml.png "Çoklu Oturum Açma")

    a. **SSO tümleştirme türü** listesinden **SAML 2,0**' yi seçin.

    b. **Kimlik sağlayıcısı (ıDP)** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    c. **Parmak izi** değerini Azure Portal **sertifika parmak izi** metin kutusuna yapıştırın.

    d.  **Uzaktan oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. **Uzak oturum kapatma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    f. Aşağıdakileri doldurun:

    * **Targetedıd** metin kutusunda şunu yazın`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * **Ad** metin kutusuna şunu yazın`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * **Soyadı** metin kutusuna şunu yazın`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * **E-posta** metin kutusuna şunu yazın`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. **Kaydet**’e tıklayın.

### <a name="create-talentlms-test-user"></a>TalentLMS test kullanıcısı oluşturma

Azure AD kullanıcılarının TalentLMS 'de oturum açmasını sağlamak için, TalentLMS 'ye sağlanması gerekir. TalentLMS söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **TalentLMS** kiracınızda oturum açın.

1. **Kullanıcılar**' a ve ardından **Kullanıcı Ekle**' ye tıklayın.

1. **Kullanıcı Ekle** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Kullanıcı Ekle](./media/talentlms-tutorial/IC777299.png "Kullanıcı Ekleme")  

    a. **İlk ad** metin kutusuna, gibi kullanıcının adını girin `Britta` .

    b. **Son ad** metin kutusuna, gibi kullanıcının soyadını girin `Simon` .
 
    c. **E-posta adresi** metin kutusuna, gibi kullanıcının e-postasını girin `brittasimon@contoso.com` .

    d. **Kullanıcı Ekle**'ye tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için TalentLMS tarafından sunulan diğer tüm TalentLMS Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz TalentLMS oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan TalentLMS oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki TalentLMS kutucuğuna tıkladığınızda bu, TalentLMS oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

TalentLMS 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).