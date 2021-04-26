---
title: 'Öğretici Azure Active Directory: Siark SAML kimlik doğrulamasıyla çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ile Siark SAML kimlik doğrulaması arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: afe340461386f9a864d0777294c1f922aa73f064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648331"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cyberark-saml-authentication"></a>Öğretici: Siark SAML kimlik doğrulamasıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Siark SAML kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Siark SAML kimlik doğrulamasını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Siark SAML kimlik doğrulamasına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Siark SAML kimlik doğrulaması için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Siark SAML kimlik doğrulaması çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Siark SAML kimlik doğrulaması **SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-cyberark-saml-authentication-from-the-gallery"></a>Galeriden Siark SAML kimlik doğrulaması ekleme

Siark SAML kimlik doğrulamasının Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Siark SAML kimlik doğrulaması eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Siark SAML kimlik doğrulaması** yazın.
1. Sonuçlar panelinden **Siark SAML kimlik doğrulaması** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-cyberark-saml-authentication"></a>Siark SAML kimlik doğrulaması için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak SIARK SAML kimlik DOĞRULAMASıYLA Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı ile Siark SAML kimlik doğrulaması arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Siark SAML kimlik doğrulamasıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Siark SAML kimlik doğrulama SSO 'Yu yapılandırın](#configure-cyberark-saml-authentication-sso)** .
    * User 'ın Azure AD gösterimine bağlı olan Siark SAML kimlik doğrulamasında B. Simon 'ya sahip olmak için, **[Siark SAML kimlik doğrulaması test kullanıcısı oluşturun](#create-cyberark-saml-authentication-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Cyberark SAML kimlik doğrulaması** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<PVWA DNS or IP>/passwordvault/api/auth/saml/logon`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<PVWA DNS or IP>/PasswordVault/v10/logon/saml`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'si ve Sign-On URL 'siyle güncelleştirin. Bu değerleri almak için [Siark SAML kimlik doğrulama istemci destek ekibine](mailto:bizdevtech@cyberark.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **CyberArk SAML kimlik doğrulamasını ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Siark SAML kimlik doğrulamasına erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Cyberark SAML kimlik doğrulaması**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-cyberark-saml-authentication-sso"></a>Siark SAML kimlik doğrulama SSO 'SU yapılandırma

**Siark SAML kimlik doğrulama** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' den [siark SAML kimlik doğrulama desteği ekibine](mailto:bizdevtech@cyberark.com)uygun şekilde kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-cyberark-saml-authentication-test-user"></a>Siark SAML kimlik doğrulaması test kullanıcısı oluştur

Bu bölümde, Siark SAML kimlik doğrulamasında B. Simon adlı bir Kullanıcı oluşturacaksınız. CyberArk SAML kimlik doğrulama platformunda kullanıcıları eklemek için [SIARK SAML kimlik doğrulaması destek ekibi](mailto:bizdevtech@cyberark.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum akışını başlatabileceğiniz Siark SAML kimlik doğrulaması oturum açma URL 'sine yeniden yönlendirilir.  

* Siark SAML kimlik doğrulaması oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız SIARK SAML kimlik doğrulamasında otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki CyberArk SAML kimlik doğrulaması kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Siark SAML kimlik doğrulamasında otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

CyberArk SAML kimlik doğrulamasını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).