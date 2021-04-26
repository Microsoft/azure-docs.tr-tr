---
title: Hızlı başlangıç-Günlükler, ölçümler ve izleme ile Azure yay bulut uygulamalarını Izleme
description: Günlük akışını, Log Analytics, ölçümleri ve izlemeyi kullanarak Azure Spring Cloud 'daki Palametrik örnek uygulamalarını izleyin.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 907bf06323d13b2d26dec5003e4739f2ae9faf74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378525"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Hızlı başlangıç: Günlükler, ölçümler ve izleme ile Azure yay bulut uygulamalarını Izleme

::: zone pivot="programming-language-csharp"
Azure Spring Cloud 'daki yerleşik izleme özelliği sayesinde, karmaşık sorunları ayıklayabilir ve izleyebilirsiniz. Azure Spring Cloud, Azure 'ın [Application Insights](../azure-monitor/app/app-insights-overview.md) [Şirket içinde dağıtılmış izlemeyi](https://steeltoe.io/docs/3/tracing/distributed-tracing) tümleştirmektedir. Bu tümleştirme, Azure portal güçlü Günlükler, ölçümler ve dağıtılmış izleme yeteneği sağlar.

Aşağıdaki yordamlarda, önceki hızlı başlangıçlarda dağıttığınız örnek uygulamayla günlük akışı, Log Analytics, ölçüm ve dağıtılmış Izlemenin nasıl kullanılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* Bu serideki önceki hızlı başlangıçlarını doldurun:

  * [Azure yay bulut hizmeti sağlayın](spring-cloud-quickstart-provision-service-instance.md).
  * [Azure yay bulut yapılandırması sunucusunu ayarlayın](spring-cloud-quickstart-setup-config-server.md).
  * [Uygulamalar oluşturun ve dağıtın](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Günlükler

Azure Spring Cloud 'da günlükleri görmenin iki yolu vardır: uygulama örneği başına gerçek zamanlı günlüklerin **günlüğünü** veya Gelişmiş sorgu özelliğine sahip toplanmış günlükler için **Log Analytics** .

### <a name="log-streaming"></a>Günlük akışı

Azure CLı 'de günlük akışını aşağıdaki komutla birlikte kullanabilirsiniz.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Aşağıdaki örneğe benzer bir çıktı görürsünüz:

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> `az spring-cloud app logs -h`Daha fazla parametre ve günlük akışı işlevini araştırmak için kullanın.

### <a name="log-analytics"></a>Log Analytics

1. Azure portal **hizmete gidin | Genel Bakış** sayfası ve **Izleme** bölümünde **Günlükler** ' i seçin. Azure yay bulutu için örnek sorgulardan birinde **Çalıştır** ' ı seçin.

   [![Günlük Analizi girişi ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Görüntüyü uyarı ve hata günlükleriyle sınırlayan WHERE yan tümcesini kaldırmak için sorguyu düzenleyin.

1. Ardından `Run` öğesini seçin ve günlükleri görürsünüz. Sorgu yazma hakkında daha fazla bilgi için bkz. [Azure Log Analytics docs](../azure-monitor/logs/get-started-queries.md) .

   [![Günlük Analizi sorgusu-Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

1. Log Analytics 'de kullanılan sorgu dili hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlük sorguları](/azure/data-explorer/kusto/query/). Tüm Log Analytics günlüklerinizi merkezi bir istemciden sorgulamak için [Azure Veri Gezgini](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)' e göz atın.

## <a name="metrics"></a>Ölçümler

1. Azure portal **hizmete gidin | Genel Bakış** sayfası ve **Izleme** bölümünde **ölçümler** ' i seçin. **Ölçüm** açılan penceresinde **performans (.net)** veya **istek (.net)** altındaki .net ölçümlerinden birini seçerek ve `Avg` Bu ölçüm için zaman çizelgesini görmek üzere **toplama** için ilk ölçümünüzü ekleyin.

   [![Ölçüm girişi-Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Araç çubuğunda **Filtre Ekle** ' ye tıklayın, `App=solar-system-weather` CPU kullanımını yalnızca **Solar-Sistem hava durumu** uygulaması için görmek üzere seçin.

   [![Ölçülerde filtre kullanma-Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Önceki adımda oluşturulan filtreyi kapatın, **bölmeyi Uygula**' yı seçin ve `App` farklı uygulamalar tarafından CPU kullanımını görmek için **değerler** ' i seçin.

   [![Ölçümlere bölme Uygula-Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Dağıtılmış izleme

1. Azure portal **hizmete gidin | Genel Bakış** sayfası ve **Izleme** bölümünde **Dağıtılmış izleme** ' yi seçin. Ardından sağdaki **uygulama haritasını görüntüle** sekmesini seçin.

   [![Dağıtılmış izleme girişi-Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Artık uygulamalar arasındaki çağrıların durumunu görebilirsiniz. 

   [![Dağıtılmış izlemeye genel bakış-Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. HTTP yöntemlerine göre en yavaş çağrılar gibi daha fazla ayrıntı görmek için **güneş-sistem durumu** ve **Planet-Hava durumu-sağlayıcı** arasındaki bağlantıyı seçin.

   [![Dağıtılmış izleme-Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Son olarak, daha güçlü yerleşik performans analizini araştırmak için **performansı araştır** ' ı seçin.

   [![Dağıtılmış izleme performansı-Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud 'daki yerleşik izleme özelliği sayesinde, karmaşık sorunları ayıklayabilir ve izleyebilirsiniz. Azure Spring Cloud, Azure 'ın [Application Insights](../azure-monitor/app/app-insights-overview.md) [yay bulutuna](https://spring.io/projects/spring-cloud-sleuth) karşı tümleşir. Bu tümleştirme, Azure portal güçlü Günlükler, ölçümler ve dağıtılmış izleme yeteneği sağlar. Aşağıdaki yordamlarda, dağıtılmış Plımetrik uygulamalarıyla günlük akışı, Log Analytics, ölçüm ve dağıtılmış izlemenin nasıl kullanılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Önceki adımları doldurun: 

* [Azure yay bulutu örneği sağlama](spring-cloud-quickstart-provision-service-instance.md)
* [Yapılandırma sunucusunu ayarlama](spring-cloud-quickstart-setup-config-server.md)
* [Uygulamalar oluşturun ve dağıtın](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Günlükler

Azure Spring Cloud 'da günlükleri görmenin iki yolu vardır: uygulama örneği başına gerçek zamanlı günlüklerin **günlüğünü** veya Gelişmiş sorgu özelliğine sahip toplanmış günlükler için **Log Analytics** .

### <a name="log-streaming"></a>Günlük akışı

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Azure CLı 'de günlük akışını aşağıdaki komutla birlikte kullanabilirsiniz.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Şu şekilde Günlükler görüntülenir:

[![Azure CLI ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) 'Dan günlük akışı](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> `az spring-cloud app logs -h`Daha fazla parametre ve günlük akışı işlevlerini araştırmak için kullanın.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Azure Toolkit for IntelliJ kullanarak günlükleri almak için:

1. **Azure Gezgini**' ni ve ardından **yay bulutu**' nı seçin.

1. Çalışan uygulamaya sağ tıklayın.

1. Açılan listeden **akış günlükleri** ' ni seçin.

   ![Akış günlüklerini seçme](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. **Örnek** seçin.

   ![Örnek seçin](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Akış günlüğü, çıkış penceresinde görünür olur.

   ![Akış günlüğü çıkışı](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. **Hizmete git | Genel Bakış** sayfası ve **Izleme** bölümünde **Günlükler** ' i seçin. Azure yay bulutu için örnek sorgulardan birinde **Çalıştır** ' a tıklayın. 

   [![Günlük Analizi girişi ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Ardından filtrelenmiş günlükleri görürsünüz. Sorgu yazma hakkında daha fazla bilgi için bkz. [Azure Log Analytics docs](../azure-monitor/logs/get-started-queries.md) .

   [![Günlük Analizi sorgusu ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

1. Log Analytics 'de kullanılan sorgu dili hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlük sorguları](/azure/data-explorer/kusto/query/). Tüm Log Analytics günlüklerinizi merkezi bir istemciden sorgulamak için [Azure Veri Gezgini](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)' e göz atın.

## <a name="metrics"></a>Ölçümler

1. **Hizmete git | Genel Bakış** sayfası ve **Izleme** bölümünde **ölçümler** ' i seçin. `system.cpu.usage` **Ölçüm** için ' i seçerek ve `Avg` toplam CPU kullanımı için zaman çizelgesini görmek üzere **toplama** için ' i seçerek ilk ölçüsünü ekleyin.

   [![Ölçüm girişi ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Yukarıdaki araç çubuğundan **Filtre Ekle** ' ye tıklayın, `App=Gateway` yalnızca **ağ geçidi** uygulamasının CPU kullanımını görmek için seçin.

   [![Ölçümlerde ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) filtre kullanma](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Yukarıda oluşturulan filtreyi kapatın, **bölmeyi Uygula** ' ya tıklayın ve `App` farklı uygulamalar tarafından CPU kullanımını görmek için **değerler** ' i seçin.

   [![Ölçümlere ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) bölme Uygula](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Dağıtılmış izleme

1. **Hizmete git | Genel Bakış** sayfası ve **Izleme** bölümünde **Dağıtılmış izleme** ' yi seçin. Ardından sağdaki **uygulama haritasını görüntüle** sekmesine tıklayın.

   [![Dağıtılmış izleme girdisi ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Artık, Palametrik uygulamalar arasındaki çağrıların durumunu görebilirsiniz. 

   [![Dağıtılmış izlemeye genel bakış ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. HTTP yöntemlerine göre en yavaş çağrılar gibi daha fazla ayrıntı görmek için **ağ geçidi** ve **Hesap-hizmet** arasındaki bağlantıya tıklayın.

   [![Dağıtılmış izleme ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Son olarak, daha güçlü yerleşik performans analizini araştırmak için **performansı araştır** ' a tıklayın.

   [![Dağıtılmış izleme performansı ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçlarda, abonelikleriniz varsa ücretlendirmeye devam edecek Azure kaynakları oluşturdunuz. Gelecekte bu kaynaklara ihtiyaç duymazsanız, portalı kullanarak veya Cloud Shell aşağıdaki komutu çalıştırarak kaynak grubunu silin:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Daha önceki bir hızlı başlangıçta, varsayılan kaynak grubu adını da ayarlarsınız. Sonraki hızlı başlangıca devam etmeyi planlamıyorsanız, aşağıdaki CLı komutunu çalıştırarak bu varsayılanı kaldırın:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Spring Cloud 'ın daha fazla izleme özelliğini araştırmak için bkz.:

> [!div class="nextstepaction"]
> [Tanı hizmetleri](diagnostic-services.md)
>
> [Dağıtılmış izleme](spring-cloud-howto-distributed-tracing.md)
>
> [Gerçek zamanlı olarak günlük akışı yapma](spring-cloud-howto-log-streaming.md)
