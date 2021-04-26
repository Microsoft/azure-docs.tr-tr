---
title: Azure Uygulama yapılandırması REST API-anahtar-değer düzeltmeleri
description: Azure Uygulama Yapılandırması ' nı kullanarak anahtar-değer düzeltmeleri ile çalışmaya yönelik başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cfa117d1ed017170c279b7c4e0a146ae4edac108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932481"
---
# <a name="key-value-revisions"></a>Anahtar-değer düzeltmeleri

*Anahtar-değer düzeltmesi* , anahtar-değer kaynağının geçmiş gösterimini tanımlar. Düzeltmeler ücretsiz katman depoları için 7 gün sonra veya Standart katman depoları için 30 gün sonra sona erer. Düzeltmeler işlemi destekler `List` .

Tüm işlemler için ``key`` isteğe bağlı bir parametredir. Atlanırsa, herhangi bir anahtar gerekir.

Tüm işlemler için ``label`` isteğe bağlı bir parametredir. Atlanırsa, herhangi bir etiketi belirtir.

Bu makale, API sürüm 1,0 için geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Düzeltmeleri listele

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Lerinde**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Sayfalandırma

Döndürülen öğe sayısı yanıt sınırını aşarsa, sonuç sayfalandırılır. İsteğe bağlı ``Link`` yanıt üst bilgisini izleyin ve ``rel="next"`` gezinme için kullanın. Alternatif olarak, içerik özellik biçiminde bir sonraki bağlantı sağlar ``@nextLink`` .

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Yanıtıyla**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>Düzeltmelerin alt kümesini Listele

`Range`İstek üst bilgisini kullanın. Yanıt, bir `Content-Range` üst bilgisi içeriyor. Sunucu istenen aralığı karşılayamaz, HTTP () ile yanıt verir `416` `RangeNotSatisfiable` .

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Response**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filtreleme

`key`Ve filtrelemenin bir birleşimi `label` desteklenir.
İsteğe bağlı `key` ve `label` sorgu dizesi parametrelerini kullanın.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Desteklenen filtreler

|Anahtar filtresi|Etki|
|--|--|
|`key` atlanır veya `key=*`|**Herhangi bir** anahtarla eşleşir|
|`key=abc`|**ABC** adlı bir anahtarla eşleşir|
|`key=abc*`|**ABC** ile başlayan anahtar adlarını eşleştirir|
|`key=*abc`|**ABC** ile biten anahtar adlarını eşleştirir|
|`key=*abc*`|**ABC** içeren anahtar adlarını eşleştirir|
|`key=abc,xyz`|**ABC** veya **xyz** anahtar adları Ile eşleşir (5 CSV ile sınırlıdır)|

|Etiket Filtresi|Etki|
|--|--|
|`label` atlanır veya `label=`|Etiketi olmayan girişle eşleşiyor|
|`label=*`|**Herhangi bir** etiketle eşleşir|
|`label=prod`|**Üretim** etiketi ile eşleşir|
|`label=prod*`|**Üretim** ile başlayan etiketlerle eşleşir|
|`label=*prod`|**Üretim** ile biten etiketlerle eşleşir|
|`label=*prod*`|**Üretim** içeren etiketleri eşleştirir|
|`label=prod,test`|Etiketler **Üretim** veya **Test** Ile eşleşir (5 CSV ile sınırlıdır)|

### <a name="reserved-characters"></a>Ayrılmış karakterler

Ayrılan karakterler şunlardır:

`*`, `\`, `,`

Ayrılmış bir karakter değerin bir parçasıysa, kullanılarak kaçışılması gerekir `\{Reserved Character}` . Ayrılmayan karakterlere de kaçışmış olabilir.

### <a name="filter-validation"></a>Filtre doğrulaması

Filtre doğrulama hatası oluşursa, yanıt `400` hata ayrıntılarına sahıp http olur:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Örnekler

- Bütün

    ```http
    GET /revisions
    ```

- Anahtar adının **ABC** ile başladığı öğeler:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Anahtar adının **ABC** ya da **xyz** olduğu ve Etiketler **Üretim**'nin bulunduğu öğeler:

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Belirli alanlar iste

İsteğe bağlı `$select` sorgu dizesi parametresini kullanın ve istenen alanların virgülle ayrılmış bir listesini sağlayın. `$select`Parametresi atlanırsa, yanıt varsayılan kümesini içerir.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Zamana dayalı erişim

Sonucun bir önceki zamanda bulunduğu gösterimi elde edin. Daha fazla bilgi için bkz. [kaynak durumlarına Time-Based erişim Için HTTP çerçevesi--Memento](https://tools.ietf.org/html/rfc7089#section-2.1), Section 2.1.1.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Yanıtıyla**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
