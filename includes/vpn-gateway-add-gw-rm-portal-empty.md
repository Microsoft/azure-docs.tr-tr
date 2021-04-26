---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6cb30b96f9c2094e6d690b565edf487d6508d520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487125"
---
1. [Azure Portal](https://portal.azure.com), **arama kaynakları, hizmetler ve docs (G +/)** ' de **sanal ağ geçidi** yazın. Arama sonuçlarında **sanal ağ geçidini** bulun ve seçin.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/search.png" alt-text="Arama alanı" lightbox="./media/vpn-gateway-add-gw-rm-portal-empty/search-expand.png":::

1. **Sanal ağ geçidi** sayfasında **+ Ekle**' yi seçin. Bu işlem **Sanal ağ geçidi oluşturma** sayfasını açar.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/add.png" alt-text="sanal ağ geçitleri sayfası":::
1. **Temel bilgiler** sekmesinde, sanal ağ geçidinizin değerlerini girin.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/gateway.png" alt-text="Ağ Geçidi alanları":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/gateway-vnet.png" alt-text="Ek ağ geçidi alanları":::

   **Proje ayrıntıları**

   * **Abonelik**: açılan listeden kullanmak istediğiniz aboneliği seçin.
   * **Kaynak grubu**: Bu sayfada Sanal ağınızı seçtiğinizde bu ayar tekrar doldurulur.

   **Örnek ayrıntıları**

   * **Ad**: Ağ geçidinizi adlandırın. Ağ geçidinizi adlandırma ağ geçidi alt ağını adlandırmayla aynı değildir. Bu ad, oluşturduğunuz ağ geçidi nesnesinin adıdır.
   * **Bölge**: Bu kaynağı oluşturmak istediğiniz bölgeyi seçin. Ağ geçidinin bölgesi, sanal ağ ile aynı olmalıdır.
   * **Ağ geçidi türü**: **VPN**’i seçin. VPN ağ geçitleri, **VPN** sanal ağ geçidi türünü kullanır.
   * **VPN türü**: Yapılandırmanızla ilgili belirtilen VPN türünü seçin. Çoğu yapılandırma, Yol Tabanlı bir VPN türü gerektirir.
   * **SKU**: Açılır listeden ağ geçidi SKU’sunu seçin. Açılır listede sıralanan SKU’lar seçtiğiniz VPN türüne bağlıdır. Ağ geçidi SKU’ları hakkında bilgi için bkz. [Ağ geçidi SKU’ları](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Oluşturma**: VPN Gateway oluşturma hakkında bilgi için bkz. [ağ geçidi SKU 'ları](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Sanal ağ**: açılan listeden, bu ağ geçidini eklemek istediğiniz sanal ağı seçin.
   * **Ağ geçidi alt ağ adres aralığı**: Bu alan yalnızca VNET 'iniz bir ağ geçidi alt ağına sahip değilse görüntülenir. Mümkünse, aralığı/27 veya daha büyük (/26,/25 vb.) aralığı yapın. /28 ' den küçük bir Aralık oluşturmanız önerilmez. Zaten bir ağ geçidi alt ağınız varsa, sanal ağınıza giderek GatewaySubnet ayrıntılarını görüntüleyebilirsiniz. Aralığı görüntülemek için **alt ağlar** ' a tıklayın. Aralığı değiştirmek istiyorsanız, GatewaySubnet 'i silip yeniden oluşturabilirsiniz.

   **Genel IP adresi**

   Bu ayar, VPN ağ geçidiyle ilişkili genel IP adresi nesnesini belirtir. VPN ağ geçidi oluşturulduğunda genel IP adresi bu nesneye dinamik olarak atanır. Genel IP adresi, yalnızca ağ geçidi silinip yeniden oluşturulduğunda değişir. VPN ağ geçidiniz üzerinde gerçekleştirilen yeniden boyutlandırma, sıfırlama veya diğer iç bakım/yükseltme işlemleri sırasında değişmez.

     * **Genel IP adresi**: **Yeni oluştur** seçili kalsın.
     * **Genel IP adresi adı**: metin kutusuna, genel IP adresi örneğiniz için bir ad yazın.
     * **Atama**: VPN Gateway yalnızca dinamik destekler.
     * **Etkin-etkin modu etkinleştir**: yalnızca etkin-etkin ağ geçidi yapılandırması oluşturuyorsanız **etkin-etkin modunu etkinleştir** ' i seçin. Aksi takdirde, bu ayarı **devre dışı** bırakın.
     * Yapılandırmanız özellikle bu ayarı gerektirmediği sürece BGP 'yi **devre dışı** olarak **yapılandırma** ' ya bırakın. Bu ayar gerekliyse varsayılan ASN değeri 65515’tir ancak bu değiştirilebilir.
1. Doğrulamayı çalıştırmak için **gözden geçir + oluştur** ' u seçin.
1. Doğrulama başarılı olduktan sonra, VPN ağ geçidini dağıtmak için **Oluştur** ' u seçin. Bir ağ geçidinin, tam olarak oluşturulması ve dağıtılması 45 dakika sürebilir. Dağıtım durumunu ağ geçidinizin Genel Bakış sayfasında görebilirsiniz.

Ağ geçidi oluşturulduktan sonra, portalda sanal ağa bakarak bu ağ geçidine atanmış IP adresini görüntüleyebilirsiniz. Ağ geçidi bağlı bir cihaz gibi görüntülenir.
