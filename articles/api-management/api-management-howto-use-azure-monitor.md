---
title: Öğretici-Azure API Management yayımlanan API 'Leri Izleme | Microsoft Docs
description: Azure API Management 'de API 'lerinizi izlemek için ölçümleri, uyarıları, etkinlik günlüklerini ve kaynak günlüklerini nasıl kullanacağınızı öğrenmek için bu öğreticideki adımları izleyin.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 1cb902c4b59193c46dbeca47bb355f0695a0f2c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572796"
---
# <a name="tutorial-monitor-published-apis"></a>Öğretici: yayımlanan API 'Leri Izleme

Azure Izleyici ile Azure API Management hizmetinizden gelen ölçümleri veya günlükleri görselleştirin, sorgulayabilir, yönlendirebilir, arşivleyebilir ve bunlar üzerinde işlem yapabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * API'nizin ölçümlerini görüntüleme 
> * Uyarı kuralı ayarlama 
> * Etkinlik günlüklerini görüntüleme
> * Kaynak günlüklerini etkinleştirme ve görüntüleme

Ayrıca, API 'nizin kullanımını ve performansını izlemek için API Management yerleşik [analizlerini](howto-use-analytics.md) de kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ Ayrıca, şu öğreticiyi tamamlayın: [İlk API'nizi içeri aktarma ve yayımlama](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>API'lerinizin ölçümlerini görüntüleme

API Management [ölçümleri](../azure-monitor/essentials/data-platform-metrics.md) her dakikada sunarak API 'lerinizin durumunu ve durumunu neredeyse gerçek zamanlı görünürlük sağlar. En sık kullanılan iki ölçüm aşağıda verilmiştir. Tüm kullanılabilir ölçümlerin bir listesi için bkz. [desteklenen ölçümler](../azure-monitor/essentials/metrics-supported.md#microsoftapimanagementservice).

* **Kapasite** -APIM hizmetlerinizi yükseltme/eski sürüme düşürme hakkında kararlar almanıza yardımcı olur. Ölçüm, dakika başına yayılır ve raporlama zamanındaki ağ geçidi kapasitesini yansıtır. Ölçüm, CPU ile bellek kullanımı gibi ağ geçidi kaynakları temel alınarak hesaplanan 0-100 aralığında değişir.
* **İstekler** -API Management hizmetleriniz aracılığıyla API trafiğini çözümlemenize yardımcı olur. Ölçüm, dakika başına yayılır ve yanıt kodları, konum, ana bilgisayar adı ve hatalar dahil olmak üzere boyutlara sahip ağ geçidi isteklerinin sayısını raporlar. 

> [!IMPORTANT]
> Aşağıdaki ölçümler 2019 Mayıs 'Tan itibaren kullanımdan kaldırılmıştır ve 2023 Ağustos 'ta kullanımdan kaldırılacak: toplam ağ geçidi Isteği, başarılı ağ geçidi Istekleri, yetkisiz ağ geçidi Istekleri, başarısız ağ geçidi istekleri, diğer ağ geçidi Istekleri. Lütfen eşdeğer işlevselliği sağlayan Istek ölçüsüne geçiş yapın.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="API Management genel bakış 'da ölçümlerin ekran görüntüsü":::

Ölçümlere erişmek için:

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin. **Genel bakış** sayfasında API 'leriniz için temel ölçümleri gözden geçirin.
1. Ölçümleri ayrıntılı araştırmak için sayfanın alt kısmındaki menüden **ölçümler** ' i seçin.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Izleme menüsünde ölçüm öğesinin ekran görüntüsü":::

1. Açılan listeden ilgilendiğiniz ölçümleri seçin. Örneğin, **istekler**. 
1. Grafikte, API çağrılarının toplam sayısı gösterilmektedir.
1. Grafik, **istek** ölçümünün boyutları kullanılarak filtrelenebilir. Örneğin, **Filtre Ekle**' yi seçin, **arka uç yanıt kodu kategorisini** seçin, değer olarak 500 girin. Artık grafik, API arka ucunda başarısız olan isteklerin sayısını gösterir.   

## <a name="set-up-an-alert-rule"></a>Uyarı kuralı ayarlama 

Ölçümler ve etkinlik günlüklerine göre [Uyarı](../azure-monitor/alerts/alerts-metric-overview.md) alabilirsiniz. Azure Izleyici, tetiklendiğinde aşağıdakileri yapmak için [bir uyarı yapılandırmanıza](../azure-monitor/alerts/alerts-metric.md) olanak sağlar:

* E-posta bildirimi gönderme
* Web kancası çağırma
* Bir Azure Mantıksal Uygulamasını çağırma

Bir istek ölçüsüne göre örnek bir uyarı kuralı yapılandırmak için:

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. Sayfanın alt kısmındaki menü çubuğundan **Uyarılar** ' ı seçin.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Izleme menüsünde uyarı seçeneğinin ekran görüntüsü":::

1. **+ Yeni uyarı kuralı**'nı seçin.
1. **Uyarı kuralı oluştur** penceresinde koşul ' ı **seçin**.
1. **Sinyal mantığını Yapılandır** penceresinde:
    1. **Sinyal türü**' nde **ölçümler**' i seçin.
    1. **Sinyal adı**' nda **istekler**' i seçin.
    1. **Boyutlara göre Böl**' de **Boyut adı**' nda **ağ geçidi yanıt kodu kategorisi**' ni seçin.
    1. **Boyut değerleri**' nde, yetkisiz veya geçersiz istekler gibi istemci hataları için **4xx**' i seçin.
    1. **Uyarı mantığı**' nda, uyarının tetiklenme sonrasında bir eşik belirtin ve **bitti**' yi seçin.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Sinyal mantığı pencerelerini yapılandırma ekran görüntüsü":::

1. Mevcut bir eylem grubu seçin veya yeni bir tane oluşturun. Aşağıdaki örnekte yeni bir eylem grubu oluşturulur. Bir bildirim e-postası gönderilecek admin@contoso.com . 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Yeni eylem grubu için bildirimlerin ekran görüntüsü":::

1. Uyarı kuralının adını ve açıklamasını girip önem derecesini seçin. 
1. **Uyarı kuralı oluşturma**’yı seçin.
1. Şimdi, bir API anahtarı olmadan konferans API 'sini çağırarak uyarı kuralını test edin. Örnek:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    Değerlendirme süresine göre bir uyarı tetiklenir ve e-posta gönderilir admin@contoso.com . 

    Uyarılar, API Management örneği için **Uyarılar** sayfasında da görünür.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Portalda uyarı ekran görüntüsü":::

## <a name="activity-logs"></a>Etkinlik günlükleri

Etkinlik günlükleri, API Management hizmetleriniz üzerinde gerçekleştirilen işlemler hakkında bilgi sağlar. Etkinlik günlüklerini kullanarak, API Management hizmetleriniz üzerinde gerçekleştirilen herhangi bir yazma işlemi (PUT, POST, DELETE) için "ne, kim ve ne zaman" sorularını yanıtlayabilirsiniz.

> [!NOTE]
> Etkinlik günlükleri, okuma (GET) işlemlerini ya da Azure portalında gerçekleştirilen veya özgün Yönetim API’leri kullanan işlemleri içermez.

API Management hizmetinizdeki etkinlik günlüklerine veya Azure İzleyici’deki tüm Azure kaynaklarınızın günlüklerine erişebilirsiniz. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Portalda Etkinlik günlüğünün ekran görüntüsü":::

Etkinlik günlüğünü görüntülemek için:

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.

1. **Etkinlik günlüğü**' nü seçin.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Izleme menüsündeki etkinlik günlüğü öğesinin ekran görüntüsü":::
1. İstenen filtreleme kapsamını seçin ve ardından **uygulayın**.

## <a name="resource-logs"></a>Kaynak günlükleri

Kaynak günlükleri, denetim ve sorun giderme amacıyla önemli işlemler ve hatalar hakkında zengin bilgiler sağlar. Kaynak günlükleri etkinlik günlüklerinden farklıdır. Etkinlik günlüğü, Azure kaynaklarınız üzerinde gerçekleştirilen işlemlere yönelik öngörüler sağlar. Kaynak günlükleri, kaynağınızın gerçekleştirdiği işlemlere ilişkin öngörüler sağlar.

Kaynak günlüklerini yapılandırmak için:

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
2. **Tanılama ayarları**' nı seçin.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Izleme menüsündeki Tanılama ayarları öğesinin ekran görüntüsü":::

1. **+ Tanılama ayarı Ekle**' yi seçin.
1. Toplamak istediğiniz günlükleri veya ölçümleri seçin.

   Kaynak günlüklerini, ölçümlerle birlikte bir depolama hesabına arşivleyebilirsiniz, bunları bir olay hub 'ına bağlayabilir veya bir Log Analytics çalışma alanına gönderebilirsiniz. 

Daha fazla bilgi için bkz. [Platform günlüklerini ve ölçümlerini farklı hedeflere göndermek için Tanılama ayarları oluşturma](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="view-diagnostic-data-in-azure-monitor"></a>Azure Izleyici 'de tanılama verilerini görüntüleme

Log Analytics çalışma alanında GatewayLogs veya ölçümler koleksiyonunu etkinleştirirseniz verilerin Azure Izleyici 'de görünmesi birkaç dakika sürebilir. Verileri görüntülemek için:

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. Sayfanın alt tarafında bulunan menüden **günlükleri** seçin.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Izleme menüsündeki Günlükler öğesinin ekran görüntüsü":::

Verileri görüntülemek için sorguları çalıştırın. Birkaç [örnek sorgu](../azure-monitor/logs/example-queries.md) sağlanır veya kendi kendinize çalıştırılır. Örneğin, aşağıdaki sorgu, GatewayLogs tablosundan en son 24 saat verileri alır:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

API Management için kaynak günlüklerini kullanma hakkında daha fazla bilgi için bkz.:

* [Azure izleyici Log Analytics kullanmaya başlayın](../azure-monitor/logs/log-analytics-tutorial.md)veya [Log Analytics demo ortamını](https://portal.loganalytics.io/demo)deneyin.

* [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/logs/log-query-overview.md).

Aşağıdaki JSON başarılı bir API isteği için GatewayLogs 'ta örnek bir giriş gösterir. Ayrıntılar için bkz. [şema başvurusu](gateway-log-schema-reference.md). 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * API'nizin ölçümlerini görüntüleme
> * Uyarı kuralı ayarlama 
> * Etkinlik günlüklerini görüntüleme
> * Kaynak günlüklerini etkinleştirme ve görüntüleme


Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [İzleme çağrıları](api-management-howto-api-inspector.md)