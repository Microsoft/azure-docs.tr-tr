---
title: 'Öğretici: deskman NEO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve deskneti arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ef3e024e1b704cc9f8711d9ca78ee916acf72680
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580798"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Öğretici: desknet 'in NEO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, desknet 'in NEO 'ın Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Desknet 'in NEO 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de desknet 'in NEO 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla desknet 'in NEO 'ya otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* desknet 'in NEO çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* desknet 'in NEO dili, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-desknets-neo-from-the-gallery"></a>Galerideki desknet 'in NEO 'ı ekleme

Desknet 'in NEO 'ı Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize desknet 'in NEO 'ı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **desknet ' in Neo** yazın.
1. Sonuçlar panelinden **desknet ' ın Neo** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Desknet 'in NEO için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak desknet 'ın Neo 'Iyle Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve desknet 'in NEO 'da ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu desknet 'in NEO 'iyle yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[desknet 'ın NEO SSO 'Yu yapılandırın](#configure-desknets-neo-sso)** .
    1. Desknet ' in, kullanıcının Azure AD gösterimine bağlı olan NEO 'da B. Simon 'a sahip olmasını sağlamak için **[desknet 'ın Neo test kullanıcısı oluşturun](#create-desknets-neo-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **desknet 'ın NEO** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CUSTOMER_NAME>.dn-cloud.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'si ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [desknet 'ın NEO istemci destek ekibine](mailto:cloudsupport@desknets.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Desknet 'ın NEO bölümünü ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, desknet 'in NEO 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **desknet ' ın NEO**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-desknets-neo-sso"></a>Desknet 'in NEO SSO 'yu yapılandırma

1. Desknet 'in NEO şirket sitesinde yönetici olarak oturum açın.

1. Menüsünde, **SAML kimlik doğrulama bağlantısı ayarları** simgesi ' ne tıklayın.

    ![SAML kimlik doğrulaması bağlantı ayarlarının ekran görüntüsü.](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. **Ortak ayarlarda**, SAML kimlik doğrulaması işbirliğiyle **kullan** ' a tıklayın.

    ![SAML kimlik doğrulaması kullanımı için ekran görüntüsü.](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. **SAML kimlik doğrulama bağlantısı ayarları** bölümünde aşağıdaki adımları gerçekleştirin.

    ![SAML kimlik doğrulaması bağlantı ayarları bölümünün ekran görüntüsü.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. **Erişim URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **SP VARLıK kimliği** metin kutusunda, Azure Portal kopyaladığınız **tanımlayıcı** değerini yapıştırın.

    c. İndirilen **sertifika (base64)** dosyasını Azure Portal **x. 509.440 sertifikası** metin kutusuna yüklemek için **Dosya Seç** ' e tıklayın.

    d. **Değiştir**' e tıklayın.

### <a name="create-desknets-neo-test-user"></a>Desknet 'in NEO test kullanıcısını oluşturma

1. Desknet 'in NEO şirket sitesinde yönetici olarak oturum açın.

1. **Menüsünde**, **yönetici ayarları** simgesine tıklayın.

    ![Yönetici ayarları için ekran görüntüsü.](./media/desknets-neo-tutorial/administrator-settings.png)

1. **Ayarlar** simgesine tıklayın ve **özel ayarlar**' da **Kullanıcı yönetimi** ' ni seçin.

    ![Kullanıcı yönetimi ayarları için ekran görüntüsü.](./media/desknets-neo-tutorial/user-management.png)

1. **Kullanıcı bilgileri oluştur**' a tıklayın.

    ![Kullanıcı bilgileri düğmesi için ekran görüntüsü.](./media/desknets-neo-tutorial/create-new-user.png)

1. Aşağıdaki sayfada gerekli alanları doldurup **Oluştur**' a tıklayın.

    ![Kullanıcı oluşturma bölümünün ekran görüntüsü.](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz desknet 'in NEO oturum açma URL 'sine yeniden yönlendirilir. 

* Desknet 'in NEO oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki desknet 'in NEO kutucuğunu tıklattığınızda, bu, desknet 'in NEO 'da oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Sonraki adımlar

Desknet 'in NEO 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


