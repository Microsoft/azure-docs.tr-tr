---
title: 'Öğretici: Prezi ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve PREZI arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 4ff8196ff4e30aa6ed036cabe00a72bfa8a5c51f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515330"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Öğretici: Prezi ile çoklu oturum açma tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile PREZI tümleştirmeyi öğreneceksiniz. Prezi 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de ön Zi erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ön planda otomatik olarak oturum açmalarına olanak sağlayın.
* Azure portal hesaplarınızı yönetin.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) ile etkinleştirilen bir PREZI aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* PREZI, SP ve ıDP tarafından başlatılan SSO 'yu destekler.
* PREZI, tam zamanında Kullanıcı sağlamayı destekler.
* PREZI 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. Daha fazla bilgi için bkz. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-prezi-from-the-gallery"></a>Galeriden PREZI ekleme

PREZI 'nın tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeriden yönetilen SaaS uygulamaları listenize PREZI eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. En soldaki bölmede **Azure Active Directory**' yi seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **PREZI** yazın.
1. Sonuçlar panelinden **PREZI** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>PREZI için Azure AD SSO 'yu yapılandırma ve test etme

B. Simon adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Prezi ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında ön planda bir bağlantı ilişkisi kurarsınız.

Azure AD SSO 'yu PREZI ile yapılandırmak ve test etmek için şu yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. Azure AD SSO 'yu B. Simon ile test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD SSO 'yu kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında SSO ayarlarını yapılandırmak için [PREZI SSO 'Yu yapılandırın](#configure-prezi-sso) .
    1. Kullanıcının Azure AD gösterimine bağlı olan Prezi 'de B. Simon 'ya karşılık gelen bir [PREZI test kullanıcısı oluşturun](#create-a-prezi-test-user) .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için:

1. [Azure Portal](https://portal.azure.com/), **PREZI** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırmasındaki** ayarları düzenlemek için **Düzenle** simgesini seçin.

   ![Temel SAML yapılandırma ayarlarını Düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

1. **Ek URL 'Ler ayarla**' yı seçin ve uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları izleyin:

    **Oturum açma URL** 'si kutusuna URL 'yi girin: `https://prezi.com/login/sso/` .

1. **Kaydet**’i seçin.

1. PREZI uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Kullanıcı öznitelikleri & talepler](common/default-attributes.png)

1. PREZI uygulaması, burada gösterildiği gibi, daha fazla özniteliğin SAML yanıtına geri geçirilmesini de bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name | Kaynak özniteliği|
    | ---------------| --------------- |
    | given_name | Kullanıcı. |
    | family_name | User. soyadı |

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)**. Sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Ön Zi ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal en soldaki bölmede **Azure Active Directory**' i seçin. **Kullanıcılar**' a gidin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. Kullanıcı Özellikleri ' nde şu adımları izleyin:
   1. **Ad** kutusuna **B. Simon** girin.
   1. **Kullanıcı adı** kutusuna, `username@companydomain.extension` Örneğin, girin `B.Simon@contoso.com` .
   1. **Parolayı göster** onay kutusunu seçin. **Parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Prezi 'e erişim vererek B. Simon 'u Azure SSO 'yu kullanmaya etkinleştirirsiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **PREZI** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   ![Kullanıcılar ve gruplar bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** seçeneğini belirleyin ve ekranın altındaki **Seç** ' e tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmında **Seç** ' e tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-prezi-sso"></a>PREZI SSO 'SU yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, ekip hesabınızla ön planda oturum açın ve [Yönetim konsoluna](https://prezi.com/organizations/manage)gidin.

1. **Yönetici konsolundan** **Ayarlar** sekmesini seçin.

    ![Ayarlar sekmesi](./media/prezi-tutorial/settings-image.png)

1. **Tek Sign-On (SSO)** bölümüne gıdın ve SSO 'yu etkinleştirmek için değiştirme 'yi açın.
    
    ![Tek Sign-On (SSO) geçiş](./media/prezi-tutorial/single-signon.png)

1. **Çoklu oturum açma (SSO)** bölümünde, şu adımları izleyin:

    ![Çoklu oturum açma (SSO) bölümü](./media/prezi-tutorial/configuration.png)

    1. **Tanımlayıcı veya veren URL 'si** kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcı** değerini yapıştırın.

    1. **SAML 2,0 uç noktası (http)** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    1. İndirilen **sertifikayı (base64)** Azure Portal Not defteri ' nden açın. Sertifikanın içeriğini kopyalayın ve içeriği **sertifika (X. 509.440)** kutusuna yapıştırın.

    1. **Kaydet**’i seçin.

### <a name="create-a-prezi-test-user"></a>Bir PREZI test kullanıcısı oluşturma

Bu bölümde, arazi 'de Britta Simon adlı bir Kullanıcı oluşturulur. PREZI, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten ön planda yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD SSO yapılandırmanızı test edersiniz.

Erişim panelinde PREZI kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız PREZI hesabında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)
- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)
- [Azure AD ile önceden Zi 'yi deneyin](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)
- [Gelişmiş görünürlük ve denetimlerle ön Zi koruma](/cloud-app-security/proxy-intro-aad)