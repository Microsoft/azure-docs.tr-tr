---
title: Azure 'dan daha fazla yararlanın Application Insights | Microsoft Docs
description: Application Insights ile çalışmaya başladıktan sonra, keşfedebileceğiniz özelliklerin bir özeti aşağıda verilmiştir.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/03/2017
ms.openlocfilehash: 8c51745c43ced8ad3031a6a01096261ef72b33fc
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678347"
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights 'ten daha fazla telemetri
[ASP.net kodunuza Application Insights](../../azure-monitor/app/asp-net.md)ekledikten sonra daha fazla telemetri almak için yapabileceğiniz birkaç şey vardır. 

| Eylem | Elde edecekleriniz|
|---|---|
|(IIS sunucuları) Her bir sunucu makinesine [durum İzleyicisi yükler](https://go.microsoft.com/fwlink/?LinkId=506648) .<br/>(Azure Web Apps) Web uygulamasının Azure denetim masasında Application Insights dikey penceresini açın.| [**Performans sayaçları**](../../azure-monitor/app/performance-counters.md)<br/>[**Özel durumlar**](asp-net-exceptions.md) -ayrıntılı yığın izlemeleri<br/>[**Bağlantılıdır**](../../azure-monitor/app/asp-net-dependencies.md)|
|[JavaScript kod parçacığını Web sayfalarınıza ekleme](../../azure-monitor/app/javascript.md)|[Sayfa performansı](../../azure-monitor/app/usage-overview.md), tarayıcı özel DURUMLARı, Ajax performansı. Özel istemci tarafı telemetrisi.|
|[Kullanılabilirlik Web testleri oluşturma](../../azure-monitor/app/monitor-web-app-availability.md)|Siteniz kullanılamaz hale gelirse uyarı alın|
|[Builınfo. config](https://msdn.microsoft.com/library/dn449058.aspx) dosyasının MSBuild tarafından oluşturulduğundan emin olun|[Ölçüm grafiklerinde derleme ek açıklamaları](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Özel olayları ve ölçümleri yazma](../../azure-monitor/app/api-custom-events-metrics.md)|İş olayları ve ölçümleri Sayın, ayrıntılı kullanımı izleyin ve daha fazlasını yapın.|
|[Canlı sitenizin profilini yapın](https://aka.ms/AIProfilerPreview)|Canlı Web uygulamanızdan ayrıntılı işlev zamanlamaları|






