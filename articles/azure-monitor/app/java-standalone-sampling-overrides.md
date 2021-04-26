---
title: Örnekleme geçersiz kılmaları (Önizleme)-Java için Azure Izleyici Application Insights
description: Java için Azure Izleyici Application Insights örnekleme geçersiz kılmalarını yapılandırmayı öğrenin.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 7602392b78f53e5b896e92058836fca60de39d64
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448891"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Örnekleme geçersiz kılmaları (Önizleme)-Java için Azure Izleyici Application Insights

> [!NOTE]
> Örnekleme geçersiz kılma özelliği, 3.0.3 'den başlayarak önizlemededir.

Örnekleme geçersiz kılmaları [Varsayılan örnekleme yüzdesini](./java-standalone-config.md#sampling)geçersiz kılmanızı sağlar, örneğin:
 * Gürültülü durum denetimleri için örnekleme yüzdesini 0 (veya küçük bir değer) olarak ayarlayın.
 * Gürültülü bağımlılık çağrıları için örnekleme yüzdesini 0 (veya küçük bir değer) olarak ayarlayın.
 * Örnekleme yüzdesini önemli bir istek türü için 100 olarak ayarlayın (örn. `/login` ), varsayılan örneklemenin daha düşük bir değere yapılandırılmış olmasına rağmen.

## <a name="terminology"></a>Terminoloji

Örnekleme geçersiz kılma bilgileri hakkında bilgi edinmek için, terim *aralığını* anlamalısınız. Yayılma, için genel bir terimdir:

* Gelen istek.
* Giden bağımlılığı (örneğin, başka bir hizmete uzak çağrı).
* İşlem içi bağımlılık (örneğin, hizmetin alt bileşenleri tarafından gerçekleştirilen iş).

Örnekleme geçersiz Kılmalarda, bu span bileşenleri önemlidir:

* Öznitelikler

Span öznitelikleri, belirli bir isteğin veya bağımlılığın hem standart hem de özel özelliklerini temsil eder.

## <a name="getting-started"></a>Kullanmaya başlama

Başlamak için, *üzerindeapplicationinsights.js* adlı bir yapılandırma dosyası oluşturun. *ApplicationInsights-Agent- \* . jar* ile aynı dizine kaydedin. Aşağıdaki şablonu kullanın.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Nasıl çalışır?

Bir yayılma başladığında, bu zaman aralığında yer alan öznitelikler, örnekleme geçersiz kılmalarının eşleşip eşleşeceğini denetlemek için kullanılır.

Örnekleme aşağıdakilerden biri eşleşiyorsa, yayılma alanının örneklenip örneklenmeyeceğine karar vermek için örnekleme yüzdesi kullanılır.

Yalnızca eşleşen ilk örnekleme geçersiz kılma kullanılır.

Örnekleme hiçbir geçersiz kılma eşleşmezse:

* Bu, izlem içindeki ilk yayılma ise, [Varsayılan örnekleme yüzdesi](./java-standalone-config.md#sampling) kullanılır.
* Bu, izlemede ilk Aralık değilse, ana örnekleme kararı kullanılır.

> [!WARNING]
> Bir kapsamı toplamayan bir karar verme işlemi yapıldığında, aşağı akış yayılımını karşılayan örnekleme geçersiz kılma işlemleri olsa bile tüm aşağı akış yayılmaları da toplanmaz.
> Bu davranış, aksi halde kopuk izlemeler nedeniyle, aşağı akış yayılmaları, ancak toplanmayan yayılmalar için ana öğe olarak sonuçlanacağından gereklidir.

> [!NOTE]
> Örnekleme kararı, TraceID 'nin (operationId olarak da bilinir) 0 ile 100 arasında bir sayıya Benzetime ve bu karmayı daha sonra örnekleme yüzdesine kıyasla temel alır.
> Belirli bir izlemede bulunan tüm yayılmalar aynı TraceID değerine sahip olduğundan, aynı karma değerine sahip olur ve örnekleme kararı tüm izleme genelinde tutarlı olacaktır.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Örnek: sistem durumu denetimleri için telemetri toplamayı gösterme

Bu, tüm istekleri için telemetri toplamayı bastırır `/health-checks` .

Bu Ayrıca, normalde altında toplanabilecek tüm aşağı akış yayılımları (bağımlılıklar) toplamayı da bastırır `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Örnek: gürültülü bağımlılık çağrısı için telemetri toplamayı gösterme

Bu, tüm reddir çağrıları için telemetri toplamayı bastırır `GET my-noisy-key` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Örnek: önemli bir istek türü için telemetri %100 ' i toplayın

Bu, için telemetri %100 ' i toplar `/login` .

Aşağı akış yayıldığı için (bağımlılıklar) üst öğenin örnekleme kararına (Bu aşağı akış kapsamı için herhangi bir örnekleme geçersiz kılma yok) uyduğundan, bunlar tüm '/Login ' istekleri için de toplanacak.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Ortak span öznitelikleri

Bu bölümde, örnekleme geçersiz kılmalarının kullanabileceği bazı ortak Aralık öznitelikleri listelenmektedir.

### <a name="http-spans"></a>HTTP yayılmaları

| Öznitelik  | Tür | Description | 
|---|---|---|
| `http.method` | dize | HTTP istek yöntemi.|
| `http.url` | string | Formdaki tam HTTP isteği URL 'SI `scheme://host[:port]/path?query[#fragment]` . Parça genellikle HTTP üzerinden aktarılmaz. Ancak parça biliniyorsa, dahil edilmelidir.|
| `http.status_code` | sayı | [Http yanıtı durum kodu](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | HTTP protokolünün türü. |
| `http.user_agent` | string | İstemci tarafından gönderilen [http Kullanıcı Aracısı](https://tools.ietf.org/html/rfc7231#section-5.5.3) üstbilgisinin değeri. |

### <a name="jdbc-spans"></a>JDBC yaymalar

| Öznitelik  | Tür | Description  |
|---|---|---|
| `db.system` | dize | Kullanılan veritabanı yönetim sistemi (DBMS) ürünü için tanımlayıcı. |
| `db.connection_string` | string | Veritabanına bağlanmak için kullanılan bağlantı dizesi. Katıştırılmış kimlik bilgilerini kaldırmanız önerilir.|
| `db.user` | string | Veritabanına erişmek için Kullanıcı adı. |
| `db.name` | string | Erişilmekte olan veritabanının adını raporlamak için kullanılan dize. Veritabanına geçiş yapan komutlar için, komut başarısız olsa bile, bu dize hedef veritabanına ayarlanmalıdır.|
| `db.statement` | string | Çalıştırılan veritabanı ekstresi.|
