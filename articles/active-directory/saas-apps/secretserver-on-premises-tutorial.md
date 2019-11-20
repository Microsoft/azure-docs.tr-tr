---
title: 'Öğretici: Gizli sunucuyla Azure Active Directory tümleştirme (Şirket Içi) | Microsoft Docs'
description: Azure Active Directory ile gizli sunucu (Şirket Içi) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4926fc1833cc14b2ad81a01e230a5c3c37ba6ab3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880153"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Öğretici: Gizli sunucuyu (Şirket Içi) Azure Active Directory ile tümleştirme

Bu öğreticide, gizli sunucuyu (Şirket Içi) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Gizli sunucuyu (Şirket Içi) Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de gizli sunucuya (Şirket Içi) erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla gizli bir sunucuda (Şirket Içi) otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Gizli sunucu (Şirket Içi) çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Gizli sunucu (Şirket Içi) **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Galeriden gizli sunucu (Şirket Içi) ekleme

Gizli sunucunun (Şirket Içi) Azure AD ile tümleştirilmesini yapılandırmak için, galerideki gizli sunucuyu (Şirket Içi) yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **gizli sunucu (Şirket içi)** yazın.
1. Sonuçlar panelinden **gizli sunucu (Şirket içi)** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu gizli sunucu (Şirket içi) ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve gizli sunucu (Şirket Içi) ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu gizli sunucu (Şirket Içi) ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[gizli sunucu (Şirket içi) SSO 'Yu yapılandırın](#configure-secret-server-on-premises-sso)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. Gizli sunucu (Şirket içi **[)](#create-secret-server-on-premises-test-user)** , Kullanıcı IÇIN Azure AD gösterimine bağlı gizli sunucuda (Şirket Içi) B. Simon 'a karşılık
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **gizli sunucu (Şirket içi)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna kullanıcı tarafından seçilen değeri bir örnek olarak girin:`https://secretserveronpremises.azure`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Yukarıda gösterilen varlık KIMLIĞI yalnızca bir örnektir ve Azure AD 'de gizli sunucu örneğinizi tanımlayan benzersiz bir değer seçebilirsiniz. Bu varlık KIMLIĞINI [gizli sunucu (Şirket içi) istemci desteği ekibine](https://thycotic.force.com/support/s/) göndermeniz ve bunları tarafında yapılandırmaları gerekir. Daha fazla ayrıntı için lütfen [Bu makaleyi](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server)okuyun.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [gizli sunucu (Şirket içi) istemci desteği ekibine](https://thycotic.force.com/support/s/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![İmzalama seçenekleri](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. **Imza seçeneğini** belirtin **SAML yanıtı ve onaylama olarak imzala**.

    ![İmzalama seçenekleri](./media/secretserver-on-premises-tutorial/signing-option.png)

1. **Gizli sunucuyu ayarla (Şirket içi)** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>Gizli sunucu (Şirket Içi) SSO 'yu yapılandırma

**Gizli sunucu (Şirket içi)** tarafında çoklu oturum açmayı yapılandırmak Için indirilen **sertifikayı (base64)** ve uygun kopyalanmış URL 'leri Azure Portal [gizli sunucu (Şirket içi) destek ekibine](https://thycotic.force.com/support/s/)göndermeniz gerekir. Bunlar, her iki kenarı da düzgün ayarlandığından SAML SSO bağlantı sağlamak için bu ayarı ayarlayın.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, gizli sunucuya (Şirket Içi) erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **gizli sunucu (Şirket içi)** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-secret-server-on-premises-test-user"></a>Gizli sunucu (Şirket Içi) test kullanıcısı oluşturma

Bu bölümde, gizli sunucuda (Şirket Içi) Britta Simon adlı bir Kullanıcı oluşturacaksınız. Gizli sunucu (Şirket içi) platformunda kullanıcıları eklemek için [gizli sunucu (Şirket içi) destek ekibi](https://thycotic.force.com/support/s/) ile çalışın. Kullanıcı oluşturulmalı ve çoklu oturum açma kullanmadan önce etkinleştirildi.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde gizli sunucu (Şirket Içi) kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız gizli sunucuda (Şirket içi) otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)