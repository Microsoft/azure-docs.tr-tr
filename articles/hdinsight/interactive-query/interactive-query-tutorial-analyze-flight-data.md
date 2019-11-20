---
title: 'Öğretici: etkileşimli sorgu ile ETL işlemleri-Azure HDInsight'
description: Öğretici-ham CSV veri kümesinden verileri ayıklama, HDInsight 'ta etkileşimli sorgu kullanarak dönüştürme ve ardından dönüştürülen verileri Apache Sqoop kullanarak Azure SQL veritabanı 'na yükleme hakkında bilgi edinin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 07/02/2019
ms.author: hrasheed
ms.custom: hdinsightactive,mvc
ms.openlocfilehash: d1136c153a529f58db1de277ec84ac332b9f78ae
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494154"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Öğretici: Azure HDInsight 'ta etkileşimli sorgu kullanarak verileri ayıklama, dönüştürme ve yükleme

Bu öğreticide, genel kullanıma açık uçuş verilerinin ham CSV veri dosyasını alır, bunu HDInsight küme depolamasına içeri aktarabilir ve ardından Azure HDInsight 'ta etkileşimli sorgu kullanarak verileri dönüştürebilirsiniz. Veriler dönüştürüldükten sonra, bu verileri [Apache Sqoop](https://sqoop.apache.org/)kullanarak BIR Azure SQL veritabanına yüklersiniz.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Örnek uçuş verilerini indirme
> * Verileri bir HDInsight kümesine yükleme
> * Etkileşimli sorgu kullanarak verileri dönüştürme
> * Azure SQL veritabanında tablo oluşturma
> * Azure SQL veritabanına veri aktarmak için Sqoop kullanma

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde etkileşimli bir sorgu kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü**için **etkileşimli sorgu** seçme.

* Bir Azure SQL veritabanı. Azure SQL veritabanını bir hedef veri deposu olarak kullanacaksınız. SQL veritabanınız yoksa bkz. [Azure portalında Azure SQL veritabanı oluşturma](/azure/sql-database/sql-database-single-database-get-started).

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Uçuş verilerini indirme

