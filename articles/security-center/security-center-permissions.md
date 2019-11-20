---
title: Azure Güvenlik Merkezi 'ndeki izinler | Microsoft Docs
description: Bu makalede, Azure Güvenlik Merkezi 'nin kullanıcılara izinler atamak ve her bir rol için izin verilen eylemleri tanımlamak için rol tabanlı erişim denetimi nasıl kullanıldığı açıklanmaktadır.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: f52d518d2ed1dfb62eed72cf9c0b839a37b7f856
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201647"
---
# <a name="permissions-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde izinler

Azure Güvenlik Merkezi, Azure'daki kullanıcılara, gruplara ve hizmetlere atanabilen [yerleşik roller](../role-based-access-control/built-in-roles.md) sağlayan [Rol Tabanlı Erişim Denetimi'ni (RBAC)](../role-based-access-control/role-assignments-portal.md) kullanır.

Güvenlik Merkezi, güvenlik sorunlarını ve güvenlik açıklarını tanımlamak amacıyla kaynaklarınızın yapılandırmasını değerlendirir. Güvenlik Merkezi'nde, yalnızca bir kaynağa sahip, katkıda bulunan veya okuyucu rolü bir kaynağın ait olduğu kaynak grubu veya abonelik atandığında ilgili bilgiler görürsünüz.

Bu rollere ek olarak iki özel Güvenlik Merkezi rolü vardır:

* **Güvenlik okuyucusu**: Bu role ait olan bir Kullanıcı güvenlik merkezi 'ne yönelik hakları görüntülüyor. Kullanıcı önerileri, uyarıları, güvenlik ilkesini ve güvenlik durumlarını görüntüleyebilir, ancak değişiklik yapamaz.
* **Güvenlik Yöneticisi**: Bu role ait olan bir Kullanıcı güvenlik okuyucuyla aynı haklara sahiptir ve ayrıca güvenlik ilkesini güncelleştirebilir ve uyarıları ve önerileri kapatabilir.

> [!NOTE]
> Güvenlik, güvenlik okuyucusu ve Güvenlik Yöneticisi güvenlik rollerinin yalnızca güvenlik merkezi 'ne erişimi vardır. Güvenlik rollerinin, Azure 'un depolama, Web & Mobile veya Nesnelerin İnterneti gibi diğer hizmet bölümlerine erişimi yoktur.
>
>

## <a name="roles-and-allowed-actions"></a>Roller ve izin verilen eylemler

Aşağıdaki tabloda Güvenlik Merkezi 'nde roller ve izin verilen eylemler görüntülenmektedir. X, bu rol için eyleme izin verildiğini gösterir.

| Role | Güvenlik ilkesini Düzenle | Bir kaynak için güvenlik önerilerini uygulama | Uyarıları ve önerileri kapat | Uyarıları ve önerileri görüntüleme |
|:--- |:---:|:---:|:---:|:---:|
| Abonelik sahibi | X | X | X | X |
| Abonelik Katılımcısı | -- | X | X | X |
| Kaynak grubu sahibi | -- | X | -- | X |
| Kaynak grubu Katılımcısı | -- | X | -- | X |
| Okuyucu | -- | -- | -- | X |
| Güvenlik Yöneticisi | X | -- | X | X |
| Güvenlik Okuyucusu | -- | -- | -- | X |

> [!NOTE]
> Kullanıcılara, görevlerini tamamlamak için gereken rolleri en alt seviyede esneklik sunacak şekilde atamanızı öneririz. Örneğin, okuyucu rolünü yalnızca bir kaynağın güvenlik durumu ile ilgili bilgileri görüntülemesi gereken ancak öneri veya ilkeleri Düzenle gibi işlemleri olmayan kullanıcılara atayın.
>
>

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede Güvenlik Merkezi 'nin kullanıcılara izinler atamak ve her bir rol için izin verilen eylemleri tanımlanması açıklanmaktadır. Artık aboneliğinizin güvenlik durumunu izlemek, güvenlik ilkelerini düzenlemek ve öneriler uygulamak için gereken rol atamalarıyla ilgili bilgi sahibi olduğunuza göre şunları yapın:

- [Güvenlik Merkezi 'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md)
- [Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md)
- [Azure kaynaklarınızın güvenlik durumunu izleme](security-center-monitoring.md)
- [Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)
- [İş ortağı güvenlik çözümlerini izleme](security-center-partner-solutions.md)
