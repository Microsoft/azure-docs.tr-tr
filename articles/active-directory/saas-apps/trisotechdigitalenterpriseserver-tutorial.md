---
title: 'Öğretici: Trısotech Digital Enterprise Server ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Triztech Digital Enterprise Server arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 05978b7300c401a0658d6c3316f37735fcb70c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521960"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Öğretici: Trısotech Digital Enterprise Server ile tümleştirme Azure Active Directory

Bu öğreticide, Trısotech Digital Enterprise Server 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Trısotech Digital Enterprise Server 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Trısotech Digital Enterprise sunucusuna erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Triztech Digital Enterprise Server 'a (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Trısotech Digital Enterprise Server ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Trısotech Digital Enterprise Server çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Trısotech Digital Enterprise Server, **SP** tarafından başlatılan SSO 'yu destekler

* Trısotech Digital Enterprise Server **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Galeriden Trısotech Digital Enterprise Server ekleme

Trısotech Digital Enterprise Server 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Gallery 'den yönetilen SaaS uygulamaları listenize Trısotech Digital Enterprise Server eklemeniz gerekir.

**Galeriden Trısotech Digital Enterprise Server eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Trısotech Digital Enterprise Server** yazın, sonuç panelinden **Trısotech Digital Enterprise Server** ' ı seçin ve ardından uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuçlar listesinde trısotech Digital Enterprise sunucusu](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına bağlı olarak Trısotech Digital Enterprise Server ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Triztech Digital Enterprise Server 'daki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı Trısotech Digital Enterprise Server ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Trısotech Digital Enterprise Server çoklu oturum açmayı yapılandırma](#configure-trisotech-digital-enterprise-server-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Trısotech Digital Enterprise Server test kullanıcısı oluşturma](#create-trisotech-digital-enterprise-server-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Triztech Digital Enterprise Server 'Da Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Trısotech Digital Enterprise Server ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Triztech Digital Enterprise Server** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Trısotech Digital Enterprise Server etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.trisotech.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.trisotech.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Trısotech Digital Enterprise Server istemci desteği ekibine](mailto:support@trisotech.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Trısotech Digital Enterprise Server tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Triztech Digital Enterprise Server yapılandırma şirket sitenizde yönetici olarak oturum açın.

2. **Menü simgesine** tıklayın ve ardından **Yönetim**' i seçin.

    ![Ekran görüntüsü, Microsoft Digital Enterprise Server 'daki yönetim simgesini gösterir.](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. **Kullanıcı sağlayıcısı**' nı seçin.

    ![Ekran görüntüsü, menüden Kullanıcı sağlayıcısının seçili olduğunu gösterir.](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. **Kullanıcı sağlayıcısı yapılandırma** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı sağlayıcısı yapılandırmasını gösterir.](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. **Kimlik doğrulama yönteminde** açılan listeden **güvenli onaylama işlemi biçimlendirme dili 2 ' yi (SAML 2)** seçin.

    b. **Meta veri URL 'si** metin kutusunda, Azure Portal formunu kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın.

    c. **Uygulama kimliği** metin kutusuna şu kalıbı kullanarak URL 'yi girin: `https://<companyname>.trisotech.com` .

    d. **Kaydet**’e tıklayın

    e. **Izin verilen etki alanları (boş anlamı herkes)** metin kutusuna etki alanı adını girin, Izin verilen etki alanları ile eşleşen kullanıcılar için otomatik olarak lisans atar

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
  
    b. **Kullanıcı adı** alanına yazın brittasimon@yourcompanydomain.extension . Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Trısotech Digital Enterprise Server 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Trısotech Digital Enterprise Server**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Trısotech Digital Enterprise Server**' ı seçin.

    ![Uygulamalar listesindeki Trısotech Digital Enterprise sunucusu bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Trısotech Digital Enterprise Server test kullanıcısı oluşturma

Bu bölümde, Trısotech Digital Enterprise Server 'da Britta Simon adlı bir Kullanıcı oluşturulur. Trısotech Digital Enterprise Server, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Trısotech Digital Enterprise Server 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>El ile bir kullanıcı oluşturmanız gerekiyorsa, [Trısotech Digital Enterprise Server destek ekibine](mailto:support@trisotech.com)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Triztech Digital Enterprise Server kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Triztech Digital Enterprise sunucusunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)