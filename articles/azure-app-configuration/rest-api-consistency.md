---
title: Azure uygulama yapılandırma REST API tutarlılığı
description: Azure Uygulama Yapılandırması ' nı kullanarak gerçek zamanlı tutarlılık sağlamaya yönelik başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932634"
---
# <a name="real-time-consistency"></a>Gerçek zamanlı tutarlılık

Bazı dağıtılmış sistemlerin doğası nedeniyle, istekler arasındaki gerçek zamanlı tutarlılık örtük olarak zorlamak zordur. Bir çözüm, birden fazla eşitleme belirteci biçiminde protokol desteğine izin verdir. Eşitleme belirteçleri isteğe bağlıdır.

## <a name="initial-request"></a>İlk istek

Farklı istemci örnekleri ve istekleri arasındaki gerçek zamanlı tutarlılığı güvence altına almak için isteğe bağlı `Sync-Token` istek ve yanıt üst bilgilerini kullanın.

Söz dizimi:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parametre|Açıklama|
|--|--|
| `<id>` | Belirteç KIMLIĞI (donuk) |
| `<value>` | Belirteç değeri (donuk). Base64 kodlu dizeye izin verir. |
| `<sn>` | Belirteç sıra numarası (sürüm). Daha yüksek olan aynı belirtecin daha yeni bir sürümü anlamına gelir. Daha iyi eşzamanlılık ve istemci önbelleklemesini sağlar. İstemci, belirteç sürümleri dahil olduğu için yalnızca belirtecin en son sürümünü kullanmayı tercih edebilir. Bu parametre istekler için gerekli değildir. |

## <a name="response"></a>Yanıt

Hizmet, `Sync-Token` Her yanıta sahip bir üst bilgi sağlar.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Sonraki istekler

Sonraki tüm istekler, belirtilen zamana göre gerçek zamanlı tutarlı bir yanıt garanti edilir `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

`Sync-Token`İstekten gelen üstbilgiyi atlarsanız, hizmetin, dahili olarak bir süre (birkaç saniye kadar) sırasında önbelleğe alınmış verilerle yanıt vermesini ve bu verilerin dahili olarak uygulanmadan önce olması mümkündür. Bu davranış, okumadan hemen önce değişiklikler gerçekleştiyse tutarsız okumaların oluşmasına neden olabilir.

## <a name="multiple-sync-tokens"></a>Birden çok eşitleme belirteci

Sunucu, tek bir istek için birden çok eşitleme belirteci ile yanıt verebilir. Sonraki istek için gerçek zamanlı tutarlılık sağlamak üzere, istemcinin tüm alınan eşitleme belirteçleriyle yanıt vermesi gerekir. Birden çok üst bilgi değeri, virgülle ayrılmış olmalıdır.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
