---
title: 'Öğretici: Sorgtetra BPM Suite ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Sorgtetra BPM Suite arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 13f05005529d3983e042398f5274fd7f981b8c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516605"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Öğretici: Sorgtetra BPM Suite ile tümleştirme Azure Active Directory

Bu öğreticide, Sorgtetra BPM Suite 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Sorgtetra BPM Suite 'i Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Sorgtetra BPM Suite 'e erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Sorgtetra BPM Suite (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Sorgıntra BPM Suite ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Sorgtetra BPM Suite çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Sorgtetra BPM Suite **SP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Galeriden Sorgtetra BPM Suite ekleniyor

Sorgtetra BPM Suite 'in Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Sorgıntra BPM Suite eklemeniz gerekir.

**Galeriden Sorgıntra BPM Suite eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **SORGTETRA BPM Suite** yazın, sonuç panelinden **Sorgtetra BPM Suite** ' i seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuçlar listesinde sorgtetra BPM Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına dayalı olarak, Sorgtetra Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Sorgtetra BPM Suite içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, Sorgtetra BPM Suite ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Sorgtetra BPM Suite çoklu oturum açmayı yapılandırın](#configure-questetra-bpm-suite-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[SORGTETRA BPM Suite test kullanıcısı oluşturun](#create-questetra-bpm-suite-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Sorgtetra BPM Suite 'Te Britta Simon 'a sahip olmalıdır.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD 'de, Sorgtetra BPM Suite ile çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **SORGTETRA BPM Suite** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Sorgtetra BPM Suite etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI ve tanımlayıcısıyla güncelleştirin. Bu değerleri, Öğreticinin ilerleyen kısımlarında açıklandığı gibi, **SORGTETRA BPM Suite** şirket sitenizde bulunan **SP bilgisi** bölümünden [alabilirsiniz.](https://www.questetra.com/contact/) Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. , **Sorgıntra BPM Suite ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Sorgtetra BPM Suite tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, **Sorgtetra BPM Suite** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **sistem ayarları**' na tıklayın. 
   
    ![Ekran görüntüsü, Sorgtetra BPM Suite şirket sitesinden seçilen sistem ayarlarını gösterir.][10]

3. **Singlesignonsaml** sayfasını açmak için **SSO (SAML)**' ye tıklayın. 
   
    ![Ekran görüntüsü seçili olan S (SAML) ' u gösterir.][11]

4. **Sorgtetra BPM Suite** şirket sitenizde, **SP bilgileri** bölümünde aşağıdaki adımları uygulayın:

    a. **ACS URL 'sini** kopyalayın ve ardından Azure Portal ' den **temel SAML YAPıLANDıRMASı** bölümünde bulunan **URL 'yi oturum aç** metin kutusuna yapıştırın.
    
    b. **VARLıK kimliğini** kopyalayın ve ardından Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** metin kutusuna yapıştırın.

5. **Sorgtetra BPM Suite** şirket sitenizde aşağıdaki adımları gerçekleştirin: 
   
    ![Tek Sign-On yapılandırma][15]
   
    a. **Çoklu oturum açmayı etkinleştir '** i seçin.
   
    b. **VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.
    
    c. **Oturum açma sayfası URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
    
    d. **Oturum kapatma sayfası URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.
    
    e. **NameID biçim** metin kutusuna yazın `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    f. Azure portal 'den indirilen Not defteri 'nde **Base-64** kodlu sertifikanızı açın, içeriğini panonuza kopyalayın ve **doğrulama sertifikası** metin kutusuna yapıştırın. 

    örneğin: **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alanına yazın brittasimon@yourcompanydomain.extension . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Sorgbatra BPM Suite 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **sorgtetra BPM Suite**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Sorgtetra BPM Suite**' i seçin.

    ![Uygulamalar listesindeki Sorgtetra BPM Suite bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-questetra-bpm-suite-test-user"></a>Sorgtetra BPM Suite test kullanıcısı oluşturma

Bu bölümün amacı, Sorgtetra BPM Suite 'te Britta Simon adlı bir Kullanıcı oluşturmaktır.

**Sorgtetra BPM Suite 'te Britta Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

1. Sorgıntra BPM Suite şirket sitenizde yönetici olarak oturum açın.

2. **Yeni kullanıcı > Kullanıcı listesi > sistem ayarları**' na gidin.
 
3. Yeni Kullanıcı iletişim kutusunda aşağıdaki adımları gerçekleştirin: 
   
    ![Test kullanıcısı oluştur][300] 
   
    a. **Ad** metin kutusuna kullanıcının **adını** yazın britta.simon@contoso.com .
   
    b. **E-posta** metin kutusuna kullanıcının **e-postasını** yazın britta.simon@contoso.com .
   
    c. **Parola** metin kutusuna kullanıcının **parolasını** yazın.
    
    d. **Yeni Kullanıcı Ekle**' ye tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Sorgtetra BPM Suite kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Sorgtetra BPM Suite 'e otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png