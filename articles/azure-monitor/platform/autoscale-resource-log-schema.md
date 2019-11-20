---
title: Azure otomatik ölçeklendirme günlüğü olayları şeması
description: Otomatik ölçeklendirme eylemlerini izlemek ve sorunlarını gidermek için günlüklerin biçimi
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: robb
ms.subservice: autoscale
ms.openlocfilehash: 8b99cec69b05fc0f78dcf813e67e6b65ba50d819
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152937"
---
# <a name="azure-monitor-autoscale-actions-resource-log-schema"></a>Azure Izleyici otomatik ölçeklendirme eylemleri kaynak günlüğü şeması

Aşağıda örnek verilerle birlikte otomatik ölçeklendirme kaynak günlüklerinin genel biçimleri verilmiştir. Aşağıdaki örneklerin hepsi, belirli bir alan için geçerli olabilecek birden çok değer içerebildiğinden JSON doğru şekilde biçimlendirilmemiş. 

Otomatik ölçeklendirme ile ilgili sorunları gidermek için bu türden olayları kullanın. Daha fazla bilgi için bkz. [Otomatik ölçeklendirme sorunlarını giderme](autoscale-troubleshoot.md).


## <a name="profile-evaluation"></a>Profil değerlendirmesi

Otomatik ölçeklendirme bir otomatik ölçeklendirme profiline göründüğünde kaydedilir

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": ["FixedDateProfileEvaluation", "RecurrentProfileEvaluation", "DefaultProfileEvaluation"],
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "profile": "defaultProfile",
    "profileSelected": [true, false]
  }
}
```

## <a name="profile-cooldown-evaluation"></a>Profil coolazaltma değerlendirmesi

Otomatik ölçeklendirme, soğuk bir süre nedeniyle ölçek yapamadığı takdirde kaydedilir. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleRuleCooldownEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "selectedProfile": "defaultProfile",
    "scaleDirection": ["Increase", "Decrease"]
    "lastScaleActionTime": "2018-09-10 18:08:00.6132593",
    "cooldown": "00:30:00",
    "evaluationTime": "2018-09-10 18:11:00.6132593",
    "skipRuleEvaluationForCooldown": true
  }
}
```

## <a name="rule-evaluation"></a>Kural değerlendirmesi

Otomatik ölçeklendirme ilk kez belirli bir ölçek kuralını değerlendirmeye başladığında kaydedilir. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleRuleEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "timeGrain": "00:01:00",
    "timeGrainStatistic": "Average",
    "timeWindow": "00:10:00",
    "timeAggregationType": "Average",
    "operator": "GreaterThan",
    "threshold": 70,
    "observedValue": 25,
    "estimateScaleResult": ["Triggered", "NotTriggered", "Unknown"]
  }
}
```

## <a name="metric-evaluation"></a>Ölçüm değerlendirmesi

Otomatik ölçeklendirme, bir ölçek eylemini tetiklemek için kullanılan ölçümü değerlendiriyorsa kaydedilir. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "MetricEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "timeGrain": "00:01:00",
    "timeGrainStatistic": "Average",
    "startTime": "2018-09-10 18:00:00.43833793",
    "endTime": "2018-09-10 18:10:00.43833793",
    "data": [0.33333333333333331,0.16666666666666666,1.0,0.33333333333333331,2.0,0.16666666666666666,9.5]
  }
}
```

## <a name="instance-count-evaluation"></a>Örnek sayısı değerlendirmesi

Otomatik ölçeklendirme, daha fazla başlaması gerekip gerekmediğini belirleme hazırlığı için zaten çalışan örneklerin sayısını değerlendirirken, bazı durumlarda veya hiçbir şey yapmazsa kaydedilir. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceCountEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "minimumInstanceCount": 15,
    "maximumInstanceCount": 30,
    "defaultInstanceCount": 20
  }
}
```

## <a name="scale-action-evaluation"></a>Ölçek eylemi değerlendirmesi

Bir ölçeklendirme eylemi gerçekleştiyse otomatik ölçeklendirme değerlendirmeye başladığında kaydedilir. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleActionOperationEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "lastScaleActionOperationId": "378ejr-7yye-892d-17dd-92ndijfe1738",
    "lastScaleActionOperationStatus": ["InProgress", "Timeout"]
    "skipCurrentAutoscaleEvaluation": [true, false]
  }
}
```

## <a name="instance-update-evaluation"></a>Örnek güncelleştirme değerlendirmesi

Otomatik ölçeklendirme, çalışan işlem örneklerinin sayısını yukarı veya aşağı doğru olarak güncelleştirdiğinde kaydedilir.

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceUpdateEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "newInstanceCount": 21,
    "shouldUpdateInstance": [true, false],
    "reason": ["Scale down action triggered", "Scale up to default instance count", ...]
  }
}
```

## <a name="scale-action"></a>Ölçeklendirme eylemi

Otomatik ölçeklendirme, yukarı veya aşağı ölçekleme eylemi başlattığında kaydedilir. 
```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceScaleAction",
  "category": "AutoscaleScaleActions",
  "resultType": ["Succeeded", "InProgress", "Failed"],
  "resultDescription": ["Create async operation job failed", ...]
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "newInstanceCount": 21,
    "scaleDirection": ["Increase", "Decrease"],
    ["createdAsyncScaleActionJob": [true, false],]
    ["createdAsyncScaleActionJobId": "378ejr-7yye-892d-17dd-92ndijfe1738",]
  }
}
```

## <a name="scale-action-tracking"></a>Ölçek eylemi izleme

Örnek ölçekleme eyleminin farklı aralıklarına kaydedilir.

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceScaleAction",
  "category": "AutoscaleScaleActions",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "scaleActionOperationId": "378ejr-7yye-892d-17dd-92ndijfe1738",
    "scaleActionOperationStatus": ["InProgress", "Timeout", "Canceled", ...],
    "scaleActionMessage": ["Scale action is inprogress", ...]
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar
[Otomatik ölçeklendirme](autoscale-overview.md) hakkında bilgi edinin