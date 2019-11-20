---
title: 'Öğretici: posta şanslarla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory! | Microsoft Docs'
description: Azure Active Directory ve posta şanslar arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 783ea511-0b02-4455-ac8c-7aefde4ae4df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: daf86270224d2a76eb124e879594ac2588b46776
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969822"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mail-luck"></a>Öğretici: posta şanslarla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory!

Bu öğreticide, posta öğreticisini tümleştirmeyi öğreneceksiniz! Azure Active Directory (Azure AD) ile. Posta şansonları tümleştirdiğinizde! Azure AD ile şunları yapabilirsiniz:

* Azure AD 'de posta Şanslarınıza erişimi olan denetim!.
* Kullanıcılarınızın e-posta şanslarınızın otomatik olarak oturum açabilmesi için etkinleştirin! Azure AD hesaplarıyla.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Posta şanslar! Çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Posta şanslar! **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-mail-luck-from-the-gallery"></a>Posta şanslar ekleniyor! Galeriden

Posta şanslar tümleştirmesini yapılandırmak için! Azure AD 'ye, posta şanslar eklemeniz gerekir! Galeriden, yönetilen SaaS uygulamaları listenize gidin.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde **posta şanslar yazın!** Arama kutusuna.
1. **Posta şanslar** ' ı seçin! sonuçlar panelinden ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mail-luck"></a>Posta için Azure AD çoklu oturum açmayı yapılandırın ve test edin!

Azure AD SSO 'yu posta şanslarla yapılandırın ve test edin! **B. Simon**adlı bir test kullanıcısı kullanılıyor. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında posta Şansileri arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu posta Şanlarıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Posta şanslar yapılandırın! SSO](#configure-mail-luck-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * **[Posta şanslar oluşturun!](#create-mail-luck-test-user)** e-posta şansından B. Simon 'a sahip olmak için kullanıcıyı test edin! Bu, kullanıcının Azure AD gösterimine bağlanır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), posta Ile Ilgili **şanslar!** Uygulama Tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://manage<UNITID>.ml-sgw.jp/<TENANT_NAME>/saml/sign_in`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://manage<UNITID>.ml-sgw.jp/<TENANT_NAME>/saml/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. İletişim e [-posta şanslar! ](https://customer.nttpc.co.jp/cgi-bin/form/inquiry_index.cgi)Bu değerleri almak için istemci destek ekibi. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

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

Bu bölümde, posta Şanslarınıza erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **posta şanslar!** ' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-mail-luck-sso"></a>Posta şanslar yapılandırın! SSO

Posta konusunda çoklu oturum açmayı yapılandırmak için **!** Bu yandan, **uygulama Federasyon meta veri URL 'sini** e [-posta şanslar! destek ekibine](https://customer.nttpc.co.jp/cgi-bin/form/inquiry_index.cgi)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-mail-luck-test-user"></a>Posta şanslar oluşturun! test kullanıcısı

Bu bölümde, posta şansından B. Simon adlı bir Kullanıcı oluşturacaksınız.  [Posta şanslarla çalışma!](https://customer.nttpc.co.jp/cgi-bin/form/inquiry_index.cgi) e-posta şanssınızda kullanıcıları eklemek için destek ekibi! platformunun. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

E-posta Şanslarınıza tıkladığınızda! kutucukta, erişim panelinde otomatik olarak oturum açmış olmanız gerekir! için SSO 'yu ayarlama. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Posta şanslar deneyin! Azure AD ile](https://aad.portal.azure.com/)