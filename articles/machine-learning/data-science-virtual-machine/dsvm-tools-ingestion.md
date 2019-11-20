---
title: Veri alma araçları
titleSuffix: Azure Data Science Virtual Machine
description: Veri Bilimi Sanal Makinesi önceden yüklenmiş veri alma araçları ve yardımcı programları hakkında bilgi edinin.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 72a881a2ab508caa2274f3b9299157921cb8ceff
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330686"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Veri Bilimi Sanal Makinesi veri alımı araçları

Bir veri bilimi veya AI projesindeki ilk teknik adımlardan biri olarak kullanılacak veri kümelerini tanımlamalısınız ve bunları analiz ortamınıza getirebilirsiniz. Veri Bilimi Sanal Makinesi (DSVM), farklı kaynaklardan gelen verileri DSVM 'de yerel olarak veya bulutta ya da şirket içinde bulunan bir veri platformuna getirmek için araçlar ve kitaplıklar sağlar.

DSVM 'de bulunan bazı veri taşıma araçları aşağıda verilmiştir.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Nedir?   | Verileri Azure Blob depolamadan Azure Data Lake Store 'e kopyalamak için bir araç. Ayrıca, verileri iki Azure Data Lake Store hesabı arasında kopyalayabilir.      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Azure Blob depolamadan birden çok blob 'u Azure Data Lake Store içine aktarma.      |
|  Nasıl kullanılır/çalıştırılır?    |   Yardım almak için bir komut istemi açın ve `adlcopy` yazın.    |
| Örneklere bağlantılar      | [AdlCopy’yi kullanma](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM 'deki ilgili araçlar      | AzCopy, Azure CLı     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Nedir?   | Azure için bir yönetim aracı. Ayrıca Azure Blob depolama ve Azure Data Lake Store gibi Azure veri platformlarından verileri taşımak için komut fiilleri de içerir.     |
| Desteklenen DSVM sürümleri      | Windows, Linux     |
| Tipik kullanımlar      | Azure depolama ve Azure Data Lake Store verileri içeri ve dışarı aktarma.      |
|  Nasıl kullanılır/çalıştırılır?    |   Yardım almak için bir komut istemi açın ve `az` yazın.    |
| Örneklere bağlantılar      | [Azure CLI’yi kullanma](https://docs.microsoft.com/cli/azure)     |
| DSVM 'deki ilgili araçlar      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Nedir?   | Yerel dosyalara, Azure Blob depolama, dosyalar ve tablolar arasında veri kopyalamak için bir araç.      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Azure Blob depolama alanına dosya kopyalama ve BLOB 'ları hesaplar arasında kopyalama.      |
|  Nasıl kullanılır/çalıştırılır?    |   Yardım almak için bir komut istemi açın ve `azcopy` yazın.    |
| Örneklere bağlantılar      | [Windows üzerinde AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| DSVM 'deki ilgili araçlar      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB veri geçiş aracı

|    |           |
| ------------- | ------------- |
| Nedir?   | Bulutta bir NoSQL veritabanı olan Azure Cosmos DB çeşitli kaynaklardan veri içeri aktarma aracı. Bu kaynaklar JSON dosyalarını, CSV dosyalarını, SQL, MongoDB, Azure Tablo depolama, Amazon DynamoDB ve Azure Cosmos DB SQL API koleksiyonlarını içerir.      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Bir VM 'den CosmosDB 'ye dosya aktarma, Azure Tablo depolama alanından CosmosDB 'ye veri aktarma ve bir Microsoft SQL Server veritabanından CosmosDB 'ye veri aktarma.     |
|  Nasıl kullanılır/çalıştırılır?    |   Komut satırı sürümünü kullanmak için bir komut istemi açın ve `dt` yazın. GUI aracını kullanmak için bir komut istemi açın ve `dtui` yazın.    |
| Örneklere bağlantılar      | [CosmosDB verileri Içeri aktarma](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| DSVM 'deki ilgili araçlar      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Depolama Gezgini

|    |           |
| ------------- | ------------- |
| Nedir?   | Azure bulutunda depolanan dosyalarla etkileşim kurmak için grafik kullanıcı arabirimi. |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | DSVM 'den verileri içeri ve dışarı aktarma.    |
|  Nasıl kullanılır/çalıştırılır?    | Başlat menüsünde "Azure Depolama Gezgini" araması yapın. |
| Örneklere bağlantılar      | [Azure Depolama Gezgini](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Nedir?   | SQL Server ve bir veri dosyası arasında veri kopyalamak için SQL Server araç.      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Bir CSV dosyasını SQL Server tablosuna aktarma ve bir SQL Server tablosunu bir dosyaya aktarma.      |
|  Nasıl kullanılır/çalıştırılır?    |   Yardım almak için bir komut istemi açın ve `bcp` yazın.    |
| Örneklere bağlantılar      | [BCP yardımcı programı](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| DSVM 'deki ilgili araçlar      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobsigortası

|    |           |
| ------------- | ------------- |
| Nedir?   | Linux dosya sistemine bir Azure Blob depolama kapsayıcısını bağlamak için bir araç.      |
| Desteklenen DSVM sürümleri      | Linux      |
| Tipik kullanımlar      | Bir kapsayıcıdaki Blobları okuma ve yazma.      |
|  Nasıl kullanılır ve çalıştırılır?    |   Bir terminalde _blobsigortası_ çalıştırın.    |
| Örneklere bağlantılar      | [GitHub 'da blobsigortası](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 'deki ilgili araçlar      | Azure CLI      |
