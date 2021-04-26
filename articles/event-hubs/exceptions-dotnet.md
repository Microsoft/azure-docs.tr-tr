---
title: Azure Event Hubs-.NET özel durumları
description: Bu makale, Azure Event Hubs .NET ileti özel durumlarının ve önerilen eylemlerin bir listesini sağlar.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: spelluru
ms.openlocfilehash: af2d820fd255d041ade21a00d7d7a9ac6b12fcba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745911"
---
# <a name="eventhubsexception---net"></a>EventHubsException-.NET
Bir **Eventhubsexception** , Event Hubs öğesine özgü bir işlem, hizmette ve istemciye özgü hatalar da dahil olmak üzere bir soruna neden olduğunda tetiklenir. 

## <a name="exception-information"></a>Özel durum bilgileri
Özel durum, hatanın bağlamını ve göreli önem derecesini anlamak için aşağıdaki bağlamsal bilgileri içerir. 

- **Isgeçici**: özel durumun kurtarılabilir olarak kabul edilip edilmeyeceğini tanımlar. Geçici olarak kabul edildiği durumda, uygun yeniden deneme ilkesi zaten uygulanmış ve yeniden denemeler başarısız oldu.
- **Neden**: kök nedeni kategorilere ayırmanıza ve açıklığa kavuşturmaya yardımcı olan başarısızlık için iyi bilinen nedenler kümesi sağlar. Bu nedenler, bir özel durum iletisinin metni incelenirken özel durum filtreleme ve diğer mantık uygulamaya izin vermek için tasarlanmıştır. Bazı önemli başarısızlık nedenleri şunlardır:
    - **Istemci kapatıldı**: zaten kapatılan veya atılmış bir olay hub 'ı istemcisi olduğunda gerçekleşir. Event Hubs istemci kitaplığından nesnelerin amaçlanan kapsamda oluşturulmasını ve kapatılmasını sağlamak için uygulama kodunu kontrol etmenizi öneririz.
    - **Hizmet zaman aşımı**: Event Hubs hizmetinin beklenen süre içinde bir işleme yanıt vermediğini gösterir. Bu sorun, geçici bir ağ sorunu veya hizmet sorunu nedeniyle oluşmuş olabilir. Event Hubs hizmeti isteği başarıyla tamamlamış olabilir veya olmayabilir; durum bilinmiyor. Geçerli durumu doğrulamaya ve gerekirse yeniden denemeye çalışılması önerilir.
    - **Kota aşıldı**: tek bir tüketici grubu için çok fazla sayıda etkin okuma işlemi olduğunu gösterir. Bu sınır Event Hubs ad alanının katmanına bağlıdır ve daha yüksek bir katmana taşımaya gerek olabilir. Alternatif, ek tüketici grupları oluşturmak ve herhangi bir grup için tüketici istemci okuma sayısının sınırın içinde olduğundan emin olmak olacaktır. Daha fazla bilgi için bkz. [Azure Event Hubs kotaları ve limitleri](event-hubs-quotas.md).
    - **Ileti boyutu aşıldı**: olay verileri, hem tek bir olay hem de bir dizi olay için izin verilen maksimum boyut olarak. Bu, olay verilerini ve ilişkili tüm meta verileri ve sistem ek yükünü içerir. Bu hatayı çözmek için bir toplu işte gönderilen olay sayısını azaltın veya iletiye eklenen verilerin boyutunu azaltın. Boyut sınırları değişikliğe tabi olduğundan, Ayrıntılar için bkz. [Azure Event Hubs kotaları ve sınırları](event-hubs-quotas.md) .
    - **Tüketici bağlantısı kesildi**: Olay Hub 'ı örneğinden bir tüketici Istemcisinin Olay Hub 'ı hizmeti bağlantısı kesildi. Bu durum genellikle, daha yüksek sahibi olan bir tüketici bir bölüm ve Tüketici grubu eşleştirmesi üzerinde sahiplik onayalgıladığında oluşur.
    - **Kaynak bulunamadı**: Event Hubs hizmeti bir olay hub 'ı, Tüketici grubu veya bölüm gibi bir kaynak bulamadı. Silinmiş olabilir veya Event Hubs hizmetin kendisi ile ilgili bir sorun var.

## <a name="handling-exceptions"></a>Özel durum işleme
**Eventhubexception** için belirli bir hata nedenine birkaç şekilde tepki verebilirsiniz. Tek yönlü, catch bloğunun bir parçası olarak bir özel durum filtre yan tümcesi uygulamaktır.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Sonraki adımlar
[Eski makalede](event-hubs-messaging-exceptions.md)belgelenen başka özel durumlar da vardır. Bunlardan bazıları yalnızca eski Event Hubs .NET istemci kitaplığı için geçerlidir.