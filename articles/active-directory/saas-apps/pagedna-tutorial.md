---
title: 'Öğretici: Azure Active Directory Tümleştirmesi ile PageDNA | Microsoft Docs'
description: Azure Active Directory ve PageDNA arasında çoklu oturum açmayı yapılandırmayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227473"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Öğretici: PageDNA ile Azure Active Directory Tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile PageDNA tümleştirme konusunda bilgi edinin.

Azure AD ile PageDNA tümleştirme ile aşağıdaki avantajları sağlar:

* Azure AD'de PageDNA için kimlerin erişebildiğini denetleyebilirsiniz.
* Kullanıcılarınız için PageDNA (çoklu oturum açma) ile Azure AD hesaplarına otomatik olarak oturum açmanız etkinleştirebilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilir: Azure portalı.

Azure AD ile bir hizmet (SaaS) uygulamasını tümleştirme olarak yazılım hakkında daha fazla ayrıntı için bkz: [uygulama erişimi ve Azure Active Directory ile çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Azure AD Tümleştirmesi ile PageDNA yapılandırmak için aşağıdaki öğeler gerekir:

* Azure AD aboneliğiniz. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* Çoklu oturum etkin açma PageDNA abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açma bir test ortamında test edin ve PageDNA Azure AD ile tümleştirme yapılandırın.

PageDNA aşağıdaki özellikleri destekler:

* SP tarafından başlatılan çoklu oturum açma (SSO).

* Just-ın-time kullanıcı sağlama.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Azure Market'ten PageDNA Ekle

Azure AD'ye PageDNA tümleştirmesini yapılandırmak için PageDNA Azure Market'te yönetilen SaaS uygulamaları listesine eklemeniz gerekir:

1. [Azure Portal](https://portal.azure.com?azure-portal=true) oturum açın.
1. Sol bölmede **Azure Active Directory**’yi seçin.

    ![Azure Active Directory seçeneği](common/select-azuread.png)

1. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Yeni bir uygulama eklemek için seçin **+ yeni uygulama** bölmenin üstünde.

    ![Yeni uygulama seçeneği](common/add-new-app.png)

1. Arama kutusuna **PageDNA**. Arama sonuçlarında seçin **PageDNA**ve ardından **Ekle** uygulama eklemek için.

    ![Sonuç listesinde PageDNA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, yapılandırma ve Azure AD çoklu oturum açma PageDNA adlı bir test kullanıcı tabanlı test **Britta Simon**. Tek iş için oturum açma için PageDNA içinde bir Azure AD kullanıcısı ile ilgili kullanıcı arasında bir bağlantı oluşturmanız gerekir.

Yapılandırma ve Azure AD çoklu oturum açma PageDNA ile test etmek için aşağıdaki yapı taşlarını tamamlanması gerekir:

1. **[Azure AD çoklu oturum açmayı yapılandırma](#configure-azure-ad-single-sign-on)**  kullanıcılarınız bu özelliği kullanmak etkinleştirmek için.
1. **[PageDNA çoklu oturum açmayı yapılandırma](#configure-pagedna-single-sign-on)**  üzerinde uygulama tarafından çoklu oturum açma ayarları yapılandırmak için.
1. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  Azure AD çoklu oturum açma Britta Simon ile test etmek için.
1. **[Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user)**  Britta Simon, Azure AD çoklu oturum açma kullanmak üzere etkinleştirmek için.
1. **[PageDNA test kullanıcısı oluşturma](#create-a-pagedna-test-user)**  kullanan Azure AD kullanıcı için bağlantılı adlandırılmış Britta Simon PageDNA, böylece bir kullanıcı Britta Simon adlı.
1. **[Çoklu oturum açmayı test](#test-single-sign-on)**  yapılandırma çalışıp çalışmadığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure AD çoklu oturum açma Azure portalında etkinleştirin.

Azure AD çoklu oturum açma ile PageDNA yapılandırmak için aşağıdaki adımları uygulayın:

1. İçinde [Azure portalında](https://portal.azure.com/), **PageDNA** uygulama tümleştirme sayfasında **çoklu oturum açma**.

    ![Çoklu oturum açma seçeneği yapılandırın](common/select-sso.png)

1. İçinde **tek bir oturum açma yönteminizi seçmeniz** bölmesinde seçin **SAML/WS-Federasyon** modu, çoklu oturum açmayı etkinleştirmek için.

    ![Çoklu oturum açma seçim modu](common/select-saml-option.png)

1. Üzerinde **yukarı çoklu oturum açma SAML ile Ayarla** bölmesinde **Düzenle** (açmak için kalem simgesi) **temel SAML yapılandırma** bölmesi.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. İçinde **temel SAML yapılandırma** bölmesinde, aşağıdaki adımları uygulayın:

    ![Oturum açma bilgileri çoklu PageDNA etki alanı ve URL'ler](common/sp-identifier.png)

    1. İçinde **oturum açma URL'si** kutusunda, aşağıdaki desenlerden birini kullanarak bir URL girin:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. İçinde **tanımlayıcı (varlık kimliği)** kutusunda, aşağıdaki desenlerden birini kullanarak bir URL girin:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri tanımlayıcısı ve gerçek oturum açma URL'si ile güncelleştirin. Bu değerleri almak için iletişime geçin [PageDNA Destek ekibine](mailto:success@pagedna.com). Gösterilen desenleri de başvurabilirsiniz **temel SAML yapılandırma** bölmesinde Azure portalında.

1. İçinde **yukarı çoklu oturum açma SAML ile ayarlanmış** bölmesinde, **SAML imzalama sertifikası** bölümünden **indirme** indirmek için **sertifika (ham)** belirtilen seçenekler ve bilgisayarınıza kaydedin.

    ![Sertifika (ham) yükleme seçeneği](common/certificateraw.png)

1. İçinde **PageDNA kümesi** bölümünde, URL veya gereken URL'leri kopyalayın:

   * **Oturum açma URL'si**
   * **Azure AD tanımlayıcısı**
   * **Oturum kapatma URL'si**

    ![Yapılandırma URL'leri kopyalayın](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>PageDNA çoklu oturum açmayı yapılandırın

Çoklu oturum açma PageDNA tarafında yapılandırmak için indirilen sertifika (ham) ve uygun kopyalanan URL'leri için Azure Portalı'ndan göndermek [PageDNA Destek ekibine](mailto:success@pagedna.com). PageDNA takım SAML SSO bağlantının her iki kenarı da düzgün ayarlandığından emin olun.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portalında Britta Simon adlı bir test kullanıcısı oluşturun.

1. Azure portalında, sol bölmede seçin **Azure Active Directory**   > **kullanıcılar** > **tüm kullanıcılar**.

    ![Kullanıcılar ve "Tüm kullanıcılar" seçenekleri](common/users.png)

1. Ekranın üst kısmında seçin **+ yeni kullanıcı**.

    ![Yeni kullanıcı seçeneği](common/new-user.png)

1. İçinde **kullanıcı** bölmesinde, aşağıdaki adımları uygulayın:

    ![Kullanıcı bölmesi](common/user-properties.png)

    1. İçinde **adı** kutusuna **BrittaSimon**.
  
    1. İçinde **kullanıcı adı** kutusuna **BrittaSimon\@\<yourcompanydomain >.\< Uzantı >** . Örneğin, **BrittaSimon\@contoso.com**.

    1. Seçin **Show parola** onay kutusunu işaretleyin ve ardından görüntülenen değeri yazın **parola** kutusu.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, kullanıcının kullanıcı erişimi için PageDNA vererek, Azure çoklu oturum açma kullanılacak Britta Simon etkinleştirin.

1. Azure portalında **kurumsal uygulamalar** > **tüm uygulamaları** > **PageDNA**.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Uygulamalar listesinde **PageDNA**.

    ![Uygulamalar listesinde PageDNA](common/all-applications.png)

1. Sol bölmede altında **Yönet**seçin **kullanıcılar ve gruplar**.

    !["Kullanıcılar ve Gruplar" seçeneği](common/users-groups-blade.png)

1. Seçin **+ Ekle kullanıcı**ve ardından **kullanıcılar ve gruplar** içinde **atama Ekle** bölmesi.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

1. İçinde **kullanıcılar ve gruplar** bölmesinde **Britta Simon** içinde **kullanıcılar** listeleyin ve ardından **seçin** bölmesinin alt kısmındaki.

1. SAML onaylaması rol değerinde ardından içinde beklediğiniz varsa **rolü Seç** bölmesinde, listeden bir kullanıcı için uygun rolü seçin. Bölmesinin en altında seçin **seçin**.

1. İçinde **atama Ekle** bölmesinde **atama**.

### <a name="create-a-pagedna-test-user"></a>PageDNA test kullanıcısı oluşturma

Britta Simon adlı bir kullanıcı PageDNA oluşturuldu. Bu kullanıcı oluşturmak için herhangi bir şey yapmanız gerekmez. PageDNA just-ın-time kullanıcı hazırlama, varsayılan olarak etkin olduğu destekler. Britta Simon adlı bir kullanıcı PageDNA içinde zaten mevcut değilse yeni bir kimlik doğrulamasından sonra oluşturulur.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, uygulamalarım portalını kullanarak Azure AD çoklu oturum açma yapılandırmanızı test.

Seçtiğinizde, **PageDNA** uygulamalarım portalında, otomatik olarak kendisi için ayarladığınız çoklu oturum açmayı PageDNA aboneliğe oturum. Uygulamalarım portal hakkında daha fazla bilgi için bkz. [erişim ve kullanım uygulamaları uygulamalarım portalında](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

* [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Azure Active Directory'de uygulamalar için çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

