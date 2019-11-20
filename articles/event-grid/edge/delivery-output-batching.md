---
title: Azure Event Grid IoT Edge çıkış toplu işleme | Microsoft Docs
description: IoT Edge Event Grid çıkış toplu işi.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7d6b83354baf3db5ddb65f94fee1c3dce2dcca94
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992463"
---
# <a name="output-batching"></a>Çıktıyı toplu işleme

Event Grid tek bir teslim isteğinde birden fazla olay sunma desteği vardır. Bu özellik, HTTP istek başına fazla kafaları ödemeksizin genel teslim aktarım hızını arttırmanızı olanaklı kılar. Toplu işlem varsayılan olarak kapalıdır ve abonelik başına açılabilir.

> [!WARNING]
> Her teslim isteğini işlemek için izin verilen en uzun süre, abone kodu potansiyel olarak toplu istek başına daha fazla iş yapmasa bile değişmez. Teslim zaman aşımı varsayılan olarak 60 saniyedir.

## <a name="batching-policy"></a>Toplu işlem ilkesi

Event Grid toplu işlem davranışı, aşağıdaki iki ayarı ayırarak abone başına özelleştirilebilir:

* Toplu işlem başına en fazla olay

  Bu ayar, toplu teslim isteğine eklenebilen olay sayısı için üst sınır ayarlar.

* Kilobayt olarak tercih edilen toplu Iş boyutu

  Bu düğme, teslim isteği başına gönderilebilen en fazla kilobayt sayısını daha fazla denetlemek için kullanılır

## <a name="batching-behavior"></a>Toplu işleme davranışı

* Tümü veya hiçbiri

  Event Grid, All veya-None semantiğinin üzerinde çalışır. Toplu bir teslimin kısmen başarısını desteklemez. Aboneler, 60 saniye içinde makul bir şekilde işleyebilecekleri için yalnızca toplu işlem başına çok sayıda olay sormaya dikkat edilmelidir.

* İyimser toplu işleme

  Toplu işleme ilke ayarları, toplu işlem davranışında katı sınırlar değildir ve en iyi çaba esasına göre dikkate alınır. Düşük olay hızlarında genellikle toplu iş boyutunu, toplu işlem başına istenen en fazla olaydan daha az olduğunu gözlemleyeceksiniz.

* Varsayılan değer kapalı olarak ayarlanmıştır

  Varsayılan olarak, Event Grid her teslim isteğine yalnızca bir olay ekler. Toplu işi açma yöntemi, daha önce bahsedilen olay aboneliği JSON ' da makalesinde bahsedilen ayarlardan birini ayarlıdır.

* Varsayılan değerler

  Olay aboneliği oluştururken, her iki ayarı da (toplu işlem başına en fazla olay ve cihazının saniyede bayt cinsinden yaklaşık bir toplu iş boyutu) belirtmeniz gerekmez. Yalnızca bir ayar ayarlandıysa Event Grid, (yapılandırılabilir) varsayılan değerleri kullanır. Varsayılan değerler için aşağıdaki bölümlere ve bunların nasıl geçersiz kılınacağını görün.

## <a name="turn-on-output-batching"></a>Çıkış toplu işlemeyi aç

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>İzin verilen maksimum değerleri yapılandırma

Aşağıdaki dağıtım zamanı ayarları, bir olay aboneliği oluştururken izin verilen en büyük değeri denetler.

| Özellik adı | Açıklama |
| ------------- | ----------- | 
| `api:deliveryPolicyLimits:maxpreferredBatchSizeInKilobytes` | `PreferredBatchSizeInKilobytes` düğmesi için izin verilen en büyük değer. Varsayılan `1033`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | `MaxEventsPerBatch` düğmesi için izin verilen en büyük değer. Varsayılan `50`.

## <a name="configuring-runtime-default-values"></a>Çalışma zamanı varsayılan değerlerini yapılandırma

Aşağıdaki dağıtım zamanı ayarları, olay aboneliğinde belirtilmediğinde her düğmenin çalışma zamanı varsayılan değerini denetler. Yeniden tekrarlamayı sağlamak için, olay aboneliğinde en az bir düğme ayarlanmalıdır ve toplu işleme davranışını açmanız gerekir.

| Özellik adı | Açıklama |
| ------------- | ----------- |
| `broker:defaultMaxBatchSizeInBytes` | Yalnızca `MaxEventsPerBatch` belirtildiğinde en fazla teslim isteği boyutu. Varsayılan `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Yalnızca `MaxBatchSizeInBytes` belirtildiğinde bir toplu işe eklenecek en fazla olay sayısı. Varsayılan `10`.
