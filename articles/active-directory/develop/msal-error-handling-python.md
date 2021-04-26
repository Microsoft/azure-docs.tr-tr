---
title: Python için MSAL.js’de hataları ve özel durumları işleme
titleSuffix: Microsoft identity platform
description: Python uygulamalarında hata ve özel durumları, koşullu erişim talep sorunlarını ve MSAL ' de yeniden denemeleri nasıl işleyeceğinizi öğrenin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761091"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Python için MSAL.js’de hataları ve özel durumları işleme

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Python için MSAL 'te hata işleme

Python için MSAL içinde, çoğu hata API çağrısından bir dönüş değeri olarak dağıtılır. Hata, Microsoft Identity platformundan gelen JSON yanıtını içeren bir sözlük olarak temsil edilir.

* Başarılı bir yanıt anahtarını içerir `"access_token"` . Yanıtın biçimi OAuth2 protokolü tarafından tanımlanır. Daha fazla bilgi için bkz. [5,1 başarılı yanıt](https://tools.ietf.org/html/rfc6749#section-5.1)
* Bir hata yanıtı içerir `"error"` ve genellikle `"error_description"` . Yanıtın biçimi OAuth2 protokolü tarafından tanımlanır. Daha fazla bilgi için bkz. [5,2 hata yanıtı](https://tools.ietf.org/html/rfc6749#section-5.2)

Bir hata döndürüldüğünde, `"error_description"` anahtar okunabilir bir ileti içerir; bu, genellikle bir Microsoft Identity platform hata kodu içerir. Çeşitli hata kodları hakkında daha fazla bilgi için bkz. [kimlik doğrulama ve yetkilendirme hata kodları](./reference-aadsts-error-codes.md).

Python için MSAL içinde, çoğu hata bir hata değeri döndürerek işlendiği için özel durumlar nadir bir durumdur. `ValueError`Özel durum yalnızca kitaplığı nasıl kullanmaya çalışırken (örneğin, API parametrelerinin yanlış biçimlendirilme) bir sorun olduğunda oluşturulur.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sorunları tanılamanıza ve hata ayıklamanıza yardımcı olmak üzere [Python IÇIN msal 'Te günlüğe kaydetmeyi](msal-logging-python.md) etkinleştirmeyi düşünün.
