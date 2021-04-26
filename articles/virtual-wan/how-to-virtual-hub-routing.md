---
title: Sanal merkez yönlendirmeyi yapılandırma
titleSuffix: Azure Virtual WAN
description: Bu makalede, sanal hub yönlendirmenin nasıl yapılandırılacağı açıklanır.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313697"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Sanal merkez yönlendirmeyi yapılandırma

Bir sanal hub, siteden siteye VPN ağ geçidi, ExpressRoute ağ geçidi, Noktadan siteye ağ geçidi ve Azure Güvenlik Duvarı gibi birden çok ağ geçidi içerebilir. Sanal hub 'daki yönlendirme özellikleri, Sınır Ağ Geçidi Protokolü (BGP) kullanan ağ geçitleri arasında geçiş yönlendirmesi dahil olmak üzere tüm yönlendirmeyi yöneten bir yönlendirici tarafından sağlanır. Bu yönlendirici Ayrıca, bir sanal hub 'a bağlanan sanal ağlar arasında geçiş bağlantısı sağlar ve 50 Gbps 'in toplam verimini destekleyebilir. Bu yönlendirme özellikleri standart sanal WAN müşterileri için geçerlidir.

Daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).

## <a name="create-a-route-table"></a><a name="create-table"></a>Yönlendirme tablosu oluşturma

1. Azure portal sanal hub 'a gidin.
2. **Bağlantı** altında, **yönlendirme**' yi seçin. Yönlendirme sayfasında, **varsayılan** ve **none** yol tablolarını görürsünüz.

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Yönlendirme sayfası":::
3. **Rota tablosu oluştur** sayfasını açmak için **+ yol tablosu oluştur** ' u seçin.
4. Yol Tablosu Oluştur sayfa **temelleri** sekmesinde aşağıdaki alanları doldurun.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Temel bilgiler sekmesi":::

   * **Ad**
   * **Rotalar**
   * **Yol adı**
   * **Hedef türü**
   * **Hedef ön eki**: ön ekleri toplayabilirsiniz. Örneğin: VNet 1:10.1.0.0/24 ve VNet 2:10.1.1.0/24, 10.1.0.0/16 olarak toplanabilir. **Dal** yolları tüm bağlı vpn siteleri, ExpressRoute devreleri ve kullanıcı VPN bağlantıları için geçerlidir.
   * **Sonraki atlama**: sanal ağ bağlantılarının listesi veya Azure Güvenlik Duvarı.

     Bir sanal ağ bağlantısı seçerseniz, **statik yolları Yapılandır**' ı görürsünüz. Bu, isteğe bağlı bir yapılandırma ayarıdır. Daha fazla bilgi için bkz. [statik yolları yapılandırma](about-virtual-hub-routing.md#static).

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Sonraki atlama":::

5. Etiket adlarını yapılandırmak için **Etiketler** sekmesini seçin. Etiketler, yönlendirme tablolarını mantıksal olarak gruplamak için bir mekanizma sağlar.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Etiket adlarını yapılandırma":::

6. Bağlantıları yol tablosuyla ilişkilendirmek için **ilişkilendirmeler** sekmesini seçin.
**Dallar**, **sanal ağlar** ve bağlantıların **geçerli ayarlarını** görürsünüz.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Yol tablosuyla ilişkilendirme bağlantıları":::

7. Yolların bağlantı tablosundan yol tablosuna yayılması için **yayılmaları** sekmesini seçin.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Rotaları yayma":::

8. Yol tablosunu oluşturmak için **Oluştur** ' u seçin.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Bir yol tablosunu düzenlemek için

Azure portal, sanal hub 'ınızın yol tablosunu bulun. Herhangi bir bilgiyi düzenlemek için yol tablosunu seçin.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Bir yol tablosunu silmek için

Azure portal, sanal hub 'ınızın yol tablosunu bulun. Varsayılan veya None yol tablosunu silemezsiniz. Ancak tüm özel yol tablolarını silebilirsiniz. **"..."** Ve ardından **Sil**' i seçin.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>Geçerli yolları görüntülemek için

Azure portal, sanal hub 'ınızın yol tablosunu bulun. Seçili yol tablosu tarafından öğrenilen yolları görüntülemek için **"..."** ve **geçerli rotalar** ' ı seçin. Yol tablosuna giden yayılmakta olan yollar, yol tablosunun **etkin rotalarında** otomatik olarak doldurulur. Daha fazla bilgi için bkz. [geçerli rotalar hakkında](effective-routes-virtual-hub.md).

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Geçerli yolları görüntüle" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>Bir sanal ağ bağlantısı için yönlendirme yapılandırmasını ayarlamak için

1. Azure portal, sanal WAN 'nize gidin ve **bağlantı** altında **sanal ağ bağlantıları**' nı seçin.
1. **+ Bağlantı ekle**' yi seçin.
1. Açılan menüden sanal ağı seçin.
1. Yönlendirme yapılandırmasını bir rota tablosuyla ilişkilendirilecek şekilde ayarlayın. **Yol tablosunu ilişkilendir** için açılan listeden yol tablosunu seçin.
1. Yönlendirme yapılandırmasını bir veya daha fazla yol tablosuna yaymaya ayarlayın. **Rota tablosuna yay** için, açılan listeden seçim yapın.
1. **Statik yollar** Için, ağ sanal gereci (varsa) statik yollarını yapılandırın. Sanal WAN, sanal ağ bağlantısı içindeki statik yol için tek bir sonraki atlama IP 'yi destekler. Örneğin, giriş ve çıkış trafik akışları için ayrı bir Sanal Gereç varsa, sanal gereçlere ayrı VNET 'lerde sahip olmak ve sanal hub 'ları sanal hub 'a eklemek en iyi seçenektir.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Yönlendirme yapılandırmasını ayarlama" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Sonraki adımlar

Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).
Sanal WAN hakkında daha fazla bilgi için bkz. [SSS](virtual-wan-faq.md).
