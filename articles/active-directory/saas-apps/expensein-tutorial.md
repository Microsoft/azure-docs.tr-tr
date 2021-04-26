---
title: "Öğretici: ' de Expenseile tümleştirme Azure Active Directory | Microsoft Docs"
description: Azure Active Directory ve Expensearasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
ms.openlocfilehash: eb7062b745ae6eb97dc2cd4ba2051cc221031630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96179206"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Öğretici: Expensewith Azure Active Directory ile tümleştirin

Bu öğreticide, Azure Active Directory (Azure AD) ile Expense'in nasıl tümleştirileceğini öğreneceksiniz. Expensewith Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, ' de Expense'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte Expenseto 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli abonelikte Expense.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 
* Expenseın, **SP ve ıDP** tarafından başlatılan SSO 'yu destekler.
* Içinde Expenseyapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-expensein-from-the-gallery"></a>Galeriden Expensefrom ekleme

' Deki expense'in tümleştirmesini Azure AD olarak yapılandırmak için, galerideki Expenseın ' u yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **expenseyazın** .
1. Sonuçlar panelinden **expensefrom '** ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>' De Expensefor Expenseazure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu expenseile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ' de Expenseiçindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu ' de Expensewith yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında SSO ayarlarını yapılandırmak için **[SSO 'Da Expenseconfigure 'U yapılandırın](#configure-expensein-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan, ' de Expenseon 'da B. Simon 'a sahip olmak için, **[Test kullanıcısına Expensecreate](#create-expensein-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), uygulama tümleştirmesinde **Expenseın** sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım gerçekleştirmesini gerektirmez.

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://app.expensein.com/saml`

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, Kopyala düğmesine tıklayarak **uygulama Federasyon meta verileri URL 'sini** kopyalayın ve sertifikayı indirmek Için **İndir** ' e tıklayın **(base64)** ve bilgisayarınıza kaydedin.

   ![Sertifika indirme bağlantısı](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. **Expenseon ' u ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, ' de Expense'e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Içinde expense'** i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.


## <a name="configure-expensein-sso"></a>SSO 'Da Expenseconfigure

1. ' De Expenseın içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek** üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, ' yi **Ayarla** ' ya tıklayın, sizi uygulamada expensesize yönlendirir. Buradan, adresinden Expenseın üzerinde oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-5 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. Expenseas 'yi el ile ayarlamak istiyorsanız şirket sitesindeki Expenseşirketinizde yönetici olarak oturum açın.

1. Sayfanın üst kısmındaki **yönetici** ' ye tıklayın, ardından **Çoklu oturum açma** ' ya gidin ve **Sağlayıcı Ekle**' ye tıklayın.

     !["Yönetici" sekmesini ve "tek Sign-On-sağlayıcıları" sayfasını ve "sağlayıcı ekle" öğesini gösteren ekran görüntüsü.](./media/expenseIn-tutorial/config01.png)

1. **Yeni kimlik sağlayıcısı** açılır penceresinde aşağıdaki adımları uygulayın:

    !["Kimlik sağlayıcısını Düzenle" açılan görüntüsünü, girilen değerlerle birlikte gösteren ekran görüntüsü.](./media/expenseIn-tutorial/config02.png)

    a. **Sağlayıcı adı** metin kutusuna adı yazın; Örneğin, Azure.

    b. **Sağlayıcının Izin verilen oturum açma izni** için **Evet** ' i seçin.

    c. **Hedef URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. **Veren** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısının** değerini yapıştırın.

    e. Sertifika (base64) Not defteri 'nde açın, içeriğini kopyalayın ve **sertifika** metin kutusuna yapıştırın.

    f. **Oluştur**’a tıklayın.

### <a name="create-expensein-test-user"></a>Test kullanıcısına Expenseoluştur

Azure AD kullanıcılarının ' de Expenseöğesinde oturum açmasını sağlamak için, ' de Expense'e sağlanması gerekir. Expenseın içinde, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Bir yönetici olarak ' de Expenseöğesinde oturum açın.

2. Sayfanın üst kısmındaki **yönetici** ' ye tıklayın, ardından **Kullanıcılar** ' a gidin ve **Yeni Kullanıcı**' ya tıklayın.

     !["Yeni Kullanıcı" seçiliyken "Yönetici" sekmesini ve "Kullanıcıları Yönet" sayfasını gösteren ekran görüntüsü.](./media/expenseIn-tutorial/config03.png)

3. **Ayrıntılar** açılır penceresinde aşağıdaki adımları uygulayın:

    ![Yapılandırmada Expense](./media/expenseIn-tutorial/config04.png)

    a. **Ad** metin kutusuna **B** gibi kullanıcının adını girin.

    b. **Soyadı** metin kutusuna, **Simon** gibi kullanıcı adının soyadını girin.

    c. **E-posta** metin kutusuna kullanıcının e-postasını girin `B.Simon@contoso.com` .

    d. **Oluştur**’a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Expenseın kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Expenseın ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Expense' i deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle Expense' i koruma](/cloud-app-security/proxy-intro-aad)