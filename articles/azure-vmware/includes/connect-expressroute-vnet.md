---
title: ExpressRoute 'ı sanal ağ geçidine bağlama
description: ExpressRoute 'ı sanal ağ geçidine bağlama adımları.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494383"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. [Azure 'Da vSphere kümesi dağıtma](../tutorial-create-private-cloud.md) öğreticisinde oluşturduğunuz özel buluta gidin. **Yönet** altında **bağlantı** ' yı seçin, **ExpressRoute** sekmesini seçin.

1. Yetkilendirme anahtarını kopyalayın. Bir yetkilendirme anahtarı yoksa, oluşturmanız gerekir ve **Yetkilendirme anahtarı iste**' yi seçin.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Yetkilendirme anahtarını kopyalayın. Bir yetkilendirme anahtarı yoksa, oluşturmanız gerekir ve yetkilendirme anahtarı ıste ' yi seçin." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Önceki adımda oluşturduğunuz sanal ağ geçidine gidin ve **Ayarlar** altında **Bağlantılar**' ı seçin. **Bağlantılar** sayfasında **+ Ekle**' yi seçin.

1. **Bağlantı ekle** sayfasında, alanlar için değerler sağlayın ve **Tamam**' ı seçin. 

   | Alan | Değer |
   | --- | --- |
   | **Ad**  | Bağlantı için bir ad girin.  |
   | **Bağlantı türü**  | **ExpressRoute**' ı seçin.  |
   | **Yetkilendirmeyi kullanma**  | Bu kutunun seçili olduğundan emin olun.  |
   | **Sanal ağ geçidi** | Daha önce oluşturduğunuz sanal ağ geçidi.  |
   | **Yetkilendirme anahtarı**  | Yetkilendirme anahtarını kopyalayın ve kaynak grubunuzun ExpressRoute sekmesinden yapıştırın. |
   | **Eş devre URI 'SI**  | ExpressRoute KIMLIĞINI kopyalayın ve kaynak grubunuzun ExpressRoute sekmesinden yapıştırın.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="ExpressRoute 'ı sanal ağ geçidine bağlamak için bağlantı ekle sayfasının ekran görüntüsü.":::

ExpressRoute bağlantı hattı ve sanal ağınız arasındaki bağlantı oluşturulur.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Sanal ağ geçidi bağlantılarının ekran görüntüsü.":::