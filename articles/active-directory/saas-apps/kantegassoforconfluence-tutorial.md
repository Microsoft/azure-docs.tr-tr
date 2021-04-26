---
title: "Öğretici: Confluence için Kantega SSO 'SU ile Azure Active Directory tümleştirme | Microsoft Docs"
description: Confluence için Azure Active Directory ve Kantega SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: be86e04359c29696d208994d85d36b7740b60cc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646223"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Öğretici: Confluence için Kantega SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Confluence için Kantega SSO 'yu nasıl tümleştirileceğini öğreneceksiniz.
Azure AD ile, Kantega SSO 'yu Confluence için tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Confluence için Kantega SSO 'ya erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Confluence (çoklu oturum açma) için Kantega SSO 'ya otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Confluence için Azure AD tümleştirmesini Kantega SSO 'SU ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Confluence Single oturum açma özellikli abonelik için Kantega SSO

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Confluence için Kantega SSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Galeriden Confluence için Kantega SSO 'SU ekleme

Kantega SSO 'SU için Azure AD 'ye yönelik tümleştirmeyi yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Confluence için Kantega SSO 'SU eklemeniz gerekir.

**Galerinin Confluence için Kantega SSO 'SU eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **Confluence Için KANTEGA SSO** yazın, sonuç panelinden **KANTEGA SSO** ' yı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Confluence için Kantega SSO 'SU](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına dayalı olarak, Confluence Için Kantega SSO 'Su Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve Kantega SSO 'SU ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Yapılandırma için Kantega SSO ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını tamamlamanız gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Yapılandırma, uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, bir yandan **[Confluence Single Sign-on Için Kantega SSO 'Yu yapılandırın](#configure-kantega-sso-for-confluence-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Confluence test kullanıcısı Için Kantega SSO oluşturma](#create-kantega-sso-for-confluence-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Confluence Için Kantega SSO 'Da Britta Simon 'un bir karşılığı.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Yapılandırma için Kantega SSO ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Confluence uygulama tümleştirmesi Için KANTEGA SSO** sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    !["Tanımlayıcı" ve "Yanıt U R L" alanları vurgulanmış ve "Kaydet" düğmesi seçili olan "temel S A M L yapılandırma" bölümünü gösteren ekran görüntüsü.](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Confluence etki alanı ve URL 'Ler çoklu oturum açma bilgileri için Kantega SSO](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'siyle güncelleştirin. Bu değerler, Öğreticinin ilerleyen kısımlarında açıklanan Confluence eklentisinin yapılandırması sırasında alınır.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **Confluence Için Kantega SSO ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Confluence Single Sign-On için Kantega SSO 'yu yapılandırın

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak **Confluence yönetici portalınızda** oturum açın.

1. Dişli üzerine gelin ve **eklentilere** tıklayın.

    !["COG" menü simgesini ve "Eklentiler" seçili olduğunu gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon1.png)

1. **Atlasme marketi** sekmesinde **yeni eklentiler bul**' a tıklayın.

    !["Yeni eklentiler bul" seçiliyken "ATTLASSIAN Market" sekmesini gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon.png)

1. **Confluence SAML Kerberos Için Kantega SSO** 'yu arayın ve yeni SAML **eklentisini yüklemek için Kaldır düğmesine tıklayın** .

    ![Arama kutusunda ve "Install" düğmesinin seçili olduğu "yeni eklenti bul" sayfasını, "bir M L-of Confluence s A M L Kerberos" ile gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Eklenti yüklemesi başlar.

    !["Yükleme" eklentisini gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Yükleme tamamlandıktan sonra. **Kapat**’a tıklayın.

    !["Kapat" eylemi seçiliyken "yüklenecek ve hazırlanmaya başla" ekranını gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon33.png)

1. **Yönet**'e tıklayın.

    !["Yönet" düğmesi seçiliyken "Kantega çoklu oturum açmayı Kerberos ve S A M L" eklentisine gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Yeni eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın.

    !["Yapılandır" düğmesinin seçili olduğu "Kantega çoklu oturum açmayı Kerberos ve S A M L ile birlikte gösteren ekran görüntüsü" sayfası.](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Bu yeni eklenti Ayrıca, **kullanıcılar & güvenlik** sekmesi altında bulunabilir.

    !["Kantega çoklu oturum açma" eylemi seçiliyken "KULLANıCıLAR & GÜVENLIĞI" sekmesini gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon36.png)

1. **SAML** bölümünde. **Kimlik sağlayıcısı ekle** açılır listesinden **Azure ACTIVE DIRECTORY (Azure AD)** öğesini seçin.

    !["Kimlik sağlayıcısı ekle" ve "Azure Active Directory (Azure AD)" seçiliyken "S A M L" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Abonelik düzeyini **temel** olarak seçin.

    !["Temel" seçiliyken "Azure AD 'yi hazırlama" sayfasını gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon5.png)

1. **Uygulama özellikleri** bölümünde aşağıdaki adımları gerçekleştirin:

    !["App ı D U R L" alanı ve "Kopyala" düğmesinin vurgulandığına ve "Ileri" düğmesine seçili "uygulama özellikleri" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. **Uygulama KIMLIĞI URI** değerini kopyalayın ve Azure Portal IÇINDEKI **temel SAML yapılandırması** bölümünde **kimlik, yanıt URL 'si ve Sign-On URL 'si** olarak kullanın.

    b. **İleri**’ye tıklayın.

1. **Meta veri içeri aktarma** bölümünde aşağıdaki adımları gerçekleştirin: 

    !["Bilgisayarımdaki meta veri dosyası" bölümünün seçildiği "meta veri alma" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Bilgisayarımdaki **meta veri dosyasını** seçin ve Azure Portal 'ten indirdiğiniz meta veri dosyasını karşıya yükleyin.

    b. **İleri**’ye tıklayın.

1. **Ad ve SSO konumu** bölümünde aşağıdaki adımları gerçekleştirin:

    !["Kimlik sağlayıcı adı" metin kutusu vurgulanmış ve "Ileri" düğmesi seçili olan "ad ve S S O konumunu" gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Kimlik sağlayıcısı **adı** metin kutusuna kimlik sağlayıcısının adını ekleyin (ör. Azure AD).

    b. **İleri**’ye tıklayın.

1. Imzalama sertifikasını doğrulayın ve **İleri**' ye tıklayın.

    !["Ileri" düğmesi seçili "Imza doğrulama" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon9.png)

1. **Confluence Kullanıcı hesapları** bölümünde aşağıdaki adımları gerçekleştirin:

    !["Confluence 'in Iç dizininde kullanıcı oluştur" seçeneğine ve "Ileri" düğmesine seçili "," Confluence User Accounts "bölümünü gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. **Gerekirse, Confluence 'ın Iç dizininde kullanıcı oluştur** ' u seçin ve Kullanıcı için grubun uygun adını girin (birden çok No olabilir. virgülle ayrılmış gruplar).

    b. **İleri**’ye tıklayın.

1. **Finish (Son)** düğmesine tıklayın.

    !["Son" düğmesinin seçili olduğu "Özet" sayfasının ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon11.png)

1. **Azure AD Için bilinen etki alanları** bölümünde aşağıdaki adımları uygulayın: 

    !["Bilinen etki alanları" metin kutusu vurgulanmış ve "Kaydet" düğmesi seçili olan "Azure AD için bilinen etki alanları" sayfasını gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/addon12.png)

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

Bu bölümde, Confluence için Kantega SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Confluence için Kantega SSO**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Confluence Için Kantega SSO**' yı seçin.

    ![Uygulamalar listesindeki Confluence bağlantısı için Kantega SSO](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Confluence test kullanıcısı için Kantega SSO oluştur

Azure AD kullanıcılarının, Confluence 'de oturum açmasını sağlamak için, bunların Confluence olarak sağlanması gerekir. Confluence için Kantega SSO durumunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Yönetici olarak Confluence Şirket sitesi için Kantega SSO 'ınız üzerinde oturum açın.

1. Dişli 'ye gelin ve **Kullanıcı yönetimine** tıklayın.

    !["COG" simgesini ve "Kullanıcı Yönetimi" ' nin seçili olduğunu gösteren ekran görüntüsü.](./media/kantegassoforconfluence-tutorial/user1.png)

1. Kullanıcılar bölümünde, **Kullanıcı Ekle** sekmesini tıklatın. **Kullanıcı Ekle** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Çalışan Ekle](./media/kantegassoforconfluence-tutorial/user2.png)

    a. Kullanıcı **adı** metin kutusuna, gibi kullanıcının e-postasını yazın Brittasimon@contoso.com .

    b. **Tam ad** metin kutusuna, Britta Simon gibi kullanıcının tam adını yazın.

    c. **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini yazın Brittasimon@contoso.com .

    d. **Parola** metin kutusuna kullanıcı parolasını yazın.

    e. Parolayı **Onayla** ' ya tıklayarak parolayı yeniden girin.

    f. **Ekle** düğmesine tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Confluence için Kantega SSO 'SU ' ne tıkladığınızda, SSO 'yu ayarladığınız Confluence için Kantega SSO 'ya otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)