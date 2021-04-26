---
title: Web API 'Lerini çağıran Daemon uygulamalarını kaydetme-Microsoft Identity platform | Mavisi
description: Web API 'Leri çağıran bir Daemon uygulaması oluşturmayı öğrenin-uygulama kaydı
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 938a19276839d5e0d2bd3e0244510fc068cb029c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582916"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Web API 'Lerini çağıran Daemon uygulaması-uygulama kaydı

Bir Daemon uygulaması için uygulamayı kaydettiğinizde bilmeniz gerekenler aşağıda verilmiştir.

## <a name="supported-account-types"></a>Desteklenen hesap türleri

Daemon uygulamaları yalnızca Azure AD kiracılarında anlamlı hale getirir. Bu nedenle, uygulamayı oluştururken aşağıdaki seçeneklerden birini seçin:

- **Yalnızca bu kuruluş dizinindeki hesaplar**. Daemon uygulamaları genellikle iş kolu (LOB) geliştiricileri tarafından yazıldığı için bu seçim en yaygın bir seçenektir.
- **Herhangi bir kuruluş dizinindeki hesaplar**. Müşterileriniz için bir yardımcı program aracı sağlayan bir ISV 'niz varsa, bu seçimi yaparsınız. Müşterilerinizin kiracı yöneticilerinin onayını onaylaması gerekir.

## <a name="authentication---no-reply-uri-needed"></a>Kimlik doğrulama-yanıt URI 'SI gerekli değil

Gizli istemci uygulamanızın *yalnızca* istemci kimlik bilgileri akışını kullanması durumunda, yanıt URI 'sinin kayıtlı olması gerekmez. Uygulama yapılandırması veya oluşturulması için gerekli değildir. İstemci kimlik bilgileri akışı bunu kullanmaz.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API izinleri-uygulama izinleri ve yönetici onayı

Bir Daemon uygulaması, API 'lere yalnızca uygulama izinleri isteyebilir (temsilci izinleri yoktur). Uygulama kaydı için **API izinleri** sayfasında, **izin Ekle** ' yi seçtikten sonra API ailesini seçtikten sonra **Uygulama izinleri**' ni seçin ve ardından izinlerinizi seçin.

![Uygulama izinleri ve yönetici onayı](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Çağırmak istediğiniz Web API 'sinin, temsilci izinleri değil *Uygulama izinleri (uygulama rolleri)* tanımlamasına ihtiyacı vardır. Böyle bir API 'yi kullanıma sunma hakkında daha fazla bilgi için bkz. [Protected Web API: uygulama kaydı-Web API 'niz bir Daemon uygulaması tarafından çağrıldığında](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Daemon uygulamaları, kiracı yöneticisinin Web API 'sini çağıran uygulamaya ön onayını gerektirir. Kiracı yöneticileri, ***kuruluşumuza* yönetici izni ver** ' i seçerek aynı **API izin** sayfasında bu onayı sağlar

Çok kiracılı bir uygulama için bir ISV oluşturuyorsanız, [çok kiracılı Daemon uygulamalarının dağıtım durumunu](scenario-daemon-production.md#deployment---multitenant-daemon-apps)okumanız gerekir.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryonun [uygulama kodu yapılandırması](./scenario-daemon-app-configuration.md)olan bir sonraki makaleye geçin.
