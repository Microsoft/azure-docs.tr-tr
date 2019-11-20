---
title: Azure Application Insights Aracısı API başvurusu
description: Aracı API başvurusunu Application Insights. Set-Applicationınsiısmonitoringconfig. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: d90739fbdc862d67dc2ce0f1dfdf5af5f4089a44
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899667"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights aracı API 'SI: set-Applicationınsiısmonitoringconfig

Bu belgede, [az. ApplicationMonitor PowerShell modülünün](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)üyesi olan bir cmdlet açıklanmaktadır.

## <a name="description"></a>Açıklama

Tam yeniden yükleme yapmadan yapılandırma dosyasını ayarlar.
Değişikliklerin etkili olması için IIS 'yi yeniden başlatın.

> [!IMPORTANT] 
> Bu cmdlet yönetici izinlerine sahip bir PowerShell oturumu gerektirir.


## <a name="examples"></a>Örnekler

### <a name="example-with-a-single-instrumentation-key"></a>Tek bir izleme anahtarına sahip örnek
Bu örnekte, geçerli bilgisayardaki tüm uygulamalara tek bir izleme anahtarı atanacaktır.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>İzleme anahtarı eşleme ile örnek
Bu örnekte:
- `MachineFilter` `'.*'` joker karakterini kullanarak geçerli bilgisayarla eşleşir.
- `AppFilter='WebAppExclude'` `null` izleme anahtarı sağlar. Belirtilen uygulama gösterilmez.
- `AppFilter='WebAppOne'`, belirtilen uygulamayı benzersiz bir izleme anahtarı olarak atar.
- `AppFilter='WebAppTwo'`, belirtilen uygulamayı benzersiz bir izleme anahtarı olarak atar.
- Son olarak, `AppFilter`, önceki kurallarla eşleşmeyen tüm Web uygulamalarını eşleştirmek ve varsayılan bir izleme anahtarı atamak için `'.*'` joker karakterini de kullanır.
- Okunabilirlik için boşluklar eklenir.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametreler

### <a name="-instrumentationkey"></a>-Instrumentationkey
**Gerekli.** Hedef bilgisayardaki tüm uygulamalar tarafından kullanılmak üzere tek bir izleme anahtarı sağlamak için bu parametreyi kullanın.

### <a name="-instrumentationkeymap"></a>-Instrumentationkeymap
**Gerekli.** Birden çok izleme anahtarı ve her bir uygulama tarafından kullanılan izleme anahtarlarının bir eşlemesini sağlamak için bu parametreyi kullanın.
`MachineFilter`ayarlayarak, birkaç bilgisayar için tek bir yükleme betiği oluşturabilirsiniz.

> [!IMPORTANT]
> Uygulamalar kuralların sağlandığı sırada kurallarla eşleştirecektir. Bu nedenle, önce en özel kuralları ve en son genel kuralları belirtmeniz gerekir.

#### <a name="schema"></a>Şema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **Machinefilter** BILGISAYARıN veya VM C# adının gerekli bir Regex.
    - '. * ' tümü eşleşecek
    - ' ComputerName ' yalnızca belirtilen ada sahip bilgisayarlarla eşleştirecektir.
- **Appfilter** BILGISAYARıN veya VM C# adının gerekli bir Regex.
    - '. * ' tümü eşleşecek
    - ' ApplicationName ' yalnızca belirtilen ada sahip IIS uygulamalarıyla eşleştirecektir.
- Önceki iki filtrelerle eşleşen uygulamaların izlenmesini etkinleştirmek için **ınstrumentationkey** gereklidir.
    - İzlemeyi hariç tutmak için kurallar tanımlamak istiyorsanız bu değeri boş bırakın.


### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri göstermek için bu anahtarı kullanın.


## <a name="output"></a>Çıktı

Varsayılan olarak, çıkış yok.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-Instrumentationkey aracılığıyla yapılandırma dosyasını ayarlamadan ayrıntılı çıkış örneği

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-Instrumentationkeymap aracılığıyla yapılandırma dosyasını ayarlamadan ayrıntılı çıkış örneği

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Sonraki adımlar

  Telemetrinizi görüntüleyin:
 - Performansı ve kullanımı izlemek için [ölçümleri](../../azure-monitor/app/metrics-explorer.md) bulun.
- Sorunları tanılamak için [olayları ve günlükleri arayın](../../azure-monitor/app/diagnostic-search.md) .
- Daha gelişmiş sorgular için [analiz kullanın](../../azure-monitor/app/analytics.md) .
- [Panolar oluşturun](../../azure-monitor/app/overview-dashboard.md).
 
 Daha fazla telemetri ekleyin:
 - Sitenizin canlı kaldığından emin olmak için [Web testleri oluşturun](monitor-web-app-availability.md) .
- Web sayfası kodundan özel durumları görmek ve izleme çağrılarını etkinleştirmek için [Web istemcisi telemetrisini ekleyin](../../azure-monitor/app/javascript.md) .
- İzleme ve günlük çağrısı ekleyebilmeniz için [Application Insights SDK 'sını kodunuza ekleyin](../../azure-monitor/app/asp-net.md)
 
 Application Insights aracısıyla daha fazlasını yapın:
 - Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.
 - Ayarlarınızın doğru kaydedildiğinden emin olmak için [yapılandırmayı alın](status-monitor-v2-api-get-config.md) .
 - İzlemeyi İnceleme [durumunu alın](status-monitor-v2-api-get-status.md) .
