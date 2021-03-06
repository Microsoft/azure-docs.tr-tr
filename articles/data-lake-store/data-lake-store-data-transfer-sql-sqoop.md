---
title: Data Lake Storage 1. ve Azure SQL-Sqoop arasında veri kopyalama | Microsoft Docs
description: Azure SQL veritabanı ve Azure Data Lake Storage 1. arasında veri kopyalamak için Sqoop kullanma
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109196"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Sqoop kullanarak Data Lake Storage 1. ve Azure SQL veritabanı arasında veri kopyalama

Verileri Azure SQL veritabanı ve Azure Data Lake Storage 1. arasında içeri ve dışarı aktarmak için Apache Sqoop 'yi nasıl kullanacağınızı öğrenin.

## <a name="what-is-sqoop"></a>Sqoop nedir?

Büyük veri uygulamaları, Günlükler ve dosyalar gibi yapılandırılmamış ve yarı yapılandırılmış verileri işlemeye yönelik doğal bir seçimdir. Bununla birlikte, ilişkisel veritabanlarında depolanan yapılandırılmış verileri de işlemek zorunda kalabilirsiniz.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) , ilişkisel veritabanları ve Data Lake Storage 1. gibi büyük bir veri deposu arasında veri aktarmak için tasarlanan bir araçtır. Azure SQL veritabanı gibi bir ilişkisel veritabanı yönetim sisteminden (RDBMS) verileri Data Lake Storage 1. içine aktarmak için bu işlemi kullanabilirsiniz. Daha sonra büyük veri iş yüklerini kullanarak verileri dönüştürebilir ve analiz edebilir ve ardından verileri bir RDBMS 'ye geri aktarabilirsiniz. Bu makalede, Azure SQL veritabanı 'nda içeri/dışarı aktarılacak ilişkisel veritabanınız olarak bir veritabanı kullanırsınız.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage 1. hesabı**. Hesabı oluşturma hakkında yönergeler için bkz. [Azure Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-portal.md)
* Data Lake Storage 1. hesabına erişimi olan **Azure HDInsight kümesi** . Bkz. [Data Lake Storage 1. HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md). Bu makalede Data Lake Storage 1. erişimi olan bir HDInsight Linux kümeniz olduğunu varsaymaktadır.
* **Azure SQL veritabanı**. Azure SQL veritabanı 'nda veritabanı oluşturma hakkında yönergeler için bkz. [Azure SQL veritabanı 'nda veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md)

## <a name="create-sample-tables-in-the-database"></a>Veritabanında örnek tablolar oluşturma

1. Başlamak için veritabanında iki örnek tablo oluşturun. Veritabanına bağlanmak ve ardından aşağıdaki sorguları çalıştırmak için [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) veya Visual Studio kullanın.

    **Table1 oluştur**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **Table2 oluştur**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. **Table1**'e bazı örnek veriler eklemek için aşağıdaki komutu çalıştırın. **Table2** boş bırakın. Daha sonra, **Table1** adresinden Data Lake Storage 1. verileri içeri aktaracaksınız. Daha sonra Data Lake Storage 1. verileri **Table2**'e dışarı aktaracaksınız.

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage 1. erişimi olan bir HDInsight kümesinden Sqoop kullanın

An HDInsight kümesinde Sqoop paketleri zaten var. HDInsight kümesini ek depolama alanı olarak Data Lake Storage 1. kullanacak şekilde yapılandırdıysanız, verileri Azure SQL veritabanı gibi ilişkisel bir veritabanı ve bir Data Lake Storage 1. hesabı arasında içeri/dışarı aktarmak için Sqoop (herhangi bir yapılandırma değişikliği olmadan) kullanabilirsiniz.

1. Bu makalede, kümeye bağlanmak için SSH kullanmanız gereken bir Linux kümesi oluşturduğunuzu varsayalım. Bkz. [Linux tabanlı HDInsight kümesine bağlanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Data Lake Storage 1. hesabına kümeden erişip erişemeyeceğinizi doğrulayın. SSH isteminde aşağıdaki komutu çalıştırın:

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   Bu komut Data Lake Storage 1. hesabındaki dosyaların/klasörlerin bir listesini sağlar.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Azure SQL veritabanındaki verileri Data Lake Storage 1. içine aktarın

1. Sqoop paketlerinin kullanılabildiği dizine gidin. Genellikle, bu konum `/usr/hdp/<version>/sqoop/bin` .

1. **Table1** 'deki verileri Data Lake Storage 1. hesabına aktarın. Aşağıdaki sözdizimini kullanın:

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   **SQL-Database-Server-Name** yer tutucusu, veritabanının çalıştığı sunucunun adını temsil eder. **SQL-Database-Name** yer tutucusu gerçek veritabanı adını temsil eder.

   Örneğin,

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. Verilerin Data Lake Storage 1. hesabına aktarıldığını doğrulayın. Şu komutu çalıştırın:

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   Aşağıdaki çıkışı görmelisiniz.

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   Her **parça-b-** _ dosyası kaynak tablodaki bir satıra karşılık gelir _ *Table1 * *. Doğrulanacak bölüm-b dosyalarının içeriğini görüntüleyebilirsiniz* .

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Data Lake Storage 1. verileri Azure SQL veritabanı 'na aktarma

1. Data Lake Storage 1. hesabındaki verileri, Azure SQL veritabanında bulunan **Table2** boş tablosuna aktarın. Aşağıdaki sözdizimini kullanın.

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   Örneğin,

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. Verilerin SQL veritabanı tablosuna yüklendiğini doğrulayın. [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) veya Visual Studio kullanarak Azure SQL veritabanına bağlanın ve ardından aşağıdaki sorguyu çalıştırın.

    ```tsql
    SELECT * FROM TABLE2
    ```

   Bu komut aşağıdaki çıktıya sahip olmalıdır.

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Sqoop kullanılırken performans konuları

Data Lake Storage 1. verileri kopyalamak için Sqoop işinizin performans ayarlaması hakkında bilgi için [Sqoop performans blog gönderisine](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure depolama Bloblarından veri kopyalama Data Lake Storage 1.](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight 'ı Data Lake Storage 1. ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)