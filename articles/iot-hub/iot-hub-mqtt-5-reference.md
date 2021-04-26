---
title: Azure IoT Hub MQTT 5 API başvurusu (Önizleme)
description: IoT Hub MQTT 5 API başvurusu hakkında bilgi edinin
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603560"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>Veri düzlemi IoT Hub MQTT 5 API başvurusu

Bu belge `2020-10-01-preview` , IoT Hub veri DÜZLEMI API 'sinin sürüm 2,0 ' de (api-Version:) kullanılabilir işlemleri tanımlar.

## <a name="operations"></a>Operations

### <a name="get-twin"></a>Ikizi al

Ikizi durumunu al

#### <a name="request"></a>İstek

**Konu adı:**`$iothub/twin/get`

**Özellikler**: yok

**Yük**: boş

#### <a name="success-response"></a>Başarı yanıtı

**Özellikler**: yok

**Yük**: ikizi

#### <a name="alternative-responses"></a>Alternatif yanıtlar

| Durum | Ad | Açıklama |
| :----- | :--- | :---------- |
| 0100 |  Hatalı İstek | İşlem iletisi hatalı biçimlendirilmiş ve işlenemiyor. |
| 0101 |  Yetkilendirilmemiş | İstemcinin işlemi gerçekleştirme yetkisi yok. |
| 0102 |  Izin verilmiyor | İşleme izin verilmiyor. |
| 0501 |  Sürecek | istek hızı SKU başına çok yüksek |
| 0502 |  Kota aşıldı | geçerli SKU başına günlük Kota aşıldı |
| 0601 |  Sunucu Hatası | iç sunucu hatası |
| 0602 |  Zaman aşımı | işlem tamamlanmadan önce zaman aşımına uğradı |
| 0603 |  Sunucu meşgul | sunucu meşgul |

#### <a name="pseudo-code-sample"></a>Sözde kod örneği

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Patch Ikizi bildirildi

Patch Ikizi 'in bildirilen durumu

#### <a name="request"></a>İstek

**Konu adı:**`$iothub/twin/patch/reported`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| If-Version | u64 | hayır |  |

**Yük**: TwinState

#### <a name="success-response"></a>Başarı yanıtı

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| sürüm | u64 | evet | Düzeltme eki uygulandıktan sonra bildirilen durumun sürümü |

**Yük**: boş

#### <a name="alternative-responses"></a>Alternatif yanıtlar

| Durum | Ad | Açıklama |
| :----- | :--- | :---------- |
| 0104 |  Önkoşul başarısız oldu | Önkoşul karşılanmadı, isteğin iptal edilmesine neden oldu |
| 0100 |  Hatalı İstek | İşlem iletisi hatalı biçimlendirilmiş ve işlenemiyor. |
| 0101 |  Yetkilendirilmemiş | İstemcinin işlemi gerçekleştirme yetkisi yok. |
| 0102 |  Izin verilmiyor | İşleme izin verilmiyor. |
| 0501 |  Sürecek | istek hızı SKU başına çok yüksek |
| 0502 |  Kota aşıldı | geçerli SKU başına günlük Kota aşıldı |
| 0601 |  Sunucu Hatası | iç sunucu hatası |
| 0602 |  Zaman aşımı | işlem tamamlanmadan önce zaman aşımına uğradı |
| 0603 |  Sunucu meşgul | sunucu meşgul |

#### <a name="pseudo-code-sample"></a>Sözde kod örneği

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Alma komutları

Al ve işle komutları

#### <a name="message"></a>İleti

**Konu adı:**`$iothub/commands`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| sıra-Hayır | u64 | evet | İletinin sıra numarası |
| sıraya alınan süre | time | evet | İleti sisteme girildiğinde zaman damgası |
| teslimat sayısı | u32 | evet | İleti tesliminin denenme sayısı |
| oluşturma zamanı | time | hayır | İletinin oluşturulduğu zaman damgası (gönderen tarafından sağlandı) |
| ileti kimliği | string | hayır | İleti kimliği (gönderen tarafından sağlanmış) |
| user-id | string | hayır | Kullanıcı kimliği (gönderen tarafından verilir) |
| correlation-id | string | hayır | Bağıntı kimliği (gönderen tarafından verilir) |
| İçerik Türü | string | hayır | yükün Içerik türünü belirler |
| İçerik kodlama | string | hayır | yükün Içerik kodlamasını belirler |

**Yük**: herhangi bir bayt dizisi

