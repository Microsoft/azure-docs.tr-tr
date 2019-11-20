---
title: Azure SQL veritabanı 'na veri okumak ve yazmak için Apache Spark kullanma
description: HDInsight Spark kümesi ile Azure SQL veritabanı arasında veri okumak, veri yazmak ve verileri bir SQL veritabanına aktarmak için bir bağlantı ayarlamayı öğrenin
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: cc225f4cae3935212844c19464afc716092e73ca
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035190"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Azure SQL veritabanı 'na veri okumak ve yazmak için HDInsight Spark kümesi kullanma

Azure HDInsight 'ta bir Apache Spark kümesini Azure SQL veritabanı ile bağlamayı ve sonra verileri SQL veritabanına okuma, yazma ve akışa alma hakkında bilgi edinin. Bu makaledeki yönergeler, Scala kod parçacıklarını çalıştırmak için bir [Jupyter Notebook](https://jupyter.org/) kullanır. Ancak, Scala veya Python 'da tek başına bir uygulama oluşturabilir ve aynı görevleri gerçekleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure HDInsight Spark kümesi *.  [HDInsight 'ta Apache Spark kümesi oluşturma bölümündeki](apache-spark-jupyter-spark-sql.md)yönergeleri izleyin.

* Azure SQL veritabanı. [Azure SQL veritabanı oluşturma](../../sql-database/sql-database-get-started-portal.md)bölümündeki yönergeleri izleyin. Örnek **AdventureWorksLT** şeması ve verilerle bir veritabanı oluşturduğunuzdan emin olun. Ayrıca, istemci IP adresinin sunucudaki SQL veritabanına erişmesine izin vermek için sunucu düzeyinde bir güvenlik duvarı kuralı oluşturduğunuzdan emin olun. Güvenlik duvarı kuralını ekleme yönergeleri aynı makalede bulunur. Azure SQL veritabanınızı oluşturduktan sonra, aşağıdaki değerleri yararlı tutmanız gerekir. Bir Spark kümesinden veritabanına bağlanmanız gerekir.

    * Azure SQL veritabanını barındıran sunucu adı.
    * Azure SQL veritabanı adı.
    * Azure SQL veritabanı yönetici Kullanıcı adı/parolası.

* SQL Server Management Studio (SSMS). [Verileri bağlanmak ve sorgulamak IÇIN SSMS kullanma](../../sql-database/sql-database-connect-query-ssms.md)yönergelerini izleyin.

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook oluşturma

Spark kümesiyle ilişkili bir [Jupyter Notebook](https://jupyter.org/) oluşturarak başlayın. Bu makalede kullanılan kod parçacıklarını çalıştırmak için bu not defterini kullanırsınız.

1. [Azure Portal](https://portal.azure.com/), kümenizi açın.
1. Sağ taraftaki **küme panoları** altında **Jupyter Not defteri** ' ni seçin.  **Küme panoları**görmüyorsanız, sol menüden **genel bakış** ' ı seçin. İstenirse, küme için yönetici kimlik bilgilerini girin.

    (./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Spark üzerinde Jupyter Not defteri") ![Apache Spark Jupyter Not defteri]

   > [!NOTE]  
   > Ayrıca, tarayıcınızda aşağıdaki URL 'YI açarak Spark kümesinde Jupyter not defterine erişebilirsiniz. **CLUSTERNAME** değerini kümenizin adıyla değiştirin:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Jupyter not defterinde, sağ üst köşeden **Yeni**' ye ve ardından **Spark** ' a tıklayarak bir Scala Not defteri oluşturun. HDInsight Spark kümesindeki jupi Not defterleri, Python2 uygulamaları için **Pyspark** çekirdeği ve Python3 uygulamaları için **PySpark3** çekirdeğini de sağlar. Bu makalede bir Scala Not defteri oluşturacağız.

    (./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Spark 'Ta Jupyter Not defteri Için Spark kernels") !['de Jupyter Not defteri Için kernels]

    Çekirdekler hakkında daha fazla bilgi için bkz. [HDInsight’ta Apache Spark kümeleri ile Jupyter not defterleri kullanma](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Bu makalede Spark (Scala) çekirdeği kullanıyoruz çünkü Spark 'tan SQL veritabanı 'na veri akışı yalnızca Scala ve Java 'da desteklenmektedir. SQL 'e okuma ve yazma işlemi, bu makalede tutarlılık için Python kullanılarak yapılabilir, ancak üç işlem için de Scala kullanırız.

1. Bu, varsayılan adı **Adsız**olan yeni bir not defteri açar. Not defteri adına tıklayın ve seçtiğiniz bir adı girin.

    ![Not defteri adını belirtme](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Not defteri adını belirtme")

Artık uygulamanızı oluşturmaya başlayabilirsiniz.

## <a name="read-data-from-azure-sql-database"></a>Azure SQL veritabanı 'ndan veri okuma

Bu bölümde, AdventureWorks veritabanında bulunan bir tablodan (örneğin, **SalesLT. Address**) verileri okuyabilirsiniz.

1. Yeni bir Jupyter not defterinde, bir kod hücresinde aşağıdaki kod parçacığını yapıştırın ve yer tutucu değerlerini Azure SQL veritabanınızın değerleriyle değiştirin.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Kod hücresini çalıştırmak için **SHIFT + ENTER** tuşlarına basın.  

1. Spark dataframe API 'Lerine geçirebilmeniz için bir JDBC URL 'SI oluşturmak üzere aşağıdaki kod parçacığını kullanın. Kod, parametreleri tutmak için bir `Properties` nesnesi oluşturur. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Azure SQL veritabanınızdaki bir tablodaki verilerle bir dataframe oluşturmak için aşağıdaki kod parçacığını kullanın. Bu kod parçacığında, **AdventureWorksLT** veritabanının bir parçası olarak kullanılabilen bir **SalesLT. Address** tablosu kullanıyoruz. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Artık, veri şemasını alma gibi veri çerçevesi üzerinde işlemler gerçekleştirebilirsiniz:

       sqlTableDF.printSchema

    Aşağıdakine benzer bir çıktı görürsünüz:

    ![şema çıkış](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "şeması çıkışı")

1. Ayrıca, ilk 10 satırı alma gibi işlemleri de yapabilirsiniz.

       sqlTableDF.show(10)

1. Ya da veri kümesinden belirli sütunları alın.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Azure SQL veritabanı 'na veri yazma

Bu bölümde, Azure SQL veritabanı 'nda tablo oluşturmak ve verileri veriyle doldurmak için kümede bulunan örnek bir CSV dosyası kullanıyoruz. Örnek CSV dosyası (**HVAC. csv**) `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` ' deki tüm HDInsight kümelerinde kullanılabilir.

1. Yeni bir Jupyter not defterinde, bir kod hücresinde aşağıdaki kod parçacığını yapıştırın ve yer tutucu değerlerini Azure SQL veritabanınızın değerleriyle değiştirin.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Kod hücresini çalıştırmak için **SHIFT + ENTER** tuşlarına basın.  

1. Aşağıdaki kod parçacığı Spark dataframe API 'Lerine geçirebilmeniz için bir JDBC URL 'SI oluşturur. Kod, parametreleri tutmak için bir `Properties` nesnesi oluşturur. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. HVAC. csv içindeki verilerin şemasını ayıklamak için aşağıdaki kod parçacığını kullanın ve veri çerçevesindeki CSV 'den verileri yüklemek için şemayı kullanın, `readDf`. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Geçici bir tablo oluşturmak için `readDf` dataframe ' i `temphvactable` ' i kullanın. Sonra bir Hive tablosu oluşturmak için geçici tabloyu kullanın, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Son olarak, Hive tablosunu kullanarak Azure SQL veritabanı 'nda tablo oluşturun. Aşağıdaki kod parçacığı Azure SQL veritabanı 'nda `hvactable` oluşturur.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. SSMS kullanarak Azure SQL veritabanı 'na bağlanın ve bir @no__t olduğunu doğrulayın.

    a. SSMS 'yi başlatın ve aşağıdaki ekran görüntüsünde gösterildiği gibi bağlantı ayrıntılarını sağlayarak Azure SQL veritabanına bağlanın.

    SSMS1 kullanarak SQL veritabanı !['Na bağlanma](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "SSMS1 kullanarak SQL veritabanı 'na bağlanma")

    b. **Nesne Gezgini**, Azure SQL veritabanı ve tablo düğümünü genişleterek **dbo. hboş tablosunun** oluşturulduğunu görüntüleyin.

    SSMS2 kullanarak SQL veritabanı !['Na bağlanma](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "SSMS2 kullanarak SQL veritabanı 'na bağlanma")

1. Tablodaki sütunları görmek için SSMS 'de bir sorgu çalıştırın.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Azure SQL veritabanı 'na veri akışı

Bu bölümde, önceki bölümde yer alan Azure SQL veritabanı 'nda zaten oluşturduğunuz **hboş tabloya** veri akışı yaptık.

1. İlk adım olarak, **hboş tablosunda**hiçbir kayıt bulunmadığından emin olun. SSMS 'yi kullanarak tabloda aşağıdaki sorguyu çalıştırın.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. HDInsight Spark kümesinde yeni bir Jupyter Not defteri oluşturun. Bir kod hücresinde aşağıdaki kod parçacığını yapıştırın ve ardından **SHIFT + enter**tuşlarına basın:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. **HVAC. csv** dosyasından hboş tabloya veri akışı yaptık. HVAC. csv dosyası `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` ' da kümede kullanılabilir. Aşağıdaki kod parçacığında, ilk olarak akışa alınacak verilerin şemasını alırız. Daha sonra, bu şemayı kullanarak bir akış veri çerçevesi oluşturacağız. Parçacığı bir kod hücresine yapıştırın ve çalıştırmak için **SHIFT + enter** tuşlarına basın.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. Çıkış **HVAC. csv**şemasını gösterir. **Hboş tablosu** aynı şemaya de sahiptir. Çıktı, tablodaki sütunları listeler.

    ![hdınsight Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "tablo") şeması tablosu şeması

1. Son olarak, HVAC. csv dosyasından verileri okumak ve Azure SQL veritabanı 'nda **hboş tablo** olarak akışa almak için aşağıdaki kod parçacığını kullanın. Parçacığı bir kod hücresine yapıştırın, yer tutucu değerlerini Azure SQL veritabanınızın değerleriyle değiştirin ve ardından çalıştırmak için **SHIFT + enter** tuşlarına basın.

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

1. SQL Server Management Studio (SSMS) ' de aşağıdaki sorguyu çalıştırarak verilerin **hboş tablosuna** aktarılmakta olduğunu doğrulayın. Sorguyu her çalıştırdığınızda, tablodaki satır sayısını artan şekilde gösterir.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Data Lake Storage verileri çözümlemek için HDInsight Spark kümesini kullanma](apache-spark-use-with-data-lake-store.md)
* [EventHub kullanarak yapılandırılmış akış olaylarını işleme](apache-spark-eventhub-structured-streaming.md)
* [HDInsight üzerinde Apache Kafka ile yapılandırılmış Apache Spark akışını kullanma](../hdinsight-apache-kafka-spark-structured-streaming.md)
