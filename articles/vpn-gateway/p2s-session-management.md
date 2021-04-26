---
title: 'Azure VPN Gateway: Noktadan siteye VPN oturumu yönetimi'
description: Bu makale, Noktadan siteye VPN oturumlarını görüntülemenize ve bunların bağlantısını kesmenize yardımcı olur.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: b55fe0bf404ecb8a81e3fe1975dfa9f5ba5dfb06
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103360"
---
# <a name="point-to-site-vpn-session-management"></a>Noktadan siteye VPN oturumu yönetimi

Azure sanal ağ geçitleri, geçerli noktadan siteye VPN oturumlarını görüntülemenin ve bunların bağlantısını kesmenin kolay bir yolunu sunar. Bu makale, geçerli oturumları görüntülemenize ve bunların bağlantısını kesmenize yardımcı olur.

>[!NOTE]
>Oturum durumu 5 dakikada bir güncelleştirilir. Anında güncellenmez.
>

## <a name="portal"></a>Portal

Portalda bir oturumu görüntülemek ve bağlantısını kesmek için:

1. VPN ağ geçidine gidin.
1. **İzleme** bölümünde, **Noktadan siteye oturumlar**' ı seçin.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Portal örneği":::
1. Tüm geçerli oturumları WindowPane içinde görüntüleyebilirsiniz.
1. Bağlantısını kesmek istediğiniz oturum için **"..."** seçeneğini belirleyin, ardından **bağlantıyı kes**' i seçin.

Şu anda bu özelliği Portal 'da VpnGw4 ve VpnGw5 SKU 'Ları için kullanamazsınız. Bu ağ geçitlerinden birine sahipseniz, sonraki bölümde açıklanan PowerShell yöntemini kullanın.

## <a name="powershell"></a>PowerShell

PowerShell kullanarak bir oturumu görüntülemek ve bağlantısını kesmek için:

1. Etkin oturumları listelemek için aşağıdaki PowerShell komutunu çalıştırın:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Bağlantısını kesmek istediğiniz oturumun **Vpnconnectionıd** değerini kopyalayın.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="PowerShell örneği":::
1. Oturumun bağlantısını kesmek için aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Sonraki adımlar

Noktadan siteye bağlantılar hakkında daha fazla bilgi için bkz. [noktadan sıteye VPN hakkında](point-to-site-about.md).
