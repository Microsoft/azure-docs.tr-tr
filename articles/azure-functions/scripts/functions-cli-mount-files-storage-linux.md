---
title: Bir Python işlev uygulamasına dosya paylaşma bağlama-Azure CLı
description: Azure CLı kullanarak sunucusuz bir Python işlev uygulaması oluşturun ve var olan bir dosya paylaşımının bağlama.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762290"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Azure CLı kullanarak bir Python işlev uygulamasına bir dosya paylaşma bağlama

Bu Azure Işlevleri örnek betiği, bir işlev uygulaması oluşturur ve Azure dosyalarında bir paylaşma oluşturur. Bunlar, verilere işlevleriniz tarafından erişilebilmesi için paylaşımdan takar.  

>[!NOTE]
>Oluşturulan işlev uygulaması Python sürüm 3,7 ' de çalışır. Azure Işlevleri Ayrıca [, 3,6 ve 3,8 Python sürümlerini de destekler](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="sample-script"></a>Örnek betik

Bu betik, Azure Işlevleri 'nde [Tüketim planını](../consumption-plan.md)kullanarak bir işlev uygulaması oluşturur.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Tablodaki her komut, komuta özgü belgelere yönlendirir. Bu betik şu komutları kullanır:

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Azure Depolama hesabı oluşturur. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Bir işlev uygulaması oluşturur. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | Depolama hesabında bir Azure dosya paylaşma oluşturur. | 
| [az Storage Directory Create](/cli/azure/storage/directory#az_storage_directory_create) | Paylaşımda bir dizin oluşturur. |
| [az WebApp config Storage-Account Add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | , Paylaşılan uygulamayı işlev uygulamasına bağlar. |
| [az WebApp config Storage-Account List](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | İşlev uygulamasına bağlanan dosya paylaşımlarını gösterir. | 

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure İşlevleri CLI betiği örnekleri, [Azure İşlevleri belgelerinde](../functions-cli-samples.md) bulunabilir.
