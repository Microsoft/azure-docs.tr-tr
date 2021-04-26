---
title: Data Lake Storage 1. diğer Azure hizmetleriyle tümleştirin
description: Azure Data Lake Storage 1. diğer Azure hizmetleriyle nasıl tümleştirebileceğinizi anlayın.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 929853f4dbedca7034c8e2a51e6231651a2dd08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461663"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Azure Data Lake Storage 1. diğer Azure hizmetleriyle tümleştirme
Azure Data Lake Storage 1., daha geniş bir senaryoyu etkinleştirmek için diğer Azure hizmetleriyle birlikte kullanılabilir. Aşağıdaki makalede Data Lake Storage 1. ile tümleştirilebilen hizmetler listelenmektedir.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Azure HDInsight ile Data Lake Storage 1. kullanma
Data Lake Storage 1. kullanan bir [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) kümesi ile, Bu sürümde, Windows ve Linux 'ta Hadoop ve fırtınası kümeleri için Data Lake Storage 1. yalnızca ek depolama alanı olarak kullanabilirsiniz. Bu kümeler, varsayılan depolama alanı olarak Azure Storage 'ı (ıLB) kullanır. Ancak, Windows ve Linux 'ta HBase kümeleri için Data Lake Storage 1. varsayılan depolama veya ek depolama ya da her ikisi olarak kullanabilirsiniz.

Data Lake Storage 1. ile HDInsight kümesi sağlama hakkında yönergeler için bkz.:

* [Azure portal kullanarak Data Lake Storage 1. bir HDInsight kümesi sağlama](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure PowerShell kullanarak Data Lake Storage 1. varsayılan depolama alanı olarak bir HDInsight kümesi sağlama](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Azure PowerShell kullanarak Data Lake Storage 1. ek depolama alanı olarak HDInsight kümesi sağlama](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Azure Data Lake Analytics ile Data Lake Storage 1. kullanma
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) , bulut ölçeğinde büyük verilerle çalışmanıza olanak sağlar. Kaynakları dinamik olarak sağlar ve desteklenen bir dizi veri kaynağında depolanabilecek Data Lake Storage 1. terabayt veya hatta eksabaytlarca üzerinde analiz etmenizi sağlar, bunlardan biri. Data Lake Analytics, büyük veri iş yükleriniz için en yüksek performans, aktarım hızı ve paralelleştirme düzeyini sağlayan Data Lake Storage 1. ile çalışmak üzere özel olarak iyileştirilmiştir.

Data Lake Analytics Data Lake Storage 1. ile birlikte kullanma hakkında yönergeler için bkz. [Data Lake Storage 1. kullanarak Data Lake Analytics kullanmaya başlama](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Azure Data Factory ile Data Lake Storage 1. kullanma
Azure tablolarından, Azure SQL veritabanı, Azure SQL veri ambarı, Azure depolama Blobları ve şirket içi veritabanlarından verileri almak için [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) kullanabilirsiniz. Azure ekosisteminde bir ilk sınıf olan vatandaşlık, bu Data Lake Storage 1. kaynaktaki verilerin içeri alımını düzenlemek için Azure Data Factory kullanılabilir.

Azure Data Factory Data Lake Storage 1. ile birlikte kullanma hakkında yönergeler için, bkz. [Data Lake Storage 1. Data Factory kullanarak verileri taşıma](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Azure depolama Bloblarından veri kopyalama Data Lake Storage 1.
Azure Data Lake Storage 1., Azure Blob depolamadan bir Data Lake Storage 1. hesabına veri kopyalamanızı sağlayan bir komut satırı aracı olan AdlCopy sağlar. Daha fazla bilgi için bkz. [Azure Storage Bloblarından veri kopyalama Data Lake Storage 1.](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Azure SQL veritabanı ve Data Lake Storage 1. arasında veri kopyalama
Verileri Azure SQL veritabanı ve Data Lake Storage 1. arasında içeri ve dışarı aktarmak için Apache Sqoop 'yi kullanabilirsiniz. Daha fazla bilgi için bkz. [Sqoop kullanarak Data Lake Storage 1. ve Azure SQL veritabanı arasında veri kopyalama](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Stream Analytics ile Data Lake Storage 1. kullanma
Azure Stream Analytics kullanılarak akan verileri depolamak için çıktılardan biri olarak Data Lake Storage 1. kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Stream Analytics kullanarak Data Lake Storage 1. Azure Depolama Blobu veri akışı](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Power BI ile Data Lake Storage 1. kullanma
Verileri analiz etmek ve görselleştirmek için bir Data Lake Storage 1. hesabındaki verileri içeri aktarmak üzere Power BI kullanabilirsiniz. Daha fazla bilgi için [Power BI kullanarak Data Lake Storage 1. verileri çözümleme](data-lake-store-power-bi.md)bölümüne bakın.

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Veri Kataloğu ile Data Lake Storage 1. kullanma
Verileri kuruluş genelinde bulunabilir hale getirmek için Azure Veri Kataloğu 'na Data Lake Storage 1. verileri kaydedebilirsiniz. Daha fazla bilgi için bkz. [Azure Veri Kataloğu 'nda Data Lake Storage 1. verileri kaydetme](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>SQL Server Integration Services (SSIS) ile Data Lake Storage 1. kullanma
SSIS ' de Data Lake Storage 1. bağlantı yöneticisini kullanarak bir SSIS paketini Data Lake Storage 1. bağlayabilirsiniz. Daha fazla bilgi için bkz. [SSIS ile Data Lake Storage 1. kullanma](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Azure SYNAPSE Analytics ile Data Lake Storage 1. kullanma
PolyBase 'i Data Lake Storage 1. Azure SYNAPSE Analytics 'e veri yüklemek için kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics ile Data Lake Storage 1. kullanma](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Azure Event Hubs ile Data Lake Storage 1. kullanma
Azure Event Hubs tarafından alınan verileri arşivlemek ve yakalamak için Azure Data Lake Storage 1. kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Event Hubs ile Data Lake Storage 1. kullanma](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Data Lake Storage 1. genel bakış](data-lake-store-overview.md)
* [Portalı kullanarak Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-portal.md)
* [PowerShell kullanarak Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-powershell.md)