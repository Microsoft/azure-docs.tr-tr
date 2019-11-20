---
title: 'Öğretici: Azure HDInsight üzerinde Apache Hive kullanarak ayıklama, dönüştürme, yükleme (ETL) işlemleri gerçekleştirin'
description: Bu öğreticide, ham CSV veri kümesinden veri çıkarmayı, Azure HDInsight üzerinde Apache Hive kullanarak dönüştürmeyi ve sonra Sqoop kullanarak dönüştürülmüş verileri Azure SQL veritabanı 'na yüklemeyi öğrenirsiniz.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f58785b17a1e6236636744c32dac07a6c9ed138d
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992248"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Öğretici: Azure HDInsight üzerinde Apache Hive kullanarak verileri ayıklama, dönüştürme ve yükleme

Bu öğreticide, bir ETL işlemi gerçekleştirirsiniz: verileri ayıklama, dönüştürme ve yükleme. Ham bir CSV veri dosyası alır, Azure HDInsight kümesine içeri aktarabilir, Apache Hive dönüştürürler ve Apache Sqoop ile bir Azure SQL veritabanı 'na yüklersiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Verileri bir HDInsight kümesine ayıklayın ve karşıya yükleyin.
> * Apache Hive kullanarak verileri dönüştürün.
> * Sqoop kullanarak verileri bir Azure SQL veritabanına yükleyin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

