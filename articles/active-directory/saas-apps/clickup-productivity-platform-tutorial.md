---
title: 'Öğretici: tıklama üretkenlik platformuyla tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve tıklama üretkenlik platformu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 4647d78255f611959bb86ca00ff6920bc823d49a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158560"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Öğretici: tıklama üretkenlik platformuyla tümleştirme Azure Active Directory

Bu öğreticide, tıklama üretkenlik platformunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Tıklama üretkenlik platformunu Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Etkileşimli üretkenlik platformuna erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla üretkenlik platformunu (çoklu oturum açma) tıklayana otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini tıklama verimliliği platformu ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Tıklama üretkenlik platformu çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Tıklama verimliliği platformu **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Galeriden tıklama üretkenlik platformunu ekleme

Tıklama üretkenlik platformunun tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden tıklama üretkenlik platformunu galerisinden yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden tıklama üretkenlik platformunu eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **tıklama üretkenlik platformunu**yazın, sonuç panelinden **tıklama üretkenlik platformunu** seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuç listesindeki üretkenlik platformunu tıklama](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre tıklama üretkenlik platformuyla yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve tıklama üretkenlik platformunda ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Tıklama üretkenlik platformuyla Azure AD çoklu oturum açma 'yı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Tıklama Için üretkenlik platformunu çoklu oturum açma yapılandırma](#configure-clickup-productivity-platform-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Tıklama üretkenlik platformu test kullanıcısı oluşturma](#create-clickup-productivity-platform-test-user)** -kullanıcının Azure AD gösterimine bağlı olan tıklama verimliliği platformunda Britta Simon 'ın bir karşılığı.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açma 'yı tıklama verimliliği platformu ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **tıklama üretkenlik platformu** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Tıklama üretkenlik platformu etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusuna bir URL yazın: `https://app.clickup.com/login/sso`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Tanımlayıcı değeri gerçek değil. Bu değeri, Bu öğreticinin ilerleyen kısımlarında açıklanan gerçek tanımlayıcı ile güncelleştirin.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Tıklama temelli üretkenlik platformunu çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcı penceresinde, yönetici olarak tıklama üretkenlik platformu kiracınızda oturum açın.

2. **Kullanıcı profiline** tıklayın ve **Ayarlar**' ı seçin.

    ![Tıklama üretkenlik yapılandırması](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Çoklu oturum açma (SSO) sağlayıcısı altında **Microsoft**' u seçin.

    ![Tıklama üretkenlik yapılandırması](./media/clickup-productivity-platform-tutorial/configure2.png)

4. **Microsoft Çoklu oturum açma 'Yı Yapılandır** sayfasında, aşağıdaki adımları uygulayın:

    ![Tıklama üretkenlik yapılandırması](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Varlık KIMLIĞI değerini kopyalamak için **Kopyala** ' ya tıklayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.
    
    b. **Azure Federasyon meta veri URL 'si** metin kutusunda, Azure Portal kopyaladığınız uygulama Federasyon meta veri URL 'si değerini yapıştırın ve **Kaydet**' e tıklayın.

5. Kurulumu tamamladıktan sonra kurulumu tamamlayıp Microsoft hesabıyla kimlik doğrulaması **yapmak Için Microsoft Ile kimlik doğrulaması** ' na tıklayın.

    ![Tıklama üretkenlik yapılandırması](./media/clickup-productivity-platform-tutorial/configure4.png)

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

Bu bölümde, tıklama üretkenlik platformuna erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **tıklama üretkenlik platformunu**seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **tıklama üretkenlik platformu**' nu seçin.

    ![Uygulamalar listesindeki tıklama üretkenlik Platformu bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-clickup-productivity-platform-test-user"></a>Tıklama üretkenlik platformu test kullanıcısı oluştur

1. Farklı bir Web tarayıcı penceresinde, yönetici olarak tıklama üretkenlik platformu kiracınızda oturum açın.

2. **Kullanıcı profili** ' ne tıklayın ve **Kullanıcılar**' ı seçin.

    ![Tıklama üretkenlik yapılandırması](./media/clickup-productivity-platform-tutorial/user1.png)

3. Metin kutusundaki kullanıcının e-posta adresini girin ve **davet et**' e tıklayın.

    ![Tıklama üretkenlik yapılandırması](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > Kullanıcı bildirimi alır ve hesabı etkinleştirme davetini kabul etmelidir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde tıklama üretkenlik platformu kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız tıklama üretkenlik platformunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

