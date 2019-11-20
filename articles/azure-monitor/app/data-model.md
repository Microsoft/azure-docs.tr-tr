---
title: Azure Application Insights Telemetri veri modeli | Microsoft Docs
description: Application Insights veri modeline genel bakış
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.subservice: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: b14ce5cc83dcbbeef2379d21027d4bca337fd9fd
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376033"
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights telemetri veri modeli

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) , uygulamanızın performansını ve kullanımını çözümleyebilmeniz için web uygulamanızdan Azure Portal telemetri gönderir. Telemetri modeli, platform ve dilden bağımsız izleme oluşturmak mümkün olacak şekilde standartlaştırılmış hale gelir. 

Application Insights tarafından toplanan veriler bu tipik uygulama yürütme modeli:

![Application Insights uygulama modeli](./media/data-model/application-insights-data-model.png)

Aşağıdaki telemetri türleri uygulamanızın yürütülmesini izlemek için kullanılır. Aşağıdaki üç tür, genellikle Web uygulaması çerçevesindeki Application Insights SDK tarafından otomatik olarak toplanır:

* Uygulamanız tarafından alınan bir isteği günlüğe kaydetmek için [**istek**](data-model-request-telemetry.md) oluşturuldu. Örneğin, Application Insights Web SDK, Web uygulamanızın aldığı her HTTP isteği için otomatik olarak bir Istek telemetrisi öğesi oluşturur. 

    **İşlem** , bir isteği işleyen yürütmenin iş parçacıklarından oluşur. Ayrıca, verileri düzenli aralıklarla işleyen bir Web işinde "uyandırma" gibi diğer işlem türlerini izlemek için de [kod yazabilirsiniz](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) .  Her işlemin bir KIMLIĞI vardır. Bu KIMLIK, uygulamanız isteği işlerken oluşturulan tüm Telemetriyi [gruplandırmak](../../azure-monitor/app/correlation.md) için kullanılabilir. Her işlem başarılı olur ya da başarısız olur ve bir süre süresi vardır.
* [**Özel durum**](data-model-exception-telemetry.md) -genellikle bir işlemin başarısız olmasına neden olan bir özel durumu temsil eder.
* [**Bağımlılık**](data-model-dependency-telemetry.md) -uygulamanızdan bir REST API veya SQL gibi bir dış hizmete veya depoya olan çağrıyı temsil eder. ASP.NET ' de, SQL 'e bağımlılık çağrıları `System.Data` tarafından tanımlanır. HTTP uç noktalarına yapılan çağrılar `System.Net` tarafından tanımlanır. 

Application Insights özel telemetri için üç ek veri türü sağlar:

* [Trace](data-model-trace-telemetry.md) -`Log4Net` veya `System.Diagnostics` gibi tanıdık bir izleme çerçevesi kullanarak tanılama günlüğü uygulamak için doğrudan veya bir bağdaştırıcı aracılığıyla kullanılır.
* [Olay](data-model-event-telemetry.md) -genellikle kullanım düzenlerini çözümlemek için hizmetinize yönelik kullanıcı etkileşimini yakalamak için kullanılır.
* [Ölçüm](data-model-metric-telemetry.md) -düzenli skaler ölçümleri raporlamak için kullanılır.

Her telemetri öğesi, uygulama sürümü veya Kullanıcı oturum kimliği gibi [bağlam bilgilerini](data-model-context.md) tanımlayabilir. Bağlam, belirli senaryoları engelleyen bir türü kesin belirlenmiş alanlar kümesidir. Uygulama sürümü düzgün şekilde başlatıldığında Application Insights, yeniden dağıtım ile bağıntılı uygulama davranışında yeni desenleri algılayabilir. Oturum kimliği, kullanıcılar üzerinde kesinti veya bir sorun etkisi hesaplamak için kullanılabilir. Bazı başarısız bağımlılık için farklı oturum kimliği sayısını hesaplama, hata izleme veya kritik özel durum, bir etkiyi daha iyi anlamak için sağlar.

Application Insights telemetri modeli, telemetrinin bir parçası olan işlem ile [ilişkilendirilmesi](../../azure-monitor/app/correlation.md) için bir yol tanımlar. Örneğin, bir istek bir SQL veritabanı çağrısı ve kayıtlı tanılama bilgileri yapabilir. İstek telemetrisine geri bağlayan telemetri öğeleri için bağıntı bağlamını ayarlayabilirsiniz.

## <a name="schema-improvements"></a>Şema geliştirmeleri

Application Insights veri modeli, uygulama telemetrinizi modeletmenin basit ve temel, güçlü bir yoludur. Temel senaryoları desteklemek ve Gelişmiş kullanım için şemayı genişletmeye izin vermek için modeli basit ve ince tutmak istiyoruz.

Veri modeli veya şema sorunlarını ve önerilerini raporlamak için GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/issues) Repository kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- [Özel telemetri yazma](../../azure-monitor/app/api-custom-events-metrics.md)
- [Telemetrinin nasıl genişletileceğini ve filtreleneceğini](../../azure-monitor/app/api-filtering-sampling.md)öğrenin.
- Veri modeli tabanlı telemetri miktarını en aza indirmek için [örnekleme](../../azure-monitor/app/sampling.md) kullanın.
- Application Insights tarafından desteklenen [platformları](../../azure-monitor/app/platforms.md) inceleyin.
