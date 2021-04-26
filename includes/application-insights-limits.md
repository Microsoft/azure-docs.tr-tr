---
title: include dosyası
description: include dosyası
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726192"
---
Uygulama başına ölçüm sayısı ve olay başına, diğer bir deyişle, izleme anahtarı başına bazı sınırlar vardır. Limitler seçtiğiniz [fiyatlandırma planına](https://azure.microsoft.com/pricing/details/application-insights/) bağlıdır.

| Kaynak | Varsayılan limit | Not
| --- | --- | --- |
| Günlük toplam veri | 100 GB | Bir uç ayarlayarak verileri azaltabilirsiniz. Daha fazla veri gerekiyorsa, portalda 1.000 GB 'a kadar olan limiti artırabilirsiniz. 1.000 GB 'tan büyük kapasiteler için adresine e-posta gönderin AIDataCap@microsoft.com .
| Azaltma | 32.000 olay/saniye | Sınır bir dakika içinde ölçülür.
| Veri saklama günlükleri | [30-730 gün](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Bu kaynak [Günlükler](../articles/azure-monitor/logs/log-query-overview.md)içindir.
| Veri saklama ölçümleri | 90 gün| Bu kaynak [Ölçüm Gezgini](../articles/azure-monitor/essentials/metrics-charts.md)içindir.
| [Çok adımlı kullanılabilirlik testi](../articles/azure-monitor/app/availability-multistep.md) ayrıntılı sonuçlarını saklama | 90 gün | Bu kaynak her adımın ayrıntılı sonuçlarını verir.
| Maksimum telemetri öğesi boyutu | 64 kB |
| Toplu iş başına maksimum telemetri öğesi | 64 K |
| Özellik ve ölçüm adı uzunluğu | 150 | Bkz. [tür şemaları](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Özellik değeri dize uzunluğu | 8,192  | Bkz. [tür şemaları](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| İzleme ve özel durum iletisi uzunluğu | 32.768  | Bkz. [tür şemaları](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Uygulama başına [kullanılabilirlik testi](../articles/azure-monitor/app/monitor-web-app-availability.md) sayısı | 100 |
| [Profil Oluşturucu](../articles/azure-monitor/app/profiler.md) veri saklama | 5 gün |
| Her gün gönderilen [Profil Oluşturucu](../articles/azure-monitor/app/profiler.md) verileri | 10 GB |

Daha fazla bilgi için bkz. [Application Insights fiyatlandırma ve kotaları hakkında](../articles/azure-monitor/app/pricing.md).