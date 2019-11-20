---
title: 'Azure sanal WAN için özel IPSec ilkesini yapılandırma: Portal | Microsoft Docs'
description: Portalı kullanarak Azure sanal WAN için özel IPSec ilkesi yapılandırma hakkında bilgi edinin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515752"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Portalı kullanarak sanal WAN için özel bir IPSec ilkesi yapılandırma

Azure portal sanal WAN için özel IPSec ilkesi yapılandırabilirsiniz. Özel ilkeler, her iki tarafının da (Şirket içi ve Azure VPN Gateway) ıKE Aşama 1 ve ıKE aşama 2 için aynı ayarları kullanmasını istediğinizde yararlıdır.

## <a name="working-with-custom-policies"></a>Özel ilkelerle çalışma

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>İlke yapılandırma

1. **Sanal hub 'ı bulun**. Bir tarayıcıdan [Azure portalına](https://aka.ms/azurevirtualwanpreviewfeatures) gidin ve Azure hesabınızla oturum açın. Siteniz için sanal hub 'ı bulun.
2. **VPN sitesini seçin**. Hub sayfasından, özel bir ilke ayarlamak istediğiniz VPN sitesini seçin.

   ![seçin](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **VPN bağlantısını düzenleyin**. **Bağlam menüsünden** **...** , **VPN bağlantısını Düzenle**' yi seçin.

   ![Düzenle](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Ayarları yapılandırın**. **VPN bağlantısını Düzenle** sayfasında ayarları Ayarlar ' ı yapılandırın. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

   ![Yapılandır ve Kaydet](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için [Sanal WAN'a Genel Bakış](virtual-wan-about.md) sayfasına bakın.