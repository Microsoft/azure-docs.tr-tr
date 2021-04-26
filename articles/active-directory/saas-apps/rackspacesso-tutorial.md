---
title: 'Öğretici: Rackspace SSO ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Rackspace SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: eed0ea075c5e9eb39810259be2aa6781a59f2f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511690"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Öğretici: Rackspace SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Rackspace SSO 'yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Rackspace SSO 'yu Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Rackspace SSO 'ya erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Rackspace SSO (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Rackspace SSO 'SU ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Rackspace SSO çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Rackspace SSO, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-rackspace-sso-from-the-gallery"></a>Galeriden Rackspace SSO 'SU ekleme

Rackspace SSO 'yu Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden Rackspace SSO 'SU ile yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden Rackspace SSO 'SU eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **RACKSPACE SSO** yazın, sonuç panelinden **RACKSPACE SSO** ' yı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuç listesinde Rackspace SSO 'SU](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına göre Rackspace SSO 'Su Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Rackspace ile çoklu oturum açma kullanılırken, Rackspace kullanıcıları ilk kez Rackspace portalında oturum açtıklarında otomatik olarak oluşturulur. 

Azure AD çoklu oturum açma 'yı Rackspace SSO 'SU ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Rackspace SSO çoklu oturum açma yapılandırma](#configure-rackspace-sso-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
1. Azure AD kullanıcılarına Rackspace rolleri atamak için **[Rackspace denetim masasında öznitelik eşlemesini ayarlayın](#set-up-attribute-mapping-in-the-rackspace-control-panel)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Rackspace SSO 'SU ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **RACKSPACE SSO** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, [URL](https://login.rackspace.com/federate/sp.xml) 'Den indirebileceğiniz **hizmet sağlayıcısı meta veri dosyasını** karşıya yükleyin ve aşağıdaki adımları uygulayın:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![Ekran görüntüsünde, meta veri dosyası yükleme bağlantısıyla temel SAML yapılandırması gösterilir.](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![Ekran görüntüsünde bir dosyayı seçip karşıya yükleyebileceğiniz bir iletişim kutusu gösterilir.](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra gerekli URL 'ler otomatik olarak otomatik olarak doldurulur.

    d. **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://login.rackspace.com/federate/`

    ![Rackspace SSO etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)   

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

Bu dosya, gerekli kimlik Federasyonu yapılandırma ayarlarını doldurmak için Rackspace 'e yüklenecek.

### <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace SSO tek Sign-On yapılandırma

**Rackspace SSO** tarafında çoklu oturum açmayı yapılandırmak için:

1. [Denetim masasına kimlik sağlayıcısı ekleme](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/) bölümüne bakın
1. Şu adımlarda size yol açacaksınız:
    1. Yeni bir kimlik sağlayıcısı oluşturma
    1. Kullanıcıların, oturum açarken şirketinizi tanımlamak için kullanacağı bir e-posta etki alanı belirtin.
    1. Daha önce Azure denetim masasından indirilen **Federasyon meta VERI XML** 'sini karşıya yükleyin.

Bu işlem, Azure ve Rackspace 'in bağlanması için gereken temel SSO ayarlarını doğru şekilde yapılandırır.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına yazın `brittasimon@yourcompanydomain.extension` . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Rackspace SSO 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Rackspace SSO**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Rackspace SSO**' yı seçin.

    ![Uygulamalar listesindeki Rackspace SSO bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Rackspace denetim masasında öznitelik eşlemesini ayarlama

Rackspace, tek oturum açma kullanıcılarınıza Rackspace rolleri ve grupları atamak için bir **öznitelik eşleme ilkesi** kullanır. **Öznitelik eşleme ilkesi** , Azure AD SAML taleplerini Rackspace 'in gerektirdiği Kullanıcı yapılandırma alanlarına çevirir. Rackspace [öznitelik eşlemesi temel belgelerinde](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)daha fazla belge bulunabilir. Bazı hususlar:

* Azure AD gruplarını kullanarak çeşitli Rackspace erişimi atamak istiyorsanız, Azure **Rackspace SSO** çoklu oturum açma ayarlarındaki gruplar talebini etkinleştirmeniz gerekir. **Öznitelik eşleme ilkesi** , bu grupları Istenen Rackspace rolleri ve grupları ile eşleştirmek için kullanılacaktır:

    ![Grup talebi ayarları](common/sso-groups-claim.png)

* Azure AD, varsayılan olarak, SAML talebinde, Grup adına karşılık olarak Azure AD gruplarının UID 'sini gönderir. Ancak şirket içi Active Directory Azure AD 'ye eşitliyorsanız, grupların gerçek adlarını gönderme seçeneğiniz vardır:

    ![Grup talebi adı ayarları](common/sso-groups-claims-names.png)

Aşağıdaki örnek **öznitelik eşleme ilkesi** şunları gösterir:
1. Rackspace kullanıcısının adı `user.name` SAML talebine ayarlanıyor. Herhangi bir talep kullanılabilir, ancak bu, en yaygın olarak kullanıcının e-posta adresini içeren bir alana ayarlanır.
1. Rackspace rollerini `admin` ve `billing:admin` bir kullanıcıyı BIR Azure AD grubuyla eşleştirerek Grup adına veya grup UID 'ye göre ayarlama. Alanı içindeki bir *değiştirme* `"{0}"` `roles` kullanılır ve kural ifadelerinin sonuçlarıyla değiştirilirler `remote` .
1. `"{D}"` *Varsayılan değiştirme* 'YI kullanarak, SAML Exchange 'de standart ve iyi bilinen SAML taleplerini arayarak rafa kspace 'in ek SAML alanları almasına izin verin.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> İlke dosyanızı düzenlenirken YAML söz dizimini doğrulayan bir metin Düzenleyicisi kullandığınızdan emin olun.

Daha fazla örnek için bkz. Rackspace [öznitelik eşleme temelleri belgeleri](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) .

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Rackspace SSO kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Rackspace SSO 'SU için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

Ayrıca, **Rackspace SSO** çoklu oturum açma ayarlarındaki **Doğrula** düğmesini de kullanabilirsiniz:

   ![SSO doğrulama düğmesi](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)