---
title: Aşağı akış Web API 'Lerini çağıran Web API 'SI (genel bakış)-Microsoft Identity platform
description: Aşağı akış Web API 'Lerini (genel bakış) çağıran bir Web API 'SI oluşturmayı öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852491"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Senaryo: Web API'lerini çağıran web API'si

Web API 'Lerini çağıran bir Web API 'SI oluşturmak için ihtiyacınız olan her şey hakkında bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

Web API 'Lerini çağıran korumalı Web API 'SI olan bu senaryo, "bir Web API 'sini koruma" senaryosunun üzerine oluşturulur. Bu temel senaryo hakkında daha fazla bilgi edinmek için önce [Protected Web API-Scenario](scenario-protected-web-api-overview.md) bölümüne bakın.

## <a name="overview"></a>Genel Bakış

- Bir istemci (Web, Masaüstü, mobil veya tek sayfalı uygulama)-aşağıdaki diyagramda temsil edilmeyen korumalı bir Web API 'sini çağırır ve "Authorization" http üstbilgisinde bir JWT taşıyıcı belirteci sağlar.
- Korunan Web API 'si, belirteci doğrular ve (Azure AD `AcquireTokenOnBehalfOf` 'den) Kullanıcı adına ikinci bir Web API 'si (aşağı akış Web API 'si olarak adlandırılır) çağırmak üzere başka bir belirteç istemek için msal yöntemini kullanır.
- Korumalı Web API 'si, bir aşağı akış API 'SI çağırmak için bu belirteci kullanır. Ayrıca, diğer aşağı `AcquireTokenSilent`akış API 'leri için belirteçleri istemek üzere daha sonra çağırabilir (ancak aynı kullanıcı adına devam edebilir). `AcquireTokenSilent`gerektiğinde belirteci yeniler.

![Web API 'SINI çağıran Web API 'SI](media/scenarios/web-api.svg)

## <a name="specifics"></a>Özelliklerini

API izinleriyle ilgili uygulama kaydının bir parçası klasik olarak görüntülenir. Uygulama yapılandırması, JWT taşıyıcı belirtecini bir aşağı akış API 'SI belirtecine göre değiştirmek için OAuth 2,0 adına sahip akış kullanımını içerir. Bu belirteç, Web API 'sinin denetleyicilerinde kullanılabildiği belirteç önbelleğine eklenir ve aşağı akış API 'Lerini çağırmak için sessizce bir belirteç alabilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama kaydı](scenario-web-api-call-api-app-registration.md)
