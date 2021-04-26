---
title: Azure CLI Betiği Örneği - Web trafiğini yönetme | Microsoft Docs
description: Azure CLI Betik Örneği - Bir uygulama ağ geçidi ve sanal makine ölçek kümesi ile web trafiğini yönetin.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 17cac91ae62f5faa80fa130364bf67f81ad20a81
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789420"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Azure CLI kullanarak web trafiğini yönetme

Bu betik, arka uç sunucuları için bir sanal makine ölçek kümesi kullanan bir uygulama ağ geçidi oluşturur. Ardından uygulama ağ geçidi web trafiğini yönetmek üzere yapılandırılabilir. Betiği çalıştırdıktan sonra, genel IP adresini kullanarak uygulama ağ geçidini test edebilirsiniz.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Kaynak grubunu, uygulama ağ geçidini ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, dağıtımı oluşturmak için aşağıdaki komutları kullanır. Tablodaki her öğe, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az network vnet create](/cli/azure/network/vnet) | Sanal ağ oluşturur. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Bir sanal ağda bir alt ağ oluşturur. |
| [az network public-ip create](/cli/azure/network/public-ip) | Uygulama ağ geçidi için genel IP adresini oluşturur. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Uygulama ağ geçidi oluşturur. |
| [az vmss create](/cli/azure/vmss) | Sanal makine ölçek kümesi oluşturur. |
| [az network public-ip show](/cli/azure/network/public-ip) | Uygulama ağ geçidinin genel IP adresini alır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure/overview).

Ek uygulama ağ geçidi CLI betiği örnekleri, [Azure Windows VM belgeleri](../cli-samples.md) içinde bulunabilir.
