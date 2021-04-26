---
title: 'Öğretici: Procore SSO ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Procore SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 13f8f1067ce7c9fe55160400d20ec0b20788c17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515296"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Öğretici: Procore SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Procore SSO 'yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Procore SSO 'yu Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Procore SSO 'ya erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Procore SSO (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Procore SSO 'SU ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* Procore SSO çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Procore SSO, **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-procore-sso-from-the-gallery"></a>Galeriden Procore SSO 'SU ekleme

Procore SSO 'nun Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Procore SSO 'SU eklemeniz gerekir.

**Galeriden Procore SSO 'SU eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Procore SSO** yazın, sonuç panelinden **Procore SSO** ' yı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuç listesinde Procore SSO 'SU](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına dayanarak PROCORE SSO 'su ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Procore SSO 'daki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı Procore SSO 'SU ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Procore SSO çoklu oturum açmayı yapılandırma](#configure-procore-sso-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan Procore SSO 'SU içinde Britta Simon 'ın bir karşılığı olacak şekilde **[Procore SSO test kullanıcısı oluşturun](#create-procore-sso-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Procore SSO ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **Procore SSO** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

    ![Procore SSO etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/preintegrated.png)

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Procore SSO 'Yu ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-procore-sso-single-sign-on"></a>Procore SSO tek Sign-On yapılandırma

1. **PROCORE SSO** tarafında çoklu oturum açmayı yapılandırmak için, Procore şirket sitenizde yönetici olarak oturum açın.

2. Araç kutusu açılan listesinden **yönetici** ' ye tıklayarak SSO ayarları sayfasını açın.

    ![Ekran görüntüsü, dizini seçili olan Procore şirket sitesini gösterir.](./media/procoresso-tutorial/procore_tool_admin.png)

3. Kutulara değerleri aşağıda açıklandığı gibi yapıştırın.

    ![Ekran görüntüsü kişi Ekle iletişim kutusunu gösterir.](./media/procoresso-tutorial/procore_setting_admin.png) 

    a. **Çoklu oturum açma veren URL 'si** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    b. **Hedef URL 'de SAML oturum açma URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Bundan sonra yukarıdaki Azure portal indirilen **Federasyon meta VERI XML** 'sini açın ve bu sertifikayı **X509Certificate** adlı etikete kopyalayın. Kopyalanmış değeri **Çoklu oturum açma x509 sertifikası** kutusuna yapıştırın.

4. **Değişiklikleri Kaydet**’e tıklayın.

5. Bu ayarlardan sonra, Procore [desteği ekibine](https://support.procore.com/) Procore oturumu açmak için kullandığınız **etki alanı adını** (ör. **contoso.com**) göndermeniz gerekir ve bu etki alanı için Federasyon SSO 'yu etkinleştirecelerdir.

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

Bu bölümde, Procore SSO 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı SEÇIN ve **Procore SSO**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Procore SSO**' yı seçin.

    ![Uygulamalar listesindeki Procore SSO bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-procore-sso-test-user"></a>Procore SSO test kullanıcısı oluşturma

Procore SSO tarafında bir Procore test kullanıcısı oluşturmak için lütfen aşağıdaki adımları izleyin.

1. Procore şirket sitenizde yönetici olarak oturum açın.    

2. Araç kutusu açılan listesinden **Dizin** ' e tıklayarak Şirket dizini sayfasını açın.

    ![Ekran görüntüsü araç kutusundan seçilen dizine sahip Procore şirket sitesini gösterir.](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Formu açmak için **kişi ekle** seçeneğine tıklayın ve aşağıdaki seçenekleri Uygula ' yı girin.

    ![Ekran görüntüsü, Kullanıcı bilgilerini girebileceğiniz Boylan oluşturmaya bir kişi ekleme hakkında bilgi gösterir.](./media/procoresso-tutorial/Procore_user_add.png)

    a. **Ilk ad** metin kutusuna kullanıcının Ilk adını **Britta** yazın.

    b. **Soyadı** metin kutusuna kullanıcının soyadı **Simon** gibi yazın.

    c. **E-posta adresi** metin kutusuna, gibi kullanıcının e-posta adresini yazın BrittaSimon@contoso.com .

    d. İzin şablonu ' nu **daha sonra Uygula** **izin şablonu** ' nu seçin.

    e. **Oluştur**’a tıklayın.

4. Yeni eklenen kişinin ayrıntılarını denetleyin ve güncelleştirin.

    ![Ekran görüntüsü, Kullanıcı ayarlarını doğrulayabileceğiniz bir düzenleme sayfası gösterir.](./media/procoresso-tutorial/Procore_user_check.png)

5. Kullanıcı kaydını gerçekleştirmek için **Kaydet ve davet gönder** (posta ile davet et gerekliyse) veya **Kaydet** (doğrudan Kaydet) seçeneğine tıklayın.
    
    ![Ekran görüntüsü, daveti kaydedebileceğiniz ve gönderebileceğiniz geçerli proje ayarlarını gösterir.](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Procore SSO kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Procore SSO 'SU için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)