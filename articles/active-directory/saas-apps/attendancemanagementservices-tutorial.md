---
title: 'Öğretici: Katılım yönetimi hizmetleriyle Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve katılımcı yönetim hizmetleri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 1f404d3613f9de8daadc4bb2ceb39282cf3b619e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689003"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Öğretici: Katılım yönetimi hizmetleriyle Azure Active Directory tümleştirme

Bu öğreticide, katılımcı yönetimi hizmetlerini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Katılımcı yönetimi hizmetlerini Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, katılımcı yönetimi hizmetlerine erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla katılım Yönetim Hizmetleri 'ne (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Katılım yönetimi hizmetleriyle yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Katılım Yönetim Hizmetleri çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Katılım yönetimi hizmetleri **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-attendance-management-services-from-the-gallery"></a>Galeriden katılımcı yönetimi hizmetleri ekleme

Katılım yönetimi hizmetlerinin tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize katılım Yönetim Hizmetleri eklemeniz gerekir.

**Galeriden katılımcı yönetimi hizmetleri eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **katılım Yönetim Hizmetleri** yazın, sonuç panelinden **katılım Yönetim Hizmetleri** ' ni seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuçlar listesinde katılım Yönetim Hizmetleri](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre katılımcı yönetimi hizmetleriyle yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve katılım Yönetim Hizmetleri 'ndeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, katılımcı yönetimi hizmetleriyle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[katılım Yönetimi Hizmetleri çoklu oturum açmayı yapılandırın](#configure-attendance-management-services-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan katılım yönetimi hizmetlerinde Britta Simon 'a sahip olmak için **[katılım Yönetim Hizmetleri test kullanıcısı oluşturun](#create-attendance-management-services-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Katılım yönetimi hizmetleriyle yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **katılımcı yönetimi hizmetleri** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Katılım Yönetim Hizmetleri etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://id.obc.jp/<tenant information >/`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [katılım Yönetim Hizmetleri istemci destek ekibine](https://www.obcnet.jp/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Katılımcı yönetimi hizmetleri ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-attendance-management-services-single-sign-on"></a>Katılımcı yönetimi hizmetleri tek Sign-On yapılandırma

1. Farklı bir tarayıcı penceresinde, katılım Yönetim Hizmetleri şirket sitenizde yönetici olarak oturum açın.

1. **Güvenlik Yönetimi bölümünde** **SAML kimlik doğrulaması** ' na tıklayın.

    ![Ekran görüntüsü, Latin olmayan karakterler kullanan bir sayfada SAML kimlik doğrulamasının seçili olduğunu gösterir.](./media/attendancemanagementservices-tutorial/user1.png)

1. Aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımda açıklanan görevleri gerçekleştirebileceğiniz pencereyi gösterir.](./media/attendancemanagementservices-tutorial/user2.png)

    a. **SAML kimlik doğrulaması kullan**' ı seçin.

    b. **Tanımlayıcı** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değeri değerini yapıştırın.

    c. **Kimlik doğrulama uç noktası URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL** 'si değerini yapıştırın.

    d. Azure AD 'den indirdiğiniz sertifikayı karşıya yüklemek için **Dosya Seç** ' e tıklayın.

    e. **Parola kimlik doğrulamasını devre dışı bırak** seçeneğini belirleyin.

    f. **Kayıt** öğesine tıklayın

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

Bu bölümde, katılımcı yönetimi hizmetlerine erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **katılımcı yönetimi hizmetleri**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **katılımcı yönetimi hizmetleri**' ni seçin.

    ![Uygulamalar listesindeki katılım Yönetimi Hizmetleri bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-attendance-management-services-test-user"></a>Katılımcı Yönetim Hizmetleri test kullanıcısı oluştur

Azure AD kullanıcılarının Katılım yönetimi hizmetlerinde oturum açmasını sağlamak için, katılımcı yönetim hizmetleri 'ne sağlanması gerekir. Katılımcı yönetimi hizmetleri söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Katılımcı yönetimi hizmetleri şirket sitenizde yönetici olarak oturum açın.

1. **Güvenlik Yönetimi bölümünde** **Kullanıcı yönetimi** ' ne tıklayın.

    ![Ekran görüntüsü, Latin olmayan karakterler kullanan bir sayfada seçili kullanıcı yönetimini gösterir.](./media/attendancemanagementservices-tutorial/user5.png)

1. **Yeni kurallar oturum aç**' a tıklayın.

    ![Ekran görüntüsü artı seçeneğini seçmeyi gösterir.](./media/attendancemanagementservices-tutorial/user3.png)

1. **Obcıd bilgileri** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan görevleri gerçekleştirebileceğiniz pencereyi gösterir.](./media/attendancemanagementservices-tutorial/user4.png)

    a. **Obcıd** metin kutusuna kullanıcının e-postasını yazın `BrittaSimon@contoso.com` .

    b. **Parola** metin kutusuna kullanıcının parolasını yazın.

    c. **Kayıt** öğesine tıklayın

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde katılımcı yönetimi hizmetleri kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız katılım yönetimi hizmetlerinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)