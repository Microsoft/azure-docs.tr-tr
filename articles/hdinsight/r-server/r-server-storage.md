---
title: HDInsight 'ta ML Hizmetleri için Azure depolama çözümleri-Azure
description: HDInsight 'ta ML Hizmetleri ile kullanılabilen farklı depolama seçenekleri hakkında bilgi edinin
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/02/2020
ms.openlocfilehash: ddc48025de164ff68fb539a293e06bae09171742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943907"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure HDInsight 'ta ML Hizmetleri için Azure depolama çözümleri

HDInsight üzerinde ML Hizmetleri, verileri, kodu veya analiz sonuçlarını içeren nesneleri kalıcı hale getirmek için farklı depolama çözümleri kullanabilir. Bu çözümler aşağıdaki seçenekleri içerir:

- [Azure Blob Depolama](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure Dosya Depolama](https://azure.microsoft.com/services/storage/files/)

Ayrıca HDInsight kümeniz ile birden çok Azure depolama hesabına veya kapsayıcılarına erişme seçeneğiniz de vardır. Azure dosya depolama, bir Azure depolama dosya paylaşımının bir örneğini, örneğin Linux dosya sistemine takabilmenizi sağlayan Edge düğümünde kullanılmak üzere kullanışlı bir veri depolama seçeneğidir. Ancak Azure dosya paylaşımları, Windows veya Linux gibi desteklenen bir işletim sistemine sahip olan herhangi bir sistem tarafından bağlanabilir ve kullanılabilir.

HDInsight 'ta bir Apache Hadoop kümesi oluşturduğunuzda, bir **Azure Blob depolama** hesabı ya da **Data Lake Storage 1.** belirtirsiniz. Bu hesaptan belirli bir depolama kapsayıcısı, oluşturduğunuz küme için dosya sistemini tutar (örneğin, Hadoop Dağıtılmış Dosya Sistemi). Daha fazla bilgi ve rehberlik için bkz.

- [HDInsight ile Azure Blob depolama kullanma](../hdinsight-hadoop-use-blob-storage.md)
- [Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Azure Blob depolama hesaplarını ML Hizmetleri kümesiyle kullanma

ML Hizmetleri kümenizi oluştururken birden fazla depolama hesabı belirttiyseniz, aşağıdaki yönergelerde bir ML Hizmetleri kümesindeki veri erişimi ve işlemler için ikincil bir hesabın nasıl kullanılacağı açıklanmaktadır. Aşağıdaki depolama hesaplarını ve kapsayıcısını varsayın: **storage1** ve **kapsayıcı1** adlı bir varsayılan kapsayıcı ve **container2** ile **Storage2** .

> [!WARNING]  
> Performans açısından, HDInsight kümesi belirttiğiniz birincil depolama hesabıyla aynı veri merkezinde oluşturulur. HDInsight kümesinden farklı bir konumda depolama hesabı kullanılması desteklenmez.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>HDInsight üzerinde ML Hizmetleri ile varsayılan depolamayı kullanma

1. SSH istemcisi kullanarak, kümenizin kenar düğümüne bağlanın. HDInsight kümeleri ile SSH kullanma hakkında bilgi için bkz. [HDInsight Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Örnek bir dosyayı, mysamplefile.csv,/Share dizinine kopyalayın.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. R Studio veya başka bir R konsoluna geçin ve ad düğümünü **varsayılan** olarak ve erişmek istediğiniz dosyanın konumuna ayarlamak için r kodu yazın.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Tüm dizin ve dosya başvuruları depolama hesabına işaret `wasbs://container1@storage1.blob.core.windows.net` . Bu, HDInsight kümesiyle ilişkili **varsayılan depolama hesabıdır** .

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>HDInsight 'ta ML Hizmetleri ile ek depolamayı kullanma

Şimdi, **Storage2** içindeki **container2** /private dizininde bulunan mysamplefile1.csv adlı bir dosyayı işlemek istediğinizi varsayalım.

R kodunuzda, **Storage2** depolama hesabına yönelik ad düğümü başvurusunu işaret edin.

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Tüm dizin ve dosya başvuruları artık depolama hesabına işaret noktasıdır `wasbs://container2@storage2.blob.core.windows.net` . Bu, belirttiğiniz **ad düğümüdür** .

`/user/RevoShare/<SSH username>` **Storage2** üzerindeki dizini aşağıdaki şekilde yapılandırın:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>ML Hizmetleri kümesiyle Azure Data Lake Storage 1. kullanma

HDInsight kümeniz ile Data Lake Storage 1. kullanmak için, kümenize, kullanmak istediğiniz her bir Azure Data Lake Storage 1. için erişim sağlamanız gerekir. Azure Data Lake Storage 1. depolama alanı veya ek depolama alanı olarak bir HDInsight kümesi oluşturmak için Azure portal kullanma hakkında yönergeler için, bkz. [Azure Portal kullanarak Data Lake Storage 1. Ile HDInsight kümesi oluşturma](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Daha sonra, önceki yordamda açıklandığı gibi ikincil bir Azure depolama hesabı gibi R betiğinizdeki depolamayı kullanırsınız.

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage 1. küme erişimi ekleyin

Data Lake Storage 1., HDInsight kümeniz ile ilişkili bir Azure Active Directory (Azure AD) hizmet sorumlusu kullanarak erişirsiniz.

1. HDInsight kümenizi oluştururken, **veri kaynağı** sekmesinden **küme Azure ad kimliği** ' ni seçin.

2. **Küme Azure ad kimliği** ILETIŞIM kutusunda **ad hizmet sorumlusu Seç**' in altında **Yeni oluştur**' u seçin.

Hizmet sorumlusuna bir ad verip bir parola oluşturduktan sonra, hizmet sorumlusunu Data Lake Storage ile ilişkilendirmek için **ADLS erişimini Yönet** ' e tıklayın.

Küme oluşturma sonrasında bir veya daha fazla Data Lake Storage Gen1 hesabına küme erişimi eklemek de mümkündür. Bir Data Lake Storage 1. Azure portal girişi açın ve **Veri Gezgini > erişim > Ekle**' ye gidin.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>HDInsight 'ta ML hizmetlerinden Data Lake Storage 1. erişme

Data Lake Storage 1. erişim izni verildikten sonra, HDInsight 'ta ML Hizmetleri kümesindeki depolamayı ikincil bir Azure depolama hesabı gibi kullanabilirsiniz. Tek fark, ön ek **wasbs://** aşağıdaki gibi **adl://** olarak değişir:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

Aşağıdaki komutlar, Data Lake Storage 1. yok etme diziniyle yapılandırmak ve önceki örnekteki Sample. csv dosyasını eklemek için kullanılır:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>HDInsight 'ta ML Hizmetleri ile Azure dosya depolama kullanma

Ayrıca, [Azure dosyaları](https://azure.microsoft.com/services/storage/files/)adlı Edge düğümünde kullanılmak üzere uygun bir veri depolama seçeneği de vardır. Bir Azure depolama dosya paylaşımının Linux dosya sistemine takabilmesini sağlar. Bu seçenek, daha sonra gerekli olabilecek veri dosyalarını, R betiklerini ve sonuç nesnelerini depolamak için kullanışlı olabilir, özellikle de yerel dosya sistemini,

Azure dosyalarının önemli bir avantajı, dosya paylaşımlarının Windows veya Linux gibi desteklenen bir IŞLETIM sistemine sahip olan herhangi bir sistem tarafından bağlanabileceği ve kullanılabileceği bir avantajdır. Örneğin, sizin veya takımınızda bulunan başka bir HDInsight kümesi tarafından, Azure VM tarafından veya şirket içi bir sistem tarafından kullanılabilir. Daha fazla bilgi için bkz.

- [Azure Dosya depolamayı Linux ile kullanma](../../storage/files/storage-how-to-use-files-linux.md)
- [Windows 'da Azure dosya depolama kullanma](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Sonraki adımlar

- [HDInsight 'ta ML Hizmetleri kümesine genel bakış](r-server-overview.md)
- [HDInsight üzerinde ML Services kümesi için işlem bağlamı seçenekleri](r-server-compute-contexts.md)
- [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
