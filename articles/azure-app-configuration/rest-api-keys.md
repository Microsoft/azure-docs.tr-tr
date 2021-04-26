---
title: Azure Uygulama yapılandırması REST API anahtarları
description: Azure Uygulama yapılandırması 'nı kullanarak anahtarlar ile çalışmaya yönelik başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932549"
---
# <a name="keys"></a>Anahtarlar

api sürümü: 1,0

Aşağıdaki sözdizimi bir anahtar kaynağını temsil eder:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operations

Anahtar kaynakları aşağıdaki işlemi destekler:

- Liste

Tüm işlemler için `name` isteğe bağlı bir filtre parametresidir. Atlanırsa, *herhangi bir* anahtar gerekir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Anahtarları Listele

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Lerinde**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Sayfalandırma

Döndürülen öğe sayısı yanıt sınırını aşarsa, sonuç sayfalandırılır. İsteğe bağlı `Link` yanıt üst bilgilerini izleyin ve `rel="next"` gezinme için kullanın. Alternatif olarak, içerik özellik biçiminde bir sonraki bağlantı sağlar `@nextLink` . Sonraki bağlantı parametre içerir `api-version` .

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Yanıtıyla**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

Filtreleme ```name``` desteklenir.

```http
GET /keys?name={key-name}&api-version={api-version}
```

Aşağıdaki filtreler desteklenir:

|Anahtar filtresi|Etki|
|--|--|
|`name` atlanır veya `name=*`|**Herhangi bir** anahtarla eşleşir|
|`name=abc`|**ABC** adlı bir anahtarla eşleşir|
|`name=abc*`|**ABC** ile başlayan anahtar adlarını eşleştirir|
|`name=abc,xyz`|**ABC** veya **xyz** anahtar adları Ile eşleşir (5 CSV ile sınırlıdır)|

Aşağıdaki karakterler ayrılmıştır: `*` , `\` , `,`

Ayrılmış bir karakter değerin bir parçasıysa, kullanılarak kaçışlı olması gerekir `\{Reserved Character}` . Ayrılmayan karakterlere de kaçışmış olabilir.

## <a name="filter-validation"></a>Filtre doğrulaması

Filtre doğrulama hatası durumunda, yanıt `400` hata ayrıntılarına sahıp http ' dir:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

## <a name="examples"></a>Örnekler

- Tümü

    ```http
    GET /keys?api-version={api-version}
    ```

- Anahtar adı **ABC** ile başlar

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- Anahtar adı **ABC** veya **xyz** 'dir

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Belirli alanlar iste

İsteğe bağlı `$select` sorgu dizesi parametresini kullanın ve istenen alanların virgülle ayrılmış listesini sağlayın. `$select`Parametresi atlanırsa, yanıt varsayılan kümesini içerir.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based erişim

Sonucun bir önceki zamanda bulunduğu gösterimi elde edin. Bkz. bölüm [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Yanıtıyla**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
