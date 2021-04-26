---
title: 'Öğretici: TrueChoice ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve TrueChoice arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: jeedes
ms.openlocfilehash: a1e62dfd89ccd23ef950767d883fde80f1a5ca26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953264"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-truechoice"></a>Öğretici: TrueChoice ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, TrueChoice 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Doğruseçimi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, TrueChoice erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak TrueChoice 'a kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* TrueChoice çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* TrueChoice, **SP** tarafından başlatılan SSO 'yu destekler.

* TrueChoice **, tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="adding-truechoice-from-the-gallery"></a>Galeriden TrueChoice ekleniyor

TrueChoice 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize TrueChoice eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **truechoice** yazın.
1. Sonuçlar panelinden **Truechoice** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-truechoice"></a>TrueChoice için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak, Truechoice Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında TrueChoice arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu TrueChoice ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[TrueChoice SSO 'Yu yapılandırın](#configure-truechoice-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan TrueChoice 'da B. Simon 'ya sahip olmak için, **[truechoice test kullanıcısı oluşturun](#create-truechoice-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Truechoice** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:


    a. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`urn:amazon:cognito:sp:<TRUECHOICE_APPID>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<APP>.auth.us-east-2.amazoncognito.com/saml2/idpresponse`

    c. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<APP>.auth.us-east-2.amazoncognito.com/login?response_type=code&client_id=<ID>&redirect_uri=https://<APP_ID>.amplifyapp.com/auth/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'si ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [Truechoice istemci desteği ekibine](mailto:helpdesk@truechoice.io) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. TrueChoice uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki seçeneğe ek olarak, TrueChoice uygulaması aşağıda gösterilen SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name |  Kaynak özniteliği|
    | ----------------- | --------- |
    | ülke | Kullanıcı. ülke |
    | name | User. DisplayName |
    |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)
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

Bu bölümde, TrueChoice 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Truechoice**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-truechoice-sso"></a>TrueChoice SSO 'yu Yapılandır

**Truechoice** tarafında çoklu oturum açmayı yapılandırmak Için, **uygulama Federasyon meta veri URL 'sini** [truechoice destek ekibine](mailto:helpdesk@truechoice.io)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-truechoice-test-user"></a>TrueChoice test kullanıcısı oluştur

Bu bölümde, Britta Simon adlı bir Kullanıcı TrueChoice içinde oluşturulmuştur. TrueChoice, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten TrueChoice 'de yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz TrueChoice oturum açma URL 'sine yönlendirecektir. 

* Doğrudan seçimizin oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki TrueChoice kutucuğuna tıkladığınızda, bu, TrueChoice oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

TrueChoice yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).