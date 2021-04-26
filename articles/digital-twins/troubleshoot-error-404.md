---
title: 'Azure dijital TWINS isteği şu durumla başarısız oldu: 404 Sub-Domain bulunamadı'
description: "' Hizmet isteği için nedenler ve çözümler başarısız oldu. Durum: 404 Sub-Domain bulunamadı ' on Azure Digital TWINS."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590341"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>Hizmet isteği başarısız oldu. Durum: 404 Sub-Domain bulunamadı

Bu makalede, hizmet isteklerinden Azure dijital TWINS 'e 404 hatası almaya yönelik nedenler ve çözüm adımları açıklanır. 

## <a name="symptoms"></a>Belirtiler

Bu hata, örnekten farklı bir [Azure Active Directory (Azure AD) kiracısına](../active-directory/develop/quickstart-create-new-tenant.md) ait bir hizmet sorumlusu veya Kullanıcı hesabı kullanılarak bir Azure dijital TWINS örneğine erişilirken meydana gelebilir. Doğru [Roller](concepts-security.md) kimliğe atanmış görünüyor, ancak API istekleri hata durumuyla başarısız oluyor `404 Sub-Domain not found` .

## <a name="causes"></a>Nedenler

### <a name="cause-1"></a>Neden #1

Azure dijital TWINS, tüm kimlik doğrulama kullanıcılarının Azure dijital TWINS örneğiyle aynı Azure AD kiracısına ait olmasını gerektirir.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Çözümler

### <a name="solution-1"></a>Çözüm #1

Bu sorunu, başka bir kiracıya ait her bir federal kimliğin Azure dijital TWINS örneğinin "giriş" kiracısından bir **belirteç** istemesi durumunda çözebilirsiniz. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>Çözüm #2

`DefaultAzureCredential`Kodunuzda sınıfını kullanıyorsanız ve bir belirteci aldıktan sonra bu sorunla karşılaşmaya devam ederseniz, `DefaultAzureCredential` kimlik doğrulaması varsayılan olarak diğer bir türe eşit olduğunda bile kiracı 'yı açıklığa kavuşturmak için giriş kiracısını belirtebilirsiniz.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure dijital TWINS üzerinde güvenlik ve izinler hakkında daha fazla bilgi edinin:
* [*Kavramlar: Azure dijital TWINS çözümleri için güvenlik*](concepts-security.md)
