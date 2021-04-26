---
title: Web API 'Lerini çağıran bir Web API 'sini kaydetme | Mavisi
titleSuffix: Microsoft identity platform
description: Aşağı akış Web API 'Lerini (uygulama kaydı) çağıran bir Web API 'SI oluşturmayı öğrenin.
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
ms.openlocfilehash: af1047c5f890b1b88ae6d043a30704e84b8dc079
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584323"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Web API 'Lerini çağıran bir Web API 'SI: uygulama kaydı

Aşağı akış Web API 'Lerini çağıran bir Web API 'si, korumalı bir Web API 'SI ile aynı kayda sahiptir. [Korumalı Web API 'si: uygulama kaydı](scenario-protected-web-api-app-registration.md)' nda bulunan yönergeleri izleyin.

Web uygulaması artık Web API 'Lerini çağırdığı için gizli bir istemci uygulaması haline gelir. Ek kayıt bilgileri de gereklidir: uygulamanın, Microsoft Identity platformu ile gizli dizileri (istemci kimlik bilgileri) paylaşması gerekir.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API izinleri

Web Apps, taşıyıcı belirtecinin alındığı Kullanıcı adına API 'Leri çağırır. Web uygulamalarının temsilci izinleri istemesi gerekir. Daha fazla bilgi için bkz. [Web API 'nize erişmek için Izin ekleme](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryonun [uygulama kodu yapılandırması](scenario-web-api-call-api-app-configuration.md)olan bir sonraki makaleye geçin.
