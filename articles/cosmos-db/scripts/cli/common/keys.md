---
title: Azure Cosmos hesabı için hesap anahtarlarıyla ve bağlantı dizeleriyle çalışma
description: Azure Cosmos hesabı için hesap anahtarlarıyla ve bağlantı dizeleriyle çalışma
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: bef0501704a3c9c400d998b6e84cf1cabb839dd1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770912"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>Azure CLı kullanarak bir Azure Cosmos hesabı için hesap anahtarlarıyla ve bağlantı dizeleriyle çalışma
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.9.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

Bu betik dört işlemi gösterir.

- Tüm hesap anahtarlarını Listele
- Salt okuma hesap anahtarlarını Listele
- Bağlantı dizelerini listeleme
- Hesap anahtarlarını yeniden oluşturma

> [!NOTE]
> Bu örnekte bir SQL (Core) API hesabı kullanılması gösterilmektedir, ancak hesap anahtarı ve bağlantı dizesi işlemleri Cosmos DB içindeki tüm veritabanı API 'Lerinde aynıdır.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Azure Cosmos DB hesabı oluşturur. |
| [az cosmosdb anahtarları listesi](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) | Azure Cosmos DB hesabın anahtarlarını listeleyin. |
| [az cosmosdb List-salt-Read-Keys](/cli/azure/cosmosdb#az_cosmosdb_list_read_only_keys) | Azure Cosmos DB hesabının salt okuma anahtarlarını listeleyin. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | Azure Cosmos DB hesabın bağlantı dizelerini listeleyin. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az_cosmosdb_regenerate-key) | Azure Cosmos DB hesap için anahtarları yeniden oluşturun. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

CLı Azure Cosmos DB hakkında daha fazla bilgi için bkz. [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

Tüm Azure Cosmos DB CLı betiği örnekleri [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)bulunabilir.
