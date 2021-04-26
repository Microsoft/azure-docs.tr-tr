---
title: 'Öğretici: çapraz çevrimiçi portal söz konusu olduğunda çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory arasında çoklu oturum açmayı nasıl yapılandıracağınızı ve çapraz çevrimiçi portal söz konusu olduğunda nasıl yapılandırılacağını öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 124b53297c04d3c9958a233cf40f56ecda57bbc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460215"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-in-case-of-crisis---online-portal"></a>Öğretici: çapraz çevrimiçi portalda çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile birlikte çevrimiçi portalda nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile çapraz çevrimiçi portal söz konusu olduğunda tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, çapraz çevrimiçi portal söz konusu olduğunda erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla çapraz çevrimiçi portalda şirket içi olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çapraz çevrimiçi portal çoklu oturum açma (SSO) özellikli abonelik olması durumunda.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Çapraz çevrimiçi portal, **IDP** tarafından başlatılan SSO 'yu destekler
* Çapraz çevrimiçi portalda bir kez yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimleri uygulayabilirsiniz. Oturum denetimleri koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-in-case-of-crisis---online-portal-from-the-gallery"></a>Galerinin çapraz çevrimiçi portalı durumunda ekleme

Çapraz çevrimiçi portalda Azure AD 'ye olan tümleştirmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize çapraz çevrimiçi portal olması durumunda eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Çapraz çevrimiçi portalın olması durumunda** yazın.
1. Sonuçlar panelinden **Çapraz çevrimiçi portal** ve sonra uygulamayı ekleme durumunda ' ı seçin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-in-case-of-crisis---online-portal"></a>Azure AD 'ye yönelik çoklu oturum açmayı yapılandırma ve test etme-çevrimiçi portal

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu, çapraz çevrimiçi portalda birlikte yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında çapraz çevrimiçi portal söz konusu olduğunda bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu, çapraz çevrimiçi portalda birlikte yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[, çapraz çevrimiçi portal SSO 'Su olması durumunda yapılandırma](#configure-in-case-of-crisis-online-portal-sso)** .
    * Şirket içi **[çevrimiçi portal test kullanıcısı](#create-in-case-of-crisis-online-portal-test-user)** , kullanıcının Azure AD gösterimine bağlı olan çapraz çevrimiçi portal söz konusu olduğunda B. Simon 'ın bir karşılığı olacak şekilde oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Çapraz çevrimiçi portal** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.


1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Çapraz çevrimiçi portalda ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, B. Simon 'u, çapraz çevrimiçi portalda erişim izni vererek Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Çapraz çevrimiçi portal söz konusu olduğunda** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-in-case-of-crisis-online-portal-sso"></a>Çapraz çevrimiçi portal SSO 'SU durumunda yapılandırma

**Çapraz çevrimiçi portal** tarafında çoklu oturum açma 'yı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal 'den [Çapraz çevrimiçi portal destek ekibi olması durumunda](mailto:support@rockdovesolutions.com), uygun kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-in-case-of-crisis-online-portal-test-user"></a>Çapraz çevrimiçi portal test kullanıcısı durumunda oluştur

Bu bölümde, çapraz çevrimiçi portal söz konusu olduğunda B. Simon adlı bir Kullanıcı oluşturacaksınız. Çapraz çevrimiçi portal platformu olması durumunda kullanıcıları eklemek için, [Çapraz çevrimiçi portal destek ekibi](mailto:support@rockdovesolutions.com) ile birlikte çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde çapraz çevrimiçi portal kutucuğunda ' e tıkladığınızda, SSO 'yu ayarladığınız çapraz çevrimiçi portal 'Da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile çapraz Çevrimiçi Portalda bir sorun olması durumunda deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)