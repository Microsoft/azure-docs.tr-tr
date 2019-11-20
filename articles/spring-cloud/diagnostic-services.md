---
title: Azure yay bulutu 'nda günlükleri ve ölçümleri çözümleme | Microsoft Docs
description: Azure Spring Cloud 'da tanılama verilerini çözümlemeyi öğrenin
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: ebe438bd2dc5b4921ce733001f3c9df19bc592fe
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607852"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Tanılama ayarlarıyla günlükleri ve ölçümleri çözümleme

Azure Spring Cloud 'ın tanılama işlevini kullanarak, aşağıdaki hizmetlerden herhangi biriyle günlükleri ve ölçümleri çözümleyebilirsiniz:

* Verilerin depolama alanına yazılması gerekmeden hemen yazıldığı Azure Log Analytics kullanın.
* Denetim veya el ile inceleme için bunları bir depolama hesabına kaydedin. Saklama süresini (gün cinsinden) belirtebilirsiniz.
* Bunları, üçüncü taraf bir hizmet veya özel analiz çözümünün alımı için Olay Hub 'ınıza akışla ayırın.

Başlamak için, bu hizmetlerden birini verileri alacak şekilde etkinleştirin. Log Analytics yapılandırma hakkında bilgi edinmek için [Azure izleyici 'de Log Analytics kullanmaya başlama](../azure-monitor/log-query/get-started-portal.md)konusunu inceleyin. 

## <a name="configure-diagnostics-settings"></a>Tanılama ayarlarını yapılandırma

1. Azure portal Azure Spring Cloud örneğinize gidin.
1. **Tanılama ayarları** seçeneğini belirleyin ve ardından **Tanılama ayarı Ekle**' yi seçin.
1. Ayar için bir ad girin ve ardından günlükleri nereye göndermek istediğinizi seçin. Aşağıdaki üç seçenekten herhangi bir birleşimini seçebilirsiniz:
    * **Bir depolama hesabına Arşivle**
    * **Bir olay hub 'ına akış**
    * **Log Analytics gönder**

1. İzlemek istediğiniz günlük kategorisini ve ölçüm kategorisini seçin ve ardından saklama süresini (gün cinsinden) belirtin. Saklama süresi yalnızca depolama hesabı için geçerlidir.
1. **Kaydet**’i seçin.

> [!NOTE]
> Günlüklerin veya ölçümlerin ne zaman yayıldığını ve depolama hesabınızda, Olay Hub 'ında veya Log Analytics göründükleri zaman arasında 15 dakikalık bir boşluk olabilir.

## <a name="view-the-logs"></a>Günlükleri görüntüleme

### <a name="use-log-analytics"></a>Log Analytics kullanma

1. Azure portal sol bölmedeki **Log Analytics**' ı seçin.
1. Tanılama ayarlarınızı eklediğinizde seçtiğiniz Log Analytics çalışma alanını seçin.
1. **Günlük araması** bölmesini açmak için **Günlükler**' i seçin.
1. **Günlük** araması kutusuna, şöyle bir basit sorgu girin:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Arama sonucunu görüntülemek için **Çalıştır**' ı seçin.
1. Filtre koşulunu ayarlayarak belirli bir uygulamanın veya örneğin günlüklerinde arama yapabilirsiniz:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Log Analytics 'de kullanılan sorgu dili hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlük sorguları](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Depolama hesabınızı kullanın 

1. Azure portal sol bölmede **depolama hesapları**' nı seçin.

1. Tanılama ayarlarınızı eklediğinizde seçtiğiniz depolama hesabını seçin.
1. **BLOB kapsayıcısı** bölmesini açmak için **Bloblar**' ı seçin.
1. Uygulama günlüklerini gözden geçirmek için, **Öngörüler-logs-applicationconsole**adlı bir kapsayıcı arayın.
1. Uygulama ölçümlerini gözden geçirmek için, **Öngörüler-ölçümler-pt1m**adlı bir kapsayıcı arayın.

Bir depolama hesabına tanılama bilgileri gönderme hakkında daha fazla bilgi edinmek için bkz. [Azure depolama 'da tanılama verilerini depolama ve görüntüleme](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Olay Hub 'ınızı kullanın

1. Azure portal sol bölmedeki **Event Hubs**' ı seçin.

1. Tanılama ayarlarınızı eklediğinizde seçtiğiniz Olay Hub 'ını arayıp seçin.
1. **Olay Hub 'ı liste** bölmesini açmak için **Event Hubs**' yi seçin.
1. Uygulama günlüklerini gözden geçirmek için, **Öngörüler-logs-applicationconsole**adlı bir olay hub 'ı arayın.
1. Uygulama ölçümlerini gözden geçirmek için, **Öngörüler-ölçümler-pt1m**adlı bir olay hub 'ı arayın.

Tanılama bilgilerini bir olay hub 'ına gönderme hakkında daha fazla bilgi için, bkz. [Event Hubs kullanarak etkin yoldaki Azure Tanılama verileri akışı](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Günlükleri çözümleyin

Azure Log Analytics, günlüklerinizi analiz etmek için Sorgulayabileceğiniz kusto sağlar. Kusto kullanarak günlükleri sorgulamaya hızlı bir giriş için [Log Analytics öğreticisini](../azure-monitor/log-query/get-started-portal.md)gözden geçirin.

Uygulama günlükleri, uygulamanızın sistem durumu, performansı ve daha fazlası hakkında önemli bilgiler sağlar. Sonraki bölümlerde, uygulamanızın geçerli ve geçmiş durumlarını anlamanıza yardımcı olacak bazı basit sorgular bulunur.

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

Azure Izleyici, Log Analytics kullanarak uygulama günlüklerini sorgulamak için kapsamlı destek sağlar. Bu hizmet hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de günlük sorgularıyla çalışmaya başlama](../azure-monitor/log-query/get-started-queries.md). Uygulama günlüklerinizi çözümlemek üzere sorgu oluşturma hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/log-query/log-query-overview.md).
