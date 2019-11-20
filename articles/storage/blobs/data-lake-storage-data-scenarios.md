---
title: Azure Data Lake Storage 2. içeren veri senaryoları | Microsoft Docs
description: Data Lake Storage 2. (daha önce Azure Data Lake Store olarak bilinirdi) hangi verilerin hangi verilerle içeri ve nasıl işlenebileceğini, işlendiğini, indirileceğini ve görselleştirdiğini anlayın.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: d6347d75e0a3883f23fdf76016080c8b7b330163
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580799"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Büyük veri gereksinimleri için Azure Data Lake Storage 2. kullanma

Büyük veri işlemede dört ana aşama vardır:

> [!div class="checklist"]
> * Büyük miktarlarda veriyi bir veri deposuna gerçek zamanlı veya toplu iş halinde geri ödeme
> * Verileri işleme
> * Veriler indiriliyor
> * Verileri görselleştirme

Bir depolama hesabı ve kapsayıcı oluşturarak başlayın. Ardından, verilere erişim izni verin. Bu makalenin ilk birkaç bölümü, bu görevleri gerçekleştirmenize yardımcı olur. Kalan bölümlerde, her bir işleme aşaması için seçenekleri ve araçları vurgulayacağız.

Azure Data Lake Storage 2. ile kullanabileceğiniz Azure hizmetlerinin kapsamlı bir listesi için bkz. [Azure hizmetleriyle Azure Data Lake Storage tümleştirme](data-lake-storage-integrate-with-azure-services.md)

## <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage 2. hesabı oluşturma

Data Lake Storage 2. hesap, hiyerarşik bir ad alanı olan bir depolama hesabıdır. 

