---
title: 'Öğretici: GitHub Enterprise Cloud-Enterprise hesabıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile GitHub Enterprise Cloud Enterprise hesabı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650039"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Öğretici: GitHub Enterprise Cloud-Enterprise hesabıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, GitHub Enterprise Cloud-Enterprise hesabını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. GitHub Enterprise Cloud-Enterprise hesabını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de bir GitHub kurumsal hesabına ve kurumsal hesap içindeki kuruluşlara erişimi olan denetim.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* [GitHub Enterprise hesabı](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts).
* Kurumsal hesap sahibi olan bir GitHub Kullanıcı hesabı. 

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* GitHub Enterprise Cloud-Enterprise hesabı **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler.
* GitHub Enterprise Cloud-Enterprise hesabı **, tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Galeriden GitHub Enterprise Cloud-Enterprise hesabı ekleme

GitHub Enterprise Cloud-Enterprise hesabının tümleştirmesini Azure AD ile yapılandırmak için, Galeriden GitHub Enterprise Cloud-Enterprise hesabını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **GitHub Enterprise Cloud-Enterprise hesabı** yazın.
1. Sonuçlar panelinden **GitHub Enterprise Cloud-Enterprise hesabı** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>GitHub Enterprise Cloud-Enterprise hesabı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu GitHub Enterprise Cloud-Enterprise hesabıyla yapılandırın ve test edin. SSO 'nun çalışması için, GitHub Enterprise Cloud-Enterprise hesabındaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu GitHub Enterprise Cloud-Enterprise hesabıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. **[Azure AD kullanıcısını ve test kullanıcı hesabınızı GitHub uygulamasına atayın](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** -kullanıcı hesabınızı ve test KULLANıCıSıNı `B.Simon` Azure AD çoklu oturum açma özelliğini kullanacak şekilde etkinleştirin.
1. **[Kurumsal hesap ve kuruluşları IÇIN SAML 'Yi etkinleştirin ve test](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** edin-uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[, başka bir kurumsal hesap sahibi veya kuruluş üyesi HESABıYLA SSO 'Yu test](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** edin.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **GitHub Enterprise Cloud-Enterprise hesabı** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

     **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > `<ENTERPRISE-SLUG>`GitHub Enterprise hesabınızın gerçek adıyla değiştirin.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificateBase64.png)

1. **GitHub Enterprise Cloud-Enterprise hesabını ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, adlı Azure portal bir test kullanıcısı oluşturacaksınız `B.Simon` .

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Azure AD kullanıcısını ve test kullanıcı hesabınızı GitHub uygulamasına atama

Bu bölümde, `B.Simon` GitHub Enterprise Cloud-Enterprise hesabına erişim vererek kullanıcı hesabınızı Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **GitHub Enterprise Cloud-Enterprise hesabı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ve kullanıcı hesabınızı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Kurumsal hesap ve kuruluşları için SAML 'yi etkinleştirin ve test edin

**GitHub Enterprise Cloud-Enterprise hesap** tarafında çoklu oturum açmayı yapılandırmak Için [Bu GitHub belgelerinde](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account)listelenen adımları izleyin. 
1. GitHub.com 'de [Kurumsal hesap sahibi](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner)olan bir kullanıcı hesabıyla oturum açın. 
1. `Login URL`Uygulamadaki alandan değeri Azure Portal kopyalayın ve `Sign on URL` GitHub ENTERPRISE hesabı SAML ayarlarındaki alana yapıştırın. 
1. `Azure AD Identifier`Uygulamadaki alandan değeri Azure Portal kopyalayın ve `Issuer` GitHub ENTERPRISE hesabı SAML ayarlarındaki alana yapıştırın. 
1. Azure portal yukarıdaki adımlarda indirdiğiniz **sertifika (base64)** dosyasının içeriğini kopyalayın ve GitHub ENTERPRISE hesabı SAML ayarlarındaki uygun alana yapıştırın. 
1. Öğesine tıklayın `Test SAML configuration` ve GitHub Enterprise hesabından Azure AD 'ye başarıyla kimlik doğrulayacağınızı doğrulayın.
1. Test başarılı olduktan sonra ayarları kaydedin. 
1. GitHub Enterprise hesabından ilk kez SAML aracılığıyla kimlik doğrulamasından geçtikten sonra, GitHub Enterprise hesabında, oturum açmış GitHub Kullanıcı hesabını Azure AD kullanıcı hesabıyla ilişkilendiren bir _bağlı dış kimlik_ oluşturulur.  
 
GitHub Enterprise hesabınız için SAML SSO 'yu etkinleştirdikten sonra, kurumsal hesabınızın sahip olduğu tüm kuruluşlar için SAML SSO varsayılan olarak etkindir. Üye oldukları kuruluşlara erişim kazanmak için SAML SSO kullanarak kimlik doğrulaması yapmak için tüm üyelerin doğrulanması gerekir ve kurumsal bir hesaba erişirken SAML SSO kullanarak kimlik doğrulaması yapmak için kuruluş sahipleri gerekecektir.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Başka bir kurumsal hesap sahibi veya kuruluş üyesi hesabıyla SSO 'yu test etme

SAML tümleştirme, GitHub Enterprise hesabı için (Ayrıca Enterprise hesabındaki GitHub kuruluşları için de geçerli olur) ayarlandıktan sonra, Azure AD 'de uygulamaya atanan diğer kurumsal hesap sahipleri GitHub kurumsal hesap URL 'sine () gidebilmelidir `https://github.com/enterprises/<enterprise account>` , SAML aracılığıyla kimlik doğrulaması yapabilir ve GitHub Enterprise hesabı altındaki ilkelere ve ayarlara erişebilir. 

Kuruluş hesabındaki bir kuruluşun sahibi [, bir kullanıcıyı GitHub kuruluşuna katılmaya davet](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization)edebilmelidir. GitHub.com 'de bir kuruluş sahibi hesabıyla oturum açın ve kuruluşa davet etmek için makaledeki adımları izleyin `B.Simon` . Zaten mevcut değilse GitHub Kullanıcı hesabının oluşturulması gerekir `B.Simon` . 

Test kullanıcısı hesabı ile kurumsal hesap altında GitHub kuruluş erişimini test etmek için `B.Simon` :
1. Kuruluş `B.Simon` hesabı altında kuruluş sahibi olarak bir kuruluşa davet edin. 
1. Azure AD Kullanıcı hesabına bağlamak istediğiniz kullanıcı hesabını kullanarak GitHub.com 'de oturum açın `B.Simon` .
1. Kullanıcı hesabını kullanarak Azure AD 'de oturum açın `B.Simon` .
1. GitHub kuruluşuna gidin. Kullanıcıdan SAML aracılığıyla kimlik doğrulaması yapması istenir. Başarılı SAML kimlik doğrulamasından sonra `B.Simon` kuruluş kaynaklarına erişebilmelidir. 

## <a name="next-steps"></a>Sonraki adımlar

GitHub Enterprise Cloud-Enterprise hesabını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
