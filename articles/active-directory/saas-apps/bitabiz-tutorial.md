---
title: 'Öğretici: Bıtabiz ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve BitaBIZ arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f827945cbeccacfdf048865b6e89b6947fe7de9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159375"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Öğretici: Bıtabiz ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile barındırtabiz tümleştirmeyi öğreneceksiniz.
Bıtabiz 'i Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de, Bıtabiz 'e erişimi olan denetimi yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak şirket içine (çoklu oturum açma) oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini, Bıtabiz ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Bıtabiz çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Bıtabiz **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-bitabiz-from-the-gallery"></a>Galeriden Bıtabiz ekleme

Bıtabiz 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize bir ektabiz eklemeniz gerekir.

**Galeriden Bıtabiz eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **bitabiz**yazın, sonuç panelinden **bitabiz** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde BitaBIZ](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre Bıtabiz ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ile ilişkili kullanıcı arasındaki bir bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açma 'yı bir arada yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[BitaBIZ çoklu oturum açmayı yapılandırın](#configure-bitabiz-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Bıtabiz test kullanıcısı oluşturun](#create-bitabiz-test-user)** -Kullanıcı IÇIN Azure AD gösterimine bağlı olan Bitta Simon 'da Britta Simon 'a sahip olmak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı, Bıtabiz ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **iki** uygulama tümleştirme sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımı uygulayın:

    ![BitaBIZ etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-identifier.png)

    **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > Yukarıdaki URL 'deki değer yalnızca tanıtım amaçlıdır. Değeri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek tanımlayıcı ile güncelleştirin.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![image](common/both-preintegrated-signon.png)

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın: `https://www.bitabiz.com/dashboard`

6. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Set up Bıtabiz** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum açma URL 'SI

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-bitabiz-single-sign-on"></a>Bıtabiz çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcı penceresinde, yönetici olarak BitaBIZ kiracınızda oturum açın.

2. **Kurulum Yöneticisi**' ne tıklayın.

    ![Bıtabiz yapılandırması](./media/bitabiz-tutorial/settings1.png)

3. **Değer Ekle** bölümünde **Microsoft tümleştirmeleri** ' ne tıklayın.

    ![Bıtabiz yapılandırması](./media/bitabiz-tutorial/settings2.png)

4. **Microsoft Azure AD (çoklu oturum açmayı etkinleştir)** bölümüne gidin ve aşağıdaki adımları gerçekleştirin:

    ![Bıtabiz yapılandırması](./media/bitabiz-tutorial/settings3.png)

    a. Değeri **VARLıK kimliğinden (Azure AD 'de "tanımlayıcı")** metin kutusuna kopyalayın ve Azure Portal IÇINDEKI **temel SAML yapılandırması** bölümünde yer alan **tanımlayıcı** metin kutusuna yapıştırın. 

    b. **Azure AD çoklu oturum açma hizmeti URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'sini**yapıştırın.

    c. **Azure AD SAML VARLıK kimliği** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısı**' nı yapıştırın.

    d. İndirilen **sertifika (base64)** dosyanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve **Azure AD Imzalama sertifikası (Base64 kodlamalı)** metin kutusuna yapıştırın.

    e. Bu e-posta etki alanı (zorunlu DEĞIL) ile şirketinizdeki kullanıcılara SSO atamak için, **etki alanı adı** metin kutusuna MyCompany.com iş e-posta etki alanı adınızı ekleyin.

    f. **Devre dışı olan SSO 'yu devre dışı** bırak.

    g. SSO yapılandırmasını kaydetmek ve etkinleştirmek için **Azure AD yapılandırmasını kaydet** ' e tıklayın.

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

Bu bölümde, Bıtabiz 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **bitabiz**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Bitabiz**' ı seçin.

    ![Uygulamalar listesindeki BitaBIZ bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-bitabiz-test-user"></a>Bıtabiz test kullanıcısı oluşturma

Azure AD kullanıcılarının, Bıtabiz 'da oturum açmasını sağlamak için, bunların Bıtabiz 'e sağlanması gerekir.  
Bıtabiz söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Bıtabiz şirket sitenizde yönetici olarak oturum açın.

2. **Kurulum Yöneticisi**' ne tıklayın.

    ![Bıtabiz Kullanıcı ekleme](./media/bitabiz-tutorial/settings1.png)

3. **Kuruluş** altında **Kullanıcı Ekle** bölümüne tıklayın.

    ![Bıtabiz Kullanıcı ekleme](./media/bitabiz-tutorial/user1.png)

4. **Yeni çalışan Ekle**' ye tıklayın.

    ![Bıtabiz Kullanıcı ekleme](./media/bitabiz-tutorial/user2.png)

5. **Yeni çalışan Ekle** iletişim sayfasında, aşağıdaki adımları uygulayın:

    ![Bıtabiz Kullanıcı ekleme](./media/bitabiz-tutorial/user3.png)

    a. **Ad** metin kutusuna, ilk Kullanıcı adını Britta gibi yazın.

    b. **Soyadı** metin kutusunda, Simon gibi kullanıcı adının soyadını yazın.

    c. **E-posta** metin kutusuna Brittasimon@contoso.comgibi kullanıcının e-posta adresini yazın.

    d. **Çalışma tarihi**olarak bir tarih seçin.

    e. Kullanıcı için ayarlanamayacak, zorunlu olmayan diğer Kullanıcı öznitelikleri vardır. Daha fazla ayrıntı için lütfen [çalışan kurulumu](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) belgelerine bakın.

    f. **Çalışanı kaydet**' e tıklayın.

    > [!NOTE]
    > Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını doğrulamak için bir bağlantıyı izler.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde BitaBIZ kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Bıtabiz 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
