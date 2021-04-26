---
title: 'Öğretici: Workware ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Workware arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f4996943fd95252be45f7a270d143f14e7f2d48a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181292"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Öğretici: Workware ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Workware 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Iş Ware 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Workware 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Iş için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Workware çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Workware, **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-workware-from-the-gallery"></a>Galeriden Iş yazılımı ekleme

Workware 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, galerideki Iş Ware 'yi yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **workware** yazın.
1. Sonuçlar panelinden **Workware** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Workware için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu, workware ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve iş ortamında ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Iş arkadaşlarınızla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Workware SSO 'Yu yapılandırma](#configure-workware-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * Kullanıcının Azure AD gösterimine bağlı olan Workware 'de B. Simon 'a karşılık gelen bir **[iş sürümü test kullanıcısı oluşturun](#create-workware-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Workware** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`<WORKWARE_URL>/WW/AuthServices`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Workware istemci destek ekibine](mailto:support@activeops.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Workware ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Workware 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Workware**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-workware-sso"></a>Workware SSO 'yu yapılandırma

Iş Ware 'de SSO özelliğini kullanmak için aşağıdaki kurulumun tamamlanması gerekir:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Workware sistem yöneticileri için SSO izinlerini etkinleştirme

* Iş yazılımı sistem yöneticilerinin SSO kimlik doğrulamasını ayarlamaya izin vermek için, SSO kimlik doğrulaması izninin ( **sistem ayarları > rol ekranındaki yönetim > sistem yapılandırma izinleri kategorisinde** ), workware sistem yöneticileri için etkinleştirilmesi gerekir.

    ![SSO kimlik doğrulaması izni](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Iş Ware 'de SSO kimlik doğrulamasını ayarlama

1. **Sistem ayarları** sayfasına gidin ve **SSO kimlik doğrulaması** ' na tıklayın

1. **SSO kimlik doğrulaması** bölümünde, **SSO kimlik doğrulaması ekle** düğmesine tıklayın ve aşağıdaki adımları uygulayın: 

    ![SSO kimlik doğrulaması](./media/workware-tutorial/authentication.png)

    1. **Dış kimlik sağlayıcısında** IDP adını belirtin.
    1. **Kimlik doğrulama türü** olarak **SAML 2.0** 'ı seçin
    1. **Kimlik sağlayıcısı oturum açma URL 'si** metin kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini girin.
    1. **Kimlik sağlayıcısı veren URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini girin.
    1. **Kimlik sağlayıcısı oturum kapatma URL 'si** metin kutusuna, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini girin.
    1. **Etkinleştir**' e tıklayın.
    1. İndirilen **sertifikayı** Azure Portal **kimlik sağlayıcısı sertifikasına** yükleyin.
    1. **Kaydet**’e tıklayın


### <a name="create-workware-test-user"></a>Workware test kullanıcısı oluşturma

1. Workware Web sitesinde yönetici olarak oturum açın.

1. **Yönetici > > Kullanıcı hesapları oluştur/görüntüle > Yeni Ekle** ' yi seçin

1. Aşağıdaki sayfada aşağıdaki adımları gerçekleştirin.

    ![Test kullanıcısı](./media/workware-tutorial/create-user.png)

    a. **Ad** alanına geçerli bir ad verin.

    b. **Kimlik doğrulama türünü** **SSO** olarak seçin.

    c. Gerekli alanları girin ve **Kaydet**' e tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Iş Ware 'de otomatik olarak oturum açmış olmanız gerekir

* Microsoft Access panel ' i kullanabilirsiniz. Erişim panelinde Workware kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Iş Ware 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Workware 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).