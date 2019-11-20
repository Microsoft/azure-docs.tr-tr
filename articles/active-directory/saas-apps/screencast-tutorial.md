---
title: 'Öğretici: ekran kaydı-O ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Azure Active Directory ile ekran kaydı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc887e95b6fa6f8b17fbbb3dbaae5105385a07fa
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132145"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Öğretici: ekran kaydı-O-tek ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile ekran kaydı-O-tek tümleştirme hakkında bilgi edineceksiniz. Azure AD ile ekran kaydı-O-tek ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, ekran kaydı-O-tek başına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ekran tabanlı olarak otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Ekran kaydı-O-tek yönlü çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Ekran kaydı-tek-özellikli **SP** tarafından başlatılan SSO 'yu destekler
* Ekran kaydı-tek başına yalnızca Kullanıcı sağlama **zamanında** desteklenir

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Galeriden ekran kaydı-O-tek ekleme

Ekran kaydı-O-tek ile Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden ekran kaydı-O-tek başına ' yı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ekran kaydı-O-matik** yazın.
1. Sonuçlar panelinden **tek başına ekran kaydı** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Ekran kaydı için Azure AD çoklu oturum açmayı yapılandırma ve test etme-O-tek

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu ekran kaydı-O-tek ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ekran kaydı-O-tek içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu ekran kaydı-O-tek ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[ekran kaydı-O-tek BAŞıNA SSO 'Yu yapılandırın](#configure-screencast-o-matic-sso)** .
    * Kullanıcının Azure AD gösterimi ile bağlantılı olarak, ekran kaydı-o-tek-' d e k i, ekran kaydı-O-tek-' y i y a, tek yönlü **[test kullanıcısı oluşturun](#create-screencast-o-matic-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **ekran kaydı-O-tek başına** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [ekran kaydı-O-tek yönlü istemci desteği ekibine](mailto:support@screencast-o-matic.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Tek başına ekran kaydı ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extensiongirin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, ekran kaydı-O-tek ' e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **ekran kaydı-O-tek**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-screencast-o-matic-sso"></a>Ekran kaydı-O-tek başına SSO yapılandırma

1. Bu yapılandırmayı, ekran kaydı-O-tek içinde otomatik hale getirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarım güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, **ekran ön kümesini ayarla-o-tek-** , sizi yalnızca ekran kaydı-o-tek başına uygulamasına yönlendirir. Buradan, ekran tabanlı olarak oturum açmak için yönetici kimlik bilgilerini sağlayın-O-tek. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-11 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. Ekran kaydı-O-el ile kurulumu yapmak istiyorsanız yeni bir Web tarayıcısı penceresi açın ve bir yönetici olarak ekran kaydı-O-tek şirket sitesinde oturum açın ve aşağıdaki adımları gerçekleştirin:

1. **Aboneliğe**tıklayın.

    ![Abonelik](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. **Erişim sayfası** bölümünde **Kurulum**' a tıklayın.

    ![Erişim](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. **Kurulum erişimi sayfasında**, aşağıdaki adımları gerçekleştirin.

1. **Erişim URL 'si** bölümüne, belirtilen metin kutusuna InstanceName yazın.

    ![Erişim](./media/screencast-tutorial/tutorial_screencast_access.png)

1. **SAML Kullanıcı kısıtlaması (isteğe bağlı)** bölümünde **etki alanı kullanıcısı gerektir** ' i seçin.

1. Azure portal 'den indirdiğiniz meta verileri karşıya yüklemek için **IDP meta VERI XML dosyasını karşıya yükle** **' ye tıklayın** .

1. **OK (Tamam)** düğmesine tıklayın.

    ![Erişim](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Ekran kaydı-O-tek yönlü test kullanıcısı oluşturma

Bu bölümde, ekran kaydı-O-tek içinde Britta Simon adlı bir Kullanıcı oluşturulur. Ekran kaydı-O-tek, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten ekran kaydı-O-tek içinde yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur. El ile bir kullanıcı oluşturmanız gerekiyorsa, [ekran kaydı-O-tek istemci desteği ekibine](mailto:support@screencast-o-matic.com)başvurun.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde ekran kaydı-O-tek kutucuğunu tıklattığınızda, SSO 'yu ayarladığınız ekran kaydı-O-kendiliğinden ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile ekran kaydı-O-tek ' i deneyin](https://aad.portal.azure.com/)