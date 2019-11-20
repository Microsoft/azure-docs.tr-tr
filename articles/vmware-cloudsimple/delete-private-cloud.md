---
title: CloudSimple özel bulutuna sahip bir Azure VMware çözümünü silme
description: CloudSimple özel bulutun nasıl silineceğini açıklar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972800"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>CloudSimple özel bulutunu silme

CloudSimple, özel bir bulutu silme esnekliği sağlar.  Bir özel bulut, bir veya daha fazla vSphere kümesinden oluşur. Her küme 3 ile 16 arasında düğüme sahip olabilir. Özel bir bulutu sildiğinizde, tüm kümeler silinir.

## <a name="before-you-begin"></a>Başlamadan önce

Özel bulutun silinmesi, tüm özel bulutun silinmesine neden olacak.  Özel bulutun tüm bileşenleri silinecek.  Verilerin herhangi birini tutmak istiyorsanız, verileri şirket içi depolamaya veya Azure Storage 'a yedeklemiş olduğunuzdan emin olun.

Özel bulutun bileşenleri şunlardır:

* CloudSimple düğümleri
* Sanal makineler
* VLAN 'Lar/alt ağlar
* Özel bulutta depolanan tüm Kullanıcı verileri
* Bir VLAN/subnet 'e yönelik tüm güvenlik duvarı kuralı ekleri

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="delete-a-private-cloud"></a>Özel bulutu silme

1. [CloudSimple portalına erişin](access-cloudsimple-portal.md).

2. **Kaynaklar** sayfasını açın.

3. Silmek istediğiniz özel buluta tıklayın

4. Özet sayfasında **Sil**' e tıklayın.

    ![Özel bulutu Sil](media/delete-private-cloud.png)

5. Onay sayfasında, özel bulutun adını girin ve **Sil**' e tıklayın. 

    ![Özel bulutu Sil-Onayla](media/delete-private-cloud-confirm.png)

Özel bulut silinmek üzere işaretlendi.  Silme işlemi üç saat sonra başlar ve özel bulutu siler.

> [!CAUTION]
> Özel bulutun silinmesinden sonra düğümlerin silinmesi gerekir.  Düğüm ölçümü, düğümlerin aboneliğinizden silindiği zamana kadar devam edecektir.

## <a name="next-steps"></a>Sonraki adımlar

* [Düğümleri Sil](delete-nodes.md)
