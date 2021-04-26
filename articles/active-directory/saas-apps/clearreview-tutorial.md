---
title: 'Öğretici: temiz Inceleme ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve gözden geçirme arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: e971b02fb87440d4833c2eeba8897143f97a20dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97670590"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Öğretici: temiz Inceleme ile Azure Active Directory tümleştirme

Bu öğreticide, temiz Incelemeyi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Azure AD ile açık Incelemeyi tümleştirmek aşağıdaki avantajları sağlar:

* Gözden geçirmeyi temizle erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla gözden geçirmeyi (çoklu oturum açma) temizlemek için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini açık gözden geçirmeyle yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Çoklu oturum açma etkin aboneliğini gözden geçirmeyi temizle

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Gözden geçirmeyi Temizle **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-clear-review-from-the-gallery"></a>Galeriden temiz Inceleme ekleme

Temiz gözden geçirmeyi Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize açık gözden geçirme eklemeniz gerekir.

**Galeriden gözden geçirmeyi açık olarak eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **gözden geçirmeyi temizle** yazın, sonuç panelinden **gözden geçirmeyi temizle** ' yi seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuçlar listesinde gözden geçirmeyi temizle](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre açık gözden geçirimiyle yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bağlantıyı açık gözden geçirme ' de kurulması gerekir.

Azure AD çoklu oturum açma 'yı açık gözden Geçirimiyle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. , Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Tekli oturum açmayı Temizle ' ye bakın](#configure-clear-review-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan açık gözden geçirme bölümünde Britta Simon 'ın bir karşılığı olacak şekilde **[Gözden geçirme testi kullanıcı oluştur](#create-clear-review-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Gözden geçirmeyi temizle ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), Uygulama tümleştirmesini **gözden geçirmeyi temizle** sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü; tanımlayıcı girebileceğiniz, yanıt U R L ve Kaydet ' i seçebileceğiniz temel SAML yapılandırmasını gösterir.](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<customer name>.clearreview.com/sso/metadata/`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<customer name>.clearreview.com/sso/acs/`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<customer name>.clearreview.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için bkz. [istemci desteği ekibini inceleyin](https://clearreview.com/contact/) . Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. Gözden geçirme uygulamasını temizle, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName** ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. Gözden geçirme uygulamasını Temizle **NameIdentifier** 'ın **User. Mail** ile eşlenmesini bekler, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

7. **Kullanıcı öznitelikleri & talepler** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    a. **Ad tanımlayıcı değerinin** sağ tarafındaki **Düzenle simgesine** tıklayın.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan talepleri & Kullanıcı özniteliklerini gösterir.](./media/clearreview-tutorial/attribute02.png)

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı taleplerini Yönet iletişim kutusunu gösterir.](./media/clearreview-tutorial/attribute01.png)

    b. **Kaynak özniteliği** listesinde, bu satır için **User. Mail** öznitelik değerini seçin.

    c. **Kaydet**’e tıklayın.

8. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

9. **Gözden geçirmeyi temizle bölümünü ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-clear-review-single-sign-on"></a>Açık gözden geçirmeyi tek Sign-On Yapılandır

1. **Gözden geçirmeyi temizle** tarafında çoklu oturum açmayı yapılandırmak için yönetici kimlik bilgileriyle açık **Gözden geçirme** portalını açın.

2. Sol gezinmede **yönetici** ' yi seçin.

    ![Ekran görüntüsünde, yönetici seçiliyken onay Incelemesi portalını gösterir.](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. Sayfanın alt kısmındaki **tümleştirmeler** bölümünde, **tek Sign-On ayarlarının** sağında bulunan **Değiştir** düğmesine tıklayın.

    ![Ekran görüntüsü, tek Sign-On değişiklik düğmesini gösterir.](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. **Tek Sign-On ayarları** sayfasında aşağıdaki adımları gerçekleştirin

    ![Ekran görüntüsü, bu adımda bilgileri girebileceğiniz tek Sign-On ayarları sayfasını gösterir.](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. **Veren URL** metin kutusuna, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    b. **SAML uç noktası** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.  

    c. **SLO uç noktası** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.  

    d. İndirilen sertifikayı not defteri 'nde açın ve içeriği **X. 509.440 sertifika** metin kutusuna yapıştırın.   

    e. **Kaydet**’e tıklayın.

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

Bu bölümde, gözden geçirmeyi Temizle ' ye erişim vererek Britta Simon 'u Azure çoklu oturum açma özelliğini kullanmaya etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **gözden geçirmeyi temizle**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **gözden geçirmeyi temizle**' yi seçin.

    ![Uygulamalar listesindeki gözden geçirmeyi temizle bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-clear-review-test-user"></a>Açık gözden geçirme testi Kullanıcı Oluştur

Bu bölümde, net gözden geçirme sırasında Britta Simon adlı bir Kullanıcı oluşturacaksınız. Açık gözden geçirme platformunda kullanıcıları eklemek için lütfen [Açık İnceleme destek ekibi](https://clearreview.com/contact/) ile çalışın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde gözden geçirmeyi temizle kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız açık gözden geçirmede otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)