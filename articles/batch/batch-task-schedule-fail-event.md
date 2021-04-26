---
title: Azure Batch görev zamanlaması başarısız olayı
description: Batch görev zamanlaması hata olayı için başvuru. Bu olay, bir görev zamanlanırsa ve daha sonra yeniden deneneceği zaman yayılır.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852188"
---
# <a name="task-schedule-fail-event"></a>Görev zamanlaması başarısızlık olayı

 Bu olay, bir görev zamanlanırsa ve daha sonra yeniden deneneceği zaman yayılır. Bu, kaynak sınırlaması nedeniyle görev zamanlama zamanında geçici bir hata, örneğin, belirtilen bir görevi çalıştırmak için düğümlerde yeterli kullanılabilir yuva yok `requiredSlots` .

 Aşağıdaki örnekte, bir görev zamanlaması hata olayının gövdesi gösterilmektedir.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
    }
}
```

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`jobId`|Dize|Görevi içeren işin KIMLIĞI.|
|`id`|Dize|Görevin KIMLIĞI.|
|`taskType`|Dize|Görevin türü. Bu, bir iş Yöneticisi görevi olduğunu ya da bir iş Yöneticisi görevi olmadığını belirten ' user ' olduğunu belirten ' JobManager ' olabilir. Bu olay iş hazırlama görevleri, iş bırakma görevleri veya başlangıç görevleri için yayınlanmaz.|
|`systemTaskVersion`|Int32|Bu, bir görevde iç yeniden deneme sayacıdır. Toplu olarak Batch hizmeti, geçici sorunlar için bir görevi hesaba yeniden deneyebilir. Bu sorunlar, iç zamanlama hataları içerebilir veya işlem düğümlerinden hatalı bir durumda kurtarmaya çalışır.|
|`requiredSlots`|Int32|Görevi çalıştırmak için gereken yuvalar.|
|[`nodeInfo`](#nodeInfo)|Karmaşık Tür|Görevin çalıştırıldığı işlem düğümüyle ilgili bilgiler içerir.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Karmaşık Tür|Görevin birden çok işlem düğümü gerektiren çok örnekli bir görev olduğunu belirtir.  [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task)Ayrıntılar için bkz..|
|[`constraints`](#constraints)|Karmaşık Tür|Bu görev için uygulanan yürütme kısıtlamaları.|
|[`schedulingError`](#schedulingError)|Karmaşık Tür|Görevin zamanlama hatası hakkında bilgi içerir.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> NodeInfo

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`poolId`|Dize|Görevin çalıştırıldığı havuzun KIMLIĞI.|
|`nodeId`|Dize|Görevin çalıştırıldığı düğümün KIMLIĞI.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> Multiınstancesettings

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Görevin gerektirdiği işlem düğümü sayısı.|

###  <a name="constraints"></a><a name="constraints"></a> kısıtlamaları

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Görevin en fazla yeniden denenme sayısı. Batch hizmeti, çıkış kodu sıfır değilse bir görevi yeniden dener.<br /><br /> Bu değerin özellikle yeniden deneme sayısını denetlediğine unutmayın. Batch hizmeti görevi bir kez dener ve daha sonra bu sınıra kadar yeniden deneyebilir. Örneğin, en fazla yeniden deneme sayısı 3 ise Batch, 4 kata kadar bir görevi dener (bir başlangıç denemesi ve 3 yeniden deneme).<br /><br /> En fazla yeniden deneme sayısı 0 ise, Batch hizmeti görevleri yeniden denemeyecek.<br /><br /> En fazla yeniden deneme sayısı-1 ise, Batch hizmeti görevleri sınırlı olmadan yeniden dener.<br /><br /> Varsayılan değer 0 ' dır (yeniden deneme yok).|


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|Öğe adı|Tür|Notlar|
|------------------|----------|-----------|
|`category`|Dize|Hatanın kategorisi.|
|`code`|Dize|Görev zamanlama hatası için bir tanımlayıcı. Kodlar değişmez ve programlı olarak tüketilmelidir.|
|`message`|Dize|Bir kullanıcı arabiriminde görüntülenmek üzere uygun olması amaçlanan görev zamanlama hatasını açıklayan bir ileti.|
