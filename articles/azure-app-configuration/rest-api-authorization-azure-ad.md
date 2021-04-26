---
title: Azure uygulama yapılandırma REST API-Azure Active Directory yetkilendirme
description: REST API kullanarak Azure Uygulama yapılandırmasına karşı yetkilendirme için Azure Active Directory kullanın
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092801"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory yetkilendirmesi-REST API başvurusu

Azure Active Directory (Azure AD) kimlik doğrulaması kullandığınızda, yetkilendirme rol tabanlı erişim denetimi (RBAC) tarafından işlenir. RBAC, kaynaklara erişim izni vermek için kullanıcılardan rollere atanmasını gerektirir. Her rol, role atanan kullanıcıların gerçekleştirebilmesini sağlayan bir dizi eylem içerir.

## <a name="roles"></a>Roller

Aşağıdaki roller varsayılan olarak Azure aboneliklerinde kullanılabilir:

- **Azure Uygulama yapılandırması veri sahibi**: Bu rol, tüm işlemlere tam erişim sağlar.
- **Azure uygulama yapılandırma veri okuyucusu**: Bu rol, okuma işlemlerine izin vermez.

## <a name="actions"></a>Eylemler

Roller, bu role atanan kullanıcıların gerçekleştirebileceği eylemlerin bir listesini içerir. Azure Uygulama yapılandırması aşağıdaki eylemleri destekler:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Bu eylem, uygulama yapılandırması anahtar-değer kaynaklarına okuma erişimi sağlar, örneğin/kV ve/Labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Bu eylem, uygulama yapılandırma anahtar-değer kaynaklarına yazma erişimine izin verir.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Bu eylem, uygulama yapılandırma anahtar-değer kaynaklarının silinmesine izin verir. Bir kaynağın silinmesinin, silinen anahtar değerini döndürdüğünü unutmayın.

## <a name="error"></a>Hata

```http
HTTP/1.1 403 Forbidden
```

**Neden:** İsteği yapan asıl işlem, istenen işlemi gerçekleştirmek için gerekli izinlere sahip değil.
**Çözüm:** İstenen işlemi gerçekleştirmek için gereken rolü, isteği yapan sorumluya atayın.

## <a name="managing-role-assignments"></a>Rol atamalarını yönetme

Rol atamalarını, tüm Azure hizmetlerinde standart olan [Azure RBAC yordamlarını](../role-based-access-control/overview.md) kullanarak yönetebilirsiniz. Bunu Azure CLı, PowerShell ve Azure portal aracılığıyla yapabilirsiniz. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md).