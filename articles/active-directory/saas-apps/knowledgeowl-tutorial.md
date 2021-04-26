---
title: 'Öğretici: KnowledgeOwl ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve KnowledgeOwl arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: c1c53da8c62acc1b4aacd2b0fa79dd968c65c13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645646"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Öğretici: KnowledgeOwl ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, KnowledgeOwl 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. KnowledgeOwl 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de KnowledgeOwl erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla KnowledgeOwl için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* KnowledgeOwl çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* KnowledgeOwl **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.
* KnowledgeOwl **, tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="add-knowledgeowl-from-the-gallery"></a>Galeriden KnowledgeOwl ekleme

KnowledgeOwl 'nin tümleştirmesini Azure AD 'ye yapılandırmak için, Galeriden KnowledgeOwl 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **knowledgeowl** yazın.
1. Sonuçlar panelinden **Knowledgeowl** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-knowledgeowl"></a>KnowledgeOwl için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak KnowledgeOwl Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve KnowledgeOwl içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu KnowledgeOwl ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[KnowledgeOwl SSO 'Yu yapılandırın](#configure-knowledgeowl-sso)** .
    1. Knowledgeowl **[test kullanıcısı oluşturma](#create-knowledgeowl-test-user)** -kullanıcının Azure AD gösterimine bağlı olan knowledgeowl 'de B. Simon 'ın bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Knowledgeowl** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki desenlerden birini kullanarak URL 'yi yazın:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. **Yanıt URL** 'si metin kutusuna aşağıdaki desenlerden birini kullanarak URL 'yi yazın:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki desenlerden birini kullanarak URL 'yi yazın:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değeri, öğreticide daha sonra açıklanan gerçek tanımlayıcı, yanıt URL 'SI ve Sign-On URL 'den güncelleştirmeniz gerekir.

1. KnowledgeOwl uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Ayrıca, KnowledgeOwl uygulaması, daha fazla özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.

    | Name | Kaynak özniteliği | Ad Alanı |
    | ------------ | -------------------- | -----|
    | ssoıd | Kullanıcı. Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **sertifika (ham)** bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

1. **Set up KnowledgeOwl** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, KnowledgeOwl erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Knowledgeowl** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-knowledgeowl-sso"></a>KnowledgeOwl SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, KnowledgeOwl şirket sitenizde yönetici olarak oturum açın.

1. **Ayarlar** ' a ve ardından **güvenlik**' i seçin.

    ![Ekran görüntüsü ayarlar menüsünden Seçili güvenlik ' i gösterir.](./media/knowledgeowl-tutorial/configure-1.png)

1. **SAML SSO tümleştirmesi** ' ne gidin ve aşağıdaki adımları gerçekleştirin:

    ![Ekran görüntüsünde, burada açıklanan değişiklikleri yapabileceğiniz SAML S S O tümleştirmesi gösterilmektedir.](./media/knowledgeowl-tutorial/configure-2.png)

    a. **SAML SSO 'Yu etkinleştir**' i seçin.

    b. **SP VARLıK kimliği** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** BÖLÜMÜNDEKI **tanımlayıcıya (varlık kimliği)** yapıştırın.

    c. **SP oturum açma URL 'si** değerini kopyalayın ve Azure Portal ÜZERINDEKI **temel SAML yapılandırması** bölümünde bulunan **oturum açma URL 'si ve yanıt URL** 'si metin kutularına yapıştırın.

    d. **IDP EntityId** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    e. **IDP oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    f. **IDP oturum kapatma URL 'si** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    örneğin: **Yükleme IDP sertifikasına** tıklayarak Azure Portal indirilen sertifikayı karşıya yükleyin.

    h. Öznitelikleri eşlemek için **SAML özniteliklerini eşle** ' ye tıklayın ve aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, eşleme SAML özniteliklerini gösterir ve burada açıklanan değişiklikleri yapabilirsiniz.](./media/knowledgeowl-tutorial/configure-3.png)

    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` **SSO kimliği** metin kutusuna girin
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **Kullanıcı adı/e-posta** metin kutusuna girin.
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` **İlk ad** metin kutusuna girin.
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` **Son ad** metin kutusuna girin.
    * **Kaydet**’e tıklayın.

    i. Sayfanın alt kısmındaki **Kaydet**’e tıklayın.

    ![Kaydet düğmesini gösteren ekran görüntüsü.](./media/knowledgeowl-tutorial/configure-4.png)

### <a name="create-knowledgeowl-test-user"></a>KnowledgeOwl test kullanıcısı oluştur

Bu bölümde, KnowledgeOwl içinde B. Simon adlı bir Kullanıcı oluşturulur. KnowledgeOwl, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı KnowledgeOwl 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!Note]
> El ile bir kullanıcı oluşturmanız gerekiyorsa, [Knowledgeowl destek ekibine](mailto:support@knowledgeowl.com)başvurun.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz KnowledgeOwl oturum açma URL 'sine yeniden yönlendirilir.  

* KnowledgeOwl oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız KnowledgeOwl ' de otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki KnowledgeOwl kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız KnowledgeOwl öğesinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

KnowledgeOwl 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).