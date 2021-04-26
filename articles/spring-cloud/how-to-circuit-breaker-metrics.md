---
title: Mikro ölçüm ile yay bulutu Resilience4J devre kesici ölçümlerini toplayın
description: Azure Spring Cloud 'da mikro ölçüm ile Spring Cloud Resilience4J devre kesici ölçümlerini toplama.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fedebd9182c168b9b7c455d5f6726e66720e0a8b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479169"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Mikro ölçüm (Önizleme) ile yay bulutu Resilience4J devre kesici ölçümlerini toplayın

Bu belgede, Application Insights Java-Process Agent ile Spring Cloud Resilience4j devre kesici ölçümlerinin nasıl toplanacağı açıklanmaktadır. Bu özellikle, mikro ölçüm ile Application Insights resilience4j devre kesici ölçümlerini izleyebilirsiniz.

Nasıl çalıştığını göstermek için [yay-bulut-devre kesici-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) ' i kullanıyoruz.

## <a name="prerequisites"></a>Önkoşullar

* [Application Insights Kılavuzu Için java In-Process aracısında](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights)Java In-Process aracısını etkinleştirin. 

* [Application Insights kılavuzunuzdan](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)resilience4j ölçümleri için boyut toplamayı etkinleştirin.

* Geliştirme bilgisayarı tarafından zaten kullanımda değilse git, Maven ve Java 'yı yükler.

## <a name="build-and-deploy-apps"></a>Uygulama oluşturma ve dağıtma

Aşağıdaki yordam uygulamaları oluşturur ve dağıtır.

1. Tanıtım deposunu kopyalayın ve derleyin.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Uç noktalarla uygulama oluşturma

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Uygulamaları dağıtın.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Resilience4j için gereken bağımlılığı ekleyin:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * Müşteri kodu, `CircuitBreakerFactory` `bean` bir yay bulutu devre kesici Başlatıcı eklediğinizde otomatik olarak oluşturulan olarak uygulanan API 'yi kullanmalıdır. Ayrıntılar için bkz. [yay bulutu devre kesici](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * Aşağıdaki 2 bağımlılıklar yukarıdaki resilient4j paketleriyle çakışıyor.  Müşterinin bunları içermediğinden emin olun.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Ağ Geçidi uygulamaları tarafından sunulan URL 'ye gidin ve bu uç noktaya [yay-bulut-devre-kesici-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) ' den aşağıdaki gibi erişin:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Portaldan Resilence4j ölçümlerini bulma

1. Azure yay bulut portalından **Application Insights** dikey penceresini seçin ve **Application Insights**' a tıklayın.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. **Application Insights** sayfasından **ölçümler** ' i seçin.  **Ölçüm ad** alanından **Azure. ApplicationInsights** ' ı seçin.  Ayrıca, **ortalamaya** sahip **resilience4j_circuitbreaker_buffered_calls** ölçümleri de seçin.

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. **Resilience4j_circuitbreaker_calls** ölçümleri ve **Ortalama** öğesini seçin.

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. **Resilience4j_circuitbreaker_calls** ölçümleri ve **Ortalama** öğesini seçin.  **Filtre Ekle**' ye tıklayın ve ardından **CreateNewAccount** olarak ad ' ı seçin.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. **Resilience4j_circuitbreaker_calls** ölçümleri ve **Ortalama** öğesini seçin.  Ardından **bölmeyi Uygula**' ya tıklayın ve **tür**' i seçin.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. **Resilience4j_circuitbreaker_calls**, '**resilience4j_circuitbreaker_buffered_calls** ve **resilience4j_circuitbreaker_slow_calls** ölçümlerini **Ortalama** olarak seçin.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Ayrıca bkz.

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Dağıtılmış izleme](spring-cloud-howto-distributed-tracing.md)
* [Devre kesici panosu](spring-cloud-tutorial-circuit-breaker.md)
