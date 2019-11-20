---
title: 'Öğretici: Elium ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Elium arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791236"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Öğretici: Elium ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Elium 'un nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Elium 'u tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Elium 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla tam olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Elium çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Elium **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Elium **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-elium-from-the-gallery"></a>Galeriden Elium ekleme

Azure AD 'de Elium tümleştirmesini yapılandırmak için Galeriden yönetilen SaaS uygulamaları listenize bir duyum eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **elium** yazın.
1. Sonuçlar panelinden **Elium** ' u seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Azure AD çoklu oturum açmayı, Elium için yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu sorunsuz bir şekilde yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında Elium 'da bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu sorunsuz bir şekilde yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Elium SSO 'Yu yapılandırın](#configure-elium-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan Elium 'da B. Simon 'ın bir karşılığı olacak şekilde, **[elium test kullanıcısı oluşturun](#create-elium-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **elium** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, Bu öğreticinin ilerleyen kısımlarında açıklanan `https://<platform-domain>.elium.com/login/saml2/metadata`' de indirilebilir olan **SP meta veri dosyasından** alırsınız.

1. Elium uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdakine ek olarak, Elium uygulaması aşağıda gösterilen SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Adı | Kaynak özniteliği|
    | ---------------| ----------------|
    | e-posta   |Kullanıcı. Mail |
    | first_name| Kullanıcı. |
    | last_name| User. soyadı|
    | job_title| User. JobTitle|
    | Şirketlerin| User. CompanyName|

    > [!NOTE]
    > Bunlar varsayılan taleplerdir. **Yalnızca e-posta talebi gereklidir**. JıT sağlama için Ayrıca yalnızca e-posta talebi zorunludur. Diğer özel talepler, bir müşteri platformundan başka bir müşteri platformuna farklılık gösterebilir.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Elium 'U ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, elim 'ye erişim vererek B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Elium**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-elium-sso"></a>Elium SSO 'yu yapılandırma

1. Yapılandırmayı tam olarak otomatik hale getirmek için, **uzantıyı yüklemek**üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

1. Tarayıcıya Uzantı eklendikten sonra, bu uygulamaya tıklayın, sizi **elium uygulamasına** yönlendirirsiniz. Buradan, oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

1. El ile el ile ayarlama yapmak istiyorsanız, yeni bir Web tarayıcısı penceresi açın ve bir yönetici olarak Elium şirket sitenizde oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Sağ üst köşedeki **Kullanıcı profiline** tıklayın ve sonra **Yönetim**' i seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/elium-tutorial/user1.png)

1. **Güvenlik** sekmesini seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/elium-tutorial/user2.png)

1. ' İ **Çoklu oturum açma (SSO)** bölümüne kaydırın ve aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırma](./media/elium-tutorial/user3.png)

    a. **SAML2 kimlik doğrulamasının hesabınız için çalıştığını doğrulayın** ve Azure Portal bu değeri, **temel SAML yapılandırması** bölümünde bulunan **oturum açma URL 'si** metin kutusuna yapıştırın.

    > [!NOTE]
    > SSO 'yu yapılandırdıktan sonra, şu URL 'de varsayılan uzaktan oturum açma sayfasına her zaman erişebilirsiniz: `https://<platform_domain>/login/regular/login` 

    b. **Enable SAML2 Federation** onay kutusunu seçin.

    c. **JIT sağlama** onay kutusunu seçin.

    d. **İndir** düğmesine tıklayarak **SP meta verilerini** açın.

    e. **SP meta veri** dosyasında **EntityId** 'Yi arayın, **entityıd** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** metin kutusuna yapıştırın. 

    ![Çoklu oturum açmayı yapılandırma](./media/elium-tutorial/user4.png)

    f. Azure portal bkz. **The The The** The **The The The** The The The The The The, **SP Metadata**

    ![Çoklu oturum açmayı yapılandırma](./media/elium-tutorial/user5.png)

    g. Azure portal indirilen meta veri dosyasını Not defteri 'nde açın, içeriği kopyalayın ve **IDP meta verileri** metin kutusuna yapıştırın.

    h. **Kaydet** düğmesine tıklayın.

### <a name="create-elium-test-user"></a>Elium test kullanıcısı oluşturma

Bu bölümde, Elium 'da B. Simon adlı bir Kullanıcı oluşturulur. Elium, varsayılan olarak etkinleştirilen **tam zamanında sağlamayı**destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Elium 'da zaten mevcut değilse, Elium 'a erişmeye çalıştığınızda yeni bir tane oluşturulur.

> [!Note]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Elium destek ekibine](mailto:support@elium.com)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Elium kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız elim 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Elium kullanmayı deneyin](https://aad.portal.azure.com/)