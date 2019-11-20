---
title: Application Insights verileri göndermek için Azure Tanılama yapılandırma
description: Application Insights verileri göndermek için Azure Tanılama genel yapılandırmayı güncelleştirin.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 03/19/2016
ms.openlocfilehash: 5328d2be4b8bf733041c39fe029ae2d02ecc3a6e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552050"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Bulut hizmeti, sanal makine veya Service Fabric tanılama verilerini Application Insights gönderin
Bulut Hizmetleri, sanal makineler, sanal makine ölçek kümeleri ve Service Fabric tümü, veri toplamak için Azure Tanılama uzantısını kullanır.  Azure tanılama, verileri Azure depolama tablolarına gönderir.  Ancak, Azure Tanılama uzantısı 1,5 veya sonraki bir sürümü kullanarak, verilerin tamamını veya bir alt kümesini diğer konumlara de kanal oluşturarak aktarabilirsiniz.

Bu makalede, Azure Tanılama uzantısından verilerin Application Insights nasıl gönderileceği açıklanır.

## <a name="diagnostics-configuration-explained"></a>Tanılama yapılandırması açıklanıyor
Azure tanılama uzantısı 1,5, tanılama verilerini gönderebileceğiniz ek konumlar olan havuzlar tarafından kullanıma sunulmuştur.

Application Insights için bir havuzun örnek yapılandırması:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- **Havuz** *adı* özniteliği, havuzu benzersiz bir şekilde tanımlayan bir dize değeridir.

- **ApplicationInsights** öğesi, Azure tanılama verilerinin gönderildiği Application Insights kaynağının izleme anahtarını belirtir.
    - Mevcut bir Application Insights kaynağınız yoksa, kaynak oluşturma ve izleme anahtarını alma hakkında daha fazla bilgi için bkz. [Yeni bir Application Insights kaynağı oluşturma](../../azure-monitor/app/create-new-resource.md ) .
    - Azure SDK 2,8 ve üzeri bir bulut hizmeti geliştiriyorsanız, bu izleme anahtarı otomatik olarak doldurulur. Değer, bulut hizmeti projesi paketlerken **APPINSIGHTS_INSTRUMENTATIONKEY** hizmeti yapılandırma ayarını temel alır. Bkz. [Cloud Services ile Application Insights kullanma](../../azure-monitor/app/cloudservices.md).

- **Channels** öğesi bir veya daha fazla **Kanal** öğesi içeriyor.
    - *Name* özniteliği bu kanala benzersiz bir şekilde başvurur.
    - *LogLevel* özniteliği, kanalın izin verdiği günlük düzeyini belirtmenizi sağlar. En çok en az bilgi için kullanılabilir günlük düzeyleri şunlardır:
        - Seçeneini
        - Bilgi
        - Uyarı
        - Hata
        - Kritik

Bir kanal filtre gibi davranır ve hedef havuza göndermek için belirli günlük düzeylerini seçmenize olanak sağlar. Örneğin, ayrıntılı Günlükler toplayabilir ve bunları depolamaya gönderebilir, ancak yalnızca havuza hata gönderebilirsiniz.

Aşağıdaki grafikte bu ilişki gösterilmektedir.

![Tanılama genel yapılandırması](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Aşağıdaki grafik yapılandırma değerlerini ve bunların nasıl çalıştığını özetler. Yapılandırmaya, hiyerarşideki farklı düzeylerde birden çok havuz dahil edebilirsiniz. En üst düzeydeki havuz genel ayar olarak davranır ve bireysel öğesinde belirtilen tek öğe, bu genel ayarda geçersiz kılma gibi davranır.

![Application Insights ile tanılama havuzları yapılandırması](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Havuz yapılandırma örneğini doldurun
Ortak yapılandırma dosyasının bir örneği aşağıda verilmiştir
1. tüm hataları Application Insights gönderir ( **Diagnosticmonitorconfiguration** düğümünde belirtilen)
2. Ayrıca, uygulama günlükleri için ayrıntılı düzey Günlükler gönderir ( **Günlükler** düğümünde belirtilir).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
Önceki yapılandırmada aşağıdaki satırlar aşağıdaki anlamlara sahiptir:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Azure tanılama tarafından toplanan tüm verileri gönder

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Application Insights havuzuna yalnızca hata günlükleri Gönder

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Ayrıntılı uygulama günlüklerini Application Insights gönder

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Sınırlamalar

- **Yalnızca kanallar performans sayaçlarını değil, günlük türünü günlüğe kaydeder.** Performans sayacı öğesiyle bir kanal belirtirseniz, bu yok sayılır.
- **Kanal için günlük düzeyi, Azure tanılama tarafından toplanmakta olan günlük düzeyini aşamaz.** Örneğin, Günlükler öğesinde uygulama günlüğü hatalarını toplayamazsınız ve ayrıntılı günlükleri Application Insight havuzuna göndermeye çalışın. *Scheduledtransferloglevelfilter* özniteliği her zaman bir havuza gönderilmeye çalıştığınız günlüklere göre eşit veya daha fazla günlük toplamalıdır.
- **Azure tanılama uzantısı tarafından toplanan blob verilerini Application Insights için gönderemezsiniz.** Örneğin, *dizinler* düğümü altında belirtilen her şey. Kilitlenme dökümlerinde, gerçek kilitlenme dökümü blob depolamaya gönderilir ve yalnızca kilitlenme dökümünün oluşturulduğu bir bildirim Application Insights gönderilir.

## <a name="next-steps"></a>Sonraki Adımlar
* Application Insights [Azure tanılama bilgilerinizi görüntülemeyi](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) öğrenin.
* Uygulamanızın Azure tanılama uzantısını etkinleştirmek için [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) 'i kullanın.
* Uygulamanız için Azure tanılama uzantısını etkinleştirmek üzere [Visual Studio 'yu](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) kullanma

