---
title: 'Öğretici: Maxdentitytıon Manager yazılımıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Maxdentitymanager yazılımı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: 4b57b3fbb338774eb8d66fc4d3c0c817e19aff4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458209"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Öğretici: Maxdentitytıon Manager yazılımıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Maxdentitymanager yazılımını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Maxdentitymanager yazılımını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'yi, Maxdentityi Yöneticisi yazılımı için kullanıcılarınızın kimliğini doğrulamak üzere kullanın
* Kullanıcılarınızın Azure AD hesaplarıyla, Maxdentitymanager yazılımını otomatik olarak oturum açabilmesi için izin verin.


Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Maxdentitymanager yazılımı çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD 'nizi Maxdentityi Yöneticisi yazılımıyla kullanım için yapılandıracaksınız.


* Maxdentitymanager yazılımı **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Galeriden Maxdentityyürütme Yöneticisi yazılımı ekleme

Maxdentitytıon Manager yazılımının tümleştirmesini Azure AD ile yapılandırmak için, Galeriden Maxdentitytıon Manager yazılımını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Maxdentitytıon Manager yazılımı** yazın.
1. Sonuçlar panelinden **Maxlıkçalıştır Yöneticisi yazılımını** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Maxdentitymanager yazılımı için Azure AD çoklu oturum açmayı yapılandırma ve test etme

Azure AD SSO 'yu Maxdentitywith Manager yazılımıyla yapılandırın ve test edin. SSO 'nun çalışması için Azure AD ile Maxdentitytıon Manager yazılımı arasında bir bağlantı kurmanız gerekir.

Azure AD SSO 'yu Maxdentitywith Manager yazılımıyla yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırma](#configure-azure-ad-sso)** -kullanıcılarınızın Maxdentityme Yöneticisi yazılımıyla kullanım için kimlik doğrulaması yapmasını sağlamak için
    1. Kuruluşunuzdaki herkesin kimlik doğrulaması yapabilmesini sağlamak için **[tüm kullanıcıları maxsin ' i kullanacak şekilde atayın](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** .
1. Configuration 'ın çalışıp çalışmadığını ve doğru özniteliklerin yayımlanmadığını doğrulamak için **[Azure AD kurulumunu test](#test-with-maxient)** edin.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Maxdentitytıon Manager yazılım** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama **IDP** tarafından başlatılan modda önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için Max, uygulama/destek temsilcinizle çalışın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.  Bu URL ile Max, uygulama/destek temsilcinizi sağlamanız gerekir.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Tüm kullanıcıları Maxdentityi Yöneticisi yazılımı için kimlik doğrulaması yapabilecek şekilde ata

Bu bölümde, Maxdentitytıon Manager yazılımı için Azure sistemini kullanarak kimlik doğrulaması yapmak üzere tüm hesapların erişimine izin vermiş olursunuz.  Bu adımın doğru çalışması için **gerekli** olduğunu unutmayın.  Max, kullanıcıların *kimliğini doğrulamak* IÇIN Azure AD sisteminizi kullanır. Kullanıcıların *yetkilendirmesi* gerçekleştirmeye çalıştıkları belirli bir Işlev için maxlevtıon sisteminde gerçekleştirilir. Maxdikkat, bu kararları vermek için dizininizdeki öznitelikleri kullanmaz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Max, yönetim Yöneticisi yazılımı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, tüm kullanıcılar (veya uygun gruplar) ' ı seçin ve bunları maxbir kimlik doğrulaması yapabilmeleri için **atayın** .

## <a name="test-with-maxient"></a>Maxbir ile test edin 

Bir destek bileti zaten bir Maxuz uygulama/destek temsilcisiyle açılmadıysa, [support@maxient.com](mailto:support@maxient.com) "kampüs tabanlı kimlik doğrulaması/Azure kurulumu-" konusuna sahip bir e-posta gönderin \<\<School Name\> \> . E-postanın gövdesinde, **uygulama Federasyon meta verileri URL 'sini** sağlayın. Uygun özniteliklerin yayımlanmakta olduğunu doğrulamak için, bir test bağlantısı ile maxıly personeli yanıt verir.  
    
## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Maxbir yönetim Yöneticisi yazılımı deneyin](https://aad.portal.azure.com/)