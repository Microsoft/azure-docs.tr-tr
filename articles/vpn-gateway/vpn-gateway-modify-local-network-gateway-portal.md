---
title: 'VPN Gateway: ağ geçidi IP adresi ayarlarını değiştir: Azure portal'
description: Bu makalede, Azure portal kullanarak yerel ağ geçidinizin IP adresi öneklerini değiştirme işlemi adım adım açıklanmaktadır.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143132"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure portalını kullanarak yerel ağ geçidi ayarlarını değiştirme

Bazen yerel ağ geçidi Adresispredüzeltmesini veya Gatewayıpaddress değişikliği ayarları değişir. Bu makalede yerel ağ geçidi ayarlarınızı nasıl değiştireceğiniz gösterilir. Ayrıca, aşağıdaki listeden farklı bir seçenek belirleyerek bu ayarları farklı bir yöntem kullanarak da değiştirebilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portalı](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Yerel ağ geçidi yapılandırması

Aşağıdaki ekran görüntüsünde, genel IP adresi uç noktası kullanılarak yerel ağ geçidi kaynağının **yapılandırma** sayfası gösterilmektedir:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="IP adresi ayarları" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Bu, bir FQDN bitiş noktası olan yapılandırma sayfasıdır:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="FQDN ayarları":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Ağ geçidi IP adresini veya FQDN 'yi değiştirmek için

> [!NOTE]
> FQDN bitiş noktası ve IP adresi uç noktası arasında yerel ağ geçidini değiştiremezsiniz. Bu yerel ağ geçidi ile ilişkili tüm bağlantıları silmeniz, yeni uç nokta (IP adresi veya FQDN) ile yeni bir tane oluşturmanız ve ardından bağlantıları yeniden oluşturmanız gerekir.
>

Bağlanmak istediğiniz VPN cihazı ortak IP adresini değiştirdiyseniz, yerel ağ geçidini aşağıdaki adımları kullanarak değiştirin:

1. Yerel ağ geçidi kaynağında, **Ayarlar** bölümünde **yapılandırma**' yı seçin.
2. **IP adresi** kutusunda, IP adresini değiştirin.
3. Ayarları kaydetmek için **Kaydet**’i seçin.

Bağlanmak istediğiniz VPN cihazı FQDN 'sini (tam etki alanı adı) değiştirdiyseniz, yerel ağ geçidini aşağıdaki adımları kullanarak değiştirin:

1. Yerel ağ geçidi kaynağında, **Ayarlar** bölümünde **yapılandırma**' yı seçin.
2. **FQDN** kutusunda, etki alanı adını değiştirin.
3. Ayarları kaydetmek için **Kaydet**’i seçin.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP adresi öneklerini değiştirmek için

Başka adres ön ekleri eklemek için:

1. Yerel ağ geçidi kaynağında, **Ayarlar** bölümünde **yapılandırma**' yı seçin.
2. IP adresi alanını *ek adres aralığı Ekle* kutusuna ekleyin.
3. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

Adres ön eklerini kaldırmak için:

1. Yerel ağ geçidi kaynağında, **Ayarlar** bölümünde **yapılandırma**' yı seçin.
2. Kaldırmak istediğiniz öneki içeren satırdaki **'... '** seçeneğini belirleyin.
3. **Kaldır**' ı seçin.
4. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>BGP ayarlarını değiştirmek için

BGP ayarlarını eklemek veya güncelleştirmek için:

1. Yerel ağ geçidi kaynağında, **Ayarlar** bölümünde **yapılandırma**' yı seçin.
2. Bu yerel ağ geçidi için BGP yapılandırmalarının görüntülenmesini veya güncelleştirilmesini sağlamak üzere **"BGP ayarlarını yapılandır"** ı seçin
3. Karşılık gelen alanlara otonom sistem numarasını veya BGP eş IP adresini ekleme veya güncelleştirme
4. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

BGP ayarlarını kaldırmak için:

1. Yerel ağ geçidi kaynağında, **Ayarlar** bölümünde **yapılandırma**' yı seçin.
2. Mevcut BGP ASN ve BGP eşi IP adresini kaldırmak için **"BGP ayarlarını yapılandır"** seçimini kaldırın
3. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Ağ Geçidi bağlantınızı doğrulayabilirsiniz. Bkz. [ağ geçidi bağlantısını doğrulama](vpn-gateway-verify-connection-resource-manager.md).
