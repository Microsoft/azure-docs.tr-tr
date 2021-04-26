---
title: 'Öğretici: Cezanne HR yazılımıyla tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Cezanne HR yazılımı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 0aa0dab7b512c85fbbdf374c962e6ee8e1c7d616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456303"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Öğretici: Cezanne HR yazılımıyla tümleştirme Azure Active Directory

Bu öğreticide, Cezanne HR yazılımını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Cezanne HR yazılımını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Cezanne HR yazılımına erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Cezanne ık Software (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Cezanne HR yazılımıyla yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Cezanne HR Software çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Cezanne HR Software, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Galeriden Cezanne ık yazılım ekleme

Cezanne HR yazılımının tümleştirmesini Azure AD ile yapılandırmak için, Galeriden Cezanne ık yazılımı, yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden Cezanne HR yazılımı eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **CEZANNE HR Software** yazın, sonuç PANELINDEN **Cezanne HR Software** ' i seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Cezanne ık Software for the results List](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına bağlı olarak Azure AD çoklu oturum açma 'Yı Cezanne HR yazılımıyla yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Cezanne HR Software ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı Cezanne HR yazılımıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[CEZANNE HR yazılımını çoklu oturum açmayı yapılandırın](#configure-cezanne-hr-software-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Cezanne ık yazılımında Britta Simon 'a sahip olmak için **[CEZANNE ık yazılım test kullanıcısı oluşturun](#create-cezanne-hr-software-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Cezanne HR yazılımıyla yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Cezanne HR yazılım** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Cezanne HR yazılım etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [CEZANNE HR yazılım istemci destek ekibine](https://cezannehr.com/services/support/) başvurun.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **CEZANNE ık yazılımını ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Cezanne ık yazılım tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Cezanne ık yazılım kiracınızda yönetici olarak oturum açın.

2. Sol gezinti bölmesinde **sistem kurulumu**' na tıklayın. **Güvenlik ayarları**' na gidin. Sonra **tek Sign-On yapılandırmaya** gidin.

    ![Ekran görüntüsünde, güvenlik ayarları ve tek Sign-On yapılandırması seçili olan Cezanne H R yazılım kiracısı gösterilmektedir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. **Kullanıcıların şu tek Sign-On (SSO) hizmet panelini kullanarak oturum açmasına Izin ver** ' de, **SAML 2,0** kutusunu işaretleyin ve **Gelişmiş yapılandırma** seçeneğini belirleyin.

    ![Ekran görüntüsü SAML 2,0 ve gelişmiş yapılandırma seçiliyken kullanıcılara Izin ver bölmesinin seçili olduğunu gösterir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. **Yeni Ekle** düğmesine tıklayın.

    ![Ekran görüntüsünde yeni Ekle düğmesi gösterilir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. **SAML 2,0 KIMLIK sağlayıcıları** bölümünde aşağıdaki adımları gerçekleştirin.

    ![Ekran görüntüsü, bu adımda açıklanan değerleri girebileceğiniz bir bölme gösterir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. **Görünen ad** olarak kimlik sağlayıcınızın adını girin.

    b. **Varlık tanımlayıcı** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın.

    c. **SAML bağlamasını** ' Post ' olarak değiştirin.

    d. **Güvenlik belirteci hizmeti uç noktası** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. Kullanıcı KIMLIĞI öznitelik adı metin kutusuna yazın `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    f. Azure portal indirilen sertifikayı karşıya yüklemek için **karşıya yükle** simgesine tıklayın.

    örneğin: **Tamam** düğmesine tıklayın.

6. **Kaydet** düğmesine tıklayın.

    ![Ekran görüntüsünde çoklu oturum açma yapılandırması için Kaydet düğmesi gösterilir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

Bu bölümde, Cezanne ık yazılımlarına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı SEÇIN ve ardından **Cezanne HR Software**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **CEZANNE HR Software**' i seçin.

    ![Uygulamalar listesindeki Cezanne HR yazılım bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-cezanne-hr-software-test-user"></a>Cezanne HR yazılım test kullanıcısı oluşturma

Azure AD kullanıcılarının Cezanne HR yazılımında oturum açmasını sağlamak için, Cezanne HR yazılımlarına sağlanması gerekir. Cezanne ık yazılım durumunda sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Cezanne ık Software Şirket sitenizde yönetici olarak oturum açın.

2. Sol gezinti bölmesinde **sistem kurulumu**' na tıklayın. **Kullanıcıları Yönet**' e gidin. Ardından **Yeni Kullanıcı Ekle**' ye gidin.

    ![Ekran görüntüsü, kullanıcıları Yönet ve Yeni Kullanıcı Ekle seçiliyken Cezanne H R yazılım kiracısını gösterir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Yeni Kullanıcı")

3. **Kışı ayrıntıları** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımda açıklanan değerleri girebileceğiniz kışı ayrıntıları bölümünü gösterir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Yeni Kullanıcı")

    a. **Iç KULLANıCıYı** kapalı olarak ayarlayın.

    b. **Ad** metin kutusuna, Ilk Kullanıcı adını **Britta** gibi yazın.  

    c. **Soyadı** metin kutusunda, **Simon** gibi kullanıcı adının soyadını yazın.

    d. **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini yazın Brittasimon@contoso.com .

4. **Hesap bilgileri** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımda açıklanan değerleri girebileceğiniz hesap BILGILERINI gösterir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Yeni Kullanıcı")

    a. Kullanıcı **adı** metin kutusuna, gibi kullanıcının e-postasını yazın Brittasimon@contoso.com .

    b. **Parola** metin kutusuna kullanıcının parolasını yazın.

    c. **Güvenlik rolü** olarak **İK Professional** ' ı seçin.

    d. **Tamam**'a tıklayın.

5. **Çoklu oturum açma** sekmesine gidin ve **SAML 2,0 tanımlayıcıları** alanında **Yeni Ekle** ' yi seçin.

    ![Ekran görüntüsü, yeni Ekle ' yi seçebileceğiniz tek Sign-On sekmesini gösterir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Kullanıcı")

6. **Kimlik sağlayıcısı** Için kimlik sağlayıcınızı seçin ve **Kullanıcı tanımlayıcısı** metin kutusunda Britta Simon hesabının e-posta adresini girin.

    ![Ekran görüntüsü, kimlik sağlayıcınızı ve Kullanıcı tanımlayıcıyı seçebileceğiniz SAML 2,0 tanımlayıcılarını gösterir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Kullanıcı")

7. **Kaydet** düğmesine tıklayın.

    ![Ekran görüntüsü Kullanıcı ayarları için Kaydet düğmesini gösterir.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Kullanıcı")

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Cezanne HR yazılım kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Cezanne HR yazılımında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)