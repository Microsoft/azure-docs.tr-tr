---
title: 'Öğretici: webMethods Integration Suite ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve webMethods tümleştirme paketi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: c3eaac19ffbf3fd93311073ff69e28532b1c15e6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520063"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Öğretici: webMethods Integration Suite ile Azure Active Directory tümleştirme

Bu öğreticide, webMethods Integration Suite 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Web yöntemleri tümleştirme paketini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de webMethods tümleştirme paketine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Web yöntemleri tümleştirme paketi ' ne otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* webMethods Integration Suite çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* webMethods Integration Suite, **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler.

* webMethods Integration Suite **, tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="add-webmethods-integration-suite-from-the-gallery"></a>Galeriden webMethods tümleştirme paketi ekleme

WebMethods Integration Suite 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize webMethods Integration Suite 'i eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **WebMethods Integration Suite** yazın.
1. Sonuçlar panelinden **WebMethods tümleştirme paketi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-webmethods-integration-suite"></a>WebMethods tümleştirme paketi için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu WebMethods tümleştirme paketiyle yapılandırın ve test edin. SSO 'nun çalışması için, webMethods Integration Suite içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu webMethods tümleştirme paketiyle yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[webMethods Integration SUITE SSO 'Yu yapılandırın](#configure-webmethods-integration-suite-sso)** .
    1. WebMethods **[Integration Suite test kullanıcısı oluşturma](#create-webmethods-integration-suite-test-user)** -Kullanıcı IÇIN Azure AD gösterimine bağlı olan WebMethods tümleştirme paketinde B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **WebMethods Integration Suite** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Web yöntemleri tümleştirme bulutu**'nı yapılandırmak Için, **temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Tanımlayıcı URL 'SI |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |
    |

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Yanıt URL'si |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse`|
    |

    c. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    d. **Oturum açma URL 'si** metin kutusunda, aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Oturum açma URL'si |
    |--------------------------------|
    |`https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest`|
    |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [WebMethods Integration Suite istemci destek ekibine](https://empower.softwareag.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **WebMethods API bulutu**'nı yapılandırmak Için, **temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Tanımlayıcı URL 'SI |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    |`<SUBDOMAIN>.webmethodscloud.eu`|
    | `<SUBDOMAIN>.webmethodscloud.de`|
    |

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Yanıt URL'si |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso`|
    |

    c. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    d. **Oturum açma URL 'si** metin kutusunda, aşağıdaki desenlerden birini kullanarak bir URL yazın:
    
    | Oturum açma URL'si |
    |--------------------------------|
    | `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [WebMethods Integration Suite istemci destek ekibine](https://empower.softwareag.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **WebMethods Integration Suite 'ı ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, webMethods Integration Suite 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **WebMethods Integration Suite**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-webmethods-integration-suite-sso"></a>WebMethods Integration Suite SSO 'yu yapılandırma

**WebMethods Integration Suite** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal ' den [WebMethods Integration Suite destek ekibine](https://empower.softwareag.com/)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-webmethods-integration-suite-test-user"></a>WebMethods tümleştirme paketi test kullanıcısı oluştur

Bu bölümde, webMethods Integration Suite 'te Britta Simon adlı bir Kullanıcı oluşturulur. webMethods Integration Suite, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı webMethods Integration Suite 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz webMethods Integration Suite oturum açma URL 'sine yeniden yönlendirilir.  

* WebMethods Integration Suite oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız WebMethods Integration Suite ' de otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki webMethods Integration Suite kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız webMethods tümleştirme paketinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

WebMethods Integration Suite 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
