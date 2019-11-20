---
title: Veri Analizi ardışık düzeni oluşturma-Azure
description: Yeni veriler tarafından tetiklenen ve kısa sonuçlar üreten bir örnek veri işlem hattı ayarlayın ve çalıştırın.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 122840614aede3ee112f8fd68cf6dabfa91fa225
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105522"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Veri analizi işlem hattını kullanıma hazır hale getirme

*Veri işlem hatları* çok sayıda veri analizi çözümünü daha az. Adından da anlaşılacağı gibi, veri işlem hattı ham verileri alır, gerektiği gibi temizler ve yeniden şekillendirir ve genellikle işlenen verileri depolamadan önce hesaplamalar veya toplamalar gerçekleştirir. İşlenen veriler istemciler, raporlar veya API 'Ler tarafından kullanılır. Bir veri işlem hattı, bir zamanlamaya göre veya yeni veriler tarafından tetiklendiğinde tekrarlanabilir sonuçlar sağlamalıdır.

Bu makalede, HDInsight Hadoop kümelerinde çalışan Oozie kullanılarak yinelenebilirlik için veri işlem hatlarınızın nasıl yapılacağı açıklanır. Örnek senaryo, hava yolu uçuş süresi serisi verilerini hazırlarken ve işleyen bir veri işlem hattı boyunca size yol gösterir.

Aşağıdaki senaryoda, giriş verileri bir ay için bir dizi uçuş verisi içeren düz bir dosyadır. Bu uçuş verileri, kaynak ve hedef Havaalanı, mil flown, ayrılma ve varış süresi gibi bilgileri içerir. Bu işlem hattına sahip amaç, her bir hava yolu 'ın dakikada ortalama ayrılma ve gelme gecikmeleri ve bu güne ait toplam mil flown ile her gün bir satıra sahip olduğu günlük hava yolu performansını özetler.

| YEAR | AY | DAY_OF_MONTH | TAŞIY |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1\. | 3 | AA | 10,142229 | 7,862926 | 2644539 |
| 2017 | 1\. | 3 | AS | 9,435449 | 5,482143 | 572289 |
| 2017 | 1\. | 3 | DL | 6,935409 | -2,1893024 | 1909696 |

Örnek işlem hattı, yeni bir zaman döneminin uçuş verilerinin gelmesini bekler, daha sonra bu ayrıntılı uçuş bilgilerini, uzun süreli analizler için Apache Hive veri ambarınıza depolar. İşlem hattı, yalnızca günlük uçuş verilerini özetleyen çok daha küçük bir veri kümesi de oluşturur. Bu günlük uçuş Özeti verileri, bir Web sitesi gibi raporlar sağlamak üzere bir SQL veritabanına gönderilir.

Aşağıdaki diyagramda örnek işlem hattı gösterilmektedir.

