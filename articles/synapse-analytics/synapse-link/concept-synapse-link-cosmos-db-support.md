---
title: Azure Cosmos DB için Azure Synapse Link'in desteklenen özellikleri
description: Azure Cosmos DB için Azure SYNAPSE bağlantısı tarafından desteklenen eylemlerin güncel listesini anlayın
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: cdc9f344e108fc58399f9bcb2a9f02a4659ecfe1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627685"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Azure Cosmos DB için Azure Synapse Link'in desteklenen özellikleri

Bu makalede Azure Cosmos DB için Azure Synapse Link'te desteklenen işlevler anlatılmıştır.

## <a name="azure-synapse-support"></a>Azure SYNAPSE desteği

Azure Cosmos DB iki tür kapsayıcı vardır:
* HTAP kapsayıcısı-SYNAPSE bağlantısı etkinleştirilmiş bir kapsayıcı. Bu kapsayıcıda hem işlem deposu hem de analitik depo vardır. 
* OLTP kapsayıcısı-Synaspe bağlantısı olan bir kapsayıcı etkin değil. Bu kapsayıcıda yalnızca işlem deposu vardır ve analitik depo yoktur.

> [!IMPORTANT]
> Azure Cosmos DB için Azure SYNAPSE bağlantısı şu anda yönetilen sanal ağ etkinleştirilmemiş SYNAPSE çalışma alanlarında destekleniyor. 

SYNAPSE bağlantısını etkinleştirmeden bir Azure Cosmos DB kapsayıcısına bağlanabilirsiniz. Bu senaryoda, yalnızca işlem deposuna okuma/yazma yapabilirsiniz. Aşağıda, Azure Cosmos DB için SYNAPSE link içindeki şu anda desteklenen özelliklerin bir listesi verilmiştir. 

| Kategori              | Açıklama |[Apache Spark havuzu](../sql/on-demand-workspace-overview.md) | [Sunucusuz SQL havuzu](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Çalışma zamanı desteği** |Azure Cosmos DB erişmek için desteklenen Azure SYNAPSE çalışma zamanı| ✓ | ✓ |
| **Azure Cosmos DB API desteği** | Desteklenen Azure Cosmos DB API türü | SQL/MongoDB | SQL/MongoDB |
| **Nesne**  |Oluşturulabilen ve Azure Cosmos DB kapsayıcıya işaret eden bir tablo gibi nesneler| Veri çerçevesi, görünüm, tablo | Görünüm |
| **Okuyamaz**    | Okunabilecek Azure Cosmos DB kapsayıcısı türü | OLTP/HTAP | HTAP  |
| **Yazarken**   | Azure Cosmos DB kapsayıcısına veri yazmak için Azure SYNAPSE çalışma zamanı kullanılabilir | Yes | Hayır |

* Spark 'tan bir Azure Cosmos DB kapsayıcısına veri yazarsanız, bu işlem Azure Cosmos DB işlem deposu üzerinden gerçekleşir. Istek birimlerini tüketerek Azure Cosmos DB işlem performansını etkiler.
* Dış tablolar aracılığıyla adanmış SQL havuzu tümleştirmesi Şu anda desteklenmiyor.
 
## <a name="supported-code-generated-actions-for-spark"></a>Spark için desteklenen kod tarafından oluşturulan eylemler

| Hareket              | Description |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **DataFrame 'e yükle** |Spark DataFrame 'e veri yükleme ve verileri okuma |✓| ✓ |
| **Spark tablosu oluşturma** |Azure Cosmos DB kapsayıcısına işaret eden tablo oluşturma|✓| ✓ |
| **Veri çerçevesini kapsayıcıya yaz** |Bir kapsayıcıya veri yazma|✓| ✓ |
| **Kapsayıcıdan akış veri çerçevesini yükleme** |Azure Cosmos DB değişiklik akışını kullanarak veri akışı|✓| ✓ |
| **Akış veri çerçevesini kapsayıcıya yaz** |Azure Cosmos DB değişiklik akışını kullanarak veri akışı|✓| ✓ |

## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Sunucusuz SQL havuzu için desteklenen kod tarafından oluşturulan eylemler

| Hareket              | Description |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Verileri inceleme** |Tanıdık T-SQL söz dizimi ve otomatik Şema çıkarımı ile bir kapsayıcıdaki verileri araştırma|X| ✓ |
| **Görünümler oluşturma ve bı raporları oluşturma** |Sunucusuz SQL havuzu aracılığıyla bı için bir kapsayıcıya doğrudan erişim sağlamak üzere bir SQL görünümü oluşturun |X| ✓ |
| **Cosmos DB verilerle birlikte farklı veri kaynaklarını birleştirin** | Azure Blob depolama 'daki verilerle birlikte Cosmos DB kapsayıcılarından ve CETAS kullanarak Azure Data Lake Storage veri okuma sonuçlarını depolayın |X| ✓ |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB için bkz. SYNAPSE bağlantısına bağlanma](../quickstart-connect-synapse-link-cosmos-db.md)
* [Spark ile Cosmos DB analitik depoyu sorgulama hakkında bilgi edinin](how-to-query-analytical-store-spark.md)