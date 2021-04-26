---
title: 'Öğretici: Yodestesi tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Yodestesi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 68e961336d7e86f9983fea49044137894ab0f985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895180"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Öğretici: Yodestesi tümleştirme Azure Active Directory

Bu öğreticide, Yodestesi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Yodestesi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Yodestesi erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla izin vermek için (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Yodestesi ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma etkin aboneliğini yodeste

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Yodestesi **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-yodeck-from-the-gallery"></a>Galeriden Yodestesi ekleme

Yodestesi Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Yodestesi eklemeniz gerekir.

**Galeriden Yodestesi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **yodestesi** yazın, sonuç panelinden **yodestesi** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

     ![Sonuçlar listesinde yodestesi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre yodestesi ile yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bir bağlantı ilişkisinin kurulması gerekir.

Yodestesi Azure AD çoklu oturum açma 'yı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, **[Yodestesi çoklu oturum açmayı yapılandırın](#configure-yodeck-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Yodestesi 'de Britta Simon 'ın bir karşılığı olacak şekilde **[Kullanıcı oluşturun](#create-yodeck-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Yodestesi Azure AD çoklu oturum açma 'yı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **yodestesi** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımı uygulayın:

    ![Etki alanı ve URL 'Lerin çoklu oturum açma bilgilerini yodestesi](common/idp-identifier.png)

    **Tanımlayıcı** metin kutusuna bir URL yazın:`https://app.yodeck.com/api/v1/account/metadata/`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Ekran görüntüsünde, meta veri dosyası yükleme bağlantısıyla temel SAML yapılandırması gösterilir.](common/both-preintegrated-signon.png)

    **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://app.yodeck.com/login`

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Yodestesi tek Sign-On yapılandırma

1. **Yodestesi** içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek** üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Ekran görüntüsü uzantıyı yükler düğmesini gösterir.](./media/target-process-tutorial/install_extension.png)

1. Tarayıcıya uzantı ekledikten sonra, **Kurulum yodestesi** ' ne tıklayın, sizi yodestesi uygulamasına yönlendirir. Buradan, oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-5 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

    **Uygulamayı el ile yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:**

1. Farklı bir Web tarayıcısı penceresinde, Yodestesi şirket sitenizde yönetici olarak oturum açın.

1. **Kullanıcı ayarları** seçeneğine tıklayın ve sayfanın sağ üst köşesini, **Hesap ayarları**' nı seçin.

    ![Ekran görüntüsünde Kullanıcı için hesap ayarları seçili görüntülenir.](./media/yodeck-tutorial/configure1.png)

1. **SAML** ' yi seçin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımları gerçekleştirebileceğiniz SAML sekmesini gösterir.](./media/yodeck-tutorial/configure2.png)

    a. **URL 'Den Içeri aktar**' ı seçin.

    b. **URL** metin kutusunda, Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın ve **içeri aktar**' a tıklayın.
    
    c. **Uygulama Federasyon meta verileri URL 'sini** içeri aktardıktan sonra, kalan alanlar otomatik olarak doldurulur.

    d. **Kaydet**’e tıklayın.

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

Bu bölümde, Yoya erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **yodestesi**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Yodestesi**' ı seçin.

    ![Uygulamalar listesindeki Yodestesi bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-yodeck-test-user"></a>Yodestesi test kullanıcısı oluştur

Azure AD kullanıcılarının Yodestesi oturum açmasını sağlamak için, bu kullanıcıların Yodestesi sağlaması gerekir. Bu durumda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Yodestesi şirket sitenizde yönetici olarak oturum açın.

2. **Kullanıcı ayarları** seçeneği ' ne tıklayın ve sayfanın sağ üst köşesini form ve **Kullanıcılar**' ı seçin.

    ![Ekran görüntüsü Kullanıcı için seçilen kullanıcıları gösterir.](./media/yodeck-tutorial/user1.png)

3. **+ Kullanıcı** ' ya tıklayarak **kullanıcı ayrıntıları** sekmesini açın.

    ![Ekran görüntüsü kullanıcılar düğmesini gösterir.](./media/yodeck-tutorial/user2.png)

4. **Kullanıcı ayrıntıları** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, bu adımları gerçekleştirebileceğiniz kullanıcı ayrıntıları sekmesini gösterir.](./media/yodeck-tutorial/user3.png)

    a. **Ilk ad** metin kutusuna, **Britta** gibi kullanıcının adını yazın.

    b. **Soyadı** metin kutusunda, **Simon** gibi kullanıcı adının soyadını yazın.

    c. **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini yazın brittasimon@contoso.com .

    d. Kuruluş gereksiniminize göre uygun **Hesap izinleri** seçeneğini belirleyin.
    
    e. **Kaydet**’e tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Yodestesi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Yodestede otomatik olarak oturumunuz açılır. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)