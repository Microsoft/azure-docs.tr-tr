---
title: 'Noktadan siteye özel IPSec ilkeleri oluşturma ve ayarlama: PowerShell'
titleSuffix: Azure VPN Gateway
description: Bu makale, VPN Gateway P2S yapılandırmalarına yönelik özel IPSec ilkeleri oluşturmanıza ve ayarlamanıza yardımcı olur
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: a77a870304e20c179e22b4a4ffe404315894d8a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91743701"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site-preview"></a>Noktadan siteye (Önizleme) yönelik özel IPSec ilkeleri oluşturma ve ayarlama

Ortamınız şifreleme için özel bir IPSec ilkesi gerektiriyorsa, gerekli ayarlarla bir ilke nesnesini kolayca yapılandırabilirsiniz. Bu makale, özel bir ilke nesnesi oluşturmanıza ve sonra PowerShell kullanarak ayarlamanıza yardımcı olur.

## <a name="before-you-begin"></a>Başlamadan önce

### <a name="prerequisites"></a>Önkoşullar

Ortamınızın aşağıdaki önkoşulları karşıladığından emin olun:

* Çalışan bir noktadan siteye VPN zaten yapılandırılmış. Aksi takdirde, [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)'i veya [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)kullanarak **Noktadan siteye VPN oluşturma** makalesini kullanarak bir tane yapılandırın.

### <a name="working-with-azure-powershell"></a>Azure PowerShell çalışma

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. değişkenleri ayarla

Kullanmak istediğiniz değişkenleri bildirin. Gerektiğinde, değerlerini kendi değerlerinizle değiştirerek aşağıdaki örneği kullanın. Alıştırma sırasında herhangi bir noktada PowerShell/Cloud Shell oturumunuzu kapatırsanız, değişkenleri yeniden bildirmek için değerleri kopyalamanız ve tekrar yapıştırmanız yeterlidir.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. ilke nesnesi oluştur

Özel bir IPSec ilkesi nesnesi oluşturun. Değerleri, ihtiyacınız olan ölçütlere uyacak şekilde ayarlayabilirsiniz.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. ağ geçidini güncelleştirme ve ilke ayarlama

Bu adımda, mevcut P2S VPN ağ geçidinizi güncelleştirin ve IPSec ilkesini ayarlayın.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>Sonraki adımlar

P2S yapılandırması hakkında daha fazla bilgi için bkz. [noktadan sıteye VPN hakkında](point-to-site-about.md).
