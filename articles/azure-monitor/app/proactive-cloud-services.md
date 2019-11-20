---
title: Azure 'da Azure Tanılama tümleştirmesini kullanarak Azure Cloud Services sorunları hakkında uyarı Application Insights | Microsoft Docs
description: Azure Cloud Services Azure Application Insights başlatma hataları, Kilitlenmeler ve rol geri dönüşüm döngüleri gibi sorunları izleyin
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: b4404f033f5bdf221590e155640e4c0442601e18
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820642"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Azure 'da Azure tanılama tümleştirmesini kullanarak Azure Cloud Services sorunları hakkında uyarı edin Application Insights

Bu makalede, Azure Cloud Services (Web ve çalışan rolleri) başlatma hataları, Kilitlenmeler ve rol geri dönüşüm döngüleri gibi sorunları izleyen uyarı kurallarını nasıl ayarlayacağız.

Bu makalede açıklanan yöntem, [Application Insights tümleştirme Azure tanılama](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)ve Application Insights özelliği için en son yayınlanan [günlük uyarılarını](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) temel alır.

## <a name="define-a-base-query"></a>Temel sorgu tanımlama

Başlamak için, Windows olay günlüğü olaylarını, izleme kayıtları olarak Application Insights yakalanan Microsoft Azure kanalından alan bir temel sorgu tanımlayacağız.
Bu kayıtlar, Azure Cloud Services 'daki başlatma hataları, çalışma zamanı hataları ve geri dönüşüm döngüleri gibi çeşitli sorunları algılamak için kullanılabilir.

> [!NOTE]
> Aşağıdaki temel sorgu, 30 dakikalık bir zaman penceresinde sorunlar olup olmadığını denetler ve telemetri kayıtlarını kabul etmek için 10 dakikalık bir gecikme süresi varsayar. Bu varsayılanlar, uygun gördüğünüz şekilde yapılandırılabilir.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Belirli olay kimliklerini denetle

Windows olay günlüğü olaylarını aldıktan sonra, ilgili olay KIMLIĞI ve ileti özellikleri denetlenerek belirli sorunlar tespit edilebilir (aşağıdaki örneklere bakın).
Yukarıdaki sorgu sorgularını, yukarıdaki sorgulardan biriyle birleştirmeniz yeterlidir ve günlük uyarı kuralını tanımlarken bu Birleşik sorgu kullanılır.

> [!NOTE]
> Aşağıdaki örneklerde, analiz edilen zaman penceresinde üçten fazla olay bulunursa bir sorun tespit edilir. Bu varsayılan, uyarı kuralının duyarlılığını değiştirecek şekilde yapılandırılabilir.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Uyarı oluşturma

Application Insights kaynağınız içindeki gezinti menüsünde **Uyarılar**' a gidin ve ardından **Yeni uyarı kuralı**' nı seçin.

![Kural oluştur ekran görüntüsü](./media/proactive-cloud-services/001.png)

**Kural oluştur** penceresinde, **Uyarı koşulunu tanımla** bölümünde **Ölçüt Ekle**' ye tıklayın ve ardından **özel günlük araması**' nı seçin.

![Uyarı için koşul ölçütü tanımlama ekran görüntüsü](./media/proactive-cloud-services/002.png)

**Arama sorgusu** kutusunda, önceki adımda hazırladığınız Birleşik sorguyu yapıştırın.

Ardından, **eşik** kutusuna ilerleyin ve değerini 0 olarak ayarlayın. İsteğe bağlı olarak **period** ve Frequency **alanlarını**ince ayar edebilirsiniz.
**Bitti**’ye tıklayın.

![Sinyal mantığını Yapılandır sorgusunun ekran görüntüsü](./media/proactive-cloud-services/003.png)

**Uyarı ayrıntılarını tanımla** bölümünde, uyarı kuralına bir **ad** ve **Açıklama** girin ve **önem derecesini**ayarlayın.
Ayrıca, **oluşturma sırasında kuralı etkinleştir** düğmesinin **Evet**olarak ayarlandığından emin olun.

![Ekran görüntüsü Uyarı ayrıntıları](./media/proactive-cloud-services/004.png)

**Eylem grubunu tanımla** bölümünde, var olan bir **Eylem grubunu** seçebilir veya yeni bir tane oluşturabilirsiniz.
Eylem grubunun çeşitli türlerde birden çok eylem içermesini seçebilirsiniz.

![Ekran görüntüsü eylem grubu](./media/proactive-cloud-services/005.png)

Eylem grubunu tanımladıktan sonra, değişikliklerinizi onaylayın ve **Uyarı kuralı oluştur**' a tıklayın.

## <a name="next-steps"></a>Sonraki Adımlar

Otomatik algılama hakkında daha fazla bilgi edinin:

[Hata bozuklukları](../../azure-monitor/app/proactive-failure-diagnostics.md) , [bellek sızıntılarını](../../azure-monitor/app/proactive-potential-memory-leak.md)

[performans bozuklulıkları](../../azure-monitor/app/proactive-performance-diagnostics.md)

