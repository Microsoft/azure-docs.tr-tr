---
title: Azure Güvenlik Merkezi 'nde kiracı genelinde izin verme ve isteme
description: Azure Güvenlik Merkezi 'nde kiracı genelinde izinleri yönetmeyi öğrenin
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 0a24546579df020dcb7c7a9b01ee3d181226d2df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617497"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Kiracı genelinde görünürlük verme ve isteme

**Genel yöneticinin** Azure ACTIVE DIRECTORY (ad) rolüne sahip bir kullanıcının kiracı genelinde sorumlulukları olabilir, ancak Azure Güvenlik Merkezi 'nde kuruluş genelinde bu bilgileri görüntülemek için Azure izinlerinin olmaması gerekir. Azure AD rol atamaları Azure kaynaklarına erişim vermediği için izin yükseltme gereklidir. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Kendi kendinize kiracı genelinde izinler verin

Kendi kiracı düzeyi izinlerini atamak için:

1. Kuruluşunuz [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)veya başka bir PIM aracıyla kaynak erişimini yönetirse, aşağıdaki yordamı izleyerek Kullanıcı için genel yönetici rolü etkin olmalıdır.

1. Kiracının kök yönetim grubu üzerinde atama olmadan genel yönetici kullanıcı olarak, güvenlik merkezi 'nin **genel bakış** sayfasını açın ve başlıktaki **kiracı genelinde görünürlük** bağlantısını seçin. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Azure Güvenlik Merkezi 'nde kiracı düzeyindeki izinleri etkinleştirme":::

1. Atanacak yeni Azure rolünü seçin. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Kullanıcıya atanacak kiracı düzeyi izinleri tanımlamak için form":::

    > [!TIP]
    > Genellikle güvenlik yöneticisi rolü kök düzeyinde uygulamak için gereklidir, ancak güvenlik okuyucusu kiracı düzeyinde görünürlük sağlamak için yeterli olacaktır. Bu rollerin verdiği izinler hakkında daha fazla bilgi için bkz. [Güvenlik Yöneticisi yerleşik rol açıklaması](../role-based-access-control/built-in-roles.md#security-admin) veya [güvenlik okuyucusu yerleşik rol açıklaması](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Güvenlik Merkezi 'ne özgü bu roller arasındaki farklar için, [rollerdeki tabloya ve izin verilen eylemlere](security-center-permissions.md#roles-and-allowed-actions)bakın.

    Kuruluş genelinde görünüm, kiracının kök yönetim grubu düzeyine roller verilerek elde edilir.  

1. Azure portal oturumunuzu kapatıp yeniden oturum açın.

1. Erişimi yükselttikten sonra Azure AD kiracınız kapsamındaki tüm aboneliklerde görünürlük olduğunu doğrulamak için Azure Güvenlik Merkezi 'ni açın veya yenileyin. 

Yukarıdaki basit süreç sizin için otomatik olarak birkaç işlem gerçekleştirir:

1. Kullanıcının izinleri geçici olarak yükseltilmiş.
1. Yeni izinleri kullanarak, Kullanıcı kök yönetim grubundaki istenen Azure RBAC rolüne atanır.
1. Yükseltilmiş izinler kaldırılır.

Azure AD yükseltme işlemi hakkında daha fazla bilgi için bkz. [tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek için erişimi yükseltme](../role-based-access-control/elevate-access-global-admin.md).


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Sizinki yetersizse kiracı genelinde izinler isteyin

Güvenlik Merkezi 'Nde oturum açarsanız ve görünümlerinizin sınırlı olduğunu söyleyen bir başlık görürseniz, kuruluşunuzun genel yöneticisine bir istek göndermek için öğesine tıklayabilirsiniz. İstekte, atanmasını istediğiniz rolü dahil edebilir ve genel yönetici hangi rolün verilmesi gerektiğini bir karar verir. 

Bu istekleri kabul etmek veya reddetmek için genel yönetici karardır. 

> [!IMPORTANT]
> Her yedi günde bir istek gönderebilirsiniz.

Genel yöneticinizden yükseltilmiş izinler istemek için:

1. Azure portal Azure Güvenlik Merkezi ' ni açın.

1. "Sınırlı bilgileri görüyorsunuz" başlığını görürseniz. Bunu seçin.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Kullanıcıya kiracı genelinde izinler isteyebilecekleri bir başlık bildiren başlık.":::

1. Ayrıntılı istek formunda, istenen rolü ve neden bu izinlere ihtiyacınız olduğunu doğrulama ' yı seçin.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Azure genel yöneticinizden kiracı genelinde izin istemek için Ayrıntılar sayfası":::

1. **Erişim iste**' yi seçin.

    Genel yöneticiye bir e-posta gönderilir. E-posta, isteği onaylayabilecekleri veya reddedebilecekleri Güvenlik Merkezi 'ne bir bağlantı içerir.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Yeni izinler için genel yöneticiye e-posta gönder":::

    Genel yönetici, **Isteği gözden geçir** ' i seçtikten sonra işlemi tamamladıktan sonra, karar istekte bulunan kullanıcıya e-posta ile gönderilir. 

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki ilgili sayfada Güvenlik Merkezi izinleri hakkında daha fazla bilgi edinin:

- [Azure Güvenlik Merkezi'nde İzinler](security-center-permissions.md)