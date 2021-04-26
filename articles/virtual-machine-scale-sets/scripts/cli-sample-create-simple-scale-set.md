---
title: Azure CLı örnekleri-sanal makine ölçek kümesi oluşturma
description: Bu betik, Ubuntu işletim sistemi ve yük dengeleyici içeren ilgili ağ kaynakları ile bir Azure sanal makine ölçek kümesi oluşturur.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 1166a5df1f814e015f57dc7e80d9e440fdbe639a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934640"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI ile sanal makine ölçek kümesi oluşturma
Bu betik, Ubuntu işletim sistemi ve yük dengeleyici içeren ilgili ağ kaynakları ile bir Azure sanal makine ölçek kümesi oluşturur. Betiği çalıştırdıktan sonra sanal makine örneklerine SSH üzerinden erişebilirsiniz.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik
```azurecli-interactive
#!/bin/bash

# Create a resource group
az group create --name myResourceGroup --location eastus

# Create a Network Security Group and allow access to port 22
az network nsg create --resource-group MyResourceGroup --name MyNsg
az network nsg rule create --resource-group MyResourceGroup --name AllowSsh --nsg-name MyNsg --priority 100 --destination-port-ranges 22

# Create a scale set
# Network resources such as an Azure load balancer are automatically created
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
  --nsg MyNsg
```

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Kaynak grubunu, ölçek kümesini ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, bir kaynak grubu, sanal makine ölçek kümesi ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/ad/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az vmss create](/cli/azure/vmss) | Sanal makine ölçek kümesi oluşturur ve bunu sanal ağa, alt ağa ve ağ güvenlik grubuna bağlar. Birden çok sanal makine örneklerine trafiği dağıtmak için bir yük dengeleyici de oluşturulur. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir.  |
| [az group delete](/cli/azure/ad/group) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure/overview).
