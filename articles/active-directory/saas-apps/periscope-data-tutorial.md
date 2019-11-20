---
title: 'Öğretici: Periscope verileriyle tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Periscope verileri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f378edb-9ac9-494d-a84a-03357b923ee1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: ffa0176c4dde6334e70b9a56b066a677d1610b48
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160203"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Öğretici: Periscope verileriyle tümleştirme Azure Active Directory

Bu öğreticide, Periscope verilerini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Periscope verilerini Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Periscope verilerine erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Periscope veri (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Periscope verileriyle yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Periscope verileri çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Periscope verileri **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-periscope-data-from-the-gallery"></a>Galeriden Periscope verileri ekleme

Periscope verilerinin Azure AD ile tümleştirilmesini yapılandırmak için galerideki Periscope verilerini, yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Periscope verileri eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Periscope Data**yazın, sonuç panelinden **Periscope verileri** seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesindeki Periscope verileri](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon**adlı bir test kullanıcısına göre Periscope VERILERIYLE Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Periscope verilerinde ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı Periscope verilerle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Periscope Data çoklu oturum açmayı yapılandırın](#configure-periscope-data-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Periscope verilerinde Britta Simon 'a sahip olmak için **[Periscope Data test kullanıcısı oluşturun](#create-periscope-data-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Periscope verileriyle yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Periscope veri** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Periscope veri etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusuna URL 'lerden herhangi birini yazın:
    
    | |
    |--|
    | `https://app.periscopedata.com/` |
    | `https://app.periscopedata.com/app/<SITENAME>` |

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Değerleri gerçek oturum açma URL 'SI ile güncelleştirin. Öğreticide daha sonra açıklanan bu değeri ve **Configure Periscope Data çoklu oturum açma** bölümünden alacağınız tanımlayıcı değeri almak Için [Periscope Data Client support ekibine](mailto:support@periscopedata.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Periscope verileri çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Periscope verilerinde yönetici olarak oturum açın.

2. Sol alt taraftaki dişli menüsünü açın ve **faturalandırma** > **güvenlik** menüsünü açın ve aşağıdaki adımları gerçekleştirin. Bu ayarlara yalnızca yöneticiler erişebilir.

    ![Periscope verileri yapılandırma bilgileri](./media/periscope-data-tutorial/configure01.png)

    a. #5 **SAML Imzalama sertifikasından** **uygulama Federasyon meta veri URL 'sini** kopyalayın ve bir tarayıcıda açın. Bu, bir XML belgesi açar.

    b. **Çoklu oturum açma** metin kutusunda **Azure Active Directory**' yi seçin.

    c. **Singlesignonservice** etiketini bulun ve **LOCATION** değerini **SSO URL** metin kutusuna yapıştırın.

    d. **Singlelogoutservice** etiketini bulun ve **konum** değerini **SLO URL** metin kutusuna yapıştırın.

    e. Örneğiniz için **tanımlayıcı** değeri kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünün **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.

    f. XML dosyasının ilk etiketini bulun, **EntityId** değerini kopyalayın ve **veren** metin kutusuna yapıştırın.

    g. SAML protokolüyle **ıdpssodescriptor** etiketini bulur. Bu bölüm içinde, **Use = imzalamayı**Içeren etiket **KeyDescriptor** ' ı bulun. **X509Certificate** değerini kopyalayın ve **sertifika** metin kutusuna yapıştırın.

    h. Birden çok boşluk içeren siteler **varsayılan alandan** açılan varsayılan alanı seçebilir. Bu, yeni kullanıcıların ilk kez Periscope verilerinde oturum açtıklarında eklendiği ve Active Directory çoklu oturum açma yoluyla sağlandığı zaman bir alandır.

    i. Son olarak **Kaydet** ' e tıklayın ve **oturum kapatma**yazarak SSO ayarları değişikliğini **onaylayın** .

    ![Periscope verileri yapılandırma bilgileri](./media/periscope-data-tutorial/configure02.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Periscope verilerine erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Periscope verileri**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Periscope verileri**' ni seçin.

    ![Uygulamalar listesindeki Periscope veri bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-periscope-data-test-user"></a>Periscope Data test kullanıcısı oluşturma

Azure AD kullanıcılarının Periscope verilerinde oturum açmasını sağlamak için, Periscope verilerine sağlanması gerekir. Periscope verilerinde, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Periscope verilerinde yönetici olarak oturum açın.

2. Menünün sol alt kısmındaki **Ayarlar** simgesine tıklayın ve **izinler**' e gidin.

    ![Periscope verileri yapılandırma bilgileri](./media/periscope-data-tutorial/configure03.png)

3. **Kullanıcı Ekle** ' ye tıklayın ve aşağıdaki adımları uygulayın:

      ![Periscope verileri yapılandırma bilgileri](./media/periscope-data-tutorial/configure04.png)

    a. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta**gibi girin.

    b. **Soyadı** metin kutusuna, **Simon**gibi kullanıcı adının soyadını girin.

    c. **E-posta** metin kutusuna, **\@contoso.com**gibi kullanıcının e-postasını girin.

    d. **Ekle**' ye tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Periscope veri kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Periscope verilerinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

