---
title: Azure SQL veritabanı 'na veri okumak ve yazmak için Apache Spark kullanma
description: HDInsight Spark kümesi ile Azure SQL veritabanı arasında bir bağlantı ayarlamayı öğrenin. Verileri okumak, veri yazmak ve bir SQL veritabanına veri akışı yapmak için
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 0c9c3b2d915e54cf954703c56c2087637cc80aa0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864627"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Azure SQL veritabanı 'na veri okumak ve yazmak için HDInsight Spark kümesi kullanma

Azure SQL veritabanı ile Azure HDInsight 'ta bir Apache Spark kümesini bağlamayı öğrenin. Sonra verileri SQL veritabanına okuyun, yazın ve veri akışını yapın. Bu makaledeki yönergeler, Scala kod parçacıklarını çalıştırmak için bir Jupyter Notebook kullanır. Ancak, Scala veya Python 'da tek başına bir uygulama oluşturabilir ve aynı görevleri gerçekleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure HDInsight Spark küme.  [HDInsight 'ta Apache Spark kümesi oluşturma bölümündeki](apache-spark-jupyter-spark-sql.md)yönergeleri izleyin.

* Azure SQL Veritabanı. [Azure SQL veritabanı 'nda veritabanı oluşturma bölümündeki](../../azure-sql/database/single-database-create-quickstart.md)yönergeleri izleyin. Örnek **AdventureWorksLT** şeması ve verilerle bir veritabanı oluşturduğunuzdan emin olun. Ayrıca, istemcinizin IP adresinin SQL veritabanına erişmesine izin vermek için sunucu düzeyinde bir güvenlik duvarı kuralı oluşturduğunuzdan emin olun. Güvenlik duvarı kuralını ekleme yönergeleri aynı makalede bulunur. SQL veritabanınızı oluşturduktan sonra, aşağıdaki değerleri yararlı tutmanız gerekir. Bir Spark kümesinden veritabanına bağlanmanız gerekir.

    * Sunucu adı.
    * Veritabanı adı.
    * Azure SQL veritabanı yönetici Kullanıcı adı/parolası.

* SQL Server Management Studio (SSMS). [Verileri bağlanmak ve sorgulamak IÇIN SSMS kullanma](../../azure-sql/database/connect-query-ssms.md)yönergelerini izleyin.

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook oluşturma

Spark kümesiyle ilişkili bir Jupyter Notebook oluşturarak başlayın. Bu makalede kullanılan kod parçacıklarını çalıştırmak için bu not defterini kullanırsınız.

1. [Azure Portal](https://portal.azure.com/), kümenizi açın.
1. Sağ tarafta **küme panoları** altında **Jupyter Notebook** seçin.  **Küme panoları** görmüyorsanız, sol menüden **genel bakış** ' ı seçin. İstenirse, küme için yönetici kimlik bilgilerini girin.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png " alt-text="Apache Spark Jupyter Notebook" border="true":::

   > [!NOTE]  
   > Ayrıca, tarayıcınızda aşağıdaki URL 'YI açarak Spark kümesindeki Jupyter Notebook de erişebilirsiniz. **CLUSTERNAME** değerini kümenizin adıyla değiştirin:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Jupyter Notebook, sağ üst köşedeki **Yeni**' ye ve ardından **Spark** ' a tıklayarak bir Scala Not defteri oluşturun. HDInsight Spark kümesindeki jupi Not defterleri, Python2 uygulamaları için **Pyspark** çekirdeği ve Python3 uygulamaları için **PySpark3** çekirdeğini de sağlar. Bu makalede bir Scala Not defteri oluşturacağız.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png " alt-text="Spark üzerinde Jupyter Notebook için kernels" border="true":::

    Çekirdekler hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Spark kümeleriyle Jupyter Notebook çekirdekler kullanma](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Bu makalede Spark (Scala) çekirdeği kullanıyoruz çünkü Spark 'tan SQL veritabanı 'na veri akışı yalnızca Scala ve Java 'da desteklenmektedir. SQL 'e okuma ve yazma işlemi, bu makalede tutarlılık için Python kullanılarak yapılabilir, ancak üç işlem için de Scala kullanırız.

1. Varsayılan adla yeni bir not defteri **açılır.** Not defteri adına tıklayın ve seçtiğiniz bir adı girin.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png " alt-text="Not defteri adını belirtme" border="true":::

Artık uygulamanızı oluşturmaya başlayabilirsiniz.

## <a name="read-data-from-azure-sql-database"></a>Azure SQL veritabanı 'ndan veri okuma

Bu bölümde, AdventureWorks veritabanında bulunan bir tablodan (örneğin, **SalesLT. Address**) verileri okuyabilirsiniz.

1. Yeni bir Jupyter Notebook, bir kod hücresinde aşağıdaki kod parçacığını yapıştırın ve yer tutucu değerlerini veritabanınızın değerleriyle değiştirin.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Kod hücresini çalıştırmak için **SHIFT + ENTER** tuşlarına basın.  

1. Spark dataframe API 'Lerine geçirebilmeniz için bir JDBC URL 'SI oluşturmak üzere aşağıdaki kod parçacığını kullanın. Kod, `Properties` parametreleri tutacak bir nesne oluşturur. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Veritabanınızdaki bir tablodaki verilerle bir dataframe oluşturmak için aşağıdaki kod parçacığını kullanın. Bu kod parçacığında, `SalesLT.Address` **AdventureWorksLT** veritabanının bir parçası olarak kullanılabilen bir tablo kullanıyoruz. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Artık dataframe üzerinde, veri şemasını alma gibi işlemler yapabilirsiniz:

    ```scala
    sqlTableDF.printSchema
    ```

    Aşağıdaki görüntüye benzer bir çıktı görürsünüz:

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png " alt-text="şema çıkışı" border="true":::

1. Ayrıca, ilk 10 satırı alma gibi işlemleri de yapabilirsiniz.

    ```scala
    sqlTableDF.show(10)
    ```

1. Ya da veri kümesinden belirli sütunları alın.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Azure SQL veritabanı 'na veri yazma

Bu bölümde, veritabanında bir tablo oluşturmak ve verileri veriyle doldurmak için kümede bulunan örnek bir CSV dosyası kullanıyoruz. Örnek CSV dosyası (**HVAC.csv**), adresindeki tüm HDInsight kümelerinde kullanılabilir `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` .

1. Yeni bir Jupyter Notebook, bir kod hücresinde aşağıdaki kod parçacığını yapıştırın ve yer tutucu değerlerini veritabanınızın değerleriyle değiştirin.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Kod hücresini çalıştırmak için **SHIFT + ENTER** tuşlarına basın.  

1. Aşağıdaki kod parçacığı Spark dataframe API 'Lerine geçirebilmeniz için bir JDBC URL 'SI oluşturur. Kod, `Properties` parametreleri tutacak bir nesne oluşturur. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Aşağıdaki kod parçacığını kullanarak HVAC.csv verilerin şemasını ayıklayın ve verileri bir dataframe içindeki CSV 'den yüklemek için şemayı kullanın `readDf` . Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. `readDf`Geçici bir tablo oluşturmak için veri çerçevesini kullanın `temphvactable` . Ardından, bir Hive tablosu oluşturmak için geçici tabloyu kullanın `hvactable_hive` .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Son olarak, veritabanınızda tablo oluşturmak için Hive tablosunu kullanın. Aşağıdaki kod parçacığı `hvactable` Azure SQL veritabanı 'nda oluşturulur.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. SSMS kullanarak Azure SQL veritabanı 'na bağlanın ve burada görmediğinizi doğrulayın `dbo.hvactable` .

    a. SSMS 'yi başlatın ve aşağıdaki ekran görüntüsünde gösterildiği gibi bağlantı ayrıntılarını sağlayarak Azure SQL veritabanına bağlanın.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png " alt-text="SSMS1 kullanarak SQL veritabanı 'na bağlanma" border="true":::

    b. **Nesne Gezgini**, **dbo. hboş tablosunun** oluşturulduğunu görmek için veritabanını ve tablo düğümünü genişletin.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png " alt-text="SSMS2 kullanarak SQL veritabanı 'na bağlanma" border="true":::

1. Tablodaki sütunları görmek için SSMS 'de bir sorgu çalıştırın.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Azure SQL veritabanı 'na veri akışı

Bu bölümde, önceki bölümde oluşturduğunuz içine veri akışı yaptık `hvactable` .

1. İlk adım olarak, içinde hiçbir kayıt bulunmadığından emin olun `hvactable` . SSMS 'yi kullanarak tabloda aşağıdaki sorguyu çalıştırın.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. HDInsight Spark kümesinde yeni bir Jupyter Notebook oluşturun. Bir kod hücresinde aşağıdaki kod parçacığını yapıştırın ve ardından **SHIFT + enter** tuşlarına basın:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. **HVAC.csv** verileri içine akışımız `hvactable` . HVAC.csv dosya üzerinde kümede kullanılabilir `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . Aşağıdaki kod parçacığında, ilk olarak akışa alınacak verilerin şemasını alırız. Daha sonra, bu şemayı kullanarak bir akış veri çerçevesi oluşturacağız. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. Çıktıda **HVAC.csv** şeması gösterilmektedir. `hvactable`Aynı şemaya da sahiptir. Çıktı, tablodaki sütunları listeler.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png " alt-text="' HDInsight Apache Spark şeması tablosu '" border="true":::

1. Son olarak, HVAC.csv verileri okumak ve veritabanınızdaki içine aktarmak için aşağıdaki kod parçacığını kullanın `hvactable` . Parçacığı bir kod hücresine yapıştırın, yer tutucu değerlerini veritabanınızın değerleriyle değiştirin ve ardından çalıştırmak için **SHIFT + enter** tuşlarına basın.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. `hvactable`SQL Server Management Studio (SSMS) ' de aşağıdaki sorguyu çalıştırarak verilerin öğesine aktarılmakta olduğunu doğrulayın. Sorguyu her çalıştırdığınızda, tablodaki satır sayısını artan şekilde gösterir.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Data Lake Storage verileri çözümlemek için HDInsight Spark kümesini kullanma](apache-spark-use-with-data-lake-store.md)
* [Azure HDInsight 'ta Apache Spark kümesinde verileri yükleme ve sorguları çalıştırma](apache-spark-load-data-run-query.md)
* [HDInsight üzerinde Apache Kafka ile yapılandırılmış Apache Spark akışını kullanma](../hdinsight-apache-kafka-spark-structured-streaming.md)
