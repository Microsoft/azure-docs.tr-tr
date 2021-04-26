---
title: Web API 'Lerini çağıran bir Web uygulamasını kaydetme | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir Web uygulamasını nasıl kaydedeceğinizi öğrenin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bb9a1ca6c2c81e3b0d5dbeff06f4de012446cf79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756323"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Web API 'Leri çağıran bir Web uygulaması: uygulama kaydı

Web API 'Lerini çağıran bir Web uygulamasının, içindeki kullanıcıları imzaladığında bir Web uygulamasıyla aynı kaydı vardır. Bu nedenle, [kullanıcıları oturum açan bir Web uygulamasındaki yönergeleri izleyin: uygulama kaydı](scenario-web-app-sign-user-app-registration.md).

Ancak, Web uygulaması artık Web API 'Lerini de çağırdığı için gizli bir istemci uygulaması haline gelir. Bu nedenle bazı ek kayıt gereklidir. Uygulama, Microsoft Identity platformu ile istemci kimlik bilgilerini veya *gizli* dizileri paylaşmalıdır.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API izinleri

Web Apps, oturum açmış kullanıcı adına API 'Leri çağırır. Bunu yapmak için, *temsilci izinleri* istemeleri gerekir. Ayrıntılar için bkz. [Web API 'nize erişmek için Izin ekleme](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryonun [kod yapılandırması](scenario-web-app-call-api-app-configuration.md)olan bir sonraki makaleye geçin.
