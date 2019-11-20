---
title: 'Öğretici: Launchkaranlık ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve başlatma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3f0671bc-f93f-496e-b465-b9ce8c6633fa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e32f8c3ea300960893163264e99bd6c51138c7c5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159647"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Öğretici: Launchkaranlık ile tümleştirme Azure Active Directory

Bu öğreticide, Launchkaranlık 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Launchkaranlık Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Launchkaranlık 'e erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla karanlık (çoklu oturum açma) başlatma işlemi için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Launchkaranlık olarak yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Açık çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Launchkaranlık **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Launchkaranlık **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-launchdarkly-from-the-gallery"></a>Galeriden Launchkaranlık ekleme

Başlatma 'nın Azure AD 'ye tümleştirilmesine yönelik tümleştirmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Launchkaranlık öğesini eklemeniz gerekir.

**Galeriden Launchkaranlık olarak eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusunda, **launchkaranlık**yazın, sonuç panelinden **launchkoyu** ' i seçin, sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

     ![Sonuçlar listesinde koyu olarak başlatma](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına bağlı olarak [uygulama adı] ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve [uygulama adı] içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı [uygulama adı] ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[Launchkoyu çoklu oturum açmayı yapılandırma](#configure-launchdarkly-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olarak, Launchkaranlık ve üzerinde bir Britta Simon 'a sahip olmak için, **[launchkaranlık test kullanıcısı oluşturun](#create-launchdarkly-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı [uygulama adı] ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **launchkoyu** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Koyu etki alanı ve URL 'Ler çoklu oturum açma bilgileri başlatma](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna bir URL yazın: `app.launchdarkly.com`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Değeri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek yanıt URL 'siyle güncellecektir. Uygulamayı **IDP** modunda kullanmak Istiyorsanız, **oturum açma URL 'si** alanını boş bırakmanız gerekir, aksi takdirde **IDP**'den oturum açma işlemi başlatamazsınız. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://app.launchdarkly.com`

    ![Koyu etki alanı ve URL 'Ler çoklu oturum açma bilgileri başlatma](common/metadata-upload-additional-signon.png)

6. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Launchkoyu 'ı ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-launchdarkly-single-sign-on"></a>Launchkoyu çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Launchkaranlık şirket sitenizde yönetici olarak oturum açın.

2. Sol Gezinti panelinden **Hesap ayarları** ' nı seçin.

    ![Başlatma koyu yapılandırma](./media/launchdarkly-tutorial/configure1.png)

3. **Güvenlik** sekmesine tıklayın.

    ![Başlatma koyu yapılandırma](./media/launchdarkly-tutorial/configure2.png)

4. **SSO 'Yu etkinleştir** ' e tıklayın ve ardından **SAML yapılandırmasını düzenleyin**.

    ![Başlatma koyu yapılandırma](./media/launchdarkly-tutorial/configure3.png)

5. **SAML yapılandırmanızı Düzenle** bölümünde aşağıdaki adımları uygulayın:

    ![Başlatma koyu yapılandırma](./media/launchdarkly-tutorial/configure4.png)

    a. Örneğiniz için **SAML tüketicisi hizmeti URL 'sini** kopyalayın ve Azure Portal ' de **Launchkoyu etki alanı ve URL 'ler** bölümünde yanıt URL 'si metin kutusuna yapıştırın.

    b. Oturum açma **URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Azure portal indirilen sertifikayı not defteri 'ne açın, içeriği kopyalayın ve **X. 509.440 sertifikası** kutusuna yapıştırın veya **karşıya yükle**' ye tıklayarak sertifikayı doğrudan karşıya yükleyebilirsiniz.

    d. **Kaydet**’e tıklayın

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Launchkaranlık 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirecektir.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra da **launchkaranlık**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Launchkaranlık**' i seçin.

    ![Uygulamalar listesinde Launchkaranlık bağlantı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-launchdarkly-test-user"></a>Launchkaranlık test kullanıcısı oluşturma

Bu bölümün amacı, Launchkaranlık ile Britta Simon adlı bir Kullanıcı oluşturmaktır. Launchkaranlık, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Henüz mevcut değilse, Launchkaranlık 'e erişme girişimi sırasında yeni bir Kullanıcı oluşturulur.

> [!Note]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Launchkaranlık istemci desteği ekibine](mailto:support@launchdarkly.com)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Ilk olarak başlatma kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız başlatma için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
