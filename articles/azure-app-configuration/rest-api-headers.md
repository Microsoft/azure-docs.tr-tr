---
title: Azure Uygulama yapılandırması REST API-üst bilgiler
description: Azure Uygulama yapılandırması REST API kullanılan üstbilgiler için başvuru sayfaları
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932583"
---
# <a name="headers"></a>Üst Bilgiler

Bu makalede, Azure Uygulama yapılandırması REST API kullanılan üst bilgiler için başvuru sayfalarına bağlantılar sağlanmaktadır.

## <a name="request-headers"></a>İstek üst bilgileri

Aşağıdaki tabloda, Azure Uygulama yapılandırmasında kullanılan genel istek üstbilgileri açıklanmaktadır.

| Üst bilgi | Açıklama | Örnek |
|--|--|--|
| **Yetkilendirme** | Hizmete yönelik bir isteğin [kimliğini doğrulamak](./rest-api-authentication-index.md) için kullanılır. Bkz. [bölüm 14,8](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Kabul Etme** | Sunucuya, istemcinin bir HTTP yanıtında kabul edeceği medya türünü bildirir. Bkz. [bölüm 14,1](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-DateTime** | Sunucusunun içeriğini önceki durumunun bir temsili olarak döndürmesini ister. Bu üstbilginin değeri, bu durumun istenen tarih/saati değeridir. Bkz. [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **İçerik türü** | HTTP istek gövdesi içindeki içeriğin medya türünü içerir. Bkz. [bölüm 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Date** | HTTP isteğinin verildiği tarih/saat. Bu üst bilgi [HMAC kimlik doğrulamasında](./rest-api-authentication-hmac.md)kullanılır. Bkz. [bölüm 14,18](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Konak** | İsteğin verildiği kiracıyı belirtir. Bu üst bilgi [HMAC kimlik doğrulamasında](./rest-api-authentication-hmac.md)kullanılır. Bkz. [bölüm 14,23](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **IF-Match** | HTTP isteği koşullu oluşturmak için kullanılır. Bu istek yalnızca hedeflenen kaynağın ETag değeri bu üst bilginin değeriyle eşleşiyorsa başarılı olmalıdır. ' * ' Değeri herhangi bir ETag ile eşleşir. Bkz. [bölüm 14,24](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | HTTP isteği koşullu oluşturmak için kullanılır. Bu istek yalnızca hedeflenen kaynağın ETag değeri bu üst bilginin değeriyle eşleşmezse başarılı olmalıdır. ' * ' Değeri herhangi bir ETag ile eşleşir. Bkz. [bölüm 14,26](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-Token** | İstek dizisi sırasında gerçek zamanlı tutarlılığı etkinleştirmek için kullanılır. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-MS-Client-Request-ID** | İsteğin gidiş-gelişini izlemek için kullanılan, istemci tarafından belirtilen benzersiz bir KIMLIK. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-MS-Content-SHA256** | HTTP istek gövdesinin SHA256 Özeti. Bu üst bilgi [HMAC kimlik doğrulamasında](./rest-api-authentication-hmac.md)kullanılır. | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-MS-Tarih** | Bu üst bilgi, `Date` Tarih başlığına erişilebiliyorsa üst bilgi yerine ayarlanabilir ve kullanılabilir. Bu üst bilgi [HMAC kimlik doğrulamasında](./rest-api-authentication-hmac.md)kullanılır. | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-MS-Return-Client-Request-ID** | Üst bilgiyle birlikte kullanılır `x-ms-client-request-id` . Bu üstbilginin değeri ' true ' ise, sunucuya `x-ms-client-request-id` istek üst bilgisinin değerini döndürmesi istenir. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Yanıt Üst Bilgileri

Sunucu, yanıtlarına aşağıdaki HTTP üstbilgilerini dahil edebilir.

| Üst bilgi | Açıklama | Örnek |
|--|--|--|
| **İçerik türü** | HTTP yanıt gövdesi içindeki içeriğin medya türünü içerir. Bkz. [bölüm 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Özelliği** | Belirli bir kaynağın durumunu temsil eden donuk bir belirteç. , Koşullu işlemlerde kullanılabilir. Bkz. [bölüm 14,19](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Son değiştirme** | İstenen kaynağın en son ne zaman değiştirildiğini açıklar. [Http-Date](https://tools.ietf.org/html/rfc2616#section-3.3.1)olarak biçimlendirilir. Bkz. [bölüm 14,29](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Bağlantı** | Yanıtla ilgili kaynaklara bağlantılar sağlar. Bu üst bilgi, _sonraki_ bağlantı kullanılarak sayfalama için kullanılır. Bkz. [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Memento-DateTime** | Yanıtta bulunan içeriğin önceki bir durumu temsil ettiğini belirtir. Bu üstbilginin değeri bu durumun tarih/saati olur. Bkz. [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **yeniden deneme-MS-After** | İstemcinin başarısız bir isteği yeniden denemeden önce bekleyeceği önerilen bir süre (milisaniye cinsinden) sağlar. | `retry-after-ms: 10` |
| **x-MS-istek kimliği** | Hizmet içinde isteği izlemek için kullanılan, sunucu tarafından oluşturulan benzersiz bir KIMLIK. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-kimlik doğrulama** | Kimlik doğrulaması için istemcileri denemek ve bir kimlik doğrulama denemesinin neden başarısız olduğuna ilişkin bir neden sağlamak için kullanılır. Bkz. [bölüm 14,47](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
