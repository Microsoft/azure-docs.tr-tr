---
title: Azure Uygulama yapılandırması ile yay önyüklemesine özellik bayrakları eklemeye yönelik hızlı başlangıç
description: Spring Boot uygulamalarına özellik bayrakları ekleyin ve Azure Uygulama yapılandırması 'nı kullanarak bunları yönetin
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 08/06/2020
ms.author: alkemper
ms.custom: devx-track-java
ms.openlocfilehash: e931b974fc48d07237b2698e7782c48be20745d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930755"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Hızlı başlangıç: Spring Boot uygulamasına özellik bayrakları ekleme

Bu hızlı başlangıçta, özellik yönetiminin uçtan uca bir uygulamasını oluşturmak için Azure uygulama yapılandırmasını bir Spring Boot Web uygulamasına katabilirsiniz. Tüm özellik Bayraklarınızı merkezi olarak depolamak ve durumlarını denetlemek için uygulama yapılandırma hizmetini kullanabilirsiniz.

Yay önyükleme özelliği yönetim kitaplıkları çerçeveyi kapsamlı özellik bayrağı desteğiyle genişletir. Bu kitaplıkların hiçbir Azure kitaplığına **bağımlılığı yoktur.** Bunlar, Spring Boot yapılandırma sağlayıcısı aracılığıyla uygulama yapılandırmasıyla sorunsuz bir şekilde tümleşir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* Sürüm 8 ile desteklenen bir [Java geliştirme seti SDK 'sı](/java/azure/jdk) .
* [Apache Maven](https://maven.apache.org/download.cgi) sürüm 3,0 veya üzeri.

## <a name="create-an-app-configuration-instance"></a>Uygulama yapılandırma örneği oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Özellik **Yöneticisi**  >  **+ Ekle** ' yi seçerek Özellik bayrağı ekleyin `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Beta adlı özellik bayrağını etkinleştir](media/add-beta-feature-flag.png)

    `label`Şimdilik tanımsız bırakın.

## <a name="create-a-spring-boot-app"></a>Spring Boot uygulaması oluşturma

Yeni bir Spring Boot projesi oluşturmak için [Spring ınitialr](https://start.spring.io/) ' i kullanın.

1. <https://start.spring.io/> adresine gidin.

1. Aşağıdaki seçenekleri belirtin:

   * **Java** ile bir **Maven** projesi oluşturun.
   * 2,0 ' e eşit veya ondan büyük bir **Spring Boot** sürümü belirtin.
   * Uygulamanız için **Grup** ve **Yapıt** adlarını belirtin.  Bu makalede `com.example` ve kullanılmaktadır `demo` .
   * **Yay Web** bağımlılığını ekleyin.

1. Önceki seçenekleri belirttikten sonra **proje oluştur**' u seçin. İstendiğinde, projeyi yerel bilgisayarınıza indirin.

## <a name="add-feature-management"></a>Özellik Yönetimi Ekle

1. Dosyaları yerel sisteminizde ayıkladıktan sonra, Spring Boot uygulamanız düzenlenmek üzere hazırlanın. Uygulamanızın kök dizininde  *pom.xml* bulun.

1. *pom.xml* dosyasını bir metin düzenleyicisinde açın ve aşağıdakileri listesine ekleyin `<dependencies>` :

    **Yay bulutu 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

    **Yay bulutu 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Spring-Web 'e bağımlılığı olmayan Web dışı bir özellik Yönetim Kitaplığı var. Farklar için GitHub [belgelerine](https://github.com/microsoft/spring-cloud-azure) bakın.

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. `resources`Uygulamanızın dizinine gidin ve açın `bootstrap.properties` .  Dosya yoksa, oluşturun. Dosyasına aşağıdaki satırı ekleyin.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Yapılandırma deponuzu uygulama yapılandırma portalında `Access keys` kenar çubuğundan seçeneğini belirleyin. Salt okuma anahtarları sekmesini seçin. Birincil bağlantı dizesinin değerini kopyalayın.

1. Birincil bağlantı dizesini, değişken adını kullanarak bir ortam değişkeni olarak ekleyin `APP_CONFIGURATION_CONNECTION_STRING` .

1. Ana uygulama Java dosyasını açın ve `@EnableConfigurationProperties` Bu özelliği etkinleştirmek için ekleyin.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Uygulamanızın paket dizininde *MessageProperties. Java* adlı yeni bir Java dosyası oluşturun.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Uygulamanızın paket dizininde *Hellocontroller. Java* adlı yeni bir Java dosyası oluşturun.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("featureManagement.Beta").block());
            return "welcome";
        }
    }
    ```

1. Uygulamanızın şablonlar dizininde *welcome.html* adlı yenı bir HTML dosyası oluşturun.

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/appconfiguration/azure-spring-cloud-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. `static` *Ana. css* ADLı yeni bir CSS dosyası IÇINDE ve içinde CSS adlı yeni bir klasör oluşturun.

    ```css
    html {
     position: relative;
     min-height: 100%;
    }
    body {
     margin-bottom: 60px;
    }
    .footer {
     position: absolute;
     bottom: 0;
     width: 100%;
     height: 60px;
     line-height: 60px;
     background-color: #f5f5f5;
    }

    body > .container {
     padding: 60px 15px 0;
    }

    .footer > .container {
     padding-right: 15px;
     padding-left: 15px;
    }

    code {
     font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. Spring Boot uygulamanızı Maven’le oluşturun ve çalıştırın.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Bir tarayıcı penceresi açın ve URL 'ye gidin: `http://localhost:8080/welcome` .

    ![Ekran görüntüsünde, bir hoş geldiniz iletisi içeren tarayıcı penceresi gösterilir.](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. Uygulama yapılandırma portalında, **Özellik Yöneticisi**' ni seçin ve **Beta** anahtarının durumunu **Açık** olarak değiştirin:

    | Anahtar | Durum |
    |---|---|
    | Beta | Açık |

1. Yeni yapılandırma ayarlarını görmek için tarayıcı sayfasını yenileyin.

    ![Ekran görüntüsünde, bir hoş geldiniz iletisi ve bir beta bağlantısı adlı bir tarayıcı penceresi gösterilir.](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir uygulama yapılandırma deposu oluşturdunuz ve bunu, [Özellik Yönetimi kitaplıkları](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)aracılığıyla bir Spring Boot Web uygulamasındaki özellikleri yönetmek için kullandınız.

* [Özellik yönetimi](./concept-feature-management.md)hakkında daha fazla bilgi edinin.
* [Özellik bayraklarını yönetin](./manage-feature-flags.md).
* [Bir Spring Boot Core uygulamasında Özellik bayraklarını kullanın](./use-feature-flags-spring-boot.md).