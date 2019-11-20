---
title: Azure Application Insights Telemetri veri modeli-ölçüm telemetrisi | Microsoft Docs
description: Ölçüm telemetrisi için Application Insights veri modeli
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 816fa37ea052b18dab80bcc0d5c1528cd3d9a014
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678140"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Ölçüm telemetrisi: Application Insights veri modeli

[Application Insights](../../azure-monitor/app/app-insights-overview.md)tarafından desteklenen iki tür ölçüm telemetrisi vardır: tek ölçüm ve önceden toplanmış ölçüm. Tek ölçüm yalnızca bir ad ve değerdir. Ön toplanmış ölçüm, ölçüm aralığı ve standart sapması içindeki ölçümün en küçük ve en büyük değerini belirtir.

Ön toplanmış ölçüm telemetrisi, toplama döneminin bir dakika olduğunu varsayar.

Application Insights tarafından desteklenen birkaç iyi bilinen ölçüm adı vardır. Bu ölçümler, performanceCounters tablosuna yerleştirildi.

Sistem ve işlem sayaçlarını temsil eden ölçüm:

| **.NET adı**             | **Platformun belirsiz adı** | **REST API adı** | **Açıklama**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | İş devam ediyor... | [Processorcpuyüzdesi](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Toplam makine CPU 'SU
| `\Memory\Available Bytes`                 | İş devam ediyor... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Bilgisayarda çalışan işlemlerin kullanabileceği fiziksel bellek miktarını bayt cinsinden gösterir. Sıfırlanmış, ücretsiz ve bekleme belleği listelerindeki alan miktarı toplanarak hesaplanır. Boş bellek kullanıma uygun; Sıfırlı bellek, sonraki işlemlerin önceki bir işlem tarafından kullanılan verileri görmesini engellemek için sıfırlarla doldurulmuş bellek sayfalarından oluşur; bekleme belleği, bir işlemin çalışma kümesinden (fiziksel belleği) en fazla diske giden, ancak geri çekmeye hazır olan bellektir. [Bellek nesnesine](https://msdn.microsoft.com/library/ms804008.aspx) bakın
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | İş devam ediyor... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Uygulamayı barındıran işlemin CPU 'SU
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | İş devam ediyor... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | uygulamayı barındıran işlem tarafından kullanılan bellek
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | İş devam ediyor... | [Processıobi Tespersecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | uygulamayı barındıran işlem tarafından çalıştırılan g/ç işlemlerinin oranı
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | İş devam ediyor... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | uygulama tarafından işlenen isteklerin oranı 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | İş devam ediyor... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | uygulama tarafından oluşturulan özel durumların oranı
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | İş devam ediyor... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | ortalama istek yürütme süresi
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | İş devam ediyor... | [Requestsınqueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | Kuyruktaki işlemeyi bekleyen istek sayısı

## <a name="name"></a>Adı

Application Insights portalında ve Kullanıcı arabiriminde görmek istediğiniz ölçümün adı. 

## <a name="value"></a>Değer

Ölçüm için tek değer. Toplama için bireysel ölçümlerin toplamı.

## <a name="count"></a>Sayı

Toplu ölçümün ölçüm ağırlığı. Ölçüm için ayarlanmamalıdır.

## <a name="min"></a>Min

Toplanan ölçümün en küçük değeri. Ölçüm için ayarlanmamalıdır.

## <a name="max"></a>Maks.

Toplanan ölçümün maksimum değeri. Ölçüm için ayarlanmamalıdır.

## <a name="standard-deviation"></a>Standart sapma

Toplu ölçümün standart sapması. Ölçüm için ayarlanmamalıdır.

## <a name="custom-properties"></a>Özel Özellikler

Özel özellik `CustomPerfCounter` `true` olarak ayarlanan ölçüm, ölçümün Windows performans sayacını temsil ettiğini gösterir. Bu ölçümler performanceCounters tablosuna yerleştirildi. Customölçümler içinde değil. Ayrıca, bu ölçümün adı kategori, sayaç ve örnek adlarını ayıklamak için ayrıştırılır.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Özel olaylar ve ölçümler için Application Insights API 'sini](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)kullanmayı öğrenin.
- Application Insights türleri ve veri modeli için [veri modeli](data-model.md) 'ne bakın.
- Application Insights tarafından desteklenen [platformları](../../azure-monitor/app/platforms.md) inceleyin.
