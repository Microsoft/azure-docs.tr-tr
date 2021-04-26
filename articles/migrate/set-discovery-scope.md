---
title: Azure geçişi ile VMware üzerinde sunucu keşfi için kapsamı ayarlama
description: Azure geçişi ile VMware değerlendirmesi ve geçişi üzerinde barındırılan sunucular için bulma kapsamının nasıl ayarlanacağını açıklar.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775366"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>VMware ortamındaki sunucular için bulma kapsamını ayarlama

Bu makalede, şu durumlarda VMware ortamındaki sunucular için bulma kapsamının nasıl sınırlandırılacağını açıklanmaktadır:

- Azure geçişi: bulma ve değerlendirme aracını kullanırken [Azure geçiş](migrate-appliance-architecture.md) gereci ile sunucu bulma.
- Azure geçişi: sunucu geçiş aracı 'nı kullanırken, sunucuları VMware ortamından Azure 'a daha az geçirmek için [Azure geçişi 'ni](migrate-appliance-architecture.md) kullanarak sunucuları keşfetme.

Gereci ayarlarken vCenter Server bağlanır ve bulmayı başlatır. Gereci vCenter Server 'e bağlanmadan önce, bulmayı vCenter Server veri merkezleri, kümeler, bir küme klasörü, konaklar, konaklar klasörü veya ayrı sunucular ile sınırlayabilirsiniz. Kapsamı ayarlamak için, gerecin vCenter Server erişmek için kullandığı hesaba izin atarsınız.

## <a name="before-you-start"></a>Başlamadan önce

Azure geçişi 'nin bulma için kullandığı bir vCenter Kullanıcı hesabı ayarlamadıysanız, artık [değerlendirme](./tutorial-discover-vmware.md#prepare-vmware) veya [aracısız geçiş](./migrate-support-matrix-vmware-migration.md#agentless-migration)için bunu yapın.


## <a name="assign-permissions-and-roles"></a>İzin ve rol atama

İki yöntemden birini kullanarak VMware Inventory Objects 'e izinler atayabilirsiniz:

- Gereç tarafından kullanılan hesapta, kapsama eklemek istediğiniz nesneler üzerinde gerekli izinlere sahip bir rol atayın.
- Alternatif olarak, veri merkezi düzeyinde hesaba bir rol atayın ve alt nesnelere yayın. Ardından hesaba, kapsam içinde istemediğiniz her nesne için bir **erişim** rolü vermeyin. Her yeni alt nesneye otomatik olarak devralınan erişim izni verildiğinden, bu yaklaşımın bir bütün olduğundan bu yaklaşımı önermiyoruz.

, VCenter Server klasör düzeyinde envanter bulmayı kapsamalamazsınız. Bir klasördeki sunucularla keşfet 'i kapsama almanız gerekiyorsa, bir kullanıcı oluşturun ve her bir gerekli sunucuya ayrı ayrı erişim verin. Konak ve küme klasörleri desteklenir.


### <a name="assign-a-role-for-assessment"></a>Değerlendirme için rol atama

1. Bulma için kullandığınız gereç vCenter hesabında, bulmak ve değerlendirmek istediğiniz sunucuları barındıran (konak, küme, konaklar klasörü, kümeler klasörü, veri merkezine kadar) tüm üst nesneler için **salt okuma** rolünü uygulayın.
2. Bu izinleri hiyerarşideki alt nesnelere yay.

    ![İzinler atama](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Aracısız geçiş için rol atama

1. Geçiş için kullanmakta olduğunuz gereç vCenter hesabında, [gereken izinlere](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)sahip kullanıcı tanımlı bir rolü, keşfetme ve geçirme istediğiniz sunucuları barındıran tüm üst nesnelere uygulayın.
2. Rolü, daha kolay tanımlanacak bir şekilde ad verebilirsiniz. Örneğin, <em>Azure_Migrate</em>.

## <a name="work-around-for-server-folder-restriction"></a>Sunucu klasörü kısıtlaması için geçici çözüm

Şu anda Azure geçişi: bulma ve değerlendirme aracı, vCenter Server klasör düzeyinde erişim verildiğinde sunucuları bulamaz. Bulma ve değerlendirmenize sunucu klasörlerine göre kapsam yapmak istiyorsanız, bu geçici çözümü kullanın.

1. Bulma ve değerlendirme için kapsama eklemek istediğiniz klasörlerde bulunan tüm sunucularda salt okunurdur izinleri atayın.
2. Sunucular konak, küme, konaklar klasörü, kümeler klasörünü, veri merkezine kadar olan tüm üst nesnelere salt okuma erişimi verin. İzinleri tüm alt nesnelere yaymaya gerek yoktur.
3. Bulma için kimlik bilgilerini kullanmak için, veri merkezini **koleksiyon kapsamı** olarak seçin.


Rol tabanlı erişim denetimi kurulumu, karşılık gelen vCenter Kullanıcı hesabının yalnızca kiracıya özgü sunuculara erişimi olmasını sağlar.


## <a name="next-steps"></a>Sonraki adımlar

[Gereci ayarlama](how-to-set-up-appliance-vmware.md)