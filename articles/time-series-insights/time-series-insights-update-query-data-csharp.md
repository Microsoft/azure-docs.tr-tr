---
title: Azure Time Series Insights | kullanarak C# bir önizleme ortamından veri sorgulama Microsoft Docs
description: "' De C#yazılmış bir uygulamayı kullanarak Azure Time Series Insights ortamından veri sorgulama hakkında bilgi edinin."
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/08/2019
ms.custom: seodec18
ms.openlocfilehash: 806460e5e4336624c6b0ead59dd5632e730c69e5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014725"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Kullanarak Azure Time Series Insights önizleme ortamından veri sorgulamaC#

Bu C# örnek, Azure Time Series Insights önizleme ortamından verilerin nasıl sorgulanacağını gösterir.

Örnek, Sorgu API’si kullanımının birkaç temel örneğini gösterir:

1. Bir hazırlık adımı olarak, Azure Active Directory API 'SI aracılığıyla erişim belirtecini alın. Bu belirteci her sorgu API isteğinin `Authorization` üst bilgisinde geçirin. Etkileşimli olmayan uygulamalar ayarlamak için bkz. [kimlik doğrulama ve yetkilendirme](time-series-insights-authentication-and-authorization.md). Ayrıca, örneğin başlangıcında tanımlanan tüm sabitlerin doğru ayarlandığından emin olun.
1. Kullanıcının erişimi olan ortamların listesi alınır. Ortamların biri, ilgilendiğiniz ortam olarak alınır ve bu ortam için daha fazla veri sorgulanır.
1. HTTPS isteğinin bir örneği olarak, ilgilenilen ortam için kullanılabilirlik verileri istenir.
1. Web yuvası isteğinin bir örneği olarak, ilgilenilen ortam için toplam olay verileri istenir. Veriler kullanılabilir oldukları tüm zaman aralığı için istenir.

> [!NOTE]
> Bu örnek kod [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)de mevcuttur.

## <a name="c-example"></a>C#örneğinde

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Yukarıdaki kod örneği, varsayılan ortam değerlerini değiştirmeksizin çalıştırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Sorgulama hakkında daha fazla bilgi edinmek için [sorgu API 'si başvurusunu](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)okuyun.

- Time Series Insights için [istemci SDK 'sını kullanarak bir JavaScript uygulamasının nasıl bağlanacağını](https://github.com/microsoft/tsiclient) okuyun.