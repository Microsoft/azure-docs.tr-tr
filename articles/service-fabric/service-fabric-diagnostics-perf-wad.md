---
title: Azure Service Fabric-Windows Azure Tanılama uzantısıyla performans izleme | Microsoft Docs
description: Azure Service Fabric kümeleriniz için performans sayaçlarını toplamak üzere Windows Azure Tanılama kullanın.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: e29c32c27e7f6c62eb2c6a9cbe2e4d3f1294f038
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155159"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Windows Azure Tanılama uzantısıyla performans izleme

Bu belge, Windows kümeleri için Windows Azure Tanılama (WAD) uzantısı aracılığıyla performans sayaçlarını toplamayı ayarlamak için gerekli olan adımları içerir. Linux kümeleri için [Log Analytics aracısını](service-fabric-diagnostics-oms-agent.md) , düğümleriniz için performans sayaçlarını toplayacak şekilde ayarlayın. 

 > [!NOTE]
> Bu adımların sizin için çalışması için kümenizde WAD uzantısının dağıtılması gerekir. Ayarlanmamışsa, [Windows Azure Tanılama kullanarak olay toplamaya ve koleksiyona](service-fabric-diagnostics-event-aggregation-wad.md)gidin.  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>WadCfg aracılığıyla performans sayaçlarını toplayın

WAD aracılığıyla performans sayaçlarını toplamak için, kümenizin Kaynak Yöneticisi şablonunda yapılandırmayı uygun şekilde değiştirmeniz gerekir. Şablonunuza toplamak istediğiniz bir performans sayacı eklemek ve bir Kaynak Yöneticisi kaynak yükseltmesi çalıştırmak için bu adımları izleyin.

1. Kümenizin şablonundaki WAD yapılandırmasını bulun-bul `WadCfg`. Kapsamında toplanacak performans sayaçlarını eklersiniz `DiagnosticMonitorConfiguration`.

2. Yapılandırmanızı Aşağıdaki bölümü `DiagnosticMonitorConfiguration`ekleyerek performans sayaçlarını toplayacak şekilde ayarlayın. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    , `scheduledTransferPeriod` Toplanan sayaçların değerlerinin ne sıklıkta Azure Storage tablonuza ve yapılandırılmış herhangi bir havuza aktarılacağını tanımlar. 

3. Toplamak istediğiniz performans sayaçlarını, `PerformanceCounterConfiguration` önceki adımda bildirildiği öğesine ekleyin. Toplamak istediğiniz her `counterSpecifier`sayaç `annotation` `sampleRate` `sinks`,,, ve ile ilgili bir, ,,veiletanımlanır.`unit`

*Toplam Işlemci süresi* (CPU 'nun işlemleri için kullanılmakta olan süre) ve *saniyedeki aktör yöntemi etkinleştirmeleri Service Fabric*bir yapılandırma örneği, Service Fabric özel performans sayaçları. Service Fabric özel performans sayaçlarının tam listesi için [güvenilir aktör performans sayaçlarına](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) ve [güvenilir hizmet performans sayaçlarına](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) bakın.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Sayaç için örnek hız, gereksinimlerinize göre değiştirilebilir. Bunun biçimi olduğu `PT<time><unit>`için, sayacın her saniye toplanmasını istiyorsanız, öğesini `"sampleRate": "PT15S"`ayarlamanız gerekir.

 Ayrıca, işlem başına performans sayaçlarını topladığınızda yararlı olabilecek bir dizi performans sayacı toplamak için ARM şabloninizdeki değişkenleri de kullanabilirsiniz. Aşağıdaki örnekte, işlem başına işlemci zamanı ve çöp toplayıcı süresi ve ardından düğümlerde 2 performans sayacı, değişkenler kullanılarak toplanmaktadır. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Toplanması gereken uygun performans sayaçlarını ekledikten sonra, bu değişikliklerin çalışan kümenize yansıtılması için küme kaynağınızı yükseltmeniz gerekir. Değiştirdiğiniz `template.json` ve açık PowerShell 'i kaydedin. Kullanarak `New-AzResourceGroupDeployment`kümenizi yükseltebilirsiniz. Çağrı, kaynak grubunun adı, güncelleştirilmiş şablon dosyası ve parametreler dosyası gerektirir ve güncelleştirdiğiniz kaynaklarda uygun değişiklikler yapmak için Kaynak Yöneticisi sorar. Hesabınızda oturum açtıktan ve doğru abonelikte yer aldıktan sonra, yükseltmeyi çalıştırmak için aşağıdaki komutu kullanın:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Yükseltme tamamlandıktan sonra (ilk dağıtım ve kaynak grubunuzun boyutu ile ilgili olarak 15-45 dakika arasında sürer), WAD 'nin performans sayaçlarını toplamaları ve bunları adlı tabloya göndermesi gerekir. Kümenizle ilişkili depolama hesabında WADPerformanceCountersTable. [Kaynak Yöneticisi ŞABLONUNA AI havuzunu ekleyerek](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)Application Insights içindeki performans Sayaçlarınızı inceleyin.

## <a name="next-steps"></a>Sonraki adımlar
* Kümeniz için daha fazla performans sayacı toplayın. Toplamanız gereken sayaçların bir listesi için bkz. [performans ölçümleri](service-fabric-diagnostics-event-generation-perf.md) .
* Tanılama verilerini göndermek üzere ek depolama hesapları yapılandırma dahil olmak üzere `WadCfg`, üzerinde daha fazla değişiklik yapmak için [Windows VM ve Azure Resource Manager şablonlarıyla izleme ve tanılama kullanın](../virtual-machines/windows/extensions-diagnostics-template.md) .
* Sıfırdan bir şablon oluşturmak ve sözdiziminin doğru olduğundan emin olmak için [Wadcfg oluşturucusunu](https://azure.github.io/azure-diagnostics-tools/config-builder/) ziyaret edin. (https://azure.github.io/azure-diagnostics-tools/config-builder/) sıfırdan bir şablon oluşturmak ve sözdiziminin doğru olduğundan emin olmak için.
