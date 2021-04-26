---
title: 'Azure VPN Gateway: ağ geçidi bağlantısını doğrulama'
description: Bu makalede, bir sanal ağ VPN Gateway bağlantısının nasıl doğrulanyapılacağı gösterilir.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b59294d07ef64875cb6fbd3e3a49dec61d8b8135
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659412"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway bağlantısını doğrulama

Bu makalede, hem klasik hem de Kaynak Yöneticisi dağıtım modelleriyle ilgili bir VPN ağ geçidi bağlantısını nasıl doğrulayabildiğinizi gösterilmektedir.

## <a name="azure-portal"></a>Azure portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

PowerShell kullanarak Kaynak Yöneticisi dağıtım modeli için bir VPN ağ geçidi bağlantısını doğrulamak için, [Azure Resource Manager PowerShell cmdlet](/powershell/azure/)'lerinin en son sürümünü yükler.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI’si

Azure CLı kullanarak Kaynak Yöneticisi dağıtım modeli için bir VPN ağ geçidi bağlantısını doğrulamak için, [CLI komutlarının](/cli/azure/install-azure-cli) en son sürümünü (2,0 veya üzeri) yüklemelisiniz.

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Azure portal (klasik)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (Klasik)

PowerShell kullanarak klasik dağıtım modeliyle ilgili VPN Gateway bağlantınızı doğrulamak için Azure PowerShell cmdlet 'lerinin en son sürümlerini yükler. [Hizmet yönetimi](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets) modülünü indirip yüklediğinizden emin olun. Klasik dağıtım modelinde oturum açmak için ' Add-AzureAccount ' kullanın.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Sanal ağlarınıza sanal makineler ekleyebilirsiniz. Adımlar için bkz. [Sanal Makine Oluşturma](../virtual-machines/windows/quick-create-portal.md).