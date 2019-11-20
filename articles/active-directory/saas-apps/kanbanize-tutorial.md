---
title: 'Öğretici: Kanbanize ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Kanbanize arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c5a3a096c5a44f681d23587837ae31fd1af33b2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373220"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Öğretici: Kanbanize ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Kanbanize Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Kanbanize Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Kanbanize erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Kanbanize etmek için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Kanbanize çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Kanbanize **SP ve ıDP** tarafından başlatılan SSO destekleniyor
* Kanbanize **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-kanbanize-from-the-gallery"></a>Galeriden Kanbanize ekleme

Kanbanize tümleştirmesini Azure AD ile yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Kanbanize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **kanbanize** yazın.
1. Sonuçlar panelinden **Kanbanize** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Kanbanize için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Kanbanize Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Kanbanize içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Kanbanize ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Kanbanize SSO 'Yu yapılandırın](#configure-kanbanize-sso)** .
    1. Kullanıcı Azure AD gösterimine bağlı olan Kanbanize 'da B. Simon 'a sahip olmak için **[kanbanize test kullanıcısı oluşturun](#create-kanbanize-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **kanbanize** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

     a. **Tanımlayıcı** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<subdomain>.kanbanize.com/`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<subdomain>.kanbanize.com/saml/acs`

    c. **Ek URL 'Ler ayarla**' ya tıklayın.

    d. **Geçiş durumu** metin kutusuna bir URL yazın: `/ctrl_login/saml_login`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Kanbanize istemci destek ekibine](mailto:support@ms.kanbanize.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Kanbanize uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde, NameIdentifier 'ın **User. UserPrincipalName**ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. Kanbanize uygulaması NameIdentifier 'ın **User. Mail**ile eşlenmesini bekliyor, bu nedenle, Düzenle simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. Bir **Kanbanize ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Kanbanize erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Kanbanize**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-kanbanize-sso"></a>Kanbanize SSO 'yu yapılandırma

1. Kanbanize içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek**üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, bunu ayarla ' ya tıklayarak **Kanbanize** 'ya tıklayın, sizi kanbanize uygulamasına yönlendirir. Buradan, Kanbanize oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Kanbanize el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve bir yönetici olarak Kanbanize şirket sitenizde oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Sayfanın sağ üst kısmına gidin, **Ayarlar** logosu ' na tıklayın.

    ![Kanbanize ayarları](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Menünün sol tarafındaki yönetim paneli sayfasında, **tümleştirmeler** ' e tıklayın ve **Çoklu oturum açmayı**etkinleştirin.

    ![Kanbanize tümleştirmeler](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Tümleştirmeler bölümünde, **tek oturum açma tümleştirmesi** sayfasını açmak için **Yapılandır** ' a tıklayın.

    ![Kanbanize yapılandırması](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. **Yapılandırma**altındaki **Çoklu oturum açma tümleştirmesi** sayfasında, aşağıdaki adımları uygulayın:

    ![Kanbanize tümleştirmeler](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. **IDP VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının**değerini yapıştırın.

    b. **IDP oturum açma uç noktası** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si**değerini yapıştırın.

    c. **IDP oturum kapatma uç noktası** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si**değerini yapıştırın.

    d. **E-posta metin kutusu Için öznitelik adı** ' nda, bu değeri girin `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. **Ilk ad metin kutusu Için öznitelik adı** kutusuna bu değeri girin `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. **Son ad metin kutusu Için öznitelik adı** kutusuna bu değeri girin `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Bu değerleri, ilgili özniteliğin ad alanı ve ad değerlerini Azure portal içindeki Kullanıcı öznitelikleri bölümünden birleştirerek alabilirsiniz.

    g. Not defteri 'nde, Azure portal indirdiğiniz temel 64 kodlu sertifikayı açın, içeriğini kopyalayın (başlangıç ve bitiş işaretçileri olmadan) ve ardından onu **IDP X. 509.440 sertifika** kutusuna yapıştırın.

    h. **SSO ve Kanbanize ile oturum açmayı etkinleştir ' i**işaretleyin.

    i. **Ayarları Kaydet**' e tıklayın.

### <a name="create-kanbanize-test-user"></a>Kanbanize test kullanıcısı oluştur

Bu bölümde, Kanbanize içinde B. Simon adlı bir Kullanıcı oluşturulur. Kanbanize, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Kanbanize 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur. El ile bir kullanıcı oluşturmanız gerekiyorsa, [istemci desteği ekibine](mailto:support@ms.kanbanize.com)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Kanbanize kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Kanbanize otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Kanbanize 'yi deneyin](https://aad.portal.azure.com/)

