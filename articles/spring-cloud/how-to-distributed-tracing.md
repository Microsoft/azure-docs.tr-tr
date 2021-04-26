---
title: Azure Spring Cloud ile dağıtılmış Izleme kullanma
description: Spring Cloud 'ın dağıtılmış izlemesini Azure 'da nasıl kullanacağınızı öğrenin Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 6bcb020b14952541c673592c1040fca211ed4edf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011860"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Azure Spring Cloud ile dağıtılmış izleme kullanma

Azure Spring Cloud 'daki dağıtılmış izleme araçlarıyla, karmaşık sorunları kolayca ayıklayabilir ve izleyebilirsiniz. Azure Spring Cloud, Azure 'ın [Application Insights](../azure-monitor/app/app-insights-overview.md) [yay bulutuna](https://spring.io/projects/spring-cloud-sleuth) karşı tümleşir. Bu tümleştirme Azure portal güçlü dağıtılmış izleme yeteneği sağlar.

::: zone pivot="programming-language-csharp"
Bu makalede, bir .NET Core Steeltoe uygulamasının dağıtılmış izlemeyi kullanmak üzere nasıl etkinleştirileceğini öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu yordamları izlemek için, [Azure Spring Cloud 'a dağıtım için hazırlanmış](how-to-prepare-app-deployment.md)bir Steeltoe uygulamasına ihtiyacınız vardır.

## <a name="dependencies"></a>Bağımlılıklar

Steeltoe 2.4.4 için aşağıdaki NuGet paketlerini ekleyin:

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. Management. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

Steeltoe 3.0.0 için aşağıdaki NuGet paketini ekleyin:

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)

## <a name="update-startupcs"></a>Güncelleştirme başlangıcı. cs

1. Steeltoe 2.4.4 için yöntemini çağırın `AddDistributedTracing` `AddZipkinExporter` `ConfigureServices` .

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

   Steeltoe 3.0.0 için yöntemini çağırın `AddDistributedTracing` `ConfigureServices` .

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration, builder => builder.UseZipkinWithTraceOptions(services));
   }
   ```

1. Steeltoe 2.4.4 için yöntemini çağırın `UseTracingExporter` `Configure` .

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
        app.UseTracingExporter();
   }
   ```

   Steeltoe 3.0.0 için yöntemde hiçbir değişiklik yapılması gerekmez `Configure` .

## <a name="update-configuration"></a>Güncelleştirme yapılandırması

Aşağıdaki ayarları, uygulama Azure yay bulutu 'nda çalıştırıldığında kullanılacak yapılandırma kaynağına ekleyin:

1. `management.tracing.alwaysSample` değerini true olarak ayarlayın.

2. Eureka sunucusu, yapılandırma sunucusu ve kullanıcı uygulamaları arasında gönderilen izlenen yayılmaları görmek isterseniz: `management.tracing.egressIgnorePattern` "/api/v2/birleþi |/v2/Apps/" olarak ayarlayın.*/Permissions |/Eureka/.*| /OAuth/. * ".

Örneğin, *appsettings.js* , aşağıdaki özellikleri içerir:

