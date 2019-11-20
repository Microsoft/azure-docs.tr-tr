---
title: Yay önyüklemesi için özellik bayrakları eklemeye yönelik hızlı başlangıç-Azure Uygulama yapılandırması | Microsoft Docs
description: Spring Boot uygulamalarına özellik bayrakları ekleme ve bunları Azure Uygulama yapılandırması 'nda yönetme için hızlı başlangıç
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Spring Boot
ms.workload: tbd
ms.date: 09/26/2019
ms.author: mametcal
ms.openlocfilehash: fe943913f9d33923438efb91630c5a85c4d1b748
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687188"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Hızlı başlangıç: Spring Boot uygulamasına özellik bayrakları ekleme

Bu hızlı başlangıçta, özellik yönetiminin uçtan uca bir uygulamasını oluşturmak için Azure uygulama yapılandırmasını bir Spring Boot Web uygulamasına katabilirsiniz. Tüm özellik Bayraklarınızı merkezi olarak depolamak ve durumlarını denetlemek için uygulama yapılandırma hizmetini kullanabilirsiniz.

Yay önyükleme özelliği yönetim kitaplıkları çerçeveyi kapsamlı özellik bayrağı desteğiyle genişletir. Bu kitaplıkların herhangi bir Azure libries **bağımlılığı yoktur.** Bunlar, Spring Boot yapılandırma sağlayıcısı aracılığıyla uygulama yapılandırmasıyla sorunsuz bir şekilde tümleşir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Sürüm 8 ile desteklenen bir [Java geliştirme seti SDK 'sı](https://docs.microsoft.com/java/azure/jdk) .
- [Apache Maven](https://maven.apache.org/download.cgi) sürüm 3,0 veya üzeri.

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Aşağıdaki özellik bayraklarını eklemek için **özellik yöneticisi** >  **+ Oluştur** ' u seçin:

    | Anahtar | Eyalet |
    |---|---|
    | Beta | Kapalı |

## <a name="create-a-spring-boot-app"></a>Spring Boot uygulaması oluşturma

Yeni bir Spring Boot projesi oluşturmak için [Spring ınitialvr](https://start.spring.io/) 'yi kullanırsınız.

1. <https://start.spring.io/> adresine gidin.

2. Aşağıdaki seçenekleri belirtin:

   - **Java**Ile **Maven** projesi oluşturun.
   - 2,0 ' e eşit veya ondan büyük bir **Spring Boot** sürümü belirtin.
   - Uygulamanız için **Grup** ve **yapıt** adlarını belirtin.
   - **Web** bağımlılığını ekleyin.

3. Önceki seçenekleri belirttikten sonra **proje oluştur**' u seçin. İstendiğinde, projeyi yerel bilgisayarınızdaki bir yola indirin.

## <a name="add-feature-management"></a>Özellik Yönetimi Ekle

1. Dosyaları yerel sisteminizde ayıkladıktan sonra, basit Spring Boot uygulamanız düzenlenmek üzere hazırlanın. Uygulamanızın kök dizinindeki *Pok. xml* dosyasını bulun.

2. *Pod. xml* dosyasını bir metin düzenleyicisinde açın ve yay bulutu Azure yapılandırma başlangıç ve özellik yönetimini `<dependencies>` listesine ekleyin:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Spring-Web 'e bağımlılığı olmayan Web dışı bir özellik Yönetim Kitaplığı var. Farklar için ek [belgelere](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) bakın. Ayrıca, uygulama yapılandırması kullanmadığınız durumlarda [Özellik bayrağı bildirimine](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management#feature-flag-declaration)bakın.

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. Uygulamanızın Resources dizininde olan `bootstrap.properties` ' ı açın ve dosyaya aşağıdaki satırları ekleyin. Uygulama yapılandırma bilgilerini ekleyin.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

2. Yapılandırma deponuza ait uygulama yapılandırma portalında erişim anahtarlarına gidin. Salt okuma anahtarları sekmesini seçin. Bu sekmede, bağlantı dizelerinden birinin değerini kopyalayın ve `APP_CONFIGURATION_CONNECTION_STRING` değişken adına sahip yeni bir ortam değişkeni olarak ekleyin.

3. Ana uygulama Java dosyasını açın ve bu özelliği etkinleştirmek için `@EnableConfigurationProperties` ' ı ekleyin.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

4. Uygulamanızın paket dizininde *Hellocontroller. Java* adlı yeni bir Java dosyası oluşturun. Aşağıdaki satırları ekleyin:

    ```java
    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabled("Beta"));
            return "welcome";
        }
    }
    ```

5. Uygulamanızın şablonlar dizininde *Welcome. html* adlı yenı bir HTML dosyası oluşturun. Aşağıdaki satırları ekleyin:

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
            <p>Learn more about <a href="https://github.com/microsoft/spring-cloud-azure/blob/master/spring-cloud-azure-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

6. Statik ve onun içinde *CSS adlı yeni*bir klasör oluşturun. Aşağıdaki satırları ekleyin:

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

1. Maven ile Spring Boot uygulamanızı derleyin ve çalıştırın, örneğin:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

2. Bir tarayıcı penceresi açın ve yerel olarak barındırılan Web uygulamasının varsayılan URL 'SI olan `https://localhost:8080` ' a gidin.

    ![Hızlı başlangıç uygulaması başlatma yerel](./media/quickstarts/spring-boot-feature-flag-local-before.png)

3. Uygulama yapılandırma portalında, **Özellik Yöneticisi**' ni seçin ve **Beta** anahtarının durumunu **Açık**olarak değiştirin:

    | Anahtar | Eyalet |
    |---|---|
    | Beta | Açık |

4. Yeni yapılandırma ayarlarını görmek için tarayıcı sayfasını yenileyin.

    ![Hızlı başlangıç uygulaması başlatma yerel](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir uygulama yapılandırma deposu oluşturdunuz ve bunu, [Özellik Yönetimi kitaplıkları](https://go.microsoft.com/fwlink/?linkid=2074664)aracılığıyla bir Spring Boot Web uygulamasındaki özellikleri yönetmek için kullandınız.

- [Özellik yönetimi](./concept-feature-management.md)hakkında daha fazla bilgi edinin.
- [Özellik bayraklarını yönetin](./manage-feature-flags.md).
- [Bir Spring Boot Core uygulamasında Özellik bayraklarını kullanın](./use-feature-flags-spring-boot.md).
