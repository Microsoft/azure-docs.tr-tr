---
title: Azure Application Insights Telemetri veri modeli-bağımlılık telemetrisi | Microsoft Docs
description: Bağımlılık telemetri için Application Insights veri modeli
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: e0923c20b11aa02f380af1faa6766d2346ad1fb2
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677443"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Bağımlılık telemetrisi: Application Insights veri modeli

Bağımlılık telemetrisi ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)), IZLENEN bileşenin SQL veya HTTP uç noktası gibi uzak bir bileşenle bir etkileşimini temsil eder.

## <a name="name"></a>Adı

Bu bağımlılık çağrısıyla başlatılan komutun adı. Düşük kardinalite değeri. Örnekler, saklı yordam adı ve URL yolu şablonudur.

## <a name="id"></a>Kimlik

Bağımlılık çağrı örneğinin tanımlayıcısı. Bu bağımlılık çağrısına karşılık gelen istek telemetrisi öğesiyle bağıntı için kullanılır. Daha fazla bilgi için bkz. [bağıntı](../../azure-monitor/app/correlation.md) sayfası.

## <a name="data"></a>Veriler

Bu bağımlılık çağrısıyla başlatılan komut. Örnekler, tüm sorgu parametrelerine sahip SQL deyimleridir ve HTTP URL 'sidir.

## <a name="type"></a>Tür

Bağımlılık türü adı. Bağımlılıkların mantıksal gruplandırması ve commandName ve resultCode gibi diğer alanların yorumlanması için düşük kardinalite değeri. SQL, Azure tablosu ve HTTP örnekleri aşağıda verilmiştir.

## <a name="target"></a>Hedefleyin

Bağımlılık çağrısının hedef sitesi. Örnek olarak sunucu adı, ana bilgisayar adresidir. Daha fazla bilgi için bkz. [bağıntı](../../azure-monitor/app/correlation.md) sayfası.

## <a name="duration"></a>Süre

Şu biçimdeki istek süresi: `DD.HH:MM:SS.MMMMMM`. @No__t_0 günden az olmalıdır.

## <a name="result-code"></a>Sonuç kodu

Bağımlılık çağrısının sonuç kodu. SQL hata kodu ve HTTP durum kodu örnekleri aşağıda verilmiştir.

## <a name="success"></a>Başarılı

Başarılı veya başarısız çağrı göstergesi.

## <a name="custom-properties"></a>Özel Özellikler

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Özel ölçümler

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Sonraki adımlar

- [.Net](../../azure-monitor/app/asp-net-dependencies.md)için bağımlılık izlemeyi ayarlayın.
- [Java](../../azure-monitor/app/java-agent.md)için bağımlılık izlemeyi ayarlayın.
- [Özel bağımlılık telemetrisi yazma](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Application Insights türleri ve veri modeli için [veri modeli](data-model.md) 'ne bakın.
- Application Insights tarafından desteklenen [platformları](../../azure-monitor/app/platforms.md) inceleyin.
