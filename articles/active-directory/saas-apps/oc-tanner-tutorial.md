---
title: 'Öğretici: O.C. ile tümleştirme Azure Active Directory Tanner-takdir Atehub | Microsoft Docs'
description: Azure Active Directory ve O.C. arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin Tanner-takdir Atehub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: jeedes
ms.openlocfilehash: 683cfc65e8154d4f409f5d4a33bf7ccf61c6f7c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518594"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>Öğretici: O.C. ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Tanner - AppreciateHub

Bu öğreticide, O.C. nasıl tümleştirileceğini öğreneceksiniz. Tanner-Azure Active Directory (Azure AD) ile bir takdir Atehub. O.C. tümleştirdiğinizde Tanner-Azure AD ile bir adım adım, şunları yapabilirsiniz:

* Azure AD 'de O.C. 'e erişimi olan denetim Tanner-takdir Atehub.
* Kullanıcılarınızın O.C. otomatik olarak oturum açabilmesi için Tanner-Onatehub, Azure AD hesaplarıyla birlikte.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* O.C. Tanner-Onatehub çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* O.C. Tanner-takdir Atehub, **IDP** tarafından başlatılan SSO 'yu destekler

* O.C. yapılandırdığınızda Tanner-onatehub, kuruluşunuzun hassas verilerinin bir kısmını gerçek zamanlı olarak koruyan oturum denetimleri uygulayabilir. Oturum denetimleri koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>O.C. ekleme Tanner-Galeriden Mediatehub

O.C. tümleştirmesini yapılandırmak için Tanner-Onatehub Azure AD 'ye O.C. eklemeniz gerekir Tanner-Galeri 'den yönetilen SaaS uygulamaları listenize yönelik bir takdir Atehub.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **O.C. Tanner-onatehub** yazın.
1. Sonuçlar panelinden **O.C. Tanner-Onatehub** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-oc-tanner---appreciatehub"></a>O.C. için Azure AD çoklu oturum açmayı yapılandırma ve test etme Tanner - AppreciateHub

Azure AD SSO 'yu O.C. ile yapılandırma ve test etme Tanner- **B. Simon** adlı bir test kullanıcısını kullanarak bir test kullanıcısı kullanın. SSO 'nun çalışması için, O.C. içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir Tanner-takdir Atehub.

Azure AD SSO 'yu O.C. ile yapılandırmak ve test etmek için Tanner-takdir Atehub, aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[O.C. Tanner-Onatehub SSO 'Yu yapılandırın](#configure-oc-tanner---appreciatehub-sso)** .
    * **[O.C. Tanner-Onatehub test kullanıcısı oluşturma](#create-oc-tanner---appreciatehub-test-user)** -O.C. 'de B. Simon 'ya karşılık gelen Tanner-kullanıcının Azure AD gösterimine bağlı olan bir Onatehub.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **O.C. Tanner-onatehub** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Set up O.C. Tanner-Onatehub** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

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

Bu bölümde, O.C. erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz. Tanner-takdir Atehub.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **O.C. Tanner-Onatehub**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-oc-tanner---appreciatehub-sso"></a>O.C. yapılandırma Tanner-takdir Atehub SSO

**O.C. Tanner-onatehub** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'leri Azure Portal ' den [O.C. Tanner-onatehub destek ekibine](mailto:sso@octanner.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>O.C. oluştur Tanner-takdir Atehub test kullanıcısı

Bu bölümün amacı, O.C. 'de Britta Simon adlı bir Kullanıcı oluşturmaktır Tanner-takdir Atehub.

**O.C. Tanner-Onatehub 'da Britta Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

[O.C. Tanner-Datehub destek takımınızdan](mailto:sso@octanner.com) , NameID özniteliği olan bir kullanıcı oluşturmak IÇIN Azure AD 'de Britta Simon Kullanıcı adı ile aynı değeri öğrenin.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

O.C. tıkladığınızda Tanner-takdir Atehub kutucuğu erişim panelinde otomatik olarak O.C. oturum açmış olmanız gerekir. Tanner-SSO 'yu ayarladığınız takdir Atehub. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile O.C. Tanner-Onatehub 'ı deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle O.C. Tanner-Onatehub 'ı koruma](/cloud-app-security/proxy-intro-aad)