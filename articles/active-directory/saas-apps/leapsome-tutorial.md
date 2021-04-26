---
title: 'Öğretici: Leapsome ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve Leapsome arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: ddc8cce7b56e0d9d4b3dc33dc1ad2d8c16582841
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458719"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Öğretici: Leapsome ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Leapsome 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Leapsome 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Leapsome erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Leapsome çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Leapsome **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-leapsome-from-the-gallery"></a>Galeriden Leapsome ekleme

Leapsome 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Leapsome eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **leapsome** yazın.
1. Sonuçlar panelinden **Leapsome** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Leapsome için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Leapsome Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Leapsome ile ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Leapsome ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Leapsome SSO 'Yu yapılandırma](#configure-leapsome-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * Kullanıcının Azure AD gösterimine bağlı olan Leapsome 'da B. Simon 'ın bir karşılığı olacak şekilde, **[leapsome test kullanıcısı oluşturun](#create-leapsome-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **leapsome** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna bir URL yazın:`https://www.leapsome.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > Önceki yanıt URL 'SI ve oturum açma URL 'SI değeri gerçek bir değer değil. Bunları, Öğreticinin ilerleyen kısımlarında açıklanan gerçek değerlerle güncelleşceksiniz.

1. Leapsome uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdakine ek olarak, Leapsome uygulaması aşağıda gösterilen SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği | Ad Alanı |
    | ---------------| --------------- | --------- |  
    | FirstName | Kullanıcı. | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Soyadı | User. soyadı | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | başlık | User. JobTitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | resim | Çalışan resminin URL 'SI | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > Resim özniteliğinin değeri gerçek değil. Bu değeri gerçek resim URL 'siyle güncelleştirin. Bu değere ulaşmak için, [Leapsome istemci desteği ekibine](mailto:support@leapsome.com)başvurun.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Set up Leapsome** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Leapsome 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Leapsome**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-leapsome-sso"></a>Leapsome SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir güvenlik yöneticisi olarak Leapsome 'da oturum açın.

1. Sağ üst köşedeki ayarlar logosu ' na ve ardından **yönetici ayarları**' na tıklayın.

    ![Leapsome kümesi](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Sol menü çubuğunda **Çoklu oturum açma (SSO)** seçeneğine tıklayın ve **SAML tabanlı çoklu oturum açma (SSO)** sayfasında aşağıdaki adımları uygulayın:

    ![Leapsome SAML](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. **SAML tabanlı çoklu oturum açmayı etkinleştir '** i seçin.

    b. Oturum açma **URL 'sini kopyalayın (oturum açmaya başlamak için kullanıcılarınıza buraya gelin)** değerini kopyalayın ve Azure Portal ÜZERINDEKI **temel SAML yapılandırması** bölümünde **oturum açma URL 'si** metin kutusuna yapıştırın.

    c. **Yanıt URL 'sini kopyalayın (kimlik sağlayıcınızdan yanıtı alır)** ve Azure Portal ÜZERINDEKI **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** metin kutusuna yapıştırın.

    d. **SSO oturum açma URL 'si (kimlik sağlayıcısı tarafından sağlanmış)** metin kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. İndirmiş olduğunuz sertifikayı açıklama olmadan Azure portal kopyalayın `--BEGIN CERTIFICATE and END CERTIFICATE--` ve **sertifikayı sertifikaya (kimlik sağlayıcısı tarafından sunulur)** metin kutusuna yapıştırın.

    f. **SSO ayarlarını Güncelleştir**' e tıklayın.

### <a name="create-leapsome-test-user"></a>Leapsome test kullanıcısı oluştur

Bu bölümde, Leapsome 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Leapsome [istemci destek](mailto:support@leapsome.com) ekibiyle birlikte çalışarak, leapan platformunun izin verilenler listesine eklenmesi gereken kullanıcıları veya etki alanını ekleyin. Etki alanı takım tarafından eklenirse, kullanıcılar otomatik olarak Leapa platformu sağlanacak. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Leapsome kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Leapsome 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Leapsome 'ı deneyin](https://aad.portal.azure.com/)