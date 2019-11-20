---
title: 'VPN Gateway: ağ geçidi IP adresi ayarlarını değiştir: Azure portal'
description: Bu makalede, Azure portal kullanarak yerel ağ geçidinizin IP adresi öneklerini değiştirme işlemi adım adım açıklanmaktadır.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 3a59f618536d44e838bf840264e70b0b2a43cced
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014919"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure portal kullanarak yerel ağ geçidi ayarlarını değiştirme

Bazen yerel ağ geçidi Adresispredüzeltmesini veya Gatewayıpaddress değişikliği ayarları değişir. Bu makalede yerel ağ geçidi ayarlarınızı nasıl değiştireceğiniz gösterilir. Ayrıca, aşağıdaki listeden farklı bir seçenek belirleyerek bu ayarları farklı bir yöntem kullanarak da değiştirebilirsiniz:

Bağlantıyı silmeden önce, tanımlanan PSK 'yi almak için bağlanan cihazlarınız için yapılandırmayı indirmek isteyebilirsiniz. Bu şekilde, diğer tarafta yeniden tanımlamanız gerekmez.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>IP adresi öneklerini değiştirme

IP adresi öneklerini değiştirirken, izleyeceğiniz adımlar Yerel Ağ geçidinizde bir bağlantı olup olmadığına bağlıdır.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Ağ geçidi IP adresini değiştirme

Bağlanmak istediğiniz VPN cihazının genel IP adresi değiştiyse, yerel ağ geçidini bu değişikliği yansıtacak şekilde değiştirmeniz gerekir. Genel IP adresini değiştirdiğinizde, izleyeceğiniz adımlar yerel ağ geçidinize bir bağlantı olup olmadığına bağlıdır.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Ağ Geçidi bağlantınızı doğrulayabilirsiniz. Bkz. [ağ geçidi bağlantısını doğrulama](vpn-gateway-verify-connection-resource-manager.md).
