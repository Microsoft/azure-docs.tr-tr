---
title: 'Öğretici: DocuSign ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve DocuSign arasında çoklu oturum açma (SSO) yapılandırma hakkında bilgi edinin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: d8c94bfaeb3438ef54e745505e51442d6d391929
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643007"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Öğretici: DocuSign ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, DocuSign 'ı Microsoft Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. DocuSign 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'yi kullanarak DocuSign 'e kimlerin erişebileceğini denetleyin.
* Kullanıcılarınız için Azure AD hesapları aracılığıyla otomatik oturum açmayı etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) etkin olan bir DocuSign aboneliği.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, aşağıdakileri doğrulamak için bir test ortamında Azure AD SSO 'yu yapılandırıp test edeceksiniz:

* DocuSign, Service Provider **SP** tarafından başlatılan SSO 'yu destekler.

* DocuSign **, tam zamanında** Kullanıcı sağlamayı destekler.

* DocuSign [Otomatik Kullanıcı sağlamayı](./docusign-provisioning-tutorial.md)destekler.

## <a name="adding-docusign-from-the-gallery"></a>Galeriden DocuSign ekleme

DocuSign 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize DocuSign eklemeniz gerekir:

1. Bir iş veya okul hesabı kullanarak veya kişisel bir Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol taraftaki Gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Docusign** yazın.
1. Sonuçlar panelinden **Docusign** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-docusign"></a>DocuSign için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu DocuSign ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve DocuSign içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu DocuSign ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanabilmesi için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [DocuSign SSO 'Yu yapılandırın](#configure-docusign-sso) .
    1. Kullanıcının Azure AD gösterimine bağlı olan DocuSign 'de B. Simon 'ın bir karşılığı oluşturmak için [bir Docusign test kullanıcısı oluşturun](#create-docusign-test-user) .
1. Yapılandırmanın çalıştığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. Azure portal, **Docusign** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde şu adımları izleyin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL girin:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL girin:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    c. **Yanıt URL** metin kutusuna aşağıdaki URL desenlerinin birini girin:
    
    | Yanıt URL'si |
    |-------------|
    | Üretiminden |
    | `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/<IDPID>` |
    | `https://<subdomain>.docusign.net/SAML/` |
    | QA örneği:|
    | `https://<SUBDOMAIN>.docusign.com/organizations/saml2` |

    > [!NOTE]
    > Bu parantez içine alınmış değerler yer tutuculardır. Bunları, gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'sindeki değerlerle değiştirin. Bu ayrıntılar, Bu öğreticinin ilerleyen kısımlarındaki "SAML 2,0 uç noktalarında görüntüle" bölümünde açıklanmaktadır.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)**. Sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **DocuSign 'ı ayarla** bölümünde, gereksinimlerinize göre uygun URL 'yi (veya URL 'leri) kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmesinde **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmında **Yeni Kullanıcı**' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına **B. Simon** girin.  
   1. **Kullanıcı adı** alanına, girin `<username>@<companydomain>.<extension>` . Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri unutmayın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, bu kullanıcının Azure çoklu oturum açmayı kullanabilmesi için DocuSign 'e B. Simon erişimi verirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Docusign**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon** öğesini seçin ve ardından ekranın altındaki **Seç** düğmesine basın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

## <a name="configure-docusign-sso"></a>DocuSign SSO 'yu yapılandırma

1. Bu yapılandırmayı, DocuSign ' ta otomatik hale getirmek için, **uzantıyı yükleyebilirsiniz**' i seçerek uygulamalarım güvenli oturum açma tarayıcı uzantısını yüklemelisiniz.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Uzantıyı tarayıcıya ekledikten sonra, **Kurulum DocuSign**' ı seçin. DocuSign uygulamasına yönlendirilirsiniz. Buradan, DocuSign 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı uygulamayı otomatik olarak yapılandırır ve 3 ile 5 arasındaki adımları otomatikleştirir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. DocuSign 'ı el ile ayarlamak istiyorsanız yeni bir Web tarayıcısı penceresi açın ve bir yönetici olarak DocuSign şirket sitenizde oturum açın.

4. Sayfanın sağ üst köşesinde profil logosu ' nı seçin ve ardından **yöneticiye git**' i seçin.
  
    ![Profil altında yöneticiye git][51]

5. Etki alanı çözümleri sayfasında **etki alanları**' nı seçin.

    ![Etki alanı çözümleri/etki alanları][50]

6. **Etki alanları** bölümünde **talep etki alanı**' nı seçin.

    ![Talep etki alanı seçeneği][52]

7. **Etki alanı talebi** iletişim kutusunda, **etki alanı adı** kutusuna şirket etki alanınızı yazın ve **talep**' ı seçin. Etki alanını ve durumunun etkin olduğundan emin olun.

    ![Etki alanı/etki alanı adı iletişim kutusu talep edin][53]

