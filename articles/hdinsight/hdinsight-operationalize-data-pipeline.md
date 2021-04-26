---
title: Veri Analizi ardışık düzeni oluşturma-Azure
description: Yeni veriler tarafından tetiklenen ve kısa sonuçlar üreten bir örnek veri işlem hattı ayarlayın ve çalıştırın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: c81eb092fa59cb890093e1e9acd0511e39b5047b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864219"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Veri analizi işlem hattını kullanıma hazır hale getirme

*Veri işlem hatları* çok sayıda veri analizi çözümünü daha az. Adından da anlaşılacağı gibi, veri işlem hattı ham verileri alır, temizler ve gerektiği gibi yeniden şekillendirir ve genellikle işlenen verileri depolamadan önce hesaplamalar veya toplamalar gerçekleştirir. İşlenen veriler istemciler, raporlar veya API 'Ler tarafından kullanılır. Bir veri işlem hattı, bir zamanlamaya göre veya yeni veriler tarafından tetiklendiğinde tekrarlanabilir sonuçlar sağlamalıdır.

Bu makalede, HDInsight Hadoop kümelerinde çalışan Oozie kullanılarak yinelenebilirlik için veri işlem hatlarınızın nasıl yapılacağı açıklanır. Örnek senaryo, hava yolu uçuş süresi serisi verilerini hazırlarken ve işleyen bir veri işlem hattı boyunca size yol gösterir.

Aşağıdaki senaryoda, giriş verileri bir ay için bir dizi uçuş verisi içeren düz bir dosyadır. Bu uçuş verileri, kaynak ve hedef Havaalanı, mil flown, ayrılma ve varış süresi gibi bilgileri içerir. Bu işlem hattına sahip amaç, her bir hava yolu 'ın dakikada ortalama ayrılma ve gelme gecikmeleri ve bu güne ait toplam mil flown ile her gün bir satıra sahip olduğu günlük hava yolu performansını özetler.

| YEAR | MONTH | DAY_OF_MONTH | TAŞıY |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10,142229 | 7,862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9,435449 | 5,482143 | 572289 |
| 2017 | 1 | 3 | DL | 6,935409 | -2,1893024 | 1909696 |

Örnek işlem hattı, yeni bir zaman döneminin uçuş verilerinin gelmesini bekler, daha sonra bu ayrıntılı uçuş bilgilerini, uzun süreli analizler için Apache Hive veri ambarınıza depolar. İşlem hattı, yalnızca günlük uçuş verilerini özetleyen çok daha küçük bir veri kümesi de oluşturur. Bu günlük uçuş Özeti verileri, bir Web sitesi gibi raporlar sağlamak üzere bir SQL veritabanına gönderilir.

Aşağıdaki diyagramda örnek işlem hattı gösterilmektedir.

:::image type="content" source="./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png" alt-text="HDI uçuş örneği veri ardışık düzenine genel bakış" border="false":::

## <a name="apache-oozie-solution-overview"></a>Apache Oozie çözümüne genel bakış

Bu işlem hattı, HDInsight Hadoop kümesinde çalışan Apache Oozie kullanır.

Oozie, *işlem hatlarını işlemler*, *iş akışları* ve *Koordinatörler* açısından açıklar. Eylemler, bir Hive sorgusu çalıştırma gibi gerçekleştirilecek fiili işi tespit. İş akışları eylemlerin sırasını tanımlar. Düzenleyiciler, iş akışının çalıştırılacağı zamanlamayı tanımlar. Düzenleyiciler, iş akışının bir örneğini başlatmadan önce yeni verilerin kullanılabilirliğini de bekleyebilir.

Aşağıdaki diyagramda Bu örnek Oozie işlem hattının üst düzey tasarımı gösterilmektedir.

:::image type="content" source="./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png" alt-text="Oozie uçuş örneği veri işlem hattı" border="false":::

## <a name="provision-azure-resources"></a>Azure kaynaklarını sağlama

