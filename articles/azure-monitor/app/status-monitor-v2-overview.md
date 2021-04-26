---
title: Azure Application Insights aracısına genel bakış | Microsoft Docs
description: Application Insights aracısına genel bakış. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 69f80856150e461c6edfafdf0aa89de77c4ab0fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583810"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Şirket içi sunucular için Azure Izleyici Application Insights aracısını dağıtma

> [!IMPORTANT]
> Bu kılavuz, Application Insights aracısına ait şirket Içi ve Azure olmayan bulut dağıtımları için önerilir. [Azure sanal makinesi ve sanal makine ölçek kümesi dağıtımları](./azure-vm-vmss-apps.md)için önerilen yaklaşım aşağıda verilmiştir.

Application Insights Aracısı (eski adıyla Durum İzleyicisi v2) [PowerShell Galerisi](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)yayımlanmış bir PowerShell modülüdür.
[Durum İzleyicisi](./monitor-performance-live-website-now.md)yerini alır.
Telemetri Azure portal, uygulamanızı [izleyebileceğiniz](./app-insights-overview.md) bir şekilde gönderilir.

> [!NOTE]
> Modül Şu anda IIS ile barındırılan .NET Web Apps 'in kodsuz kullanacaksınız araçlarını desteklemektedir. ASP.NET Core, Java ve Node.js uygulamalarını işaretlemek için bir SDK kullanın.

## <a name="powershell-gallery"></a>PowerShell Galerisi

Application Insights Aracısı şurada bulunur: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![PowerShell Galerisi](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Yönergeler
- Kısa kod örneklerini kullanmaya başlamak için Başlarken [yönergelerine](status-monitor-v2-get-started.md) bakın.
- Nasıl başlatacağınız hakkında ayrıntılı bilgi için bkz. [ayrıntılı yönergeler](status-monitor-v2-detailed-instructions.md) .

## <a name="powershell-api-reference"></a>PowerShell API başvurusu
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-ınstrumentationengine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-ınstrumentationengine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-Applicationınsiısmonitoringconfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-Applicationınsiısmonitoringstatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-Applicationınsiısmonitoringconfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start-Applicationınsiizsmonitoringtrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Sorun giderme
- [Sorun giderme](status-monitor-v2-troubleshoot.md)
- [Bilinen sorunlar](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>SSS

- Application Insights Aracısı proxy yüklemelerini destekliyor mu?

  *Evet*. Application Insights aracısını indirmek için birden çok yol vardır. Bilgisayarınızda internet erişimi varsa, parametreleri kullanarak PowerShell Galerisi ekleyebilirsiniz `-Proxy` .
Ayrıca, modülü el ile indirebilir ve bilgisayarınıza yükleyebilir ya da doğrudan kullanabilirsiniz.
Bu seçeneklerin her biri [ayrıntılı yönergeler](status-monitor-v2-detailed-instructions.md)bölümünde açıklanmıştır.

- Durum İzleyicisi v2 ASP.NET Core uygulamaları destekliyor mu?

  *Hayır*. ASP.NET Core uygulamalarının izlenmesini etkinleştirme yönergeleri için, bkz. [ASP.NET Core uygulamalar için Application Insights](./asp-net-core.md). ASP.NET Core bir uygulama için StatusMonitor yüklemeniz gerekmez. Bu, ASP.NET Core uygulama IIS 'de barındırıldığında bile geçerlidir.

- Nasıl yaparım?, etkinleştirme başarılı oldu mu?

  - [Get-Applicationınsisofsmonitoringstatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) cmdlet 'i, etkinleştirme başarılı olduğunu doğrulamak için kullanılabilir.
  - Uygulamanızın telemetri gönderip göndermediğine hızlı bir şekilde anlamak için [canlı ölçümleri](./live-stream.md) kullanmanızı öneririz.

  - Şu anda telemetri gönderen tüm bulut rollerini listelemek için [Log Analytics](../logs/log-analytics-tutorial.md) de kullanabilirsiniz:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Sonraki adımlar

Telemetrinizi görüntüleyin:

* Performansı ve kullanımı izlemek için [ölçümleri](../essentials/metrics-charts.md) bulun.
* Sorunları tanılamak için [olayları ve günlükleri arayın](./diagnostic-search.md) .
* Daha gelişmiş sorgular için [analiz kullanın](../logs/log-query-overview.md) .
* [Panolar oluşturun](./overview-dashboard.md).

Daha fazla telemetri ekleyin:

* Sitenizin canlı kalması için [web testleri oluşturun](monitor-web-app-availability.md).
* Web sayfası kodundan özel durumları görmek ve izleme çağrılarını etkinleştirmek için [Web istemcisi telemetrisini ekleyin](./javascript.md) .
* İzleme ve günlük çağrısı ekleyebilmeniz [için Application Insights SDK 'sını kodunuza ekleyin](./asp-net.md) .