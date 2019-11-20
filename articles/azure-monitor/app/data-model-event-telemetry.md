---
title: Azure Application Insights Telemetri veri modeli-olay telemetrisi | Microsoft Docs
description: Olay telemetrisi için veri modeli Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 620983fb2174fe7f32a61503b0b87e8e7ce0c330
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678141"
---
# <a name="event-telemetry-application-insights-data-model"></a>Olay telemetrisi: Application Insights veri modeli

Uygulamanızda gerçekleşen bir olayı göstermek için olay telemetrisi öğeleri ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)) oluşturabilirsiniz. Genellikle, düğme tıklama veya siparişi teslim alma gibi bir Kullanıcı etkileşimidir. Ayrıca, başlatma veya yapılandırma güncelleştirmesi gibi bir uygulama yaşam döngüsü olayı da olabilir. 

Anlamsal olarak olaylar, isteklerle bağıntılı olabilir veya olmayabilir. Ancak, düzgün şekilde kullanılırsa, olay telemetrisi isteklerden veya İzlemelerden daha önemlidir. Olaylar iş telemetrisini temsil eder ve ayrı, daha az agresif [örneklemeye](../../azure-monitor/app/api-filtering-sampling.md)tabi olmalıdır.

## <a name="name"></a>Adı

Olay adı. Doğru gruplandırılmasına ve yararlı ölçümlere izin vermek için uygulamanızı, az sayıda ayrı olay adı oluşturacak şekilde kısıtlayın. Örneğin, bir olayın oluşturulan her örneği için ayrı bir ad kullanmayın.

Maksimum Uzunluk: 512 karakter

## <a name="custom-properties"></a>Özel Özellikler

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Özel ölçümler

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Application Insights türleri ve veri modeli için [veri modeli](data-model.md) 'ne bakın.
- [Özel olay telemetrisini yazma](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Application Insights tarafından desteklenen [platformları](../../azure-monitor/app/platforms.md) inceleyin.
