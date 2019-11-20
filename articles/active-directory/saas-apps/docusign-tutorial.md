---
title: 'Öğretici: DocuSign ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve DocuSign arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f02492dd7c9563c8c0002a63f0b105bd0be8b14
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345577"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Öğretici: DocuSign ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, DocuSign 'ı Microsoft Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. DocuSign 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'yi kullanarak DocuSign 'e kimlerin erişebileceğini denetleyin.
* Kullanıcılarınız için Azure AD hesapları aracılığıyla otomatik oturum açmayı etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure AD 'de uygulamalarda çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) etkin olan bir DocuSign aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, aşağıdakileri doğrulamak için bir test ortamında Azure AD SSO 'yu yapılandırıp test edeceksiniz:

* DocuSign, Service Provider (SP) tarafından başlatılan SSO 'yu destekler.

* DocuSign *, tam zamanında* Kullanıcı sağlamayı destekler.

* DocuSign [Otomatik Kullanıcı sağlamayı](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)destekler.

## <a name="adding-docusign-from-the-gallery"></a>Galeriden DocuSign ekleme

DocuSign 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize DocuSign eklemeniz gerekir:

1. Bir iş veya okul hesabı kullanarak veya kişisel bir Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol taraftaki Gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Docusign** yazın.
1. Sonuçlar panelinden **Docusign** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>DocuSign için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu DocuSign ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve DocuSign içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu DocuSign ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanabilmesi için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [DocuSign SSO 'Yu yapılandırın](#configure-docusign-sso) .
1. Kullanıcının Azure AD gösterimine bağlı olan DocuSign 'de B. Simon 'ın bir karşılığı oluşturmak için [bir Docusign test kullanıcısı oluşturun](#create-docusign-test-user) .
1. Yapılandırmanın çalıştığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), **Docusign** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde şu adımları izleyin:

    a. **Oturum açma URL 'si** kutusunda, aşağıdaki kalıbı kullanarak bir URL girin: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. **Tanımlayıcı (VARLıK kimliği)** kutusunda, aşağıdaki kalıbı kullanarak bir URL girin: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Bu parantez içine alınmış değerler yer tutuculardır. Bunları, gerçek oturum açma URL 'SI ve Tanımlayıcıdaki değerlerle değiştirin. Bu ayrıntılar, Bu öğreticinin ilerleyen kısımlarındaki "SAML 2,0 uç noktalarında görüntüle" bölümünde açıklanmaktadır.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** . Sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **DocuSign 'ı ayarla** bölümünde, gereksinimlerinize göre uygun URL 'yi (veya URL 'leri) kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmesinde **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmında **Yeni Kullanıcı**' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına **B. Simon**girin.  
   1. **Kullanıcı adı** alanına `<username>@<companydomain>.<extension>` girin. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri unutmayın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, bu kullanıcının Azure çoklu oturum açmayı kullanabilmesi için DocuSign 'e B. Simon erişimi verirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Docusign**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon** öğesini seçin ve ardından ekranın altındaki **Seç** düğmesine basın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesine basın.
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

    d. **Kimlik sağlayıcısı oturum kapatma URL 'si** kutusunda, Azure Portal ' den kopyaladığınız **oturum kapatma URL 'si**değerini yapıştırın.

    e. **Imza AuthN isteğini**seçin.

    f. **Kimlik doğrulama Isteği gönderme**için **gönderi**' ı seçin.

    g. **Oturumu kapatma Isteği gönderme**için **Al**' ı seçin.

    h. **Özel öznitelik eşleme** bölümünde **Yeni eşleme Ekle**' yi seçin.

       ![Özel öznitelik eşleme Kullanıcı arabirimi][62]

    i. Azure AD talebine eşlemek istediğiniz alanı seçin. Bu örnekte, **emadresi** talebi `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` değeri ile eşleştirilir. Bu, Azure AD 'den e-posta talebi için varsayılan talep adıdır. **Kaydet**' i seçin.

       ![Özel öznitelik eşleme alanları][57]

       > [!NOTE]
       > Kullanıcıyı Azure AD 'den DocuSign Kullanıcı eşlemesine eşlemek için uygun **Kullanıcı tanımlayıcısını** kullanın. Uygun alanı seçin ve kuruluşunuzun ayarlarına göre uygun değeri girin.

    j. **Kimlik sağlayıcısı sertifikaları** bölümünde **sertifika ekle**' yı seçin, Azure AD portalından indirdiğiniz sertifikayı karşıya yükleyin ve **Kaydet**' i seçin.

       ![Kimlik sağlayıcısı sertifikaları/sertifika ekle][58]

    k. **Kimlik sağlayıcıları** bölümünde **Eylemler**' i ve ardından **uç noktalar**' ı seçin.

       ![Kimlik sağlayıcıları/uç noktalar][59]

    girişindeki. DocuSign yönetim portalının **SAML 2,0 uç noktalarını görüntüle** bölümünde şu adımları izleyin:
       1. **Hizmet sağlayıcı verenin URL 'sini**kopyalayın ve ardından Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** kutusuna yapıştırın.

       1. **Hizmet sağlayıcısı oturum açma URL**'sini kopyalayın ve ardından Azure Portal **temel SAML yapılandırması** bölümündeki **URL 'yi oturum aç** kutusuna yapıştırın.

       1. **Kapat**' ı seçin.

       ![SAML 2,0 uç noktalarını görüntüle][60]

### <a name="create-docusign-test-user"></a>DocuSign test kullanıcısı oluştur

Bu bölümde, DocuSign içinde B. Simon adlı bir Kullanıcı oluşturulur. DocuSign, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten DocuSign 'da yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa, [Docusign destek ekibine](https://support.docusign.com/)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde DocuSign kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız DocuSign örneğinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure AD ile tümleştirme hakkında öğreticiler](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure AD 'de uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure AD 'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile DocuSign 'ı deneyin](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
