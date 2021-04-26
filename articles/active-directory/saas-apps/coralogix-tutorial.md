---
title: 'Öğretici: Coralogix ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Coralogix arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: e676268abb9e1a4a597ef431ea6ba6593411711e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455230"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coralogix"></a>Öğretici: Coralogix ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Coralogix 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Coralogix 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Coralogix 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Coralogix için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Coralogix çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Coralogix, **SP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-coralogix-from-the-gallery"></a>Galeriden Coralogix ekleme

Coralogix 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Coralogix ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Coralogix** yazın.
1. Sonuçlar panelinden **Coralogix** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-coralogix"></a>Coralogix için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Coralogix Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Coralogix içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Coralogix ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Coralogix SSO 'Yu yapılandırma](#configure-coralogix-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[Coralogix test kullanıcısı oluşturun](#create-coralogix-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan coralogix 'de B. Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Coralogix** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** kutusunda, aşağıdaki düzene sahıp bir URL girin:`https://<SUBDOMAIN>.coralogix.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna bir URL girin, örneğin:
    
    `https://api.coralogix.com/saml/metadata.xml`

    veya

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Coralogix istemci desteği ekibine](mailto:info@coralogix.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümündeki desenlere de başvurabilirsiniz.

 1. Coralogix uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile tek Sign-On ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesini seçin.

    !["Düzenle" düğmesinin vurgulandığı "Kullanıcı öznitelikleri" iletişim kutusunu gösteren ekran görüntüsü.](common/edit-attribute.png)

1. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, **düzenleme** simgesini kullanarak talepleri düzenleyin. Ayrıca, önceki görüntüde gösterildiği gibi SAML belirteci özniteliğini yapılandırmak için **yeni talep Ekle** öğesini kullanarak talepler ekleyebilirsiniz. Ardından aşağıdaki adımları uygulayın:
    
    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **Düzenle simgesini** seçin.

    !["Düzenle" düğmesi vurgulanmış "Kullanıcı öznitelikleri & talepler" iletişim kutusunu gösteren ekran görüntüsü.](./media/coralogix-tutorial/tutorial_usermail.png)
    ![image](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. **Ad tanımlayıcı biçimi Seç** listesinde, **e-posta adresi**' ni seçin.

    c. **Kaynak özniteliği** listesinden **User. Mail** öğesini seçin.

    d. **Kaydet**’i seçin.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Coralogix ayarlama** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Coralogix 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Coralogix**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-coralogix-sso"></a>Coralogix SSO 'yu yapılandırma

**Coralogix** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal ' den [coralogix destek ekibine](mailto:info@coralogix.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-coralogix-test-user"></a>Coralogix test kullanıcısı oluştur

Bu bölümde, Coralogix 'te Britta Simon adlı bir Kullanıcı oluşturacaksınız. Coralogix platformunda kullanıcıları eklemek için [coralogix destek ekibi](mailto:info@coralogix.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Coralogix kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Coralogix ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Coralogix 'i deneyin](https://aad.portal.azure.com/)