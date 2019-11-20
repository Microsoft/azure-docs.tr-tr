---
title: Azure Application Insights Aracısı API başvurusu
description: Aracı API başvurusunu Application Insights. Enable-ınstrumentationengine. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 796c2cc669e238499223d233cf4ddcf740af7c95
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899716"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Application Insights aracı API 'SI: Enable-ınstrumentationengine

Bu makalede, [az. ApplicationMonitor PowerShell modülünün](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)üyesi olan bir cmdlet açıklanmaktadır.

## <a name="description"></a>Açıklama

Bazı kayıt defteri anahtarlarını ayarlayarak izleme altyapısını sunar.
Değişikliklerin etkili olması için IIS 'yi yeniden başlatın.

İzleme altyapısı, .NET SDK 'Ları tarafından toplanan verileri tamamlayabilir.
Yönetilen bir işlemin yürütülmesini tanımlayan olayları ve iletileri toplar. Bu olaylar ve mesajlar bağımlılık sonuç kodlarını, HTTP fiillerini ve [SQL komut metnini](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)içerir.

Şu durumlarda izleme altyapısını etkinleştirin:
- Enable cmdlet 'i ile izlemeyi zaten etkinleştirdiniz, ancak izleme altyapısını etkinleştirmediniz.
- Uygulamanızı .NET SDK 'Ları ile el ile gördünüz ve ek telemetri toplamak istiyorsunuz.

> [!IMPORTANT] 
> Bu cmdlet yönetici izinlerine sahip bir PowerShell oturumu gerektirir.

> [!NOTE] 
> - Bu cmdlet, lisans ve Gizlilik Bildirimimizi incelemenizi ve kabul etmenizi gerektirir.
> - İzleme altyapısı ek yük ekler ve varsayılan olarak kapalıdır.

## <a name="examples"></a>Örnekler

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametreler

### <a name="-acceptlicense"></a>-AcceptLicense
**Seçim.** Gözetimsiz yüklemelerde lisans ve gizlilik bildirimini kabul etmek için bu anahtarı kullanın.

### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri çıkarmak için bu anahtarı kullanın.

## <a name="output"></a>Çıktı


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>İzleme altyapısını başarıyla etkinleştirmenin örnek çıkışı

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Sonraki adımlar

  Telemetrinizi görüntüleyin:
 - Performansı ve kullanımı izlemek için [ölçümleri](../../azure-monitor/app/metrics-explorer.md) bulun.
- Sorunları tanılamak için [olayları ve günlükleri arayın](../../azure-monitor/app/diagnostic-search.md) .
- Daha gelişmiş sorgular için [analiz](../../azure-monitor/app/analytics.md) kullanın.
- [Panolar oluşturun](../../azure-monitor/app/overview-dashboard.md).
 
 Daha fazla telemetri ekleyin:
 - Sitenizin canlı kaldığından emin olmak için [Web testleri oluşturun](monitor-web-app-availability.md) .
- Web sayfası kodundan özel durumları görmek ve izleme çağrılarını etkinleştirmek için [Web istemcisi telemetrisini ekleyin](../../azure-monitor/app/javascript.md) .
- İzleme ve günlük çağrısı ekleyebilmeniz [için Application Insights SDK 'sını kodunuza ekleyin](../../azure-monitor/app/asp-net.md) .
 
 Application Insights aracısıyla daha fazlasını yapın:
 - Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.
 - Ayarlarınızın doğru kaydedildiğinden emin olmak için [yapılandırmayı alın](status-monitor-v2-api-get-config.md) .
 - İzlemeyi İnceleme [durumunu alın](status-monitor-v2-api-get-status.md) .
