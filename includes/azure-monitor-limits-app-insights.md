---
title: include dosyası
description: include dosyası
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0185710b6255dabd61d6361ed98c658f1e8075c7
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975354"
---
Uygulama başına ölçüm sayısı ve olay başına, diğer bir deyişle, izleme anahtarı başına bazı sınırlar vardır. Limitler seçtiğiniz [fiyatlandırma planına](https://azure.microsoft.com/pricing/details/application-insights/) bağlıdır.

| Kaynak | Varsayılan limit | Not
| --- | --- | --- |
| Günlük toplam veri | 100 GB | Bir uç ayarlayarak verileri azaltabilirsiniz. Daha fazla veri gerekiyorsa, portalda 1.000 GB 'a kadar olan limiti artırabilirsiniz. 1\.000 GB 'tan büyük kapasiteler için AIDataCap@microsoft.com ' a e-posta gönderin.
| Azaltma | 32.000 olay/saniye | Sınır bir dakika içinde ölçülür.
| Veri saklama | 90 gün | Bu kaynak [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) ve [Ölçüm Gezgini](../articles/azure-monitor/app/metrics-explorer.md) içindir.
| [Çok adımlı kullanılabilirlik testi](../articles/azure-monitor/app/availability-multistep.md) ayrıntılı sonuçlarını saklama | 90 gün | Bu kaynak her adımın ayrıntılı sonuçlarını verir.
| En fazla olay boyutu | 64.000 |
| Özellik ve ölçüm adı uzunluğu | 150 | Bkz. [tür şemaları](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Özellik değeri dize uzunluğu | 8,192 | Bkz. [tür şemaları](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| İzleme ve özel durum iletisi uzunluğu | 32.768  | Bkz. [tür şemaları](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Uygulama başına [kullanılabilirlik testi](../articles/azure-monitor/app/monitor-web-app-availability.md) sayısı | 100 |
| [Profil Oluşturucu](../articles/azure-monitor/app/profiler.md) veri saklama | 5 gün |
| Her gün gönderilen [Profil Oluşturucu](../articles/azure-monitor/app/profiler.md) verileri | 10 GB |

Daha fazla bilgi için bkz. [Application Insights fiyatlandırma ve kotaları hakkında](../articles/azure-monitor/app/pricing.md).
