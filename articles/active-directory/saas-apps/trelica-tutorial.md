---
title: 'Öğretici: Trelica ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Trelica arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92517761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Öğretici: Trelica ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide Trelica 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

Bu tümleştirmeyle şunları yapabilirsiniz:

* Azure AD 'de Trelica erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Trelica ' de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli bir Trelica aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Trelica, ıDP tarafından başlatılan SSO 'yu destekler.
* Trelica, tam zamanında Kullanıcı sağlamayı destekler.
* Trelica 'yı yapılandırdıktan sonra, oturum denetimini zorunlu kılabilirsiniz. Bu denetim, kuruluşunuzun hassas verilerinin, gerçek zamanlı olarak ayıklanma ve savunma korumasını korur. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Galeriden Trelica ekleme

Trelica 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Trelica eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. En soldaki gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **trelica** girin.
1. Arama sonuçlarından **Trelica** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Trelica için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu trelica ile yapılandırın ve test edin. SSO 'nun çalışması için Trelica içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Azure AD SSO 'yu Trelica ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Trelica SSO 'Yu yapılandırın](#configure-trelica-sso)** .
    1. Trelica 'de B. Simon 'ya karşılık gelen bir **[trelica test kullanıcısı oluşturun](#create-a-trelica-test-user)** . Bu karşılık gelen bu, kullanıcının Azure AD gösterimine bağlıdır.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) **trelica** uygulama tümleştirmesi sayfasında, **Yönet** bölümüne gidin. **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırması için kalem simgesi vurgulanmış şekilde SAML ile tek Sign-On ayarla sayfası](common/edit-urls.png)

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki değerleri girin:

    1. **Tanımlayıcı** kutusuna URL 'yi girin **https://app.trelica.com** .

    1. **Yanıt URL 'si** kutusuna, modele sahıp bir URL girin `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` .

    > [!NOTE]
    > Yanıt URL 'SI değeri gerçek değil. Bu değeri gerçek yanıt URL 'siyle (ACS olarak da bilinir) güncelleştirin.
    > Bunu Trelica ' da oturum açarak ve [SAML kimlik sağlayıcıları yapılandırma sayfasına](https://app.trelica.com/Admin/Profile/SAML) (yönetici > HESABı > SAML) giderek bulabilirsiniz. Bunu panoya koymak için **assertion Consumer Service (ACS) URL 'sinin** yanındaki Kopyala düğmesine tıklayın ve Azure AD 'de **yanıt URL 'si** metin kutusuna yapıştırmaya hazırlanın.
    > [Trelica Yardım belgelerini](https://docs.trelica.com/admin/saml/azure-ad) okuyun veya sorularınız varsa [trelica istemci destek ekibine](mailto:support@trelica.com) başvurun.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde Kopyala düğmesine tıklayarak **uygulama Federasyon meta verileri URL 'sini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Uygulama Federasyon meta verileri URL 'Sinin yanında bulunan Kopyala düğmesi vurgulanmış şekilde SAML Imzalama sertifikası bölümü](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal B. Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure Portal en soldaki bölmede, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmında **Yeni Kullanıcı**' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına **B. Simon** girin.
   1. **Kullanıcı adı** alanına **B.Simon@**_şirketetkialanı_ yazın **.** _uzantı_. Örneğin, B.Simon@contoso.com.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda gösterilen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Trelica erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirirsiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Trelica**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümüne gidin ve **Kullanıcılar ve gruplar**' ı seçin.

   ![Kullanıcılar ve gruplar vurgulanmış şekilde Yönet bölümü](common/users-groups-blade.png)

1. **Kullanıcı ekle**'yi seçin. **Atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

   ![Kullanıcı Ekle vurgulanmış olan kullanıcılar ve gruplar penceresi](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** öğesini seçin. Ardından ekranın alt kısmındaki **Seç** düğmesini seçin.
1. SAML onaylama 'da herhangi bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ardından ekranın alt kısmındaki **Seç** düğmesini seçin.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-trelica-sso"></a>Trelica SSO 'SU yapılandırma

**Trelica** tarafında çoklu oturum açmayı yapılandırmak için [SAML kimlik sağlayıcıları yapılandırma sayfasına](https://app.trelica.com/Admin/Profile/SAML) gidin (yönetıcı > hesabı > SAML). **Yeni** düğmesine tıklayın. Ad olarak **Azure AD** girin ve meta veri türü için **URL 'den meta veriler** ' i seçin. Azure AD 'den aldığınız **uygulama Federasyon meta verileri URL** 'Sini trelica Içindeki **meta veri URL 'si** alanına yapıştırın.

[Trelica Yardım belgelerini](https://docs.trelica.com/admin/saml/azure-ad) okuyun veya sorularınız varsa [trelica istemci destek ekibine](mailto:support@trelica.com) başvurun.

### <a name="create-a-trelica-test-user"></a>Trelica test kullanıcısı oluşturma

Trelica, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde gerçekleştirmeniz gereken bir işlem yok. Bir Kullanıcı Trelica 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı My Apps portalını kullanarak test edersiniz.

Uygulamalarım portalındaki Trelica kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Trelica 'da otomatik olarak oturumunuz açılır. Uygulamalarım portalı hakkında daha fazla bilgi için bkz. [My Apps portalına giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirmek için öğreticiler](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Trelica 'yi deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Trelica 'yi gelişmiş görünürlük ve denetimlerle koruma](/cloud-app-security/proxy-intro-aad)