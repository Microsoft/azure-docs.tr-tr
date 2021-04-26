---
title: 'Öğretici: PageDNA | ile tümleştirme Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve PageDNA arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: e1ccd337851f8242526362675a11280e33584082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993933"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Öğretici: PageDNA ile Azure Active Directory tümleştirme

Bu öğreticide, PageDNA Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

PageDNA 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, PageDNA 'ye kimlerin erişebileceğini denetleyebilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla PageDNA (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini PageDNA ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* Çoklu oturum açma özellikli bir PageDNA aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edin ve PageDNA 'yi Azure AD ile tümleştirin.

PageDNA aşağıdaki özellikleri destekler:

* SP tarafından başlatılan çoklu oturum açma (SSO).

* Tam zamanında Kullanıcı hazırlama.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Azure Marketi 'nden PageDNA ekleyin

PageDNA 'in Azure AD 'ye tümleştirmesini yapılandırmak için, Azure Marketi 'nden yönetilen SaaS uygulamaları listenize PageDNA eklemeniz gerekir:

1. [Azure portalında](https://portal.azure.com?azure-portal=true) oturum açın.
1. Sol bölmede **Azure Active Directory**’yi seçin.

    ![Azure Active Directory seçeneği](common/select-azuread.png)

1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **+ Yeni uygulama** ' yı seçin.

    ![Yeni uygulama seçeneği](common/add-new-app.png)

1. Arama kutusuna **PageDNA** yazın. Arama sonuçlarında, **PageDNA** öğesini seçin ve ardından uygulamayı eklemek için **Ekle** ' yi seçin.

    ![Sonuçlar listesinde PageDNA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına göre PageDNA Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz. Çoklu oturum açma için, PageDNA içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı kurmanız gerekir.

Azure AD çoklu oturum açma 'yı PageDNA ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[PageDNA Single oturum açmayı yapılandırın](#configure-pagedna-single-sign-on)** .
1. Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
1. Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Britta Simon adlı Azure AD kullanıcısına bağlı olan PageDNA 'de Britta Simon adlı bir Kullanıcı **[oluşturmak için bir PageDNA test kullanıcısı oluşturun](#create-a-pagedna-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı PageDNA ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **PageDNA** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma seçeneğini yapılandırma](common/select-sso.png)

1. Çoklu oturum **açma yöntemi seçin** bölmesinde, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

1. **SAML Ile tek Sign-On ayarla** bölmesinde, **temel SAML yapılandırması** bölmesini açmak için **Düzenle** (kurşun kalem simgesi) öğesini seçin.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölmesinde aşağıdaki adımları uygulayın:

    ![PageDNA etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    1. **Oturum açma URL 'si** kutusunda, aşağıdaki desenlerden birini kullanarak bir URL girin:

        ```https
        https://stores.pagedna.com/<your site>
        https://<your domain>
        https://<your domain>/<your site>
        https://www.nationsprint.com/<your site>
        ```

    1. **Tanımlayıcı (VARLıK kimliği)** kutusunda, aşağıdaki desenlerden birini kullanarak bir URL girin:

        ```https
        https://stores.pagedna.com/<your site>/saml2ep.cgi
        https://www.nationsprint.com/<your site>/saml2ep.cgi
        ```

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için, [PageDNA destek ekibine](mailto:success@pagedna.com)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölmesinde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tek Sign-On ayarla** bölmesinde, **SAML imzalama sertifikası** bölümünde, **sertifikayı (ham)** verilen seçeneklerden indirmek için **İndir** ' i seçin ve bilgisayarınıza kaydedin.

    ![Sertifika (ham) indirme seçeneği](common/certificateraw.png)

1. **PageDNA ayarla** bölümünde, IHTIYACıNıZ olan URL veya URL 'leri kopyalayın:

   * **Oturum açma URL 'SI**
   * **Azure AD tanımlayıcısı**
   * **Oturum kapatma URL 'SI**

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>PageDNA Single oturum açmayı yapılandırma

PageDNA tarafında çoklu oturum açmayı yapılandırmak için, indirilen sertifikayı (ham) ve uygun kopyalanmış URL 'Leri Azure portal [PageDNA support ekibine](mailto:success@pagedna.com)gönderin. PageDNA ekibi, SAML SSO bağlantısının her iki tarafında da düzgün şekilde ayarlanmış olmasını sağlayacaktır.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmedeki **Azure Active Directory**    >  **Kullanıcılar**  >  **tüm kullanıcılar**' ı seçin.

    ![Kullanıcılar ve "tüm kullanıcılar" seçenekleri](common/users.png)

1. Ekranın üst kısmında **+ Yeni Kullanıcı**' yı seçin.

    ![Yeni Kullanıcı seçeneği](common/new-user.png)

1. **Kullanıcı** bölmesinde, aşağıdaki adımları uygulayın:

    ![Kullanıcı bölmesi](common/user-properties.png)

    1. **Ad** kutusuna **Brittasıon** yazın.
  
    1. **Kullanıcı adı** kutusuna **Brittasıon yazın \@ \<yourcompanydomain> . \<extension>** Örneğin, **Brittasıon \@ contoso.com**.

    1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.

    1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, kullanıcının PageDNA 'e erişimi vererek Azure çoklu oturum açma özelliğini kullanmasını sağlayabilirsiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**  >  **PageDNA**' yi seçin.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Uygulamalar listesinde **PageDNA**' yi seçin.

    ![Uygulamalar listesinde PageDNA](common/all-applications.png)

1. Sol bölmedeki **Yönet** altında **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" seçeneği](common/users-groups-blade.png)

1. **+ Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** bölmesinde, **Kullanıcılar** listesinde **Britta Simon** ' ı seçin ve ardından bölmenin en altında bulunan **Seç** ' i seçin.

1. SAML onaylama 'da bir rol değeri bekliyorsanız, **Rol Seç** bölmesinde, Kullanıcı için listeden uygun rolü seçin. Bölmenin en altında bulunan **Seç**' i seçin.

1. **Atama Ekle** bölmesinde **ata**' yı seçin.

### <a name="create-a-pagedna-test-user"></a>PageDNA test kullanıcısı oluşturma

Britta Simon adlı bir Kullanıcı artık PageDNA içinde oluşturulmuştur. Bu kullanıcıyı oluşturmak için herhangi bir şey yapmanız gerekmez. PageDNA, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Britta Simon adlı bir Kullanıcı PageDNA içinde zaten yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı My Apps portalını kullanarak test edersiniz.

Uygulamalarım portalındaki **PageDNA** ' yi seçtiğinizde, çoklu oturum açmayı ayarladığınız PageDNA aboneliğine otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım portalı hakkında daha fazla bilgi için bkz. [My Apps Portalındaki uygulamaları erişme ve kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [SaaS uygulamalarını Azure Active Directory tümleştirme öğreticilerinin listesi](./tutorial-list.md)

* [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md)

* [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)