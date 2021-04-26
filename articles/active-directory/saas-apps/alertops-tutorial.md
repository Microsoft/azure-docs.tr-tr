---
title: 'Öğretici: Alerle ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Alerler arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 094b1a029c8e10abce3ca282de0cc99be34d313a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92318853"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile Alerle tümleştirme

Bu öğreticide, Alerin Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Alerle 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Alerler 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla aleri için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Alerte çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Alerler **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-alertops-from-the-gallery"></a>Galeriden Alerler ekleme

Alerler 'in Azure AD 'ye tümleştirilmesini yapılandırmak için galerideki yönetim SaaS uygulamaları listenize Alerler eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **alerler** yazın.
1. Sonuçlar panelinden **alerler** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Azure AD SSO 'yu, **Britta Simon** adlı bir test kullanıcısı kullanarak alerle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Alerdaki ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Alerde ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[alerler 'ı yapılandırın](#configure-alertops)** .
3. Britta Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Azure AD 'de çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı olan alerte 'da Britta Simon 'ın bir karşılığı olacak şekilde **[alerte test kullanıcısı oluşturun](#create-alertops-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **alerla** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    1. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.alertops.com`

    1. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [alerte istemci destek ekibine](mailto:support@alertops.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Alerte ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Aleri yapılandırma

1. Alerte yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarım güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **Kuruluma** tıklayın. Bu işlem sizi, alerme uygulamasına yönlendirir. Buradan, alerte oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-5 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Alerla 'yı el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve alerla şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Sol Gezinti panelinden **Hesap ayarları** ' na tıklayın.

    ![Ekran görüntüsü, hesap ayarları olarak adlandırılan Alerle menüsünü gösterir.](./media/alertops-tutorial/configure1.png)

5. **Abonelik ayarları** sayfasında **SSO** ' yı seçin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımda açıklandığı şekilde girilen değerleri içeren S S için abonelik ayarları penceresini gösterir.](./media/alertops-tutorial/configure2.png)

    a. **Çoklu oturum açma (SSO)** onay kutusunu seçin.

    b. Açılan listeden **SSO sağlayıcısı** olarak **Azure Active Directory** ' ı seçin.

    c. **Veren URL** metin kutusunda, Azure Portal **temel SAML yapılandırması** bölümünde kullandığınız tanımlayıcı değerini kullanın.

    d. **SAML uç noktası URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. **SLO ENDPOINT URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    f. Açılan listeden **SAML Imza algoritması** olarak **SHA256** ' ı seçin.

    örneğin: İndirilen sertifika (base64) dosyanızı Not defteri 'nde açın. İçeriğini panonuza kopyalayın ve X. 509.440 sertifikası metin kutusuna yapıştırın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Alerler 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **alerlar**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-alertops-test-user"></a>Alerte test kullanıcısı oluşturma

1. Farklı bir tarayıcı penceresinde, Alerla şirket sitenizde yönetici olarak oturum açın.

2. Sol Gezinti panelinden **kullanıcılara** tıklayın.

    ![Ekran görüntüsü, kullanıcılar tarafından çağrılan Alerle menüsünü gösterir.](./media/alertops-tutorial/user1.png)

3. **Kullanıcı Ekle**' yi seçin.

    ![Ekran görüntüsü Kullanıcı Ekle düğmesi olan kullanıcılar penceresini gösterir.](./media/alertops-tutorial/user2.png)

4. **Kullanıcı Ekle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsü, bu adımda açıklandığı şekilde girilen değerleri içeren kullanıcıları Ekle bölmesini gösterir.](./media/alertops-tutorial/user3.png)

    a. **Oturum açma Kullanıcı adı** metin kutusuna, **Brittasıon** gibi kullanıcının Kullanıcı adını girin.

    b. **Resmi e-posta** metin kutusuna, **Brittasıon \@ contoso.com** gibi kullanıcının e-posta adresini girin.

    c. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta** gibi girin.

    d. **Soyadı** metin kutusunda, **Simon** gibi kullanıcının adını girin.

    e. Kuruluş uyarınca açılan listeden **tür** değerini seçin.

    f. Kullanıcı açılır listesinden kuruluşunuza göre Kullanıcı **rolünü** seçin.

    örneğin: **Add (Ekle)** seçeneğini belirleyin.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Alerle kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız alerte otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)