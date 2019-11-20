---
title: 'Öğretici: argıs Online ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ArcGIS arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4afd7697272775b8d5558d9c091eeea0d2b9adf
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158016"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Öğretici: ArcGIS Online ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile argıs 'yi çevrimiçi olarak tümleştirmeyi öğreneceksiniz.
Azure AD ile ArcGIS 'yi çevrimiçi tümleştirme aşağıdaki avantajları sağlar:

* Azure AD 'de, argıs online 'a erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla çevrimiçi olarak çevrimiçi (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

ArcGIS Online ile Azure AD tümleştirmesini yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* ArcGIS Online çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* ArcGIS Online, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-arcgis-online-from-the-gallery"></a>Galeriden çevrimiçi olarak ArcGIS ekleme

ArcGIS Online 'ın Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize çevrimiçi olarak ArcGIS eklemeniz gerekir.

**Galeriden çevrimiçi olarak ArcGIS eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **argıs online**yazın, sonuç panelinden ArcGIS **Online** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde çevrimiçi olan ArcGIS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon**adlı bir test kullanıcısına bağlı olarak, ArcGIS Online Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma 'nın çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin çevrimiçi olması gerekir.

ArcGIS Online ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Argıs online çoklu oturum açmayı yapılandırma](#configure-arcgis-online-single-sign-on)** uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. ArcGIS **[online test kullanıcısı oluşturun](#create-arcgis-online-test-user)** . Bu, kullanıcının Azure AD gösterimine bağlı olan ArcGIS Online 'Da Britta Simon 'a sahip olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

ArcGIS Online ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), ArcGIS **çevrimiçi** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![ArcGIS Online etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<companyname>.maps.arcgis.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [çevrimiçi istemci desteği ekibine](https://support.esri.com/en/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. ArcGIS **Online**içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek**üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Tarayıcıya Uzantı eklendikten sonra, **çevrimiçi** olarak bulunan ArcGIS Online uygulamasına tıklayın. Buradan, çevrimiçi ortamda oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve daha sonra **çevrimiçi çoklu oturum açmayı yapılandırma**bölümündeki adımları otomatik hale getirir.

### <a name="configure-arcgis-online-single-sign-on"></a>Argıs online çoklu oturum açmayı yapılandırma

1. Argıs 'yi el ile çevrimiçi olarak kurmak istiyorsanız yeni bir Web tarayıcı penceresi açın ve argıs şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

2. **Ayarları Düzenle**' ye tıklayın.

    ![Ayarları Düzenle](./media/arcgis-tutorial/ic784742.png "Ayarları Düzenle")

3. **Güvenlik**' e tıklayın.

    ![Güvenlik](./media/arcgis-tutorial/ic784743.png "Güvenlik")

4. **Kurumsal oturum açmalar**altında **kimlik sağlayıcısını ayarla**' ya tıklayın.

    ![Kurumsal oturum açmalar](./media/arcgis-tutorial/ic784744.png "Kurumsal oturum açmalar")

5. **Kimlik sağlayıcısı yapılandırmasını ayarla** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Kimlik sağlayıcısını ayarla](./media/arcgis-tutorial/ic784745.png "Kimlik sağlayıcısını ayarla")

    a. **Ad** metin kutusuna kuruluşunuzun adını yazın.

    b. **Kurumsal kimlik sağlayıcısı Için meta veriler kullanılarak sağlanacak** **bir dosya**seçin.

    c. İndirilen meta veri dosyanızı karşıya yüklemek için **Dosya Seç**' e tıklayın.

    d. **KIMLIK sağlayıcısını ayarla**' ya tıklayın.

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

Bu bölümde, argıs online 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **çevrimiçi**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **çevrimiçi olarak argıs**yazın ve seçin.

    ![Uygulamalar listesindeki ArcGIS Online bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-arcgis-online-test-user"></a>ArcGIS Online test kullanıcısı oluşturma

Azure AD kullanıcılarının çevrimiçi olarak ArcGIS 'de oturum açmasını sağlamak için, bu kullanıcıların çevrimiçi olarak ArcGIS 'de sağlanması gerekir.  
ArcGIS çevrimiçi olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Argıs** kiracınızda oturum açın.

2. **Üyeleri davet et**' e tıklayın.
   
    ![Üyeleri davet et](./media/arcgis-tutorial/ic784747.png "Üyeleri davet et")

3. **E-posta göndermeden otomatik olarak üye Ekle**' yi seçin ve ardından **İleri**' ye tıklayın.
   
    ![Üyeleri otomatik olarak ekle](./media/arcgis-tutorial/ic784748.png "Üyeleri otomatik olarak ekle")

4. **Üyeler** iletişim sayfasında, aşağıdaki adımları uygulayın:
   
     ![Ekle ve gözden geçir](./media/arcgis-tutorial/ic784749.png "Ekle ve gözden geçir")
    
     a. Sağlamak istediğiniz geçerli bir AAD hesabının **e-posta**, **ad**ve **Soyadı adını** girin.
  
     b. **Ekle ve gözden geçir**' e tıklayın.
5. Girdiğiniz verileri gözden geçirin ve ardından **üye Ekle**' ye tıklayın.
   
    ![Üye Ekle](./media/arcgis-tutorial/ic784750.png "Üye Ekle")
        
    > [!NOTE]
    > Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını onaylamak için bir bağlantıyı izler.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde ArcGIS Çevrimiçi kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız çevrimiçi ortamda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

