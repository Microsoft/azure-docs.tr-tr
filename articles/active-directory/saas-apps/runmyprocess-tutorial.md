---
title: 'Öğretici: RunMyProcess ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve RunMyProcess arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68880392"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Öğretici: RunMyProcess 'i Azure Active Directory tümleştirin

Bu öğreticide, RunMyProcess 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. RunMyProcess 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de RunMyProcess 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla RunMyProcess 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* RunMyProcess çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* RunMyProcess, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-runmyprocess-from-the-gallery"></a>Galeriden RunMyProcess ekleme

RunMyProcess 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize RunMyProcess eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **RunMyProcess** yazın.
1. Sonuçlar panelinden **RunMyProcess** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu RunMyProcess ile yapılandırın ve test edin. SSO 'nun çalışması için, RunMyProcess içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu RunMyProcess ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. **[RunMyProcess SSO 'Yu yapılandırma](#configure-runmyprocess-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. **[RunMyProcess test kullanıcısı oluşturma](#create-runmyprocess-test-user)** -kullanıcının Azure AD gösterimine bağlı olan RunMyProcess 'te B. Simon 'a karşılık gelen bir.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **RunMyProcess** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [RunMyProcess istemci destek ekibine](mailto:support@runmyprocess.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **RunMyProcess 'ı ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>RunMyProcess SSO 'yu yapılandırma

1. Farklı bir Web tarayıcı penceresinde, yönetici olarak RunMyProcess kiracınızda oturum açın.

1. Sol gezinti panelinde **Hesap** ' a tıklayın ve **yapılandırma**' yı seçin.

    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. **Kimlik doğrulama yöntemi** bölümüne gidin ve aşağıdaki adımları gerçekleştirin:

    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. As **yöntemi**olarak **Samlv2 ile SSO**'yu seçin.

    b. **SSO yeniden yönlendirme** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si**değerini yapıştırın.

    c. **Logout Redirect** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si**değerini yapıştırın.

    d. **Ad kimliği biçimi** metin kutusunda, **ad tanımlayıcı biçimindeki** değeri **urn: oassıs: names: TC: SAML: 1.1: NameID-Format: emapostaadı**olarak yazın.

    e. İndirilen sertifika dosyasını Not defteri 'nde Azure portal açın, sertifika dosyasının içeriğini kopyalayın ve ardından **sertifikayı sertifika** metin kutusuna yapıştırın.

    f. **Kaydet** simgesine tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, RunMyProcess 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **RunMyProcess**öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-runmyprocess-test-user"></a>RunMyProcess test kullanıcısı oluştur

Azure AD kullanıcılarının RunMyProcess 'de oturum açmasını sağlamak için, RunMyProcess 'e sağlanması gerekir. RunMyProcess söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. RunMyProcess şirket sitenizde yönetici olarak oturum açın.

1. **Hesap** ' a tıklayıp sol gezinti panelinde **Kullanıcılar** ' ı seçin ve ardından **Yeni Kullanıcı**' ya tıklayın.

    ![Yeni Kullanıcı](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Yeni Kullanıcı")

1. **Kullanıcı ayarları** bölümünde aşağıdaki adımları uygulayın:

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure AD hesabının **adını** ve **e-postasını** yazın.

    b. Bir **IDE dili**, **dili**ve **profili**seçin.

    c. **Hesap oluşturma e-postasını bana gönder**' i seçin.

    d. **Kaydet** düğmesine tıklayın.

    > [!NOTE]
    > Azure Active Directory Kullanıcı hesapları sağlamak için RunMyProcess tarafından sunulan diğer bir RunMyProcess Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde RunMyProcess kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız RunMyProcess ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)