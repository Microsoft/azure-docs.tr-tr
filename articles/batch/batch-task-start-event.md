---
title: Azure Batch görev başlatma olayı
description: Batch görevi başlatma olayı için başvuru bilgileri. Bu olay, bir Görev Zamanlayıcı tarafından bir işlem düğümünde başlamak üzere zamanlandıktan sonra yayınlanır.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 3a57ffbb1e1659cff54d101aa4b90ca1bd5d3a57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91851025"
---
# <a name="task-start-event"></a>Görev başlangıç olayı

 Bu olay, bir Görev Zamanlayıcı tarafından bir işlem düğümünde başlamak üzere zamanlandıktan sonra yayınlanır. Görev yeniden deneniyorsa veya yeniden kuyruğa bu olay aynı görev için tekrar yayınlanacaktır, ancak yeniden deneme sayısı ve sistem görevinin sürümü uygun şekilde güncelleştirilir.


 Aşağıdaki örnekte, bir görev başlatma olayının gövdesi gösterilmektedir.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`jobId`|Dize|Görevi içeren işin KIMLIĞI.|
|`id`|Dize|Görevin KIMLIĞI.|
|`taskType`|Dize|Görevin türü. Bu, bir iş Yöneticisi görevi olduğunu ya da bir iş Yöneticisi görevi olmadığını belirten ' user ' olduğunu belirten ' JobManager ' olabilir.|
|`systemTaskVersion`|Int32|Bu, bir görevde iç yeniden deneme sayacıdır. Toplu olarak Batch hizmeti, geçici sorunlar için bir görevi hesaba yeniden deneyebilir. Bu sorunlar, iç zamanlama hataları içerebilir veya işlem düğümlerinden hatalı bir durumda kurtarmaya çalışır.|
|`requiredSlots`|Int32|Görevi çalıştırmak için gereken yuvalar.|
|[`nodeInfo`](#nodeInfo)|Karmaşık Tür|Görevin çalıştırıldığı işlem düğümüyle ilgili bilgiler içerir.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Karmaşık Tür|Görevin birden çok işlem düğümü gerektiren çok örnekli bir görev olduğunu belirtir.  Ayrıntılar için bkz. [Multiınstancesettings](/rest/api/batchservice/get-information-about-a-task) .|
|[`constraints`](#constraints)|Karmaşık Tür|Bu görev için uygulanan yürütme kısıtlamaları.|
|[`executionInfo`](#executionInfo)|Karmaşık Tür|Görevin yürütülmesi hakkındaki bilgileri içerir.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> NodeInfo

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`poolId`|Dize|Görevin çalıştırıldığı havuzun KIMLIĞI.|
|`nodeId`|Dize|Görevin çalıştırıldığı düğümün KIMLIĞI.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> Multiınstancesettings

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`numberOfInstances`|int|Görevin gerektirdiği işlem düğümü sayısı.|

###  <a name="constraints"></a><a name="constraints"></a> kısıtlamaları

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Görevin en fazla yeniden denenme sayısı. Batch hizmeti, çıkış kodu sıfır değilse bir görevi yeniden dener.<br /><br /> Bu değerin özellikle yeniden deneme sayısını denetlediğine unutmayın. Batch hizmeti görevi bir kez dener ve daha sonra bu sınıra kadar yeniden deneyebilir. Örneğin, en fazla yeniden deneme sayısı 3 ise Batch, 4 kata kadar bir görevi dener (bir başlangıç denemesi ve 3 yeniden deneme).<br /><br /> En fazla yeniden deneme sayısı 0 ise, Batch hizmeti görevleri yeniden denemeyecek.<br /><br /> En fazla yeniden deneme sayısı-1 ise, Batch hizmeti görevleri sınırlı olmadan yeniden dener.<br /><br /> Varsayılan değer 0 ' dır (yeniden deneme yok).|

###  <a name="executioninfo"></a><a name="executionInfo"></a> ExecutionInfo

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`retryCount`|Int32|Görevin Batch hizmeti tarafından yeniden denenme sayısı. Bu görev, belirtilen MaxTaskRetryCount 'a kadar sıfır olmayan çıkış kodu ile çıktıktan sonra yeniden denenir|
