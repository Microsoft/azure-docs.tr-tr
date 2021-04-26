---
title: 'Öğretici: Gigya ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Gigya arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6956b8ba2dcad0e67caac797a47e5308e649d042
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516114"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Öğretici: Gigya ile tümleştirme Azure Active Directory

Bu öğreticide, Gigya Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Gigya 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Gigya 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Gigya otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Gigya çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Gigya **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-gigya-from-the-gallery"></a>Galeriden Gigya ekleme

Gigya 'ın Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden Gigya 'yı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Gigya** yazın.
1. Sonuçlar panelinden **Gigya** ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-gigya"></a>Gigya için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Gigya Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Gigya 'da bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Gigya ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Gigya SSO 'Yu yapılandırma](#configure-gigya-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[Gigya test kullanıcısı oluşturun](#create-gigya-test-user)** ; Gigya 'da, kullanıcının Azure AD gösterimine bağlı olan B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Gigya** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`http://<companyname>.gigya.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Gigya istemci destek ekibine](https://developers.gigya.com/display/GD/Opening+A+Support+Incident) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Gigya ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Gigya 'e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Gigya** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-gigya-sso&quot;></a>Gigya SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Gigya şirket sitenizde yönetici olarak oturum açın.

2. **Ayarlar \> SAML oturum açma** bölümüne gidin ve **Ekle** düğmesine tıklayın.
   
    ![SAML oturum açma](./media/gigya-tutorial/login.png &quot;SAML oturum açma")

3. **SAML oturum açma** bölümünde aşağıdaki adımları uygulayın:
   
    ![SAML yapılandırması](./media/gigya-tutorial/configuration.png "SAML yapılandırması")
   
    a. **Ad** metin kutusuna yapılandırmanız için bir ad yazın.
   
    b. **Veren** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın. 
   
    c. **Tek Sign-On hizmet URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
   
    d. **Ad kimliği biçimi** metin kutusunda, Azure Portal kopyaladığınız **ad tanımlayıcı biçiminin** değerini yapıştırın.
   
    e. Azure portal 'den indirilen Not defteri 'nde Base-64 kodlu sertifikanızı açın, içeriğini panonuza kopyalayın ve **X. 509.440 sertifika** metin kutusuna yapıştırın.
   
    f. **Ayarları Kaydet**' e tıklayın.

## <a name="create-gigya-test-user"></a>Gigya test kullanıcısı oluşturma

Azure AD kullanıcılarının Gigya 'da oturum açmasını sağlamak için, Gigya 'a sağlanması gerekir. Gigya durumunda, sağlama el ile gerçekleştirilen bir görevdir.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:

1. **Gigya** şirket sitenizde yönetici olarak oturum açın.

2. **Yönetici \> Kullanıcıları Yönet**' e gidin ve ardından **kullanıcıları davet et**' i tıklatın.
   
    ![Kullanıcıları yönetme](./media/gigya-tutorial/users.png "Kullanıcıları Yönet")

3. Kullanıcıları davet et iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Kullanıcıları davet et](./media/gigya-tutorial/invite-user.png "Kullanıcıları davet et")
   
    a. **E-posta** metin kutusuna, sağlamak istediğiniz geçerli bir Azure Active Directory hesabının e-posta diğer adını yazın.
    
    b. **Kullanıcıyı davet et**' e tıklayın.
      
    > [!NOTE]
    > Azure Active Directory hesap sahibi, hesabı etkin olmadan önce onaylamaya yönelik bir bağlantı içeren bir e-posta alır.
    > 

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Gigya oturum açma URL 'sine yeniden yönlendirilir. 

* Gigya oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Gigya Kutucuğa tıkladığınızda, bu, Gigya oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Gigya 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
