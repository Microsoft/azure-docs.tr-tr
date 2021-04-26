---
title: Azure Uygulama yapılandırması 'nı kullanmayı öğrenmek için hızlı başlangıç
description: Bu hızlı başlangıçta, kodınızdan ayrı uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek için Azure Uygulama yapılandırmasıyla bir Java Spring uygulaması oluşturun.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: 40c8fae292f465b3ecc8778a21e4903bdf423a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932039"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Hızlı başlangıç: Azure Uygulama yapılandırması ile bir Java Spring uygulaması oluşturma

Bu hızlı başlangıçta, kodınızdan ayrı uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek için Azure uygulama yapılandırmasını bir Java Spring uygulamasına katabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Sürüm 8 ile desteklenen bir [Java Geliştirme Seti (JDK)](/java/azure/jdk) .
- [Apache Maven](https://maven.apache.org/download.cgi) sürüm 3,0 veya üzeri.

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >    >  Aşağıdaki anahtar-değer çiftlerini eklemek için yapılandırma Gezgini +**anahtar-değer** oluştur ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | /Application/config.exe iletisi | Hello |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

8. **Uygula**’yı seçin.

## <a name="create-a-spring-boot-app"></a>Spring Boot uygulaması oluşturma

Yeni bir Spring Boot projesi oluşturmak için [Spring ınitialr](https://start.spring.io/) ' i kullanın.

1. <https://start.spring.io/> adresine gidin.

1. Aşağıdaki seçenekleri belirtin:

   - **Java** ile bir **Maven** projesi oluşturun.
   - 2,0 ' e eşit veya ondan büyük bir **Spring Boot** sürümü belirtin.
   - Uygulamanız için **Grup** ve **Yapıt** adlarını belirtin.
   - **Yay Web** bağımlılığını ekleyin.

1. Önceki seçenekleri belirttikten sonra **proje oluştur**' u seçin. İstendiğinde, projeyi yerel bilgisayarınızda bir yola indirin.

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. Dosyaları yerel sisteminizde ayıkladıktan sonra, basit Spring Boot uygulamanız düzenlenmek üzere hazırlanın. *pom.xml* dosyasını uygulamanızın kök dizininde bulun.

1. *pom.xml* dosyasını bir metin düzenleyicisinde açın ve yay bulutu Azure yapılandırma başlangıç listesini şu listeye ekleyin `<dependencies>` :

    **Yay bulutu 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Yay bulutu 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
    </dependency>
    ```

1. Uygulamanızın paket dizininde *MessageProperties. Java* adlı yeni bir Java dosyası oluşturun. Aşağıdaki satırları ekleyin:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

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

1. Uygulamanızın paket dizininde *Hellocontroller. Java* adlı yeni bir Java dosyası oluşturun. Aşağıdaki satırları ekleyin:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Ana uygulama Java dosyasını açın ve `@EnableConfigurationProperties` Bu özelliği etkinleştirmek için ekleyin.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Uygulamanızın Resources dizininde adlı yeni bir dosya oluşturun `bootstrap.properties` ve aşağıdaki satırları dosyaya ekleyin. Örnek değerleri, uygulama yapılandırma deponuzın uygun özellikleriyle değiştirin.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. **APP_CONFIGURATION_CONNECTION_STRING** adlı bir ortam değişkeni ayarlayın ve bunu uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Komut satırında, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. Maven ile Spring Boot uygulamanızı derleyin ve çalıştırın, örneğin:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Uygulamanız çalıştırıldıktan sonra, uygulamanızı test etmek için *kıvrımlı* kullanın, örneğin:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Uygulama yapılandırma deposuna girdiğiniz iletiyi görürsünüz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir uygulama yapılandırma deposu oluşturdunuz ve bunu bir Java Spring uygulamasıyla kullandınız. Daha fazla bilgi için bkz. [Azure 'Da yay](/java/azure/spring-framework/). Java Spring uygulamanızı yapılandırma ayarlarını dinamik olarak yenilemek üzere nasıl etkinleştireceğinizi öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Dinamik yapılandırmayı etkinleştirme](./enable-dynamic-configuration-java-spring-app.md)