Bir tane oluşturmak için bkz. [hızlı başlangıç: Azure Data Lake Storage 2. depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Dosyalarınız için bir kapsayıcı oluşturmak üzere kullanabileceğiniz araçların bir listesi aşağıdadır.

|Araç | Rehber |
|---|--|
|Azure Depolama Gezgini | [Depolama Gezgini kullanarak kapsayıcı oluşturma](data-lake-storage-explorer.md#create-a-container) |
|AzCopy | [AzCopyV10 kullanarak blob kapsayıcısı veya dosya paylaşma oluşturma](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|HDInsight ile Hadoop kapsayıcısı (bir) komut satırı arabirimi (CLı) |[HDInsight ile bir kapsayıcısı kullanarak bir kapsayıcı oluşturma](data-lake-storage-use-hdfs-data-lake-storage.md#create-a-container) |
|Azure Databricks bir not defterinde kod|[Depolama hesabı kapsayıcısı oluşturma (Scala)](data-lake-storage-quickstart-create-databricks-account.md#create-storage-account-container) <br><br> [Kapsayıcı oluşturma ve bağlama (Python)](data-lake-storage-use-databricks-spark.md#create-a-container-and-mount-it)|

Depolama Gezgini veya AzCopy kullanarak dosya sistemlerinin oluşturulması en kolay yoldur. HDInsight ve Databricks kullanarak dosya sistemleri oluşturmaya biraz daha fazla çalışma sürer. Bununla birlikte, verilerinizi aynı şekilde işlemek için HDInsight veya Databricks kümelerini kullanmayı planlıyorsanız, önce kümelerinizi oluşturabilir ve bu durumda, oluşturma dosya sistemlerinizde bir işlem adı kullanabilirsiniz.  

## <a name="grant-access-to-the-data"></a>Verilere erişim izni verme

Veri almaya başlamadan önce hesabınıza ve hesabınızdaki verilere uygun erişim izinlerini ayarlayın.

Erişim vermek için üç yol vardır:

* Bu rollerden birini bir kullanıcıya, gruba, Kullanıcı tarafından yönetilen kimliğe veya hizmet sorumlusuna atayın:

  [Depolama Blobu veri okuyucusu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Depolama Blobu veri Katılımcısı](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Depolama Blobu veri sahibi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Paylaşılan erişim Imzası (SAS) belirteci kullanın.

* Bir depolama hesabı anahtarı kullanın.

Bu tabloda, her bir Azure hizmeti veya aracı için nasıl erişim verilecek gösterilmektedir.

|Araç | Erişim vermek için | Rehber |
|---|--|---|
|Depolama Gezgini| Kullanıcılara ve gruplara rol atama | [Azure Active Directory sahip kullanıcılara yönetici ve yönetici olmayan roller atama](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Kullanıcılara ve gruplara rol atama <br>**veya**<br> SAS belirteci kullanma| [Azure Active Directory sahip kullanıcılara yönetici ve yönetici olmayan roller atama](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Azure depolama 'dan bir dosyayı Indirmek için kolayca bir SAS oluşturun: Azure Depolama Gezgini kullanarak](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Kullanıcı tarafından atanan yönetilen kimliğe rol atama | [Data Lake Storage 2. ile HDInsight kümesi oluşturma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Kullanıcı tarafından atanan yönetilen kimliğe rol atama<br>**veya**<br> Hizmet sorumlusuna rol atama<br>**veya**<br> Depolama hesabı anahtarı kullan | [Bağlı hizmet özellikleri](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Kullanıcı tarafından atanan yönetilen kimliğe rol atama | [Data Lake Storage 2. ile HDInsight kümesi oluşturma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Hizmet sorumlusuna rol atama | [Nasıl yapılır: kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Belirli dosya ve klasörlere erişim vermek için, bu makalelere bakın.

* [Azure Data Lake Storage 2. ile Azure Depolama Gezgini kullanarak dosya ve Dizin düzeyi izinleri ayarlama](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Dosya ve dizinlerdeki erişim denetim listeleri](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Güvenliğin diğer yönlerini ayarlama hakkında bilgi edinmek için bkz. [Azure Data Lake Storage 2. Security Guide](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Verileri alma

Bu bölümde, farklı veri kaynakları ve verilerin Data Lake Storage 2. hesaba alınmasının farklı yolları vurgulanmaktadır.

![Data Lake Storage 2. verileri alma](./media/data-lake-storage-data-scenarios/ingest-data.png "Data Lake Storage 2. verileri alma")

### <a name="ad-hoc-data"></a>Geçici veri

Bu, büyük bir veri uygulamasını prototip yazmak için kullanılan daha küçük veri kümelerini temsil eder. Veri kaynağına bağlı olarak, geçici verileri almanın farklı yolları vardır. 

İşte, geçici verileri almak için kullanabileceğiniz araçların listesi.

| Veri kaynağı | Şunu kullanarak Al |
| --- | --- |
| Yerel bilgisayar |[Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy aracı](../common/storage-use-azcopy-v10.md)|
| Azure Depolama Blobu |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy aracı](../common/storage-use-azcopy-v10.md)<br><br>[HDInsight kümesinde çalışan DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Akışlı veriler

Bu, uygulamalar, cihazlar, algılayıcılar vb. gibi çeşitli kaynaklarla oluşturulabilecek verileri temsil eder. Bu veriler çeşitli araçlarla Data Lake Storage 2. alınabilir. Bu araçlar genellikle verileri bir olay temelinde gerçek zamanlı olarak yakalayıp işleyerek, daha sonra işlenebilmeleri için olayları toplu işlemlere Data Lake Storage 2. olarak yazar.

İşte, akış verilerini almak için kullanabileceğiniz araçların listesi.

|Araç | Rehber |
|---|--|
|Azure HDInsight fırtınası | [HDInsight 'ta Apache Storm Apache Hadoop rsunucudan yaz](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>İlişkisel veriler

İlişkisel veritabanlarındaki verileri de kaynak olarak kullanabilirsiniz. İlişkisel veritabanları, bir süre boyunca büyük miktarlarda veri toplar ve bu da büyük bir veri işlem hattı üzerinden işlenirse önemli öngörülere sahip olabilir. Bu tür verileri Data Lake Storage 2. taşımak için aşağıdaki araçları kullanabilirsiniz.

İlişkisel verileri almak için kullanabileceğiniz araçların listesi aşağıda verilmiştir.

|Araç | Rehber |
|---|--|
|Azure Data Factory | [Azure Data Factory’de Kopyalama Etkinliği](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web sunucusu günlük verileri (özel uygulamalar kullanarak karşıya yükle)

Web sunucusu günlük verilerinin çözümlenmesi büyük veri uygulamaları için ortak bir kullanım durumu olduğundan ve Data Lake Storage 2. ' ye karşıya yüklenecek büyük hacimde günlük dosyaları gerektirdiğinden bu veri kümesi türü özellikle çağrılır. Bu tür verileri karşıya yüklemek üzere kendi betiklerinizi veya uygulamalarınızı yazmak için aşağıdaki araçlardan herhangi birini kullanabilirsiniz.

Aşağıda, Web sunucusu günlük verilerini almak için kullanabileceğiniz araçların bir listesi verilmiştir.

|Araç | Rehber |
|---|--|
|Azure Data Factory | [Azure Data Factory’de Kopyalama Etkinliği](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Web sunucusu günlük verilerini karşıya yüklemek ve aynı zamanda diğer veri türlerini (örn. sosyal yaklaşım verileri) karşıya yüklemek için, kendi özel betiklerinizi/uygulamalarınızı yazmak iyi bir yaklaşımdır çünkü bu sayede, veri yükleme bileşeninizin bir parçası olarak büyük veri uygulamanız. Bazı durumlarda bu kod bir komut dosyası veya basit komut satırı yardımcı programı biçiminde olabilir. Diğer durumlarda kod, büyük veri işlemeyi bir iş uygulaması veya çözümüyle bütünleştirmek için kullanılabilir.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight kümeleri ile ilişkili veriler

Çoğu HDInsight küme türleri (Hadoop, HBase, fırtınası) veri depolama deposu olarak Data Lake Storage 2. destekler. HDInsight kümeleri, Azure depolama Bloblarından (ıLB) verilere erişir. Daha iyi performans için, bulunan verileri, kümeyle ilişkili bir Data Lake Storage 2. hesabına kopyalayabilirsiniz. Verileri kopyalamak için aşağıdaki araçları kullanabilirsiniz.

HDInsight kümeleriyle ilişkili verileri almak için kullanabileceğiniz araçların listesi aşağıda verilmiştir.

|Araç | Rehber |
|---|--|
|Apache DistCp | [Azure depolama Blobları ve Azure Data Lake Storage 2. arasında veri kopyalamak için DistCp kullanma](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy aracı | [AzCopy ile veri aktarma](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Azure Data Factory kullanarak Azure Data Lake Storage 2. veri kopyalama](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Şirket içinde veya IaaS Hadoop kümelerinde depolanan veriler

Büyük miktarlarda veri, var olan Hadoop kümelerinde yerel olarak,,, işlem kullanan makinelerde depolanabilir. Hadoop kümeleri şirket içi bir dağıtımda olabilir veya Azure 'da bir IaaS kümesi içinde olabilir. Bu tür verileri tek kapalı bir yaklaşım veya yinelenen bir biçimde kopyalamak için Azure Data Lake Storage 2. gereksinimler olabilir. Bunu başarmak için kullanabileceğiniz çeşitli seçenekler vardır. Aşağıda, alternatifleri ve ilişkili ticaretin bir listesi verilmiştir.

| Uygulanabilecek | Ayrıntılar | Üstünlü | Dikkat edilmesi gerekenler |
| --- | --- | --- | --- |
| Verileri doğrudan Hadoop kümelerinden Azure Data Lake Storage 2. kopyalamak için Azure Data Factory (ADF) kullanın |[ADF, bir veri kaynağı olarak mı 'yi destekler](../../data-factory/connector-hdfs.md) |ADF, ve ilk sınıf uçtan uca yönetim ve izleme için kullanıma hazır destek sağlar |Şirket içinde veya IaaS kümesinde dağıtılması için Veri Yönetimi ağ geçidi gerekir |
| Hadoop 'tan Azure depolama 'ya veri kopyalamak için Distcp 'yi kullanın. Ardından uygun mekanizmayı kullanarak Azure depolama alanından Data Lake Storage 2. verileri kopyalayın. |Azure depolama 'dan Data Lake Storage 2. kullanarak veri kopyalayabilirsiniz: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy aracı](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight kümelerinde çalışan Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |Açık kaynak araçları 'nı kullanabilirsiniz. |Birden çok teknolojiyi kapsayan çok adımlı işlem |

### <a name="really-large-datasets"></a>Gerçekten büyük veri kümeleri

Birden çok terabayt içinde yer alan veri kümelerini yüklemek için yukarıda açıklanan yöntemleri kullanmak bazen yavaş ve maliyetli olabilir. Bu gibi durumlarda Azure ExpressRoute ' u kullanabilirsiniz.  

Azure ExpressRoute, Azure veri merkezleri ve şirket içi altyapı arasında özel bağlantılar oluşturmanızı sağlar. Bu, büyük miktarlarda veri aktarmaya yönelik güvenilir bir seçenek sağlar. Daha fazla bilgi için bkz. [Azure ExpressRoute belgeleri](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Verileri işleme

Data Lake Storage 2. veriler kullanılabildiğinde, desteklenen büyük veri uygulamalarını kullanarak bu verilerde analiz gerçekleştirebilirsiniz. 

![Data Lake Storage 2. verileri analiz etme](./media/data-lake-storage-data-scenarios/analyze-data.png "Data Lake Storage 2. verileri analiz etme")

Data Lake Storage 2. depolanan veriler üzerinde veri analizi işlerini çalıştırmak için kullanabileceğiniz araçların listesi aşağıda verilmiştir.

|Araç | Rehber |
|---|--|
|Azure HDInsight | [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage 2.](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Hızlı başlangıç: Azure Databricks kullanarak Azure Data Lake Storage 2. verileri çözümleme](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Öğretici: Azure Databricks kullanarak verileri ayıklama, dönüştürme ve yükleme](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Verileri görselleştirme

Data Lake Storage 2. depolanan verilerin görsel sunumlarını oluşturmak için bir hizmet karışımı kullanabilirsiniz.

![Data Lake Storage 2. verileri görselleştirme](./media/data-lake-storage-data-scenarios/visualize-data.png "Data Lake Storage 2. verileri görselleştirme")

* [Data Lake Storage 2. verileri Azure SQL veri ambarı 'na taşımak için Azure Data Factory](../../data-factory/copy-activity-overview.md) kullanarak başlayabilirsiniz
* Bundan sonra, verilerin görsel temsilini oluşturmak için [Power BI Azure SQL veri ambarı ile tümleştirebilirsiniz](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) .

## <a name="download-the-data"></a>Verileri indirin

Ayrıca, gibi senaryolar için Azure Data Lake Storage 2. verileri indirmek veya taşımak isteyebilirsiniz:

* Mevcut veri işleme işlem hatlarınız ile, verileri diğer depolara, arayüze taşıyın. Örneğin, Data Lake Storage 2. verileri Azure SQL veritabanı 'na veya şirket içi SQL Server taşımak isteyebilirsiniz.

* Uygulama prototipleri oluştururken IDE ortamlarında işlenmek üzere verileri yerel bilgisayarınıza indirin.

![Data Lake Storage 2. çıkış verileri](./media/data-lake-storage-data-scenarios/egress-data.png "Data Lake Storage 2. çıkış verileri")

Data Lake Storage 2. verileri indirmek için kullanabileceğiniz araçların listesi aşağıda verilmiştir.

|Araç | Rehber |
|---|--|
|Azure Data Factory | [Azure Data Factory’de Kopyalama Etkinliği](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Azure depolama Blobları ve Azure Data Lake Storage 2. arasında veri kopyalamak için DistCp kullanma](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
