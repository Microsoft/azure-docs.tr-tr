---
title: 'Öğretici: ScaleX Kurumsal ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ScaleX Kurumsal arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e54994d02dd1abbca1602952fbad058b3ad993d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72594270"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Öğretici: ScaleX Kurumsal ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, ScaleX kurumsal Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. ScaleX Enterprise 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de ScaleX Enterprise erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ScaleX kurumsal 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* ScaleX Kurumsal Çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* ScaleX Enterprise **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Galeriden ScaleX kurumsal ekleme

ScaleX Enterprise 'ın Azure AD ile tümleştirilmesini yapılandırmak için, galerideki ScaleX Enterprise 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **scaleX Enterprise** yazın.
1. Sonuçlar panelinden **scaleX Enterprise** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>ScaleX Enterprise için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak, scaleX kurumsal Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, ScaleX Enterprise 'da bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu ScaleX Kurumsal ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[ScaleX Kurumsal SSO 'Yu yapılandırma](#configure-scalex-enterprise-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * ScaleX kurumsal **[test kullanıcısı oluşturun](#create-scalex-enterprise-test-user)** -kullanıcının Azure AD gösterimine bağlı olan scaleX kuruluşunda B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **scaleX kurumsal** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://platform.rescale.com/saml2/<company id>/`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [scaleX Kurumsal istemci desteği ekibine](https://info.rescale.com/contact_sales) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. ScaleX kurumsal uygulamanız belirli bir biçimde SAML onayları bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde, **emaposta/** **Kullanıcı. Mail**ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. ScaleX kurumsal uygulaması **emadresi** 'nin **User. UserPrincipalName**ile eşlenmesini bekler, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **ScaleX Enterprise ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, ScaleX kurumsal erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **scaleX kurumsal**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-scalex-enterprise-sso"></a>ScaleX Kurumsal SSO 'yu yapılandırma

1. ScaleX Enterprise 'daki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek**üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya uzantı ekledikten sonra, **scaleX kuruluşu ayarla** ' ya tıklayarak sizi scaleX kurumsal uygulamasına yönlendirebilirsiniz. Buradan, ScaleX Enterprise 'ta oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. ScaleX Enterprise 'ı el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve ScaleX Kurumsal Şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Sağ üstteki menüye tıklayın ve **contoso yönetimi**' ni seçin.

    > [!NOTE]
    > Contoso yalnızca bir örnektir. Bunun gerçek şirket adı olması gerekir.

    ![Çoklu oturum açmayı yapılandırma](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Üstteki menüden **tümleştirmeler** ' i seçin ve **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Formu aşağıdaki gibi doldurun:

    ![Çoklu oturum açmayı yapılandırma](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. **SSO ile kimlik doğrulayabilecek herhangi bir kullanıcı oluştur** ' u seçin

    b. **Hizmet sağlayıcısı SAML**: ***urn: oassıs: names: TC: SAML: 2.0: NameID-Format: persistent*** değerini yapıştırın

    c. **ACS yanıtında kimlik sağlayıcısı e-posta alanının adı**: değeri yapıştırın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Kimlik sağlayıcısı EntityDescriptor VARLıK kimliği:** Azure portal kopyalanmış **Azure AD tanımlayıcı** değerini yapıştırın.

    e. **Kimlik sağlayıcısı SingleSignOnService URL 'si:** Azure portal **oturum açma URL** 'sini yapıştırın.

    f. **Kimlik sağlayıcısı genel x509 sertifikası:** Not defteri 'nde Azure 'dan indirilen x509 sertifikasını açın ve içeriği bu kutuya yapıştırın. Sertifika içeriklerinin ortasında satır sonu olmadığından emin olun.

    g. Şu onay kutularını işaretleyin: **etkin, ad kimliğini şifreleyin ve AuthnRequests 'ı imzalayın.**

    h. Ayarları kaydetmek için **SSO ayarlarını Güncelleştir** ' e tıklayın.

### <a name="create-scalex-enterprise-test-user"></a>ScaleX kurumsal test kullanıcısı oluştur

Azure AD kullanıcılarının ScaleX kuruluşunda oturum açmasını sağlamak için, bu kullanıcıların, ScaleX kurumsal sürümünde sağlanması gerekir. ScaleX kurumsal söz konusu olduğunda, sağlama otomatik bir görevdir ve el ile adım gerekmez. SSO kimlik bilgileriyle başarıyla kimlik doğrulayabilecek kullanıcılar ScaleX tarafında otomatik olarak sağlanır.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde ScaleX kurumsal kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız ScaleX kuruluşunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [ScaleX Enterprise 'ı Azure AD ile deneyin](https://aad.portal.azure.com/)