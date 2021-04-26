---
title: 'Öğretici: Peakon ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Peakon arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: a84d4d71e5190c455441d1e627381be86ef5e129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608536"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Öğretici: Peakon ile tümleştirme Azure Active Directory

Bu öğreticide, Peakon 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Peakon Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Peakon 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Peakon (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Peakon ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Peakon çoklu oturum açma etkin aboneliği

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Peakon **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-peakon-from-the-gallery"></a>Galeriden Peakon ekleme

Peakon tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Peakon yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden Peakon eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Peakon** yazın, sonuç panelinden **Peakon** ' yi seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde Peakon](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre Peakon ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Peakon 'deki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı Peakon ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Peakon çoklu oturum açmayı yapılandırın](#configure-peakon-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Peakon 'de Britta Simon 'ın bir karşılığı olacak şekilde **[Peakon test kullanıcısı oluşturun](#create-peakon-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Peakon ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Peakon** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    ![Peakon etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.peakon.com/saml/<companyid>/metadata`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.peakon.com/saml/<companyid>/assert`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    !["Ek U r 'yi ayarla" seçeneğinin seçili olduğunu gösteren ekran görüntüsü "U R üzerinde Sing" metin kutusu vurgulanır.](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://app.peakon.com/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (ham)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

7. **Peakon ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-peakon-single-sign-on"></a>Peakon Single Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak Peakon 'de oturum açın.

2. Sayfanın sol tarafındaki menü çubuğunda **yapılandırma**' ya tıklayın ve ardından **tümleştirmelere** gidin.

    ![Yapılandırma](./media/peakon-tutorial/tutorial_peakon_config.png)

3. **Tümleştirmeler** sayfasında, **Çoklu oturum** açma ' ya tıklayın.

    ![Tek](./media/peakon-tutorial/tutorial_peakon_single.png)

4. **Çoklu oturum açma** bölümünde **Etkinleştir**' e tıklayın.

    ![Etkinleştir](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. **SAML kullanan çalışanlar Için çoklu oturum açma** bölümünde aşağıdaki adımları uygulayın:

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. **SSO oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **SSO Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    c. Azure portal indirdiğiniz sertifikayı sertifika kutusuna yüklemek için **Dosya Seç** ' e tıklayın.

    d. **VARLıK kimliğini** kopyalamak ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı** metin kutusuna yapıştırmak için **simgeye** tıklayın.

    e. Azure portal üzerindeki **temel SAML yapılandırması** bölümüne **yanıt URL 'sini (ACS)** ve **yanıt URL 'si** metin kutusunu kopyalamak için **simgeye** tıklayın.

    f. **Kaydet**’e tıklayın

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alan türü **brittasimon@yourcompanydomain.extension**  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Peakon 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Peakon**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Peakon**' yi seçin.

    ![Uygulamalar listesindeki Peakon bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-peakon-test-user"></a>Peakon test kullanıcısı oluştur

Azure AD kullanıcılarının Peakon 'de oturum açmasını etkinleştirmek için, Peakon ' a sağlanması gerekir.  
Peakon durumunda sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Peakon şirket sitenizde yönetici olarak oturum açın.

2. Sayfanın sol tarafındaki menü çubuğunda **yapılandırma**' ya tıklayın ve ardından **çalışanlar**' a gidin.

    ![Çalışan](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. Sayfanın sağ üst kısmında **çalışan Ekle**' ye tıklayın.

    ![Çalışan Ekle](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. **Yeni çalışan** iletişim sayfasında aşağıdaki adımları gerçekleştirin:

    ![Yeni çalışan](./media/peakon-tutorial/tutorial_peakon_create.png)

    1. **Ad** metin kutusuna, Ilk adı **Britta** ve adı **Simon** olarak yazın.

    1. **E-posta** metin kutusuna **Brittasıon \@ contoso.com** gibi e-posta adresini yazın.

    1. **Çalışan oluştur**' a tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Peakon kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Peakon için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)