---
title: Azure Application Insights Aracısı API başvurusu
description: Aracı API başvurusunu Application Insights. Enable-ApplicationInsightsMonitoring. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dccd7e617174bef4a85cb6293cbcc459542310f9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899702"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Application Insights aracı API 'SI: Enable-ApplicationInsightsMonitoring

Bu makalede, [az. ApplicationMonitor PowerShell modülünün](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)üyesi olan bir cmdlet açıklanmaktadır.

## <a name="description"></a>Açıklama

Bir hedef bilgisayardaki IIS uygulamalarının birlikte izlenmesini eklemeyi sunar.

Bu cmdlet, IIS applicationHost. config dosyasını değiştirecek ve bazı kayıt defteri anahtarlarını ayarlayacaktır.
Ayrıca, her bir uygulama tarafından kullanılan izleme anahtarını tanımlayan bir ApplicationInsights. Ikey. config dosyası oluşturur.
IIS, uygulama başlatılırken Application Insights SDK 'Yı uygulamalara ekleyecektir ve RedfieldModule 'u başlangıçta yükler.
Değişikliklerin etkili olması için IIS 'yi yeniden başlatın.

İzlemeyi etkinleştirdikten sonra, uygulamanızın Telemetriyi hızla gönderdiğini denetlemek için [canlı ölçümleri](live-stream.md) kullanmanızı öneririz.


> [!NOTE] 
> - Başlamak için bir izleme anahtarına ihtiyacınız vardır. Daha fazla bilgi için bkz. [kaynak oluşturma](create-new-resource.md#copy-the-instrumentation-key).
> - Bu cmdlet, lisans ve Gizlilik Bildirimimizi incelemenizi ve kabul etmenizi gerektirir.

> [!IMPORTANT] 
> Bu cmdlet, yönetici izinlerine sahip bir PowerShell oturumu ve yükseltilmiş bir yürütme ilkesi gerektirir. Daha fazla bilgi için bkz. [yükseltilmiş yürütme Ilkesiyle PowerShell 'i yönetici olarak çalıştırma](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Örnekler

### <a name="example-with-a-single-instrumentation-key"></a>Tek bir izleme anahtarına sahip örnek
Bu örnekte, geçerli bilgisayardaki tüm uygulamalara tek bir izleme anahtarı atanır.

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
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **Machinefilter** BILGISAYARıN veya VM C# adının gerekli bir Regex.
    - '. * ' tümü eşleşecek
    - ' ComputerName ' yalnızca tam ada sahip bilgisayarlar ile eşleştirecektir.
- **Appfilter** , IIS sitesi C# adının gerekli bir Regex. [Get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)komutunu çalıştırarak sunucunuzdaki sitelerin bir listesini alabilirsiniz.
    - '. * ' tümü eşleşecek
    - ' SiteName ' yalnızca belirtilen tam ada sahip IIS sitesiyle eşleştirecektir.
- Önceki iki filtrelerle eşleşen uygulamaların izlenmesini etkinleştirmek için **ınstrumentationkey** gereklidir.
    - İzlemeyi hariç tutmak için kurallar tanımlamak istiyorsanız bu değeri boş bırakın.


### <a name="-enableinstrumentationengine"></a>-Enableınstrumentationengine
**Seçim.** İzleme altyapısının yönetilen bir işlemin yürütülmesi sırasında neler olduğunu hakkında olayları ve iletileri toplamasını sağlamak için bu anahtarı kullanın. Bu olaylar ve mesajlar bağımlılık sonuç kodlarını, HTTP fiillerini ve SQL komut metnini içerir.

İzleme altyapısı ek yük ekler ve varsayılan olarak kapalıdır.

### <a name="-acceptlicense"></a>-AcceptLicense
**Seçim.** Gözetimsiz yüklemelerde lisans ve gizlilik bildirimini kabul etmek için bu anahtarı kullanın.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Bir Web sunucuları kümeniz olduğunda, [paylaşılan bir yapılandırma](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)kullanıyor olabilirsiniz.
HttpModule bu paylaşılan yapılandırmaya eklenemez.
Bu betik, ek yükleme adımlarının gerekli olduğu iletiyle başarısız olur.
Bu denetimi yoksaymak ve önkoşulları yüklemeye devam etmek için bu anahtarı kullanın. Daha fazla bilgi için bkz. [bilinen çakışma-IIS ile paylaşılan-yapılandırma](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri göstermek için bu anahtarı kullanın.

### <a name="-whatif"></a>-WhatIf 
**Ortak parametre.** İzlemeyi gerçekten etkinleştirmeden giriş parametrelerinizi test etmek ve doğrulamak için bu anahtarı kullanın.

## <a name="output"></a>Çıktı


#### <a name="example-output-from-a-successful-enablement"></a>Başarılı bir etkinleştirme için örnek çıkış

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
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
- İzleme ve günlük çağrısı ekleyebilmeniz [için Application Insights SDK 'sını kodunuza ekleyin](../../azure-monitor/app/asp-net.md) .
 
 Application Insights aracısıyla daha fazlasını yapın:
 - Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.
 - Ayarlarınızın doğru kaydedildiğinden emin olmak için [yapılandırmayı alın](status-monitor-v2-api-get-config.md) .
 - İzlemeyi İnceleme [durumunu alın](status-monitor-v2-api-get-status.md) .
