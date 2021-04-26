---
title: HDInsight 'ta Apache Hadoop işleri için veri yükleme
description: HDInsight 'ta Apache Hadoop işleri için verileri karşıya yükleme ve verilere erişme hakkında bilgi edinin. Azure klasik CLı, Azure Depolama Gezgini, Azure PowerShell, Hadoop komut satırı veya Sqoop kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020, devx-track-azurecli
ms.date: 04/27/2020
ms.openlocfilehash: d58d194e8dbf011eec949602e4f8cd2e084a0d98
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482118"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>HDInsight 'ta Apache Hadoop işleri için veri yükleme

HDInsight, Azure depolama üzerinden bir Hadoop Dağıtılmış dosya sistemi (bir) sağlar ve Azure Data Lake Storage. Bu depolama gen1 ve Gen2 içerir. Azure depolama ve Data Lake Storage 1. ile Gen2, Bunlar, Hadoop ortamında doğrudan yönettiği verilerde çalışmak üzere tüm bileşen kümesini etkinleştirir. Azure depolama, Data Lake Storage 1. ve Gen2 farklı dosya sistemleridir. Sistemler verilerin ve hesaplamaların depolanması için en iyi duruma getirilmiştir. Azure Storage kullanmanın avantajları hakkında daha fazla bilgi için bkz. [HDInsight Ile Azure depolama 'Yı kullanma](hdinsight-hadoop-use-blob-storage.md). Ayrıca bkz. [HDInsight ile Data Lake Storage 1. kullanma](hdinsight-hadoop-use-data-lake-storage-gen1.md)ve [HDInsight ile Data Lake Storage 2. kullanma](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdaki gereksinimleri dikkate alın:

* Bir Azure HDInsight kümesi. Yönergeler için bkz. [Azure HDInsight kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Aşağıdaki makaleler hakkında bilgi:
    * [HDInsight ile Azure depolama kullanma](hdinsight-hadoop-use-blob-storage.md)
    * [HDInsight ile Data Lake Storage 1. kullanma](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [HDInsight ile Data Lake Storage 2. kullanma](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Azure depolama 'ya veri yükleme

### <a name="utilities"></a>Yardımcı Programlar

Microsoft, Azure depolama ile çalışmak için aşağıdaki yardımcı programları sağlar:

| Araç | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portalı](../storage/blobs/storage-quickstart-blobs-portal.md) |âœ" |âœ" |âœ" |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |âœ" |âœ" |âœ" |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |âœ" |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |âœ" | |âœ" |
| [Hadoop komutu](#hadoop-command-line) |âœ" |âœ" |âœ" |

> [!NOTE]  
> Hadoop komutu yalnızca HDInsight kümesinde kullanılabilir. Komutu yalnızca yerel dosya sisteminden Azure depolama 'ya veri yüklemeye izin verir.  

### <a name="hadoop-command-line"></a>Hadoop komut satırı

Hadoop komut satırı yalnızca, veriler küme baş düğümünde zaten mevcut olduğunda Azure Storage blob 'a veri depolamak için yararlıdır.

Hadoop komutunu kullanmak için önce [SSH veya PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)kullanarak baş düğümüne 'a bağlanmanız gerekir.

Bağlandıktan sonra bir dosyayı depolamaya yüklemek için aşağıdaki sözdizimini kullanabilirsiniz.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Örneğin, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

HDInsight için varsayılan dosya sistemi Azure Storage 'da olduğundan,/example/Data/data.txt aslında Azure Storage 'da bulunur. Ayrıca dosyaya şu şekilde bakabilirsiniz:

`wasbs:///example/data/data.txt`

veya

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Dosyalarla çalışan diğer Hadoop komutlarının bir listesi için bkz. [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Apache HBase kümelerinde, veri yazılırken kullanılan varsayılan blok boyutu 256 KB 'dir. Bu, HBase API 'Leri veya REST API 'Leri kullanırken iyi çalışırken, `hadoop` `hdfs dfs` ~ 12 GB 'den büyük verileri yazmak için veya komutlarını kullanarak bir hatayla sonuçlanır. Daha fazla bilgi için bkz. [BLOB üzerinde yazma için depolama özel durumu](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Grafik istemcileri

Azure depolama ile çalışmak için grafik arabirimi sağlayan çeşitli uygulamalar da vardır. Aşağıdaki tabloda bu uygulamaların bir listesi verilmiştir:

| İstemci | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [HDInsight için Microsoft Visual Studio Araçları](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |âœ" |âœ" |âœ" |
| [Azure Depolama Gezgini](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |âœ" |âœ" |âœ" |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |âœ" |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |âœ" |
| [Microsoft Azure için Cloudbraz Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |âœ" |
| [Sizack](https://cyberduck.io/) | |âœ" |âœ" |

## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage 'ı yerel sürücü olarak bağlama

Bkz. [Azure Storage 'ı yerel sürücü olarak bağlama](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Hizmetleri kullanarak karşıya yükleme

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory hizmeti, veri oluşturmaya yönelik tam olarak yönetilen bir hizmettir: depolama, işleme ve taşıma hizmetleri için kolaylaştırılmış, uyarlanabilir tablo ve güvenilir veri üretimi işlem hatları.

|Depolama türü|Belgeler|
|----|----|
|Azure Blob depolama|[Azure Data Factory kullanarak Azure Blob depolama alanına veya oradan veri kopyalama](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Azure Data Factory kullanarak Azure Data Lake Storage 1. veri kopyalama](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Azure Data Factory ile Azure Data Lake Storage 2. verileri yükleme](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop, Hadoop ve ilişkisel veritabanları arasında veri aktarmak için tasarlanan bir araçtır. SQL Server, MySQL veya Oracle gibi bir ilişkisel veritabanı yönetim sisteminden (RDBMS) veri almak için kullanın. Ardından Hadoop Dağıtılmış dosya sistemi 'ne (IBU). Hadoop 'daki verileri MapReduce veya Hive ile dönüştürün ve sonra verileri bir RDBMS 'ye geri aktarın.

Daha fazla bilgi için bkz. [HDInsight Ile Sqoop kullanma](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Geliştirme SDK 'Ları

Azure depolama 'ya aşağıdaki programlama dillerinden bir Azure SDK 'Sı kullanılarak da erişilebilir:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Azure SDK 'larını yükleme hakkında daha fazla bilgi için bkz. [Azure İndirmeleri](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Sonraki adımlar

HDInsight 'a nasıl veri alınacağını anladığınıza göre, analiz hakkında bilgi edinmek için aşağıdaki makaleleri okuyun:

* [Azure HDInsight 'ı kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop işleri programlı olarak gönderme](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