* **HDInsight için yapılandırılmış Azure Data Lake Storage 2. depolama hesabı**

    Bkz. [Azure HDInsight kümeleri ile Azure Data Lake Storage 2. kullanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **HDInsight 'ta Linux tabanlı Hadoop kümesi**

    Bkz [. hızlı başlangıç: Azure portal](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal)kullanarak Azure HDInsight 'ta Apache Hadoop ve Apache Hive ile çalışmaya başlayın.

* **Azure SQL veritabanı**: Azure SQL veritabanını bir hedef veri deposu olarak kullanacaksınız. SQL veritabanınız yoksa bkz. [Azure portalında Azure SQL veritabanı oluşturma](../../sql-database/sql-database-get-started.md).

* **Azure CLI**: Azure CLı 'yi yüklemediyseniz bkz. [Azure CLI 'Yı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Bir Secure Shell (SSH) istemcisi**: Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'a (Hadoop) bağlanma](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Uçuş verilerini indirme

1. [Araştırma ve yenilikçi teknoloji yönetimi, nakliye Istatistikleri Bürosu ' nı](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)inceleyin.

2. Sayfada aşağıdaki değerleri seçin:

   | Adı | Değer |
   | --- | --- |
   | Yıl Filtresi |2013 |
   | Dönem Filtresi |Ocak |
   | Alanlar |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, Originairportıd, Origin, OriginCityName, OriginState, Destairportıd, dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, dalgalı Therdelay, NASDelay, SecurityDelay, Lateaırcraftdelay. |
   
   Diğer tüm alanları temizleyin.

3. **Download** (İndir) seçeneğini belirleyin. Seçtiğiniz veri alanlarını içeren bir .zip dosyası alırsınız.

## <a name="extract-and-upload-the-data"></a>Verileri ayıklama ve karşıya yükleme

Bu bölümde, HDInsight kümenize verileri yükleyecek ve ardından bu verileri Data Lake Storage 2. hesabınıza kopyalayacaksınız.

1. Bir komut istemi açın ve. zip dosyasını HDInsight kümesi baş düğümüne yüklemek için aşağıdaki güvenli kopya (SCP) komutunu kullanın:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * `<file-name>` Yer tutucusunu. zip dosyasının adıyla değiştirin.
   * `<ssh-user-name>` Yer tutucusunu HDInsight kümesi için SSH oturum açma ile değiştirin.
   * `<cluster-name>` Yer tutucusunu HDInsight kümesinin adıyla değiştirin.

   SSH oturum açma bilgilerinizi doğrulamak için bir parola kullanıyorsanız parola girmeniz istenir.

   Ortak anahtar kullanıyorsanız, eşleşen özel anahtarın yolunu belirtmek için `-i` parametresini kullanmanız gerekebilir. Örneğin, `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Karşıya yükleme tamamlandıktan sonra SSH kullanarak kümeye bağlanın. Komut istemine aşağıdaki komutu girin:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. .zip dosyasını açmak için aşağıdaki komutu kullanın:

   ```bash
   unzip <file-name>.zip
   ```

   Komut bir **. csv** dosyası ayıklar.

4. Data Lake Storage 2. kapsayıcısını oluşturmak için aşağıdaki komutu kullanın.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Yer tutucusunu `<container-name>` , kapsayıcınıza vermek istediğiniz adla değiştirin.

   `<storage-account-name>` Yer tutucusunu depolama hesabınızın adıyla değiştirin.

5. Bir dizin oluşturmak için aşağıdaki komutu kullanın.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. *. Csv* dosyasını dizine kopyalamak için aşağıdaki komutu kullanın:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Dosya adı boşluk veya özel karakterler içeriyorsa dosya adı etrafında tırnak işareti kullanın.

## <a name="transform-the-data"></a>Verileri dönüştürme

Bu bölümde, bir Apache Hive işini çalıştırmak için Beeline kullanırsınız.

Apache Hive işi kapsamında, verileri. csv dosyasından **gecikmeler**adlı bir Apache Hive tablosuna aktarırsınız.

1. HDInsight kümesi için zaten sahip olduğunuz SSH isteminden, **flightgecikmeleri. HQL**adlı yeni bir dosya oluşturmak ve düzenlemek için aşağıdaki komutu kullanın:

   ```bash
   nano flightdelays.hql
   ```

2. `<container-name>` Ve`<storage-account-name>` yer tutucuları kapsayıcınıza ve depolama hesabı adınızla değiştirerek aşağıdaki metni değiştirin. Sonra sağ fare tıklama düğmesi ile birlikte SHIFT tuşuna basarak metni nano konsola kopyalayıp yapıştırın.

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
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
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

3. Dosyayı CTRL + X kullan ' ı kullanarak kaydedin ve sorulduğunda yazın `Y` .

4. Hive’ı başlatmak ve **flightdelays.hql** dosyasını çalıştırmak için aşağıdaki komutu kullanın:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. __flightdelays.hql__ betiği çalışmayı tamamladıktan sonra aşağıdaki komutu kullanarak etkileşimli bir Beeline oturumu açın:

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

   Bu sorgu, hava durumundan kaynaklanan gecikmeler yaşayan şehirlerin bir listesini, ortalama gecikme süresi ile birlikte alır ve `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` konumuna kaydeder. Daha sonra, Sqoop bu konumdaki verileri okur ve Azure SQL Veritabanına aktarır.

7. Beeline’dan çıkmak için isteme `!quit` girin.

## <a name="create-a-sql-database-table"></a>SQL veritabanı tablosu oluşturma

Bu işlem için SQL veritabanınızda sunucu adına ihtiyacınız vardır. Sunucu adınızı bulmak için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com) gidin.

2. **SQL veritabanlarını**seçin.

3. Kullanmayı seçtiğiniz veritabanının adını filtreleyin. Sunucu adı, **Sunucu adı** sütununda listelenir.

4. Kullanmak istediğiniz veritabanının adını filtreleyin. Sunucu adı, **Sunucu adı** sütununda listelenir.

    ![Azure SQL server ayrıntılarını alma](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Azure SQL server ayrıntılarını alma")

    SQL Veritabanına bağlanıp tablo oluşturmanın çok sayıda yolu vardır. Aşağıdaki adımlarda HDInsight kümesinden [FreeTDS](https://www.freetds.org/) kullanılır.

5. FreeTDS yüklemek için küme ile kurulan bir SSH bağlantısından aşağıdaki komutu kullanın:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Yükleme tamamlandıktan sonra, SQL veritabanı sunucusuna bağlanmak için aşağıdaki komutu kullanın.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * `<server-name>` Yer tutucusunu SQL veritabanı sunucu adıyla değiştirin.

   * `<admin-login>` Yer tutucusunu SQL veritabanı için yönetici oturum kimliğiyle değiştirin.

   * `<database-name>` Yer tutucusunu veritabanı adıyla değiştirin

   İstendiğinde, SQL veritabanı yönetici oturumu açma parolasını girin.

   Aşağıdakine benzer bir çıktı alırsınız:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. `1>` İsteminde aşağıdaki deyimleri girin:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. `GO` deyimi girildiğinde önceki deyimler değerlendirilir.

   Sorgu, kümelenmiş dizine sahip olan **gecikmeler**adlı bir tablo oluşturur.

9. Tablonun oluşturulduğunu doğrulamak için aşağıdaki sorguyu kullanın:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   Çıktı aşağıdaki metne benzer:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Tsql yardımcı programından çıkış yapmak için `1>` istemine `exit` girin.

## <a name="export-and-load-the-data"></a>Verileri dışarı ve yükleme

Önceki bölümlerde, dönüştürülen verileri konumda `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`kopyaladınız. Bu bölümde, verileri `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` Azure SQL veritabanında oluşturduğunuz tabloya aktarmak için Sqoop 'yi kullanırsınız.

1. Sqoop’un SQL veritabanınızı görebildiğini doğrulamak için aşağıdaki komutu kullanın:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   Komut, **gecikmeler** tablosunu oluşturduğunuz veritabanı da dahil olmak üzere veritabanlarının bir listesini döndürür.

2. **Hivesampletable** tablosundan **gecikme** tablosuna veri aktarmak için aşağıdaki komutu kullanın:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop, **gecikmeler** tablosunu içeren veritabanına bağlanır ve verileri `/tutorials/flightdelays/output` dizinden **gecikmeler** tablosuna aktarır.

3. `sqoop` Komut bittikten sonra, veritabanına bağlanmak için TSQL yardımcı programını kullanın:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Verilerin **gecikmeler** tablosuna verildiğini doğrulamak için aşağıdaki deyimleri kullanın:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Tabloda verilerin listesini görürsünüz. Tablo, şehir adını ve bu şehre ait ortalama uçuş gecikme süresini içerir.

5. TSQL `exit` yardımcı programından çıkmak için girin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide kullanılan tüm kaynaklar önceden yüklenir. Temizlik gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

HDInsight 'ta verilerle çalışmanın daha fazla yolunu öğrenmek için aşağıdaki makaleye bakın:

> [!div class="nextstepaction"]
> [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
