---
title: Azure Uygulama yapılandırması REST API-Etiketler
description: Azure Uygulama Yapılandırması ' nı kullanarak etiketlerle çalışmak için başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932517"
---
# <a name="labels"></a>Etiketler

api sürümü: 1,0

**Etiket** kaynağı aşağıdaki gibi tanımlanır:

```json
{
      "name": [string]             // Name of the label
}
```

Aşağıdaki işlemleri destekler:

- Liste

Tüm işlemler için ``name`` isteğe bağlı bir filtre parametresidir. Atlanırsa, **herhangi bir** etiketi belirtir.

## <a name="prerequisites"></a>Önkoşullar

- Tüm HTTP isteklerinin kimliği doğrulanmalıdır. [Kimlik doğrulama](./rest-api-authentication-index.md) bölümüne bakın.
- Tüm HTTP isteklerinin açık sağlaması gerekir `api-version` . [Sürüm oluşturma](./rest-api-versioning.md) bölümüne bakın.

## <a name="list-labels"></a>Liste etiketleri

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Lerinde**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Sayfalandırma

Döndürülen öğe sayısı yanıt sınırını aşarsa, sonuç sayfalandırılır. İsteğe bağlı `Link` yanıt üst bilgilerini izleyin ve `rel="next"` gezinme için kullanın. Alternatif olarak, içerik özellik biçiminde bir sonraki bağlantı sağlar `@nextLink` . Sonraki bağlantı parametre içerir `api-version` .

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Yanıtıyla**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>Filtreleme

Filtreleme `name` desteklenir.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Desteklenen filtreler

|Anahtar filtresi|Etki|
|--|--|
|`name` atlanır veya `name=*`|**Herhangi bir** etiketle eşleşir|
|`name=abc`|**ABC** adlı bir etiketle eşleşir|
|`name=abc*`|**ABC** ile başlayan etiket adlarıyla eşleşir|
|`name=abc,xyz`|**ABC** veya **xyz** etiket adları Ile eşleşir (5 CSV ile sınırlıdır)|

### <a name="reserved-characters"></a>Ayrılmış karakterler

`*`, `\`, `,`

Ayrılmış bir karakter değerin bir parçasıysa, kullanılarak kaçışlı olması gerekir `\{Reserved Character}` . Ayrılmayan karakterlere de kaçışmış olabilir.

### <a name="filter-validation"></a>Filtre doğrulaması

Filtre doğrulama hatası oluşursa, yanıt `400` hata ayrıntılarına sahıp http olur:

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

### <a name="examples"></a>Örnekler

- Tümü

    ```http
    GET /labels?api-version={api-version}
    ```

- Etiket adı **ABC** ile başlar

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- Etiket adı **ABC** veya **xyz** 'dir

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Belirli alanlar iste

İsteğe bağlı `$select` sorgu dizesi parametresini kullanın ve istenen alanların virgülle ayrılmış listesini sağlayın. `$select`Parametresi atlanırsa, yanıt varsayılan kümesini içerir.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based erişim

Sonucun bir önceki zamanda bulunduğu gösterimi elde edin. Bkz. bölüm [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Yanıtıyla**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
