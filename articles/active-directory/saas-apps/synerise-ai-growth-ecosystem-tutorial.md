---
title: 'Öğretici: Synerıse AI büyüme Işletim sistemi ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Synerıse AI büyüme Işletim sistemi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: jeedes
ms.openlocfilehash: 0ffc6543a2597e39470c0d4aff86e7d5a1631d69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736644"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synerise-ai-growth-operating-system"></a>Öğretici: Synerıse AI büyüme Işletim sistemiyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Synerıse tümleştirme hakkında bilgi edineceksiniz. Synerıse 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Synerıse erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla eşitlemek için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Synerıse çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Synerıse **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Synerıse **, tam zamanında** Kullanıcı sağlamayı destekliyor

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.


## <a name="adding-synerise-ai-growth-operating-system-from-the-gallery"></a>Galeriden Synerıse AI büyüme Işletim sistemi ekleme

Synerıse 'nin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Synerıse eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SYNERıSE AI büyüme işletim sistemi** yazın.
1. Sonuçlar panelinden **Synerıse AI büyüme Işletim sistemi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-synerise-ai-growth-operating-system"></a>Synerıse AI büyüme Işletim sistemi için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Synerıse ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Synerıse içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Synerıse ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Synerıse AI büyüme Işletim sistemi SSO 'Su yapılandırma](#configure-synerise-ai-growth-operating-system-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan Synerıse 'de B. Simon 'ya karşılık gelen, **[SYNERıSE AI büyüme Işletim sistemi test kullanıcısı oluşturun](#create-synerise-ai-growth-operating-system-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Synerıse AI büyüme Işletim sistemi** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için, [Synerıse destek ekibine](mailto:support@synerise.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Synerıse ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Brerıse erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Synerıse AI büyüme Işletim sistemi**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-synerise-ai-growth-operating-system-sso"></a>Synerıse AI büyüme Işletim sistemi SSO 'SU yapılandırma

1. Synerıse 'de yönetici olarak oturum açın.

1. **> Access Control ayarlar**' a gidin.

    ![Synerıse ayarları](./media/synerise-ai-growth-ecosystem-tutorial/settings.png)

1. **Access Control** sayfasında, **Çoklu oturum açma** sekmesinde **göster** düğmesine tıklayın.

    ![Synerıse Access Control](./media/synerise-ai-growth-ecosystem-tutorial/single-sign-on.png)

1. Aşağıdaki adımları aşağıdaki sayfada gerçekleştirin.

    ![Synerıse yapılandırması](./media/synerise-ai-growth-ecosystem-tutorial/configuration.png)

    a. **Tanımlayıcı sağlayıcısı VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    b. **SSO uç noktası (https)** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Kimlik sağlayıcısı uygulama kimliği** metin kutusunda, **uygulama kimliği** değerini yapıştırın.

    d. **Hizmet sağlayıcı yeniden YÖNLENDIRME URI** değerini Kopyala, bu değeri Azure Portal temel SAML yapılandırması bölümündeki **yanıt URL 'si** metin kutusuna yapıştırın.

    e. **İstek bağlamasında** **http yeniden yönlendirmesi** ' ni seçin.

    f. **İstek imzasına** geçiş yapın.

    örneğin: İndirilen **sertifika (base64)** dosyasını **kimlik sağlayıcısı imza sertifikasına** yükleyin.

    i. **Uygula**' ya tıklayın.

### <a name="create-synerise-ai-growth-operating-system-test-user"></a>Synerıse AI büyüme Işletim sistemi test kullanıcısı oluşturma

Bu bölümde, Brerıse 'de Britta Simon adlı bir Kullanıcı oluşturulur. Synerıse, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten Synerıse 'de yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Synerıse oturum açma URL 'sine yönlendirecektir.  

* Doğrudan Synerıse oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Synerıse 'de otomatik olarak oturum açmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki Synerıse kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Synerıse 'de otomatik olarak oturum açmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Synerıse 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).