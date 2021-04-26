---
title: Windows Azure Tanılama ile performans izleme
description: Azure Service Fabric kümeleriniz için performans sayaçlarını toplamak üzere Windows Azure Tanılama kullanın.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 6803494d29bf97336e30128f9f5ad20ec73ce202
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627431"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Windows Azure Tanılama uzantısıyla performans izleme

Bu belge, Windows kümeleri için Windows Azure Tanılama (WAD) uzantısı aracılığıyla performans sayaçlarını toplamayı ayarlamak için gerekli olan adımları içerir. Linux kümeleri için [Log Analytics aracısını](service-fabric-diagnostics-oms-agent.md) , düğümleriniz için performans sayaçlarını toplayacak şekilde ayarlayın. 

 > [!NOTE]
> Bu adımların sizin için çalışması için kümenizde WAD uzantısının dağıtılması gerekir. Ayarlanmamışsa, [Windows Azure Tanılama kullanarak olay toplamaya ve koleksiyona](service-fabric-diagnostics-event-aggregation-wad.md)gidin.  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>WadCfg aracılığıyla performans sayaçlarını toplayın

WAD aracılığıyla performans sayaçlarını toplamak için, kümenizin Kaynak Yöneticisi şablonunda yapılandırmayı uygun şekilde değiştirmeniz gerekir. Şablonunuza toplamak istediğiniz bir performans sayacı eklemek ve bir Kaynak Yöneticisi kaynak yükseltmesi çalıştırmak için bu adımları izleyin.

1. Kümenizin şablonundaki WAD yapılandırmasını bulun-bul `WadCfg` . Kapsamında toplanacak performans sayaçlarını eklersiniz `DiagnosticMonitorConfiguration` .

2. Yapılandırmanızı Aşağıdaki bölümü ekleyerek performans sayaçlarını toplayacak şekilde ayarlayın `DiagnosticMonitorConfiguration` . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    , `scheduledTransferPeriod` Toplanan sayaçların değerlerinin ne sıklıkta Azure Storage tablonuza ve yapılandırılmış herhangi bir havuza aktarılacağını tanımlar. 

3. Toplamak istediğiniz performans sayaçlarını, `PerformanceCounterConfiguration` önceki adımda bildirildiği öğesine ekleyin. Toplamak istediğiniz her sayaç,,, ve ile ilgili bir,,, ve ile tanımlanır `counterSpecifier` `sampleRate` `unit` `annotation` `sinks` .

Aşağıda, Service Fabric özel performans sayaçlarından biri olan *Toplam Işlemci zamanı* (CPU 'nun işlem işlemleri için kullanılmakta olduğu süre) ve *saniyedeki aktör yöntemi etkinleştirmeleri Service Fabric* olan bir yapılandırmaya bir örnek verilmiştir. Service Fabric özel performans sayaçlarının tam listesi için [güvenilir aktör performans sayaçlarına](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) ve [güvenilir hizmet performans sayaçlarına](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) bakın.

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

 Sayaç için örnek hız, gereksinimlerinize göre değiştirilebilir. Bunun biçimi olduğu için, `PT<time><unit>` sayacın her saniye toplanmasını istiyorsanız, öğesini ayarlamanız gerekir `"sampleRate": "PT15S"` .

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

1. Toplanması gereken uygun performans sayaçlarını ekledikten sonra, bu değişikliklerin çalışan kümenize yansıtılması için küme kaynağınızı yükseltmeniz gerekir. Değiştirdiğiniz `template.json` ve açık PowerShell 'i kaydedin. Kullanarak kümenizi yükseltebilirsiniz `New-AzResourceGroupDeployment` . Çağrı, kaynak grubunun adı, güncelleştirilmiş şablon dosyası ve parametreler dosyası gerektirir ve güncelleştirdiğiniz kaynaklarda uygun değişiklikler yapmak için Kaynak Yöneticisi sorar. Hesabınızda oturum açtıktan ve doğru abonelikte yer aldıktan sonra, yükseltmeyi çalıştırmak için aşağıdaki komutu kullanın:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Yükseltme tamamlandıktan sonra (ilk dağıtım ve kaynak grubunuzun boyutu ile ilgili olarak 15-45 dakika arasında sürer), WAD performans sayaçlarını toplayıp, kümelerle ilişkili depolama hesabındaki WADPerformanceCountersTable adlı tabloya göndermesi gerekir. [Kaynak Yöneticisi ŞABLONUNA AI havuzunu ekleyerek](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)Application Insights içindeki performans Sayaçlarınızı inceleyin.

## <a name="next-steps"></a>Sonraki adımlar
* Kümeniz için daha fazla performans sayacı toplayın. Toplamanız gereken sayaçların bir listesi için bkz. [performans ölçümleri](service-fabric-diagnostics-event-generation-perf.md) .
* Tanılama verilerini göndermek üzere ek depolama hesapları yapılandırma dahil olmak üzere, üzerinde daha fazla değişiklik yapmak için [WINDOWS VM ve Azure Resource Manager şablonlarıyla izleme ve tanılama kullanın](../virtual-machines/extensions/diagnostics-template.md) `WadCfg` .
* Sıfırdan bir şablon oluşturmak ve sözdiziminin doğru olduğundan emin olmak için [Wadcfg oluşturucusunu](https://azure.github.io/azure-diagnostics-tools/config-builder/) ziyaret edin. ( https://azure.github.io/azure-diagnostics-tools/config-builder/) sıfırdan bir şablon oluşturmak ve sözdiziminin doğru olduğundan emin olmak için.
