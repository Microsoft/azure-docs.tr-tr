---
title: Azure Application Insights Telemetri veri modeli-özel durum telemetrisi | Microsoft Docs
description: Özel durum telemetrisi için Application Insights veri modeli
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0ba1c94ee8dc78b937d650cff32e1518a7ca5a12
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677414"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Özel durum telemetrisi: Application Insights veri modeli

[Application Insights](../../azure-monitor/app/app-insights-overview.md)bir özel durum örneği, izlenen uygulamanın yürütülmesi sırasında oluşan işlenmiş veya işlenmemiş bir özel durumu temsil eder.

## <a name="problem-id"></a>Sorun kimliği

Kodda özel durumun oluşturulduğu tanımlayıcı. Özel durumlar gruplandırması için kullanılır. Genellikle, çağrı yığınından özel durum türü ve bir işlev birleşimi.

Maksimum Uzunluk: 1024 karakter

## <a name="severity-level"></a>Önem derecesi

Önem derecesi düzeyini izleyin. Değer `Verbose`, `Information`, `Warning`, `Error`, `Critical` olabilir.

## <a name="exception-details"></a>Özel durum ayrıntıları

(Genişletilmek üzere)

## <a name="custom-properties"></a>Özel Özellikler

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Özel ölçümler

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Application Insights türleri ve veri modeli için [veri modeli](data-model.md) 'ne bakın.
- [Web uygulamalarınızda Application Insights özel durumları tanılamayı](../../azure-monitor/app/asp-net-exceptions.md)öğrenin.
- Application Insights tarafından desteklenen [platformları](../../azure-monitor/app/platforms.md) inceleyin.
