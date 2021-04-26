---
title: Azure CLI Betik Örneği - Batch’te Uygulama Ekleme
description: Bu örnek betik, bir Azure Batch havuzuyla veya görevle kullanım için bir uygulamanın nasıl ekleneceğini gösterir.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06afb59a76e763c25e943c3be1531372a6bd2aa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765296"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI örneği: Azure Batch hesabına uygulama ekleme

Bu betik, kullanmak üzere bir Azure Batch havuzu veya göreviyle uygulama eklemeyi gösterir. Batch hesabınıza eklemek üzere bir uygulama ayarlamak için, yürütülebilir dosyanızı tüm bağımlılıklarıyla birlikte bir zip dosyasına paketleyin. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin sürüm 2.0.20 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="example-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır.
Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Bir depolama hesabı oluşturur. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch hesabını oluşturur. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Daha fazla CLI etkileşimi için belirtilen Batch hesabına karşı kimlik doğrulaması yapar.  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | Uygulama oluşturur.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | Belirtilen uygulamaya bir uygulama paketi ekler.  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | Bir uygulamanın özelliklerini güncelleştirir.  |
| [az group delete](/cli/azure/group#az_group_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).