8. Etki alanı çözümleri sayfasında **kimlik sağlayıcıları**' nı seçin.
  
    ![Kimlik sağlayıcıları seçeneği][54]

9. **Kimlik sağlayıcıları** bölümünde **kimlik sağlayıcısı ekle**' yi seçin.

    ![Kimlik sağlayıcısı ekle seçeneği][55]

10. **Kimlik sağlayıcısı ayarları** sayfasında, şu adımları izleyin:

    ![Kimlik sağlayıcısı ayarları alanları][56]

    a. **Ad** kutusuna yapılandırmanız için benzersiz bir ad yazın. Boşluk kullanmayın.

    b. **Kimlik sağlayıcısı veren kutusunda**, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. **Kimlik sağlayıcısı oturum açma URL 'si** kutusunda, Azure Portal ' den kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. **Kimlik sağlayıcısı oturum kapatma URL 'si** kutusunda, Azure Portal ' den kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    e. **Imza AuthN isteğini** seçin.

    f. **Kimlik doğrulama Isteği gönderme** için **gönderi**' ı seçin.

    örneğin: **Oturumu kapatma Isteği gönderme** için **Al**' ı seçin.

    h. **Özel öznitelik eşleme** bölümünde **Yeni eşleme Ekle**' yi seçin.

       ![Özel öznitelik eşleme Kullanıcı arabirimi][62]

    i. Azure AD talebine eşlemek istediğiniz alanı seçin. Bu örnekte, **emadresi** talebi değeri ile eşleştirilir `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` . Bu, Azure AD 'den e-posta talebi için varsayılan talep adıdır. **Kaydet**' i seçin.

       ![Özel öznitelik eşleme alanları][57]

       > [!NOTE]
       > Kullanıcıyı Azure AD 'den DocuSign Kullanıcı eşlemesine eşlemek için uygun **Kullanıcı tanımlayıcısını** kullanın. Uygun alanı seçin ve kuruluşunuzun ayarlarına göre uygun değeri girin.

    j. **Kimlik sağlayıcısı sertifikaları** bölümünde **sertifika ekle**' yı seçin, Azure AD portalından indirdiğiniz sertifikayı karşıya yükleyin ve **Kaydet**' i seçin.

       ![Kimlik sağlayıcısı sertifikaları/sertifika ekle][58]

    k. **Kimlik sağlayıcıları** bölümünde **Eylemler**' i ve ardından **uç noktalar**' ı seçin.

       ![Kimlik sağlayıcıları/uç noktalar][59]

    l. DocuSign yönetim portalının **SAML 2,0 uç noktalarını görüntüle** bölümünde şu adımları izleyin:

       ![SAML 2,0 uç noktalarını görüntüle][60]
       
       1. **Hizmet sağlayıcı verenin URL 'sini** kopyalayın ve ardından Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** kutusuna yapıştırın.
       
       1. **Hizmet sağlayıcı onaylama tüketici hizmeti URL 'sini** kopyalayın ve ardından Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'si** kutusuna yapıştırın.
       
       1. **Hizmet sağlayıcısı oturum açma URL**'sini kopyalayın ve ardından Azure Portal **temel SAML yapılandırması** bölümündeki **URL 'yi oturum aç** kutusuna yapıştırın. **Hizmet sağlayıcı oturum açma URL** 'sinin sonunda ıdpid değerini alırsınız.

       1. **Kapat**’ı seçin.

### <a name="create-docusign-test-user"></a>DocuSign test kullanıcısı oluştur

Bu bölümde, DocuSign içinde B. Simon adlı bir Kullanıcı oluşturulur. DocuSign, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten DocuSign 'da yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!Note]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Docusign destek ekibine](https://support.docusign.com/)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

1. Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz DocuSign oturum açma URL 'sine yeniden yönlendirilir. 

2. Doğrudan DocuSign oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

3. Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki DocuSign kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız DocuSign 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki Adımlar

DocuSign 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial-docusign-18.png
[51]: ./media/docusign-tutorial/tutorial-docusign-21.png
[52]: ./media/docusign-tutorial/tutorial-docusign-22.png
[53]: ./media/docusign-tutorial/tutorial-docusign-23.png
[54]: ./media/docusign-tutorial/tutorial-docusign-19.png
[55]: ./media/docusign-tutorial/tutorial-docusign-20.png
[56]: ./media/docusign-tutorial/tutorial-docusign-24.png
[57]: ./media/docusign-tutorial/tutorial-docusign-25.png
[58]: ./media/docusign-tutorial/tutorial-docusign-26.png
[59]: ./media/docusign-tutorial/tutorial-docusign-27.png
[60]: ./media/docusign-tutorial/tutorial-docusign-28.png
[61]: ./media/docusign-tutorial/tutorial-docusign-29.png
[62]: ./media/docusign-tutorial/tutorial-docusign-30.png