---
title: 'Öğretici: Recognize ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Recognize arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 7195a126938deabf542e6fa3e52b72c2efdddac2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515228"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Öğretici: tanıma sahip Azure Active Directory tümleştirme

Bu öğreticide, tanılamayı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Tanılamayı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Tanımak için erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla (çoklu oturum açma) tanımak üzere otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
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

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Recognize** yazın, sonuç panelinden **Recognize** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde tanı](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre tanıma ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve tanılamada ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı tanıma ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Tekli oturum açmayı tanıma yapılandırma](#configure-recognize-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcı tarafından Azure AD gösterimine bağlı olan, tanıma göre bir Britta Simon 'ın karşılığına sahip olmak için **[Kullanıcı tanıma testi oluşturun](#create-recognize-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı tanıma ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), Uygulama tümleştirmesini **tanı** sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa, aşağıdaki adımları uygulayın:

    >[!NOTE]
    >**Hizmet sağlayıcı meta veri dosyasını** öğreticinin **tek oturum açma tanıma ' yı yapılandırma** bölümünden alacaksınız.

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, temel SAML yapılandırması bölümünde **tanımlayıcı** değeri otomatik olarak doldurulur.

    ![Etki alanı ve URL 'Leri tanıma çoklu oturum açma bilgileri](common/sp-identifier.png)

     **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > **Tanımlayıcı** değeri otomatik olarak doldurulmazsa, öğreticinin **yapılandırma tek oturum açma** bölümünde açıklanan SSO ayarları bölümünden Service Provider meta veri URL 'sini açarak tanımlayıcı değeri alırsınız. Oturum açma URL 'SI değeri gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [istemci destek ekibine](mailto:support@recognizeapp.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Tanıması ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-recognize-single-sign-on"></a>Yapılandırma tek Sign-On tanıma

1. Farklı bir Web tarayıcısı penceresinde, tanı kiracınızda yönetici olarak oturum açın.

2. Sağ üst köşede **menü**' ye tıklayın. **Şirket Yöneticisi**' ne gidin.
   
    ![Ekran görüntüsü, Ayarlar menüsünde şirket yöneticisinin seçili olduğunu gösterir.](./media/recognize-tutorial/tutorial_recognize_000.png)

3. Sol gezinti bölmesinde, **Ayarlar**' a tıklayın.
   
    ![Ekran görüntüsü, gezinti sayfasından seçilen ayarları gösterir.](./media/recognize-tutorial/tutorial_recognize_001.png)

4. **SSO ayarları** bölümünde aşağıdaki adımları gerçekleştirin.
   
    ![Ekran görüntüsü, tanımlanan değerleri girebileceğiniz, S S O ayarlarını gösterir.](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. **SSO 'Yu etkinleştirmek** için **Açık** seçeneğini belirleyin.

    b. **IDP VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.
    
    c. **SSO hedef URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.
    
    d. **SLO hedefi URL** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın. 
    
    e. İndirilen **sertifika (base64)** dosyanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve **sertifika** metin kutusuna yapıştırın.
    
    f. **Ayarları Kaydet** düğmesine tıklayın. 

5. **SSO ayarları** bölümünün yanında, URL 'Yi **hizmet sağlayıcısı meta veri URL 'si** altına kopyalayın.
   
    ![Ekran görüntüsünde, hizmet sağlayıcısı meta verilerini kopyalayabileceğiniz notlar gösterilir.](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Meta veri belgesini indirmek için boş bir tarayıcı altındaki **metadata URL bağlantısını** açın. Sonra EntityDescriptor değerini (EntityId) dosyadan kopyalayın ve Azure portal **temel SAML yapılandırmasındaki** **tanımlayıcı** metin kutusuna yapıştırın.
    
    ![Ekran görüntüsü, b varlığını alabileceğiniz düz metin X M L olan bir metin kutusunu gösterir.](./media/recognize-tutorial/tutorial_recognize_004.png)

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

Bu bölümde, tanıma erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **tanı**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **tanı**' yı seçin.

    ![Uygulamalar listesindeki tanıma bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-recognize-test-user&quot;></a>Tanı testi kullanıcısı oluştur

Azure AD kullanıcılarının tanımasına oturum açmasını sağlamak için, bunların tanınması için sağlanması gerekir. Tanı söz konusu olduğunda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

Bu uygulama SCıM sağlamasını desteklemez, ancak kullanıcıları sağlayan alternatif bir Kullanıcı eşitlemesi vardır. 

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Tanıma şirket sitenizde yönetici olarak oturum açın.

2. Sağ üst köşede **menü**' ye tıklayın. **Şirket Yöneticisi**' ne gidin.

3. Sol gezinti bölmesinde, **Ayarlar**' a tıklayın.

4. **Kullanıcı eşitleme** bölümünde aşağıdaki adımları gerçekleştirin.
   
    ![Yeni Kullanıcı](./media/recognize-tutorial/tutorial_recognize_005.png &quot;Yeni Kullanıcı")
   
    a. **Eşitleme etkinken** **Açık**' ı seçin.
   
    b. **Eşitleme sağlayıcısını Seç**' i seçin ve **Microsoft/Office 365**' ı seçin.
   
    c. **Kullanıcı eşitlemesini Çalıştır**' a tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde tanıma kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız tanıma göre otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)