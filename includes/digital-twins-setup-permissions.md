---
author: baanders
description: Azure dijital TWINS kurulumu 'nda izin önkoşulu için dosya ekleme
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205511"
---
## <a name="prerequisites-permission-requirements"></a>Önkoşullar: Izin gereksinimleri

Bu makaledeki tüm adımları tamamlayabilmeniz için, aboneliğinizde aşağıdaki izinlere sahip bir [rolün](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) olması gerekir:
* Azure kaynaklarını oluşturma ve yönetme
* Azure kaynaklarına Kullanıcı erişimini yönetme (izin verme ve temsilci atama dahil)

Bu gereksinimi karşılayan ortak roller, *sahip*, *Hesap Yöneticisi* veya *Kullanıcı erişimi Yöneticisi* ile *katkıda bulunan* birleşimidir. Rol ve izinlerin, diğer rollere dahil edilen izinler dahil olmak üzere tüm açıklamaları için, Azure RBAC belgelerindeki [*Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD rolleri*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) ' ni ziyaret edin.

Aboneliğinizdeki rolü görüntülemek için Azure portal [abonelikler sayfasını](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ziyaret edin (Bu bağlantıyı kullanabilir veya Portal arama çubuğu ile *abonelikleri* arayabilirsiniz). Kullanmakta olduğunuz aboneliğin adını arayın *ve rolü rol sütununda görüntüleyin* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Kullanıcı sahip olarak gösteren Azure portal Abonelikler sayfasının görünümü" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Değerin *katkıda* bulunduğunu veya yukarıda açıklanan gerekli izinlere sahip olmayan başka bir rolü fark ederseniz, aboneliğinizde bu *izinlere sahip olan* kullanıcıyla (abonelik sahibi veya hesap yöneticisi gibi) başvurabilirsiniz ve aşağıdaki yollarla devam edebilirsiniz:
* Sizin adınıza bu makaledeki adımları tamamlamaları isteyin
* Kendinize devam etmek için izinleriniz olacak şekilde abonelikte rolünüzü yükseltirse. Bunun uygun olup olmadığı, kuruluşunuza ve onun içindeki rolünüze bağlıdır.