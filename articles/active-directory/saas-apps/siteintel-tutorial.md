---
title: 'Öğretici: Siteıntel ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Siteıntel arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92510007"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Öğretici: Siteıntel ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Siteıntel 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Siteıntel 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Siteıntel 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Siteıntel 'te otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin, Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Siteıntel çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Siteıntel, SP tarafından başlatılan ve IDP tarafından başlatılan SSO 'yu destekler.
* Siteıntel 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>Galeriden Siteıntel 'i ekleme

Siteıntel 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, galerideki Siteıntel 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol bölmede **Azure Active Directory**' yi seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** kutusuna **siteıntel**' i girin.
1. Sonuçlar listesinde **Siteıntel**' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Siteıntel için Azure AD çoklu oturum açmayı yapılandırma ve test etme

*B. Simon* adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu siteıntel ile yapılandırın ve test edin. SSO 'nun çalışması için, Siteıntel 'te bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Siteıntel ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .  

    a. Kullanıcı B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .  

    b. Azure AD çoklu oturum açma özelliğini kullanmak için Kullanıcı B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .

1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Siteıntel SSO 'Yu yapılandırın](#configure-siteintel-sso)** .

    * Siteıntel 'te kullanıcının Azure AD gösterimine bağlı olan B. Simon kullanıcısına karşılık gelen bir **[siteıntel test kullanıcısı oluşturun](#create-a-siteintel-test-user)** .

1. Yapılandırmanın çalıştığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için aşağıdakileri yapın:

1. [Azure Portal](https://portal.azure.com/), **siteıntel** uygulama tümleştirmesi sayfasında, **Yönet** bölümüne gidin ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması**' nın yanındaki **Düzenle** ' yi (kalem simgesi) seçin.

   !["SAML ile çoklu oturum açma ayarlama" bölmesinin ekran görüntüsü](common/edit-urls.png)

1. Uygulamayı IDP tarafından başlatılan modda yapılandırmak için, **temel SAML yapılandırması** bölümünde aşağıdakileri yapın:

    a. **Tanımlayıcı** kutusunda, aşağıdaki BIÇIMDE bir URL yazın:`urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. **Yanıt URL** 'si kutusuna aşağıdaki BIÇIMDE bir URL yazın:`https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. **Geçiş durumu** kutusunda, aşağıdaki BIÇIMDE bir URL yazın:`https://<CLIENT>.siteintel.com`

1. Uygulamayı SP tarafından başlatılan modda yapılandırmak için **ek URL 'Ler ayarla**' yı seçin ve ardından aşağıdakileri yapın:

   * **Oturum açma URL 'si** kutusuna aşağıdaki BIÇIMDE bir URL yazın:`https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bunları gerçek tanımlayıcı, yanıt URL 'SI, oturum açma URL 'SI ve geçiş durumuyla güncelleştirin. Bu değerleri almak için, [Siteıntel istemci desteği ekibine](mailto:support@intalytics.com)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, URL 'Yi **uygulama Federasyon meta verileri URL 'si** kutusuna kopyalamak için **Kopyala** düğmesini seçin.

    !["Uygulama Federasyon meta veri URL 'Si" kopyalama düğmesinin ekran görüntüsü](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmede, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Bölmenin en üstünde bulunan **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şunları yapın:

   a. **Ad** kutusuna **B. Simon** girin.  

   b. **Kullanıcı adı** kutusuna şu biçimde Kullanıcı adını girin: `username@companydomain.extension` (örneğin, `B.Simon@contoso.com` ).

   c. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.

   d. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Siteıntel 'e erişim vererek Kullanıcı B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştirirsiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. **Uygulamalar** listesinde **siteıntel**' i seçin.
1. Uygulamaya Genel Bakış sayfasında, **Yönet** bölümünde **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısının ekran görüntüsü](common/users-groups-blade.png)

1. **Kullanıcı Ekle** ' yi seçin ve sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcı Ekle" düğmesinin ekran görüntüsü](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** bölmesinde **B. Simon**' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesini seçin.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** bölmesinde, Kullanıcı için listeden uygun rolü seçin ve ardından **Seç** düğmesini seçin.
1. **Atama Ekle** bölmesinde **ata** düğmesini seçin.

## <a name="configure-siteintel-sso"></a>Siteıntel SSO 'yu yapılandırma

Siteıntel tarafında çoklu oturum açmayı yapılandırmak için, **uygulama Federasyon meta verileri URL 'si** kutusundan kopyaladığınız URL 'Yi [siteıntel destek ekibine](mailto:support@intalytics.com)gönderin. Bu değer, SAML SSO bağlantısını her iki tarafta da düzgün şekilde kuracak şekilde ayarlar.

### <a name="create-a-siteintel-test-user"></a>Siteıntel test kullanıcısı oluşturma

Bu bölümde, Siteıntel 'te *Britta Simon* adlı bir Kullanıcı oluşturacaksınız. Siteıntel platformunda kullanıcıları eklemek için [siteıntel destek ekibi](mailto:support@intalytics.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde **siteıntel** kutucuğunu SEÇTIĞINIZDE, SSO 'Yu ayarladığınız siteıntel ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)
- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)
- [Azure AD ile Siteıntel 'i deneyin](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)
- [Gelişmiş görünürlük ve denetimlerle Siteıntel 'i koruma](/cloud-app-security/proxy-intro-aad)