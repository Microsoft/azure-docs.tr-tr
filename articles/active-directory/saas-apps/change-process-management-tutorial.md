---
title: 'Öğretici: değişiklik Işlemi yönetimi ile Azure AD SSO tümleştirmesi'
description: Azure Active Directory ve Işlem yönetimini değiştirme arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 03c78f05566876356e4f486368dc2a5b3a29de43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456267"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>Öğretici: değişiklik Işlemi yönetimiyle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, değişiklik Işlem yönetimini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Değişiklik Işlem yönetimini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Değişiklik Işlem yönetimine kimlerin erişebileceğini denetlemek için Azure AD 'yi kullanın.
* Işlem yönetimini Azure AD hesaplarıyla değiştirmek için kullanıcılarınızın otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) etkin bir değişiklik Işlemi yönetimi aboneliği.

## <a name="tutorial-description"></a>Öğretici açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edeceksiniz.

Işlem yönetimini Değiştir, ıDP tarafından başlatılan SSO 'yu destekler.

Değişiklik Işlem yönetimini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimleri koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-change-process-management-from-the-gallery"></a>Galeriden değişiklik Işlemi yönetimi ekleme

Değişiklik Işlemi yönetiminin Azure AD ile tümleştirilmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize değişiklik Işlem yönetimini eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) bir iş veya okul hesabıyla ya da kişisel bir Microsoft hesabı oturum açın.
1. Sol bölmede **Azure Active Directory**’yi seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **işlem yönetimini Değiştir** ' i girin.
1. Sonuçlar panelinde **Işlem yönetimini Değiştir** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Değişiklik Işlemi yönetimi için Azure AD SSO 'yu yapılandırma ve test etme

B. Simon adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu değişiklik Işlemi yönetimiyle yapılandırıp test edersiniz. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı ve değişiklik Işlemi yönetimi arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu değişiklik Işlemi yönetimi ile yapılandırmak ve test etmek için aşağıdaki üst düzey adımları uygulayın:

1. Kullanıcılarınızın özelliğini kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Kullanıcının Azure AD çoklu oturum açma özelliğini kullanmasını sağlamak için **[Test kullanıcısına erişim Izni verin](#grant-access-to-the-test-user)** .
1. Uygulama tarafında **[Işlem yönetimini DEĞIŞTIR SSO 'Yu yapılandırın](#configure-change-process-management-sso)** .
    1. Kullanıcının Azure AD gösterimine karşılık gelen bir **[değişiklik Işlemi yönetimi test kullanıcısı oluşturun](#create-a-change-process-management-test-user)** .
1. Yapılandırmanın çalıştığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **işlem yönetimini Değiştir** uygulama tümleştirmesi sayfasında, **Yönet** bölümünde, **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem düğmesini seçin:

   ![Temel SAML yapılandırması için kalem düğmesi](common/edit-urls.png)

1. **SAML Ile tek Sign-On ayarlama** sayfasında şu adımları uygulayın:

    a. **Tanımlayıcı** kutusunda, aşağıdaki düzende bir URL girin:`https://<hostname>:8443/`

    b. **Yanıt URL** 'si kutusuna aşağıdaki düzende bir URL girin:`https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > Önceki **tanımlayıcı** ve **yanıt URL** değerleri, kullanmanız gereken gerçek değerler değildir. Gerçek değerleri almak için [Işlem yönetimi destek ekibine](mailto:support@realtech-us.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, sertifikayı Indirip bilgisayarınıza kaydetmek için sertifika **indirme** bağlantısını **(base64)** seçin:

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Değişiklik Işlem yönetimini ayarla** bölümünde, gereksinimlerinize göre uygun URL 'Yi veya URL 'leri kopyalayın:

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

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

Bu bölümde, bu kullanıcıya Işlem yönetimini değiştirme erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Işlem yönetimini Değiştir**' i seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünde, **Kullanıcılar ve gruplar**' ı seçin:

   ![Kullanıcı ve gruplar'ı seçin](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı ekle seçeneğini belirleme](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinde **B. Simon** öğesini seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-change-process-management-sso"></a>Işlem yönetimini değiştirme SSO 'yu yapılandırma

Değişiklik Işlemi yönetimi tarafında çoklu oturum açmayı yapılandırmak için, indirilen Base64 sertifikasını ve Azure portal kopyaladığınız uygun URL 'Leri [Işlem yönetimi destek ekibine](mailto:support@realtech-us.com)göndermeniz gerekir. SAML SSO bağlantısını her iki tarafta da doğru olacak şekilde yapılandırır.

### <a name="create-a-change-process-management-test-user"></a>Değişiklik Işlemi yönetimi test kullanıcısı oluşturma
 Değişiklik Işlemi yönetimi 'nde B. Simon adlı bir kullanıcı eklemek için [Işlem yönetimi destek](mailto:support@realtech-us.com) ekibiyle birlikte çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim paneli ' ni kullanarak Azure AD SSO yapılandırmanızı test edeceksiniz.

Erişim panelinde Işlem yönetimi kutucuğunu Değiştir ' i seçtiğinizde, SSO 'yu ayarladığınız değişiklik Işlemi yönetimi örneğinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneli 'Ne giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)

- [Işlem yönetimini Azure AD ile değiştirmeyi deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle değişiklik Işlem yönetimini koruma](/cloud-app-security/proxy-intro-aad)