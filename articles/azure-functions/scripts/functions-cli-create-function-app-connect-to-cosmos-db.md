---
title: Azure Cosmos DB-Azure CLı ile işlev uygulaması oluşturma
description: Azure CLI Betiği Örneği - Azure Cosmos DB'ye bağlanan bir Azure İşlevi oluşturma
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 261ede1493b93ccd3cfed19125bbabc3be871698
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786306"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Azure Cosmos DB’ye bağlanan bir Azure İşlevi oluşturma

Bu Azure İşlevleri örnek betiği, bir işlev uygulaması oluşturur ve işlevi bir Azure Cosmos DB veritabanına bağlar. Bağlantıyı içeren oluşturulan uygulama ayarı, [Azure Cosmos DB tetikleyicisi veya bağlaması](../functions-bindings-cosmosdb.md) ile birlikte kullanılabilir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="sample-script"></a>Örnek betik

Bu örnek, bir Azure İşlevi uygulaması oluşturur ve uygulama ayarlarına Cosmos DB uç noktası ve erişim anahtarı ekler.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır: Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Konum ile bir kaynak grubu oluşturun |
| [az storage accounts create](/cli/azure/storage/account#az_storage_account_create) | Depolama hesabı oluşturma |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Sunucusuz [Tüketim planında](../consumption-plan.md)bir işlev uygulaması oluşturur. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Azure Cosmos DB veritabanı oluşturun. |
| [az cosmosdb Show](/cli/azure/cosmosdb#az_cosmosdb_show)| Veritabanı hesabı bağlantısını alır. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az_cosmosdb_list_keys)| Veritabanı için anahtarları alır. |
| [az functionapp config appSettings kümesi](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | İşlev uygulamasındaki uygulama ayarı olarak bağlantı dizesini ayarlar. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure İşlevleri CLI betiği örnekleri, [Azure İşlevleri belgelerinde](../functions-cli-samples.md) bulunabilir.
