---
title: 'Öğretici Azure Active Directory: SignalFx ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve SignalFx arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 1fbc42864761360d252ed62cea1aef6f2937b599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516078"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Öğretici: SignalFx ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, SignalFx 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SignalFx 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'den SignalFx 'e erişimi olan denetim;
* Kullanıcılarınızın Azure AD hesaplarıyla SignalFx 'e otomatik olarak oturum açmalarına olanak sağlar; '
* Hesaplarınızı tek bir konumda (Azure portal) yönetin.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yapmanız gerekir:

* Azure AD aboneliği
    * Aboneliğiniz yoksa [burada ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SignalFx çoklu oturum açma (SSO) etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SignalFx, **IDP** tarafından başlatılan SSO 'yu destekler
* SignalFx **, tam zamanında** Kullanıcı sağlamasını destekler
* SignalFx 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>1. Adım: Azure 'da SignalFx uygulamasını ekleme

SignalFx uygulamasını yönetilen SaaS uygulamaları listenize eklemek için bu yönergeleri kullanın.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol taraftaki Gezinti penceresinde **Azure Active Directory**' yi seçin.
1. **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. **Yeni uygulama**’yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **signalfx**' i girin ve seçin.
     * Uygulamanın kiracınıza eklenmesi için birkaç dakika beklemeniz gerekebilir.
1. Azure portal açık bırakın ve sonra yeni bir Web sekmesi açın.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>2. Adım: SignalFx SSO yapılandırmasına başla

SignalFx SSO için yapılandırma işlemine başlamak üzere bu yönergeleri kullanın.

1. Yeni açılan sekmesinde, SignalFx Kullanıcı arabirimine erişin ve oturum açın. 
1. Üstteki menüden **tümleştirmeler**' e tıklayın. 
1. Arama alanına **Azure Active Directory** girin ve öğesini seçin.
1. **Yeni tümleştirme oluştur**' a tıklayın.
1. **Ad** alanına kullanıcılarınızın anlayabilmesi için kolayca tanınabilir bir ad girin.
1. **Oturum açma sayfasında göster '** i işaretleyin.
    * Bu özellik, oturum açma sayfasında kullanıcılarınızın tıkladiği özelleştirilmiş bir düğme görüntüler. 
    * **Adında** girdiğiniz bilgiler, düğmesinde görüntülenir. Sonuç olarak, kullanıcılarınızın tanıyacağı bir **ad** girin. 
    * Bu seçenek yalnızca, **YourCompanyName.signalfx.com** gibi signalfx uygulaması için özel bir alt etki alanı kullanıyorsanız çalışır. Özel bir alt etki alanı almak için, SignalFx desteğiyle iletişim kurun. 
1. **TÜMLEŞTIRME kimliğini** kopyalayın. Daha sonraki bir adımda bu bilgilere ihtiyacınız olacaktır. 
1. SignalFx Kullanıcı arabirimini açık bırakın. 

## <a name="step-3-configure-azure-ad-sso"></a>3. Adım: Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu yönergeleri kullanın.

1. [Azure Portal](https://portal.azure.com/)dönün ve **signalfx** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem (düzenleme) simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, aşağıdaki alanları doldurun: 

    a. **Tanımlayıcı** alanına aşağıdaki URL 'yi girin `https://api.<realm>.signalfx.com/v1/saml/metadata` ve `<realm>` signalfx Realm ile değiştirin. 

    b. **Yanıt URL 'si**' nde aşağıdaki URL 'yi girin `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` ve `<realm>` signalfx Realm ile değiştirin ve `<integration ID>` daha önce Signalfx kullanıcı arabiriminden kopyaladığınız **tümleştirme kimliği** ile değiştirin.

1. SignalFx uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. 
    
1. Aşağıdaki taleplerin Active Directory doldurulmuş kaynak özniteliklerle eşlendiğini gözden geçirin ve doğrulayın. 

    | Name |  Kaynak özniteliği|
    | ------------------- | -------------------- |
    | Kullanıcı. FirstName  | Kullanıcı. |
    | User.email  | Kullanıcı. Mail |
    | Personimmutableıd       | User. UserPrincipalName    |
    | User. LastName       | User. soyadı    |

    > [!NOTE]
    > Bu işlem, Active Directory en az bir doğrulanmış özel etki alanı ile yapılandırılmasını ve ayrıca bu etki alanındaki e-posta hesaplarına erişmesini gerektirir. Bu yapılandırma konusunda emin değilseniz veya yardıma ihtiyacınız varsa lütfen SignalFx desteğiyle iletişime geçin.  

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika bulun (base64)** ve ardından **İndir**' i seçin. Sertifikayı indirin ve bilgisayarınıza kaydedin. Ardından, **uygulama Federasyon meta veri URL 'si** değerini kopyalayın; Bu bilgilere, SignalFx Kullanıcı arabirimindeki sonraki bir adımda ihtiyacınız olacak. 

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **SignalFx ayarla** bölümünde, **Azure AD tanımlayıcı** değerini kopyalayın. Bu bilgilere, SignalFx Kullanıcı arabirimindeki sonraki bir adımda ihtiyacınız olacak. 

## <a name="step-4-create-an-azure-ad-test-user"></a>4. Adım: Azure AD test kullanıcısı oluşturma

**B. Simon** adlı Azure Portal bir test kullanıcısı oluşturmak için bu yönergeleri kullanın.

1. Azure portal, sol taraftaki Gezinti penceresinde **Azure Active Directory**' i seçin, ardından **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Sayfanın üst kısmında **Yeni Kullanıcı**' yı seçin.
1. **Kullanıcı** özelliklerinde:
   1. **Kullanıcı adı** alanına, gibi girin `username@companydomain.extension` `b.simon@contoso.com` .
   1. **Ad** alanına girin `B.Simon` .
   1. **Parolayı göster**' i işaretleyin ve ardından görünen değeri **parolada** kopyalayın. Bu tümleştirmeyi test etmek için sonraki adımda bu bilgilere ihtiyacınız olacaktır. 
   1. **Oluştur**’a tıklayın.

## <a name="step-5-assign-the-azure-ad-test-user"></a>5. Adım: Azure AD test kullanıcısını atama

Test kullanıcısına SignalFx için Azure çoklu oturum açma özelliğini kullanmasını sağlamak için bu yönergeleri kullanın.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Signalfx**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve ardından **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon**' ı seçin ve ardından sayfanın alt kısmında **Seç**' e tıklayın.
1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından sayfanın alt kısmındaki **Seç** ' e tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata**' ya tıklayın.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>6. Adım: SignalFx SSO yapılandırmasını doldurun 

1. Önceki sekmeyi açın ve geçerli Azure Active Directory tümleştirme sayfasını görüntülemek için SignalFx Kullanıcı arabirimine dönün. 
1. **Sertifika (base64)** seçeneğinin yanındaki **dosyayı karşıya yükle**' ye tıklayın ve ardından Azure Portal daha önce indirdiğiniz **Base64 kodlamalı sertifika** dosyasını bulun.
1. **Azure AD tanımlayıcısı**' nın yanında, daha önce KOPYALADıĞıNıZ **Azure ad tanımlayıcı** değerini Azure Portal yapıştırın. 
1. **Federasyon meta verileri URL 'sinin** yanında, daha önce Azure Portal kopyaladığınız **uygulama Federasyon meta veri URL 'si** değerini yapıştırın. 
1. **Kaydet**’e tıklayın.

## <a name="step-7-test-sso"></a>7. Adım: test SSO 'SU

SSO 'yu test etme ile ilgili olarak aşağıdaki bilgileri ve SignalFx ' de ilk kez oturum açma beklentilerini gözden geçirin. 

### <a name="test-logins"></a>Test oturumları

* Oturumu test etmek için bir özel/bilito penceresi kullanmanız veya Azure portal oturumunuzu kapatabilirsiniz. Aksi takdirde, uygulamayı yapılandıran kullanıcının tanımlama bilgileri test kullanıcısına başarılı bir oturum açma işlemi yapılmasını önler ve engeller.

* Yeni bir test kullanıcısı ilk kez oturum açtığında, Azure bir parola değişikliğine zorlayacaktır. Bu gerçekleştiğinde, SSO oturum açma işlemi tamamlanmayacak; test kullanıcısı Azure portal yönlendirilir. Sorunu gidermek için, test kullanıcısı parolasını değiştirmeli ve SignalFx oturum açma sayfasına ya da erişim paneline gitmeli ve yeniden denemelidir.
    * Erişim panelinde SignalFx kutucuğuna tıkladığınızda, SignalFx ' de otomatik olarak oturum açmış olmanız gerekir. 
        * Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

* SignalFx uygulamasına erişim panelinden veya kuruluşa atanmış özel bir oturum açma sayfası aracılığıyla erişilebilir. Test kullanıcısı bu konumdan başlayarak tümleştirmeyi test etmelidir.
    * Test kullanıcısı **b. Simon \@ contoso.com** için bu işlemde daha önce oluşturulan kimlik bilgilerini kullanabilir.

### <a name="first-time-logins"></a>İlk kez oturum açma işlemleri

* Kullanıcı, SAML SSO 'dan ilk kez SignalFx 'e oturum açtığında, Kullanıcı bir bağlantı içeren bir SignalFx e-postası alır. Kullanıcı, kimlik doğrulama amacıyla bağlantıya tıklamalıdır. Bu e-posta doğrulaması yalnızca ilk kullanıcılara uygulanır. 

* SignalFx, **tam zamanında** Kullanıcı oluşturmayı destekler. Bu, bir Kullanıcı signalfx 'te yoksa, Kullanıcı hesabının ilk oturum açma denemesinden oluşturulması anlamına gelir.

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Azure AD ile SignalFx 'i deneyin](https://aad.portal.azure.com/)