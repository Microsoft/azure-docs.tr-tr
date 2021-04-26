---
title: Data Lake Storage 1. içeren veri senaryoları | Microsoft Docs
description: Data Lake Storage 1. (daha önce Azure Data Lake Store olarak bilinirdi) hangi verilerin hangi verilerle içeri ve nasıl işlenebileceğini, işlendiğini, indirileceğini ve görselleştirdiğini anlayın.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0bd895b0c19293a199b2a9b135915b7dc45b9e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702506"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Büyük veri gereksinimleri için Azure Data Lake Storage 1. kullanma

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Büyük veri işlemede dört ana aşama vardır:

* Büyük miktarlarda veriyi bir veri deposuna gerçek zamanlı veya toplu iş halinde geri ödeme
* Verileri işleme
* Veriler indiriliyor
* Verileri görselleştirme

Bu makalede, büyük veri gereksinimlerinizi karşılayacak seçenekleri ve araçları anlamak için Azure Data Lake Storage 1. göre bu aşamalara göz atacağız.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Data Lake Storage 1. verileri alma
Bu bölümde, farklı veri kaynakları ve verilerin Data Lake Storage 1. hesaba alınmasının farklı yolları vurgulanmaktadır.

![Data Lake Storage 1. verileri alma](./media/data-lake-store-data-scenarios/ingest-data.png "Data Lake Storage 1. verileri alma")

### <a name="ad-hoc-data"></a>Geçici veri
Bu, büyük bir veri uygulamasını prototip yazmak için kullanılan daha küçük veri kümelerini temsil eder. Veri kaynağına bağlı olarak, geçici verileri almanın farklı yolları vardır.

| Veri Kaynağı | Şunu kullanarak Al |
| --- | --- |
| Yerel bilgisayar |<ul> <li>[Azure portalındaki](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Visual Studio için Data Lake araçları kullanma](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Depolama Blobu |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy aracı](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight kümesinde çalışan DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Akışlı veriler
Bu, uygulamalar, cihazlar, algılayıcılar vb. gibi çeşitli kaynaklarla oluşturulabilecek verileri temsil eder. Bu veriler çeşitli araçlarla Data Lake Storage 1. alınabilir. Bu araçlar genellikle verileri bir olay temelinde gerçek zamanlı olarak yakalayıp işleyerek, daha sonra işlenebilmeleri için olayları toplu işlemlere Data Lake Storage 1. olarak yazar.

Kullanabileceğiniz araçlar aşağıda verilmiştir:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md) -Event Hubs alınan olaylar, Azure Data Lake Storage 1. çıktısı kullanılarak Azure Data Lake Storage 1. yazılabilir.
* [Azure HDInsight fırtınası](../hdinsight/storm/apache-storm-write-data-lake-store.md) -fırtınası kümesinden Data Lake Storage 1. doğrudan veri yazabilirsiniz.
* [Eventprocessorhost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) : Event Hubs olayları alabilir ve [Data Lake Storage 1. .net SDK](data-lake-store-get-started-net-sdk.md)kullanarak Data Lake Storage 1. yazabilirsiniz.

