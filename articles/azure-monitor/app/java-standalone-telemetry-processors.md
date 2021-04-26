---
title: Telemetri işlemcileri (Önizleme)-Java için Azure Izleyici Application Insights
description: Java için Azure Izleyici Application Insights telemetri işlemcilerini yapılandırmayı öğrenin.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 991e52c13a5730b83552abb6b922d4d7a57c5429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024124"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetri işlemcileri (Önizleme)-Java için Azure Izleyici Application Insights

> [!NOTE]
> Telemetri işlemcileri özelliği önizleme aşamasındadır.

Application Insights için Java 3,0 Aracısı, veriler verilene kadar telemetri verilerini işleyebilir.

Telemetri işlemcilerin bazı kullanım örnekleri aşağıda verilmiştir:
 * Hassas verileri maskeleme.
 * Özel boyutları koşullu olarak ekleyin.
 * Azure portal benzer Telemetriyi toplamak için kullanılan span adını güncelleştirin.
 * Alım maliyetlerini denetlemek için belirli span özniteliklerini bırakın.

> [!NOTE]
> Alım maliyetini denetlemek için belirli (tam) yayılmayı arıyorsanız, bkz. [örnekleme geçersiz kılmaları](./java-standalone-sampling-overrides.md).

## <a name="terminology"></a>Terminoloji

Telemetri işlemcileri hakkında bilgi vermeden önce, terim *aralığını* anlamalısınız. Yayılma, için genel bir terimdir:

* Gelen istek.
* Giden bağımlılığı (örneğin, başka bir hizmete uzak çağrı).
* İşlem içi bağımlılık (örneğin, hizmetin alt bileşenleri tarafından gerçekleştirilen iş).

Telemetri işlemcileri için bu yayılmış bileşenler önemlidir:

* Name
* Öznitelikler

Yayılma adı, Azure portal istekler ve Bağımlılıklar için birincil görüntüdir. Span öznitelikleri, belirli bir isteğin veya bağımlılığın hem standart hem de özel özelliklerini temsil eder.

## <a name="telemetry-processor-types"></a>Telemetri işlemcisi türleri

Şu anda iki tür telemetri işlemcisi, öznitelik işlemcileri ve yayılma işlemcidir.

Öznitelik işlemcisi özniteliği ekleyebilir, güncelleştirebilir, silebilir veya karma öznitelikleri içerebilir.
Ayrıca, varolan bir öznitelikten bir veya daha fazla yeni özniteliği ayıklamak için normal bir ifade kullanabilir.

Bir yayılma işlemcisi telemetri adını güncelleştirebilir.
Ayrıca, Aralık adından bir veya daha fazla yeni özniteliği ayıklamak için normal bir ifade kullanabilir.

> [!NOTE]
> Şu anda telemetri işlemcileri yalnızca dize türündeki öznitelikleri işler. Boole veya sayı türündeki öznitelikleri işlemez.

## <a name="getting-started"></a>Kullanmaya başlama

Başlamak için, *üzerindeapplicationinsights.js* adlı bir yapılandırma dosyası oluşturun. *ApplicationInsights-Agent- \* . jar* ile aynı dizine kaydedin. Aşağıdaki şablonu kullanın.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="include-criteria-and-exclude-criteria"></a>Ölçütleri ve dışlama ölçütlerini dahil et

Her iki öznitelik işlemcisi ve span işlemcisi isteğe bağlı `include` ve `exclude` ölçütleri destekler.
Bir işlemci yalnızca ölçütleriyle eşleşen yayılmalar için uygulanır `include` (sağlanmışsa) _ve_ `exclude` ölçütleriyle eşleşmez (sağlanmışsa).

Bu seçeneği `include` veya `exclude` (veya her ikisi) altında yapılandırmak için, en az bir `matchType` ve ya da `spanNames` belirtin `attributes` .
Include-exclude yapılandırması, belirtilen birden fazla koşula izin verir.
Belirtilen koşulların tümü, eşleşme ile sonuçlanmaları için true olarak değerlendirilmelidir. 

* **Gerekli alan**: `matchType` `spanNames` dizilerdeki ve `attributes` dizilerdeki öğelerin nasıl yorumlandığını denetler. Olası değerler şunlardır `regexp` `strict` . 

* **Isteğe bağlı alanlar**: 
    * `spanNames` öğelerin en az biriyle eşleşmesi gerekir. 
    * `attributes` eşleştirilecek özniteliklerin listesini belirtir. Bu özniteliklerin hepsi, eşleşme sonucu ile tam olarak eşleşmelidir.
    
> [!NOTE]
> Her ikisi `include` de `exclude` belirtilirse, Özellikler `include` denetlenme öncesinde Özellikler denetlenir `exclude` .

### <a name="sample-usage"></a>Örnek kullanım

