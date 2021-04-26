---
title: 'Öğretici: VERITAS Enterprise Vault.cloud SSO ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve VERITAS Enterprise Vault.cloud SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222291"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Öğretici: VERITAS Enterprise Vault.cloud SSO ile tümleştirme Azure Active Directory

Bu öğreticide, VERITAS Enterprise Vault.cloud SSO 'yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. VERITAS Enterprise Vault.cloud SSO 'yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de VERITAS Enterprise Vault.cloud SSO erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla VERITAS Enterprise Vault.cloud SSO 'ya otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini VERITAS Enterprise Vault.cloud SSO ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* VERITAS Enterprise Vault.cloud SSO çoklu oturum açma aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* VERITAS Enterprise Vault.cloud SSO, **SP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Galeriden VERITAS Enterprise Vault.cloud SSO 'SU ekleme

VERITAS Enterprise Vault.cloud SSO 'yu Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden VERITAS Enterprise Vault.cloud SSO 'SU ile yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **VERıTAS Enterprise Vault.Cloud SSO** yazın.
1. Sonuçlar panelinden **VERITAS Enterprise Vault.Cloud SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>VERITAS Enterprise Vault.cloud SSO için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu verıtas ENTERPRISE Vault.Cloud SSO ile yapılandırın ve test edin. SSO 'nun çalışması için, VERITAS Enterprise Vault.cloud SSO 'daki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu VERITAS Enterprise Vault.cloud SSO ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[VERITAS Enterprise Vault.Cloud SSO SSO 'Yu yapılandırma](#configure-veritas-enterprise-vaultcloud-sso-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Veritas Enterprise **[Vault.Cloud SSO test kullanıcısı oluşturma](#create-veritas-enterprise-vaultcloud-sso-test-user)** -kullanıcının Azure AD gösterimine bağlı olan verıtas ENTERPRISE Vault.Cloud SSO 'da B. Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **VERITAS Enterprise Vault.Cloud SSO** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. **Tanımlayıcı** kutusunda, veri merkezine göre URL 'lerden birini yazın:

    | Veri merkezi| URL |
    |----------|----|
    | Kuzey Amerika| `https://auth.lax.archivecloud.net` |
    | Avrupa | `https://auth.ams.archivecloud.net` |
    | Asya Pasifik| `https://auth.syd.archivecloud.net`|

    c. **Yanıt URL 'si** metin kutusuna, veri merkezine göre URL 'lerden birini yazın:

    | Veri merkezi| URL |
    |----------|----|
    | Kuzey Amerika| `https://auth.lax.archivecloud.net` |
    | Avrupa | `https://auth.ams.archivecloud.net` |
    | Asya Pasifik| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Bu değer gerçek değil. Bu değeri gerçek Sign-On URL 'siyle güncelleştirin. Bu değeri almak için [VERITAS Enterprise Vault.Cloud SSO istemci destek ekibine](https://www.veritas.com/support/.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **VERITAS Enterprise Vault.Cloud SSO 'Yu ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Veritas Enterprise Vault.cloud SSO 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **VERITAS Enterprise Vault.Cloud SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>VERITAS Enterprise Vault.cloud SSO SSO 'yu yapılandırma

**VERITAS enterprise Vault.Cloud SSO** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' den [Veritas Enterprise Vault.Cloud SSO destek ekibine](https://www.veritas.com/support/.html)uygun olarak kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>VERITAS Enterprise Vault.cloud SSO test kullanıcısı oluşturma

Bu bölümde, VERITAS Enterprise Vault.cloud SSO 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. VERITAS Enterprise Vault.cloud SSO platformunda kullanıcıları eklemek için [VERITAS enterprise Vault.Cloud SSO destek ekibi](https://www.veritas.com/support/.html) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz VERITAS Enterprise Vault.cloud SSO oturum açma URL 'sine yeniden yönlendirilir. 

* Veritas Enterprise Vault.cloud SSO oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki VERITAS Enterprise Vault.cloud SSO kutucuğuna tıkladığınızda, bu, VERITAS Enterprise Vault.cloud SSO oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

VERITAS Enterprise Vault.cloud SSO 'yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
