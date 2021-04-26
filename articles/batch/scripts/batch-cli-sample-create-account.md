---
title: Azure CLı betik örneği-Batch hesabı oluşturma-Batch hizmeti
description: Bu betik Batch hizmeti modunda bir Azure Batch hesabı oluşturur ve hesabın çeşitli özelliklerini sorgulamayı veya güncelleştirmeyi gösterir.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3d978cf56e92abf9824c116e51f94d53bbe14bbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768363"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI örneği: Batch hizmeti modunda Batch hesabı oluşturma

Bu betik Batch hizmeti modunda bir Azure Batch hesabı oluşturur ve hesabın çeşitli özelliklerini sorgulamayı veya güncelleştirmeyi gösterir. Varsayılan Batch hizmeti modunda bir Batch hesabı oluşturduğunuzda, işlem düğümleri Batch hizmeti tarafından dahili olarak atanır. Ayrılmış işlem düğümleri ayrı bir vCPU (çekirdek) kotasına tabidir ve hesap paylaşılan anahtar kimlik bilgileri veya bir Azure Active Directory belirteci aracılığıyla doğrulanabilir.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu öğretici, Azure CLı 'nin sürüm 2.0.20 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="example-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Bir depolama hesabı oluşturur. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Batch hesabının özelliklerini güncelleştirir.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Belirtilen Batch hesabının ayrıntılarını alır.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Belirtilen Batch hesabının erişim anahtarlarını alır.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Daha fazla CLI etkileşimi için belirtilen Batch hesabına karşı kimlik doğrulaması yapar.  |
| [az group delete](/cli/azure/group#az_group_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).
