---
title: Azure VPN Gateway için OpenVPN 'i yapılandırma
description: Noktadan siteye ortam için Azure VPN Gateway 'da OpenVPN protokolünü etkinleştirmeyi öğrenin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 137e4e1372ef1af3319c0b9af7ba965fffcb9e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584049"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>Noktadan siteye VPN ağ geçitleri için OpenVPN yapılandırma

Bu makale, Azure VPN Gateway üzerinde **OpenVPN® Protokolü** ayarlamanıza yardımcı olur. Portalı ya da PowerShell yönergelerini kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Makalesinde, zaten çalışan bir noktadan siteye ortamınız olduğunu varsayılmaktadır. Aksi takdirde, aşağıdaki yöntemlerden birini kullanarak bir tane oluşturun.

  * [Portal-Noktadan siteye oluşturma](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell-Noktadan siteye oluştur](vpn-gateway-howto-point-to-site-rm-ps.md)

* VPN Gateway 'nizin temel SKU 'YU kullanmadığından emin olun. Temel SKU, OpenVPN için desteklenmez.

## <a name="portal"></a>Portal

1. Portalda, **sanal ağ geçidinizin > Noktadan siteye yapılandırmaya** gidin.
1. **Tünel türü** için açılan listeden **OpenVPN (SSL)** öğesini seçin.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Açılan menüden OpenVPN SSL 'yi seçin":::
1. Değişikliklerinizi kaydedin ve **sonraki adımlarla** devam edin.

## <a name="powershell"></a>PowerShell

1. Aşağıdaki örneği kullanarak ağ geçidinizdeki OpenVPN 'i etkinleştirin:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. **Sonraki adımlarla** devam edin.

## <a name="next-steps"></a>Sonraki adımlar

İstemcileri OpenVPN için yapılandırmak için bkz. [OpenVPN Istemcilerini yapılandırma](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN", OpenVPN Inc 'nin ticari markasıdır.**