#### <a name="success-acknowledgment"></a>Başarı bildirimi

Komutun istemci tarafından işleme için kabul edildiğini gösterir

**Özellikler**: yok

**Yük**: boş

#### <a name="alternative-acknowledgments"></a>Alternatif bildirimler

| Neden Kodu | Durum | Ad | Açıklama |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Bırak | Komutun Şu anda işlenmeyeceğini ve ileride yeniden teslim edilmesi gerektiğini gösterir. |
| 131 | 0100 | Reddet | Komutun istemci tarafından reddedildiğini ve yeniden denenmemelidir. |

#### <a name="pseudo-code-sample"></a>Sözde kod örneği

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Doğrudan Yöntemler al

Doğrudan Yöntem çağrılarını al ve işle

#### <a name="request"></a>İstek

**Konu adı:**`$iothub/methods/{name}`

**Özellikler**: yok

**Yük**: herhangi bir bayt dizisi

#### <a name="success-response"></a>Başarı yanıtı

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| Yanıt kodu | u32 | evet |  |

**Yük**: herhangi bir bayt dizisi

#### <a name="alternative-responses"></a>Alternatif yanıtlar

| Durum | Ad | Açıklama |
| :----- | :--- | :---------- |
| 06A0 |  Kullanılamaz | İstemciye bu bağlantı üzerinden erişilemediğini belirtir. |

#### <a name="pseudo-code-sample"></a>Sözde kod örneği

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Ikizi Istenen durum değişikliklerini al

Ikizi 'ın istenen durumuna yönelik güncelleştirmeleri al

#### <a name="message"></a>İleti

**Konu adı:**`$iothub/twin/patch/desired`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| sürüm | u64 | evet | Bu güncelleştirmeyle eşleşen istenen durumun sürümü |

**Yük**: TwinState

#### <a name="pseudo-code-sample"></a>Sözde kod örneği

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Telemetri gönder

Yönlendirme yapılandırması aracılığıyla varsayılan veya diğer uç nokta ile telemetri kanalına ileti gönderin.

#### <a name="message"></a>İleti

**Konu adı:**`$iothub/telemetry`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| İçerik Türü | string | hayır | `content-type`postalanan iletideki sistem özelliğine çevirir |
| İçerik kodlama | string | hayır | `content-encoding`postalanan iletideki sistem özelliğine çevirir |
| ileti kimliği | string | hayır | `message-id`postalanan iletideki sistem özelliğine çevirir |
| user-id | string | hayır | `user-id`postalanan iletideki sistem özelliğine çevirir |
| correlation-id | string | hayır | `correlation-id`postalanan iletideki sistem özelliğine çevirir |
| oluşturma zamanı | time | hayır | `iothub-creation-time-utc`postalanan iletideki özelliğe çevirir |

**Yük**: herhangi bir bayt dizisi

#### <a name="success-acknowledgment"></a>Başarı bildirimi

İleti telemetri kanalına başarıyla gönderildi

**Özellikler**: yok

**Yük**: boş

#### <a name="alternative-acknowledgments"></a>Alternatif bildirimler

| Neden Kodu | Durum | Ad | Açıklama |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Hatalı İstek | İşlem iletisi hatalı biçimlendirilmiş ve işlenemiyor. |
| 135 | 0101 | Yetkilendirilmemiş | İstemcinin işlemi gerçekleştirme yetkisi yok. |
| 131 | 0102 | Izin verilmiyor | İşleme izin verilmiyor. |
| 131 | 0601 | Sunucu Hatası | iç sunucu hatası |
| 151 | 0501 | Sürecek | istek hızı SKU başına çok yüksek |
| 151 | 0502 | Kota aşıldı | geçerli SKU başına günlük Kota aşıldı |
| 131 | 0602 | Zaman aşımı | işlem tamamlanmadan önce zaman aşımına uğradı |
| 131 | 0603 | Sunucu meşgul | sunucu meşgul |

#### <a name="pseudo-code-sample"></a>Sözde kod örneği

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Yanıtlar

### <a name="bad-request"></a>Hatalı İstek

İşlem iletisi hatalı biçimlendirilmiş ve işlenemiyor.

**Neden kodu:**`131`

**Durum:**`0100`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| reason | string | hayır | ileti hakkında özellikle geçerli olmayan bilgiler içerir |

**Yük**: boş

### <a name="conflict"></a>Çakışma

İşlem başka bir devam eden işlemle çakışıyor.

**Neden kodu:**`131`

