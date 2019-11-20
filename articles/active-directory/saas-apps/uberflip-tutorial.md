---
title: 'Öğretici: Uberflip ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Uberflip arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 6710344e73e3c0ea6f9b3491209689871cc14a4f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160947"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Öğretici: Uberflip ile tümleştirme Azure Active Directory

Bu öğreticide, Uberflip 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

Uberflip 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Uberflip 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Uberflip (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Uberflip ile Azure AD tümleştirmesini yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Çoklu oturum açma özelliği etkinken bir Uberflip aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

Uberflip aşağıdaki özellikleri destekler:

* SP tarafından başlatılan ve ıDP tarafından başlatılan çoklu oturum açma (SSO).
* Tam zamanında Kullanıcı hazırlama.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Azure Marketi 'nden Uberflip ekleme

Uberflip 'ın Azure AD 'ye tümleştirmesini yapılandırmak için, Azure Marketi 'nden yönetilen SaaS uygulamaları listenize Uberflip eklemeniz gerekir:

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. Sol bölmede **Azure Active Directory**’yi seçin.

   ![Azure Active Directory seçeneği](common/select-azuread.png)

1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

   ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **+ Yeni uygulama** ' yı seçin.

   ![Yeni uygulama seçeneği](common/add-new-app.png)

1. Arama kutusuna **Uberflip**yazın. Arama sonuçlarında **Uberflip**' ı seçin ve ardından uygulamayı eklemek için **Ekle** ' yi seçin.

   ![Sonuçlar listesinde uberflip](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **B Simon**adlı bir test kullanıcısına göre Uberflip Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz. Çoklu oturum açma için, Uberflip içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Uberflip ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Uberflip çoklu oturum açmayı yapılandırın](#configure-uberflip-single-sign-on)** .
1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. B. Simon adlı Azure AD kullanıcısına bağlı olan Uberflip 'da B. Simon adlı bir Kullanıcı **[oluşturmak için bir uberflip test kullanıcısı oluşturun](#create-an-uberflip-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Uberflip ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **uberflip** Uygulama tümleştirmesini sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma seçeneğini yapılandırma](common/select-sso.png)

1. Çoklu oturum **açma yöntemi seçin** bölmesinde, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

1. **SAML Ile çoklu oturum açmayı ayarla** bölmesinde, **temel SAML yapılandırması** bölmesini açmak için **Düzenle** ' yi (kurşun kalem simgesi) seçin.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölmesinde, YAPıLANDıRMAK istediğiniz SSO moduna bağlı olarak aşağıdaki adımlardan birini yapın:

   * Uygulamayı ıDP tarafından başlatılan SSO modunda yapılandırmak için, **yanıt URL 'si (onaylama tüketici hizmeti URL 'si)** kutusuna aşağıdaki kalıbı kullanarak bir URL girin:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/both-replyurl.png)

     > [!NOTE]
     > Bu değer gerçek değildir. Bu değeri gerçek yanıt URL 'siyle güncelleştirin. Gerçek değeri almak için [Uberflip destek ekibine](mailto:support@uberflip.com)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölmesinde gösterilen desenlere de başvurabilirsiniz.

   * Uygulamayı SP tarafından başlatılan SSO modunda yapılandırmak için **ek URL 'Ler ayarla**' yı seçin ve **oturum açma URL 'si** kutusuna şu URL 'yi girin:

     `https://app.uberflip.com/users/login`

     ![Uberflip etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/both-signonurl.png)

1. **SAML Ile çoklu oturum açmayı ayarla** bölmesinde, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Federasyon meta verileri XML indirme seçeneği](common/metadataxml.png)

1. **Uberflip ayarla** bölmesinde, gereken URL veya URL 'leri kopyalayın:

   * **Oturum açma URL 'SI**
   * **Azure AD tanımlayıcısı**
   * **Oturum kapatma URL 'SI**

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Uberflip çoklu oturum açmayı yapılandırma

Uberflip tarafında çoklu oturum açmayı yapılandırmak için, indirilen Federasyon meta veri XML 'sini ve ilgili kopyalanmış URL 'Leri Azure portal [Uberflip destek ekibine](mailto:support@uberflip.com)göndermeniz gerekir. Uberflip ekibi, SAML SSO bağlantısının her iki tarafta da düzgün şekilde ayarlanmış olmasını sağlayacaktır.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmedeki  > **kullanıcılar**  > **tüm kullanıcılar**' ı **Azure Active Directory** seçin.

    ![Kullanıcılar ve "tüm kullanıcılar" seçenekleri](common/users.png)

1. Ekranın üst kısmında **+ Yeni Kullanıcı**' yı seçin.

    ![Yeni Kullanıcı seçeneği](common/new-user.png)

1. **Kullanıcı** bölmesinde, aşağıdaki adımları uygulayın:

    ![Kullanıcı bölmesi](common/user-properties.png)

    1. **Ad** kutusuna **bsıon**yazın.
  
    1. **Kullanıcı adı** kutusuna **bsıon\@\<yourcompanydomain >.\<uzantısı >** girin. Örneğin, **Bsıon\@contoso.com**.

    1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.

    1. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Uberflip erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirin.

1. Azure portal, **tüm uygulamalar** > **uberflip** > **Kurumsal uygulamalar** ' ı seçin.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Uygulamalar listesinde **Uberflip**' ı seçin.

    ![Uygulamalar listesinde uberflip](common/all-applications.png)

1. Sol bölmedeki **Yönet**altında **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" seçeneği](common/users-groups-blade.png)

1. **+ Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** bölmesinde, **Kullanıcılar** listesinden **B Simon** ' ı seçin ve ardından bölmenin en altında bulunan **Seç** ' i seçin.

1. SAML onaylama 'da bir rol değeri bekliyorsanız, **Rol Seç** bölmesinde, Kullanıcı için listeden uygun rolü seçin. Bölmenin en altında bulunan **Seç**' i seçin.

1. **Atama Ekle** bölmesinde **ata**' yı seçin.

### <a name="create-an-uberflip-test-user"></a>Uberflip test kullanıcısı oluşturma

B. Simon adlı bir Kullanıcı artık Uberflip içinde oluşturulmuştur. Bu kullanıcıyı oluşturmak için herhangi bir şey yapmanız gerekmez. Uberflip, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. B. Simon adlı bir Kullanıcı Uberflip içinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!NOTE]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Uberflip destek ekibine](mailto:support@uberflip.com)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı My Apps portalını kullanarak test edersiniz.

Uygulamalarım portalındaki **Uberflip** ' ı seçtiğinizde, çoklu oturum açmayı ayarladığınız uberflip aboneliğine otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım portalı hakkında daha fazla bilgi için bkz. [My Apps Portalındaki uygulamaları erişme ve kullanma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

* [SaaS uygulamalarını Azure Active Directory tümleştirme öğreticilerinin listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
