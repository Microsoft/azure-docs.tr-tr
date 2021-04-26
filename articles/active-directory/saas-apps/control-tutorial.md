---
title: 'Öğretici: denetimle Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve süreklilik denetimi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.openlocfilehash: d6b752b2c562944748d21aa1a28a177163a95aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455298"
---
# <a name="tutorial-integrate-continuity-control-with-azure-active-directory"></a>Öğretici: süreksizlik denetimini Azure Active Directory ile tümleştirin

Bu öğreticide, devamlılığı denetimini (denetim) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Denetimi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de denetime erişimi olan yönetme.
* Kullanıcılarınızın Azure AD hesaplarıyla denetlemek için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Bir denetimin çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Denetim, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-control-from-the-gallery"></a>Galeriden denetim ekleme

Denetim tümleştirmesini Azure AD ile yapılandırmak için Galeriden yönetilen SaaS uygulamaları listenize denetim eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Denetim** yazın.
1. Sonuçlar panelinden **Denetim** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Azure AD SSO 'yu, **Britta Simon** adlı bir test kullanıcısı kullanarak denetimle yapılandırın ve test edin. SSO 'nun çalışması için, denetim içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu denetim ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Denetim SSO 'Su yapılandırma](#configure-control-sso)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan denetimde Britta Simon 'a sahip olmak için **[Denetim testi kullanıcısı oluşturun](#create-control-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), Uygulama tümleştirmesini **Denetle** sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alana ait değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.continuity.net/auth/saml`

    > [!Note]
    > Değer gerçek değil. Değeri doğru alt etki alanıyla güncelleştirin. SSO alt etki alanı, [Denetim kimlik doğrulama stratejilerinde](https://control.continuity.net/settings/account_profile#tab/security)yapılandırılabilir. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

1. **SAML Imzalama sertifikası** bölümünde, **parmak izini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

1. **Denetim ayarlama** bölümünde, oturum açma URL 'sini kopyalayın ve bilgisayarınıza kaydedin.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-control-sso"></a>Denetim SSO 'SU yapılandırma

**Denetim** tarafında çoklu oturum açmayı yapılandırmak Için, [Denetim kimlik doğrulama stratejilerinde](https://control.continuity.net/settings/account_profile#tab/security)çoklu oturum açma kimlik doğrulama ayarlarını güncelleştirmeniz gerekir. **SAML SSO URL** 'Sini, **oturum açma URL** 'Si ve **sertifika parmak iziyle** Azure Portal, **parmak izi değeriyle** güncelleştirin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, denetim erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Denetim**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-control-test-user"></a>Denetim testi kullanıcısı oluştur

Bu bölümde, denetimde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Denetim platformunda kullanıcıları eklemek için [Denetim desteği ekibi](mailto:help@continuity.net) ile çalışın. Denetimdeki **kimlik sağlayıcısı Kullanıcı kimliğini** doldurmak Için Britta Simon 'ıN Azure AD **Kullanıcı adını** kullanın. Kullanıcıların çoklu oturum açmayı kullanabilmesi için önce denetimde ve **kimlik sağlayıcısı Kullanıcı kimliği** kümesi oluşturulması gerekir.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Denetim kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız denetimde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)