---
title: "Öğretici: Bitbucket için Kantega SSO 'SU ile Azure Active Directory tümleştirme | Microsoft Docs"
description: Bitbucket için Azure Active Directory ve Kantega SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 52879eb7cb7a9d90113971aa66c590b99b2d5e88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459297"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Öğretici: Bitbucket için Kantega SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Bitbucket için Kantega SSO 'yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Bitbucket için Kantega SSO 'yu Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Bitbucket için Kantega SSO 'ya erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Bitbucket (çoklu oturum açma) için Kantega SSO 'ya otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bitbucket için Kantega SSO 'SU ile Azure AD tümleştirmesini yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Bitbucket çoklu oturum açma özellikli abonelik için Kantega SSO

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Bitbucket için Kantega SSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Galeriden Bitbucket için Kantega SSO 'SU ekleme

Bitbucket için Kantega SSO 'SU ile Azure AD arasında tümleştirmeyi yapılandırmak için, galerideki Bitbucket için Kantega SSO 'SU ile yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden Bitbucket için Kantega SSO eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Ara kutusuna, **Bitbucket Için KANTEGA SSO** yazın, sonuç panelinden **KANTEGA SSO** ' yı seçin, sonra da uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesindeki Bitbucket için Kantega SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına göre Bitbucket Için Kantega SSO 'Su Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve BitBucket için Kantega SSO 'SU içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Bitbucket için Kantega SSO 'SU ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Bit demeti çoklu oturum açma Için Kantega SSO](#configure-kantega-sso-for-bitbucket-single-sign-on)** 'yu, uygulama tarafında tek Sign-On ayarlarını yapılandırmak için yapılandırın.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Bitbucket test kullanıcısı Için Kantega SSO oluşturma](#create-kantega-sso-for-bitbucket-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Bitbucket Için Kantega SSO 'Da Britta Simon 'un bir karşılığı olmalıdır.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Bitbucket için Kantega SSO 'SU ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Bitbucket Için KANTEGA SSO** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü; tanımlayıcı girebileceğiniz, yanıt U R L ve Kaydet ' i seçebileceğiniz temel SAML yapılandırmasını gösterir.](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Ekran görüntüsü, U R L 'ye bir Işaret girebileceğiniz ek U R 'Leri ayarlamayı gösterir.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'siyle güncelleştirin. Bu değerler, Öğreticinin ilerleyen kısımlarında açıklanan Bitbucket eklentisinin yapılandırması sırasında alınır.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **Bitbucket Için Kantega SSO ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Bitbucket tek Sign-On için Kantega SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Bitbucket yönetici portalınızdaki yönetici olarak oturum açın.

1. Dişli ' ye tıklayın ve **yeni eklentiler bul**' a tıklayın.

    ![Ekran görüntüsü, yeni eklentiler bul seçiliyken BitBucket yönetimini gösterir.](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. **Bit DEMETI SAML & Kerberos Için Kantega SSO** 'yu arayın ve yeni SAML **eklentisini yüklemek için** , Kaldır düğmesine tıklayın.

    ![Ekran görüntüsü Bitbucket SAML & Kerberos için Kantega SSO 'yu yükler seçeneğiyle gösterir.](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Eklenti yüklemesi başlar.

    ![Ekran görüntüsü yüklemenin ilerlemesini gösterir.](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Yükleme tamamlandıktan sonra. **Kapat**’a tıklayın.

    ![Ekran görüntüsü Kapat düğmesini gösterir.](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. **Yönet**'e tıklayın.

    ![Ekran görüntüsü Yönet düğmesini gösterir.](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Yeni eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın.

    ![Ekran görüntüsü, Kullanıcı tarafından yüklenen eklentileri Yapılandır seçiliyken gösterir.](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. **SAML** bölümünde. **Kimlik sağlayıcısı ekle** açılır listesinden **Azure ACTIVE DIRECTORY (Azure AD)** öğesini seçin.

    ![Ekran görüntüsünde, kimlik sağlayıcısı olarak seçili Azure A ile Kantega tek Sign-On gösterilmektedir.](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Abonelik düzeyini **temel** olarak seçin.

    ![Ekran görüntüsü, temel seçili bir D ile Azure 'u hazırlar.](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. **Uygulama özellikleri** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımdaki bilgileri sağlayabileceğiniz uygulama özellikleri bölümünü gösterir.](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. **Uygulama KIMLIĞI URI** değerini kopyalayın ve Azure Portal IÇINDEKI **temel SAML yapılandırması** bölümünde **kimlik, yanıt URL 'si ve Sign-On URL 'si** olarak kullanın.

    b. **İleri**’ye tıklayın.

1. **Meta veri içeri aktarma** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, meta veri içeri aktarma bölümünü gösterir, burada bir meta veri dosyasına gözatabileceğiniz.](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Bilgisayarımdaki **meta veri dosyasını** seçin ve Azure Portal 'ten indirdiğiniz meta veri dosyasını karşıya yükleyin.

    b. **İleri**’ye tıklayın.

1. **Ad ve SSO konumu** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsünde, Azure A D 'nin kimlik sağlayıcısı adı olduğu ad ve S S O konumu gösterilir.](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Kimlik sağlayıcısı **adı** metin kutusuna kimlik sağlayıcısının adını ekleyin (ör. Azure AD).

    b. **İleri**’ye tıklayın.

1. Imzalama sertifikasını doğrulayın ve **İleri**' ye tıklayın.

    ![Ekran görüntüsünde Imza doğrulaması gösterilmektedir.](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. **Bitbucket Kullanıcı hesapları** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, Kullanıcı oluşturma seçeneğiniz olan BitBucket Kullanıcı hesaplarını gösterir.](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. **Gerekirse Bitbucket 'ın Iç dizininde kullanıcı oluştur** ' u seçin ve Kullanıcı için grubun uygun adını girin (birden çok No olabilir. virgülle ayrılmış gruplar).

    b. **İleri**’ye tıklayın.

1. **Finish (Son)** düğmesine tıklayın.

    ![Ekran görüntüsü Özet sayfasını gösterir.](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. **Azure AD Için bilinen etki alanları** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımları gerçekleştirebileceğiniz Azure A 'nın bilinen etki alanlarını gösterir.](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Sayfanın sol panelinden **bilinen etki alanları ' nı** seçin.

    b. **Bilinen etki alanları** metin kutusuna etki alanı adını girin.

    c. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alan türü`brittasimon@yourcompanydomain.extension`  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Bitbucket için Kantega SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Bitbucket için Kantega SSO**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Bitbucket Için Kantega SSO**' yı seçin.

    ![Uygulamalar listesindeki Bitbucket için Kantega SSO bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Bitbucket test kullanıcısı için Kantega SSO oluştur

Azure AD kullanıcılarının Bitbucket 'da oturum açmasını sağlamak için, Bitbucket 'a sağlanması gerekir. Bitbucket için Kantega SSO olması durumunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Bitbucket şirket sitenizde yönetici olarak oturum açın.

1. Ayarlar simgesine tıklayın.

    ![Ekran görüntüsü ayarlar simgesini gösterir.](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. **Yönetim** sekmesi bölümünde **Kullanıcılar**' a tıklayın.

    ![Ekran görüntüsü, kullanıcılar seçiliyken BitBucket yönetimini gösterir. ](./media/kantegassoforbitbucket-tutorial/user2.png)

1. **Kullanıcı oluştur**' a tıklayın.

    ![Ekran görüntüsü, Kullanıcı Oluştur seçiliyken BitBucket yönetimini gösterir.](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. **Kullanıcı oluştur** iletişim sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımları gerçekleştirebileceğiniz Kullanıcı Oluştur iletişim kutusunu gösterir.](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Kullanıcı **adı** metin kutusuna, gibi kullanıcının e-postasını yazın Brittasimon@contoso.com .

    b. **Tam ad** metin kutusuna, Britta Simon gibi kullanıcının tam adını yazın.

    c. **E-posta adresi** metin kutusuna, gibi kullanıcının e-posta adresini yazın Brittasimon@contoso.com .

    d. **Parola** metin kutusuna kullanıcının parolasını yazın.

    e. **Parolayı Onayla** metin kutusuna kullanıcı parolasını yeniden girin.

    f. **Kullanıcı oluştur**' a tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Bitbucket için Kantega SSO kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Bitbucket için Kantega SSO 'SU için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)