---
title: HDInsight 'ta Apache Hadoop işleri için veri yükleme
description: Azure klasik CLı, Azure Depolama Gezgini, Azure PowerShell, Hadoop komut satırı veya Sqoop kullanarak HDInsight 'ta Apache Hadoop işleri için verileri karşıya yükleme ve verilere erişme hakkında bilgi edinin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100138"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>HDInsight 'ta Apache Hadoop işleri için veri yükleme

Azure HDInsight, Azure Azure Data Lake Storage Depolama üzerinden tam özellikli bir Hadoop Dağıtılmış dosya sistemi (ıGen1 ve Gen2) sağlar. Azure depolama ve Data Lake Storage 1. ve Gen2, müşteriler için sorunsuz bir deneyim sunmak üzere, bir. Bu kişiler, Hadoop ekosistemindeki tüm bileşen kümesini doğrudan yönettiği verilerde çalışacak şekilde etkinleştirir. Azure Storage, Data Lake Storage 1. ve Gen2, verilerin depolanması ve bu verilerde hesaplamalar için optimize edilmiş farklı dosya sistemleridir. Azure Storage kullanmanın avantajları hakkında daha fazla bilgi için bkz. [HDInsight Ile Azure depolama 'Yı kullanma](hdinsight-hadoop-use-blob-storage.md), [HDInsight ile Data Lake Storage 1. kullanma](hdinsight-hadoop-use-data-lake-store.md)ve [HDInsight ile Data Lake Storage 2. kullanma](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdaki gereksinimleri dikkate alın:

* Bir Azure HDInsight kümesi. Yönergeler için bkz. [Azure HDInsight kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md) veya [HDInsight kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md).
* Aşağıdaki makaleler hakkında bilgi:
    * [HDInsight ile Azure depolama kullanma](hdinsight-hadoop-use-blob-storage.md)
    * [HDInsight ile Data Lake Storage 1. kullanma](hdinsight-hadoop-use-data-lake-store.md)
    * [HDInsight ile Data Lake Storage 2. kullanma](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Azure depolama 'ya veri yükleme

## <a name="utilities"></a>Altyapı Hizmetleri

Microsoft, Azure depolama ile çalışmak için aşağıdaki yardımcı programları sağlar:

| Araç | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portalda](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop komutu](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop komutu yalnızca HDInsight kümesinde kullanılabilir. Komutu yalnızca yerel dosya sisteminden Azure depolama 'ya veri yüklemeye izin verir.  

## <a id="commandline"></a>Hadoop komut satırı

Hadoop komut satırı yalnızca, veriler küme baş düğümünde zaten mevcut olduğunda Azure Storage blob 'a veri depolamak için yararlıdır.

Hadoop komutunu kullanmak için önce [SSH veya PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)kullanarak baş düğümüne 'a bağlanmanız gerekir.

Bağlandıktan sonra bir dosyayı depolamaya yüklemek için aşağıdaki sözdizimini kullanabilirsiniz.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Örneğin, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

HDInsight için varsayılan dosya sistemi Azure Storage 'da olduğundan,/example/Data/Data.txt aslında Azure Storage 'da bulunur. Ayrıca dosyaya şu şekilde bakabilirsiniz:

    wasbs:///example/data/data.txt

or

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Dosyalarla çalışan diğer Hadoop komutlarının bir listesi için bkz. [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Apache HBase kümelerinde, veri yazılırken kullanılan varsayılan blok boyutu 256 KB 'dir. Bu, HBase API 'Leri veya REST API 'Leri kullanırken, en fazla ~ 12 GB 'den büyük verileri yazmak için `hadoop` veya `hdfs dfs` komutlarını kullanarak bir hatayla sonuçlanır. Daha fazla bilgi için bu makaledeki [BLOB 'da yazma için depolama özel durumu](#storageexception) bölümüne bakın.

## <a name="graphical-clients"></a>Grafik istemcileri

Azure depolama ile çalışmak için grafik arabirimi sağlayan çeşitli uygulamalar da vardır. Aşağıdaki tabloda bu uygulamaların bir listesi verilmiştir:

| İstemci | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [HDInsight için Microsoft Visual Studio Araçları](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Depolama Gezgini](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulet](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Microsoft Azure için Cloudbraz Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Sizack](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage 'ı yerel sürücü olarak bağlama

Bkz. [Azure Storage 'ı yerel sürücü olarak bağlama](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Hizmetleri kullanarak karşıya yükleme

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory hizmeti, veri depolama, veri işleme ve veri taşıma hizmetlerini kolaylaştırılmış, ölçeklenebilir ve güvenilir veri üretimi işlem hatları halinde oluşturmaya yönelik tam olarak yönetilen bir hizmettir.

|Depolama türü|Belgeler|
|----|----|
|Azure Blob depolama|[Azure Data Factory kullanarak Azure Blob depolama alanına veya veri kopyalama](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Azure Data Factory kullanarak Azure Data Lake Storage 1. veri kopyalama](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Azure Data Factory ile Azure Data Lake Storage 2. verileri yükleme](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop

Sqoop, Hadoop ve ilişkisel veritabanları arasında veri aktarmak için tasarlanan bir araçtır. SQL Server, MySQL veya Oracle gibi bir ilişkisel veritabanı yönetim sisteminden (RDBMS) verileri Hadoop Dağıtılmış dosya sistemine (ISE) içeri aktarmak, Hadoop 'daki verileri MapReduce veya Hive 'e dönüştürmek ve ardından verileri bir RDBMS 'ye geri aktarmak için kullanabilirsiniz.

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

## <a name="troubleshooting"></a>Sorun giderme

### <a id="storageexception"></a>Blob üzerinde yazma için depolama özel durumu

**Belirtiler**: bir HBase kümesinde ~ 12 GB veya daha büyük olan dosyaları yazmak için `hadoop` veya `hdfs dfs` komutlarını kullanırken aşağıdaki hatayla karşılaşabilirsiniz:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Neden**: HDInsight kümelerinde HBase, Azure depolama 'ya yazarken varsayılan olarak 256 KB 'lik bir blok boyutuna sahiptir. HBase API 'Leri veya REST API 'Leri için çalışırken, `hadoop` veya `hdfs dfs` komut satırı yardımcı programları kullanılırken bir hatayla sonuçlanır.

**Çözüm**: daha büyük bir blok boyutu belirtmek için `fs.azure.write.request.size` kullanın. Bunu, `-D` parametresini kullanarak kullanım başına temelinde yapabilirsiniz. Aşağıdaki komut, `hadoop` komutuyla bu parametreyi kullanan bir örnektir:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Ayrıca, Apache ambarı 'nı kullanarak `fs.azure.write.request.size` değerini küresel olarak artırabilirsiniz. Aşağıdaki adımlar, ambarı Web Kullanıcı arabirimindeki değeri değiştirmek için kullanılabilir:

1. Tarayıcınızda kümeniz için ambarı Web Kullanıcı arabirimine gidin. Bu `https://CLUSTERNAME.azurehdinsight.net`, `CLUSTERNAME` kümenizin adıdır.

    İstendiğinde, kümenin yönetici adını ve parolasını girin.
2. Ekranın sol tarafında, bu ' ı seçin ve ardından **configs** **sekmesini seçin.**
3. **Filter...** alanına `fs.azure.write.request.size`girin. Bu, alanı ve geçerli değeri sayfanın ortasında görüntüler.
4. 262144 (256 KB) değerini yeni değerle değiştirin. Örneğin, 4194304 (4 MB).

    ![Ambarı Web Kullanıcı arabirimi üzerinden değerin değiştirilmesinin görüntüsü](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Ambarı kullanma hakkında daha fazla bilgi için bkz. [Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Sonraki adımlar

HDInsight 'a nasıl veri alınacağını anladığınıza göre, analiz yapmayı öğrenmek için aşağıdaki makaleleri okuyun:

* [Azure HDInsight 'ı kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop işleri programlı olarak gönderme](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight ile Apache Pig kullanma](hadoop/hdinsight-use-pig.md)
