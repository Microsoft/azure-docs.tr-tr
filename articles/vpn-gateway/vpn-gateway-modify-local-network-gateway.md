---
title: 'Ağ geçidi IP adresi ayarlarını değiştir: PowerShell'
description: Bu makalede, PowerShell kullanarak yerel ağ geçidinizin IP adresi öneklerini değiştirme işlemi adım adım açıklanmaktadır
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 07d9124105424d42970800b1c5bca956d512722d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388677"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>PowerShell kullanarak yerel ağ geçidi ayarlarını değiştirme

Bazen yerel ağ geçidi Adresispredüzeltmesini veya Gatewayıpaddress değişikliği ayarları değişir. Bu makalede yerel ağ geçidi ayarlarınızı nasıl değiştireceğiniz gösterilir. Ayrıca, aşağıdaki listeden farklı bir seçenek belirleyerek bu ayarları farklı bir yöntem kullanarak da değiştirebilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portalı](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Başlamadan önce

Azure Resource Manager PowerShell cmdlet'lerinin en son sürümünü yükleyin. PowerShell cmdlet'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/).

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP adresi öneklerini değiştirme

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Ağ geçidi IP adresini değiştirme

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Ağ Geçidi bağlantınızı doğrulayabilirsiniz. Bkz. [ağ geçidi bağlantısını doğrulama](vpn-gateway-verify-connection-resource-manager.md).