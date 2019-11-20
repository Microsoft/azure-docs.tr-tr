---
title: include dosyası
description: include dosyası
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "69982629"
---
Uygulama başına ölçüm sayısı ve olay başına, diğer bir deyişle, izleme anahtarı başına bazı sınırlar vardır. Limitler seçtiğiniz [fiyatlandırma planına](https://azure.microsoft.com/pricing/details/application-insights/) bağlıdır.

| Resource | Varsayılan limit | Not
| --- | --- | --- |
| Günlük toplam veri | 100 GB | Bir uç ayarlayarak verileri azaltabilirsiniz. Daha fazla veri gerekiyorsa, portalda 1.000 GB 'a kadar olan limiti artırabilirsiniz. 1\.000 GB 'tan büyük kapasiteler için adresine AIDataCap@microsoft.come-posta gönderin.
| Azaltma | 32.000 olay/saniye | Sınır bir dakika içinde ölçülür.
| Veri saklama | [30-730 gün](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | Bu kaynak [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) ve [Ölçüm Gezgini](../articles/azure-monitor/app/metrics-explorer.md) içindir.
| [Çok adımlı kullanılabilirlik testi](../articles/azure-monitor/app/availability-multistep.md) ayrıntılı sonuçlarını saklama | 90 gün | Bu kaynak her adımın ayrıntılı sonuçlarını verir.
| Maksimum telemetri öğesi boyutu | 64 kB |
| Toplu iş başına maksimum telemetri öğesi | 64 K |
| Özellik ve ölçüm adı uzunluğu | 150 | Bkz. [tür şemaları](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Özellik değeri dize uzunluğu | 8,192  | Bkz. [tür şemaları](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| İzleme ve özel durum iletisi uzunluğu | 32.768  | Bkz. [tür şemaları](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Uygulama başına [kullanılabilirlik testi](../articles/azure-monitor/app/monitor-web-app-availability.md) sayısı | 100 |
| [Profil Oluşturucu](../articles/azure-monitor/app/profiler.md) veri saklama | 5 gün |
| Her gün gönderilen [Profil Oluşturucu](../articles/azure-monitor/app/profiler.md) verileri | 10 GB |

Daha fazla bilgi için bkz. [Application Insights fiyatlandırma ve kotaları hakkında](../articles/azure-monitor/app/pricing.md).