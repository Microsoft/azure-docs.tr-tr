---
title: Azure CLı betik örneği-Batch 'te Linux havuzu
description: Bu betik, Azure Batch ' de bir Linux işlem düğümü havuzu oluşturmak ve yönetmek için Azure CLı 'de mevcut olan komutlardan bazılarını gösterir.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b5e1bdccefffa7803fbe744e27c1b36ca719560d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768296"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI örneği: Azure Batch’te bir Linux havuzu oluşturma ve yönetme

Bu betik, Azure Batch ' de bir Linux işlem düğümü havuzu oluşturmak ve yönetmek için Azure CLı 'de mevcut olan komutlardan bazılarını gösterir.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu öğretici, Azure CLı 'nin sürüm 2.0.20 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="example-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch hesabını oluşturur. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Daha fazla CLI etkileşimi için belirtilen Batch hesabına karşı kimlik doğrulaması yapar.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Kullanılabilir düğüm aracısı SKU’larını ve görüntü bilgilerini listeler.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | İşlem düğümleri havuzu oluşturur.  |
| [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) | Belirtilen havuzda çalışan VM’lerin sayısını yeniden boyutlandırır.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Havuzun özelliklerini görüntüler.  |
| [az batch node list](/cli/azure/batch/node#az_batch_node_list) | Belirtilen havuzdaki işlem düğümlerinin tümünü listeler.  |
| [az batch node reboot](/cli/azure/batch/node#az_batch_node_reboot) | Belirtilen işlem düğümünü yeniden başlatır.  |
| [az batch node delete](/cli/azure/batch/node#az_batch_node_delete) | Listelenen düğümleri belirtilen havuzdan siler.  |
| [az group delete](/cli/azure/group#az_group_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).
