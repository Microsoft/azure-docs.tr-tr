---
title: Azure Spring Cloud 'da bir uygulamayı dağıtım için hazırlama
description: Bir uygulamayı Azure Spring Cloud 'a dağıtıma hazırlama hakkında bilgi edinin.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: cabc4784dfb19f569212f4d0cb93e6838473e559
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714497"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud 'da bir uygulamayı dağıtıma hazırlama

::: zone pivot="programming-language-csharp"
Azure yay bulutu, bir Bueltoe uygulamasını barındırmak, izlemek, ölçeklendirmek ve güncelleştirmek için güçlü hizmetler sağlar. Bu makalede, mevcut bir Steeltoe uygulamasının Azure Spring Cloud 'a dağıtılması için nasıl hazırlanacağı gösterilmektedir. 

Bu makalede, Azure yay bulutu 'nda .NET Core Steeltoe uygulaması çalıştırmak için gereken bağımlılıklar, yapılandırma ve kod açıklanmaktadır. Azure yay bulutuna bir uygulamanın nasıl dağıtılacağı hakkında bilgi için, bkz. [Ilk Azure Spring Cloud uygulamanızı dağıtma](spring-cloud-quickstart.md).

>[!Note]
> Azure yay bulutu için steeltoe desteği şu anda genel önizleme olarak sunulmaktadır. Genel Önizleme teklifleri, müşterilerin resmi sürümünden önceki yeni özelliklerle deneme yapmasına olanak tanır.  Genel Önizleme özellikleri ve Hizmetleri üretim kullanımı için tasarlanmamıştır.  Önizlemeler sırasında destek hakkında daha fazla bilgi için bkz. [SSS](https://azure.microsoft.com/support/faq/) veya dosya a [destek isteği](../azure-portal/supportability/how-to-create-azure-support-request.md).

##  <a name="supported-versions"></a>Desteklenen sürümler

Azure yay bulutu şunları destekler:

* .NET Core 3.1
* Steeltoe 2,4 ve 3,0

## <a name="dependencies"></a>Bağımlılıklar

Steeltoe 2,4 için, en son [Microsoft. Azure. SpringCloud. Client 1. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) paketini proje dosyasına ekleyin:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Steeltoe 3,0 için, en son [Microsoft. Azure. SpringCloud. Client 2. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) paketini proje dosyasına ekleyin:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Program. cs 'yi Güncelleştir

`Program.Main`Yönteminde `UseAzureSpringCloudService` yöntemini çağırın.

Steeltoe 2.4.4 için, çağrıldıktan `UseAzureSpringCloudService` sonra `ConfigureWebHostDefaults` ve sonra çağırın `AddConfigServer` :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

Steeltoe 3.0.0 için, `UseAzureSpringCloudService` `ConfigureWebHostDefaults` Tüm steeltoe yapılandırma kodundan önce ve önce çağırın:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Eureka sunucu hizmeti bulmayı etkinleştir

Uygulama Azure yay bulutu 'nda çalıştırıldığında kullanılacak yapılandırma kaynağında, `spring.application.name` projenin dağıtılacağı Azure yay bulutu uygulamasıyla aynı ada ayarlanır.

Örneğin, bir Azure yay bulut uygulamasına adlı bir .NET projesi dağıtırsanız `EurekaDataProvider` `planet-weather-provider` , *appSettings.json* dosyası adlı BIR .NET projesi aşağıdaki JSON 'u içermelidir:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Hizmet bulmayı kullan

Eureka Server hizmet keşfini kullanarak bir hizmeti çağırmak için, `http://<app_name>` `app_name` hedef uygulamanın değeri olduğu yerde http istekleri yapın `spring.application.name` . Örneğin, aşağıdaki kod `planet-weather-provider` hizmeti çağırır:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
Bu konu başlığı altında, mevcut bir Java Spring uygulamasının Azure Spring Cloud 'a dağıtılması için nasıl hazırlanacağı gösterilmektedir. Doğru yapılandırılmışsa, Azure yay bulutu, Java Spring Cloud uygulamanızı izlemek, ölçeklendirmek ve güncelleştirmek için güçlü hizmetler sunar.

Bu örneği çalıştırmadan önce [temel hızlı](spring-cloud-quickstart.md)başlangıcı deneyebilirsiniz.

Diğer örneklerde pod dosyası yapılandırıldığında bir uygulamanın Azure Spring Cloud 'a nasıl dağıtılacağı açıklanmaktadır. 
* [İlk uygulamanızı başlatın](spring-cloud-quickstart.md)
* [Mikro hizmetler oluşturma ve çalıştırma](spring-cloud-quickstart-sample-app-introduction.md)

Bu makalede, gerekli bağımlılıklar ve bunların Pod dosyasına nasıl ekleneceği açıklanmaktadır.

## <a name="java-runtime-version"></a>Java çalışma zamanı sürümü

Azure yay bulutu 'nda yalnızca Spring/Java uygulamaları çalıştırılabilir.

Azure yay bulutu hem Java 8 hem de Java 11 ' i destekler. Barındırma ortamı, Azure için Azul Zulu OpenJDK 'nin en son sürümünü içerir. Azure için Azul Zulu OpenJDK hakkında daha fazla bilgi için bkz. [JDK 'Yi yüklemeyin](/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Yay önyükleme ve yay bulut sürümleri

Mevcut bir Spring Boot uygulamasını Azure yay bulutu 'na dağıtmak üzere hazırlamak için, aşağıdaki bölümlerde gösterildiği gibi, Application Pod dosyasına Spring Boot ve Spring Cloud bağımlılıklarını ekleyin.

Azure yay bulutu, Spring Boot sürüm 2,2, 2,3, 2,4 ' i destekler. Aşağıdaki tabloda desteklenen Spring Boot ve Spring Cloud kombinasyonlarını listelenmektedir:

Spring Boot sürümü | Yay bulutu sürümü
---|---
2,2 | Hoxton. SR8
2.3 | Hoxton. SR8
2.4.1 + | 2020.0.0

> [!NOTE]
> Uygulama ve Eureka arasında TLS kimlik doğrulamasında Spring Boot 2.4.0 ile ilgili bir sorun belirledik, lütfen 2.4.1 veya üstünü kullanın. 2.4.0 kullanarak insist geçici çözüm için lütfen [SSS bölümüne](./spring-cloud-faq.md?pivots=programming-language-java#development) bakın.

### <a name="dependencies-for-spring-boot-version-2223"></a>Spring Boot sürüm 2.2/2.3 için bağımlılıklar

Spring Boot sürüm 2,2 için aşağıdaki bağımlılıkları uygulama Pod dosyasına ekleyin.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-24"></a>Spring Boot sürüm 2,4 için bağımlılıklar

Spring Boot sürüm 2,2 için aşağıdaki bağımlılıkları uygulama Pod dosyasına ekleyin.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> Yapılandırmanızda belirtmeyin `server.port` . Azure Spring Cloud, bu ayarı bir sabit bağlantı noktası numarasına overide. Lütfen bu ayara de dikkat edin ve kodunuzda sunucu bağlantı noktası belirtmeyin.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Azure Spring Cloud özelliklerini etkinleştirmek için önerilen diğer bağımlılıklar

Azure Spring Cloud 'ın yerleşik özelliklerini hizmet kayıt defterinden dağıtılmış izlemeye etkinleştirmek için, uygulamanıza aşağıdaki bağımlılıkları da dahil etmeniz gerekir. Belirli uygulamalar için ilgili özelliklere ihtiyacınız yoksa, bu bağımlılıklardan bazılarını bırakabilirsiniz.

### <a name="service-registry"></a>Hizmet kayıt defteri

Yönetilen Azure hizmeti kayıt defteri hizmetini kullanmak için, `spring-cloud-starter-netflix-eureka-client` pom.xml dosyasına bağımlılığı aşağıda gösterildiği gibi ekleyin:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Hizmet kayıt defteri sunucusunun uç noktası, uygulamanıza ortam değişkenleri olarak otomatik olarak eklenir. Uygulamalar, kendilerini hizmet kayıt defteri sunucusuna kaydedebilir ve diğer bağımlı mikro hizmetleri bulabilir.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient ek açıklaması

Uygulama kaynak koduna aşağıdaki ek açıklamayı ekleyin.
```java
@EnableDiscoveryClient
```
Örneğin, önceki örneklerden plımetriler uygulamasına bakın:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Dağıtılmış yapılandırma

Dağıtılmış yapılandırmayı etkinleştirmek için, `spring-cloud-config-client` pom.xml dosyanızın bağımlılıklar bölümüne aşağıdaki bağımlılığı ekleyin:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> `spring.cloud.config.enabled=false`Önyükleme yapılandırmanızda belirtmeyin. Aksi takdirde, uygulamanız yapılandırma sunucusu ile çalışmayı durduruyor.

### <a name="metrics"></a>Ölçümler

`spring-boot-starter-actuator`pom.xml dosyanızın bağımlılıklar bölümüne bağımlılığı aşağıda gösterildiği gibi ekleyin:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Ölçümler, JMX uç noktalarından düzenli aralıklarla çekilir. Azure portal kullanarak ölçümleri görselleştirebilirsiniz.

 > [!WARNING]
 > Lütfen `spring.jmx.enabled=true` yapılandırma özelliği içinde belirtin. Aksi takdirde, ölçümler Azure portal görselleştirilebilen.

### <a name="distributed-tracing"></a>Dağıtılmış Izleme

Ayrıca Azure Spring Cloud Service örneğiniz ile çalışmak için bir Azure Application Insights örneğini etkinleştirmeniz gerekir. Azure Spring Cloud ile Application Insights kullanma hakkında daha fazla bilgi için bkz. [Dağıtılmış izleme hakkındaki belgeler](spring-cloud-tutorial-distributed-tracing.md).

#### <a name="spring-boot-2223"></a>Spring Boot 2.2/2.3
`spring-cloud-starter-sleuth` `spring-cloud-starter-zipkin` pom.xml dosyanızın bağımlılıklar bölümüne aşağıdaki ve bağımlılıklarını ekleyin:

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

#### <a name="spring-boot-24"></a>Spring Boot 2,4
`spring-cloud-sleuth-zipkin`pom.xml dosyanızın bağımlılıklar bölümüne aşağıdaki bağımlılığı ekleyin:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Ayrıca bkz.
* [Uygulama günlüklerini ve ölçümleri çözümleme](./diagnostic-services.md)
* [Yapılandırma Sunucunuzu ayarlama](spring-cloud-tutorial-config-server.md)
* [Azure Spring Cloud ile dağıtılmış izleme kullanma](spring-cloud-tutorial-distributed-tracing.md)
* [Yay hızlı başlangıç kılavuzu](https://spring.io/quickstart)
* [Spring Boot belgeleri](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Sonraki adımlar

Bu konu başlığında, Java Spring uygulamanızı Azure Spring Cloud 'a dağıtmak üzere nasıl yapılandıracağınızı öğrendiniz. Bir yapılandırma sunucusu örneğini ayarlamayı öğrenmek için bkz. [bir yapılandırma sunucusu örneği ayarlama](spring-cloud-tutorial-config-server.md).

GitHub 'da daha fazla örnek vardır: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end