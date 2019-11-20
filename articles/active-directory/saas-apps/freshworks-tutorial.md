---
title: 'Öğretici: en iyi çalışmalarla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve yeniden oluşturma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 697c3509-0e3a-4b05-9d5b-9ded4269eb60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b73b8769b5f7360ba4ad778600b1000b8b3f7bc7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376416"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Öğretici: en iyi çalışmalarla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile en iyi çalışmaları tümleştirmeyi öğreneceksiniz. Yalnızca Azure AD ile birlikte çalışarak tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, En Iyi şekilde çalışan erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte çalışmak üzere otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* En adet, çoklu oturum açma (SSO) özellikli aboneliği kullanılabilir.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* En güncel çalışma **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-freshworks-from-the-gallery"></a>Galeriden daha sonra çalışma ekleme

En Iyi çalışmalardan Azure AD ile tümleşmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize en güncel çalışmalar eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna en **uygun ' ı yazın.**
1. Sonuçlar panelinden yeniden çalıştır ' **ı seçin ve** ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Azure AD SSO 'yu, **B. Simon**adlı bir test kullanıcısı kullanarak en iyi şekilde yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ondaki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu, En Iyi şekilde çalışacak şekilde yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, tek başına **[çalışma SSO 'Yu yapılandırın](#configure-freshworks-sso)** .
    1. Kullanıcı için Azure AD gösterimine bağlı olan, en geç çalışmadaki B. Simon 'a sahip olmak için, yalnızca bir **[test kullanıcısı oluşturun](#create-freshworks-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), en **iyi çalışma** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<SUBDOMAIN>.freshworks.com/login`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için, en güncel [istemci desteği ekibine](mailto:support@freshworks.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1.  **İmzalama** seçeneklerini gereksiniminize göre değiştirmek için,  Düğmesini **Düzenle**' ye tıklayarak **SAML imzalama sertifikası** İletişim kutusunu açın.

     ![image](common/edit-certificate.png)

     ![En iyi iş yapılandırması](./media/freshworks-tutorial/response.png)

    a. **SAML yanıtını** **Imzalama seçeneği**olarak imzala seçeneğini belirleyin.

    b. **Kaydet** düğmesine tıklayın.

1. **En güncel çalışmaları ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak En Iyi şekilde çalışmasına erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **yeniden Seç ' i seçin**.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-freshworks-sso"></a>En Iyi çalışma SSO 'yu yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve yeni bir yönetici olarak, yeni bir şirket sitesinde oturum açın ve aşağıdaki adımları gerçekleştirin:

2. Menünün sol tarafında **güvenlik** simgesine tıklayın ve ardından **Çoklu oturum açma** seçeneğini Işaretleyin ve **KIMLIK doğrulama yöntemleri**altında **SAML SSO** 'yu seçin.

    ![En iyi iş yapılandırması](./media/freshworks-tutorial/configure01.png)

3. **Çoklu oturum açma** bölümünde aşağıdaki adımları gerçekleştirin:

    ![En iyi iş yapılandırması](./media/freshworks-tutorial/configure02.png)

    a. Örneğiniz için **hizmet sağlayıcısı (SP) VARLıK kimliğini** kopyalamak ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırmak için **Kopyala** ' ya tıklayın.

    b. **IDP metin kutusu tarafından belirtilen VARLıK kimliğinde** , Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. **SAML SSO URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. Base64 Ile kodlanmış sertifikayı not defteri 'nde açın, içeriğini kopyalayın ve **güvenlik sertifikası** metin kutusuna yapıştırın.

    e. **Kaydet** düğmesine tıklayın.

### <a name="create-freshworks-test-user"></a>Yalnızca bir test kullanıcısı oluştur

Bu bölümde, En Iyi şekilde çalışması için B. Simon adlı bir Kullanıcı oluşturacaksınız. En güncel kullanıcıları [istemci destek ekibi](mailto:support@freshworks.com) ile çalışarak, kullanıcıları en iyi çalışma platformunda ekleyin. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde otomatik olarak çalışma kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız otomatik olarak çalışır durumda oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile En Iyi şekilde çalışma yapmayı deneyin](https://aad.portal.azure.com/)

