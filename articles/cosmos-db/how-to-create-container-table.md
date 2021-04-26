---
title: Azure Cosmos DB Tablo API'si bir kapsayıcı oluşturma
description: Azure portal, .NET, Java, Python, Node.js ve diğer SDK 'Ları kullanarak Azure Cosmos DB Tablo API'si bir kapsayıcı oluşturmayı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101587"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Azure Cosmos DB Tablo API'si bir kapsayıcı oluşturma
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Bu makalede, Azure Cosmos DB Tablo API'si bir kapsayıcı oluşturmanın farklı yolları açıklanmaktadır. Azure portal, Azure CLı, PowerShell veya desteklenen SDK 'lar kullanılarak nasıl kapsayıcı oluşturulacağını gösterir. Bu makalede bir kapsayıcı oluşturma, Bölüm anahtarını belirtme ve üretilen iş sağlama işlemlerinin nasıl yapılacağı gösterilir.

Bu makalede, Azure Cosmos DB Tablo API'si bir kapsayıcı oluşturmanın farklı yolları açıklanmaktadır. Farklı bir API kullanıyorsanız, kapsayıcıyı oluşturmak için bkz. [MongoDB Için API](how-to-create-container-mongodb.md), [Cassandra API](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)ve [SQL API](how-to-create-container.md) makaleleri.

> [!NOTE]
> Kapsayıcılar oluştururken, aynı ada ancak büyük küçük harflere sahip iki kapsayıcı oluşturmadığınızdan emin olun. Bunun nedeni, Azure platformunun bazı bölümlerinin büyük/küçük harfe duyarlı olmaması ve bu nedenle, bu tür adlara sahip kapsayıcılar ve telemetri ve eylemlerin karışmasına ve çakışmasına neden olabilir.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Azure portalını kullanarak oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-table-dotnet.md#create-a-database-account)veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **Yeni tablo**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Tablo KIMLIĞI girin.
   * Sağlanacak bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam**’ı seçin.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Tablo API'si, tablo Ekle iletişim kutusunun ekran görüntüsü":::

> [!Note]
> Tablo API'si için, her yeni satır eklediğinizde bölüm anahtarı belirtilir.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Azure CLı kullanarak oluşturma

[Azure CLI ile tablo API'si tablo oluşturun](./scripts/cli/table/create.md). Tüm Azure Cosmos DB API 'lerde tüm Azure CLı örneklerinin listelenmesi için bkz. [Azure Cosmos DB Için Azure CLI örnekleri](cli-samples.md).

## <a name="create-using-powershell"></a>PowerShell kullanarak oluşturma

[PowerShell ile tablo API'si tablo oluşturun](./scripts/powershell/table/create.md). Tüm Azure Cosmos DB API 'lerde tüm PowerShell örneklerinin listesi için bkz. [PowerShell örnekleri](powershell-samples.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
* [Azure Cosmos DB'de İstek birimleri](request-units.md)
* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md)
* [Azure Cosmos hesabıyla çalışma](./account-databases-containers-items.md)