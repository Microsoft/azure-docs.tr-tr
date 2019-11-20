---
title: Azure Application Insights Telemetri veri modeli-Izleme telemetrisi | Microsoft Docs
description: İzleme telemetri için Application Insights veri modeli
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 855a93d8a6350c625fe0820fae83644aec3459ed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678109"
---
# <a name="trace-telemetry-application-insights-data-model"></a>İzleme telemetrisi: Application Insights veri modeli

İzleme telemetrisi ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)), metin arama olan `printf` stili izleme deyimlerini temsil eder. `Log4Net`, `NLog` ve diğer metin tabanlı günlük dosyası girdileri bu türdeki örneklere çevrilir. İzlemenin bir genişletilebilirlik olarak ölçümleri yoktur.

## <a name="message"></a>İleti

Trace iletisi.

Maksimum Uzunluk: 32768 karakter

## <a name="severity-level"></a>Önem derecesi

Önem derecesi düzeyini izleyin. Değer `Verbose`, `Information`, `Warning`, `Error`, `Critical` olabilir.

## <a name="custom-properties"></a>Özel Özellikler

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Application Insights .NET izleme günlüklerini keşfet](../../azure-monitor/app/asp-net-trace-logs.md).
- [Application Insights Java izleme günlüklerini keşfet](../../azure-monitor/app/java-trace-logs.md).
- Application Insights türleri ve veri modeli için [veri modeli](data-model.md) 'ne bakın.
- [Özel izleme telemetrisini yazma](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Application Insights tarafından desteklenen [platformları](../../azure-monitor/app/platforms.md) inceleyin.
