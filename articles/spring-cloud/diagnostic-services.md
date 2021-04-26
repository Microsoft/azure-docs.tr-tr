---
title: Azure yay bulutu 'nda günlükleri ve ölçümleri çözümleme | Microsoft Docs
description: Azure Spring Cloud 'da tanılama verilerini çözümlemeyi öğrenin
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6472ba7dd055de97a1855211f21fd0c75eea814f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874126"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Tanılama ayarlarıyla günlükleri ve ölçümleri çözümleme

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Azure Spring Cloud 'ın tanılama işlevini kullanarak, aşağıdaki hizmetlerden herhangi biriyle günlükleri ve ölçümleri çözümleyebilirsiniz:

* Verilerin Azure Storage 'a yazıldığı Azure Log Analytics kullanın. Log Analytics Günlükler dışarı aktarılırken bir gecikme olur.
* Denetim veya el ile inceleme için günlükleri bir depolama hesabına kaydedin. Saklama süresini (gün cinsinden) belirtebilirsiniz.
* Bir üçüncü taraf hizmeti veya özel analiz çözümünün alımı için Olay Hub 'ınıza Günlükler akışını yapın.

İzlemek istediğiniz günlük kategorisini ve ölçüm kategorisini seçin.

> [!TIP]
> Günlüklerinizi akışa almak mı istiyorsunuz? Bu [Azure CLI komutuna](/cli/azure/spring-cloud/app#az_spring_cloud_app_logs)göz atın!

## <a name="logs"></a>Günlükler

|Günlük | Description |
|----|----|
| **ApplicationConsole** | Tüm müşteri uygulamalarının konsol günlüğü. |
| **Sistem günlükleri** | Şu anda, bu kategoride yalnızca [Spring Cloud config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) kaydedilir. |

## <a name="metrics"></a>Ölçümler

Ölçümlerin tüm listesi için bkz. [yay bulut ölçümleri](./spring-cloud-concept-metrics.md#user-metrics-options).

Başlamak için, bu hizmetlerden birini verileri alacak şekilde etkinleştirin. Log Analytics yapılandırma hakkında bilgi edinmek için bkz. [Azure izleyici 'de Log Analytics kullanmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Tanılama ayarlarını yapılandırma

1. Azure portal Azure Spring Cloud örneğinize gidin.
1. **Tanılama ayarları** seçeneğini belirleyin ve ardından **Tanılama ayarı Ekle**' yi seçin.
1. Ayar için bir ad girin ve ardından günlükleri nereye göndermek istediğinizi seçin. Aşağıdaki üç seçenekten herhangi bir birleşimini seçebilirsiniz:
    * **Bir depolama hesabına Arşivle**
    * **Olay hub’ına akış yapma**
    * **Log Analytics’e gönderme**

1. İzlemek istediğiniz günlük kategorisini ve ölçüm kategorisini seçin ve ardından saklama süresini (gün cinsinden) belirtin. Saklama süresi yalnızca depolama hesabı için geçerlidir.
1. **Kaydet**’i seçin.

> [!NOTE]
> 1. Günlüklerin veya ölçümlerin ne zaman yayıldığını ve depolama hesabınızda, Olay Hub 'ında veya Log Analytics göründükleri zaman arasında 15 dakikalık bir boşluk olabilir.
> 1. Azure yay bulutu örneği silinmişse veya taşınırsa, işlem, **Tanılama ayarları** kaynaklarıyla basamaklanmaz. **Tanılama ayarlarının** kaynakları, üst öğesiyle (Azure yay bulutu örneği) karşı işlem öncesinde el ile silinmelidir. Aksi takdirde, silinen tek kaynak KIMLIĞIYLE yeni bir Azure yay bulutu örneği sağlandıysa veya Azure Spring Cloud Instance geri taşınırsa, önceki **Tanılama ayarları** kaynakları genişlemeye devam eder.

## <a name="view-the-logs-and-metrics"></a>Günlükleri ve ölçümleri görüntüleme
Günlükleri ve ölçümleri aşağıdaki başlıklar altında açıklandığı gibi görüntülemek için çeşitli yöntemler vardır.

### <a name="use-the-logs-blade"></a>Günlükler dikey penceresini kullanma

1. Azure portal Azure Spring Cloud örneğinize gidin.
1. **Günlük araması** bölmesini açmak için **Günlükler**' i seçin.
1. **Tablolar** arama kutusunda
   * Günlükleri görüntülemek için, şöyle bir basit sorgu girin:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Ölçümleri görüntülemek için, şöyle bir basit sorgu girin:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Arama sonucunu görüntülemek için **Çalıştır**' ı seçin.

### <a name="use-log-analytics"></a>Log Analytics kullanma

1. Azure portal sol bölmedeki **Log Analytics**' ı seçin.
1. Tanılama ayarlarınızı eklediğinizde seçtiğiniz Log Analytics çalışma alanını seçin.
1. **Günlük araması** bölmesini açmak için **Günlükler**' i seçin.
1. **Tablolar** arama kutusunda,
   * günlükleri görüntülemek için, şöyle bir basit sorgu girin:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * ölçümleri görüntülemek için, şöyle bir basit sorgu girin:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Arama sonucunu görüntülemek için **Çalıştır**' ı seçin.
1. Filtre koşulunu ayarlayarak belirli bir uygulamanın veya örneğin günlüklerinde arama yapabilirsiniz:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` büyük/küçük harfe duyarlıdır, ancak `=~` değildir.

Log Analytics 'de kullanılan sorgu dili hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlük sorguları](/azure/data-explorer/kusto/query/). Tüm Log Analytics günlüklerinizi merkezi bir istemciden sorgulamak için [Azure Veri Gezgini](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)' e göz atın.

### <a name="use-your-storage-account"></a>Depolama hesabınızı kullanın

1. Azure portal, sol gezinti panelinde veya arama kutusunda **depolama hesapları** bulun.
1. Tanılama ayarlarınızı eklediğinizde seçtiğiniz depolama hesabını seçin.
1. **BLOB kapsayıcısı** bölmesini açmak için **Bloblar**' ı seçin.
1. Uygulama günlüklerini gözden geçirmek için, **Öngörüler-logs-applicationconsole** adlı bir kapsayıcı arayın.
1. Uygulama ölçümlerini gözden geçirmek için, **Öngörüler-ölçümler-pt1m** adlı bir kapsayıcı arayın.

Bir depolama hesabına tanılama bilgileri gönderme hakkında daha fazla bilgi edinmek için bkz. [Azure depolama 'da tanılama verilerini depolama ve görüntüleme](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Olay Hub 'ınızı kullanın

1. Azure portal sol gezinti panelinde veya arama kutusunda **Event Hubs** bulun.

1. Tanılama ayarlarınızı eklediğinizde seçtiğiniz Olay Hub 'ını arayıp seçin.
1. **Olay Hub 'ı liste** bölmesini açmak için **Event Hubs**' yi seçin.
1. Uygulama günlüklerini gözden geçirmek için, **Öngörüler-logs-applicationconsole** adlı bir olay hub 'ı arayın.
1. Uygulama ölçümlerini gözden geçirmek için, **Öngörüler-ölçümler-pt1m** adlı bir olay hub 'ı arayın.

Tanılama bilgilerini bir olay hub 'ına gönderme hakkında daha fazla bilgi için, bkz. [Event Hubs kullanarak etkin yoldaki Azure Tanılama verileri akışı](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>Günlükleri çözümleyin

Azure Log Analytics, bir kusto altyapısıyla çalışıyor, böylece günlüklerinizi analiz için sorgulayabilirsiniz. Kusto kullanarak günlükleri sorgulamaya hızlı bir giriş için [Log Analytics öğreticisini](../azure-monitor/logs/log-analytics-tutorial.md)gözden geçirin.

Uygulama günlükleri, uygulamanızın sistem durumu, performansı ve daha fazlası hakkında önemli bilgiler ve ayrıntılı Günlükler sağlar. Sonraki bölümlerde, uygulamanızın geçerli ve geçmiş durumlarını anlamanıza yardımcı olacak bazı basit sorgular bulunur.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Azure Spring Cloud 'dan uygulama günlüklerini göster

Azure Spring Cloud 'dan uygulama günlüklerinin listesini gözden geçirmek için ilk olarak gösterilen en son günlüklere göre sıralanmış olarak sıralanmış aşağıdaki sorguyu çalıştırın:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Hata veya özel durum içeren günlük girişlerini göster

Bir hata veya özel durumdan bahseden sıralanmamış günlük girişlerini gözden geçirmek için aşağıdaki sorguyu çalıştırın:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Hataları bulmak için bu sorguyu kullanın veya belirli hata kodlarını veya özel durumları bulmak için sorgu terimlerini değiştirin.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Son bir saat içinde uygulamanız tarafından bildirilen hata ve özel durum sayısını göster

Son bir saat içinde uygulamanız tarafından günlüğe kaydedilen hata ve özel durumların sayısını görüntüleyen bir pasta grafiği oluşturmak için aşağıdaki sorguyu çalıştırın:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Uygulama günlüklerini sorgulama hakkında daha fazla bilgi edinin

Azure Izleyici, Log Analytics kullanarak uygulama günlüklerini sorgulamak için kapsamlı destek sağlar. Bu hizmet hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de günlük sorgularıyla çalışmaya başlama](../azure-monitor/logs/get-started-queries.md). Uygulama günlüklerinizi çözümlemek üzere sorgu oluşturma hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/logs/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Sık sorulan sorular (SSS)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Çok satırlı Java yığını izlemeleri tek satıra nasıl dönüştürülür?

Çok satırlı yığın izlemelerinizi tek bir satıra dönüştürmek için geçici bir çözüm vardır. Yığın izleme iletilerini yeniden biçimlendirmek için Java günlük çıkışını değiştirebilirsiniz ve yeni satır karakterlerini bir belirteçle değiştirin. Java Logback kitaplığı kullanıyorsanız, aşağıdaki gibi ekleyerek yığın izleme iletilerini yeniden biçimlendirebilirsiniz `%replace(%ex){'[\r\n]+', '\\n'}%nopex` :

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
Ardından Log Analytics yeni satır karakteri ile aşağıdaki gibi bir şekilde değiştirebilirsiniz:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Diğer Java günlük kitaplıkları için de aynı stratejiyi kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: ilk Azure Spring Cloud uygulamanızı dağıtın](spring-cloud-quickstart.md)
