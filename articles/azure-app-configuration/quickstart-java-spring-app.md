---
title: Azure Uygulama yapılandırması 'nı kullanmayı öğrenmek için hızlı başlangıç | Microsoft Docs
description: Java Spring uygulamalarıyla Azure Uygulama yapılandırması 'nı kullanmaya yönelik hızlı başlangıç.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: f4ebbd4f37422c5aa2fea07a243eb624ec9e2961
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687015"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Hızlı başlangıç: Azure Uygulama yapılandırması ile bir Java Spring uygulaması oluşturma

Bu hızlı başlangıçta, kodınızdan ayrı uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek için Azure uygulama yapılandırmasını bir Java Spring uygulamasına katabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Sürüm 8 ile desteklenen bir [Java Geliştirme Seti (JDK)](https://docs.microsoft.com/java/azure/jdk) .
- [Apache Maven](https://maven.apache.org/download.cgi) sürüm 3,0 veya üzeri.

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Aşağıdaki anahtar-değer çiftlerini eklemek için **yapılandırma gezgini** >  **+ Oluştur** ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | /Application/config.exe iletisi | Merhaba |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

## <a name="create-a-spring-boot-app"></a>Spring Boot uygulaması oluşturma

Yeni bir Spring Boot projesi oluşturmak için [Spring ınitialvr](https://start.spring.io/) 'yi kullanırsınız.

1. <https://start.spring.io/> adresine gidin.

2. Aşağıdaki seçenekleri belirtin:

   * **Java**Ile **Maven** projesi oluşturun.
   * 2,0 ' e eşit veya ondan büyük bir **Spring Boot** sürümü belirtin.
   * Uygulamanız için **Grup** ve **yapıt** adlarını belirtin.
   * **Web** bağımlılığını ekleyin.

3. Önceki seçenekleri belirttikten sonra **proje oluştur**' u seçin. İstendiğinde, projeyi yerel bilgisayarınızdaki bir yola indirin.

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. Dosyaları yerel sisteminizde ayıkladıktan sonra, basit Spring Boot uygulamanız düzenlenmek üzere hazırlanın. Uygulamanızın kök dizinindeki *Pok. xml* dosyasını bulun.

2. *Pod. xml* dosyasını bir metin düzenleyicisinde açın ve Spring Cloud Azure config starter 'ı `<dependencies>` listesine ekleyin:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    ```

3. Uygulamanızın paket dizininde *MessageProperties. Java* adlı yeni bir Java dosyası oluşturun. Aşağıdaki satırları ekleyin:

    ```java
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

4. Uygulamanızın paket dizininde *Hellocontroller. Java* adlı yeni bir Java dosyası oluşturun. Aşağıdaki satırları ekleyin:

    ```java
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

5. Ana uygulama Java dosyasını açın ve bu özelliği etkinleştirmek için `@EnableConfigurationProperties` ' ı ekleyin.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Uygulamanızın Resources dizininde `bootstrap.properties` adlı yeni bir dosya oluşturun ve aşağıdaki satırları dosyaya ekleyin. Örnek değerleri, uygulama yapılandırma deponuzın uygun özellikleriyle değiştirin.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. Maven ile Spring Boot uygulamanızı derleyin ve çalıştırın, örneğin:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Uygulamanız çalıştırıldıktan sonra, uygulamanızı test etmek için *kıvrımlı* kullanın, örneğin:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Uygulama yapılandırma deposuna girdiğiniz iletiyi görürsünüz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir uygulama yapılandırma deposu oluşturdunuz ve bunu bir Java Spring uygulamasıyla kullandınız. Daha fazla bilgi için bkz. [Azure 'Da yay](https://docs.microsoft.com/java/azure/spring-framework/).

Uygulama yapılandırmasını kullanma hakkında daha fazla bilgi edinmek için, kimlik doğrulamasını gösteren bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)
