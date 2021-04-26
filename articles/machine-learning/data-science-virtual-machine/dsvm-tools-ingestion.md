---
title: Veri alma araçları
titleSuffix: Azure Data Science Virtual Machine
description: Veri Bilimi Sanal Makinesi önceden yüklenmiş veri alma araçları ve yardımcı programları hakkında bilgi edinin.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 21a6efa8108bfd0a317eb955e8b3ffcfba0862a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519380"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Veri Bilimi Sanal Makinesi veri alımı araçları

Bir veri bilimi veya AI projesindeki ilk teknik adımlardan biri olarak kullanılacak veri kümelerini tanımlamalısınız ve bunları analiz ortamınıza getirebilirsiniz. Veri Bilimi Sanal Makinesi (DSVM), farklı kaynaklardan gelen verileri DSVM 'de yerel olarak veya bulutta ya da şirket içinde bulunan bir veri platformuna getirmek için araçlar ve kitaplıklar sağlar.

DSVM 'de bulunan bazı veri taşıma araçları aşağıda verilmiştir.

## <a name="adlcopy"></a>AdlCopy

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Verileri Azure Blob depolamadan Azure Data Lake Store 'e kopyalamak için bir araç. Ayrıca, verileri iki Azure Data Lake Store hesabı arasında kopyalayabilir.      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Azure Blob depolamadan birden çok blob 'u Azure Data Lake Store içine aktarma.      |
|  Nasıl kullanılır/çalıştırılır?    |   Yardım almak için bir komut istemi açın ve yazın `adlcopy` .    |
| Örneklere bağlantılar      | [AdlCopy kullanma](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| DSVM 'deki ilgili araçlar      | AzCopy, Azure CLı     |

## <a name="azure-cli"></a>Azure CLI’si

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Azure için bir yönetim aracı. Ayrıca Azure Blob depolama ve Azure Data Lake Store gibi Azure veri platformlarından verileri taşımak için komut fiilleri de içerir.     |
| Desteklenen DSVM sürümleri      | Windows, Linux     |
| Tipik kullanımlar      | Azure depolama ve Azure Data Lake Store verileri içeri ve dışarı aktarma.      |
|  Nasıl kullanılır/çalıştırılır?    |   Yardım almak için bir komut istemi açın ve yazın `az` .    |
| Örneklere bağlantılar      | [Azure CLI’yı kullanma](/cli/azure)     |
| DSVM 'deki ilgili araçlar      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Yerel dosyalara, Azure Blob depolama, dosyalar ve tablolar arasında veri kopyalamak için bir araç.      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Azure Blob depolama alanına dosya kopyalama ve BLOB 'ları hesaplar arasında kopyalama.      |
|  Nasıl kullanılır/çalıştırılır?    |   Yardım almak için bir komut istemi açın ve yazın `azcopy` .    |
| Örneklere bağlantılar      | [Windows üzerinde AzCopy](../../storage/common/storage-use-azcopy-v10.md)      |
| DSVM 'deki ilgili araçlar      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB veri geçiş aracı

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Bulutta bir NoSQL veritabanı olan Azure Cosmos DB çeşitli kaynaklardan veri içeri aktarma aracı. Bu kaynaklar JSON dosyalarını, CSV dosyalarını, SQL, MongoDB, Azure Tablo depolama, Amazon DynamoDB ve Azure Cosmos DB SQL API koleksiyonlarını içerir.      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Bir VM 'den CosmosDB 'ye dosya aktarma, Azure Tablo depolama alanından CosmosDB 'ye veri aktarma ve bir Microsoft SQL Server veritabanından CosmosDB 'ye veri aktarma.     |
|  Nasıl kullanılır/çalıştırılır?    |   Komut satırı sürümünü kullanmak için bir komut istemi açın ve yazın `dt` . GUI aracını kullanmak için bir komut istemi açın ve yazın `dtui` .    |
| Örneklere bağlantılar      | [CosmosDB verileri Içeri aktarma](../../cosmos-db/import-data.md)      |
| DSVM 'deki ilgili araçlar      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Depolama Gezgini

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Azure bulutunda depolanan dosyalarla etkileşim kurmak için grafik kullanıcı arabirimi. |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | DSVM 'den verileri içeri ve dışarı aktarma.    |
|  Nasıl kullanılır/çalıştırılır?    | Başlat menüsünde "Azure Depolama Gezgini" araması yapın. |
| Örneklere bağlantılar      | [Azure Depolama Gezgini](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | SQL Server ve bir veri dosyası arasında veri kopyalamak için SQL Server araç.      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Bir CSV dosyasını SQL Server tablosuna aktarma ve bir SQL Server tablosunu bir dosyaya aktarma.      |
|  Nasıl kullanılır/çalıştırılır?    |   Yardım almak için bir komut istemi açın ve yazın `bcp` .    |
| Örneklere bağlantılar      | [BCP yardımcı programı](/sql/tools/bcp-utility)      |
| DSVM 'deki ilgili araçlar      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobsigortası

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Linux dosya sistemine bir Azure Blob depolama kapsayıcısını bağlamak için bir araç.      |
| Desteklenen DSVM sürümleri      | Linux      |
| Tipik kullanımlar      | Bir kapsayıcıdaki Blobları okuma ve yazma.      |
|  Nasıl kullanılır ve çalıştırılır?    |   Bir terminalde _blobsigortası_ çalıştırın.    |
| Örneklere bağlantılar      | [GitHub 'da blobsigortası](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 'deki ilgili araçlar      | Azure CLI      |