![HDI uçuş örneği veri ardışık düzenine genel bakış](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Apache Oozie çözümüne genel bakış

Bu işlem hattı, HDInsight Hadoop kümesinde çalışan Apache Oozie kullanır.

Oozie, *işlem hatlarını işlemler*, *iş akışları*ve *Koordinatörler*açısından açıklar. Eylemler, bir Hive sorgusu çalıştırma gibi gerçekleştirilecek fiili işi tespit. İş akışları eylemlerin sırasını tanımlar. Düzenleyiciler, iş akışının çalıştırılacağı zamanlamayı tanımlar. Düzenleyiciler, iş akışının bir örneğini başlatmadan önce yeni verilerin kullanılabilirliğini de bekleyebilir.

Aşağıdaki diyagramda Bu örnek Oozie işlem hattının üst düzey tasarımı gösterilmektedir.

![Oozie uçuş örneği veri işlem hattı](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Azure kaynaklarını sağlama

Bu işlem hattı aynı konumda bir Azure SQL veritabanı ve bir HDInsight Hadoop kümesi gerektirir. Azure SQL veritabanı, ardışık düzen ve Oozie meta veri deposu tarafından oluşturulan özet verileri depolar.

#### <a name="provision-azure-sql-database"></a>Azure SQL veritabanı sağlama

1. Azure Portal kullanarak, bu örnek tarafından kullanılan tüm kaynakları içeren `oozie` adlı yeni bir kaynak grubu oluşturun.
2. `oozie` Kaynak grubu içinde bir Azure SQL Server ve veritabanı sağlayın. S1 standart fiyatlandırma katmanından daha büyük bir veritabanı gerekmez.
3. Azure portal kullanarak, yeni dağıtılan SQL veritabanınızın bölmesine gidin ve **Araçlar**' ı seçin.

    ![HDInsight SQL DB araçları düğme simgesi](./media/hdinsight-operationalize-data-pipeline/hdi-sql-db-tools-button.png)

4. **Sorgu Düzenleyicisi**' ni seçin.

    ![Araçlar SQL DB sorgu Düzenleyicisi önizlemesi](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor1.png)

5. **Sorgu Düzenleyicisi** bölmesinde, **oturum aç**' ı seçin.

    ![Sorgu Düzenleyicisi SQL DB oturum açma penceresi](./media/hdinsight-operationalize-data-pipeline/sql-db-login-window1.png)

6. SQL veritabanı kimlik bilgilerinizi girip **Tamam**' ı seçin.

   ![Sorgu Düzenleyicisi SQL DB oturum açma parametreleri](./media/hdinsight-operationalize-data-pipeline/sql-db-login-window2.png)

7. Sorgu Düzenleyicisi metin alanına, işlem hattının her çalıştırmasıyla özetlenen verileri depolayacak `dailyflights` tabloyu oluşturmak için aşağıdaki SQL deyimlerini girin.

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

8. SQL deyimlerini yürütmek için **Çalıştır** ' ı seçin.

    ![HDInsight SQL DB yürütme düğmesi](./media/hdinsight-operationalize-data-pipeline/hdi-sql-db-run-button.png)

Azure SQL veritabanınız artık hazır.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>HDInsight Hadoop kümesi sağlama

1. Azure portal **+ Yeni** ' yi seçin ve HDInsight için arama yapın.
2. **Oluştur**’u seçin.
3. Temel bilgiler bölmesinde, kümeniz için benzersiz bir ad sağlayın ve Azure aboneliğinizi seçin.

    ![HDInsight kümesi adı ve aboneliği](./media/hdinsight-operationalize-data-pipeline/cluster-name-subscription.png)

4. **Küme türü** bölmesinde, **Hadoop** kümesi türünü, **Linux** işletim sistemini ve HDInsight kümesinin en son sürümünü seçin. **Küme katmanını** **Standart**olarak bırakın.

    ![Azure portal kümesi yapılandırma türü](./media/hdinsight-operationalize-data-pipeline/hdinsight-cluster-type.png)

5. Küme türü Seçiminizi uygulamak için **Seç ' i** seçin.
6. Bir oturum açma parolası sağlayıp listeden `oozie` kaynak grubunuzu seçip **İleri**' yi seçerek **temel bilgiler** bölmesini doldurun.

    ![Azure portal küme temelleri bölmesi oluştur](./media/hdinsight-operationalize-data-pipeline/hdinsight-basics-pane.png)

7. **Depolama** bölmesinde, birincil depolama türünü **Azure Storage**olarak ayarlayın, **Yeni oluştur**' u seçin ve yeni hesap için bir ad sağlayın.

    ![HDInsight depolama hesabı ayarları](./media/hdinsight-operationalize-data-pipeline/storage-account-settings.png)

8. Meta veri **deposu ayarları**Için, **HIVE için bir SQL veritabanı seçin**altında, daha önce oluşturduğunuz veritabanını seçin.

    ![HDInsight Hive meta veri deposu ayarları](./media/hdinsight-operationalize-data-pipeline/hive-metastore-settings.png)

9. **SQL veritabanı kimlik doğrulaması**' nı seçin.

    ![HDInsight Hive meta kimlik doğrulama](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. SQL veritabanınızın Kullanıcı adı ve parolanızı girip **Seç**' i seçin.

       ![HDInsight Hive meta kimlik doğrulama oturum açma](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Meta veri deposu **ayarları** bölmesine dönün, Oozie meta veri deposu için veritabanınızı seçin ve daha önce yaptığınız gibi kimlik doğrulaması yapın.

       ![Azure portal meta veri deposu ayarları](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. **İleri**’yi seçin.
13. Kümenizi dağıtmak için **Özet** bölmesinde **Oluştur** ' u seçin.

### <a name="verify-ssh-tunneling-setup"></a>SSH tüneli kurulumunu doğrulama

Koordinatör ve iş akışı örneklerinizin durumunu görüntülemek için Oozie web konsolunu kullanmak istiyorsanız, HDInsight kümenize bir SSH tüneli ayarlayın. Daha fazla bilgi için bkz. [SSH tüneli](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Ayrıca, SSH tüneli genelinde kümenizin Web kaynaklarına gözatabilmeniz için [Foxy ara sunucu](https://getfoxyproxy.org/) Uzantısı ile Chrome 'ı kullanabilirsiniz. Tünelin bağlantı noktası 9876 üzerindeki ana bilgisayar `localhost` aracılığıyla tüm istekleri proxy olarak yapılandırın. Bu yaklaşım, Windows 10 üzerinde Bash olarak da bilinen Linux için Windows alt sistemi ile uyumludur.

1. Kümenize bir SSH tüneli açmak için aşağıdaki komutu çalıştırın:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Göz atarak baş düğüminizdeki ambarı 'na giderek tünelin çalıştığını doğrulayın:

    http:\//headnodehost: 8080

3. ' Dan **Oozie web konsoluna** erişmek Için **Oozie**, **hızlı bağlantılar**' ı seçin ve ardından **Oozie Web Konsolu**' nu seçin.

### <a name="configure-hive"></a>Hive 'yi yapılandırma

1. Bir ay için uçuş verileri içeren örnek bir CSV dosyası indirin. [HDInsight GitHub deposundan](https://github.com/hdinsight/hdinsight-dev-guide) `2017-01-FlightData.csv`ZIP `2017-01-FlightData.zip` dosyasını indirin ve CSV dosyasına ayıklayın. 

2. Bu CSV dosyasını HDInsight kümenize bağlı Azure depolama hesabına kopyalayın ve `/example/data/flights` klasöre yerleştirin.

`bash` Kabuk oturumunuzda SCP 'yi kullanarak dosyayı kopyalayabilirsiniz.

1. Dosyaları yerel makinenizden HDInsight kümesi baş düğümünüz yerel depolama alanına kopyalamak için SCP 'YI kullanın.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Dosyayı baş düğüm yerel depoınızdan Azure depolama 'ya kopyalamak için,

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Örnek veriler artık kullanılabilir. Ancak, işlem hattı, bir diğeri (`rawFlights`) ve özetlenen veriler (`flights`) için olmak üzere iki Hive tablosu gerektirir. Bu tabloları aşağıdaki şekilde ambarı 'nda oluşturun.

1. Http:\//headnodehost: 8080 adresine giderek, ambarı 'nda oturum açın.

2. Hizmetler listesinden **Hive**öğesini seçin.

    ![Apache ambarı hizmetleri listesi Hive seçme](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Hive görünümü 2,0 etiketinin yanındaki **görünümü görüntülemek Için git** ' i seçin.

    ![Ambarı Apache Apache Hive özet listesi](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Sorgu metin alanında, `rawFlights` tabloyu oluşturmak için aşağıdaki deyimleri yapıştırın. Tablo, Azure depolama 'daki `/example/data/flights` klasörü içinde CSV dosyaları için bir şema okuması sağlar. `rawFlights`

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

    ![HDI ambarı Hizmetleri Hive sorgusu](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. `flights` Tabloyu oluşturmak için, sorgu metin alanındaki metni aşağıdaki deyimlerle değiştirin. `flights` Tablo, verileri yıla, aya ve aya göre yüklenen verileri bölümleyerek Hive tarafından yönetilen bir tablodur. Bu tablo, uçuşa göre bir satırın kaynak verilerinde en düşük ayrıntı düzeyi olan tüm geçmiş uçuş verilerini içerir.

    ```
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

### <a name="create-the-oozie-workflow"></a>Oozie iş akışını oluşturma

İşlem hatları, genellikle belirli bir zaman aralığındaki verileri toplu halde işler. Bu durumda, işlem hattı uçuş verilerini günlük olarak işler. Bu yaklaşım, giriş CSV dosyalarının günlük, haftalık, aylık veya yıllık olarak ulaşmasını sağlar.

Örnek iş akışı, üç önemli adımda uçuş verilerini gün içinde işler:

1. Bu güne ait tarih aralığına ait verileri `rawFlights` tablo tarafından temsil edilen kaynak CSV dosyasından ayıklamak ve verileri `flights` tabloya eklemek için bir Hive sorgusu çalıştırın.
2. Bir Hive sorgusu çalıştırarak, günlük ve taşıyıcı tarafından özetlenen uçuş verilerinin bir kopyasını içeren, Hive içindeki bir hazırlama tablosunu dinamik olarak oluşturun.
3. Hive içindeki günlük hazırlama tablosundaki tüm verileri Azure SQL veritabanı 'ndaki hedef `dailyflights` tabloya kopyalamak için Apache Sqoop 'yi kullanın. Sqoop, Azure depolama 'da bulunan Hive tablosunun arkasındaki verilerden kaynak satırları okur ve bir JDBC bağlantısı kullanarak bunları SQL veritabanı 'na yükler.

Bu üç adım bir Oozie iş akışı tarafından koordine edilir. 

1. Dosyada `hive-load-flights-partition.hql`bir sorgu oluşturun.

    ```
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

    Oozie değişkenleri söz dizimini `${variableName}`kullanır. Bu değişkenler, `job.properties` sonraki adımda açıklandığı gibi dosyada ayarlanır. Oozie, çalışma zamanında gerçek değerleri yerine koyar.

2. Dosyada `hive-create-daily-summary-table.hql`bir sorgu oluşturun.

    ```
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
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Bu sorgu yalnızca bir gün için özetlenen verileri depolayacak bir hazırlama tablosu oluşturur, gün bazında taşıyıcının ortalama gecikmelerini ve toplam mesafe akış sayısını hesaplayan SELECT ifadesine göz atın. Bir sonraki adımda Sqoop için kaynak olarak kullanılabilmesi için, bilinen bir konumda (hiveDataFolder değişkeni tarafından belirtilen yol) depolanan bu tabloya eklenen veriler.

3. Aşağıdaki Sqoop komutunu çalıştırın.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Bu üç adım, adlı `workflow.xml`aşağıdaki Oozie iş akışı dosyasında üç ayrı eylem olarak ifade edilir.

```
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
            <archive>sqljdbc41.jar</archive>
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

İki Hive sorgusuna Azure Storage 'daki yolundan erişilir ve kalan değişken değerleri aşağıdaki `job.properties` dosya tarafından sağlanır. Bu dosya, iş akışını 3 Ocak 2017 tarihi için çalışacak şekilde yapılandırır.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

Aşağıdaki tablo, özelliklerinin her birini özetler ve kendi ortamınız için değerleri nerede bulabileceğinizi gösterir.

| Özellik | Değer kaynağı |
| --- | --- |
| NameNode | HDInsight kümenize eklenen Azure depolama kapsayıcısının tam yolu. |
| Jobtracker 'a | Etkin kümenizin YARN baş düğümüne ait iç ana bilgisayar adı. Ambarı giriş sayfasında, hizmetler listesinden YARN ' yi seçin ve ardından etkin Kaynak Yöneticisi ' ni seçin. Ana bilgisayar adı URI 'SI sayfanın en üstünde görüntülenir. 8050 numaralı bağlantı noktasını ekleyin. |
| Adı | Hive eylemleri planlanırken kullanılan YARN kuyruğunun adı. Varsayılan olarak bırakın. |
| oozie.use.system.libpath | Doğru olarak bırakın. |
| Uygulama tabanı dizininin | Oozie iş akışını ve destekleyici dosyaları dağıttığınız Azure depolama 'daki alt klasörün yolu. |
| oozie.wf.application.path | Çalıştırılacak Oozie iş akışının `workflow.xml` konumu. |
| hiveScriptLoadPartition | Azure depolama 'daki yol, Hive sorgu dosyasına `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | Azure depolama 'daki yol, Hive sorgu dosyasına `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | Hazırlama tablosu için kullanılacak dinamik olarak oluşturulan ad. |
| hiveDataFolder | Azure depolama 'daki, hazırlama tablosunun içerdiği verilere yönelik yol. |
| sqlDatabaseConnectionString | Azure SQL veritabanınıza JDBC sözdizimi bağlantı dizesi. |
| sqlDatabaseTableName | Azure SQL veritabanı 'ndaki özet satırlarının eklendiği tablonun adı. Farklı `dailyflights`bırak. |
| yıl | Uçuş özetlerinin hesaplandığı günün yıl bileşeni. Olduğu gibi bırakın. |
| ay | Uçuş özetlerinin hesaplandığı günün ay bileşeni. Olduğu gibi bırakın. |
| gün | Uçuş özetlerinin hesaplandığı günün ayın günü bileşeni. Olduğu gibi bırakın. |

> [!NOTE]  
> Oozie iş akışınızı dağıtmadan ve çalıştırmadan `job.properties` önce dosya kopyanızı ortamınıza özgü değerlerle güncelleştirdiğinizden emin olun.

### <a name="deploy-and-run-the-oozie-workflow"></a>Oozie iş akışını dağıtma ve çalıştırma

Oozie`workflow.xml`iş akışınızı (), Hive sorgularını (`hive-load-flights-partition.hql` ve `hive-create-daily-summary-table.hql`) ve iş yapılandırmasını`job.properties`() dağıtmak için bash oturumunuzda SCP 'yi kullanın.  Oozie 'de, yalnızca `job.properties` dosya, headnode 'un yerel deposunda bulunabilir. Diğer tüm dosyalar, bu durumda Azure Storage 'da, bu durumda, Azure depolama 'da olmalıdır. İş akışı tarafından kullanılan Sqoop eylemi, SQL veritabanınız ile iletişim kurmak için bir JDBC sürücüsüne bağlıdır ve bu, baş düğümden,

1. Baş düğümün yerel depolamadaki Kullanıcı yolunun altında altklasörüoluşturun.`load_flights_by_day`

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Geçerli dizindeki ( `workflow.xml` ve `job.properties` dosyaları) tüm dosyaları altklasörekopyalayın.`load_flights_by_day`

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. Baş düğümünüz için `load_flights_by_day` SSH ekleyin ve klasöre gidin.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. İş akışı dosyalarını, 1.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Yerel `sqljdbc41.jar` baş düğümden, 1. adımda iş akışı klasörüne kopyala:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. İş akışını çalıştırın.

        oozie job -config job.properties -run

7. Oozie web konsolunu kullanarak durumu gözlemleyin. Ambarı içinden **Oozie**, **hızlı bağlantılar**ve sonra **Oozie Web Konsolu**' nu seçin. **Iş akışı işleri** sekmesinde **tüm işler**' i seçin.

    ![HDI Oozie Web konsolu iş akışları](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Durum başarılı olduğunda, ekli satırları görüntülemek için SQL veritabanı tablosunu sorgulayın. Azure portal kullanarak, SQL veritabanınızın bölmesine gidin, **Araçlar**' ı seçin ve **sorgu düzenleyicisini**açın.

        SELECT * FROM dailyflights

İş akışı tek bir test günü boyunca çalışıyor olduğuna göre, bu iş akışını günlük olarak çalışacak şekilde iş akışını zamanlayan bir düzenleyiciyle sardırabilirsiniz.

### <a name="run-the-workflow-with-a-coordinator"></a>İş akışını bir düzenleyici ile çalıştırma

Bu iş akışını günlük olarak (veya bir tarih aralığındaki tüm günler) çalışacak şekilde zamanlamak için bir düzenleyici kullanabilirsiniz. Bir düzenleyici bir XML dosyası tarafından tanımlanır, örneğin `coordinator.xml`:

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

* Nokta 1: Öğesiüzerindeki`coordinator-app` ve `end`öznitelikleri , düzenleyicinin çalıştığı zaman aralığını denetler. `start`

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Bir düzenleyici, `start` ve `end` tarih aralığı içindeki eylemlerin, `frequency` öznitelik tarafından belirtilen aralığa göre planlanmasından sorumludur. Her zamanlanmış eylem, iş akışını yapılandırılmış olarak çalıştırır. Yukarıdaki düzenleyici tanımında, Koordinatör, 1 Ocak 2017 ' den 5 Ocak 2017 ' e kadar olan eylemleri çalıştıracak şekilde yapılandırılmıştır. Sıklık, [Oozie Ifade dili](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) sıklık ifadesi `${coord:days(1)}`tarafından 1 gün olarak ayarlanır. Bu, düzenleyicinin bir eylemi (ve dolayısıyla iş akışını) günde bir kez zamanlamaya neden olur. Geçmişte olan tarih aralıkları için, bu örnekte olduğu gibi, eylem gecikme olmadan çalışacak şekilde zamanlanır. Bir eylemin çalıştırılmak üzere zamanlandığı tarihin başlangıcı *nominal zaman*olarak adlandırılır. Örneğin, 1 Ocak 2017 ' den itibaren verileri işlemek için, düzenleyici işlemi, saat 2017-01-01T00:00:00 ' da olan eylemi zamanlıyacaktır.

* 2\. nokta: İş akışının tarih aralığı içinde, `dataset` öğesi belirli bir tarih aralığı için verilerin ne şekilde görüneceğini belirtir ve Oozie 'nin verilerin henüz işlenmek üzere kullanılabilir olup olmadığını nasıl belirlediğini yapılandırır.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    IBU içindeki verilerin yolu, `uri-template` öğesinde belirtilen ifadeye göre dinamik olarak oluşturulur. Bu koordinatda, veri kümesiyle bir günün sıklığı da kullanılır. İşlem, düzenleyici öğe denetimindeki başlangıç ve bitiş tarihleri (ve bunların kabul edilen zamanlarını tanımlar) `initial-instance` sırasında, veri kümesindeki ve `frequency` üzerinde kullanılan tarihin hesaplamasını denetlemek için `uri-template`. Bu durumda, ilk örneği düzenleyicinin başlangıcından önce bir gün önce, ilk günün (1/1/2017) değerinde veri aldığından emin olmak için ayarlayın. Veri kümesinin tarih hesaplaması `initial-instance` , düzenleyicinin (2017-01-01T00:00:00 GMT) tarafından ayarlanan en son tarihi bulana kadar, (12/31/2016) veri kümesi sıklığı artışlarına (1 gün) ilerletir. ilk eylem için).

    Boş `done-flag` öğe, Oozie 'nin bir süre içinde giriş verilerinin varlığını denetlediğini gösteriyorsa, Oozie bir dizin veya dosya olup olmadığını belirler. Bu durumda, bir CSV dosyası var demektir. Bir CSV dosyası varsa, Oozie verilerin hazır olduğunu varsayar ve dosyayı işlemek için bir iş akışı örneği başlatır. Hiçbir CSV dosyası yoksa Oozie verilerin henüz hazır olmadığını ve iş akışının çalışmasının bekleme durumuna gireceğini varsayar.

* Nokta 3: Öğesi, ilişkili veri kümesi için içindeki `uri-template` değerleri değiştirirken nominal zaman olarak kullanılacak belirli bir zaman damgasını belirtir. `data-in`

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Bu durumda, örneği, ilk olarak düzenleyici tarafından zamanlanan `${coord:current(0)}`eylemin nominal süresini kullanmaya çeviren ifadesi olarak ayarlayın. Diğer bir deyişle, düzenleyici eylemi nominal 01/01/2017 süresi ile çalışacak şekilde zamanlıyor olduğunda, URI şablonundaki YEAR (2017) ve MONTH (01) değişkenlerini değiştirmek için 01/01/2017 kullanılır. Bu örnek için URI şablonu hesaplandıktan sonra Oozie beklenen dizin veya dosyanın kullanılabilir olup olmadığını denetler ve iş akışının bir sonraki çalıştırmasını uygun şekilde zamanlar.

Önceki üç noktası, düzenleyicinin kaynak verileri bir gün içinde işlemeyi zamanladığı bir durum sağlamak için birleşmelidir. 

* Nokta 1: Koordinatör nominal Tarih 2017-01-01 ile başlar.

* 2\. nokta: Oozie ' de `sourceDataFolder/2017-01-FlightData.csv`bulunan verileri arar.

* Nokta 3: Oozie bu dosyayı bulduğunda, 2017-01-01 için verileri işleyecek iş akışının bir örneğini zamanlar. Oozie, 2017-01-02 için işlemeye devam eder. Bu değerlendirme, 2017-01-05 içermez ancak dahil değildir.

İş akışlarında olduğu gibi, bir düzenleyicinin yapılandırması, iş akışı tarafından kullanılan `job.properties` ayarların üst kümesini içeren bir dosyada tanımlanır.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

Bu `job.properties` dosyada sunulan tek yeni özellikler şunlardır:

| Özellik | Değer kaynağı |
| --- | --- |
| oozie.coord.application.path | Çalıştırmak için Oozie `coordinator.xml` düzenleyicisini içeren dosyanın konumunu gösterir. |
| hiveDailyTableNamePrefix | Hazırlama tablosunun tablo adını dinamik olarak oluştururken kullanılan önek. |
| hiveDataFolderPrefix | Tüm hazırlama tablolarının depolanacağı yolun öneki. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Oozie düzenleyicisini dağıtma ve çalıştırma

İşlem hattını bir düzenleyiciyle çalıştırmak için, iş akışınızı içeren klasörün üzerinde bir düzey bir klasörden çalışmanız dışında, iş akışı için benzer bir şekilde ilerleyin. Bu klasör kuralı, düzenleyicilerinin disk üzerindeki iş akışlarıyla ayırır, böylece bir düzenleyiciyi farklı alt iş akışlarıyla ilişkilendirebilirsiniz.

1. Düzenleyici dosyalarını, kümenizin baş düğümünün yerel depolama alanına kopyalamak için yerel makinenizden SCP 'YI kullanın.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. Baş düğümünüz için SSH.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Düzenleyici dosyalarını, 1.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Düzenleyiciyi çalıştırın.

    ```bash
    oozie job -config job.properties -run
    ```

5. Oozie web konsolunu kullanarak durumu doğrulayın, bu kez **Düzenleyici işleri** sekmesini ve ardından **tüm işleri**seçin.

    ![Oozie Web Konsolu Düzenleyicisi Işleri](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Zamanlanmış eylemlerin listesini göstermek için bir düzenleyici örneği seçin. Bu durumda, 1/1/2017 ile 1/4/2017 arasında bir saat olarak kabul edilen dört eylem görmeniz gerekir.

    ![Oozie Web Konsolu Düzenleyicisi Işi](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Bu listedeki her bir eylem, bir güne ait verileri işleyen bir iş akışı örneğine karşılık gelir ve bu günün başlangıcı nominal süre ile belirtilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Oozie belgeleri](https://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
