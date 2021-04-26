---
title: 'Öğretici: yeniden yazdırma sırasında çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory-article Galaxy | Microsoft Docs'
description: Azure Active Directory ve yeniden yazdırma masası makalesi Galaxy arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: e28281b783c66f8dbb0bc4842679eeec43755508
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515011"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-reprints-desk---article-galaxy"></a>Öğretici: yeniden yazdırma sırasında çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory-article Galaxy

Bu öğreticide, Azure Active Directory (Azure AD) ile yeniden yazdırma makalesi Galaxy 'yi nasıl tümleştirileceğini öğreneceksiniz. Yeniden yazdırma masası makalesi Galaxy 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Masa makalesi Galaxy ' i yeniden yazdırma erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla masa makalesi Galaxy ' i yeniden yazdırmaları için otomatik olarak oturum açmaya olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Desk-article Galaxy çoklu oturum açma (SSO) etkin aboneliğini yeniden yazdırır.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Yeniden yazdırma masası-makalesi Galaxy, **IDP** tarafından başlatılan SSO 'yu destekler

* Yeniden yazdırma masası-article Galaxy **, tam zamanında** Kullanıcı sağlamasını destekler

* [Yeniden yazdırma masası makalesi Galaxy ' u yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimlerini uygulayabilirsiniz. Oturum denetimleri koşullu erişimden genişletilir. Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-reprints-desk---article-galaxy-from-the-gallery"></a>Galeri 'den yeniden yazdırma masası makalesi Galaxy

Çalışma masası makalesi Galaxy 'ı Azure AD 'ye yeniden yazdırma tümleştirmesini yapılandırmak için, galerisindeki çalışma masası makalesi Galaxy ' i yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Desk-article Galaxy** yazın.
1. Sonuçlar panelinden **masa makalesi Galaxy** ' u seçerek yeniden yazdırır ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-reprints-desk---article-galaxy"></a>Yeniden yazdırma sırasında Azure AD çoklu oturum açma yapılandırma ve test etme-makalesi Galaxy

**B. Simon** adlı bir test kullanıcısı kullanarak, BIR Azure AD SSO 'Yu yeniden yazdırır ve bu şekilde çalışma masası makalesi Galaxy 'i yapılandırın SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu yeniden yazdırma masası Galaxy ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Yeniden yazdırma masasını yapılandırma Galaxy SSO](#configure-reprints-desk-article-galaxy-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    * **[Yeniden yazdırma oluşturma makalesi Galaxy test kullanıcısı](#create-reprints-desk-article-galaxy-test-user)** -B. Simon 'ın, kullanıcının Azure AD gösterimine bağlı olan Desk-article Galaxy 'Yi yeniden yazdırır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **yeniden yazdırma masası-makalesi Galaxy** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.


1. Yeniden yazdırma masası-article Galaxy uygulaması SAML onaylamalarını belirli bir biçimde bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Bu makalenin yanı sıra, çalışma masası makalesi Galaxy uygulaması, daha fazla özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği|
    | ------------ | --------- |
    | FirstName | Kullanıcı. |
    | Soyadı | User. soyadı |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Yeniden yazdırma ayarlama-makalesi Galaxy** bölümünde, uygun URL 'leri gereksinime göre kopyalayın.

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

Bu bölümde, B. Simon 'u yeniden yazdırma masası Galaxy 'e erişim izni vererek Azure çoklu oturum açma özelliğini etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **çalışma masası makalesi Galaxy**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-reprints-desk-article-galaxy-sso"></a>Yeniden yazdırma masasını yapılandırma Galaxy SSO

**Yeniden yazdırma masası makalesi Galaxy** tarafında çoklu oturum açma 'yı [yapılandırmak için,](mailto:customersupport@reprintsdesk.com)' den INDIRILEN **Federasyon meta verileri XML** 'sini ve uygun kopyalanmış URL 'leri Azure Portal. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-reprints-desk-article-galaxy-test-user"></a>Yeniden yazdırma oluşturma makalesi Galaxy test kullanıcısı

Bu bölümde, B. Simon adlı bir Kullanıcı, yeniden yazdırma masası Galaxy bölümünde oluşturulur. Yeniden yazdırma masası-article Galaxy, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı, yeniden yazdırma işlemi için zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde, pano makalesi Galaxy kutucuğunu yeniden Yazdır ' a tıkladığınızda, SSO 'yu ayarladığınız yeniden yazdırma masası makalesi Galaxy ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile masa makalesi Galaxy adlı yeniden yazdırma yapmayı deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle, çalışma masası makalesi Galaxy 'yi koruma](/cloud-app-security/proxy-intro-aad)