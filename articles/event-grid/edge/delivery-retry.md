---
title: Teslim ve yeniden deneme-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid teslim edin ve yeniden deneyin.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 324c0e9b8dcaafacaac52b622ce9c533d82c7ff1
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100703"
---
# <a name="delivery-and-retry"></a>Teslim ve yeniden deneme

Event Grid dayanıklı teslim sağlar. Her iletiyi her bir eşleşen abonelik için en az bir kez teslim etmeye çalışır. Bir abonenin uç noktası bir olayın alındığını kabul etmez veya bir hata oluşursa, yeniden deneme **çizelgesine** ve **yeniden deneme ilkesine**göre teslimi Event Grid.  Varsayılan olarak, Event Grid modülü abone için bir seferde bir olay sunar. Yük, ancak tek bir olay içeren bir dizidir. Çıkış toplu işleme özelliğini etkinleştirerek modülün bir seferde birden fazla olay sunabilmenize olanak sağlayabilirsiniz. Bu özellik hakkında daha fazla bilgi için bkz. [çıktıyı toplu işleme](delivery-output-batching.md).  

> [!IMPORTANT]
>Olay verileri için kalıcılık desteği yoktur. Bu, Event Grid modülün yeniden dağıtılması veya yeniden başlatılması, henüz teslim edilmemiş tüm olayları kaybetmenize neden olur.

## <a name="retry-schedule"></a>Zamanlamayı yeniden dene

Event Grid bir ileti teslim edildikten sonra yanıt için 60 saniyeye kadar bekler. Abonenin uç noktası yanıta onay vermezse, sonraki yeniden denemeler için geri dönüş kuyruklarımızdan birinde ileti sıraya alınır.

Yeniden deneneceği zamanlamayı belirleyen, önceden yapılandırılmış iki geri dönüş kuyruğu vardır. Bunlar:-

| Zamanlama | Açıklama |
| ---------| ------------ |
| 1 dakika | Burada biten iletiler her dakikada denenir.
| 10 dakika | Burada sona eklenen iletiler her 10 dakikada bir denenir.

### <a name="how-it-works"></a>Nasıl çalışır

1. İleti Event Grid modülüne ulaştı. Hemen teslim etme denemesi yapılır.
1. Teslim başarısız olursa, ileti 1 dakikalık sıraya göre sıraya konur ve bir dakika sonra yeniden denenir.
1. Teslim başarısız olmaya devam ederse, ileti 10 dakikalık sıraya göre sıraya alınır ve her 10 dakikada bir yeniden denenir.
1. Teslimler, başarılı olana veya yeniden deneme ilkesi sınırlarına ulaşılıncaya kadar denenir.

## <a name="retry-policy-limits"></a>İlke sınırlarını yeniden dene

Yeniden deneme ilkesini tespit eden iki yapılandırma vardır. Bunlar:-

* En fazla deneme sayısı
* Etkinlik yaşam süresi (TTL)

Yeniden deneme ilkesinin limitlerinin herhangi birine ulaşıldığında bir olay bırakılır. Yeniden deneme zamanlaması, yeniden deneme zamanlaması bölümünde açıklanmıştı. Bu limitlerin yapılandırılması, tüm aboneler veya abonelik başına temelinde yapılabilir. Aşağıdaki bölümde, her biri daha ayrıntılı olarak açıklanmıştır.

## <a name="configuring-defaults-for-all-subscribers"></a>Tüm aboneler için Varsayılanları Yapılandırma

Event Grid dağıtımının bir parçası olarak yapılandırılabilecek iki özellik vardır: `brokers:defaultMaxDeliveryAttempts` ve `broker:defaultEventTimeToLiveInSeconds` tüm aboneler için yeniden deneme ilkesi varsayılanlarını denetler.

| Özellik adı | Açıklama |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Bir olayı teslim etmeye yönelik deneme sayısı üst sınırı. Varsayılan değer: 30.
| `broker:defaultEventTimeToLiveInSeconds` | Bir olayın teslim edilmeden önce bırakılması gereken saniye cinsinden olay TTL 'SI. Varsayılan değer: **7200** saniye

## <a name="configuring-defaults-per-subscriber"></a>Abone başına Varsayılanları Yapılandırma

Ayrıca, her abonelik için yeniden deneme ilkesi sınırlarını belirtebilirsiniz.
Abone başına Varsayılanları yapılandırma hakkında bilgi edinmek için [API belgelerimize](api.md) bakın. Abonelik düzeyi Varsayılanları, modül düzeyi yapılandırmalarının üzerine yazar.

## <a name="examples"></a>Örnekler

Aşağıdaki örnek, Event Grid modülünde Maxnumberofdenemeler = 3 ve olay TTL değeri 30 dakika olan yeniden deneme ilkesini ayarlar

```json
{
  "Env": [
    "broker:defaultMaxDeliveryAttempts=3",
    "broker:defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Aşağıdaki örnek, Maxnumberofdenemeler = 3 ve olay TTL 'si olan 30 dakikalık bir Web kancası aboneliği ayarlıyor

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
