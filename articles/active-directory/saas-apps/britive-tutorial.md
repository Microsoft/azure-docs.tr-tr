---
title: 'Öğretici: Briat ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Brikeler arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2020
ms.author: jeedes
ms.openlocfilehash: 74f449afe2081feecfe7f0194b20429314ec572d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456679"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-britive"></a>Öğretici: Briat ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Brikmeyi nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Briksiz tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Briat erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Britıcı çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Britıcı, **SP** tarafından başlatılan SSO 'yu destekler
* Briksiz 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimlerini uygulayabilirsiniz. Oturum denetimi koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-britive-from-the-gallery"></a>Galeriden Britıcı ekleme

Azure AD 'de brikliği tümleştirmeyi yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Briat eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **britıcı** yazın.
1. Sonuçlar panelinden **Briat** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-britive"></a>Azure AD çoklu oturum açmayı, Briat için yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu bir ile birlikte yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında Briat bağlantısı oluşturmanız gerekir.

Azure AD SSO 'yu bir şekilde yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Britive SSO 'Yu yapılandırma](#configure-britive-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimi ile bağlantılı olarak, Brit bir **[test kullanıcısı oluşturun](#create-britive-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **britıcı** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<TENANTNAME>.britive-app.com/sso`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`urn:amazon:cognito:sp:<UNIQUE_ID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, Bu öğreticinin ilerleyen kısımlarında açıklanan gerçek oturum açma URL 'SI ve tanımlayıcı ile güncelleştirin. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Brit ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, e-postayla erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Briat**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-britive-sso"></a>Britıcı SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak Britive Web sitesinde oturum açın.

1. **Yönetici ayarları simgesine** tıklayın ve **güvenlik**' i seçin.

    ![Ekran görüntüsünde, ayarlar ve Güvenlik seçiliyken Britive Web sitesi gösterilir.](./media/britive-tutorial/configure1.png)

1. **SSO yapılandırması** ' nı seçin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımdaki bilgileri girdiğiniz S O yapılandırmasını gösterir.](./media/britive-tutorial/configure2.png)

    a. **Hedef kitle/VARLıK kimliği** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın.

    b. ' İ **Başlat SSO URL 'si** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **URL 'yi oturum aç** metin kutusuna yapıştırın.

    c. Azure portal 'ten indirilen meta veri XML dosyasını karşıya yüklemek için **SAML meta verilerini karşıya yükle** ' ye tıklayın. Meta veri dosyasını karşıya yükledikten sonra, yukarıdaki değerler otomatik olarak doldurulur ve değişiklikleri kaydeder.

### <a name="create-britive-test-user"></a>Briat test kullanıcısı oluşturma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak Britive Web sitesinde oturum açın.

1. **Yönetici ayarları simgesine** tıklayın ve **Kullanıcı yönetimi**' ni seçin.

    ![Ekran görüntüsünde, ayarlar ve Kullanıcı yönetimi seçiliyken Britive Web sitesi gösterilir.](./media/britive-tutorial/user1.png)

1. **Kullanıcı Ekle**' ye tıklayın.

    ![Ekran görüntüsü Kullanıcı Ekle düğmesini gösterir.](./media/britive-tutorial/user2.png)

1. Kullanıcının gerekli tüm ayrıntılarını kuruluşunuzun gereksinimlerine göre doldurup **Kullanıcı Ekle**' ye tıklayın.

    ![Ekran görüntüsü, Kullanıcı bilgilerini girdiğiniz ad bir Kullanıcı sayfasını gösterir.](./media/britive-tutorial/user3.png)

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Britıcı Kutucuğa tıkladığınızda, SSO 'yu ayarladığınız Brikte otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile briki deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)