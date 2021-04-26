---
title: 'Öğretici: aslına uygunluk avantajları ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve uygunlukta Netavantajlarla çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: ac41bf89a3c2997fe0c179ef1a64787b5fb8d617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453595"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Öğretici: uygunlukta Netavantajlarla tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile uygunluk avantajlarının nasıl tümleştirileceğini öğreneceksiniz.
Aslına uygunluk avantajlarının Azure AD ile tümleştirilmesi aşağıdaki avantajları sağlar:

* Azure AD 'de, uygunlukta Netavantajlara erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Netavantajlara (çoklu oturum açma) uygunluk için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini uygunluk açısından Netavantajlarla yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Uygunlukta Netavantaj çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Uygunluk açısından güvenilirlik, **IDP** tarafından başlatılan SSO 'yu destekler

* Uygunlukta Netavantajlar **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Galeriden aslına uygunluk avantajları ekleme

Aslına uygunluk avantajlarının Azure AD 'de tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize uygunluk açısından aslına uygunluk avantajları eklemeniz gerekir.

**Galeriden uygunluk avantajları eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **uygunluk netme** yazın, sonuç panelinden **uygunluk Netliklerden yararlanın** ' ı seçin ve ardından uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuçlar listesinde aslına uygunluk avantajları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre uygunlukta netavantajlarla yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasındaki uygunluk açısından uygunluğu yüksek bir bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, uygunluk açısından yüksek avantajlar ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Uygunlukta Netavantajların tek oturum açma ayarlarını yapılandırın](#configure-fidelity-netbenefits-single-sign-on)** ve uygulama tarafında tek Sign-On ayarlarını yapılandırın.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimi ile bağlantılı, uygunluk açısından yüksek bir avantaj elde etmek için **[uygunluk açısından uygunluk testi kullanıcısı oluşturun](#create-fidelity-netbenefits-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, uygunluk açısından Netavantajlarla yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **uygunluk açısından netavantajlar** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Uygunlukta Netavantajlar etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

    Test ortamı için:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Üretim ortamı için:  `urn:sp:fidelity:geninbndnbparts20`

    b. **Yanıt URL 'si** metin kutusuna, uygulama sırasında uygunluk tarafından sağlanacak bir URL yazın veya atanan uygunlukta istemci Service Manager başvurun.

5. Uygunlukta Netavantajlar uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName** ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. Uygunlukta Netavantajlar uygulaması, **NameIdentifier** 'ın **EmployeeID** ile veya **adıdentifier** olarak kuruluşunuz için geçerli olan başka bir taleple eşlenmesini bekler, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Aslına uygunluk avantajları statik ve dinamik Federasyonu destekler. Statik, Kullanıcı sağlama ve dinamik 'in yalnızca zamanında Kullanıcı sağlamasını desteklediği anlamına gelen SAML 'yi kullanmayacağı anlamına gelir. JıT tabanlı sağlama müşterilerinin kullanılması için, Azure AD 'de kullanıcının Doğum tarihi vb. gibi bazı talepler daha fazla talep eklemektir. Bu ayrıntılar, atanan **uygunluk istemciniz Service Manager** tarafından sağlanır ve bu dinamik Federasyonun örneğiniz için etkinleştirmeleri gerekir.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **Aslına uygunluk neti ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Uygunlukta Netavantajların tek Sign-On yapılandırılması

**Aslına uygunluk açısından** yüksek bir oturum açma 'yı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal [uygunlukta netavantajlar destek ekibine](mailto:SSOMaintenance@fmr.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına **\@ bricompansıon yourcompanydomain. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, aslına uygunluk avantajlarına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **uygunluk düzeyi avantajları**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **uygunluk/avantajlar**' ı seçin.

    ![Uygulamalar listesindeki uygunlukta Netavantajlar bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-fidelity-netbenefits-test-user"></a>Uygunluk Netavantaj testi Kullanıcı Oluştur

Bu bölümde, uygunluk açısından açık bir netme olarak Britta Simon adlı bir Kullanıcı oluşturacaksınız. Statik Federasyon oluşturuyorsanız, uygun uygunlukta Netavantaj platformunda Kullanıcı oluşturmak için lütfen atanan **uygunluk istemciniz Service Manager** çalışın. Çoklu oturum açma kullanılmadan önce bu kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

Dinamik Federasyon için, kullanıcılar tam zamanında Kullanıcı hazırlama kullanılarak oluşturulur. JıT tabanlı sağlama müşterilerinin kullanılması için, Azure AD 'de kullanıcının Doğum tarihi vb. gibi bazı talepler daha fazla talep eklemektir. Bu ayrıntılar, atanan **uygunluk istemciniz Service Manager** tarafından sağlanır ve bu dinamik Federasyonun örneğiniz için etkinleştirmeleri gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim paneli 'nde uygunluk netme kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız uygunluk açısından otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)