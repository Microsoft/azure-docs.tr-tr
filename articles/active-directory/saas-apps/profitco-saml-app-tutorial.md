---
title: 'Öğretici: Profit.co ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Profit.co arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.openlocfilehash: 027fb66538dfcead24470507b8080f2e0aacb469
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515211"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco"></a>Öğretici: Profit.co ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Profit.co 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Profit.co 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Profit.co 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Profit.co ' de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin, Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Profit.co çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Profit.co, ıDP tarafından başlatılan SSO 'yu destekler.

* Profit.co yapılandırdıktan sonra, oturum denetimini zorunlu kılabilirsiniz. Bu, kuruluşunuzun hassas verilerinin, gerçek zamanlı olarak ayıklanma ve içe taşmayı korur. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-from-the-gallery"></a>Galeriden Profit.co ekleme

Profit.co tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Profit.co yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Profit.co** yazın.
1. Sonuçlar panelinden **Profit.co** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco"></a>Profit.co için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Profit.co ile yapılandırın ve test edin. SSO 'nun çalışması için, Profit.co 'deki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlı bir ilişki kurun.

Azure AD SSO 'yu Profit.co ile yapılandırmak ve test etmek için genel adımlar şunlardır:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[PROFIT.co SSO 'Yu yapılandırın](#configure-profitco-sso)** .
    1. Profit.co 'de B. Simon 'ya karşılık gelen **[Profit.co test kullanıcısı oluşturun](#create-a-profitco-test-user)** . Bu karşılık gelen bu, kullanıcının Azure AD gösterimine bağlıdır.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Profit.co** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun. **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Kalem simgesi vurgulanmış şekilde, SAML ile çoklu oturum açmayı ayarlama ekran görüntüsü](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure 'da zaten önceden doldurulur. Kullanıcıların **Kaydet** düğmesini seçerek yapılandırmayı kaydetmesi gerekir.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **Kopyala** düğmesini seçin. Bu, **uygulama Federasyon meta veri URL 'sini** kopyalar ve bilgisayarınıza kaydeder.

    ![SAML Imzalama sertifikasının, Kopyala düğmesi vurgulanmış şekilde ekran görüntüsü](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** alanında gösterilen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Profit.co 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirin.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Profit.co**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   ![Kullanıcılar ve gruplar vurgulanmış şekilde Yönet bölümünün ekran görüntüsü](common/users-groups-blade.png)

1. **Kullanıcı ekle**'yi seçin. **Atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcılar ve gruplar sayfasının ekran görüntüsü, Kullanıcı Ekle vurgulanmış olarak](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmındaki **Seç** düğmesini seçin.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından ekranın alt kısmındaki **Seç** düğmesini seçin.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-profitco-sso"></a>Profit.co SSO 'yu yapılandırma

Profit.co Side çoklu oturum açmayı yapılandırmak için, uygulama Federasyon meta veri URL 'sini [Profit.co destek ekibine](mailto:support@profit.co)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak için bu ayarı yapılandırır.

### <a name="create-a-profitco-test-user"></a>Profit.co test kullanıcısı oluşturma

Bu bölümde, Profit.co içinde B. Simon adlı bir Kullanıcı oluşturacaksınız. Kullanıcıları Profit.co platformuna eklemek için [Profit.co destek](mailto:support@profit.co) ekibiyle çalışın. Kullanıcıları oluşturup etkinleştirene kadar çoklu oturum açma kullanamazsınız.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim paneli ' ni kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Profit.co kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Profit.co için otomatik olarak oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [erişim paneli 'Ne giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Profit.co deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle Profit.co koruma](/cloud-app-security/proxy-intro-aad)