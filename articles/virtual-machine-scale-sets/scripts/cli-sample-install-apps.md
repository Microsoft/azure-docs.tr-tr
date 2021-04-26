---
title: Azure CLı örnekleri-uygulama yüklemesi
description: Bu betik Ubuntu çalıştıran bir sanal makine ölçek kümesi oluşturur ve Özel Betik Uzantısı kullanarak temel bir web uygulaması yükler.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 40a5b4005e55e507ffff5bf8e7dbf740a1e50cf3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932293"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI ile sanal makine ölçek kümesine uygulama yükleme
Bu betik Ubuntu çalıştıran bir sanal makine ölçek kümesi oluşturur ve Özel Betik Uzantısı kullanarak temel bir web uygulaması yükler. Betiği çalıştırdıktan sonra web uygulamasına web tarayıcısı üzerinden erişebilirsiniz.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

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
| [az vmss extension set](/cli/azure/vmss/extension) | Her sanal makine örneğinde veri disklerini hazırlayan bir betik çalıştırmak için Azure Özel Betik Uzantısı’nı yükler. |
| [az network lb rule create](/cli/azure/network/lb/rule) | 80 numaralı TCP bağlantı noktası üzerindeki trafiği ölçek kümesindeki sanal makine örneklerine dağıtmak için bir yük dengeleyici kuralı oluşturur. |
| [az network public-ip show](/cli/azure/network/public-ip) | Yük dengeleyici tarafından kullanılan atanan genel IP adresi hakkında bilgi alır. |
| [az group delete](/cli/azure/ad/group) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure/overview).