```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

.NET Core Steeltoe uygulamalarında dağıtılmış izleme hakkında daha fazla bilgi için bkz. Steeltoe belgelerinde [Dağıtılmış izleme](https://steeltoe.io/docs/3/tracing/distributed-tracing) .
::: zone-end
::: zone pivot="programming-language-java"
Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure portal dağıtılmış izlemeyi etkinleştirin.
> * Uygulamanıza yay bulutu uykuya geçin.
> * Mikro hizmet uygulamalarınız için bağımlılık haritalarını görüntüleyin.
> * Farklı filtrelerle izleme verilerinde arama yapın.

## <a name="prerequisites"></a>Önkoşullar

Bu yordamları izlemek için, zaten sağlanmış ve çalışan bir Azure yay bulut hizmetine ihtiyacınız vardır. Azure yay bulut hizmeti sağlamak ve çalıştırmak için [Ilk Azure yay bulutu uygulaması](spring-cloud-quickstart.md) hızlı başlangıç adımlarını gerçekleştirin.

## <a name="add-dependencies"></a>Bağımlılık Ekle

1. Aşağıdaki satırı Application. Properties dosyasına ekleyin:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Bu değişiklikten sonra, Zipkabağı gönderici Web 'e gönderebilir.

1. [Azure yay bulut uygulaması hazırlama kılavuzumuzu](how-to-prepare-app-deployment.md)izlediyseniz bu adımı atlayın. Aksi takdirde, yerel geliştirme ortamınıza gidin ve pom.xml dosyanızı düzenleyerek aşağıdaki yay bulutu uykuya geçme bağımlılığını ekleyin:

    * Spring Boot sürüm < 2.4. x.

      ```xml
      <dependencyManagement>
          <dependencies>
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-sleuth</artifactId>
                  <version>${spring-cloud-sleuth.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
          </dependencies>
      </dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-sleuth</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-zipkin</artifactId>
          </dependency>
      </dependencies>
      ```

    * Spring Boot sürüm >= 2,4. x.

      ```xml
      <dependencyManagement>
          <dependencies>
            <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-sleuth</artifactId>
                  <version>${spring-cloud-sleuth.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
          </dependencies>
      </dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-sleuth</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-sleuth-zipkin</artifactId>
           </dependency>
      </dependencies>
      ```


1. Bu değişiklikleri yansıtacak şekilde Azure Spring Cloud Service için derleyin ve dağıtın.

## <a name="modify-the-sample-rate"></a>Örnek hızını değiştirme

Örnek hızını değiştirerek telemetrinizin toplandığı oranı değiştirebilirsiniz. Örneğin, çoğunlukla yarı bir örnek örneklemek istiyorsanız, Application. Properties dosyanızı açın ve aşağıdaki satırı değiştirin:

```xml
spring.sleuth.sampler.probability=0.5
```

Zaten bir uygulama oluşturup dağıttıysanız, örnek hızı değiştirebilirsiniz. Önceki satırı Azure CLı veya Azure portal bir ortam değişkeni olarak ekleyerek bunu yapın.
::: zone-end

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

1. Azure portal Azure Spring Cloud Service sayfanıza gidin.
1. **İzleme** sayfasında, **Dağıtılmış izleme**' yi seçin.
1. Düzenlemek veya yeni bir ayar eklemek için **ayarı Düzenle** ' yi seçin.
1. Yeni bir Application Insights sorgusu oluşturun veya var olan bir sorgu seçin.
1. İzlemek istediğiniz günlüğe kaydetme kategorisini seçin ve bekletme süresini gün olarak belirtin.
1. Yeni izlemeyi uygulamak için **Uygula** ' yı seçin.

## <a name="view-the-application-map"></a>Uygulama haritasını görüntüleme

**Dağıtılmış izleme** sayfasına dönün ve **uygulama haritasını görüntüle**' yi seçin. Uygulamanızın ve izleme ayarlarının görsel temsilini gözden geçirin. Uygulama haritasını nasıl kullanacağınızı öğrenmek için bkz. [uygulama Haritası: dağıtılmış uygulamaları önceliklendirme](../azure-monitor/app/app-map.md).

## <a name="use-search"></a>Aramayı Kullanma

Diğer belirli telemetri öğelerini sorgulamak için Search işlevini kullanın. **Dağıtılmış izleme** sayfasında, **Ara**' yı seçin. Arama işlevini kullanma hakkında daha fazla bilgi için bkz. [Application Insights arama kullanma](../azure-monitor/app/diagnostic-search.md).

## <a name="use-application-insights"></a>Application Insights kullan

Application Insights, uygulama eşlemesi ve arama işlevine ek olarak izleme özellikleri sağlar. Azure portal uygulamanızın adı için arama yapın ve ardından izleme bilgilerini bulmak için bir Application Insights sayfası açın. Bu araçların nasıl kullanılacağına dair daha fazla bilgi için [Azure izleyici günlük sorgularını](/azure/data-explorer/kusto/query/)inceleyin.

## <a name="disable-application-insights"></a>Application Insights devre dışı bırak

1. Azure portal Azure Spring Cloud Service sayfanıza gidin.
1. **İzleme** sırasında **Dağıtılmış izleme**' yi seçin.
1. Application Insights devre dışı bırakmak için **Disable** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Spring Cloud 'da dağıtılmış izlemeyi etkinleştirme ve anlama hakkında bilgi edindiniz. Bir uygulamaya hizmet bağlama hakkında bilgi edinmek için bkz. [Azure Spring Cloud uygulamasına Azure Cosmos db veritabanını](spring-cloud-howto-bind-cosmos.md)bağlama.