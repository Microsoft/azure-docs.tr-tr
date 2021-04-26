---
title: "Hızlı başlangıç: Microsoft 'a Java Web uygulamasına oturum açma ekleme | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, OpenID Connect kullanarak bir Java Web uygulamasına oturum açma eklemeyi öğreneceksiniz.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: a7337175241834cef862b4af07c7bcf7c8b845d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103779"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Hızlı başlangıç: Microsoft 'a Java Web uygulamasına oturum açma ekleme

Bu hızlı başlangıçta, Java Web uygulamasının kullanıcılara nasıl oturum açıp Microsoft Graph API 'sini çağırabileceğinizi gösteren bir kod örneği indirip çalıştırırsınız. Herhangi bir Azure Active Directory (Azure AD) kuruluşundaki kullanıcılar uygulamada oturum açabilir.

 Genel bakış için bkz. Örneğin [nasıl çalıştığına ilişkin diyagram](#how-the-sample-works).

## <a name="prerequisites"></a>Önkoşullar

Bu örneği çalıştırmak için şunlar gerekir:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 veya üzeri. 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı kaydetme ve indirme
> Hızlı başlangıç uygulamanızı başlatmanın iki yolu vardır: Express (seçenek 1) ve el ile (seçenek 2).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>1. seçenek: uygulamanızı kaydedin ve otomatik olarak yapılandırın ve ardından kod örneğini indirin
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure portal uygulama kayıtları</a> hızlı başlangıç deneyimine gidin.
> 1. Uygulamanız için bir ad girin ve ardından **Kaydet**' i seçin.
> 1. Otomatik olarak yapılandırılan uygulama kodunu indirmek için portalın hızlı başlangıç deneyimindeki yönergeleri izleyin.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma
>
> #### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme
>
> Uygulamanızı kaydetmek ve uygulamanın kayıt bilgilerini el ile eklemek için aşağıdaki adımları izleyin:
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden çok kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.
> 1. **Yönet**'in altında **Uygulama kayıtları** nı seçin.
> 1. **Yeni kayıt** seçeneğini belirleyin.
> 1. Uygulamanız için bir **ad** girin, örneğin **Java-WebApp**. Uygulamanızın kullanıcıları bu adı görebilir. Daha sonra değiştirebilirsiniz.
> 1. **Kaydet**’i seçin.
> 1. **Genel bakış** sayfasında, **uygulama (istemci) kimliği** ve **Dizin (kiracı) kimliği**' ne göz atın. Bu değerlere daha sonra ihtiyacınız olacak.
> 1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
> 1. **Platform Web ekle**' yi seçin  >  .
> 1. **Yeniden yönlendirme URI 'leri** bölümünde, girin `https://localhost:8443/msal4jsample/secure/aad` .
> 1. **Yapılandır**'ı seçin.
> 1. **Web** bölümünde, **yeniden yönlendirme URI 'leri** altında `https://localhost:8443/msal4jsample/graph/me` ikinci yeniden yönlendirme URI 'si olarak girin.
> 1. **Yönet**’in altında **Sertifikalar ve gizli diziler**’i seçin. **İstemci gizli** dizileri bölümünde **yeni istemci parolası**' nı seçin.
> 1. Bir anahtar açıklaması girin (örneğin, *uygulama gizli* anahtarı), varsayılan süre sonunu bırakın ve **Ekle**' yi seçin.
> 1. İstemci parolasının **değerini** aklınızda yapın. Buna daha sonra ihtiyacınız olacak.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. Adım: uygulamanızı Azure portal yapılandırma
>
> Bu hızlı başlangıçta kod örneğini kullanmak için:
>
> 1. Yanıt URL 'Leri `https://localhost:8443/msal4jsample/secure/aad` ve ekleyin `https://localhost:8443/msal4jsample/graph/me` .
> 1. Bir istemci gizli dizisi oluşturun.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişiklikleri benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-aspnet-webapp/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-the-code-sample"></a>2. Adım: kod örneğini Indirme
> [!div renderon="docs"]
> [Kod örneğini indirin](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Projeyi indirin ve. zip dosyasını sürücünüzün köküne yakın bir klasöre ayıklayın. Örneğin, *C:\Azure-Samples*.
>
> HTTPS 'yi localhost ile kullanmak için, `server.ssl.key` özellikleri sağlayın. Kendinden imzalı bir sertifika oluşturmak için, Keytool yardımcı programını (JRE 'de bulunur) kullanın.
>
> Aşağıda bir örnek verilmiştir:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Oluşturulan anahtar deposu dosyasını *Resources* klasörüne yerleştirin.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Kod örneğini indirin](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>3. Adım: kod örneğini yapılandırma
> 1. Zip dosyasını yerel bir klasöre çıkarın.
> 1. *Seçim.* Tümleşik bir geliştirme ortamı kullanıyorsanız, örneği bu ortamda açın.
> 1. *Application. Properties* dosyasını açın. Bunu *src/Main/Resources/* klasöründe bulabilirsiniz. Alanlarındaki değerleri, `aad.clientId` `aad.authority` ve `aad.secretKey` sırasıyla uygulama KIMLIĞI, Kiracı kimliği ve istemci gizli değerleri ile değiştirin. Şöyle görünmelidir:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    Önceki kodda:
>
>    - `Enter_the_Application_Id_here` , kaydettiğiniz uygulamanın uygulama KIMLIĞIDIR.
>    - `Enter_the_Client_Secret_Here`, sertifikalarında oluşturduğunuz ve kaydettiğiniz uygulamanın **gizli dizileri &** **gizli anahtar** .
>    - `Enter_the_Tenant_Info_Here` , kaydettiğiniz uygulamanın **Dizin (kiracı) kimlik** değeridir.
> 1. HTTPS 'yi localhost ile kullanmak için, `server.ssl.key` özellikleri sağlayın. Kendinden imzalı bir sertifika oluşturmak için, Keytool yardımcı programını (JRE 'de bulunur) kullanın.
>
>    Aşağıda bir örnek verilmiştir:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Oluşturulan anahtar deposu dosyasını *Resources* klasörüne yerleştirin.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>3. Adım: kod örneğini çalıştırma
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>4. Adım: kod örneğini çalıştırma

Projeyi çalıştırmak için şu adımlardan birini uygulayın:

- Katıştırılmış Spring Boot Server 'ı kullanarak doğrudan IDE 'nizden çalıştırın. 
- [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html)kullanarak bir War dosyasına paketleyin ve ardından [Apache TOMCAT](http://tomcat.apache.org/)gibi bir J2EE kapsayıcı çözümüne dağıtın.

##### <a name="running-the-project-from-an-ide"></a>Projeyi IDE 'den çalıştırma

Web uygulamasını bir IDE 'den çalıştırmak için Çalıştır ' ı seçin ve ardından projenin giriş sayfasına gidin. Bu örnek için, standart giriş sayfası URL 'sidir https://localhost:8443 .

1. Ön sayfada, kullanıcıları Azure Active Directory yeniden yönlendirmek ve kimlik bilgilerini istemek için **oturum aç** düğmesini seçin.

1. Kullanıcıların kimliği doğrulandıktan sonra, yeniden yönlendirilir `https://localhost:8443/msal4jsample/secure/aad` . Bunlar artık oturum açırlar ve sayfada Kullanıcı hesabı hakkında bilgi gösterilir. Örnek kullanıcı arabirimi şu düğmelere sahiptir:
    - **Oturumu** kapat: geçerli kullanıcıyı uygulamanın dışına imzalar ve bu kullanıcıyı giriş sayfasına yönlendirir.
    - **Kullanıcı bilgilerini göster**: Microsoft Graph için bir belirteç alır ve Microsoft Graph çağırır, bu belirteç içeren bir istekle birlikte oturum açan kullanıcı hakkında temel bilgileri döndürür.

##### <a name="running-the-project-from-tomcat"></a>Projeyi Tomcat 'ten çalıştırma

Web örneğini Tomcat 'e dağıtmak istiyorsanız, kaynak kodunda birkaç değişiklik yapın.

1. *MS-Identity-Java-WebApp/pom.xml* açın.
    - Altında `<name>msal-web-sample</name>` , ekleyin `<packaging>war</packaging>` .

2. *MS-identity-Java-WebApp/src/Main/Java/com. Microsoft. Azure. msalwebsample/MsalWebSampleApplication* öğesini açın.

    - Tüm kaynak kodu silin ve bu kodla değiştirin:

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

3.   Tomcat 'in varsayılan HTTP bağlantı noktası 8080 ' dir, ancak bağlantı noktası 8443 üzerinden bir HTTPS bağlantısına ihtiyacınız vardır. Bu ayarı yapılandırmak için:
        - *Tomcat/conf/server.xml* sayfasına gidin.
        - `<connector>`Etiketi arayın ve var olan bağlayıcıyı bu bağlayıcı ile değiştirin:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Bir komut istemi penceresi açın. Bu örneğin (pom.xml dosyasının bulunduğu) kök klasörüne gidin ve `mvn package` Projeyi derlemek için çalıştırın.
    - Bu komut, */targets* dizininizde bir *msal-Web-Sample-0.1.0. war* dosyası oluşturur.
    - Bu dosyayı *msal4jsample. war* olarak yeniden adlandırın.
    - Tomcat veya başka bir J2EE kapsayıcı çözümünü kullanarak WAR dosyasını dağıtın.
        - Msal4jsample. war dosyasını dağıtmak için, Tomcat yüklemenizde */webapps/* dizinine kopyalayın ve ardından Tomcat sunucusunu başlatın.

5. Dosya dağıtıldıktan sonra https://localhost:8443/msal4jsample tarayıcı kullanarak öğesine gidin.


> [!IMPORTANT]
> Bu hızlı başlangıç uygulaması, kendisini gizli bir istemci olarak tanımlamak için bir istemci gizli anahtarı kullanır. İstemci parolası proje dosyalarınıza düz metin olarak eklendiğinden, güvenlik nedenleriyle, uygulamayı bir üretim ortamında kullanmadan önce istemci parolası yerine bir sertifika kullanmanızı öneririz. Sertifika kullanma hakkında daha fazla bilgi için bkz. [uygulama kimlik doğrulaması Için sertifika kimlik bilgileri](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Daha fazla bilgi

### <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı
![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösteren diyagram.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>MSAL al

Java için MSAL (MSAL4J), kullanıcıları oturum açmak için kullanılan Java kitaplığı ve Microsoft Identity platformu tarafından korunan bir API 'ye erişmek için kullanılan belirteçleri ister.

Uygulamanızın pom.xml (Maven) veya Build. Gradle (Gradle) dosyasında aşağıdaki değişiklikleri yaparak bağımlılıklarınızı yönetmek için Maven veya Gradle kullanarak uygulamanıza MSAL4J ekleyin.

pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

Build. Gradle içinde:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>MSAL Başlat

MSAL4J kullanacağınız dosyanın başlangıcına aşağıdaki kodu ekleyerek Java için MSAL öğesine bir başvuru ekleyin:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Identity platformunda kullanıcılara oturum açmış Web uygulamaları oluşturmaya yönelik daha ayrıntılı bir tartışma için bkz. çok parçalı senaryo serisi:

> [!div class="nextstepaction"]
> [Senaryo: kullanıcılarda oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md?tabs=java)
