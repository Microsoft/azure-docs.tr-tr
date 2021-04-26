---
title: Azure Uygulama yapılandırması REST API-sürüm oluşturma
description: Azure Uygulama Yapılandırması ' nı kullanarak sürüm oluşturma için başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a869531860942e5a8b2b05212e778aca2170c89b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932430"
---
# <a name="versioning"></a>Sürüm Oluşturma

Her istemci isteğinin sorgu dizesi parametresi olarak açık bir API sürümü sağlaması gerekir. Örneğin: `https://{myconfig}.azconfig.io/kv?api-version=1.0`.

`api-version` SemVer (ana. Minor) biçiminde ifade edilir. Aralık veya sürüm anlaşması desteklenmez.

Bu makale, API sürüm 1,0 için geçerlidir.

Aşağıda, istenen API sürümü eşleştirilemezse sunucu tarafından döndürülen olası hata yanıtlarının Özeti özetlenmektedir.

## <a name="api-version-unspecified"></a>API sürümü belirtilmedi

Bu hata, bir istemci bir API sürümü sağlamadan istek yaptığında oluşur.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

## <a name="unsupported-api-version"></a>Desteklenmeyen API sürümü

Bu hata, bir istemci istenen API sürümü sunucu tarafından desteklenen API sürümlerinden hiçbiriyle eşleşmediği zaman oluşur.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="invalid-api-version"></a>Geçersiz API sürümü

Bu hata, bir istemci bir API sürümüyle istek yaptığında oluşur, ancak değer hatalı biçimlendirilmiş veya sunucu tarafından ayrıştırılamaz.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="ambiguous-api-version"></a>Belirsiz API sürümü

Bu hata, istemci sunucuya belirsiz bir API sürümü istediğinde (örneğin, birden çok farklı değer) oluşur.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
