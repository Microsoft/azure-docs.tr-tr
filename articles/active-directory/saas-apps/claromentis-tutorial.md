---
title: 'Öğretici: Claromentıs ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ve Claroi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: 92b068ee9b8aaf4c462002354bbb6490f4888a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455913"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Öğretici: Claromentıs ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Claromenma 'yı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Claromentıma 'yı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Claromentıs 'ye erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla çakışanlar için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Claromentıma çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Claromentıs **, SP ve ıDP** tarafından başlatılan SSO 'yu destekliyor
* Claromentıs **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-claromentis-from-the-gallery"></a>Galeriden Claromens ekleme

Claromentıs 'in Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Claromens eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Claromentıs** yazın.
1. Sonuçlar panelinden **Claromenma** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Claromenma için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Claromentıs ile yapılandırın ve test edin. SSO 'nun çalışması için, çakışan bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Claromentıs ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Claromentıs SSO 'Yu yapılandırın](#configure-claromentis-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan Claromenm 'de B. Simon 'a karşılık gelen, **[claromentıs test kullanıcısı oluşturun](#create-claromentis-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Claromentıs** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, kuruluşunuzun gereksinimlerine göre tanımlayıcı değerini girin.

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'si ve daha sonra Turorial içinde açıklanan oturum açma URL 'SI ile güncelleştirin.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Claromentıs ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Claromens 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Claromentıs**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-claromentis-sso"></a>Claromentıs SSO 'yu yapılandırma

1. Farklı bir tarayıcı penceresinde, bir yönetici olarak Claromentıs Web sitesinde oturum açın.

1. **Uygulamalar simgesine** tıklayın ve **yönetici**' yi seçin.

    ![Ekran görüntüsünde, yönetici seçiliyken Claromentıs Web sitesi gösterilir.](./media/claromentis-tutorial/config1.png)

1. **Özel oturum açma işleyicisi** sekmesini seçin.

    ![Ekran görüntüsü özel oturum açma Işleyicisinin seçili olduğu yönetim sayfasını gösterir.](./media/claromentis-tutorial/config2.png)

1. **SAML yapılandırması**' nı seçin.

    ![Ekran görüntüsü SAML için yapılandırma sayfasını gösterir.](./media/claromentis-tutorial/config3.png)

1. **SAML yapılandırması** sekmesinde, **yapılandırma** bölümüne gidin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımda açıklanan bilgileri girebileceğiniz sayfanın yapılandırma bölümünü gösterir.](./media/claromentis-tutorial/config4.png)

    a. **Teknik Iletişim adı** metin kutusuna teknik iletişim kişinin adını girin.

    b. **Teknik Iletişim e-posta** metin kutusuna teknik Iletişim kişisinin e-posta adresini girin.

    c. **AUTH yönetici parolası** metin kutusuna parolayı girin.

1. **Kimlik doğrulama kaynakları** ' na gidin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımda açıklanan bilgileri girebileceğiniz auth kaynakları bölümünü gösterir.](./media/claromentis-tutorial/config5.png)

    a. **IDP** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini girin.

    b. **VARLıK kimliği** metin kutusuna varlık kimliği değerini girin.

    c. Azure portal indirdiğiniz **Federasyon meta VERI XML** dosyasını karşıya yükleyin.

    d. **Kaydet**’e tıklayın.

1. Artık, **SAML yapılandırması** bölümünün **kimlik sağlayıcısı** bölümünde tüm URL 'lerin doldurulduğunu görürsünüz.

    ![Ekran görüntüsünde, U R ls ile doldurulmuş kimlik sağlayıcısı sayfası gösterilir.](./media/claromentis-tutorial/config6.png)

    a. Kopya **tanımlayıcı (VARLıK kimliği)** değeri, bu değeri Azure Portal IÇINDEKI **temel SAML yapılandırması** bölümünde yer alan **tanımlayıcı** metin kutusuna yapıştırın.

    b. **Yanıt URL 'si** değerini kopyalayın, bu değeri Azure Portal IÇINDEKI **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** metin kutusuna yapıştırın.

    c. **Oturum açma URL 'si** değeri, bu değeri Azure Portal IÇINDEKI **temel SAML yapılandırması** bölümünde bulunan **oturum açma URL 'si** metin kutusuna yapıştırın.

### <a name="create-claromentis-test-user"></a>Claromentıs test kullanıcısı oluşturma

Bu bölümde, Claromentıs 'de B. Simon adlı bir Kullanıcı oluşturulur. Claromentıs, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Kullanıcı clade zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim paneli 'nde Claromentıs kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız clade otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile çakışmalı deneyin](https://aad.portal.azure.com/)