**Durum:**`0103`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| İzleme kimliği | string | hayır | hata için ek tanılamalarla bağıntı için izleme KIMLIĞI |
| reason | string | hayır | ileti hakkında özellikle geçerli olmayan bilgiler içerir |

**Yük**: boş

### <a name="not-allowed"></a>Izin verilmiyor

İşleme izin verilmiyor.

**Neden kodu:**`131`

**Durum:**`0102`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| reason | string | hayır | ileti hakkında özellikle geçerli olmayan bilgiler içerir |

**Yük**: boş

### <a name="not-authorized"></a>Yetkilendirilmemiş

İstemcinin işlemi gerçekleştirme yetkisi yok.

**Neden kodu:**`135`

**Durum:**`0101`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| İzleme kimliği | string | hayır | hata için ek tanılamalarla bağıntı için izleme KIMLIĞI |

**Yük**: boş

### <a name="not-found"></a>Bulunamadı

istenen kaynak yok

**Neden kodu:**`131`

**Durum:**`0504`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| reason | string | hayır | ileti hakkında özellikle geçerli olmayan bilgiler içerir |

**Yük**: boş

### <a name="not-modified"></a>Değiştirilmedi

Kaynak, belirtilen önkoşula göre değiştirilmedi.

**Neden kodu:**`0`

**Durum:**`0001`

**Özellikler**: yok

**Yük**: boş

### <a name="precondition-failed"></a>Önkoşul başarısız oldu

Önkoşul karşılanmadı, isteğin iptal edilmesine neden oldu

**Neden kodu:**`131`

**Durum:**`0104`

**Özellikler**: yok

**Yük**: boş

### <a name="quota-exceeded"></a>Kota aşıldı

geçerli SKU başına günlük Kota aşıldı

**Neden kodu:**`151`

**Durum:**`0502`

**Özellikler**: yok

**Yük**: boş

### <a name="resource-exhausted"></a>Kaynak tükendi

kaynağın işlemi tamamlamaya yönelik bir kapasitesi yok

**Neden kodu:**`131`

**Durum:**`0503`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| reason | string | hayır | ileti hakkında özellikle geçerli olmayan bilgiler içerir |

**Yük**: boş

### <a name="server-busy"></a>Sunucu meşgul

sunucu meşgul

**Neden kodu:**`131`

**Durum:**`0603`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| İzleme kimliği | string | hayır | hata için ek tanılamalarla bağıntı için izleme KIMLIĞI |

**Yük**: boş

### <a name="server-error"></a>Sunucu Hatası

iç sunucu hatası

**Neden kodu:**`131`

**Durum:**`0601`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| İzleme kimliği | string | hayır | hata için ek tanılamalarla bağıntı için izleme KIMLIĞI |

**Yük**: boş

### <a name="target-failed"></a>Hedef başarısız oldu

Hedef yanıtladı, ancak yanıt geçersiz veya hatalı biçimlendirilmiş

**Neden kodu:**`131`

**Durum:**`06A2`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| reason | string | hayır | ileti hakkında özellikle geçerli olmayan bilgiler içerir |

**Yük**: boş

### <a name="target-timeout"></a>Hedef zaman aşımı

Hedefin isteği tamamlaması beklenirken zaman aşımı oluştu

**Neden kodu:**`131`

**Durum:**`06A1`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| İzleme kimliği | string | hayır | hata için ek tanılamalarla bağıntı için izleme KIMLIĞI |
| reason | string | hayır | ileti hakkında özellikle geçerli olmayan bilgiler içerir |

**Yük**: boş

### <a name="target-unavailable"></a>Hedef kullanılamıyor

İsteğin tamamlanabilmesi için hedefe ulaşılamıyor

**Neden kodu:**`131`

**Durum:**`06A0`

**Özellikler**: yok

**Yük**: boş

### <a name="throttled"></a>Sürecek

istek hızı SKU başına çok yüksek

**Neden kodu:**`151`

**Durum:**`0501`

**Özellikler**: yok

**Yük**: boş

### <a name="timeout"></a>Zaman aşımı

işlem tamamlanmadan önce zaman aşımına uğradı

**Neden kodu:**`131`

**Durum:**`0602`

**Özellikler**:

| Ad | Tür | Gerekli | Açıklama |
| :--- | :--- | :------- | :---------- |
| İzleme kimliği | string | hayır | hata için ek tanılamalarla bağıntı için izleme KIMLIĞI |

**Yük**: boş

