---
title: 'Öğretici Azure Active Directory: ıamıp patent platformu ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve ıamıp patent platformu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: dc9be41cd7975610f2779da09b94066c70a91652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460538"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Öğretici: ıamıp patent platformu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, ıamıp patent platformunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Iamıp patent platformunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Iamıp patent platformuna kimlerin erişebileceğini denetlemek için Azure AD 'yi kullanın.
* Kullanıcılarınızın Azure AD hesaplarıyla ıamıp patent platformunda otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli bir ıamıp patent platformu aboneliği.

## <a name="tutorial-description"></a>Öğretici açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edeceksiniz.

Iamıp patent platformu, SP tarafından başlatılan ve ıDP tarafından başlatılan SSO 'yu destekler.

Iamıp patent platformunu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimleri koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Galeriden ıamıp patent platformunu ekleyin

Iamıp patent platformunun tümleştirmesini Azure AD ile yapılandırmak için, Galeriden ıamıp patent platformunu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) bir iş veya okul hesabıyla ya da kişisel bir Microsoft hesabı oturum açın.
1. Sol bölmede **Azure Active Directory**’yi seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ıamıp patent platformunu** yazın.
1. Sonuçlar panelinden **ıamıp patent platformunu** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Iamıp patent platformu için Azure AD SSO 'yu yapılandırma ve test etme

B. Simon adlı bir test kullanıcısı kullanarak ıamıp patent platformuyla Azure AD SSO 'yu yapılandırıp test edersiniz. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ıamıp patent platformunda buna karşılık gelen kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Iamıp patent platformuyla Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki üst düzey adımları uygulayın:

1. Kullanıcılarınızın özelliğini kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    * Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    * Kullanıcının Azure AD çoklu oturum açma özelliğini kullanmasını sağlamak için **[Test kullanıcısına erişim Izni verin](#grant-access-to-the-test-user)** .

1. Uygulama tarafında **[ıamıp patent platformu SSO 'Yu yapılandırın](#configure-iamip-patent-platform-sso)** .
    * Kullanıcının Azure AD gösterimine karşılık gelen bir **[ıamıp patent platformu test kullanıcısı oluşturun](#create-iamip-patent-platform-test-user)** .

1. Yapılandırmanın çalıştığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), **ıamıp patent platformu** uygulama tümleştirmesi sayfasında, **Yönet** bölümünde **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem düğmesini seçin:

   ![Temel SAML yapılandırması için kalem düğmesi](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, bir hizmet sağlayıcı meta veri dosyanız varsa ve IDP tarafından BAŞLATıLAN modda SSO 'yu yapılandırmak istiyorsanız şu adımları uygulayın:

    a. **Karşıya yükleme meta veri dosyasını** seçin:

    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Klasör düğmesini seçin, meta veri dosyasını seçin ve ardından **karşıya yükle**' yi seçin:

    ![Klasör ve karşıya yükleme düğmeleri](common/browse-upload-metadata.png)

    c. Meta veri dosyası karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL 'SI** değerleri **temel SAML yapılandırması** bölümünde otomatik olarak doldurulur:

    ![Tanımlayıcı ve yanıt URL 'SI değerleri](common/idp-intiated.png)

    > [!Note]
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak doldurulmamışsa, değerleri gereksinimlerinize göre el ile sağlayın.

1. Uygulamayı SP tarafından başlatılan modda yapılandırmak istiyorsanız **ek URL 'Ler ayarla** ' yı seçin ve aşağıdaki adımı doldurun:

    **Oturum açma URL 'si** kutusuna **https: \/ /Patents.iamip.com/login-User** girin.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, sertifikayı Indirip bilgisayarınıza kaydetmek için sertifika **yükleme** bağlantısını **(ham)** seçin:

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

1. **Iamıp patent platformunu ayarla** bölümünde, gereksinimlerinize göre uygun URL 'Yi veya URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmesinde **Azure Active Directory**' ı seçin. **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları uygulayın:
   1. **Ad** kutusuna **B. Simon** girin.  
   1. **Kullanıcı adı** kutusuna \<username> @ \<companydomain> .. yazın. \<extension> Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster**' i seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="grant-access-to-the-test-user"></a>Test kullanıcısına erişim izni verme

Bu bölümde, bu kullanıcıya ıamıp patent platformu erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **ıamıp patent platformu**' nu seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünde, **Kullanıcılar ve gruplar**' ı seçin:

   ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin:

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinde **B. Simon** öğesini seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesini seçin.

## <a name="configure-iamip-patent-platform-sso"></a>Iamıp patent platformu SSO 'yu yapılandırma

Iamıp patent platformu tarafında çoklu oturum açmayı yapılandırmak için, indirilen ham sertifikayı ve Azure portal 'den kopyaladığınız uygun URL 'Leri [ıamıp patent Platformu destek ekibine](mailto:info@iamip.com)göndermeniz gerekir. SAML SSO bağlantısını her iki tarafta da doğru olacak şekilde yapılandırır.

### <a name="create-iamip-patent-platform-test-user"></a>Iamıp patent platformu test kullanıcısı oluştur

Iamıp patent platformunda B. Simon adlı bir kullanıcı eklemek için [ıamıp patent Platformu destek ekibi](mailto:info@iamip.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim paneli ' ni kullanarak Azure AD SSO yapılandırmanızı test edeceksiniz.

Erişim paneli 'nde ıamıp patent platformu kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız ıamıp patent platformu örneğinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneli 'Ne giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile ıamıp patent platformunu deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)