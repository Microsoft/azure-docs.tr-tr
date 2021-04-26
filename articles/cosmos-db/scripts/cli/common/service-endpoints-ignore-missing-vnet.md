---
title: Mevcut bir Azure Cosmos hesabını sanal ağ hizmeti uç noktaları ile bağlama
description: Mevcut bir Azure Cosmos hesabını sanal ağ hizmeti uç noktaları ile bağlama
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: eae343b0ac85f3fdf6d0f6d52c7afbb91f401df4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770806"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Azure CLı kullanarak mevcut bir Azure Cosmos hesabını sanal ağ hizmet uç noktaları ile bağlama
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.9.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

Bu örnek, mevcut bir Azure Cosmos hesabını, alt ağın, parametresi kullanılarak hizmet uç noktaları için henüz yapılandırılmadığı mevcut yeni bir sanal ağa nasıl bağlayabileceğini göstermek için tasarlanmıştır `ignore-missing-vnet-service-endpoint` . Bu, Cosmos hesabının yapılandırmasının, sanal ağın alt ağının yapılandırması tamamlanmadan önce hatasız olarak tamamlanmasını sağlar. Alt ağ yapılandırması tamamlandıktan sonra, Cosmos hesabına, yapılandırılmış alt ağ üzerinden erişilebilecektir.

> [!NOTE]
> Bu örnekte bir SQL (Core) API hesabı kullanılması gösterilmektedir. Bu örneği diğer API 'Ler için kullanmak üzere `enable-virtual-network` `virtual-network-rules` API 'ye özel betiğe aşağıdaki komut dosyasına ve parametrelerini uygulayın.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Bir Azure sanal ağı oluşturur. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Azure sanal ağı için bir alt ağ oluşturur. |
| [az Network VNET subnet Show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) | Bir Azure sanal ağı için bir alt ağ döndürür. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Azure Cosmos DB hesabı oluşturur. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Bir Azure sanal ağı için bir alt ağ güncelleştirir. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

CLı Azure Cosmos DB hakkında daha fazla bilgi için bkz. [Azure Cosmos DB CLI belgeleri](/cli/azure/cosmosdb).

Tüm Azure Cosmos DB CLı betiği örnekleri [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)bulunabilir.
