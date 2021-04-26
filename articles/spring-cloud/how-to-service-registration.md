---
title: Hizmet kayıt defteri ve bulmayı otomatikleştirin
description: Yay bulut hizmeti kayıt defteri 'ni kullanarak hizmet bulmayı ve kaydı otomatikleştirmeyi öğrenin
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: e2ad6d8e3d81497eb7ebe612d01e60415790faea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878811"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Spring Cloud Services 'ı bulma ve kaydetme

Hizmet bulma, mikro hizmet tabanlı bir mimari için anahtar gereksinimidir.  Her bir istemciyi el ile yapılandırmak zaman alır ve insan hatası olasılığını tanıtır.  Azure Spring Cloud Service kayıt defteri Bu sorunu çözer.  Yapılandırıldıktan sonra bir hizmet kayıt defteri sunucusu, uygulamanızın mikro hizmetleri için hizmet kaydını ve bulmayı denetler. Hizmet kayıt defteri sunucusu dağıtılan mikro hizmetlerin bir kayıt defterini tutar, DNS 'ye bağlı olmadan istemci tarafı yük dengelemesi ve hizmet sağlayıcılarını istemcilerden ayırır.

::: zone pivot="programming-language-csharp"
Bir Steeltoe uygulaması için hizmet kaydını ayarlama hakkında daha fazla bilgi için bkz. [Azure yay bulutu 'nda bir Java Spring uygulamasını dağıtıma hazırlama](how-to-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Spring Cloud Service kayıt defteri 'ni kullanarak uygulamanızı kaydetme

Uygulamanızın, yay bulut hizmeti kayıt defteri kullanarak hizmet kaydını ve bulmayı yönetebilmesi için, uygulamanın *pom.xml* dosyasına birkaç bağımlılık eklenmelidir.
*Spring-Cloud-Starter-Netflix-Eureka-Client* ve *Spring-Cloud-Starter-Azure-Spring-Cloud-Client* için *pom.xml* bağımlılıklarını ekleyin

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>En üst düzey sınıfı güncelleştirme

Son olarak, uygulamanızın en üst düzey sınıfına bir ek açıklama ekleyeceğiz

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Yay bulutu hizmeti kayıt defteri sunucu uç noktası, uygulamanıza bir ortam değişkeni olarak eklenecektir.  Mikro hizmetler, kendilerini hizmet kayıt defteri sunucusuna kaydedebilve diğer bağımlı mikro hizmetleri keşfedebilecektir.

Değişikliklerin sunucudan tüm mikro hizmetlere yayılması birkaç dakika sürebileceğini unutmayın.
::: zone-end
