---
title: 'Öğretici: Teamphoria ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Teamphoria arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2631b34f5658c9d4f76ca26d378bc63fe59ad156
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373263"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Öğretici: Teamphoria ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Teamphoria 'yı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Teamphoria 'yı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Teamphoria erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Teamphoria 'ya otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Teamphoriçoklu oturum açma (SSO) özellikli bir abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Teamphoria, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-teamphoria-from-the-gallery"></a>Galeriden Teamphoria ekleme

Teamphoria 'nın tümleştirmesini Azure AD ile yapılandırmak için, Galeriden Teamphoria 'yı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Teamphoria** yazın.
1. Sonuçlar panelinden **Teamphoria** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Teamphoria için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Teamphoria Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Teamphoria içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Teamphoria ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Teamphoria SSO 'Yu yapılandırın](#configure-teamphoria-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Teamphoria 'da B. Simon 'a sahip olmak için **[teamphcuma test kullanıcısı oluşturun](#create-teamphoria-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Teamphoria** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Teamphoria istemci destek ekibine](https://www.teamphoria.com/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Set up Teamphoria** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

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

Bu bölümde, Teamphoria erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Teamphoria**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-teamphoria-sso"></a>Teamphcuma SSO 'yu yapılandırma

1. Teamphoria 'daki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek**üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **set up teamphoria** öğesine tıkladığınızda sizi teamphoria uygulamasına yönlendirirsiniz. Buradan, Teamphoria 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Teamphcuma 'yı el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve Teamphoria şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. Sol araç çubuğunda **yönetıcı ayarları** seçeneğine gidin ve yapılandırma sekmesinde, **tek oturum** aç ' a tıklayarak SSO yapılandırma penceresini açın.

    ![Çoklu oturum açmayı yapılandırma](./media/teamphoria-tutorial/admin_sso_configure.png)

5. SSO ayarlarını ekleme formunu açmak için sağ üst köşedeki **yenı KIMLIK sağlayıcısı ekle** seçeneğine tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Alanlara aşağıda açıklanan şekilde ayrıntıları girin-

    ![Çoklu oturum açmayı yapılandırma](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **Görünen ad**: yönetici sayfasında eklentinin görünen adını girin.

    b. **Düğme adı**: SSO aracılığıyla oturum açmak için oturum açma sayfasında görüntülenecek sekmenin adı.

    c. **Sertifika**: daha önce Indirilen bir sertifikayı not defteri 'nde Azure Portal açın, içeriğini kopyalayın ve kutuya yapıştırın.

    d. **GIRIŞ noktası**: daha önce Azure Portal kopyaladığınız **oturum açma URL** 'sini yapıştırın.

    e. Seçeneği **Açık** olarak değiştirin ve **Kaydet**' e tıklayın.

### <a name="create-teamphoria-test-user"></a>Teamphoria test kullanıcısı oluşturma

Azure AD kullanıcılarının Teamphoria 'da oturum açmasını sağlamak için, Teamphoria ' ya sağlanması gerekir. Teamphoria durumunda, sağlama elle gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Teamphcuma şirket sitenizde yönetici olarak oturum açın.

1. Sol araç çubuğunda **yönetici** ayarları ' na tıklayın ve **Yönet** sekmesinde Kullanıcılar ' a tıklayarak kullanıcılar için yönetici sayfasını açın.

    ![Çalışan Ekle](./media/teamphoria-tutorial/admin_manage_users.png)

1. **El Ile davet** seçeneğine tıklayın.

    ![Kişileri davet et](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Bu sayfada aşağıdaki eylemi gerçekleştirin.

    ![Kişileri davet et](./media/teamphoria-tutorial/manual_user_invite.png)

    a. **E-posta adresi** metin kutusuna B. Simon gibi kullanıcının **e-posta adresini** girin.

    b. **Ilk ad** metin kutusuna **B**gibi kullanıcının adını girin.

    c. Soyadı metin kutusunda, **Simon** **adlı** kullanıcının soyadını girin.

    d. **Davet 1 Kullanıcı**' ya tıklayın. Kullanıcının sistemde oluşturulmasını sağlamak için daveti kabul etmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Teamphoria kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Teamphoria 'da otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Teamphoria 'yi deneyin](https://aad.portal.azure.com/)

