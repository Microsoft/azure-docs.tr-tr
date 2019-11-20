---
title: 'Öğretici: Recognize ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Recognize arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943337"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Öğretici: Tanıma Azure Active Directory tümleştirme

Bu öğreticide, tanılamayı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Tanılamayı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Tanımak için erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla (çoklu oturum açma) tanımak üzere otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınız bir merkezi konumda - Azure portalında yönetebilir.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini tanıma ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Çoklu oturum açma etkin aboneliğini tanıma

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Recognize, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-recognize-from-the-gallery"></a>Galeriden tanıma ekleme

Tanılamayı Azure AD 'ye yönelik olarak yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize tanıma eklemeniz gerekir.

**Galeriden tanıma eklemek için aşağıdaki adımları uygulayın:**

1. İçinde **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde tıklayın **Azure Active Directory** simgesi.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için tıklatın **yeni uygulama** iletişim üst kısmındaki düğmesi.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Recognize**yazın, sonuç panelinden **Recognize** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde tanı](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre tanıma ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve tanılamada ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı tanıma ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açmayı yapılandırmayı](#configure-azure-ad-single-sign-on)**  - bu özelliği kullanmak, kullanıcılarınızın etkinleştirmek için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Tekli oturum açmayı tanıma yapılandırma](#configure-recognize-single-sign-on)** .
3. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  - Azure AD çoklu oturum açma Britta Simon ile test etmek için.
4. **[Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user)**  - Azure AD çoklu oturum açmayı kullanmak Britta Simon etkinleştirmek için.
5. Kullanıcı tarafından Azure AD gösterimine bağlı olan, tanıma göre bir Britta Simon 'ın karşılığına sahip olmak için **[Kullanıcı tanıma testi oluşturun](#create-recognize-test-user)** .
6. **[Çoklu oturum açmayı test](#test-single-sign-on)**  - yapılandırma çalışıp çalışmadığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı tanıma ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), Uygulama tümleştirmesini **tanı** sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısı yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **Düzenle** açmak için simgeyi **temel SAML yapılandırma** iletişim.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız**varsa, aşağıdaki adımları uygulayın:

    >[!NOTE]
    >**Hizmet sağlayıcı meta veri dosyasını** öğreticinin **tek oturum açma tanıma ' yı yapılandırma** bölümünden alacaksınız.

    a. Tıklayın **meta veri dosyasını karşıya yükleme**.

    ![Meta veri dosyasını yükleyin](common/upload-metadata.png)

    b. Tıklayarak **klasör logosu** meta veri dosyası seçin ve **karşıya**.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, temel SAML yapılandırması bölümünde **tanımlayıcı** değeri otomatik olarak doldurulur.

    ![Etki alanı ve URL 'Leri tanıma çoklu oturum açma bilgileri](common/sp-identifier.png)

     **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > **Tanımlayıcı** değeri otomatik olarak doldurulmazsa, bu tanımlayıcı değeri, daha sonra açıklanan SSO ayarları bölümünden Service Provider meta verileri URL 'sini açarak, öğreticide. Oturum açma URL 'SI değeri gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [istemci destek ekibine](mailto:support@recognizeapp.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Tanıması ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL'si:

    b. Azure AD Tanımlayıcısı

    c. Oturum Kapatma URL'si

### <a name="configure-recognize-single-sign-on"></a>Yapılandırma çoklu oturum açmayı tanıma

1. Farklı bir Web tarayıcısı penceresinde, tanı kiracınızda yönetici olarak oturum açın.

2. Sağ üst köşede **menü**' ye tıklayın. **Şirket Yöneticisi**' ne gidin.
   
    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/recognize-tutorial/tutorial_recognize_000.png)

3. Sol gezinti bölmesinde, **Ayarlar**' a tıklayın.
   
    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/recognize-tutorial/tutorial_recognize_001.png)

4. **SSO ayarları** bölümünde aşağıdaki adımları gerçekleştirin.
   
    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. **SSO 'Yu etkinleştirmek**için **Açık**seçeneğini belirleyin.

    b. **IDP VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.
    
    c. **SSO hedef URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
    
    d. **SLO hedefi URL** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın. 
    
    e. İndirilen **sertifika (base64)** dosyanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve **sertifika** metin kutusuna yapıştırın.
    
    f. **Ayarları Kaydet** düğmesine tıklayın. 

5. **SSO ayarları** bölümünün yanında, URL 'Yi **hizmet sağlayıcısı meta veri URL 'si**altına kopyalayın.
   
    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Meta veri belgesini indirmek için boş bir tarayıcı altındaki **metadata URL bağlantısını** açın. Sonra EntityDescriptor değerini (EntityId) dosyadan kopyalayın ve Azure portal **temel SAML yapılandırmasındaki** **tanımlayıcı** metin kutusuna yapıştırın.
    
    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Britta Simon adlı Azure portalında bir test kullanıcısı oluşturmaktır.

1. Azure portalında, sol bölmede seçin **Azure Active Directory**seçin **kullanıcılar**ve ardından **tüm kullanıcılar**.

    !["Kullanıcılar ve Gruplar" ve "Tüm kullanıcılar" bağlantıları](common/users.png)

2. Seçin **yeni kullanıcı** ekranın üstünde.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı özellikleri, aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanına yazın brittasimon@yourcompanydomain.extension. Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, tanıma erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **tanı**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **tanı**' yı seçin.

    ![Uygulamalar listesindeki tanıma bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. İçinde **kullanıcılar ve gruplar** iletişim kutusunda **Britta Simon** 'a tıklayın kullanıcı listesinde **seçin** ekranın alt kısmındaki düğmesi.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-recognize-test-user"></a>Tanı testi kullanıcısı oluştur

Azure AD kullanıcılarının tanımasına oturum açmasını sağlamak için, bunların tanınması için sağlanması gerekir. Tanı söz konusu olduğunda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

Bu uygulama SCıM sağlamasını desteklemez, ancak kullanıcıları sağlayan alternatif bir Kullanıcı eşitlemesi vardır. 

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Tanıma şirket sitenizde yönetici olarak oturum açın.

2. Sağ üst köşede **menü**' ye tıklayın. **Şirket Yöneticisi**' ne gidin.

3. Sol gezinti bölmesinde, **Ayarlar**' a tıklayın.

4. **Kullanıcı eşitleme** bölümünde aşağıdaki adımları gerçekleştirin.
   
    ![Yeni Kullanıcı](./media/recognize-tutorial/tutorial_recognize_005.png "Yeni Kullanıcı")
   
    a. **Eşitleme etkinken** **Açık**' ı seçin.
   
    b. **Eşitleme sağlayıcısını Seç**' i seçin ve **Microsoft/Office 365**' ı seçin.
   
    c. **Kullanıcı eşitlemesini Çalıştır**' a tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde tanıma kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız tanıma göre otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