### <a name="relational-data"></a>İlişkisel veriler
İlişkisel veritabanlarındaki verileri de kaynak olarak kullanabilirsiniz. İlişkisel veritabanları, bir süre boyunca büyük miktarlarda veri toplar ve bu da büyük bir veri işlem hattı üzerinden işlenirse önemli öngörülere sahip olabilir. Bu tür verileri Data Lake Storage 1. taşımak için aşağıdaki araçları kullanabilirsiniz.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web sunucusu günlük verileri (özel uygulamalar kullanarak karşıya yükle)
Web sunucusu günlük verilerinin çözümlenmesi büyük veri uygulamaları için ortak bir kullanım durumu olduğundan ve Data Lake Storage 1. ' ye karşıya yüklenecek büyük hacimde günlük dosyaları gerektirdiğinden bu veri kümesi türü özellikle çağrılır. Bu tür verileri karşıya yüklemek üzere kendi betiklerinizi veya uygulamalarınızı yazmak için aşağıdaki araçlardan herhangi birini kullanabilirsiniz.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage 1. .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Web sunucusu günlük verilerini karşıya yüklemek ve aynı zamanda diğer veri türlerini (örn. sosyal yaklaşım verileri) karşıya yüklemek için, kendi özel betiklerinizi/uygulamalarınızı yazmak iyi bir yaklaşımdır. bu sayede, verileri karşıya yükleme bileşeninizin büyük veri uygulamanızın bir parçası olarak yüklenmesine yönelik esneklik sağlanır. Bazı durumlarda bu kod bir komut dosyası veya basit komut satırı yardımcı programı biçiminde olabilir. Diğer durumlarda kod, büyük veri işlemeyi bir iş uygulaması veya çözümüyle bütünleştirmek için kullanılabilir.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight kümeleri ile ilişkili veriler
Çoğu HDInsight küme türleri (Hadoop, HBase, fırtınası) veri depolama deposu olarak Data Lake Storage 1. destekler. HDInsight kümeleri, Azure depolama Bloblarından (ıLB) verilere erişir. Daha iyi performans için, bulunan verileri, kümeyle ilişkili bir Data Lake Storage 1. hesabına kopyalayabilirsiniz. Verileri kopyalamak için aşağıdaki araçları kullanabilirsiniz.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy hizmeti](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Şirket içinde veya IaaS Hadoop kümelerinde depolanan veriler
Büyük miktarlarda veri, var olan Hadoop kümelerinde yerel olarak,,, işlem kullanan makinelerde depolanabilir. Hadoop kümeleri şirket içi bir dağıtımda olabilir veya Azure 'da bir IaaS kümesi içinde olabilir. Bu tür verileri tek kapalı bir yaklaşım veya yinelenen bir biçimde kopyalamak için Azure Data Lake Storage 1. gereksinimler olabilir. Bunu başarmak için kullanabileceğiniz çeşitli seçenekler vardır. Aşağıda, alternatifleri ve ilişkili ticaretin bir listesi verilmiştir.

| Yaklaşım | Ayrıntılar | Avantajlar | Dikkat edilmesi gerekenler |
| --- | --- | --- | --- |
| Verileri doğrudan Hadoop kümelerinden Azure Data Lake Storage 1. kopyalamak için Azure Data Factory (ADF) kullanın |[ADF, bir veri kaynağı olarak mı 'yi destekler](../data-factory/connector-hdfs.md) |ADF, ve ilk sınıf uçtan uca yönetim ve izleme için kullanıma hazır destek sağlar |Şirket içinde veya IaaS kümesinde dağıtılması için Veri Yönetimi ağ geçidi gerekir |
| Verileri Hadoop 'dan dosya olarak dışarı aktarın. Ardından uygun mekanizmayı kullanarak dosyaları Azure Data Lake Storage 1. kopyalayın. |Kullanarak dosyaları Azure Data Lake Storage 1. kopyalayabilirsiniz: <ul><li>[Windows işletim sistemi için Azure PowerShell](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Herhangi bir Data Lake Storage 1. SDK kullanarak özel uygulama</li></ul> |Kullanmaya başlamak için hızlı. Özelleştirilmiş karşıya yüklemeler gerçekleştirebilir |Birden çok teknolojiyi kapsayan çok adımlı işlem. Yönetim ve izleme, araçların özelleştirilmiş doğası sırasında zaman içinde bir bilgi olarak büyüyecektir |
| Hadoop 'tan Azure depolama 'ya veri kopyalamak için Distcp 'yi kullanın. Ardından uygun mekanizmayı kullanarak Azure depolama alanından Data Lake Storage 1. verileri kopyalayın. |Azure depolama 'dan Data Lake Storage 1. kullanarak veri kopyalayabilirsiniz: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy aracı](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight kümelerinde çalışan Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Açık kaynak araçları 'nı kullanabilirsiniz. |Birden çok teknolojiyi kapsayan çok adımlı işlem |

### <a name="really-large-datasets"></a>Gerçekten büyük veri kümeleri
Birden çok terabayt içinde yer alan veri kümelerini yüklemek için yukarıda açıklanan yöntemleri kullanmak bazen yavaş ve maliyetli olabilir. Böyle durumlarda, aşağıdaki seçenekleri kullanabilirsiniz.

* **Azure ExpressRoute kullanma**. Azure ExpressRoute, Azure veri merkezleri ve şirket içi altyapı arasında özel bağlantılar oluşturmanızı sağlar. Bu, büyük miktarlarda veri aktarmaya yönelik güvenilir bir seçenek sağlar. Daha fazla bilgi için bkz. [Azure ExpressRoute belgeleri](../expressroute/expressroute-introduction.md).
* **Verilerin "çevrimdışı" yüklenmesi**. Azure ExpressRoute 'ın kullanılması herhangi bir nedenle uygun değilse, Azure [içeri/dışarı aktarma hizmeti](../import-export/storage-import-export-service.md) 'ni kullanarak verilerinize bir Azure veri merkezine sabit disk sürücüleri gönderebilirsiniz. Verileriniz ilk olarak Azure Storage Bloblarına yüklenir. Daha sonra, Azure depolama Bloblarındaki verileri Data Lake Storage 1. kopyalamak için [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) veya [AdlCopy aracını](data-lake-store-copy-data-azure-storage-blob.md) kullanabilirsiniz.

  > [!NOTE]
  > Içeri/dışarı aktarma hizmetini kullanırken, Azure veri merkezi 'ne sevk ettiğiniz disklerdeki dosya boyutları 195 GB 'tan büyük olmamalıdır.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Data Lake Storage 1. depolanan verileri işleme
Data Lake Storage 1. veriler kullanılabildiğinde, desteklenen büyük veri uygulamalarını kullanarak bu verilerde analiz gerçekleştirebilirsiniz. Şu anda, Data Lake Storage 1. depolanan verilerde veri analizi işlerini çalıştırmak için Azure HDInsight ve Azure Data Lake Analytics kullanabilirsiniz.

![Data Lake Storage 1. verileri analiz etme](./media/data-lake-store-data-scenarios/analyze-data.png "Data Lake Storage 1. verileri analiz etme")

Aşağıdaki örneklere bakabilirsiniz.

* [Depolama alanı olarak Data Lake Storage 1. bir HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Data Lake Storage 1. verileri indirin
Ayrıca, gibi senaryolar için Azure Data Lake Storage 1. verileri indirmek veya taşımak isteyebilirsiniz:

* Mevcut veri işleme işlem hatlarınız ile, verileri diğer depolara, arayüze taşıyın. Örneğin, Data Lake Storage 1. verileri Azure SQL veritabanı 'na veya SQL Server taşımak isteyebilirsiniz.
* Uygulama prototipleri oluştururken IDE ortamlarında işlenmek üzere verileri yerel bilgisayarınıza indirin.

![Data Lake Storage 1. çıkış verileri](./media/data-lake-store-data-scenarios/egress-data.png "Data Lake Storage 1. çıkış verileri")

Böyle durumlarda, aşağıdaki seçeneklerden herhangi birini kullanabilirsiniz:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Ayrıca, Data Lake Storage 1. verileri indirmek üzere kendi komut dosyanızı/uygulamanızı yazmak için aşağıdaki yöntemleri de kullanabilirsiniz.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage 1. .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Data Lake Storage 1. verileri görselleştirme
Data Lake Storage 1. depolanan verilerin görsel sunumlarını oluşturmak için bir hizmet karışımı kullanabilirsiniz.

![Data Lake Storage 1. verileri görselleştirme](./media/data-lake-store-data-scenarios/visualize-data.png "Data Lake Storage 1. verileri görselleştirme")

* [Data Lake Storage 1. verileri Azure SYNAPSE Analytics 'e taşımak için Azure Data Factory](../data-factory/copy-activity-overview.md) kullanarak başlayabilirsiniz
* Bundan sonra, verilerin görsel temsilini oluşturmak için [Power BI Azure SYNAPSE Analytics ile tümleştirebilirsiniz](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) .