---
title: Azure uygulama yapılandırma REST API-HMAC yetkilendirmesi
description: REST API kullanarak Azure Uygulama yapılandırmasına karşı yetkilendirme için HMAC kullanın
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932668"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC yetkilendirme-REST API başvurusu

HMAC kimlik doğrulaması kullanıldığında, işlemler iki kategoriden birine girer, okuma veya yazma. Okuma-yazma erişim anahtarları tüm işlemleri çağırmak için izin verir. Salt okuma erişim anahtarları yalnızca okuma işlemlerini çağırma izni verir. Bir erişim anahtarı salt okunurdur veya okuma yazma özelliği tarafından belirlenir `readOnly` . Salt okuma erişimli bir anahtarla yazma isteği oluşturma girişimleri, isteğin yetkisiz olarak oluşmasına neden olur.

## <a name="obtaining-access-keys"></a>Erişim anahtarları alma

Erişim anahtarlarını ve bunları elde etmek için kullanılan API 'YI açıklayan belirtim [buradaki](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json)Azure uygulama yapılandırma kaynak sağlayıcısı belirtiminde ayrıntılı olarak açıklanmıştır. Erişim tuşları "ConfigurationStores_ListKeys" işlemi yoluyla elde edilir.

## <a name="errors"></a>Hatalar

```http
HTTP/1.1 403 Forbidden
```

**Neden:** İsteğin kimliğini doğrulamak için kullanılan erişim anahtarı, istenen işlemi gerçekleştirmek için gerekli izinleri sağlamıyor.
**Çözüm:** İstenen işlemi gerçekleştirme izni sağlayan bir erişim anahtarı edinin ve isteğin kimliğini doğrulamak için onu kullanın.
