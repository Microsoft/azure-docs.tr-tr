---
title: Azure Güvenlik duvarını bir sanal WAN hub 'ına yükler
titleSuffix: Azure Virtual WAN
description: Sanal WAN hub 'ında Azure Güvenlik duvarını yapılandırma adımları
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983666"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Sanal WAN hub 'ında Azure Güvenlik duvarını yapılandırma

**Güvenli bir hub** , Azure Güvenlik duvarı olan bir Azure sanal WAN hub 'ına sahiptir. Bu makalede, Azure Güvenlik duvarını doğrudan Azure sanal WAN Portal sayfalarından yükleyerek bir sanal WAN hub 'ını güvenli bir hub 'a dönüştürme adımları adım adım açıklanmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımlarda, bir veya daha fazla hub ile sanal bir WAN dağıtmış olduğunuz varsayılır.

Yeni bir sanal WAN ve yeni bir hub oluşturmak için aşağıdaki makalelerdeki adımları kullanın:

* [Sanal WAN oluşturma](virtual-wan-site-to-site-portal.md#openvwan)
* [Hub oluşturma](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Sanal hub 'ları görüntüle

Sanal WAN 'nizin **genel bakış** sayfası, sanal hub 'ların ve güvenli hub 'ların listesini gösterir. Aşağıdaki şekilde, güvenli hub 'ları olmayan bir sanal WAN gösterilmektedir.

[![Ekran görüntüsünde, sanal hub 'ların listesini içeren bir sanal WAN Için genel bakış sayfası gösterilir.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Güvenli hub 'a Dönüştür

1. Sanal WAN 'nizin **genel bakış** sayfasında, güvenli bir hub 'a dönüştürmek istediğiniz hub 'ı seçin. Sanal hub sayfasında, bu hub 'a Azure Güvenlik Duvarı dağıtmak için iki seçenek görürsünüz. İki seçenekten birini seçin.

   [![Ekran görüntüsü, sanal WAN 'ınız için güvenli hub 'a veya Azure Güvenlik Duvarı 'Na Dönüştür ' ü seçebileceğiniz genel bakış sayfasını gösterir.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Seçeneklerden birini belirledikten sonra, **güvenli hub 'A Dönüştür** sayfasını görürsünüz. Dönüştürülecek bir hub seçin ve ardından Ileri ' yi seçin. sayfanın en altındaki **Azure Güvenlik Duvarı** .

   [![hub seçin](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. İş akışını tamamladıktan sonra **Onayla**' yı seçin.

   [![Ekran görüntüsü, seçili Onayla seçiliyken güvenli hub 'A Dönüştür bölmesini gösterir.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Hub, güvenli bir hub 'a dönüştürüldükten sonra sanal WAN **genel bakış** sayfasında görüntüleyebilirsiniz.

   [![güvenli hub 'ı görüntüle](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Hub kaynaklarını görüntüle

Sanal WAN **genel bakış** sayfasında, güvenli hub ' ı seçin. Hub sayfasında, Azure Güvenlik Duvarı dahil olmak üzere tüm sanal hub kaynaklarını görüntüleyebilirsiniz.

[![hub kaynaklarını görüntüle](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Azure Güvenlik Duvarı ayarlarını güvenli hub 'dan görüntülemek için **güvenlik** altında **güvenli sanal hub ayarları**' nı seçin.
[![hub ayarlarını görüntüle](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Ek ayarları yapılandırın

Sanal hub için ek Azure Güvenlik Duvarı ayarlarını yapılandırmak için **Azure Güvenlik Duvarı Yöneticisi** bağlantısını seçin. Güvenlik Duvarı ilkeleri hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Yöneticisi](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[![ek ayarlar](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Hub 'a **genel bakış** sayfasına geri dönmek için, aşağıdaki şekildeki okla gösterildiği gibi yola tıklayarak geri gidebilirsiniz.

[![genel bakışa dön](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için bkz. [SSS](virtual-wan-faq.md).
