---
title: 'Öğretici Azure Active Directory: tek oturum açma (SSO) öğesini en güncel hizmet ile tümleştirme | Microsoft Docs'
description: Azure Active Directory ve tek başına hizmeti arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ba2f59799629873553fe261abebf59529ad94d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "73570597"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Öğretici: tek oturum açma (SSO) öğesini en güncel hizmet ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile en güncel hizmeti tümleştirmeyi öğreneceksiniz. En güncel hizmeti Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, en yenisi hizmetine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak hizmet vermek için oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Ön koşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* En güncel hizmeti çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* En güncel hizmet **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-freshservice-from-the-gallery"></a>Galeriden bir hizmet ekleme

En güncel hizmeti Azure AD ile tümleştirmeyi yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize en güncel hizmeti eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna her bir **hizmet** yazın.
1. Sonuçlar panelinden yalnızca bir **hizmet** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Geçici hizmet için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak, Azure AD SSO 'yu yalnızca bir hizmet ile yapılandırma ve test etme. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bu kullanıcı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu en güncel hizmetle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, tek başına **[HIZMET SSO 'Yu yapılandırın](#configure-freshservice-sso)** .
    1. Kullanıcı için Azure AD gösterimine bağlı olan, yalnızca bir hizmet **[testi kullanıcısı oluşturun](#create-freshservice-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), en güncel **hizmet** uygulaması tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<democompany>.freshservice.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için, en güncel [hizmet istemci destek ekibine](https://support.freshservice.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. En az hizmeti, SSO 'nun çalışmasını sağlamak için SHA-256 parmak izini gerektirir. SHA-256 parmak izini almak için aşağıdaki adımları gerçekleştirin:

    ![Parmak izi](./media/freshservice-tutorial/ic790821.png "parmak izi")

    1. [Bağlantıyı](https://www.samltool.com/fingerprint.php) farklı bir Web tarayıcısında açın.

    1. İndirilen sertifika (base64) dosyasını Not defteri 'nde açın ve içeriği **X. 509.440 CERT** metin kutusuna yapıştırın.

    1. Algoritma için açılan listeden **SHA256** öğesini seçin.

    1. **Parmak Izini hesapla**' ya tıklayın.

    1. Oluşturulan **parmak izini** kopyalayıp bilgisayarınıza kaydetmek için Kopyala simgesine tıklayın.

1. **Azure Portal** **en güncel hizmeti ayarla** bölümünde, GEREKSINIMINIZE göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak en güncel hizmet 'e erişim izni vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **yeniden Seç ' i seçin**.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-freshservice-sso"></a>En güncel hizmet SSO 'SU yapılandırma

1. Yapılandırma işlemini otomatik hale getirmek için, **uzantıyı yüklemek**üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **kurulumu** en sonra, bunu en doğru hizmet uygulamasına yönlendirirsiniz. Buradan, tek bir hizmette oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Yeni bir hizmeti el ile ayarlamak isterseniz, yeni bir Web tarayıcı penceresi açın ve yeni bir Web tarayıcısı penceresi açın ve aşağıdaki adımları gerçekleştirin:

4. Üstteki menüden **yönetici**' ye tıklayın.

    ![Yönetici](./media/freshservice-tutorial/ic790814.png "Yöneticisi")

5. **Müşteri portalında** **güvenlik**' e tıklayın.

    ![Güvenlik](./media/freshservice-tutorial/ic790815.png "güvenliği")

6. **Güvenlik** bölümünde aşağıdaki adımları uygulayın:

    ![Çoklu oturum]açma(./media/freshservice-tutorial/ic790816.png "Çoklu oturum") açma

    a. **Çoklu oturum açmayı**değiştir.

    b. **SAML SSO**'yu seçin.

    c. **SAML oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si**değerini yapıştırın.

    d. **Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si**değerini yapıştırın.

    e. **Güvenlik sertifikası parmak izi** metin kutusunda, daha önce oluşturduğunuz **parmak izi** değerini yapıştırın.

    f. **Kaydet**’e tıklayın

### <a name="create-freshservice-test-user"></a>Yalnızca bir hizmet testi kullanıcısı oluştur

Azure AD kullanıcılarının, tek bir hizmette oturum açmasını sağlamak için, bu kullanıcıların, tek bir hizmet olarak sağlanması gerekir. En güncel hizmet durumunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Alternatif **hizmet** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüden **yönetici**' ye tıklayın.

    ![Yönetici](./media/freshservice-tutorial/ic790814.png "Yöneticisi")

3. **Kullanıcı yönetimi** bölümünde, **istek sahipleri**' na tıklayın.

    ![İstek](./media/freshservice-tutorial/ic790818.png "sahipleri")

4. **Yeni Istek sahibi**öğesine tıklayın.

    ![Yeni istekleyiciler](./media/freshservice-tutorial/ic790819.png "Yeni istekleyiciler")

5. **Yeni Istek sahibi** bölümünde aşağıdaki adımları uygulayın:

    ![Yeni Istek sahibi](./media/freshservice-tutorial/ic790820.png "yeni istek sahibi")  

    a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure Active Directory hesabının **ad** ve **e-posta** özniteliklerini girin.

    b. **Kaydet** düğmesine tıklayın.

    > [!NOTE]
    > Azure Active Directory hesap sahibi, hesabı etkin olmadan önce onaylamaya yönelik bir bağlantı içeren bir e-posta alır
    >  

> [!NOTE]
> AAD Kullanıcı hesapları sağlamak için, başka bir kullanıcı hesabı oluşturma aracını veya en güncel hizmet tarafından sunulan API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde yalnızca bir hizmet kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız otomatik olarak hizmette oturum açmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile en güncel hizmeti deneyin](https://aad.portal.azure.com/)