Bu işlem hattı aynı konumda bir Azure SQL veritabanı ve bir HDInsight Hadoop kümesi gerektirir. Azure SQL veritabanı, ardışık düzen ve Oozie meta veri deposu tarafından oluşturulan özet verileri depolar.

### <a name="provision-azure-sql-database"></a>Azure SQL veritabanı sağlama

1. Azure SQL veritabanı oluşturun. Bkz. [Azure Portal Azure SQL veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md).

1. HDInsight kümenizin bağlı Azure SQL veritabanına erişebildiğinizden emin olmak için Azure SQL veritabanı güvenlik duvarı kurallarını Azure hizmetleri ve kaynaklarının sunucuya erişmesine izin verecek şekilde yapılandırın. Bu seçeneği, Azure portal **sunucu güvenlik duvarını ayarla**' yı seçerek ve Azure hizmetleri ve kaynaklarının Azure SQL veritabanı için **Bu sunucuya erişmesine izin ver** ' **i seçerek etkinleştirebilirsiniz** . Daha fazla bilgi için bkz. [IP güvenlik duvarı kuralları oluşturma ve yönetme](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. İşlem hattının her çalıştırmasıyla özetlenen verileri depolayacak tabloyu oluşturmak için [sorgu Düzenleyicisi](../azure-sql/database/single-database-create-quickstart.md#query-the-database) 'ni kullanarak aşağıdaki SQL deyimlerini yürütün `dailyflights` .

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Azure SQL veritabanınız artık hazır.

### <a name="provision-an-apache-hadoop-cluster"></a>Apache Hadoop kümesi sağlama

Özel bir meta veri deposu ile Apache Hadoop kümesi oluşturun. Portalda küme oluşturma sırasında, **depolama** sekmesinden, **meta veri deposu ayarları** altında SQL veritabanınızı seçtiğinizden emin olun. Bir meta veri deposu seçme hakkında daha fazla bilgi için bkz. [küme oluşturma sırasında özel bir meta veri deposu seçme](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Küme oluşturma hakkında daha fazla bilgi için bkz. [Linux 'Ta HDInsight kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>SSH tünelini ayarlamayı doğrulama

Koordinatör ve iş akışı örneklerinizin durumunu görüntülemek için Oozie web konsolunu kullanmak istiyorsanız, HDInsight kümenize bir SSH tüneli ayarlayın. Daha fazla bilgi için bkz. [SSH tüneli](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Ayrıca, SSH tüneli genelinde kümenizin Web kaynaklarına gözatabilmeniz için [Foxy ara sunucu](https://getfoxyproxy.org/) Uzantısı ile Chrome 'ı kullanabilirsiniz. Tünelin bağlantı noktası 9876 üzerindeki ana bilgisayar aracılığıyla tüm istekleri proxy olarak yapılandırın `localhost` . Bu yaklaşım, Windows 10 üzerinde Bash olarak da bilinen Linux için Windows alt sistemi ile uyumludur.

1. Kümenize bir SSH tüneli açmak için aşağıdaki komutu çalıştırın; burada `CLUSTERNAME` kümenizin adıdır:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Göz atarak baş düğüminizdeki ambarı 'na giderek tünelin çalıştığını doğrulayın:

    `http://headnodehost:8080`

1. **Oozie web konsoluna** , ambarı içinden erişmek için **Oozie**  >  **hızlı bağlantılara** > [Active Server] > **Oozie Web Kullanıcı arabirimine** gidin.

## <a name="configure-hive"></a>Hive 'yi yapılandırma

### <a name="upload-data"></a>Verileri karşıya yükleme

1. Bir ay için uçuş verileri içeren örnek bir CSV dosyası indirin. `2017-01-FlightData.zip` [HDInsight GITHUB deposundan](https://github.com/hdinsight/hdinsight-dev-guide) ZIP dosyasını indirin ve CSV dosyasına ayıklayın `2017-01-FlightData.csv` .

1. Bu CSV dosyasını HDInsight kümenize bağlı Azure depolama hesabına kopyalayın ve `/example/data/flights` klasöre yerleştirin.

    1. Dosyaları yerel makinenizden HDInsight kümesi baş düğümünüz yerel depolama alanına kopyalamak için SCP 'YI kullanın.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Kümenize bağlanmak için [SSH komutunu](./hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. Aşağıdaki komutu, `CLUSTERNAME` kümenizin adıyla değiştirerek düzenleyin ve ardından şu komutu girin:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. SSH oturumunda, dosyayı baş düğüm yerel depoınızdan Azure depolama 'ya kopyalamak için, bu komutu kullanın.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Tablo oluşturma

Örnek veriler artık kullanılabilir. Ancak, işlem hattı, bir diğeri ( `rawFlights` ) ve özetlenen veriler () için olmak üzere Iki Hive tablosu gerektirir `flights` . Bu tabloları aşağıdaki şekilde ambarı 'nda oluşturun.

1. ' A giderek, ambarı 'nda oturum açın `http://headnodehost:8080` .

2. Hizmetler listesinden **Hive** öğesini seçin.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png" alt-text="Apache ambarı hizmetleri listesi Hive seçme":::

3. Hive görünümü 2,0 etiketinin yanındaki **görünümü görüntülemek Için git** ' i seçin.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png" alt-text="Ambarı Apache Hive özet listesi":::

4. Sorgu metin alanında, tabloyu oluşturmak için aşağıdaki deyimleri yapıştırın `rawFlights` . `rawFlights`Tablo, `/example/data/flights` Azure depolama 'daki klasörü içinde CSV dosyaları için bir şema okuması sağlar.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. Tabloyu oluşturmak için **Yürüt** ' ü seçin.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png" alt-text="HDI ambarı Hizmetleri Hive sorgusu":::

6. Tabloyu oluşturmak için `flights` , sorgu metin alanındaki metni aşağıdaki deyimlerle değiştirin. `flights`Tablo, verileri yıla, aya ve aya göre yüklenen verileri bölümleyerek Hive tarafından yönetilen bir tablodur. Bu tablo, uçuşa göre bir satırın kaynak verilerinde en düşük ayrıntı düzeyi olan tüm geçmiş uçuş verilerini içerir.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Tabloyu oluşturmak için **Yürüt** ' ü seçin.

## <a name="create-the-oozie-workflow"></a>Oozie iş akışını oluşturma

İşlem hatları, genellikle belirli bir zaman aralığındaki verileri toplu halde işler. Bu durumda, işlem hattı uçuş verilerini günlük olarak işler. Bu yaklaşım, giriş CSV dosyalarının günlük, haftalık, aylık veya yıllık olarak ulaşmasını sağlar.

Örnek iş akışı, üç önemli adımda uçuş verilerini gün içinde işler:

1. Bu güne ait tarih aralığına ait verileri tablo tarafından temsil edilen kaynak CSV dosyasından ayıklamak `rawFlights` ve verileri tabloya eklemek için bir Hive sorgusu çalıştırın `flights` .
2. Bir Hive sorgusu çalıştırarak, günlük ve taşıyıcı tarafından özetlenen uçuş verilerinin bir kopyasını içeren, Hive içindeki bir hazırlama tablosunu dinamik olarak oluşturun.
3. Hive içindeki günlük hazırlama tablosundaki tüm verileri `dailyflights` Azure SQL veritabanı 'ndaki hedef tabloya kopyalamak Için Apache Sqoop 'yi kullanın. Sqoop, Azure depolama 'da bulunan Hive tablosunun arkasındaki verilerden kaynak satırları okur ve bir JDBC bağlantısı kullanarak bunları SQL veritabanı 'na yükler.

Bu üç adım bir Oozie iş akışı tarafından koordine edilir.

1. Yerel iş istasyonunuzdan adlı bir dosya oluşturun `job.properties` . Dosya için başlangıç içeriği olarak aşağıdaki metni kullanın.
Ardından, belirli ortamınızın değerlerini güncelleştirin. Metnin altındaki tablo her bir özelliği özetler ve kendi ortamınız için değerleri nerede bulabileceğinizi gösterir.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Özellik | Değer kaynağı |
    | --- | --- |
    | nameNode | HDInsight kümenize eklenen Azure depolama kapsayıcısının tam yolu. |
    | Jobtracker 'a | Etkin kümenizin YARN baş düğümüne ait iç ana bilgisayar adı. Ambarı giriş sayfasında, hizmetler listesinden YARN ' yi seçin ve ardından etkin Kaynak Yöneticisi ' ni seçin. Ana bilgisayar adı URI 'SI sayfanın en üstünde görüntülenir. 8050 numaralı bağlantı noktasını ekleyin. |
    | Adı | Hive eylemleri planlanırken kullanılan YARN kuyruğunun adı. Varsayılan değerde bırakın. |
    | oozie.use.sysdıtem. libpath | Doğru olarak bırakın. |
    | Uygulama tabanı dizininin | Oozie iş akışını ve destekleyici dosyaları dağıttığınız Azure depolama 'daki alt klasörün yolu. |
    | Oozie. WF. Application. Path | Çalıştırılacak Oozie iş akışının konumu `workflow.xml` . |
    | hiveScriptLoadPartition | Azure depolama 'daki yol, Hive sorgu dosyasına `hive-load-flights-partition.hql` . |
    | hiveScriptCreateDailyTable | Azure depolama 'daki yol, Hive sorgu dosyasına `hive-create-daily-summary-table.hql` . |
    | hiveDailyTableName | Hazırlama tablosu için kullanılacak dinamik olarak oluşturulan ad. |
    | hiveDataFolder | Azure depolama 'daki, hazırlama tablosunun içerdiği verilere yönelik yol. |
    | sqlDatabaseConnectionString | Azure SQL veritabanınıza JDBC sözdizimi bağlantı dizesi. |
    | sqlDatabaseTableName | Azure SQL veritabanı 'ndaki özet satırlarının eklendiği tablonun adı. Farklı bırak `dailyflights` . |
    | yıl | Uçuş özetlerinin hesaplandığı günün yıl bileşeni. Olduğu gibi bırakın. |
    | ay | Uçuş özetlerinin hesaplandığı günün ay bileşeni. Olduğu gibi bırakın. |
    | gün | Uçuş özetlerinin hesaplandığı günün ayın günü bileşeni. Olduğu gibi bırakın. |

1. Yerel iş istasyonunuzdan adlı bir dosya oluşturun `hive-load-flights-partition.hql` . Aşağıdaki kodu dosyanın içeriği olarak kullanın.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
          FL_DATE,
          CARRIER,
          FL_NUM,
          ORIGIN,
          DEST,
          DEP_DELAY,
          ARR_DELAY,
          ACTUAL_ELAPSED_TIME,
          DISTANCE,
        YEAR,
          MONTH,
          DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Oozie değişkenleri söz dizimini kullanır `${variableName}` . Bu değişkenler `job.properties` dosyada ayarlanır. Oozie, çalışma zamanında gerçek değerleri yerine koyar.

1. Yerel iş istasyonunuzdan adlı bir dosya oluşturun `hive-create-daily-summary-table.hql` . Aşağıdaki kodu dosyanın içeriği olarak kullanın.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
          MONTH INT,
          DAY_OF_MONTH INT,
          CARRIER STRING,
          AVG_DEP_DELAY FLOAT,
          AVG_ARR_DELAY FLOAT,
          TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT     year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Bu sorgu yalnızca bir gün için özetlenen verileri depolayacak bir hazırlama tablosu oluşturur, gün bazında taşıyıcının ortalama gecikmelerini ve toplam mesafe akış sayısını hesaplayan SELECT ifadesine göz atın. Bir sonraki adımda Sqoop için kaynak olarak kullanılabilmesi için, bilinen bir konumda (hiveDataFolder değişkeni tarafından belirtilen yol) depolanan bu tabloya eklenen veriler.

1. Yerel iş istasyonunuzdan adlı bir dosya oluşturun `workflow.xml` . Aşağıdaki kodu dosyanın içeriği olarak kullanın. Yukarıdaki bu adımlar, Oozie iş akışı dosyasında ayrı eylemler olarak ifade edilir.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

İki Hive sorgusuna Azure Storage 'daki yolundan erişilir ve kalan değişken değerleri dosya tarafından sağlanır `job.properties` . Bu dosya, iş akışını 3 Ocak 2017 tarihi için çalışacak şekilde yapılandırır.

## <a name="deploy-and-run-the-oozie-workflow"></a>Oozie iş akışını dağıtma ve çalıştırma

Oozie iş akışınızı ( `workflow.xml` ), Hive sorgularını ( `hive-load-flights-partition.hql` ve `hive-create-daily-summary-table.hql` ) ve iş yapılandırmasını () dağıtmak için bash oturumunuzda SCP 'yi kullanın `job.properties` .  Oozie 'de, yalnızca `job.properties` Dosya, headnode 'un yerel deposunda bulunabilir. Diğer tüm dosyalar, bu durumda Azure Storage 'da, bu durumda, Azure depolama 'da olmalıdır. İş akışı tarafından kullanılan Sqoop eylemi, SQL veritabanınız ile iletişim kurmak için bir JDBC sürücüsüne bağlıdır ve bu, baş düğümden,

1. `load_flights_by_day`Baş düğümün yerel depolamadaki Kullanıcı yolunun altında alt klasörü oluşturun. Açık SSH oturumunda aşağıdaki komutu yürütün:

    ```bash
    mkdir load_flights_by_day
    ```

1. Geçerli dizindeki ( `workflow.xml` ve `job.properties` dosyaları) tüm dosyaları `load_flights_by_day` alt klasöre kopyalayın. Yerel iş istasyonunuzdan aşağıdaki komutu yürütün:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. İş akışı dosyalarını, 1. Açık SSH oturumınızdan aşağıdaki komutları yürütün:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. `mssql-jdbc-7.0.0.jre8.jar`Yerel baş düğümden, 1. adımda iş akışı klasörüne kopyalayın. Kümeniz farklı bir jar dosyası içeriyorsa, komutu gereken şekilde gözden geçirin. `workflow.xml`Farklı bir JAR dosyasını yansıtmak için gerektiğinde gözden geçirin. Açık SSH oturumunda aşağıdaki komutu yürütün:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. İş akışını çalıştırın. Açık SSH oturumunda aşağıdaki komutu yürütün:

    ```bash
    oozie job -config job.properties -run
    ```

1. Oozie web konsolunu kullanarak durumu gözlemleyin. Ambarı içinden **Oozie**, **hızlı bağlantılar** ve sonra **Oozie Web Konsolu**' nu seçin. **Iş akışı işleri** sekmesinde **tüm işler**' i seçin.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png" alt-text="HDI Oozie Web konsolu iş akışları":::

1. Durum başarılı olduğunda, ekli satırları görüntülemek için SQL veritabanı tablosunu sorgulayın. Azure portal kullanarak, SQL veritabanınızın bölmesine gidin, **Araçlar**' ı seçin ve **sorgu düzenleyicisini** açın.

    ```sql
    SELECT * FROM dailyflights
    ```

İş akışı tek bir test günü boyunca çalışıyor olduğuna göre, bu iş akışını günlük olarak çalışacak şekilde iş akışını zamanlayan bir düzenleyiciyle sardırabilirsiniz.

## <a name="run-the-workflow-with-a-coordinator"></a>İş akışını bir düzenleyici ile çalıştırma

Bu iş akışını günlük olarak (veya bir tarih aralığındaki tüm günler) çalışacak şekilde zamanlamak için bir düzenleyici kullanabilirsiniz. Bir düzenleyici bir XML dosyası tarafından tanımlanır, örneğin `coordinator.xml` :

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Gördüğünüz gibi, düzenleyicinin çoğunluğu yalnızca yapılandırma bilgilerini iş akışı örneğine geçirmektir. Ancak, kullanıma almak için birkaç önemli öğe vardır.

* 1. nokta: `start` `end` öğe üzerindeki ve öznitelikleri, `coordinator-app` Düzenleyicinin çalıştığı zaman aralığını denetler.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Bir düzenleyici, ve tarih aralığı içindeki eylemlerin, `start` `end` öznitelik tarafından belirtilen aralığa göre planlanmasından sorumludur `frequency` . Her zamanlanmış eylem, iş akışını yapılandırılmış olarak çalıştırır. Yukarıdaki düzenleyici tanımında, Koordinatör 1 Ocak 2017 ' den 5 Ocak 2017 ' e kadar olan eylemleri çalıştıracak şekilde yapılandırılmıştır. Sıklık, [Oozie Ifade dili](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) sıklık ifadesi tarafından bir güne ayarlanır `${coord:days(1)}` . Bu, düzenleyicinin bir eylemi (ve dolayısıyla iş akışını) günde bir kez zamanlamaya neden olur. Geçmişte olan tarih aralıkları için, bu örnekte olduğu gibi, eylem gecikme olmadan çalışacak şekilde zamanlanır. Bir eylemin çalıştırılmak üzere zamanlandığı tarihin başlangıcı *nominal zaman* olarak adlandırılır. Örneğin, 1 Ocak 2017 ' den itibaren verileri işlemek için, düzenleyici işlemi, saat 2017-01-01T00:00:00 ' da olan eylemi zamancak.

* 2. nokta: iş akışının tarih aralığı Içinde, `dataset` öğesi belirli bir tarih aralığı için verilerin ne şekilde görüneceğini belirtir ve Oozie 'nin verilerin henüz işlenmek üzere kullanılabilir olup olmadığını nasıl belirlediğini yapılandırır.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    IBU içindeki verilerin yolu, öğesinde belirtilen ifadeye göre dinamik olarak oluşturulur `uri-template` . Bu koordinatda, veri kümesiyle bir günün sıklığı da kullanılır. (Ve süresi belirlenen zamanları tanımlar) düzenleyici öğe denetimindeki başlangıç ve bitiş tarihleri sırasında, `initial-instance` `frequency` veri kümesindeki ve üzerinde, oluşturulurken kullanılan tarihin hesaplanması kontrol edilir `uri-template` . Bu durumda, ilk örneği düzenleyicinin başlangıcından önce bir gün önce, ilk günün (1/1/2017) değerinde veri aldığından emin olmak için ayarlayın. Veri kümesinin tarih hesaplaması, `initial-instance` (12/31/2016) veri kümesi sıklığının (bir gün), düzenleyici tarafından ayarlanan nominal süreyi (ilk eylem için 2017-01-01T00:00:00 GMT) atlayarak en son tarihi bulana kadar ilerletir.

    Boş `done-flag` öğe, Oozie 'nin bir süre içinde giriş verilerinin varlığını denetlediğini gösteriyorsa, Oozie bir dizin veya dosya olup olmadığını belirler. Bu durumda, bir CSV dosyası var demektir. Bir CSV dosyası varsa, Oozie verilerin hazır olduğunu varsayar ve dosyayı işlemek için bir iş akışı örneği başlatır. Hiçbir CSV dosyası yoksa Oozie verilerin henüz hazır olmadığını ve iş akışının çalışmasının bekleme durumuna gireceğini varsayar.

* Nokta 3: `data-in` öğesi, `uri-template` ilişkili veri kümesi için içindeki değerleri değiştirirken nominal zaman olarak kullanılacak belirli bir zaman damgasını belirtir.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Bu durumda, örneği, `${coord:current(0)}` ilk olarak düzenleyici tarafından zamanlanan eylemin nominal süresini kullanmaya çeviren ifadesi olarak ayarlayın. Diğer bir deyişle, düzenleyici eylemi nominal 01/01/2017 süresi ile çalışacak şekilde zamanlıyor olduğunda, URI şablonundaki YEAR (2017) ve MONTH (01) değişkenlerini değiştirmek için 01/01/2017 kullanılır. Bu örnek için URI şablonu hesaplandıktan sonra Oozie beklenen dizin veya dosyanın kullanılabilir olup olmadığını denetler ve iş akışının bir sonraki çalıştırmasını uygun şekilde zamanlar.

Önceki üç noktası, düzenleyicinin kaynak verileri bir gün içinde işlemeyi zamanladığı bir durum sağlamak için birleşmelidir.

* 1. nokta: düzenleyici nominal Tarih 2017-01-01 ile başlar.

* 2. nokta: Oozie ' de bulunan verileri arar `sourceDataFolder/2017-01-FlightData.csv` .

* Nokta 3: Oozie bu dosyayı bulduğunda, 2017-01-01 için verileri işleyecek iş akışının bir örneğini zamanlar. Oozie, 2017-01-02 için işlemeye devam eder. Bu değerlendirme, 2017-01-05 içermez ancak dahil değildir.

İş akışlarında olduğu gibi, bir düzenleyicinin yapılandırması, `job.properties` iş akışı tarafından kullanılan ayarların üst kümesini içeren bir dosyada tanımlanır.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

Bu dosyada sunulan tek yeni özellikler `job.properties` şunlardır:

| Özellik | Değer kaynağı |
| --- | --- |
| Oozie. cozı. Application. Path | `coordinator.xml`Çalıştırmak Için Oozie düzenleyicisini içeren dosyanın konumunu gösterir. |
| hiveDailyTableNamePrefix | Hazırlama tablosunun tablo adını dinamik olarak oluştururken kullanılan önek. |
| hiveDataFolderPrefix | Tüm hazırlama tablolarının depolanacağı yolun öneki. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Oozie düzenleyicisini dağıtma ve çalıştırma

İşlem hattını bir düzenleyiciyle çalıştırmak için, iş akışınızı içeren klasörün üzerinde bir düzey bir klasörden çalışmanız dışında, iş akışı için benzer bir şekilde ilerleyin. Bu klasör kuralı, düzenleyicilerinin disk üzerindeki iş akışlarıyla ayırır, böylece bir düzenleyiciyi farklı alt iş akışlarıyla ilişkilendirebilirsiniz.

1. Düzenleyici dosyalarını, kümenizin baş düğümünün yerel depolama alanına kopyalamak için yerel makinenizden SCP 'YI kullanın.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. Baş düğümünüz için SSH.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Düzenleyici dosyalarını, 1.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Düzenleyiciyi çalıştırın.

    ```bash
    oozie job -config job.properties -run
    ```

5. Oozie web konsolunu kullanarak durumu doğrulayın, bu kez **Düzenleyici işleri** sekmesini ve ardından  **tüm işleri** seçin.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png" alt-text="Oozie Web Konsolu Düzenleyicisi Işleri":::

6. Zamanlanmış eylemlerin listesini göstermek için bir düzenleyici örneği seçin. Bu durumda, 1/1/2017 ile 1/4/2017 arasında bir saat olarak kabul edilen dört eylem görmeniz gerekir.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png" alt-text="Oozie Web Konsolu Düzenleyicisi Işi":::

    Bu listedeki her bir eylem, bir güne ait verileri işleyen bir iş akışı örneğine karşılık gelir ve bu günün başlangıcı nominal süre ile belirtilir.

## <a name="next-steps"></a>Sonraki adımlar

[Apache Oozie belgeleri](https://oozie.apache.org/docs/4.2.0/index.html)
