---
title: 'Öğretici: RStudio Connect SAML kimlik doğrulaması ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve RStudio Connect SAML kimlik doğrulaması arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182402"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Öğretici: RStudio Connect SAML kimlik doğrulaması ile tümleştirme Azure Active Directory

Bu öğreticide, RStudio Connect SAML kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
RStudio Connect SAML kimlik doğrulamasını Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* RStudio Connect SAML kimlik doğrulamasına erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla SAML kimlik doğrulamasını (çoklu oturum açma) bağlamak için kullanıcılarınızın otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini RStudio Connect SAML kimlik doğrulamasıyla yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* RStudio SAML kimlik doğrulamasını bağlayın. [45 günlük ücretsiz değerlendirme vardır.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* RStudio Connect SAML kimlik doğrulaması **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

* RStudio Connect SAML kimlik doğrulaması **yalnızca zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>Galeriden RStudio Connect SAML kimlik doğrulaması ekleme

RStudio Connect SAML kimlik doğrulamasını Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden RStudio Connect SAML kimlik doğrulaması ' nı galerisinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **RSTUDIO bağlantı SAML kimlik doğrulaması** yazın.
1. Rstudio, sonuçlar panelinden **SAML kimlik doğrulamasını bağla** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>RStudio Connect SAML kimlik doğrulaması için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak rstudio Connect SAML kimlik doğrulaması Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve RStudio Connect SAML kimlik doğrulaması içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'Yu RStudio Connect SAML kimlik doğrulamasıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. **[RStudio Connect SAML kimlik doğrulama SSO](#configure-rstudio-connect-saml-authentication-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için yapılandırın.
    * Rstudio **[Connect SAML kimlik doğrulaması test kullanıcısı](#create-rstudio-connect-saml-authentication-test-user)** -Kullanıcı Azure AD gösterimine bağlı olan RSTUDIO Connect SAML kimlik doğrulaması ' nda Britta Simon 'un bir karşılığı olmalıdır.
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **rstudio SAML kimlik doğrulaması** Uygulama tümleştirmesini bul sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak Istiyorsanız aşağıdaki adımları uygulayarak `<example.com>` rstudio Connect SAML kimlik doğrulama sunucusu adresiniz ve bağlantı noktasıyla değiştirin:

    ![RStudio bağlantı SAML kimlik doğrulaması etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<example.com>/__login__/saml`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<example.com>/__login__/saml/acs`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![RStudio Connect SAML kimlik doğrulaması meta verilerini karşıya yükleme](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<example.com>/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bunlar RStudio Connect SAML kimlik doğrulama sunucusu adresinden ( `https://example.com` Yukarıdaki örneklerde) belirlenir. Sorun yaşıyorsanız [rstudio Connect SAML kimlik doğrulaması destek ekibine](mailto:support@rstudio.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. RStudio Connect SAML kimlik doğrulama uygulamanız SAML onayları için belirli bir biçimde bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName** ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. RStudio Connect SAML kimlik doğrulaması uygulaması **NameIdentifier** 'ın **User. Mail** ile eşlenmesini bekliyor, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

7. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

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

Bu bölümde, RStudio Connect SAML kimlik doğrulamasına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **rstudio Connect SAML kimlik doğrulaması**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>RStudio Connect SAML kimlik doğrulama SSO 'yu yapılandırma

**Rstudio Connect SAML kimlik doğrulaması** için çoklu oturum açma 'yı yapılandırmak için yukarıda kullanılan **uygulama Federasyon meta verileri URL 'Sini** ve **sunucu adresini** kullanmanız gerekir. Bu, üzerindeki RStudio Connect SAML kimlik doğrulaması yapılandırma dosyasında yapılır `/etc/rstudio-connect.rstudio-connect.gcfg` .

Bu örnek bir yapılandırma dosyasıdır:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

**Sunucu adresinizi** , değerinde `Server.Address` ve **uygulama Federasyon meta veri URL 'sini** değerinde depolayın `SAML.IdPMetaData` . Bu örnek yapılandırmanın şifrelenmemiş bir HTTP bağlantısı kullandığını ve Azure AD 'nin şifreli bir HTTPS bağlantısının kullanılmasını gerektirdiğini unutmayın. RStudio Connect SAML kimlik doğrulamasının önünde bir [ters proxy](https://docs.rstudio.com/connect/admin/proxy/) kullanabilir veya RSTUDIO Connect SAML kimlik DOĞRULAMASıNı [doğrudan https kullanacak](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS)şekilde yapılandırabilirsiniz. 

Yapılandırma ile ilgili sorun yaşıyorsanız, yardım için [rstudio Connect SAML kimlik doğrulaması yönetici kılavuzunu](https://docs.rstudio.com/connect/admin/authentication/saml/) okuyabilir veya [rstudio destek ekibine](mailto:support@rstudio.com) e-posta gönderebilirsiniz.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>RStudio Connect SAML kimlik doğrulaması test kullanıcısı oluştur

Bu bölümde, RStudio Connect SAML kimlik doğrulaması ' nda Britta Simon adlı bir Kullanıcı oluşturulur. RStudio Connect SAML kimlik doğrulaması, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten RStudio Connect SAML kimlik doğrulaması 'nda yoksa, RStudio Connect SAML kimlik doğrulamasına erişmeye çalıştığınızda yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz RStudio Connect SAML kimlik doğrulaması oturum açma URL 'sine yeniden yönlendirilir.  

* RStudio SAML kimlik doğrulaması oturum açma URL 'sine doğrudan bağlanın ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız rstudio Connect SAML kimlik doğrulamasında otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft Access panel ' i de kullanabilirsiniz. Erişim panelinde RStudio Connect SAML kimlik doğrulaması kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'Yu ayarladığınız RStudio Connect SAML kimlik doğrulamasında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

RStudio Connect SAML kimlik doğrulamasını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).