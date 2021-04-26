---
title: 'Öğretici: Netmini kuruluş Enterprise ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Netmini kuruluş arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: ccf96ddc2d223b4643c280acaa1fd7b6e734ad85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181943"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsparker-enterprise"></a>Öğretici: Netmini yazılım ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Netmini BT Enterprise 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Netmini BT Enterprise 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Netmini kuruluş kuruma erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Netmini BT Enterprise 'ta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Netparlak er Enterprise çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Netparlak er Enterprise **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Netparlak er Enterprise **, tam zamanında** Kullanıcı sağlamasını destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.


## <a name="adding-netsparker-enterprise-from-the-gallery"></a>Galeriden Netmini er Enterprise ekleme

Netmini BT Enterprise 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Netparlak er Enterprise eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **Netparlak er Enterprise** yazın.
1. Sonuçlar panelinden **Netparlak er Enterprise** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-netsparker-enterprise"></a>Netmini yazılım için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Netparlak er Enterprise ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Netmini er Enterprise içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Netmini yazılım ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Netmini kuruluş SSO 'Yu yapılandırma](#configure-netsparker-enterprise-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Netparlak **[er Enterprise test kullanıcısı oluşturun](#create-netsparker-enterprise-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan Netmini er Enterprise 'ta B. Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Netmini kuruluş kurumsal** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.netsparkercloud.com/account/assertionconsumerservice/?spId=<SPID>`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın:`https://www.netsparkercloud.com/account/ssosignin/`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Değeri gerçek yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Netmini yazılım Kurumsal istemci desteği ekibine](mailto:support@netsparker.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz. 

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Netparlak kuruluş ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Netmini BT Enterprise 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Netparlak er Enterprise**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-netsparker-enterprise-sso"></a>Netmini yazılım SSO 'yu yapılandırma

1.  Netmini kuruluş 'da yönetici olarak oturum açın.

1. **Çoklu oturum açma > ayarlar '** a gidin.

1. **Çoklu oturum açma** penceresinde **Azure Active Directory** sekmesini seçin. 

1. Aşağıdaki sayfada aşağıdaki adımları gerçekleştirin.

    ![Azure Active Directory sekmesi](./media/netsparker-enterprise-tutorial/configure-sso.png)

    a. **Tanımlayıcı** değerini kopyalayın, bu değeri Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** metin kutusuna yapıştırın.

    b. **SAML 2,0 hizmeti URL 'si** değerini kopyalayın, bu değeri Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'si** metin kutusuna yapıştırın.

    c. Azure portal, **IDP tanımlayıcı** alanına kopyaladığınız **tanımlayıcı** değerini yapıştırın.

    e. Azure portal kopyaladığınız **yanıt URL 'si** değerini **SAML 2,0 uç noktası** alanına yapıştırın.

    f. İndirilen **sertifikayı (base64)** Azure Portal Not defteri ' nden açın ve içeriği **x. 509.440 sertifikası** metin kutusuna yapıştırın.

    örneğin: **Otomatik sağlamayı etkinleştir** ve **SAML onayları 'nin gerektiği şekilde şifrelenmesini gerektir** ' i işaretleyin.

    h. **Değişiklikleri Kaydet**’e tıklayın.

### <a name="create-netsparker-enterprise-test-user"></a>Netmini kuruluş test kullanıcısı oluşturma

Bu bölümde, pMini er Enterprise 'ta Britta Simon adlı bir Kullanıcı oluşturulur. Netparlak er Enterprise, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Netmini kuruluş kuruluşunda zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Netmini bir kurumsal oturum açma URL 'sine yönlendirecektir.  

* Doğrudan Netmini şirket oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Netmini BT kuruluşunda otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft Access panel ' i de kullanabilirsiniz. Erişim paneli 'nde Netmini şirket kurumsal kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Netmini BT kuruluşunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Netmini BT Enterprise 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).