---
title: Web kancası kullanarak mevcut sorun yönetimi sistemleri için Azure hizmet durumu bildirimlerini yapılandırma
description: Mevcut sorun yönetimi sisteminize hizmet durumu olayları hakkında kişiselleştirilmiş bildirimler gönderin.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 3/27/2018
ms.openlocfilehash: 8f84b43519c197797b39397cfd15c4f90444177c
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854376"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Sorun yönetim sistemleri için sistem durumu bildirimlerini yapılandırmak için Web kancası kullanın

Bu makalede, Azure hizmet durumu uyarılarının mevcut bildirim sisteminize Web kancaları aracılığıyla veri göndermek üzere nasıl yapılandırılacağı gösterilmektedir.

Hizmet durumu uyarılarını, bir Azure hizmeti olayı sizi etkilediği kısa mesaj veya e-posta ile bildirecek şekilde yapılandırabilirsiniz.

Ancak, kullanmayı tercih ettiğiniz bir dış bildirim sisteminiz zaten var olabilir. Bu makale, Web kancası yükünün en önemli bölümlerini tanımlar. Ayrıca, ilgili hizmet sorunları oluştuğunda size bildirimde bulunan özel uyarıların nasıl oluşturulacağını açıklar.

Önceden yapılandırılmış bir tümleştirme kullanmak istiyorsanız, bkz:
* [ServiceNow ile uyarıları yapılandırma](service-health-alert-webhook-servicenow.md)
* [Pagerharcı ile uyarıları yapılandırma](service-health-alert-webhook-pagerduty.md)
* [OpsGenie ile uyarıları yapılandırma](service-health-alert-webhook-opsgenie.md)

**Tanıtım videosunu izleyin:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Hizmet durumu Web kancası yükünü kullanarak özel bir bildirim yapılandırma
Kendi özel Web kancası tümleştirmenizi ayarlamak için, hizmet durumu bildirimi aracılığıyla gönderilen JSON yükünü ayrıştırmaya ihtiyacınız vardır.

[](../azure-monitor/platform/activity-log-alerts-webhook.md) Örnek`ServiceHealth` bir Web kancası yüküne bakın.

' A bakarak `context.eventSource == "ServiceHealth"`bir hizmet durumu uyarısı olduğunu doğrulayabilirsiniz. Aşağıdaki özellikler en ilgili özelliklerdir:
- **Data. Context. activityLog. Status**
- **Data. Context. activityLog. Level**
- **Data. Context. activityLog. SubscriptionID**
- **Data. Context. activityLog. Properties. title**
- **Data. Context. activityLog. Properties. ımpactstarttime**
- **Data. Context. activityLog. Properties. Communication**
- **Data. Context. activityLog. Properties. ımpactedservices**
- **Data. Context. activityLog. Properties. Trackingıd**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Bir olay için hizmet durumu panosu bağlantısı oluşturma
Özel bir URL oluşturarak masaüstü veya mobil cihazda hizmet sistem durumu panonuz için doğrudan bir bağlantı oluşturabilirsiniz. Aşağıdaki biçimde *SubscriptionID* 'Nizin *trackingıd* ve ilk üç ve son üç basamağını kullanın:

https<i></i>://App.Azure.com/h/ *&lt;trackingıd&gt;* /*ilk üç ve en son üç sayı SubscriptionID&gt; &lt;*

Örneğin, *SubscriptionID* 'niz Bba14129-e895-429b-8809-278e836ecdb3 ve *TRACKINGıD* 0DET-URB Ise, hizmet durumu URL 'niz şu şekilde olur:

https<i></i>://App.Azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Sorunun önem derecesini algılamak için düzeyi kullanın
En düşük ve en yüksek öneme sahip olan yükteki **Level** özelliği *bilgilendirme*, *Uyarı*, *hata*veya *kritik*olabilir.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Etkilenen Hizmetleri, olay kapsamını tespit etmek için ayrıştırın
Hizmet durumu uyarıları, birden çok bölgedeki ve hizmetlerde sorunlar hakkında bilgi verebilir. Tüm ayrıntıları almak için değerini `impactedServices`ayrıştırmalıdır.

İçinde olan içerik, atlamadığınızda, düzenli olarak ayrıştırılabilecek başka bir JSON nesnesi içeren kaçış [JSON](https://json.org/) dizesidir. Örneğin:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

geldiğinde

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Bu örnekte, için sorunlar gösterilmektedir:
- Avustralya Doğu ve Avustralya Güneydoğu ' teki uyarılar & ölçümleri.
- Avustralya Güneydoğu içinde "App Service".

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>HTTP POST isteği aracılığıyla Web kancası tümleştirmenizi test etme

Şu adımları uygulayın:

1. Göndermek istediğiniz hizmet durumu yükünü oluşturun. [Azure etkinlik günlüğü uyarıları Için Web kancalarında](../azure-monitor/platform/activity-log-alerts-webhook.md)örnek bir hizmet durumu Web kancası yüküne bakın.

1. Aşağıdaki gibi bir HTTP POST isteği oluşturun:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   "2XX-başarılı" yanıt almanız gerekir.

1. Tümleştirmenin başarıyla ayarlandığını onaylamak için [Pagerharcı](https://www.pagerduty.com/) 'e gidin.

## <a name="next-steps"></a>Sonraki adımlar
- [Etkinlik günlüğü uyarısı Web kancası şemasını](../azure-monitor/platform/activity-log-alerts-webhook.md)gözden geçirin. 
- [Hizmet durumu bildirimleri](../azure-monitor/platform/service-notifications.md)hakkında bilgi edinin.
- [Eylem grupları](../azure-monitor/platform/action-groups.md)hakkında daha fazla bilgi edinin.