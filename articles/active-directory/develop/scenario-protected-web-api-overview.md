---
title: Korumalı Web API 'SI-genel bakış
titleSuffix: Microsoft identity platform
description: Korumalı bir Web API 'SI (genel bakış) oluşturmayı öğrenin.
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
ms.openlocfilehash: 64418138f9eff61cc94a57be4dc5e2625e5fdebb
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803675"
---
# <a name="scenario-protected-web-api"></a>Senaryo: korumalı Web API 'SI

Bu senaryoda, bir Web API 'sini nasıl kullanıma sunabileceğiniz ve API 'ye yalnızca kimliği doğrulanmış kullanıcıların erişebilmesi için nasıl koruyabileceğiniz gösterilmektedir. Web API 'nizi kullanmak için hem iş hem de okul hesaplarıyla kimliği doğrulanmış kullanıcıları veya kişisel Microsoft kişisel hesaplarını etkinleştirmek isteyeceksiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Özelliklerini

Web API 'Lerini korumak için bilmeniz gereken bazı ayrıntılar aşağıda verilmiştir:

- Uygulama kaydınız en az bir kapsam kullanıma sunmalıdır. Web API 'niz tarafından kabul edilen belirteç sürümü, hedef kitlede bulunan oturum açma durumuna bağlıdır.
- Web API 'SI için kodun yapılandırması, Web API 'SI çağrılırken kullanılan belirteci doğrulamalıdır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama kaydı](scenario-protected-web-api-app-registration.md)
