---
title: Azure Uygulama yapılandırması REST API-anahtar-değer
description: Azure Uygulama Yapılandırması ' nı kullanarak anahtar değerleriyle çalışmaya yönelik başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 95081d6f8c2770d01f7836e08b6851860bf47ba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932566"
---
# <a name="key-values"></a>Anahtar değerler

Anahtar değeri, benzersiz birleşimi tarafından tanımlanan bir kaynaktır `key`  +  `label` . `label` isteğe bağlıdır. Anahtar-değer etiketi olmadan açık bir şekilde başvurmak için "\ 0" (URL kodlamalı ``%00`` ) kullanın. Her işlemin ayrıntılarına bakın.

Bu makale, API sürüm 1,0 için geçerlidir.

## <a name="operations"></a>Operations

- Al
- Birden çok Listele
- Ayarla
- Sil

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Syntax

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Anahtar-değer al

Gerekli: ``{key}`` , ``{api-version}``  
İsteğe bağlı: ``label`` (atlanırsa, etiket olmadan bir anahtar-değer anlamına gelir.)

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Lerinde**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Anahtar yoksa, aşağıdaki yanıt döndürülür:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Al (koşullu)

İstemci önbelleğini geliştirmek için, `If-Match` veya `If-None-Match` istek üst bilgilerini kullanın. `etag`Bağımsız değişken, anahtar gösteriminin bir parçasıdır. Daha fazla bilgi için bkz. [bölümler 14,24 ve 14,26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Aşağıdaki istek, yalnızca geçerli gösterim belirtilen değerle eşleşmiyorsa anahtar değerini alır `etag` :

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Lerinde**

```http
HTTP/1.1 304 NotModified
```

veya

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Anahtar değerlerini listeleme

İsteğe bağlı: ``key`` (belirtilmemişse, herhangi bir anahtar anlamına gelir.) İsteğe bağlı: ``label`` (belirtilmemişse, herhangi bir etiketi gösterir.)

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Yanıtıyla**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

Ek seçenekler için, bu makalenin devamındaki "filtreleme" bölümüne bakın.

## <a name="pagination"></a>Sayfalandırma

Döndürülen öğe sayısı yanıt sınırını aşarsa, sonuç sayfalandırılır. İsteğe bağlı `Link` yanıt üst bilgilerini izleyin ve `rel="next"` gezinme için kullanın.
Alternatif olarak, içerik özellik biçiminde bir sonraki bağlantı sağlar `@nextLink` . Bağlı URI, `api-version` bağımsız değişkenini içerir.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Yanıtıyla**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
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

## <a name="filtering"></a>Filtreleme

`key`Ve filtrelemenin bir birleşimi `label` desteklenir.
İsteğe bağlı `key` ve `label` sorgu dizesi parametrelerini kullanın.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Desteklenen filtreler

|Anahtar filtresi|Etki|
|--|--|
|`key` atlanır veya `key=*`|**Herhangi bir** anahtarla eşleşir|
|`key=abc`|**ABC** adlı bir anahtarla eşleşir|
|`key=abc*`|**ABC** ile başlayan anahtar adlarını eşleştirir|
|`key=abc,xyz`|**ABC** veya **xyz** anahtar adları Ile eşleşir (5 CSV ile sınırlıdır)|

|Etiket Filtresi|Etki|
|--|--|
|`label` atlanır veya `label=*`|**Herhangi bir** etiketle eşleşir|
|`label=%00`|Etiketi olmayan KV ile eşleşir|
|`label=prod`|**Üretim** etiketi ile eşleşir|
|`label=prod*`|**Üretim** ile başlayan etiketlerle eşleşir|
|`label=prod,test`|Etiketler **Üretim** veya **Test** Ile eşleşir (5 CSV ile sınırlıdır)|

***Ayrılmış karakterler***

`*`, `\`, `,`

Ayrılmış bir karakter değerin bir parçasıysa, kullanılarak kaçışılması gerekir `\{Reserved Character}` . Ayrılmayan karakterlere de kaçışmış olabilir.

***Filtre doğrulaması***

Filtre doğrulama hatası durumunda, yanıt `400` hata ayrıntılarına sahıp http ' dir:

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

**Örnekler**

- Tümü

    ```http
    GET /kv?api-version={api-version}
    ```

- Anahtar adı **ABC** ile başlar ve tüm etiketleri içerir

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- Anahtar adı **ABC** ve etiket eşittir **v1** veya **v2** ile başlar

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Belirli alanlar iste

İsteğe bağlı `$select` sorgu dizesi parametresini kullanın ve istenen alanların virgülle ayrılmış bir listesini sağlayın. `$select`Parametresi atlanırsa, yanıt varsayılan kümesini içerir.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Zamana dayalı erişim

Sonucun bir önceki zamanda bulunduğu gösterimi elde edin. Daha fazla bilgi için bkz. Section [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). Sayfalandırma hala bu makalede daha önce tanımlanan şekilde desteklenmektedir.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Yanıtıyla**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
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

## <a name="set-key"></a>Anahtar ayarla

- Gerekli: ``{key}``
- İsteğe bağlı: ``label`` (belirtilmemişse veya etiket = %00, etiket olmadan anahtar değeri gerektirir.)

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Lerinde**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Öğe kilitliyse, aşağıdaki yanıtı alırsınız:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Anahtar ayarla (koşullu)

Yarış durumlarını engellemek için, `If-Match` veya `If-None-Match` istek üst bilgilerini kullanın. `etag`Bağımsız değişken, anahtar gösteriminin bir parçasıdır.
`If-Match`Veya `If-None-Match` atlanırsa, işlem koşulsuz olur.

Aşağıdaki yanıt, yalnızca geçerli temsil belirtilen ile eşleşiyorsa değeri güncelleştirir `etag` :

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Aşağıdaki yanıt, yalnızca geçerli temsil belirtilen ile eşleşmezse değeri güncelleştirir `etag` :

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Aşağıdaki istek yalnızca bir gösterim zaten varsa değeri ekler:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

Aşağıdaki istek yalnızca bir gösterim mevcut değilse değeri ekler:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Yanıtlar**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

veya

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Sil

- Gerekli: `{key}` , `{api-version}`
- İsteğe bağlı: `{label}` (belirtilmemişse veya etiket = %00, etiket olmadan anahtar değeri gerektirir.)

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Yanıt:** Silinen anahtar değerini, anahtar değeri yoksa None döndürün.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

veya

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Anahtarı sil (koşullu)

Bu makalenin önceki kısımlarında "anahtar ayarlama (koşullu)" bölümüne benzer.
