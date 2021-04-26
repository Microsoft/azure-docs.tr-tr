---
title: Azure uygulama yapılandırma REST API azaltma
description: Azure Uygulama yapılandırması 'nı kullanırken azaltmayı anlamak için başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b4472fdcacfe5b62c0cded089224c6d41e32f47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932447"
---
# <a name="throttling"></a>Azaltma

Yapılandırma depoları, karşılaşabileceği istekler üzerinde sınırlara sahiptir. Bir yapılandırma deposu için ayrılan kotayı aşan tüm istekler, bir HTTP 429 (çok fazla Istek) yanıtı alır.

Azaltma, farklı kota ilkelerine ayrılmıştır:

- **Toplam** istek sayısı-toplam istek sayısı
- **Toplam bant genişliği** -bayt cinsinden giden veri
- **Depolama** -bayt cinsinden Kullanıcı verilerinin toplam depolama alanı boyutu

## <a name="handling-throttled-responses"></a>Kısıtlanmış yanıtları işleme

Belirli bir kota için hız sınırına ulaşıldığında sunucu, _429_ durum kodu ile o türdeki diğer isteklere yanıt verir. _429_ yanıtı, istemciye istek kotasının stoğunu sağlamak için önerilen bekleme süresi (milisaniye cinsinden) sağlayan bir _yeniden deneme-MS_ üst bilgisi içerir.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

Yukarıdaki örnekte istemci, izin verilen kotasını aşmıştır ve daha fazla istek denemeden önce bu sürenin yavaşlanmasını ve 10 milisaniyeyi beklemesi önerilir. İstemciler aşamalı geri dönüşü de göz önünde bulundurmalıdır.

## <a name="other-retry"></a>Diğer yeniden deneme

Hizmet, istemci yeniden denemesi gereken daraltma dışında durumları tanımlayabilir (örn: 503 Hizmet kullanılamıyor). Bu gibi durumlarda, `retry-after-ms` yanıt üst bilgisi sağlanacaktır. Sağlamlığını artırmak için, istemcinin önerilen aralığı izlemesi ve yeniden deneme yapması önerilir.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
