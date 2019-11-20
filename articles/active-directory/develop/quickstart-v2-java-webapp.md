---
title: Microsoft Identity platform Java Web uygulaması hızlı başlangıç | Mavisi
description: OpenID Connect kullanarak Java Web uygulamasında Microsoft oturum açma 'yı nasıl uygulayacağınızı öğrenin
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 93ae820f8c98b749ef8f71b17bf3d540d7886ed6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832122"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Hızlı başlangıç: Microsoft 'a Java Web uygulamasına oturum açma ekleme

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Bu hızlı başlangıçta, bir Java Web uygulamasını Microsoft Identity platformu ile tümleştirmeyi öğreneceksiniz. Uygulamanız bir kullanıcıya oturum açacaktır, Microsoft Graph API 'sini çağırmak için bir erişim belirteci alır ve Microsoft Graph API 'sine bir istek yapar.

Bu hızlı başlangıcı tamamladığınızda, uygulamanız kişisel Microsoft hesaplarının (outlook.com, live.com ve diğerleri dahil) oturum açma işlemlerini ve Azure Active Directory kullanan herhangi bir şirketten veya kuruluştan iş veya okul hesaplarını kabul eder.

![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Ön koşullar

Bu örneği çalıştırmak için şunlar gerekir:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 veya üzeri ve [Maven](https://maven.apache.org/).
- Azure Active Directory (Azure AD) kiracısı. Azure AD kiracısı alma hakkında daha fazla bilgi için bkz. [Azure AD kiracısı alma](https://azure.microsoft.com/documentation/articles/active-directory-howto-tenant/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı kaydetme ve indirme
> Hızlı başlangıç uygulamanızı başlatmak için iki seçeneğiniz vardır: Express (seçenek 1) veya el ile (seçenek 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. Seçenek: Uygulamanızı otomatik olarak kaydedip yapılandırın ve ardından kod örneğinizi indirin
>
> 1. [Azure portal uygulama kayıtları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)gidin.
> 1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
> 1. Yeni uygulamanızı indirip otomatik olarak yapılandırmak için yönergeleri izleyin.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma
>
> #### <a name="step-1-register-your-application"></a>1\. Adım: Uygulamanızı kaydetme
>
> Uygulamanızı kaydetmek ve uygulamanın kayıt bilgilerini çözümünüze el ile eklemek için şu adımları izleyin:
>
> 1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
> 1. Hesabınız birden fazla Azure AD kiracısına erişim sunuyorsa sağ üst köşeden hesabınızı seçin ve portal oturumunuzu kullanmak istediğiniz Azure AD kiracısına ayarlayın.
>
> 1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](/azure/active-directory/develop/) sayfasına gidin.
> 1. **Yeni kayıt**seçeneğini belirleyin.
> 1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
>    - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `java-webapp`.
>    - **Yeniden yönlendirme URI 'sini** Şu anda boş bırakın ve **Kaydet**' i seçin.
> 1. **Genel bakış** sayfasında uygulamanın **uygulama (istemci) kimliğini** ve **Dizin (kiracı) kimliği** değerlerini bulun. Daha sonra bu değerleri kopyalayın.
> 1. Menüden **kimlik doğrulamasını** seçin ve ardından aşağıdaki bilgileri ekleyin:
>    - **Yeniden yönlendirme URI 'lerinde**`http://localhost:8080/msal4jsamples/secure/aad` ve `http://localhost:8080/msal4jsamples/graph/me`ekleyin.
>    - **Kaydet**’i seçin.
> 1. Menüdeki **gizli dizileri &** seçin ve **istemci gizli** dizileri bölümünde **yeni istemci parolası**' na tıklayın:
>
>    - Bir anahtar açıklaması yazın (örneğin, uygulama gizli anahtarı).
>    - **1 yılda**bir anahtar süresi seçin.
>    - **Ekle**' yi seçtiğinizde anahtar değeri görüntülenecektir.
>    - Anahtarın değerini daha sonra kopyalayın. Bu anahtar değeri bir daha görüntülenmez veya başka yollarla alınabilir, bu nedenle Azure portal görünür hale geldiğinde onu kaydedin.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1\. Adım: uygulamanızı Azure portal yapılandırma
>
> Bu hızlı başlangıçta çalışması için kod örneği için şunları yapmanız gerekir:
>
> 1. `http://localhost:8080/msal4jsamples/secure/aad` ve `http://localhost:8080/msal4jsamples/graph/me`olarak yanıt URL 'Leri ekleyin.
> 1. Bir Istemci gizli dizisi oluşturun.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişiklikleri benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-aspnet-webapp/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-the-code-sample"></a>2\. Adım: kod örneğini Indirme

 [Kod örneğini indirin](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

#### <a name="step-3-configure-the-code-sample"></a>3\. Adım: kod örneğini yapılandırma

 1. ZIP dosyasını yerel bir klasöre ayıklayın.
 1. Tümleşik bir geliştirme ortamı kullanıyorsanız, örneği en sevdiğiniz IDE (isteğe bağlı) içinde açın.

 1. Src/Main/Resources/klasöründe bulunan Application. Properties dosyasını açın ve *AAD. ClientID*, *AAD. Authority* ve *AAD. SecretKey* alanlarını aşağıdaki şekilde **uygulama kimliği**, **Kiracı kimliği** ve **istemci gizli** anahtarı değerleriyle değiştirin:

    ```file
    aad.clientId=Enter_the_Application_Id_here
    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
    aad.secretKey=Enter_the_Client_Secret_Here
    aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
    aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
    ```

> [!div renderon="docs"]
> Konumlar:
>
> - `Enter_the_Application_Id_here` - Kaydettiğiniz uygulamanın Uygulama Kimliği değeridir.
> - `Enter_the_Client_Secret_Here`, **sertifikalarında** oluşturduğunuz ve kaydettiğiniz uygulamanın gizli dizileri & **gizli anahtar olan istemci sırrı** .
> - `Enter_the_Tenant_Info_Here`-kaydettiğiniz uygulamanın **Dizin (kiracı) kimlik** değeridir.

#### <a name="step-4-run-the-code-sample"></a>4\. Adım: kod örneğini çalıştırma

Projeyi çalıştırmak için şunlardan birini yapabilirsiniz:

Katıştırılmış yay önyükleme sunucusunu kullanarak doğrudan IDE 'nizden çalıştırın veya [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) kullanarak bir War dosyasına paketleyin ve bunu [Apache TOMCAT](http://tomcat.apache.org/)gibi bir J2EE kapsayıcı çözümüne dağıtın.

##### <a name="running-from-ide"></a>IDE 'den çalıştırma

Web uygulamasını bir IDE 'den çalıştırıyorsanız, Çalıştır ' a tıklayın ve ardından projenin giriş sayfasına gidin. Bu örnek için, standart giriş sayfası URL 'SI http://localhost:8080

1. Ön sayfada, Azure Active Directory yeniden yönlendirmek için **oturum aç** düğmesini seçin ve kullanıcıdan kimlik bilgilerini girmesini isteyebilirsiniz.

1. Kullanıcının kimliği doğrulandıktan sonra, *http://localhost:8080/msal4jsamples/secure/aad* yeniden yönlendirilir. Bunlar artık oturum açırlar ve sayfada oturum açmış hesap hakkında bilgi gösterilir. Örnek kullanıcı arabirimi aşağıdaki düğmelere sahiptir:
    - *Oturumu*kapat: geçerli kullanıcıyı uygulamanın dışına imzalar ve bunları giriş sayfasına yönlendirir.
    - *Kullanıcı bilgilerini göster*: Microsoft Graph için bir belirteç alır ve Microsoft Graph çağırır, bu belirteç içeren bir istekle birlikte oturum açan kullanıcı hakkında temel bilgileri döndürür.

> [!IMPORTANT]
> Bu hızlı başlangıç uygulaması, kendisini gizli istemci olarak tanımlamak için bir istemci gizli anahtarı kullanır. İstemci parolası proje dosyalarınıza düz metin olarak eklendiğinden, güvenlik nedenleriyle, uygulamayı üretim uygulaması olarak düşünmeden önce istemci parolası yerine bir sertifika kullanmanız önerilir. Sertifika kullanma hakkında daha fazla bilgi için bkz. [uygulama kimlik doğrulaması Için sertifika kimlik bilgileri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Daha fazla bilgi

### <a name="getting-msal"></a>MSAL alma

MSAL4J, kullanıcıları oturum açmak için kullanılan Java kitaplığı ve Microsoft Identity platformu tarafından korunan bir API 'ye erişmek için kullanılan belirteçleri ister.

Uygulamanın Pod. xml (Maven) veya Build. Gradle (Gradle) dosyasında aşağıdaki değişiklikleri yaparak bağımlılıklarınızı yönetmek için Maven veya Gradle kullanarak uygulamanıza MSAL4J ekleyin.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL başlatma

MSAL4J kullanacağınız dosyanın en üstüne aşağıdaki kodu ekleyerek MSAL4J öğesine bir başvuru ekleyin:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Sonraki Adımlar

İzinler ve onay hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [İzinler ve onay](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Bu senaryoya yönelik kimlik doğrulama akışı hakkında daha fazla bilgi edinmek için bkz. OAuth 2,0 yetkilendirme kodu akışı:

> [!div class="nextstepaction"]
> [Yetkilendirme kodu OAuth akışı](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Microsoft Identity platformunu geliştirmemize yardımcı olun. Kısa bir iki sorulık anketi tamamlayarak düşüncelerinizi bize söyleyin.

> [!div class="nextstepaction"]
> [Microsoft Identity platform Anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
