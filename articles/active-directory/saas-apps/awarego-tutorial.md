---
title: 'Öğretici: AwareGo ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve AwareGo arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735352"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Öğretici: AwareGo ile çoklu oturum açma tümleştirmesi Azure Active Directory

Bu öğreticide, AwareGo 'Yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. AwareGo 'Yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de AwareGo 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla AwareGo 'da otomatik olarak oturum açmalarına izin vermek.
* Hesaplarınızı tek bir merkezi konumda yönetin, Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* AwareGo çoklu oturum açma (SSO) özellikli bir abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. AwareGo, bir hizmet sağlayıcısı (SP) tarafından başlatılan SSO 'Yu destekler.


## <a name="adding-awarego-from-the-gallery"></a>Galeriden AwareGo ekleme

AwareGo 'un Azure AD 'ye tümleştirmesini yapılandırmak için, Galeriden bir hizmet olarak yazılım (SaaS) uygulamaları listesine AwareGo eklemeniz gerekir.

1. İş hesabı, okul hesabı veya kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **awarego** yazın.
1. Sonuçlar bölmesinde, **Awarego**' yı seçin ve ardından uygulamayı ekleyin. Birkaç saniye içinde, uygulama kiracınıza eklenir.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>AwareGo için Azure AD SSO 'Yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak AwareGo Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, AwareGo içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'Yu AwareGo ile yapılandırmak ve test etmek için aşağıdakileri yapın:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .  

    a. Kullanıcı B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .  
    b. Azure AD çoklu oturum açma özelliğini kullanmak için Kullanıcı B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .  

1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[AwareGo SSO 'Yu yapılandırın](#configure-awarego-sso)** .

    a. Kullanıcının Azure AD gösterimine bağlı olan AwareGo 'da B. Simon 'a karşılık gelen bir **[awarego test kullanıcısı oluşturun](#create-an-awarego-test-user)** .  
    b. Yapılandırmanın çalıştığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için aşağıdakileri yapın:

1. Azure portal, **Awarego** uygulama tümleştirmesi sayfasında, **Yönet** altında, **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. Ayarları düzenlemek için, **SAML Ile tek Sign-On ayarlama** bölmesinde **Düzenle** düğmesini seçin.

   ![Temel SAML yapılandırması için Düzenle düğmesinin ekran görüntüsü.](common/edit-urls.png)

1. Düzen bölmesinde, **temel SAML yapılandırması** altında aşağıdakileri yapın:

    a. **Oturum açma URL 'si** kutusuna aşağıdaki URL 'lerden birini girin:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. **Tanımlayıcı (VARLıK kimliği)** kutusunda, aşağıdaki BIÇIMDE bir URL girin:`https://<SUBDOMAIN>.awarego.com`

    c. **Yanıt URL** 'si kutusuna aşağıdaki BIÇIMDE bir URL girin:`https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Yukarıdaki değerler gerçek değildir. Bunları gerçek tanımlayıcı ve yanıt URL 'Leriyle güncelleştirin. Değerleri almak için [Awarego istemci desteği ekibine](mailto:support@awarego.com)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümündeki örneklere da bakabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **sertifika (base64)** seçeneğinin yanındaki **İndir** ' i seçerek sertifikayı indirin ve bilgisayarınıza kaydedin.

    ![SAML Imzalama sertifikası bölmesindeki sertifikanın "Indir" bağlantısının ekran görüntüsü.](common/certificatebase64.png)

1. **AwareGo ayarla** bölümünde, gereksinimlerinize bağlı olarak bir veya daha fazla URL kopyalayın.

    ![Yapılandırma URL 'Lerini kopyalamak için "AwareGo ayarla" bölmesinin ekran görüntüsü.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmesinde **Azure Active Directory**' i seçin ve ardından **Kullanıcılar**  >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri bölmesinde şunları yapın:

   a. **Ad** kutusuna **B. Simon** girin.  
   b. **Kullanıcı adı** kutusuna şu biçimde Kullanıcı adını girin: `<username>@<companydomain>.<extension>` (örneğin, B.Simon@contoso.com ).  
   c. **Parolayı göster** onay kutusunu seçin ve ardından, daha sonra kullanmak üzere **parola** kutusunda görüntülenen değeri unutmayın.  
   d. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, AwareGo 'ya erişim vererek Kullanıcı B. Simon 'u Azure SSO 'Yu kullanacak şekilde etkinleştirirsiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. **Uygulamalar** listesinde, **awarego**' yı seçin.
1. Uygulamaya Genel Bakış sayfasında, **Yönet** bölümünde **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle** ' yi seçin ve sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** bölmesinde, **Kullanıcılar** listesinde, **B. Simon**' ı seçin ve ardından **Seç** düğmesini seçin.
1. Kullanıcılara bir rol atamayı bekliyorsanız, **rol seçin** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, *varsayılan erişim* rolü seçilidir.
1. **Atama Ekle** bölmesinde **ata** düğmesini seçin.

## <a name="configure-awarego-sso"></a>AwareGo SSO 'Yu yapılandırma

**Awarego** tarafında çoklu oturum açmayı yapılandırmak için, daha önce indirdiğiniz **sertifika (base64)** sertifikasını ve daha önce Azure Portal kopyaladığınız URL 'leri [awarego destek ekibine](mailto:support@awarego.com)gönderin. Destek ekibi, SAML SSO bağlantısını her iki tarafta da doğru şekilde kurmak için bu ayarı oluşturur.

### <a name="create-an-awarego-test-user"></a>AwareGo test kullanıcısı oluşturma

Bu bölümde, AwareGo 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Awarego platformu içindeki kullanıcıları eklemek için [awarego destek ekibi](mailto:support@awarego.com) ile çalışın. Çoklu oturum açmayı kullanabilmeniz için önce kullanıcıları oluşturmanız ve etkinleştirmeniz gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, aşağıdakilerden birini yaparak Azure AD çoklu oturum açma yapılandırmanızı test edebilirsiniz: 

* Azure portal **Bu uygulamayı test et**' i seçin. Bu, sizi oturum açma akışını başlatabileceğiniz AwareGo oturum açma sayfasına yönlendirir. 

* Doğrudan AwareGo oturum açma sayfasına gidin ve oturum açma akışını buradan başlatın.

* Microsoft uygulamalarım ' a gidin. Uygulamamda **awarego** kutucuğunu seçtiğinizde, awarego oturum açma sayfasına yönlendirilirsiniz. Daha fazla bilgi için bkz. [uygulamalarım portalından oturum açma ve uygulamaları başlatma](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

AwareGo yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi Koşullu Erişim Uygulama Denetimi genişletiliyor. Daha fazla bilgi için bkz. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).