1. [Araştırma ve yenilikçi teknoloji yönetimi, nakliye Istatistikleri Bürosu ' nı](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)inceleyin.

2. Sayfasında, tüm alanlar ' ı temizleyin ve ardından aşağıdaki değerleri seçin:

   | Ad | Değer |
   | --- | --- |
   | Yıl Filtresi |2019 |
   | Dönem Filtresi |Ocak |
   | Alanlar |Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, Originairportıd, Origin, OriginCityName, OriginState, Destairportıd, dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, dalgalı gecikme, NASDelay, SecurityDelay, Lateaırcraftdelay. |

3. **Download** (İndir) seçeneğini belirleyin. Seçtiğiniz veri alanlarını içeren bir .zip dosyası alırsınız.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Verileri bir HDInsight kümesine yükleme

Bir HDInsight kümesiyle ilişkili depolama birimine veri yüklemenin birçok yolu vardır. Bu bölümde, verileri karşıya yüklemek için `scp` kullanacaksınız. Verileri karşıya yüklemenin diğer yollarını öğrenmek için bkz. [Verileri HDInsight'a yükleme](../hdinsight-upload-data.md).

1. . Zip dosyasını HDInsight kümesi baş düğümüne yükleyin. `FILENAME`. zip dosyasının adıyla ve HDInsight kümesinin adıyla `CLUSTERNAME` değiştirerek aşağıdaki komutu düzenleyin. Sonra bir komut istemi açın, çalışma dizininizi dosya konumu olarak ayarlayın ve ardından komutunu girin.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Devam etmek için Evet veya Hayır girmeniz istenirse, komut istemine Evet yazın ve ENTER tuşuna basın. Metin yazarken pencerede görünmez.

2. Karşıya yükleme tamamlandıktan sonra SSH kullanarak kümeye bağlanın. `CLUSTERNAME` HDInsight kümesinin adıyla değiştirerek aşağıdaki komutu düzenleyin. Ardından aşağıdaki komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Bir SSH bağlantısı kurulduktan sonra ortam değişkenini ayarlayın. `FILE_NAME`, `SQL_SERVERNAME`, `SQL_DATABASE`, `SQL_USER`ve `SQL_PASWORD` uygun değerlerle değiştirin. Sonra şu komutu girin:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Aşağıdaki komutu girerek. zip dosyasını ayıklayın:

    ```bash
    unzip $FILENAME.zip
    ```

5. HDInsight depolamada bir dizin oluşturun ve ardından aşağıdaki komutu girerek. csv dosyasını dizine kopyalayın:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Hive sorgusu kullanarak veri dönüştürme

Bir HDInsight kümesi üzerinde Hive işi çalıştırmanın çok sayıda yolu vardır. Bu bölümde, bir Hive işini çalıştırmak için [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) kullanırsınız. Hive işi çalıştırmanın diğer yöntemleri hakkında daha fazla bilgi için bkz. [HDInsight üzerinde Apache Hive kullanma](../hadoop/hdinsight-use-hive.md).

Hive işinin bir parçası olarak, verileri .csv dosyasından **Delays** adlı bir Hive tablosuna aktarın.

1. HDInsight kümesi için zaten sahip olduğunuz SSH isteminden, **flightgecikmeleri. HQL**adlı yeni bir dosya oluşturmak ve düzenlemek için aşağıdaki komutu kullanın:

    ```bash
    nano flightdelays.hql
    ```

2. Bu dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Dosyayı kaydetmek için **CTRL + X**, ardından **y**tuşlarına basın ve ardından girin.

4. Hive’ı başlatmak ve **flightdelays.hql** dosyasını çalıştırmak için aşağıdaki komutu kullanın:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. **flightdelays.hql** betiği çalışmayı tamamladıktan sonra aşağıdaki komutu kullanarak etkileşimli bir Beeline oturumu açın:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. `jdbc:hive2://localhost:10001/>` istemini aldığınızda, içeri aktarılan uçuş gecikme verilerini almak için aşağıdaki sorguyu kullanın:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Bu sorgu, hava durumundan kaynaklanan gecikmeler yaşayan şehirlerin bir listesini, ortalama gecikme süresi ile birlikte alır ve `/tutorials/flightdelays/output` konumuna kaydeder. Daha sonra, Sqoop bu konumdaki verileri okur ve Azure SQL Veritabanına aktarır.

7. Beeline’dan çıkmak için isteme `!quit` girin.

## <a name="create-a-sql-database-table"></a>SQL veritabanı tablosu oluşturma

SQL Veritabanına bağlanıp tablo oluşturmanın çok sayıda yolu vardır. Aşağıdaki adımlarda HDInsight kümesinden [FreeTDS](http://www.freetds.org/) kullanılır.

1. FreeTDS 'yi yüklemek için, kümeye açık SSH bağlantısından aşağıdaki komutu kullanın:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Yükleme tamamlandıktan sonra SQL Veritabanı sunucusuna bağlanmak için aşağıdaki komutu kullanın.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Aşağıdakine benzer bir çıktı alırsınız:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. `1>` isteminde aşağıdaki satırları girin:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    `GO` deyimi girildiğinde önceki deyimler değerlendirilir. Bu ifade, bir kümelenmiş dizine sahip **gecikmeler**adlı bir tablo oluşturur.

    Tablonun oluşturulduğunu doğrulamak için aşağıdaki sorguyu kullanın:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Çıktı aşağıdaki metne benzer:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Tsql yardımcı programından çıkış yapmak için `exit` istemine `1>` girin.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Apache Sqoop kullanarak verileri SQL veritabanı 'na aktarma

Önceki bölümlerde, `/tutorials/flightdelays/output` konumunda dönüştürülen verileri kopyaladınız. Bu bölümde, verileri `/tutorials/flightdelays/output` dizininden Azure SQL veritabanında oluşturduğunuz tabloya aktarmak için Sqoop kullanacaksınız.

1. Sqoop 'nin SQL veritabanınızı aşağıdaki komutu girerek görebildiğini doğrulayın:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Bu komut, daha önce `delays` tablosunu oluşturduğunuz veritabanı da dahil olmak üzere veritabanlarının bir listesini döndürür.

2. Aşağıdaki komutu girerek `/tutorials/flightdelays/output` verileri `delays` tabloya aktarın:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop, `delays` tablosunu içeren veritabanına bağlanır ve verileri `/tutorials/flightdelays/output` dizininden `delays` tablosuna aktarır.

3. Sqoop komutu bittikten sonra, aşağıdaki komutu girerek veritabanına bağlanmak için TSQL yardımcı programını kullanın:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Verilerin delays tablosuna aktarıldığını doğrulamak için aşağıdaki ifadeleri kullanın:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Tabloda verilerin listesini görürsünüz. Tablo, şehir adını ve bu şehre ait ortalama uçuş gecikme süresini içerir. 

    Tsql yardımcı programından çıkmak için `exit` yazın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ham bir CSV veri dosyası aldınız, bir HDInsight küme depolamasına içeri aktardınız ve sonra Azure HDInsight 'ta etkileşimli sorgu kullanarak verileri dönüştürürsünüz.  Apache Hive ambar Bağlayıcısı hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
>[Hive ambarı Bağlayıcısı ile Apache Spark ve Apache Hive tümleştirme](./apache-hive-warehouse-connector.md)
