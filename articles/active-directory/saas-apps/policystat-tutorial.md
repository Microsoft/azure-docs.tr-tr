---
title: 'Öğretici: PolicyStat ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve PolicyStat arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 5c2520c8e209ab8319cbc5a369b70d247a52232c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601007"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Öğretici: PolicyStat ile tümleştirme Azure Active Directory

Bu öğreticide, PolicyStat 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
PolicyStat 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* PolicyStat erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla PolicyStat (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

PolicyStat ile Azure AD tümleştirmesini yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* PolicyStat çoklu oturum açma etkin aboneliği

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* PolicyStat **SP** tarafından başlatılan SSO 'yu destekler

* PolicyStat **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-policystat-from-the-gallery"></a>Galeriden PolicyStat ekleme

PolicyStat 'ın Azure AD ile tümleştirilmesini yapılandırmak için Galeriden PolicyStat 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden PolicyStat eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **PolicyStat** yazın, sonuç panelinden **PolicyStat** ' yi seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde PolicyStat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre PolicyStat ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve PolicyStat içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

PolicyStat ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[PolicyStat çoklu oturum açmayı yapılandırın](#configure-policystat-single-sign-on)** .
3. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
4. PolicyStat ' de kullanıcının Azure AD gösterimine bağlı olan bir Britta Simon 'un bir karşılığı olacak **[PolicyStat test kullanıcısı oluşturun](#create-policystat-test-user)** .
5. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

PolicyStat ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **PolicyStat** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![PolicyStat etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.policystat.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [PolicyStat istemci destek ekibine](https://rldatix.com/services-support/support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

5. PolicyStat uygulamanız, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    !["Düzenle" simgesi seçili "Kullanıcı öznitelikleri" iletişim kutusunu gösteren ekran görüntüsü.](common/edit-attribute.png)

6. PolicyStat uygulaması, yukarıdakine ek olarak, SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde AŞAĞıDAKI tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Name | Kaynak özniteliği |
    |------------------- | -------------------- |
    | 'sini | Extractmailprefıx ([mail]) |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.
    
    !["Yeni talep Ekle" ve "Kaydet" eylemlerinin vurgulandığı "Kullanıcı talepleri" bölümünü gösteren ekran görüntüsü.](common/new-save-attribute.png)

    !["Ad", "dönüşüm" ve "parametre" metin kutuları vurgulanmış ve "Kaydet" düğmesi seçili olan "Kullanıcı taleplerini Yönet" iletişim kutusunu gösteren ekran görüntüsü.](./media/policystat-tutorial/attribute01.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Dönüşüm** olarak kaynak ' ı seçin.

    e. **Dönüştürme** listesinden, bu satır için gösterilen öznitelik değerini yazın.
    
    f. **Parameter 1** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    örneğin: **Kaydet**’e tıklayın.

7. **PolicyStat ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-policystat-single-sign-on&quot;></a>PolicyStat tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, PolicyStat şirket sitenizde yönetici olarak oturum açın.

2. **Yönetici** sekmesine tıklayın ve ardından sol gezinti bölmesinde **tek Sign-On yapılandırması** ' na tıklayın.
   
    ![Yönetici Menüsü](./media/policystat-tutorial/ic808633.png &quot;Yönetici Menüsü")

3. **IDP meta verilerinize** tıklayın ve sonra **IDP meta verileri** bölümünde aşağıdaki adımları uygulayın:
   
    !["I D P meta verilerini" seçtiğiniz ekran görüntüsü.](./media/policystat-tutorial/ic808636.png "Tek Sign-On yapılandırması")
   
    a. İndirilen meta veri dosyanızı açın, içeriği kopyalayın ve ardından **kimlik sağlayıcısı meta verileri** metin kutusuna yapıştırın.

    b. **Değişiklikleri Kaydet**’e tıklayın.

4. **Öznitelikleri Yapılandır**' a tıklayın ve ardından **öznitelikleri Yapılandır** bölümünde aşağıdaki adımları gerçekleştirin:
   
    a. **Kullanıcı adı özniteliği** metin kutusuna **uid** yazın.

    b. **First Name öznitelik** metin kutusunda, Azure 'Dan ad özniteliği talep adınızı yazın **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    c. **Son ad öznitelik** metin kutusunda, Azure 'Dan son ad öznitelik talebi adınızı yazın **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .

    d. **E-posta özniteliği** metin kutusunda, Azure 'Dan e-posta öznitelik talep adınızı yazın **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    e. **Değişiklikleri Kaydet**’e tıklayın.

5. **Kurulum** bölümünde **Çoklu oturum açma tümleştirmesini etkinleştir**' i seçin.
   
    ![Tek Sign-On yapılandırması](./media/policystat-tutorial/ic808634.png "Tek Sign-On yapılandırması")


### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, PolicyStat 'a erişim vererek kendi hesabınızı Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **PolicyStat**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **PolicyStat**' yi seçin.

    ![Uygulamalar listesindeki PolicyStat bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden hesabınızı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-policystat-test-user"></a>PolicyStat test kullanıcısı oluştur

Bu bölümde, PolicyStat 'da Britta Simon adlı bir Kullanıcı oluşturulur. PolicyStat, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. PolicyStat içinde bir kullanıcı zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!NOTE]
>Azure AD Kullanıcı hesapları sağlamak için PolicyStat tarafından sunulan diğer PolicyStat Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde PolicyStat kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız PolicyStat ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)
