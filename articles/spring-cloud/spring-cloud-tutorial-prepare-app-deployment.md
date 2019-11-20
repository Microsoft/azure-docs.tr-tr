---
title: Azure yay bulutu 'nda dağıtım için bir yay uygulaması hazırlama | Microsoft Docs
description: Bu hızlı başlangıçta, bir Java Spring uygulamasını dağıtım için hazırlarsınız.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 6c3048fd27c3d3d4fdba0841fb4ab21bd10e15fe
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721242"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Öğretici: Azure yay bulutu 'nda dağıtım için bir Java Spring uygulaması hazırlama

Bu hızlı başlangıçta, mevcut bir Java Spring Cloud uygulamasının Azure Spring Cloud 'a dağıtılması için nasıl hazırlanacağı gösterilir.  Azure yay bulutu, doğru şekilde yapılandırıldığında Spring Cloud uygulamanızı izlemek, ölçeklendirmek ve güncelleştirmek için güçlü hizmetler sağlar. 

## <a name="java-runtime-version"></a>Java çalışma zamanı sürümü

Azure yay bulutu 'nda yalnızca Spring/Java uygulamaları çalıştırılabilir.

Hem Java 8 hem de Java 11 desteklenir. Barındırma ortamı, Azure için en son Azul Zulu OpenJDK 'yi içerir. Azure için Azul Zulu OpenJDK hakkında daha fazla bilgi için [Bu makaleye](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) bakın. 

## <a name="spring-boot-and-spring-cloud-versions"></a>Yay önyükleme ve yay bulut sürümleri

Azure yay bulutu 'nda yalnızca Spring Boot uygulamaları desteklenir. Hem Spring Boot 2,0 hem de 2,1 desteklenir. Desteklenen Spring Boot ve Spring Cloud kombinasyonları, aşağıdaki tabloda listelenmiştir.

Spring Boot sürümü | Yay bulutu sürümü
---|---
2.0. x | Finchley. RELEASE
2.1. x | Greenwich. RELEASE

`pom.xml` dosyanızda, sürümünüze bağlı olarak Spring Boot ve Spring Cloud Dependencies olduğunu doğrulayın.

### <a name="version-20"></a>Sürüm 2,0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Sürüm 2,1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure yay bulutu istemci bağımlılığı

Azure yay bulutu, Spring Cloud Service kayıt defteri ve Spring Cloud config Server gibi, sizin için Spring Cloud bileşenlerini barındırır ve yönetir. Azure Spring Cloud Service örneğiniz ile iletişime izin vermek için bağımlılıklarınızı Azure Spring Cloud 'ın istemci kitaplığını dahil edin.

Aşağıdaki tabloda bahar önyükleme/yay bulut uygulamanız için doğru sürümler listelenmektedir.

Spring Boot sürümü | Yay bulutu sürümü | Azure Spring Cloud sürümü
---|---|---
2.0. x | Finchley. RELEASE | 2.0. x
2.1. x | Greenwich. RELEASE | 2.1. x

`pom.xml`aşağıdaki kod parçacıklarında birini ekleyin.  Sürümü kendi ile eşleşen kod parçacığını seçin.

### <a name="version-20x"></a>Sürüm 2.0. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>2\.1. x sürümü:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Diğer gerekli bağımlılıklar

Azure Spring Cloud 'ın yerleşik özelliklerini etkinleştirmek için, uygulamanız aşağıdaki bağımlılıkları içermelidir. Bu, uygulamanızın kendisini her bileşenle doğru bir şekilde yapılandırmasını sağlayacaktır.  

### <a name="service-registry"></a>Hizmet kayıt defteri

Yönetilen Azure hizmeti kayıt defteri hizmetini kullanmak için aşağıda gösterildiği gibi `POM.xml` `spring-cloud-starter-netflix-eureka-client` ekleyin.

Hizmet kayıt defteri sunucusunun uç noktası, uygulamanıza ortam değişkenleri olarak otomatik olarak eklenir. Uygulamalar, kendilerini hizmet kayıt defteri sunucusuna kaydedebilir ve diğer bağımlı mikro hizmetleri bulabilir.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Dağıtılmış yapılandırma

Dağıtılmış yapılandırmayı etkinleştirmek için `pom.xml`bağımlılıklar bölümüne `spring-cloud-config-client` ekleyin.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Önyükleme yapılandırmasında `spring.cloud.config.enabled=false` belirtmeyin, çünkü uygulamanın yapılandırma sunucusu ile çalışmasını durduracaktır.

### <a name="metrics"></a>Ölçümler

Pod. xml 'nizin bağımlılıklar bölümüne `spring-boot-starter-actuator` ekleyin. Ölçümler, JMX uç noktalarından düzenli aralıklarla çekilir ve Azure portal kullanılarak görselleştirilir.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Dağıtılmış Izleme

Pod. xml 'nizin bağımlılıklar bölümüne `spring-cloud-starter-sleuth` ve `spring-cloud-starter-zipkin` ekleyin. Ayrıca, Azure Application Insights örneğini Azure Spring Cloud Service örneğiniz ile çalışacak şekilde etkinleştirmeniz gerekir. Azure [Spring Cloud Ile](spring-cloud-tutorial-distributed-tracing.md) uygulama öngörülerini nasıl etkinleştirebilirim hakkında daha fazla bilgi edinin

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Java Spring uygulamanızı Azure Spring Cloud 'a dağıtmak üzere nasıl yapılandıracağınızı öğrendiniz.  Yapılandırma sunucusunu nasıl etkinleştireceğinizi öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yapılandırma sunucunuzu ayarlamayı öğrenin](spring-cloud-tutorial-config-server.md).

