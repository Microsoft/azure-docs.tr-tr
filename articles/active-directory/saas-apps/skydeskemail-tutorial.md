---
title: 'Öğretici: ufuk Masası e-postasıyla Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve ufuk Masası e-postası arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 30e94c9737241ff49c29898adcc5e50c6b73a9b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516049"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Öğretici: ufuk Masası e-postasıyla Azure Active Directory tümleştirme

Bu öğreticide, ufuk Masası e-postasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Azure AD ile ufuk Masası e-postasını tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, ufuk Masası e-postasına erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla ufuk Masası e-postasına (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini, ufuk Masası e-postasıyla yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Ufuk Masası e-postası çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Ufuk Masası e-postası **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-skydesk-email-from-the-gallery"></a>Galeriden ufuk Masası e-postası ekleme

Ufuk Masası e-postası tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden, Galeri 'den yönetilen SaaS uygulamaları listenize ufuk Masası e-postası eklemeniz gerekir.

**Galeriden ufuk Masası e-postası eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **ufuk Masası e-postası** yazın, sonuç panelinden **ufuk Masası e-postası** ' nı seçin, sonra da uygulamayı eklemek için **Ekle** düğmesine tıklayın.

     ![Sonuçlar listesinde ufuk Masası e-postası](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre ufuk Masası e-postası ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve bu, ufuk Masası e-postasında ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, ufuk Masası e-postası ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[ufuk Masası e-postası çoklu oturum açmayı yapılandırın](#configure-skydesk-email-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan ufuk Masası e-postasında Britta Simon 'a sahip olmak için, **[ufuk Masası e-postası test kullanıcısı oluşturun](#create-skydesk-email-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, ufuk Masası e-postası ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **ufuk Masası e-posta** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ufuk Masası e-posta etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için, [ufuk Masası e-posta istemci destek ekibine](https://www.skydesk.jp/apps/support/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Ufuk Masası e-postası ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-skydesk-email-single-sign-on"></a>Tek başına ufuk Masası e-postası Sign-On yapılandırma

1. Farklı bir Web tarayıcısında, ufuk Masası e-posta hesabınızda Yönetici olarak oturum açın.

1. Üstteki menüde **Kurulum**' a tıklayın ve **org**' ı seçin.

    ![Ekran görüntüsü Kurulum menüsünden Seçili kuruluş ' i gösterir.](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Sol paneldeki **etki alanları** ' na tıklayın.

    ![Ekran görüntüsü, Denetim Masası 'ndan seçilen etki alanlarını gösterir.](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. **Etki alanı Ekle**' ye tıklayın.

    ![Ekran görüntüsü seçili etki alanı Ekle ' ye gösterir.](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Etki alanı adınızı girin ve etki alanını doğrulayın.

    ![Ekran görüntüsü, etki alanınızı girebileceğiniz etki alanı Ekle sekmesini gösterir.](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Sol panelde **SAML kimlik doğrulaması** ' na tıklayın.

    ![Ekran görüntüsü, Denetim Masası 'ndan SAML kimlik doğrulamasının seçili olduğunu gösterir.](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. **SAML kimlik doğrulaması** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz SAML kimlik doğrulaması ayrıntıları iletişim kutusunu gösterir.](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > SAML tabanlı kimlik doğrulaması kullanmak için etki alanı veya **Portal URL 'si** kurulumunu **doğrulamış** olmanız gerekir. Portal URL 'sini benzersiz adla ayarlayabilirsiniz.

    ![Ekran görüntüsü, adı girdiğiniz b R L 'yi gösterir.](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. **Logout** URL metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    c. **Parola değiştirme URL 'si** isteğe bağlıdır, bu nedenle boş bırakın.

    d. Azure portal 'ten indirilen sertifikayı seçmek için **anahtardan anahtar al** ' a tıklayın ve ardından sertifikayı karşıya yüklemek için **Aç** ' a tıklayın.

    e. **Algoritma** olarak **RSA**' yı seçin.

    f. Değişiklikleri kaydetmek için **Tamam** ' ı tıklatın.

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

Bu bölümde, ufuk Masası e-postasına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **ufuk Masası e-postası**' nı seçin

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **ufuk Masası e-postası**' nı seçin.

    ![Uygulamalar listesindeki ufuk Masası e-posta bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-skydesk-email-test-user"></a>Ufuk Masası e-posta testi kullanıcısı oluşturma

Bu bölümde, ufuk Masası e-postasında Britta Simon adlı bir Kullanıcı oluşturacaksınız.

Ufuk Masası e-postasında sol panelden **Kullanıcı erişimi** ' ne tıklayın ve ardından Kullanıcı adınızı girin.

![Ekran görüntüsü, Denetim Masası 'ndan seçilen kullanıcı erişimini gösterir.](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Toplu kullanıcılar oluşturmanız gerekiyorsa, [ufuk Masası e-posta istemcisi destek ekibine](https://www.skydesk.jp/apps/support/)başvurmanız gerekir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde ufuk Masası e-posta kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız ufuk Masası e-postasına otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)