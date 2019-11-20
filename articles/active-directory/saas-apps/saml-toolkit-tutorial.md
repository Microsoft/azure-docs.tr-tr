---
title: 'Öğretici: Azure AD SAML araç seti ile çoklu oturum açma (SSO) Tümleştirmesi | Azure Active Directory Microsoft Docs'
description: Azure Active Directory ile Azure AD SAML araç seti arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e5790a036a93c947a79bc2848e7be6febf30b6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122039"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Öğretici: Azure AD SAML araç seti ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure AD SAML araç takımını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Azure AD SAML araç takımını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Azure AD SAML araç seti 'ne erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Azure AD SAML araç seti ' ne otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Azure AD SAML araç seti çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Azure AD SAML araç seti **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Galeriden Azure AD SAML araç seti ekleme

Azure AD SAML araç seti 'nin tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeriden Azure AD SAML araç seti 'ni yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Azure AD SAML araç seti** yazın.
1. Sonuçlar panelinden **Azure AD SAML araç seti** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Azure AD SAML araç seti için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'YU Azure AD SAML araç seti ile yapılandırın ve test edin. SSO 'nun çalışması için Azure AD SAML araç seti ' nde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Azure AD SAML araç seti ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Azure AD SAML araç SETI SSO 'Yu yapılandırın](#configure-azure-ad-saml-toolkit-sso)** .
    1. Azure AD SAML **[araç seti test kullanıcısı oluşturun](#create-azure-ad-saml-toolkit-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Azure AD SAML araç setinde B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **Azure AD SAML araç seti** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://samltoolkit.azurewebsites.net/`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir URL yazın:`https://samltoolkit.azurewebsites.net`

    c. **Yanıt URL** 'si metin kutusuna bir URL yazın:`https://samltoolkit.azurewebsites.net/SAML/Consume`

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **sertifika (ham)** bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

1. **Azure AD SAML araç seti ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Azure AD SAML araç seti 'ne erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Azure AD SAML araç seti**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Azure AD SAML araç seti SSO 'yu yapılandırma

1. Yeni bir Web tarayıcısı penceresi açın, Azure AD SAML araç seti Web sitesine kaydolmadıysanız önce **kayda**tıklayarak kaydolun. Zaten kaydolduysanız, kayıtlı oturum açma kimlik bilgilerini kullanarak Azure AD SAML araç seti şirket sitenizde oturum açın.

    ![Azure AD SAML araç seti kaydı](./media/saml-toolkit-tutorial/register.png)

1. **SAML yapılandırması**' na tıklayın.

    ![Azure AD SAML araç seti SAML yapılandırması](./media/saml-toolkit-tutorial/saml-configure.png)

1. **Oluştur**’a tıklayın.

    ![Azure AD SAML araç seti SSO oluştur](./media/saml-toolkit-tutorial/createsso.png)

1. **SAML SSO yapılandırma** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Azure AD SAML araç seti SSO oluştur](./media/saml-toolkit-tutorial/fill-details.png)

    1. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. **Azure AD tanımlayıcı** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    1. **Logout URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    1. **Dosya Seç** ' e tıklayın ve Azure Portal indirdiğiniz **sertifika (ham)** dosyasını karşıya yükleyin.

    1. **Oluştur**’a tıklayın.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Azure AD SAML araç seti test kullanıcısı oluşturma

Bu bölümde, Azure AD SAML araç seti 'nde B. Simon adlı bir Kullanıcı oluşturulur. Azure AD SAML araç seti, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Azure AD SAML araç setinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde Azure AD SAML araç seti kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Azure AD SAML araç setinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Azure AD SAML araç setini deneyin](https://aad.portal.azure.com/)