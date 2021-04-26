---
title: 'Öğretici: BLINK ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve yanıp sönme arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: jeedes
ms.openlocfilehash: 7591f7eba71230d757443c09e2d4e54e69f51e30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457019"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blink"></a>Öğretici: BLINK ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile yanıp sönme nasıl tümleştirileceğini öğreneceksiniz. Yanıp sönme 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de yanıp söndürme erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla yanıp sönmek üzere otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Yanıp sönme **SP** tarafından başlatılan SSO 'yu destekler
* BLINK **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="adding-blink-from-the-gallery"></a>Galeriden yanıp sönme ekleme

Yanıp sönme tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeriden, yönetilen SaaS uygulamaları listenize yanıp söndürme eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **yanıp sönme** yazın.
1. Sonuçlar panelinden **yanıp sönme** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-blink"></a>Yanıp sönmek için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu yanıp söndürme yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında yanıp sönmek üzere bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu yanıp sönme ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[yanıp sönme SSO 'Yu yapılandırın](#configure-blink-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olarak yanıp sönmek için, yanıp **[sönen test kullanıcısı oluşturun](#create-blink-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **yanıp sönen** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki düzenin birini kullanarak bir URL yazın:
    
    ```http
    https://app.joinblink.com
    https://<SUBDOMAIN>.joinblink.com
    ```

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://api.joinblink.com/saml/o-<TENANTID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [yanıp sönen istemci desteği ekibine](https://help.joinblink.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Yanıp sönen toplantılar uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![image](common/edit-attribute.png)

1. Yukarıdaki ' a ek olarak, yanıp sönen toplantılar uygulaması, SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. Kullanıcı öznitelikleri iletişim kutusundaki Kullanıcı talepleri bölümünde aşağıdaki tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin:

    | Name | Kaynak özniteliği|
    | ---------------|  --------- |
    |   first_name    | Kullanıcı. |
    |   second_name    | User. soyadı |
    |   e-posta       | Kullanıcı. Mail |
    | | |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini yazın.

    f. **Kaydet**’e tıklayın.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Yanıp sönme ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, yanıp söndürme erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **yanıp sönme**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-blink-sso"></a>Yanıp sönme SSO 'yu Yapılandır

**Yanıp sönen** tarafta çoklu oturum açmayı yapılandırmak Için Indirilen **Federasyon meta veri XML** 'sini ve Azure Portal ' den uygun şekilde kopyalanmış URL 'leri [yanıp sönen destek ekibine](https://help.joinblink.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-blink-test-user"></a>Yanıp sönen test kullanıcısı oluştur

Bu bölümde, Britta Simon adlı bir Kullanıcı yanıp sönmek için oluşturulur. Yanıp sönme, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten yanıp sönmek 'de yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde yanıp sönen Kutucuğa tıkladığınızda, SSO 'yu ayarladığınız yanıp sönmede otomatik olarak oturum açmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile bolluk 'i deneyin](https://aad.portal.azure.com/)