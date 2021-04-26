---
title: 'Öğretici: SDS ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory & Cheyasal bilgi yönetimi | Microsoft Docs'
description: Azure Active Directory ve SDS & kimyasal bilgi yönetimi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: e83274c404980549063b9a94a9118540f1ec3bb3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509350"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sds--chemical-information-management"></a>Öğretici: SDS ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory & Cheyasal bilgi yönetimi

Bu öğreticide, SDS & Cheyasal bilgi yönetimini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SDS & Cheyasal bilgi yönetimini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, SDS & kimyasal bilgi yönetimine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte & kimyasal bilgi yönetimi için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SDS & kimyasal bilgi yönetimi çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SDS & Cheyasal bilgi yönetimi, **SP** tarafından başlatılan SSO 'yu destekler.

* SDS & kimyasal bilgi yönetimi **, tam zamanında** Kullanıcı sağlamayı destekler.


## <a name="adding-sds--chemical-information-management-from-the-gallery"></a>Galeriden SDS & Cheyasal bilgi yönetimi ekleme

SDS & Chemik Information Management 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize SDS & Cheyasal bilgi yönetimi eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SDS & Cheyasal bilgi yönetimi** yazın.
1. Sonuçlar panelinden **SDS & Cheyasal bilgi yönetimi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-sds--chemical-information-management"></a>SDS & Cheyasal bilgi yönetimi için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu SDS & Cheyasal bilgi yönetimiyle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve SDS & kimyasal bilgi yönetimi ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SDS & Cheyasal bilgi yönetimi ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. , Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[SDS & Cheyasal bilgi yönetimi SSO 'Su yapılandırma](#configure-sds--chemical-information-management-sso)** .
    1. SDS **[& Cheyasal bilgi yönetimi sınama kullanıcısına](#create-sds--chemical-information-management-test-user)** , kullanıcının Azure AD gösterimine bağlı olan, SDS & kimyasal bilgi yönetimi ' ne karşılık gelen B. Simon 'a sahip olması IÇIN bir SDS oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **SDS & Cheyasal bilgi yönetimi** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://cs.cloudsds.com/saml/<ID>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://cs.cloudsds.com/saml/<ID>/consumeAssertion`
    
    c. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://cs.cloudsds.com/saml/<ID>/Login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'siyle güncelleştirin. Bu değerleri almak için [SDS & Cheyasal bilgi yönetimi istemci destek ekibine](mailto:info@cloudsds.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)
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

Bu bölümde, SDS & Cheyasal bilgi yönetimine erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **SDS & Cheyasal bilgi yönetimi**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sds--chemical-information-management-sso"></a>SDS 'yi yapılandırma & kimyasal bilgi yönetimi SSO 'SU

**Sds & Cheyasal bilgi yönetimi** tarafında çoklu oturum açmayı yapılandırmak Için, **uygulama Federasyon meta veri Url 'Sini** [SDS & cheyasal bilgi yönetimi destek ekibine](mailto:info@cloudsds.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-sds--chemical-information-management-test-user"></a>SDS & kimyasal bilgi yönetimi test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı bir Kullanıcı SDS & Cheyasal bilgi yönetimi ' nde oluşturulur. SDS & kimyasal bilgi yönetimi, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı SDS & Cheyasal bilgi yönetimi 'nde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz, SDS & Cheyasal bilgi yönetimi oturum açma URL 'sine yeniden yönlendirilir. 

* SDS & Cheyasal bilgi yönetimi oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki SDS & Cheyasal bilgi yönetimi kutucuğuna tıkladığınızda, bu işlem SDS 'ye & kimyasal bilgi yönetimi oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Sonraki adımlar

SDS & kimyasal bilgi yönetimini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


