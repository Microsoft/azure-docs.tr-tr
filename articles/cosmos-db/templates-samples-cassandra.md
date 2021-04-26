---
title: Azure Cosmos DB için Kaynak Yöneticisi şablonlar Cassandra API
description: Azure Cosmos DB Cassandra API oluşturmak ve yapılandırmak için Azure Resource Manager şablonları kullanın.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: eb746e8105717883b029586db2bfce8e31290e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340677"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak Azure Cosmos DB Cassandra API kaynaklarını yönetme
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Bu makalede, Azure Cosmos DB hesaplarınız, anahtar alanları ve tabloları dağıtma ve yönetmeye yardımcı olmak için Azure Resource Manager şablonlarını nasıl kullanacağınızı öğreneceksiniz.

Bu makalede yalnızca Cassandra API hesap örnekleri bulunur, diğer API türü hesaplara yönelik örnekler bulunur. bkz. [SQL](templates-samples-sql.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md), [tablo](templates-samples-table.md) makaleleri için Azure Cosmos DB API 'si ile Azure Resource Manager şablonları kullanma.

> [!IMPORTANT]
>
> * Hesap adları, tümü küçük harfle 44 karakter ile sınırlıdır.
> * Verimlilik değerlerini değiştirmek için, şablonu güncelleştirilmiş RU/s ile yeniden dağıtın.
> * Azure Cosmos hesabına konum eklediğinizde veya kaldırdığınızda, diğer özellikleri aynı anda değiştiremezsiniz. Bu işlemlerin ayrı olarak yapılması gerekir.

Aşağıdaki Azure Cosmos DB kaynaklarından herhangi birini oluşturmak için aşağıdaki örnek şablonu yeni bir JSON dosyasına kopyalayın. İsteğe bağlı olarak, farklı adlara ve değerlere sahip aynı kaynağın birden çok örneğini dağıttığınızda kullanmak üzere bir Parameters JSON dosyası oluşturabilirsiniz. [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) ve [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)dahil Azure Resource Manager şablonları dağıtmanın birçok yolu vardır.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Otomatik ölçeklendirme sağlanan aktarım hızı ile Cassandra için Azure Cosmos hesabı

Bu şablon, otomatik ölçeklendirme üretilen iş için yapılandırılmış bir anahtar alanı ve tablo ile, tutarlılık ve yük devretme seçenekleriyle iki bölgede bir Azure Cosmos hesabı oluşturur. Bu şablon, Azure hızlı başlangıç şablonları galerisinden tek tıklamayla dağıtım için de kullanılabilir.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Standart sağlanan aktarım hızı ile Cassandra için Azure Cosmos hesabı

Bu şablon, standart üretilen iş için yapılandırılmış bir anahtar uzayı ve tablo ile, tutarlılık ve yük devretme seçenekleriyle iki bölgede bir Azure Cosmos hesabı oluşturur. Bu şablon, Azure hızlı başlangıç şablonları galerisinden tek tıklamayla dağıtım için de kullanılabilir.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki ek kaynakları da inceleyebilirsiniz:

* [Azure Resource Manager belgeleri](../azure-resource-manager/index.yml)
* [Azure Cosmos DB kaynak sağlayıcısı şeması](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Ortak Azure Resource Manager Dağıtım hatalarını giderme](../azure-resource-manager/templates/common-deployment-errors.md)