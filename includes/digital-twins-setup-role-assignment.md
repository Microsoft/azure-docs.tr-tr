---
author: baanders
description: Azure Digital TWINS kurulumu 'nda erişim izinleri için dosya ekleme adımı
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: a905bb3b4effb0381facfbfaa37c8ea412b81287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100551632"
---
Azure dijital TWINS, rol tabanlı erişim denetimi (RBAC) için [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) kullanır. Bu, bir Kullanıcı Azure dijital TWINS örneğiniz için veri düzlemi çağrısı yapmadan önce, söz konusu kullanıcının bu kullanıcı için uygun izinlere sahip bir rol atanması gerektiğini gösterir.

Azure dijital TWINS için bu rol, _**Azure dijital TWINS veri sahibidir**_. Kavramlar ve güvenlik hakkında daha fazla bilgi için bkz. [*Azure dijital TWINS çözümleri Için güvenlik*](../articles/digital-twins/concepts-security.md).

Bu bölümde, Azure dijital TWINS örneğinizdeki bir kullanıcı için Azure aboneliğinizdeki Azure AD kiracısındaki e-postasını kullanarak bir rol atamasının nasıl oluşturulduğu gösterilir. Kuruluşunuzdaki rolünüze bağlı olarak, bu izni kendiniz ayarlayabilir veya Azure dijital TWINS örneğini yönetebilecek başka bir kişi adına ayarlayabilirsiniz.

### <a name="assign-the-role"></a>Rolü ata

Bir Azure dijital TWINS örneğini yönetmek üzere bir kullanıcıya izin vermek için, _**Azure Digital TWINS veri sahibi**_ rolünü örnek içinde atamanız gerekir.

> [!NOTE]
> Bu rolün Azure AD *sahibi* rolünden farklı olduğunu, bu da Azure dijital TWINS örneğinin kapsamına da atanmadığını unutmayın. Bunlar iki ayrı yönetim rolleridir ve Azure AD *sahibi* , *Azure dijital TWINS veri sahibiyle* verilen veri düzlemi özelliklerine erişim vermez.