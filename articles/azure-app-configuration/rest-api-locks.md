---
title: Azure uygulama yapılandırma REST API-kilitler
description: Azure Uygulama Yapılandırması ' nı kullanarak anahtar-değer kilitleri ile çalışmaya yönelik başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932532"
---
# <a name="locks"></a>Kilitler

Bu API (sürüm 1,0) anahtar-değer kaynağı için kilit ve kilit açma semantiğini sağlar. Aşağıdaki işlemleri destekler:

- Kilit yerleştir
- Kilidi Kaldır

Varsa, `label` açık bir etiket değeri (joker karakter değil) olmalıdır. Tüm işlemler için bu, isteğe bağlı bir parametredir. Atlanırsa, etiket olmaz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Anahtar-değer kilitle

- Gerekli: ``{key}`` , ``{api-version}``  
- Seçim ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Lerinde**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Anahtar değeri yoksa, aşağıdaki yanıt döndürülür:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Anahtar-değer kilidini aç

- Gerekli: ``{key}`` , ``{api-version}``  
- Seçim ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Lerinde**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Anahtar değeri yoksa, aşağıdaki yanıt döndürülür:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>Koşullu kilit ve kilit açma

Yarış durumlarını engellemek için, `If-Match` veya `If-None-Match` istek üst bilgilerini kullanın. `etag`Bağımsız değişken, anahtar gösteriminin bir parçasıdır. `If-Match`Veya `If-None-Match` atlanırsa, işlem koşulsuz olur.

Aşağıdaki istek yalnızca geçerli anahtar-değer gösterimi belirtilen değerle eşleşiyorsa işlemi uygular `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Aşağıdaki istek yalnızca geçerli anahtar-değer gösterimi varsa ancak belirtilen değerle eşleşmiyorsa işlemi uygular `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
