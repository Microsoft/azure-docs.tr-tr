---
title: 'Öğretici: LinkedIn yükseltmesine Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve LinkedIn 'ten çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b7cb8d6ab34a632e36ea2fd1c87005a038bc523
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823724"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Öğretici: LinkedIn yükseltmesine Azure Active Directory tümleştirme

Bu öğreticide, LinkedIn yükseltmesine Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
LinkedIn 'in Azure AD ile tümleştirilmesi aşağıdaki avantajları sağlar:

* Azure AD 'de LinkedIn yükseltmesine erişimi olan denetimi yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla LinkedIn 'in (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınız bir merkezi konumda - Azure portalında yönetebilir.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini LinkedIn ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* LinkedIn çoklu oturum açma özellikli aboneliği yükselt

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* LinkedIn yükseltme **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

* LinkedIn 'in **tam zamanında** Kullanıcı sağlamasını desteklemesi

* LinkedIn yükseltme [ **Otomatik** Kullanıcı sağlamasını destekler](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Galeriden LinkedIn 'i ekleme

LinkedIn 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize LinkedIn 'ten yükseltme eklemeniz gerekir.

**Galeriden LinkedIn 'e yükseltme eklemek için aşağıdaki adımları uygulayın:**

1. İçinde **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde tıklayın **Azure Active Directory** simgesi.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için tıklatın **yeni uygulama** iletişim üst kısmındaki düğmesi.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **LinkedIn Yükselt**yazın, sonuç panelinden LinkedIn 'ten **Yükselt** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuçlar listesinde LinkedIn yükselt](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre LinkedIn ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilişkili kullanıcı arasındaki bir bağlantı ilişkisinin LinkedIn 'de oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı LinkedIn ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açmayı yapılandırmayı](#configure-azure-ad-single-sign-on)**  - bu özelliği kullanmak, kullanıcılarınızın etkinleştirmek için.
2. **[LinkedIn 'ı çoklu oturum açmayı yapılandırma](#configure-linkedin-elevate-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırma.
3. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  - Azure AD çoklu oturum açma Britta Simon ile test etmek için.
4. **[Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user)**  - Azure AD çoklu oturum açmayı kullanmak Britta Simon etkinleştirmek için.
5. LinkedIn 'in Azure AD gösterimi ile bağlantılı olan LinkedIn 'e yönelik bir ve daha fazla bağlantı sahibi olmak üzere **[LinkedIn yükseltme testi oluşturun](#create-linkedin-elevate-test-user)** .
6. **[Çoklu oturum açmayı test](#test-single-sign-on)**  - yapılandırma çalışıp çalışmadığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı LinkedIn ile birlikte yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **LinkedIn** Uygulama tümleştirmesini Yükselt sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısı yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **Düzenle** açmak için simgeyi **temel SAML yapılandırma** iletişim.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![LinkedIn etki alanı ve URL 'Leri çoklu oturum açma bilgilerini yükselt](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusuna **varlık kimliği** değerini girin, bu öğreticide daha sonra AÇıKLANAN LinkedIn portalından varlık kimliği değerini kopyalayacaksınız.

    b. **Yanıt URL** 'si metin kutusuna, **onaylama TÜKETICI erişimi (ACS) URL 'si** değerini girin, bu öğreticide daha sonra açıklanan LinkedIn portalından, onaylama tüketici erişimi (ACS) URL 'si değerini kopyalayacaksınız.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![LinkedIn etki alanı ve URL 'Leri çoklu oturum açma bilgilerini yükselt](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. LinkedIn 'i uygulama, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName**ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. LinkedIn yükseltme uygulaması, NameIdentifier 'ın **User. Mail**ile eşlenmesini bekler, bu nedenle, Düzenle simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

7. LinkedIn 'in yanı sıra, LinkedIn 'in uygulama daha fazla özniteliğin SAML yanıtına geri geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki Kullanıcı talepleri bölümünde aşağıdaki tabloda GÖSTERILDIĞI gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Ad | Kaynak özniteliği|
    | -------| -------------|
    | Bölüm | User. Departmanı |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik**olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. Tıklayın **Tamam**

    g. **Kaydet**’e tıklayın.

8. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

9. LinkedIn 'i **ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL'si:

    b. Azure AD Tanımlayıcısı

    c. Oturum Kapatma URL'si

### <a name="configure-linkedin-elevate-single-sign-on"></a>LinkedIn yükseltme çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, LinkedIn yükseltme kiracınızda yönetici olarak oturum açın.

1. **Hesap Merkezi**'nde **Ayarlar**' ın altında **Genel ayarlar** ' a tıklayın. Ayrıca, açılır listeden **Yükselt-Yükselt AAD test** ' i seçin.

    ![Çoklu oturum açmayı yapılandırın](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. **Formdan tek tek alanları yüklemek ve kopyalamak için tıklayın veya buraya tıklayın** ve aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı yapılandırın](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. **Varlık kimliğini** kopyalayın ve Azure Portal **temel SAML yapılandırmasındaki** **tanımlayıcı** metin kutusuna yapıştırın.

    b. **Onaylama tüketici erişimi (ACS) URL 'sini** kopyalayın ve Azure Portal **temel SAML yapılandırmasındaki** **yanıt URL 'si** metin kutusuna yapıştırın.

1. **LinkedIn yönetici ayarları** bölümüne gidin. XML dosyasını karşıya yükle seçeneğine tıklayarak Azure portal indirdiğiniz XML dosyasını karşıya yükleyin.

    ![Çoklu oturum açmayı yapılandırın](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. SSO 'yu etkinleştirmek için tıklayın. SSO durumu **bağlı** **değil** olarak değiştirilecek

    ![Çoklu oturum açmayı yapılandırın](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Britta Simon adlı Azure portalında bir test kullanıcısı oluşturmaktır.

1. Azure portalında, sol bölmede seçin **Azure Active Directory**seçin **kullanıcılar**ve ardından **tüm kullanıcılar**.

    !["Kullanıcılar ve Gruplar" ve "Tüm kullanıcılar" bağlantıları](common/users.png)

2. Seçin **yeni kullanıcı** ekranın üstünde.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı özellikleri, aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanına yazın `brittasimon@yourcompanydomain.extension`. Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, LinkedIn 'e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve **LinkedIn Yükselt**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **LinkedIn Yükselt**' i seçin.

    ![Uygulamalar listesinde LinkedIn Yükselt bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. İçinde **kullanıcılar ve gruplar** iletişim kutusunda **Britta Simon** 'a tıklayın kullanıcı listesinde **seçin** ekranın alt kısmındaki düğmesi.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-linkedin-elevate-test-user"></a>LinkedIn yükseltme testi Kullanıcı Oluştur

LinkedIn uygulaması, yalnızca Kullanıcı sağlamasını ve kimlik doğrulama kullanıcılarının uygulamada otomatik olarak oluşturulmasını destekler. LinkedIn 'in yönetim ayarları sayfasında, anahtarı ters çevir ' i tersine çevir, anahtarı **otomatik olarak** etkin bir şekilde sağlamak üzere atar ve bu da kullanıcıya bir lisans atar. LinkedIn, otomatik Kullanıcı sağlamayı da destekler, Ayrıca, otomatik Kullanıcı sağlamayı [](linkedinelevate-provisioning-tutorial.md) yapılandırma hakkında daha fazla ayrıntı bulabilirsiniz.

   ![Bir Azure AD test kullanıcısı oluşturma](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde LinkedIn yükseltme kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız LinkedIn yükseltmesine otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)