---
title: Kaynak Yöneticisi şablonlarla Azure Cosmos DB oluşturma ve yönetme
description: Çekirdek (SQL) API için Azure Cosmos DB oluşturmak ve yapılandırmak üzere Azure Resource Manager şablonları kullanma
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: dd70e196180c68d6a498d147493411e6d1703e59
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034053"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla Azure Cosmos DB Core (SQL) API kaynaklarını yönetme

[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Azure Cosmos DB hesaplarınızı, veritabanlarınızı ve Kapsayıcılarınızı dağıtmayı ve yönetmeyi kolaylaştırmak için Azure Resource Manager şablonlarını nasıl kullanacağınızı öğreneceksiniz.

Bu makalede, çekirdek (SQL) API hesapları için yalnızca Azure Resource Manager şablon örnekleri gösterilmektedir. [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md)ve [tablo](templates-samples-table.md) API 'leri için şablon örnekleri de bulabilirsiniz.

> [!IMPORTANT]
>
> * Hesap adları, tümü küçük harfle 44 karakter ile sınırlıdır.
> * Verimlilik değerlerini değiştirmek için, şablonu güncelleştirilmiş RU/s ile yeniden dağıtın.
> * Azure Cosmos hesabına konum eklediğinizde veya kaldırdığınızda, diğer özellikleri aynı anda değiştiremezsiniz. Bu işlemlerin ayrı olarak yapılması gerekir.
> * Azure Cosmos DB kaynaklar, Azure Resource Manager kaynak URI 'leriyle nasıl çalıştığını ihlal ettiğinden yeniden adlandırılamaz.

Aşağıdaki Azure Cosmos DB kaynaklarından herhangi birini oluşturmak için aşağıdaki örnek şablonu yeni bir JSON dosyasına kopyalayın. İsteğe bağlı olarak, farklı adlara ve değerlere sahip aynı kaynağın birden çok örneğini dağıttığınızda kullanmak üzere bir Parameters JSON dosyası oluşturabilirsiniz. [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) ve [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)dahil Azure Resource Manager şablonları dağıtmanın birçok yolu vardır.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Otomatik ölçeklendirme işleme ile Azure Cosmos hesabı

Bu şablon, çoğu ilke seçeneğinin etkinleştirildiği otomatik ölçeklendirme üretilen işi için yapılandırılmış veritabanı ve kapsayıcı ile iki bölgede tutarlılık ve yük devretme seçenekleriyle bir Azure Cosmos hesabı oluşturur. Bu şablon, Azure hızlı başlangıç şablonları galerisinden tek tıklamayla dağıtım için de kullanılabilir.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Analitik depo ile Azure Cosmos hesabı

Bu şablon, analitik TTL etkin bir kapsayıcı ve el ile veya otomatik ölçeklendirme üretilen iş için seçenekler içeren bir bölgede bir Azure Cosmos hesabı oluşturur. Bu şablon, Azure hızlı başlangıç şablonları galerisinden tek tıklamayla dağıtım için de kullanılabilir.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Standart sağlanan aktarım hızı ile Azure Cosmos hesabı

Bu şablon, çoğu ilke seçeneğinin etkinleştirildiği standart aktarım hızı için yapılandırılmış veritabanı ve kapsayıcı ile iki bölgede tutarlılık ve yük devretme seçenekleriyle bir Azure Cosmos hesabı oluşturur. Bu şablon, Azure hızlı başlangıç şablonları galerisinden tek tıklamayla dağıtım için de kullanılabilir.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Sunucu tarafı işlevselliğiyle kapsayıcı Azure Cosmos DB

Bu şablon bir Azure Cosmos hesabı, veritabanı ve kapsayıcı ile saklı yordam, tetikleyici ve Kullanıcı tanımlı işlev oluşturur. Bu şablon, Azure hızlı başlangıç şablonları galerisinden tek tıklamayla dağıtım için de kullanılabilir.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Azure AD ve RBAC ile Azure Cosmos DB hesabı

Bu şablon bir SQL Cosmos hesabı, yerel olarak tutulan bir rol tanımı ve AAD kimliği için yerel olarak tutulan bir rol ataması oluşturacak. Bu şablon, Azure hızlı başlangıç şablonları galerisinden tek tıklamayla dağıtım için de kullanılabilir.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-rbac%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-rbac/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Ücretsiz katman Azure Cosmos DB hesabı

Bu şablon, serbest katmanlı bir Azure Cosmos hesabı ve en fazla 25 kapsayıcı ile paylaşılabilen paylaşılan aktarım hızı içeren bir veritabanı oluşturur. Bu şablon, Azure hızlı başlangıç şablonları galerisinden tek tıklamayla dağıtım için de kullanılabilir.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki ek kaynakları da inceleyebilirsiniz:

* [Azure Resource Manager belgeleri](../azure-resource-manager/index.yml)
* [Azure Cosmos DB kaynak sağlayıcısı şeması](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Ortak Azure Resource Manager Dağıtım hatalarını giderme](../azure-resource-manager/templates/common-deployment-errors.md)