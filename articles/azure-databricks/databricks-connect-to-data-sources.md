---
title: 'Azure Databricks farklı veri kaynaklarına bağlanma '
description: Azure Databricks Azure SQL veritabanı, Azure Data Lake Store, BLOB depolama, Cosmos DB, Event Hubs ve Azure SQL veri ambarı 'na nasıl bağlanacağınızı öğrenin.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129394"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Azure Databricks veri kaynaklarına bağlanma

Bu makalede, Azure 'daki Azure Databricks bağlı olabilecek tüm farklı veri kaynaklarına bağlantılar sağlanmaktadır. Bu bağlantılardaki örnekleri izleyerek Azure veri kaynaklarından (örneğin, Azure Blob depolama, Azure Event Hubs, vb.) verileri bir Azure Databricks kümesine ayıklayın ve analitik işleri bunlar üzerinde çalıştırın. 

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure Databricks çalışma alanınız ve Spark kümeniz olmalıdır. [Azure Databricks kullanmaya başlama](quickstart-create-databricks-workspace-portal.md)yönergelerini izleyin.

## <a name="data-sources-for-azure-databricks"></a>Azure Databricks için veri kaynakları

Aşağıdaki liste, Azure Databricks ile kullanabileceğiniz Azure 'daki veri kaynaklarını sağlar. Azure Databricks ile kullanılabilecek veri kaynaklarının tamamen listesi için bkz. [Azure Databricks Için veri kaynakları](/azure/databricks/data/data-sources/index).

- [Azure SQL veritabanı](/azure/databricks/data/data-sources/sql-databases)

    Bu bağlantı, JDBC kullanarak SQL veritabanlarına bağlanmak için veri çerçevesi API 'sini ve JDBC arabirimi aracılığıyla okumaların paralelliğini denetleme olanağı sağlar. Bu konu, sonunda kısaltılmış Python ve Spark SQL örnekleri ile Scala API 'sini kullanarak ayrıntılı örnekler sağlar.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Bu bağlantı, Azure Data Lake Storage kimlik doğrulamak için Azure Active Directory hizmet sorumlusunu kullanma hakkında örnekler sağlar. Ayrıca, Azure Databricks Azure Data Lake Storage verilere nasıl erişileceğini gösteren yönergeler de sağlar.

- [Azure Blob Depolama](/azure/databricks/data/data-sources/azure/azure-storage)

    Bu bağlantı, belirli bir kapsayıcı için erişim anahtarını veya SAS 'yi kullanarak Azure Databricks Azure Blob depolamaya doğrudan erişme hakkında örnekler sağlar. Bağlantı Ayrıca, RDD API kullanarak Azure Databricks Azure Blob depolamaya nasıl erişeceğimiz hakkında bilgi de sağlar.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Bu bağlantı, Azure Cosmos DB verilere erişmek için Azure Databricks [Azure Cosmos DB Spark bağlayıcısının](https://github.com/Azure/azure-cosmosdb-spark) nasıl kullanılacağına ilişkin yönergeler sağlar.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Bu bağlantı, Azure Event Hubs 'deki verilere erişmek için Azure Databricks [azure Event Hubs Spark bağlayıcısının](https://github.com/Azure/azure-event-hubs-spark) nasıl kullanılacağına ilişkin yönergeler sağlar.

- [Azure SQL Veri Ambarı](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Bu bağlantı, Azure Databricks bağlantı kurmak için Azure SQL veri ambarı bağlayıcısının nasıl kullanılacağına ilişkin yönergeler sağlar.
    

## <a name="next-steps"></a>Sonraki adımlar

Azure Databricks verileri içeri aktarabileceğiniz kaynaklar hakkında bilgi edinmek için bkz. [Azure Databricks Için veri kaynakları](/azure/databricks/data/data-sources/index).


