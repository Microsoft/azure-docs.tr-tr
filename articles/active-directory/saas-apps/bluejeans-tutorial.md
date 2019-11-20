---
title: 'Öğretici: Azure AD için BlueJeans ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure AD için Azure Active Directory ve şema arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595042"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Öğretici: Azure AD için BlueJeans ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure AD için şema Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Azure AD 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Azure AD 'ye erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Azure AD için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Azure AD çoklu oturum açma (SSO) özellikli abonelik için BlueJeans.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Azure AD için BlueJeans, **SP** tarafından başlatılan SSO 'yu destekler

* Azure AD için BlueJeans [ **Otomatik** Kullanıcı sağlamayı destekler](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Galeriden Azure AD için şema ekleme

Azure AD 'de BlueJeans 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden Azure AD için şema ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Azure AD Için BlueJeans** yazın.
1. Sonuçlar panelinden **Azure AD Için BlueJeans** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Azure AD için şema için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Azure AD için şema ile yapılandırın ve test edin. SSO 'nun çalışması için bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında Azure AD 'de bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Azure AD için şema ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Azure AD SSO Için BlueJeans 'ı yapılandırın](#configure-bluejeans-for-azure-ad-sso)** .
    1. Azure AD **[test kullanıcısı için şema oluşturma](#create-bluejeans-for-azure-ad-test-user)** -Kullanıcı Azure AD gösterimi ile bağlantılı olan Azure AD Için BlueJeans 'da B. Simon 'un bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Azure AD** uygulama tümleştirmesinin şeması sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Azure AD istemci desteği ekibine yönelik BlueJeans](https://support.bluejeans.com/contact) ile iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Azure AD için şema ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure AD 'ye yönelik şema erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Azure AD Için BlueJeans**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Azure AD SSO için şema yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak **Azure AD** Şirket sitesi için şema yöneticinize oturum açın.

2. **Yönetim \> grup ayarları \> güvenlik**' e gidin.

    ![Yöneticileri](./media/bluejeans-tutorial/ic785868.png "Yöneticileri")

3. **Güvenlik** bölümünde aşağıdaki adımları uygulayın:

    ![SAML çoklu oturum açma](./media/bluejeans-tutorial/ic785869.png "SAML çoklu oturum açma")

    a. **SAML çoklu oturum açma**seçeneğini belirleyin.

    b. **Otomatik sağlamayı etkinleştir**' i seçin.

4. Aşağıdaki adımlarla ilerleyin:

    ![Sertifika yolu](./media/bluejeans-tutorial/ic785870.png "Sertifika yolu")

    a. Azure portal indirdiğiniz Base-64 kodlu sertifikayı karşıya yüklemek için **Dosya Seç**' e tıklayın.

    b. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Parola DEĞIŞTIRME URL** metin kutusuna, Azure Portal kopyaladığınız **parola URL 'sini Değiştir** değerini yapıştırın.

    d. **Logout URL** metin kutusuna, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

5. Aşağıdaki adımlarla ilerleyin:

    ![Değişiklikleri Kaydet](./media/bluejeans-tutorial/ic785874.png "Değişiklikleri Kaydet")

    a. **Kullanıcı kimliği** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` yazın.

    b. **E-posta** metin kutusuna `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` yazın.

    c. **Değişiklikleri Kaydet**' e tıklayın.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Azure AD test kullanıcısı için şema oluşturma

Bu bölümün amacı, Azure AD için şema olarak B. Simon adlı bir Kullanıcı oluşturmaktır. Azure AD şeması, varsayılan olarak etkinleştirilen Otomatik Kullanıcı sağlamayı destekler. Otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](bluejeans-provisioning-tutorial.md) .

**Kullanıcı el ile oluşturmanız gerekiyorsa aşağıdaki adımları gerçekleştirin:**

1. **Azure AD** şirket sitenizde yönetici olarak şema olarak oturum açın.

2. **Yönetici \> KULLANıCıLARı yönet \> Kullanıcı Ekle**' ye gidin.

    ![Yöneticileri](./media/bluejeans-tutorial/ic785877.png "Yöneticileri")

    > [!IMPORTANT]
    > **Kullanıcı Ekle** sekmesi yalnızca, **güvenlikte sekmesinde** **otomatik sağlamayı etkinleştir** seçeneği işaretli değilse kullanılabilir.

3. **Kullanıcı Ekle** bölümünde aşağıdaki adımları uygulayın:

    ![Kullanıcı Ekle](./media/bluejeans-tutorial/ic785886.png "Kullanıcı Ekleme")

    a. **Ad** metin kutusuna **B**gibi kullanıcının adını girin.

    b. **Soyadı** metin kutusuna, **Simon**gibi kullanıcı adının soyadını girin.

    c. **Azure AD Kullanıcı adı için bir şema seçin** metin kutusuna, **Brittasıon** gibi kullanıcının Kullanıcı adını girin

    d. **Parola oluştur** metin kutusuna parolanızı girin.

    e. **Şirket** metin kutusuna şirketinizi girin.

    f. **E-posta adresi** metin kutusuna, `b.simon\@contoso.com` gibi kullanıcının e-postasını girin.

    g. **Azure AD toplantısı için şema oluşturma ı. D** metin kutusuna toplantı kimliğinizi girin.

    h. **Bir moderatör geçiş kodu seçin** metin kutusuna geçiş kodunuzu girin.

    i. **Devam**' a tıklayın.

    ![Kullanıcı Ekle](./media/bluejeans-tutorial/ic785887.png "Kullanıcı Ekleme")

    J. **Kullanıcı Ekle**' ye tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesabı oluşturma araçları veya Azure AD Kullanıcı hesapları sağlamak için şema tarafından sunulan API 'Ler tarafından sunulan API 'Ler için başka bir şema kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Azure AD kutucuğunun şeması ' nı tıklattığınızda, SSO 'yu ayarladığınız Azure AD için şema için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Azure AD için BlueJeans 'ı deneyin](https://aad.portal.azure.com/)

