---
title: 'Öğretici Azure Active Directory: Fheye/CRUCIBLE için Kantega SSO ile tümleştirme | Microsoft Docs'
description: Fheye/CRUCIBLE için Azure Active Directory ile Kantega SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 06a4e8aa1ad74f47526f3a39931632953bfaaec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459195"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Öğretici: Fheye/CRUCIBLE için Kantega SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Fheye/CRUCIBLE için Kantega SSO 'yu nasıl tümleştirileceğini öğreneceksiniz.
Azure AD ile Fheys/CRUCIBLE için Kantega SSO 'yu tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Fheye/CRUCIBLE için Kantega SSO 'ya erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Fheys/Crucible (çoklu oturum açma) için Kantega SSO 'ya otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Fheye/CRUCIBLE için Kantega SSO 'SU ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Fheys/Crucible çoklu oturum açma özellikli abonelik için Kantega SSO

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Fheye/CRUCIBLE için Kantega SSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Galeri 'den Fheye/CRUCIBLE için Kantega SSO 'SU ekleme

Tomheys/CRUCIBLE için Kantega SSO 'SU ile Azure AD arasında tümleştirmeyi yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Fheye/CRUCIBLE için Kantega SSO 'SU eklemeniz gerekir.

**Galerinin Fheye/CRUCIBLE için Kantega SSO 'SU eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Ara kutusuna, **fheye/CRUCIBLE Için Kantega SSO** yazın, sonuç panelinde **Kantega/CRUCIBLE Için KANTEGA SSO** ' yı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Sonuçlar listesinde Fheys/CRUCIBLE için Kantega SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına bağlı olarak Fheye/crucıya Için Kantega IÇIN Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ve Kantega/CRUCIBLE için ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı, Fheye/Cruto için Kantega SSO 'SU ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[Fheye/Crucible çoklu oturum açma Için Kantega SSO 'Yu yapılandırın](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Fheys/CRUCIBLE test kullanıcısı Için Kantega SSO 'Su oluşturun](#create-kantega-sso-for-fisheyecrucible-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan Fheye/CRUCIBLE Için Kantega SSO 'Da Britta Simon 'ın bir karşılığı olmalıdır.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Fheye/CRUCIBLE için Kantega SSO 'SU ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **fheys/CRUCIBLE uygulama tümleştirmesi Için KANTEGA SSO** sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    !["Tanımlayıcı" ve "Yanıt U R L" metin kutusu vurgulanmış ve "Kaydet" düğmesi seçili olan "temel S A M L yapılandırma" bölümünü gösteren ekran görüntüsü.](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Fheys/CRUCIBLE etki alanı ve URL 'Ler çoklu oturum açma bilgileri için Kantega SSO](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'siyle güncelleştirin. Bu değerler, Öğreticinin ilerleyen kısımlarında açıklanan Fheys/CRUCIBLE eklentisinin yapılandırması sırasında alınır.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

7. **Fheye/CRUCIBLE Için Tetega SSO 'Yu ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Fheye/Crucible tek Sign-On için Kantega SSO 'yu yapılandırın

1. Farklı bir Web tarayıcısı penceresinde, Fheys/CRUCIBLE şirket içi sunucunuzda yönetici olarak oturum açın.

1. Dişli üzerine gelin ve **eklentilere** tıklayın.

    !["COG" simgesini ve "eklentileri" seçili olduğunu gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. Sistem ayarları bölümünde **yeni eklentiler bul**' a tıklayın. 

    !["Yeni eklentiler bul" seçiliyken "sistem ayarları" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. **CRUCIBLE Için Kantega SSO** 'yu arayın ve yeni SAML eklentisini yüklemek için **Install** düğmesine tıklayın.

    ![Arama kutusuna ve "Install" düğmesine seçili "CRUCIBLE için" Kanvalar için Attlasian marketi "sayfasını gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. Eklenti yüklemesi başlar. 

    ![Eklenti için "yükleme" iletişim kutusunu gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. Yükleme tamamlandıktan sonra. **Kapat**’a tıklayın.

    !["Yüklenecek ve hazırlanıyor" iletişim kutusunu ve "Kapat" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  **Yönet**'e tıklayın.

    ![Bir M L & Kerberos "uygulama sayfasına ve" Yönet "düğmesine seçili olan" Kantega s S O "adlı ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Yeni eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın. 

    !["Kullanıcı tarafından yüklenen eklentiler" sayfasını ve "Yapılandır" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. **SAML** bölümünde. **Kimlik sağlayıcısı ekle** açılır listesinden **Azure ACTIVE DIRECTORY (Azure AD)** öğesini seçin.

    !["Kimlik sağlayıcısı ekle" açılan ve "Azure Active Directory (Azure AD)" seçiliyken "Eklentiler-Kantega çoklu oturum açma" sayfasını gösteren ekran görüntüsü. ](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Abonelik düzeyini **temel** olarak seçin.

    !["Temel" seçiliyken "Azure A D 'yi hazırlama" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. **Uygulama özellikleri** bölümünde aşağıdaki adımları gerçekleştirin:

    !["App ı D U R I" metin kutusu ve kopyala düğmesinin seçili olduğu "uygulama özellikleri" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. **Uygulama KIMLIĞI URI** değerini kopyalayın ve Azure Portal IÇINDEKI **temel SAML yapılandırması** bölümünde **kimlik, yanıt URL 'si ve Sign-On URL 'si** olarak kullanın.

    b. **İleri**’ye tıklayın.

1. **Meta veri içeri aktarma** bölümünde aşağıdaki adımları gerçekleştirin:

    !["Bilgisayarımdaki meta veri dosyası" bölümünün seçildiği "meta veri alma" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Bilgisayarımdaki **meta veri dosyasını** seçin ve Azure Portal 'ten indirdiğiniz meta veri dosyasını karşıya yükleyin.

    b. **İleri**’ye tıklayın.

1. **Ad ve SSO konumu** bölümünde aşağıdaki adımları gerçekleştirin:

    !["Kimlik sağlayıcı adı" metin kutusu vurgulanmış ve "Ileri" düğmesi seçili olan "ad ve S S O konumunu" gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Kimlik sağlayıcısı **adı** metin kutusuna kimlik sağlayıcısının adını ekleyin (ör. Azure AD).

    b. **İleri**’ye tıklayın.

1. Imzalama sertifikasını doğrulayın ve **İleri**' ye tıklayın.   

    !["Imza doğrulama" Bölüm bilgilerini ve "Ileri" düğmesini seçili gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. **Fheys Kullanıcı hesapları** bölümünde aşağıdaki adımları uygulayın:

    !["Fheys 'in Iç dizininde kullanıcı oluşturma" seçeneği ve "Ileri" düğmesi seçili olan "Fheys Kullanıcı hesapları" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. **Gerekirse, Fheys 'in Iç dizininde kullanıcı oluştur** ' u seçin ve Kullanıcı için Grup adını girin (birden çok No olabilir. virgülle ayrılmış gruplar).

    b. **İleri**’ye tıklayın.

1. **Finish (Son)** düğmesine tıklayın.

    !["Son" düğmesinin seçili olduğu "Özet" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. **Azure AD Için bilinen etki alanları** bölümünde aşağıdaki adımları uygulayın:  

    !["Kaydet" düğmesinin seçili olduğu "Azure A için bilinen etki alanları" bölümünde gösterilen ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

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
  
    b. **Kullanıcı adı** alanına yazın `brittasimon@yourcompanydomain.extension` . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Fheye/CRUCIBLE için Kantega SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Fheye/CRUCIBLE için Kantega SSO**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Fheye/CRUCIBLE Için Kantega SSO**' yı seçin.

    ![Uygulamalar listesinde Fheys/CRUCIBLE bağlantısı için Kantega SSO](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Fheye/CRUCIBLE test kullanıcısı için Kantega SSO oluştur

Azure AD kullanıcılarının fhete/Crucıya 'da oturum açmasını sağlamak için bunların Fheye/CRUCIBLE 'a sağlanması gerekir. Fheye/CRUCIBLE için Kantega SSO içinde, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Bir yönetici olarak CRUCIBLE şirket içi sunucunuzda oturum açın.

1. Dişli üzerine gelin ve **kullanıcılara** tıklayın.

    !["COG" simgesinin seçili olduğunu ve açılan listeden "kullanıcılar" seçeneğini gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. **Kullanıcılar** sekmesi bölümünde **Kullanıcı Ekle**' ye tıklayın.

    !["Kullanıcı Ekle" düğmesinin seçili olduğu "kullanıcılar" bölümünü gösteren ekran görüntüsü.](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. **Yeni Kullanıcı Ekle** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Çalışan Ekle](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. Kullanıcı **adı** metin kutusuna, gibi kullanıcının e-postasını yazın Brittasimon@contoso.com .

    b. **Görünen ad** metin kutusuna, Britta Simon gibi kullanıcının görünen adını yazın.

    c. **E-posta adresi** metin kutusuna, gibi kullanıcının e-posta adresini yazın Brittasimon@contoso.com .

    d. **Parola** metin kutusuna kullanıcının parolasını yazın.

    e. **Parolayı Onayla** metin kutusuna kullanıcı parolasını yeniden girin.

    f. **Ekle**'ye tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Fheye/Crucible için Kantega SSO 'SU ' ne tıkladığınızda, SSO 'yu ayarladığınız Fheye/CRUCIBLE için Kantega SSO 'ya otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)