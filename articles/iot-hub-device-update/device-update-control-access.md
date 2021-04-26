---
title: IoT Hub kimlik doğrulaması ve yetkilendirme için cihaz güncelleştirmesini anlama | Microsoft Docs
description: IoT Hub cihaz güncelleştirmesinin, kullanıcılar ve hizmet API 'Leri için kimlik doğrulama ve yetkilendirme sağlamak üzere Azure RBAC 'yi nasıl kullandığını anlayın.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ca55b1df347b47a6eb82557658d59a3de666b703
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558406"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Azure rol tabanlı erişim denetimi (RBAC) ve cihaz güncelleştirmesi

Cihaz güncelleştirme, kullanıcılar ve hizmet API 'Leri için kimlik doğrulama ve yetkilendirme sağlamak üzere Azure RBAC kullanır.

## <a name="configure-access-control-roles"></a>Erişim denetimi rollerini yapılandırma

Diğer kullanıcıların ve uygulamaların cihaz güncelleştirmesine erişimi olması için, kullanıcılara veya uygulamalara bu kaynağa erişim verilmesi gerekir. Cihaz güncelleştirmesi tarafından desteklenen roller şunlardır

|   Rol Adı   | Description  |
| :--------- | :---- |
|  Cihaz Güncelleştirme Yöneticisi | Tüm cihaz güncelleştirme kaynaklarına erişebilir  |
|  Cihaz güncelleştirme okuyucusu| Tüm güncelleştirmeleri ve dağıtımları görüntüleyebilir |
|  Cihaz güncelleştirme Içerik Yöneticisi | Güncelleştirmeleri görüntüleyebilir, alabilir ve silebilir  |
|  Cihaz güncelleştirme Içeriği okuyucusu | Güncelleştirmeleri görüntüleyebilir  |
|  Cihaz güncelleştirme dağıtımları Yöneticisi | , Cihazların güncelleştirmelerinin dağıtımını yönetebilir|
|  Cihaz güncelleştirme dağıtımları okuyucu| Cihazlarda güncelleştirmelerin dağıtımlarını görüntüleyebilir |

Rollerin bir birleşimi, doğru erişim düzeyini sağlamak için kullanılabilir. Örneğin, bir geliştirici cihaz güncelleştirme Içeriği yönetici rolünü kullanarak güncelleştirmeleri içeri aktarıp yönetebilir, ancak bir güncelleştirmenin ilerlemesini görüntülemek için bir cihaz güncelleştirme dağıtımları okuyucu rolü gerektirir. Buna karşılık, cihaz güncelleştirme okuyucusu rolüne sahip bir çözüm operatörü tüm güncelleştirmeleri görüntüleyebilir, ancak cihazlara belirli bir güncelleştirmeyi dağıtmak için cihaz güncelleştirme dağıtımları yönetici rolünü kullanması gerekir.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Cihaza kimlik doğrulama yayımlama ve yönetim için REST API 'Leri güncelleştirme

Cihaz güncelleştirme ayrıca kimlik doğrulaması için Azure AD 'yi kullanarak, hizmet API 'Leri aracılığıyla içerik yayımlayabilir ve yönetebilir. Başlamak için bir istemci uygulaması oluşturmanız ve yapılandırmanız gerekir.

### <a name="create-client-azure-ad-app"></a>İstemci Azure AD Uygulaması Oluştur

Bir uygulamayı veya hizmeti Azure AD ile bütünleştirmek için önce Azure AD ile bir uygulamayı [kaydedin](../active-directory/develop/quickstart-register-app.md) . İstemci uygulama kurulumu, kullanılan yetkilendirme akışına bağlı olarak farklılık gösterir.  Aşağıdaki yapılandırma, cihaz güncelleştirme REST API 'Leri kullanılırken rehberlik içindir.

* İstemci kimlik doğrulamasını ayarla: ' yerel veya Web istemcisi için URI 'Leri yeniden yönlendir '.
* API Izinlerini ayarla-IoT Hub için cihaz güncelleştirmesi kullanıma sunar:
  * Temsilci izinleri: ' user_impersonation '
  * **Isteğe bağlı**, yönetici onayı verme

[Sonraki adımlar: cihaz güncelleştirme kaynakları oluşturma ve erişim denetimi rollerini yapılandırma](./create-device-update-account.md)