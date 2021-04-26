---
title: 'Öğretici: Samsara ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Samsara arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: jeedes
ms.openlocfilehash: 9512287f656c6c64399cb8749b7451a5a780bba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsara"></a>Öğretici: Samsara ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Samsara 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Samsara 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Samsara 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Samsara otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Samsara çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Samsara **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler
* Samsara **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-samsara-from-the-gallery"></a>Galeriden Samsara ekleme

Samsara tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Samsara yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)
    
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

1. **Galeriden Ekle** bölümünde, arama kutusuna **Samsara** yazın.

     ![Sonuçlar listesinde OneTrust gizlilik yönetimi yazılımı](common/search-new-app.png)

1. Sonuçlar panelinden **Samsara** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-samsara"></a>Samsara için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Samsara ile yapılandırın ve test edin. SSO 'nun çalışması için, Samsara içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Samsara ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Samsara SSO 'Yu yapılandırın](#configure-samsara-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Samsara 'de B. Simon 'ya karşılık gelen bir **[Samsara test kullanıcısı oluşturun](#create-samsara-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Samsara** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL YAZıN: ABD 'de, `https://cloud.samsara.com/signin/<ORGID>` `https://cloud.eu.samsara.com/signin/<ORGID>` AB bulut müşterilerine yönelik bulut müşterileri

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`urn:auth0:samsara-dev:samlp-orgid-<ORGID>`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://samsara-dev.auth0.com/login/callback?connection=samlp-orgid-<ORGID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI, yanıt URL 'SI ve tanımlayıcı ile güncelleştirin. Bu değerleri almak için [Samsara istemci destek ekibine](mailto:support@samsara.com) başvurun veya Samsara ' de, **Ayarlar**' a gidin  >    >  ve **Yeni SAML bağlantısı** ' na giderek ' ı edinin \<ORGID\> . Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de bakabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Samsara ayarla** bölümünde, **oturum açma URL** 'sini kopyalayın

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

Bu bölümde, Samsara 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Samsara**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-samsara-sso"></a>Samsara SSO 'yu yapılandırma

**Samsara** Side çoklu oturum açmayı yapılandırmak için, indirilen **sertifika (base64)** ve **oturum açma URL 'sini** Azure Portal [Destek ekibine](mailto:support@samsara.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-samsara-test-user"></a>Samsara test kullanıcısı oluştur

Bu bölümde, Samsara içinde B. Simon adlı bir Kullanıcı oluşturulur. Samsara, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Samsara içinde zaten yoksa, kuruluş için standart yönetici (Dash Cam erişimi olmayan) varsayılan rolüyle kimlik doğrulamasından sonra yeni bir tane oluşturulur. Kullanıcının erişimi daha sonra Samsara ' de gerektiği şekilde arttırılabiliyor veya azaltılabilir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

1. Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Samsara oturum açma URL 'sine yeniden yönlendirilir. 

2. Doğrudan Samsara oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

3. Microsoft Access panel ' i kullanabilirsiniz. Erişim panelinde Samsara kutucuğuna tıkladığınızda bu, Samsara oturum açma URL 'sine yeniden yönlendirilir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Samsara yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).