```json
"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
Daha fazla bilgi için bkz. [telemetri işlemcisi örnekleri](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Öznitelik işlemcisi

Öznitelik işlemcisi bir yayılımın özniteliklerini değiştirir. Yayılmaları ekleme veya hariç tutma özelliğini destekleyebilir. Yapılandırma dosyasının belirttiği sırada gerçekleştirilen eylemlerin bir listesini alır. İşlemci şu eylemleri destekler:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

Bu `insert` eylem, anahtar bulunmayan yayılmalar içine yeni bir öznitelik ekler.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
`insert`Eylem aşağıdaki ayarları gerektirir:
* `key`
* `value`Ya da`fromAttribute`
* `action`: `insert`

### `update`

`update`Eylem, anahtarın zaten bulunduğu yayılmalar içindeki bir özniteliği güncelleştirir.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
`update`Eylem aşağıdaki ayarları gerektirir:
* `key`
* `value`Ya da`fromAttribute`
* `action`: `update`


### `delete` 

`delete`Eylem bir özniteliği bir yayılma alanından siler.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
`delete`Eylem aşağıdaki ayarları gerektirir:
* `key`
* `action`: `delete`

### `hash`

`hash`Eylem karmaları (SHA1) var olan bir öznitelik değeri.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
`hash`Eylem aşağıdaki ayarları gerektirir:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> `extract`Özelliği yalnızca Version 3.0.2 ve üzeri sürümlerde kullanılabilir.

`extract`Eylem, giriş anahtarından, kuralın belirttiği anahtarları hedeflemek için bir normal ifade kuralı kullanarak değerleri ayıklar. Bir hedef anahtar zaten varsa, geçersiz kılınır. Bu eylem [](#extract-attributes-from-the-span-name) `toAttributes` , var olan özniteliğin kaynak olduğu yayılma işlemcisi ayarı gibi davranır.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
`extract`Eylem aşağıdaki ayarları gerektirir:
* `key`
* `pattern`
* `action`: `extract`

Daha fazla bilgi için bkz. [telemetri işlemcisi örnekleri](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Yayılma işlemcisi

Yayılma işlemcisi, span adına göre bir yayılma alanının yayılma adını veya özniteliklerini değiştirir. Yayılmaları ekleme veya hariç tutma özelliğini destekleyebilir.

### <a name="name-a-span"></a>Bir yayılımı adlandırın

`name`Bölüm, ayarı gerektirir `fromAttributes` . Bu özniteliklerden değerler, yapılandırmanın belirttiği sırada birleştirilmiş yeni bir ad oluşturmak için kullanılır. İşlemci, yalnızca bu özniteliklerin hepsi yayılma üzerinde mevcutsa, span adını değiştirecek.

Bu `separator` ayar isteğe bağlıdır. Bu ayar bir dizedir. Değerleri ayırmak için belirtilir.
> [!NOTE]
> Yeniden adlandırma öznitelikleri değiştirmek için öznitelikler işlemcisini temel alıyorsa, işlem hattı belirtiminde özniteliklerin işlemcisinden sonra yayılma işlemcisinin belirtildiğinden emin olun.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-span-name"></a>Aralık adından öznitelikleri Ayıkla

`toAttributes`Bölüm, yayılma adıyla eşleşecek normal ifadeleri listeler. Öznitelikleri alt ifadelere göre ayıklar.

`rules`Ayar gereklidir. Bu ayar, Aralık adından öznitelik değerlerini ayıklamak için kullanılan kuralları listeler. 

Yayılma adındaki değerler ayıklanan öznitelik adlarıyla değiştirilmiştir. Listedeki her kural bir normal ifade (Regex) model dizesidir. 

Değerlerin ayıklanan öznitelik adlarıyla nasıl değiştirildiği aşağıda verilmiştir:

1. Yayılma adı, Regex ile denetlenir. 
1. Regex eşleşiyorsa, Regex 'nin adlandırılmış tüm alt ifadeleri öznitelik olarak ayıklanır. 
1. Ayıklanan öznitelikler, yayılmasına eklenir. 
1. Her alt ifade adı bir öznitelik adı olur. 
1. Eşleşen alt ifade, öznitelik değeri haline gelir. 
1. Yayılma adındaki eşleşen bölüm ayıklanan öznitelik adı ile değiştirilmiştir. Öznitelikler zaten yayılmakta mevcutsa, üzerine yazılır. 
 
Bu işlem, tüm kurallar belirtildikleri sırada yinelenir. Sonraki her kural, önceki kuralın çıktısı olan yayılma adı üzerinde kullanılır.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="common-span-attributes"></a>Ortak span öznitelikleri

Bu bölümde, telemetri işlemcilerin kullanabileceği bazı ortak Aralık öznitelikleri listelenmektedir.

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
