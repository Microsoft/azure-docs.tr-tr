---
title: 'Öğretici: bolluk ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve bolluk arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 6e2428967b8e3b4c677752955ea743c5b7d144e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Öğretici: bolluk ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, bolluğu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Bolluk 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de bolluk erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte çalışmak üzere otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bolluk çoklu oturum açma (SSO) etkin abonelik.

> [!NOTE]
> Tek bir kiracıda birden fazla bolluk örneğiyle tümleştirmeniz gerekiyorsa, her bir uygulama için tanımlayıcı bir değişken olabilir.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Bolluk, **SP** tarafından başlatılan SSO 'yu destekler
* Bolluk **, tam zamanında** Kullanıcı sağlamayı destekler
* Bolluk [ **Otomatik** Kullanıcı sağlamasını destekler](./slack-provisioning-tutorial.md)

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-slack-from-the-gallery"></a>Galeriden bolluk ekleme

Bolluk 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize bolluk eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **bolluk** yazın.
1. Sonuçlar panelinden **bolluk** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Bolluk için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu bolluk ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bolluk içinde ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu bolluk ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Bolluk SSO 'Yu yapılandırma](#configure-slack-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcı için Azure AD gösterimine bağlı olan bolluk 'de B. Simon 'a sahip olmak için **[bolluk test kullanıcısı oluşturun](#create-slack-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **bolluk** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`https://slack.com`
    
    c. **Yanıt URL 'si** IÇIN aşağıdaki URL örüntüsünün birini girin:
    
    | Yanıt URL'si|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve yanıt URL 'SI ile güncelleştirmeniz gerekir. Değeri almak için [bolluk istemci destek ekibine](https://slack.com/help/contact) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

    > [!NOTE]
    > Kiracı ile tümleştirmeniz gereken birden fazla bolluk örneğine sahipseniz, **tanımlayıcı (VARLıK kimliği)** değeri bir değişken olabilir. Kalıbı kullanın `https://<DOMAIN NAME>.slack.com` . Bu senaryoda ayrıca aynı değeri kullanarak bolluk içinde başka bir ayarla aynı değeri eşleştirmelidir.

1. Bolluk uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/edit-attribute.png)

1. Bolluk uygulaması, yukarıdakine ek olarak aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz. Özniteliği de eklemeniz gerekir `email` . Kullanıcının bir e-posta adresi yoksa, **emapostaadresi** ' ni **User. UserPrincipalName** ile eşleyin ve **e-postayı** **User. UserPrincipalName** ile eşleyin.

    | Name | Kaynak özniteliği |
    | -----|---------|
    | EmailAddress | User. UserPrincipalName |
    | e-posta | User. UserPrincipalName |

   > [!NOTE]
   > Hizmet sağlayıcısı (SP) yapılandırmasını ayarlamak için SAML yapılandırması sayfasındaki **Gelişmiş Seçenekler** ' ın yanındaki **Genişlet** ' e tıklamanız gerekir. **Hizmet sağlayıcısı veren** kutusunda, çalışma alanı URL 'sini girin. Varsayılan değer slack.com ' dir. 

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Bolluk ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, bolluk erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Slack**'i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-slack-sso"></a>Bolluk SSO 'SU yapılandırma

1. Bu yapılandırmayı bolluk içinde otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **Ayarla** ' ya tıklayın, sizi bolluk uygulamasına yönlendirirler. Buradan, oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Bolluğu el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, bolluk şirket sitenizde yönetici olarak oturum açın.

2. **Microsoft Azure AD** gidin ve **Takım ayarları**' na gidin.

     ![Microsoft Azure AD çoklu oturum açmayı yapılandırma](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. **Takım ayarları** bölümünde **kimlik doğrulama** sekmesine tıklayın ve ardından **Ayarları Değiştir**' e tıklayın.

    ![Takım ayarlarında çoklu oturum açmayı yapılandırma](./media/slack-tutorial/tutorial-slack-authentication.png)

4. **SAML kimlik doğrulama ayarları** iletişim kutusunda, aşağıdaki adımları uygulayın:

    ![SAML kimlik doğrulaması ayarlarında çoklu oturum açmayı yapılandırma](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  **SAML 2,0 uç noktası (http)** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b.  **Kimlik sağlayıcısı veren** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın.

    c.  İndirilen sertifika dosyanızı Not defteri 'nde açın, bu içeriği panonuza kopyalayın ve **ortak sertifika** metin kutusuna yapıştırın.

    d. Yukarıdaki üç ayarı bolluk ekibiniz için uygun şekilde yapılandırın. Ayarlar hakkında daha fazla bilgi için lütfen **bolluk 'ın SSO yapılandırma kılavuzunu** burada bulabilirsiniz. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Uygulama tarafında çoklu oturum açmayı yapılandırma](./media/slack-tutorial/tutorial-slack-expand.png)

    e. **Genişlet** ' e tıklayın ve `https://slack.com` **hizmet sağlayıcı verenin** metin kutusuna girin.

    f.  **Yapılandırmayı kaydet**' e tıklayın.
    
    > [!NOTE]
    > Azure AD ile tümleştirmeniz gereken birden fazla bolluk örneğiniz varsa, `https://<DOMAIN NAME>.slack.com` Azure uygulama **tanımlayıcı** ayarıyla eşleşebilmesi için **hizmet sağlayıcısı veren** olarak ayarlayın.

### <a name="create-slack-test-user"></a>Bolluk test kullanıcısı oluşturma

Bu bölümün amacı, boşluk olarak B. Simon adlı bir Kullanıcı oluşturmaktır. Bolluk, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Henüz yoksa bolluk 'e erişme girişimi sırasında yeni bir Kullanıcı oluşturulur. Bolluk, otomatik Kullanıcı sağlamayı da destekler, Ayrıca, otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](slack-provisioning-tutorial.md) .

> [!NOTE]
> Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [bolluk destek ekibine](https://slack.com/help/contact)başvurmanız gerekir.

> [!NOTE]
> Azure AD Connect, şirket içi Active Directory kimliklerde Azure AD 'ye eşitlenebilen ve bu eşitlenmiş kullanıcıların uygulamaları diğer bulut kullanıcıları gibi kullanabilmesi için eşitleme aracıdır.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz bolluk oturum açma URL 'sine yeniden yönlendirilir.

* Doğrudan bolluk oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarımın bolluk kutucuğuna tıkladığınızda bu işlem, bolluk oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Bolluk yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)