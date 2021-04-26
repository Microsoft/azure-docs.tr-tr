---
title: 'Öğretici: yeni relik ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve yeni relik arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 8c0ffe4affb6b30f2e2a1aa97a0f4795c130f59b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517615"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Öğretici: yeni relik ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile yeni bir relik tümleştirmeyi nasıl tümleştirileceğini öğreneceksiniz. Yeni bir depoyu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de yeni relik erişimine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla yeni bir depoda otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için gerekli olanlar:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) için etkinleştirilen yeni bir relik aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Yeni relik, hizmet sağlayıcısı ya da kimlik sağlayıcısı tarafından başlatılan SSO 'yu destekler.

## <a name="add-new-relic-from-the-gallery"></a>Galeriden yeni relik ekleme

Yeni relik 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize **Yeni relik (kuruluşa göre)** eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**  >  **Yeni uygulama**' yı seçin.
1. **Azure AD galerisine gözatamıyorum** sayfasında, arama kutusuna **Yeni relik (kuruluşa göre)** yazın.
1. Sonuçlardan **Yeni relik (kuruluşa göre)** öğesini seçin ve ardından **Oluştur**' u seçin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Yeni relik için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu yeni relik ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında yeni bir relik ile bağlantılı bir ilişki kurmanız gerekir.

Azure AD SSO 'yu yeni relik ile yapılandırmak ve test etmek için:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
   1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
   1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Yeni relik tarafında çoklu oturum açma ayarlarını yapılandırmak için [Yeni RELIK SSO 'Yu yapılandırın](#configure-new-relic-sso) .
   1. Yeni [bir relik test kullanıcısı oluşturun](#create-a-new-relic-test-user) ve bu, Azure AD kullanıcısına bağlı yeni relik 'de B. Simon için bir karşılığına sahip olacak.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. **Kuruluşa göre yeni relik** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun. Azure Portal Sonra **Çoklu oturum açma** seçeneğini belirleyin.

1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Kalem simgesi vurgulanmış şekilde, SAML ile tek Sign-On ayarlama ekran görüntüsü.](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde **tanımlayıcı** ve **yanıt URL 'si** değerlerini girin.

   * Yeni Relü **kuruluş uygulamamı** kullanarak bu değerleri alın. Bu uygulamayı kullanmak için:
      1. Yeni relik ['de oturum açın](https://login.newrelic.com/) .
      1. Üstteki menüden **uygulamalar**' ı seçin.
      1. **Uygulamalarınız** bölümünde **kuruluş**  >  **kimlik doğrulama etki alanları**' nı seçin.
      1. Azure AD SSO 'nun bağlanmasını istediğiniz kimlik doğrulama etki alanını seçin (birden fazla kimlik doğrulama etki alanınız varsa). Çoğu şirketin **varsayılan** olarak yalnızca bir kimlik doğrulama etki alanı vardır. Yalnızca bir kimlik doğrulama etki alanı varsa, hiçbir şey belirlemeniz gerekmez.
      1. **Kimlik doğrulama** bölümünde **onaylama tüketici URL 'SI** , **yanıt URL**'si için kullanılacak değeri içerir.
      1. **Kimlik doğrulama** bölümünde, **varlık kimliğiniz** **tanımlayıcı** için kullanılacak değeri içerir.

1. **Kullanıcı öznitelikleri & talepler** bölümünde, **benzersiz kullanıcı tanımlayıcısının** yeni relik sırasında kullanılan e-posta adresini içeren bir alanla eşlendiğinden emin olun.

   * Varsayılan alan olan **User. UserPrincipalName** , değerleri yeni relik e-posta adresleriyle aynıysa sizin için çalışacaktır.
   * User. **userPrincipalName** yeni relik e-posta adresi değilse, **Kullanıcı. Mail** alanı sizin için daha iyi çalışabilir.

1. **SAML Imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalayın ve daha sonra kullanmak üzere değerini kaydedin.

1. **Kuruluşa göre yeni relik ayarla** bölümünde, **oturum açma URL 'sini** kopyalayın ve daha sonra kullanmak üzere değerini kaydedin.

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

Bu bölümde, yeni relik 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Yeni relik**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-new-relic-sso"></a>Yeni relik SSO 'yu yapılandırma

Yeni relik 'de SSO 'yu yapılandırmak için bu adımları izleyin.

1. Yeni relik ['de oturum açın](https://login.newrelic.com/) .

1. Üstteki menüden **uygulamalar**' ı seçin.

1. **Uygulamalarınız** bölümünde **kuruluş**  >  **kimlik doğrulama etki alanları**' nı seçin.

1. Azure AD SSO 'nun bağlanmasını istediğiniz kimlik doğrulama etki alanını seçin (birden fazla kimlik doğrulama etki alanınız varsa). Çoğu şirketin **varsayılan** olarak yalnızca bir kimlik doğrulama etki alanı vardır. Yalnızca bir kimlik doğrulama etki alanı varsa, hiçbir şey belirlemeniz gerekmez.

1. **Kimlik doğrulama** bölümünde **Yapılandır**' ı seçin.

   1. **SAML meta verileri kaynağı** için, daha önce Azure AD **uygulama Federasyon meta veri URL 'si** alanından kaydettiğiniz değeri girin.

   1. **SSO hedef URL 'si** için, daha önce Azure AD **oturum açma URL 'si** alanından kaydettiğiniz değeri girin.

   1. Ayarların hem Azure AD hem de yeni relik tarafında iyi görünceyi doğruladıktan sonra **Kaydet**' i seçin. Her iki taraf da düzgün yapılandırılmamışsa kullanıcılarınız yeni bir relik 'de oturum açamaz.

### <a name="create-a-new-relic-test-user"></a>Yeni bir relik test kullanıcısı oluşturma

Bu bölümde, yeni relik 'de B. Simon adlı bir Kullanıcı oluşturacaksınız.

1. Yeni relik ['de oturum açın](https://login.newrelic.com/) .

1. Üstteki menüden **uygulamalar**' ı seçin.

1. **Uygulamalarınız** bölümünde **Kullanıcı yönetimi**' ni seçin.

1. **Kullanıcı ekle**'yi seçin.

   1. **Ad** için **B. Simon** girin.
   
   1. **E-posta** Için, Azure AD SSO tarafından gönderilecek değeri girin.
   
   1. Kullanıcı için bir Kullanıcı **türü** ve Kullanıcı **grubu** seçin. Bir test kullanıcısı için, türü ve **Kullanıcı** grubu Için **temel Kullanıcı** makul seçimlerdir.
   
   1. Kullanıcıyı kaydetmek için **Kullanıcı Ekle**' yi seçin.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz yeni relik oturum açma URL 'sine yönlendirecektir.  

* Yeni relik oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız yeni relik için otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda yeni relik kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız yeni relik için otomatik olarak oturum açmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Yeni yeniden oluşturma 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
