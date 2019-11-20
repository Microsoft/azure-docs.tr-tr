---
title: Linux tabanlı Azure HDInsight 'ta Hadoop Oozie iş akışlarını kullanma
description: Linux tabanlı HDInsight 'ta Hadoop Oozie kullanın. Oozie iş akışını tanımlama ve Oozie işi gönderme hakkında bilgi edinin.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 89364a3ee948abbe5d233052878abe92bc7663a7
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241680"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Linux tabanlı Azure HDInsight 'ta iş akışını tanımlamak ve çalıştırmak için Apache Hadoop ile Apache Oozie kullanma

Azure HDInsight üzerinde Apache Hadoop Apache Oozie 'yi nasıl kullanacağınızı öğrenin. Oozie, Hadoop işlerini yöneten bir iş akışı ve düzenleme sistemidir. Oozie, Hadoop yığınında tümleşiktir ve aşağıdaki işleri destekler:

* MapReduce Apache Hadoop
* Apache Pig
* Apache Hive
* Apache Sqoop

Ayrıca, Java programları veya kabuk betikleri gibi bir sisteme özgü işleri zamanlamak için Oozie de kullanabilirsiniz.

> [!NOTE]  
> HDInsight ile iş akışlarını tanımlamaya yönelik başka bir seçenek Azure Data Factory kullanmaktır. Data Factory hakkında daha fazla bilgi edinmek için bkz. [Data Factory Ile Apache Pig ve Apache Hive kullanma][azure-data-factory-pig-hive]. Kurumsal Güvenlik Paketi kümeleriyle Oozie 'yi kullanmak için lütfen bkz. [HDInsight Hadoop kümelerinde Apache Oozie 'yi kurumsal güvenlik paketi Ile çalıştırma](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Önkoşullar

* **HDInsight üzerinde bir Hadoop kümesi**. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bir SSH istemcisi**. Bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Bir Azure SQL veritabanı**.  Bkz. [Azure Portal Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started.md).  Bu makale, **oozıetest**adlı bir veritabanını kullanır.

* Kümelerinizin birincil depolama alanı için [URI şeması](./hdinsight-hadoop-linux-information.md#URI-and-scheme) . Bu, Azure depolama için `wasb://`, Azure Data Lake Storage 1. için Azure Data Lake Storage 2. veya `adl://` `abfs://`. Azure depolama için güvenli aktarım etkinleştirilirse, URI `wasbs://`olur. Ayrıca bkz. [Güvenli aktarım](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Örnek iş akışı

Bu belgede kullanılan iş akışı iki eylem içerir. Eylemler, Hive, Sqoop, MapReduce veya diğer işlemleri çalıştırma gibi görevler için tanımlardır:

![HDInsight Oozie iş akışı diyagramı](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Hive eylemi, HDInsight 'ta bulunan `hivesampletable` kayıtları ayıklamak için HiveQL betiğini çalıştırır. Her veri satırı, belirli bir mobil cihazdan ziyaret işlemini açıklar. Kayıt biçimi aşağıdaki metin gibi görünür:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Bu belgede kullanılan Hive betiği, Android veya iPhone gibi her platformun toplam ziyaretlerini sayar ve sayıları yeni bir Hive tablosuna depolar.

    Hive hakkında daha fazla bilgi için bkz. [HDInsight ile Apache Hive kullanma][hdinsight-use-hive].

2. Bir Sqoop eylemi, yeni Hive tablosunun içeriğini Azure SQL veritabanı 'nda oluşturulan bir tabloya dışarı aktarır. Sqoop hakkında daha fazla bilgi için bkz. [HDInsight Ile Apache Sqoop kullanma][hdinsight-use-sqoop].

> [!NOTE]  
> HDInsight kümelerinde desteklenen Oozie sürümleri için bkz. [HDInsight tarafından sunulan Hadoop kümesi sürümlerindeki][hdinsight-versions]yenilikler.

## <a name="create-the-working-directory"></a>Çalışma dizinini oluşturma

Oozie aynı dizindeki bir iş için gereken tüm kaynakları depolamanızı bekler. Bu örnek `wasbs:///tutorials/useoozie`kullanır. Bu dizini oluşturmak için aşağıdaki adımları izleyin:

1. `sshuser`, küme için SSH Kullanıcı adı ile değiştirmek için aşağıdaki kodu düzenleyin ve `CLUSTERNAME` değerini kümenin adıyla değiştirin.  Ardından, [SSH kullanarak](hdinsight-hadoop-linux-use-ssh-unix.md)HDInsight kümesine bağlanmak için kodu girin.  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Dizini oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > `-p` parametresi, yoldaki tüm dizinlerin oluşturulmasına neden olur. `data` Dizin, `useooziewf.hql` betiği tarafından kullanılan verileri tutmak için kullanılır.

3. `sshuser`, SSH kullanıcı adınızla değiştirmek için aşağıdaki kodu düzenleyin.  Oozie 'nin Kullanıcı hesabınızın kimliğine bürünebileceği emin olmak için aşağıdaki komutu kullanın:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Kullanıcının zaten `users` grubunun bir üyesi olduğunu belirten hataları yoksayabilirsiniz.

## <a name="add-a-database-driver"></a>Veritabanı sürücüsü ekleme

Bu iş akışı SQL veritabanına veri aktarmak için Sqoop kullandığından, SQL veritabanıyla etkileşim kurmak için kullanılan JDBC sürücüsünün bir kopyasını sağlamanız gerekir. JDBC sürücüyü çalışma dizinine kopyalamak için SSH oturumunda aşağıdaki komutu kullanın:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> `/usr/share/java/`bulunan gerçek JDBC sürücüsünü doğrulayın.

İş akışınız, MapReduce uygulaması içeren bir jar gibi diğer kaynakları kullandıysanız, bu kaynakları da eklemeniz gerekir.

## <a name="define-the-hive-query"></a>Hive sorgusunu tanımlama

Bir sorguyu tanımlayan bir Hive sorgu dili (HiveQL) betiği oluşturmak için aşağıdaki adımları kullanın. Sorguyu bu belgenin ilerleyen kısımlarında bir Oozie iş akışında kullanacaksınız.

1. SSH bağlantısından, aşağıdaki komutu kullanarak `useooziewf.hql`adlı bir dosya oluşturun:

    ```bash
    nano useooziewf.hql
    ```

1. GNU nano Düzenleyicisi açıldıktan sonra dosyanın içeriği olarak aşağıdaki sorguyu kullanın:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Betikte kullanılan iki değişken vardır:

   * `${hiveTableName}`: oluşturulacak tablonun adını Içerir.

   * `${hiveDataFolder}`: tablo için veri dosyalarının depolandığı konumu Içerir.

     Bu makaledeki Workflow. XML iş akışı Tanım dosyası, bu değerleri çalışma zamanında bu HiveQL betiğine geçirir.

1. Dosyayı kaydetmek için **CTRL + X**' i seçin, **Y**girin ve ardından **ENTER**' u seçin.  

1. `useooziewf.hql` `wasbs:///tutorials/useoozie/useooziewf.hql`kopyalamak için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Bu komut, `useooziewf.hql` dosyasını küme için, uyumlu depolamada depolar.

## <a name="define-the-workflow"></a>İş akışını tanımlama

Oozie iş akışı tanımları, XML işlem tanımı dili olan Hadoop Işlem tanımı dilinde (hPDL) yazılır. İş akışını tanımlamak için aşağıdaki adımları kullanın:

1. Yeni bir dosya oluşturmak ve düzenlemek için aşağıdaki ifadeyi kullanın:

    ```bash
    nano workflow.xml
    ```

2. Nano düzenleyici açıldıktan sonra, dosya içeriği olarak aşağıdaki XML 'i girin:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
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
            <arg>${hiveDataFolder}</arg>
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

    İş akışında tanımlanmış iki eylem vardır:

   * `RunHiveScript`: Bu eylem başlangıç eylemi ve `useooziewf.hql` Hive betiğini çalıştırır.

   * `RunSqoopExport`: Bu eylem, Hive betiğinizden oluşturulan verileri Sqoop kullanarak bir SQL veritabanına aktarır. Bu eylem yalnızca `RunHiveScript` eylemi başarılı olursa çalışır.

     İş akışında `${jobTracker}`gibi birkaç giriş vardır. Bu girişleri, iş tanımında kullandığınız değerlerle değiştirirsiniz. Bu belgede daha sonra iş tanımını oluşturacaksınız.

     Ayrıca, Sqoop bölümündeki `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` girişini de aklınızda bulabilirsiniz. Bu giriş, bu eylem çalıştırıldığında Oozie 'yi bu arşivi Sqoop için kullanılabilir hale getirir.

3. Dosyayı kaydetmek için **CTRL + X**' i seçin, **Y**girin ve ardından **ENTER**' u seçin.  

4. `workflow.xml` dosyasını `/tutorials/useoozie/workflow.xml`kopyalamak için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Bir tablo oluşturma

> [!NOTE]  
> Tablo oluşturmak için SQL veritabanı 'na bağlanmanın birçok yolu vardır. Aşağıdaki adımlarda HDInsight kümesinden [FreeTDS](https://www.freetds.org/) kullanılır.

1. HDInsight kümesine FreeTDS yüklemek için aşağıdaki komutu kullanın:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. `<serverName>` Azure SQL Server adınızla değiştirmek ve Azure SQL Server oturum açma ile `<sqlLogin>` için aşağıdaki kodu düzenleyin.  Önkoşul SQL veritabanına bağlanmak için komutunu girin.  Komut isteminde parolayı girin.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Aşağıdaki metin gibi bir çıktı alırsınız:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. `1>` isteminde aşağıdaki satırları girin:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    `GO` deyimi girildiğinde önceki deyimler değerlendirilir. Bu deyimler, iş akışı tarafından kullanılan `mobiledata`adlı bir tablo oluşturur.

    Tablonun oluşturulduğunu doğrulamak için aşağıdaki komutları kullanın:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Aşağıdaki metin gibi bir çıktı görürsünüz:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. `1>` isteminde `exit` girerek TSQL yardımcı programından çıkın.

## <a name="create-the-job-definition"></a>İş tanımını oluşturma

İş tanımı iş akışı. xml ' nin nerede bulunacağını açıklar. Ayrıca, iş akışı tarafından kullanılan `useooziewf.hql`gibi diğer dosyaların nerede bulunacağını da açıklar. Ayrıca, iş akışı ve ilişkili dosyalar içinde kullanılan özellikler için değerleri tanımlar.

1. Varsayılan depolamanın tam adresini almak için aşağıdaki komutu kullanın. Bu adres, bir sonraki adımda oluşturduğunuz yapılandırma dosyasında kullanılır.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Bu komut aşağıdaki XML gibi bilgileri döndürür:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > HDInsight kümesi varsayılan depolama alanı olarak Azure Storage kullanıyorsa `<value>` öğesi içerikleri `wasbs://`ile başlar. Bunun yerine Azure Data Lake Storage 1. kullanılırsa `adl://`başlar. Azure Data Lake Storage 2. kullanılıyorsa, `abfs://`başlar.

    `<value>` öğesinin içeriğini, sonraki adımlarda kullanıldığı şekilde kaydedin.

2. Aşağıdaki XML 'i aşağıda gösterildiği gibi düzenleyin:

    |Yer tutucu değeri| Değiştirilmiş değer|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Adım 1 ' den alınan değer.|
    |Yöneticileri| Yönetici değilse HDInsight kümesi için oturum açma adınız.|
    |serverName| Azure SQL veritabanı sunucu adı.|
    |sqlLogin| Azure SQL veritabanı sunucusu oturum açma.|
    |SQLPassword de belirtilmelidir| Azure SQL veritabanı sunucusu oturum açma parolası.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    Bu dosyadaki bilgilerin çoğu, `${nameNode}`gibi Workflow. xml veya oozıewf. HQL dosyalarında kullanılan değerleri doldurmak için kullanılır.  Yol bir `wasbs` yolu ise, tam yolu kullanmanız gerekir. Yalnızca `wasbs:///`için kısalmayın. `oozie.wf.application.path` girişi Workflow. xml dosyasının nerede bulunacağını tanımlar. Bu dosya, bu iş tarafından çalıştırılan iş akışını içerir.

3. Oozie iş tanımı yapılandırması oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    nano job.xml
    ```

4. Nano düzenleyici açıldıktan sonra, düzenlenen XML dosyasını dosyanın içeriği olarak yapıştırın.

5. Dosyayı kaydetmek için **CTRL + X**' i seçin, **Y**girin ve ardından **ENTER**' u seçin.

## <a name="submit-and-manage-the-job"></a>İşi gönderme ve yönetme

Aşağıdaki adımlarda, kümedeki Oozie iş akışlarını göndermek ve yönetmek için Oozie komutu kullanılır. Oozie komutu, [oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)üzerinde kolay bir arabirimdir.

> [!IMPORTANT]  
> Oozie komutunu kullandığınızda, HDInsight baş düğümü için FQDN 'yi kullanmanız gerekir. Bu FQDN 'ye yalnızca kümeden erişilebilir veya küme bir Azure sanal ağı üzerinde ise aynı ağdaki diğer makinelerden yapılır.

1. Oozie hizmetinin URL 'sini almak için aşağıdaki komutu kullanın:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Bu, aşağıdaki XML gibi bilgileri döndürür:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` bölümü Oozie komutuyla kullanılacak URL 'dir.

2. URL 'YI daha önce aldığınız kodla değiştirecek şekilde kodu düzenleyin. URL için bir ortam değişkeni oluşturmak üzere aşağıdakileri kullanarak her komut için girmeniz gerekmez:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. İşi göndermek için aşağıdakileri kullanın:

    ```bash
    oozie job -config job.xml -submit
    ```

    Bu komut `job.xml` 'dan iş bilgilerini yükler ve Oozie 'ye gönderir ancak çalıştırmaz.

    Komut bittikten sonra, işin KIMLIĞINI (örneğin, `0000005-150622124850154-oozie-oozi-W`) döndürmelidir. Bu KIMLIK, işi yönetmek için kullanılır.

4. `<JOBID>` değiştirmek için aşağıdaki kodu düzenleyin ve önceki adımda döndürülen KIMLIK ile değiştirin.  İşin durumunu görüntülemek için aşağıdaki komutu kullanın:

    ```bash
    oozie job -info <JOBID>
    ```

    Bu, aşağıdaki metin gibi bilgileri döndürür:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Bu işin durumu `PREP`. Bu durum işin oluşturulduğunu, ancak başlatılmadığını gösterir.

5. `<JOBID>` değiştirmek için aşağıdaki kodu düzenleyin, daha önce döndürülen KIMLIK ile değiştirin.  İşi başlatmak için aşağıdaki komutu kullanın:

    ```bash
    oozie job -start <JOBID>
    ```

    Bu komuttan sonra durumu denetlürsünüz, bu durum çalışır durumdadır ve iş içindeki eylemler için bilgi döndürülür.  İşin tamamlanması birkaç dakika sürer.

6. `<serverName>` Azure SQL Server adınızla değiştirmek ve Azure SQL Server oturum açma ile `<sqlLogin>` için aşağıdaki kodu düzenleyin.  *Görev başarıyla tamamlandıktan sonra* , aşağıdaki komutu kullanarak verilerin OLUŞTURULDUĞUNU ve SQL veritabanı tablosuna verildiğini doğrulayabilirsiniz.  Komut isteminde parolayı girin.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    `1>` istemine aşağıdaki sorguyu girin:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Döndürülen bilgiler aşağıdaki metin gibidir:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Oozie komutu hakkında daha fazla bilgi için bkz. [Apache Oozie komut satırı aracı](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Oozie REST API ile, Oozie ile çalışan kendi araçlarınızı oluşturabilirsiniz. Aşağıda, Oozie REST API kullanımı hakkında HDInsight 'a özgü bilgiler verilmiştir:

* **URI**: REST API `https://CLUSTERNAME.azurehdinsight.net/oozie`küme dışından erişebilirsiniz.

* **Kimlik doğrulama**: kimlik doğrulaması IÇIN, API 'yi, küme http hesabı (yönetici) ve parolasını kullanın. Örnek:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Oozie REST API kullanma hakkında daha fazla bilgi için bkz. [Apache Oozie Web Hizmetleri API 'si](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie Web Kullanıcı arabirimi

Oozie Web Kullanıcı arabirimi, kümedeki Oozie işlerinin durumuna Web tabanlı bir görünüm sağlar. Web Kullanıcı arabirimi ile aşağıdaki bilgileri görüntüleyebilirsiniz:

   * İş durumu
   * İş tanımı
   * Yapılandırma
   * İşteki eylemlerin bir grafiği
   * İş için Günlükler

Ayrıca, bir iş içindeki eylemlerin ayrıntılarını görüntüleyebilirsiniz.

Oozie Web Kullanıcı arabirimine erişmek için aşağıdaki adımları izleyin:

1. HDInsight kümesine SSH tüneli oluşturun. Daha fazla bilgi için bkz. [HDInsight Ile SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md).

2. Bir tünel oluşturduktan sonra, URI `http://headnodehost:8080`kullanarak Web tarayıcınızda ambarı Web Kullanıcı arabirimini açın.

3. Sayfanın sol tarafındaki **oozie** > **hızlı bağlantılar** > **Oozie Web Kullanıcı arabirimi**' ni seçin.

    ![Apache ambarı Oozie Web Kullanıcı arabirimi adımları](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Oozie Web Kullanıcı arabirimi, çalışan iş akışı işlerinin varsayılan değerlerini görüntüler. Tüm iş akışı işlerini görmek için **tüm işler**' i seçin.

    ![Oozie Web konsolu iş akışı işleri](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Bir iş hakkında daha fazla bilgi görüntülemek için işi seçin.

    ![HDInsight Apache Oozie iş bilgileri](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. **Iş bilgileri** sekmesinden, iş içindeki temel iş bilgilerini ve bireysel eylemleri görebilirsiniz. En üstteki sekmeleri kullanarak **Iş tanımı**, **Iş yapılandırması**, **Iş günlüğüne**erişin veya iş **dag**altında işin yönlendirilmiş bir döngüsel bir grafiğini (DAG) görüntüleyebilirsiniz.

   * **Iş günlüğü**: iş için tüm günlükleri almak üzere **günlükleri al** düğmesini seçin veya günlükleri filtrelemek için **arama filtresi gir** alanını kullanın.

       ![HDInsight Apache Oozie iş günlüğü](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Iş dag**: dag, iş akışı aracılığıyla alınan veri yollarına ilişkin grafiksel bir genel bakıştır.

       ![HDInsight Apache Oozie iş dag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. **Iş bilgileri** sekmesindeki eylemlerden birini seçerseniz eyleme ilişkin bilgileri getirir. Örneğin **Runsqoopexport** eylemini seçin.

    ![HDInsight Oozie işi eylem bilgileri](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Eyleme ilişkin ayrıntıları, örneğin **konsol URL 'si**bağlantısını görebilirsiniz. İş için iş izleyici bilgilerini görüntülemek için bu bağlantıyı kullanın.

## <a name="schedule-jobs"></a>İşleri zamanlayın

Bir başlangıç, bitiş ve iş için yineleme sıklığını belirtmek için düzenleyiciyi kullanabilirsiniz. İş akışı için bir zamanlama tanımlamak üzere aşağıdaki adımları izleyin:

1. **Coordinator. xml**adlı bir dosya oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    nano coordinator.xml
    ```

    Dosyanın içeriği olarak aşağıdaki XML 'i kullanın:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > `${...}` değişkenleri, çalışma zamanında iş tanımındaki değerlerle değiştirilmiştir. Değişkenler şunlardır:
    >
    > * `${coordFrequency}`: işin çalıştırma örnekleri arasındaki zaman.
    > * `${coordStart}`: iş başlangıç zamanı.
    > * `${coordEnd}`: iş bitiş saati.
    > * `${coordTimezone}`: düzenleyici işleri, genellikle UTC kullanılarak temsil edilen gün ışığından yararlanma süresi olmayan sabit bir saat dilimlidir. Bu saat dilimi, *Oozie işleme saat dilimi* olarak adlandırılır.
    > * `${wfPath}`: Workflow. XML yolu.

2. Dosyayı kaydetmek için **CTRL + X**' i seçin, **Y**girin ve ardından **ENTER**' u seçin.

3. Bu işin çalışma dizinine dosyayı kopyalamak için aşağıdaki komutu kullanın:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Daha önce oluşturduğunuz `job.xml` dosyasını değiştirmek için aşağıdaki komutu kullanın:

    ```bash
    nano job.xml
    ```

    Aşağıdaki değişiklikleri yapın:

   * Oozie 'nin iş akışı yerine düzenleyici dosyasını çalıştırmasını istemek için `<name>oozie.wf.application.path</name>` `<name>oozie.coord.application.path</name>`olarak değiştirin.

   * Düzenleyici tarafından kullanılan `workflowPath` değişkenini ayarlamak için aşağıdaki XML 'i ekleyin:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       `wasbs://mycontainer@mystorageaccount.blob.core.windows` metnini, Job. xml dosyasındaki diğer girdilerde kullanılan değerle değiştirin.

   * Düzenleyicinin başlangıç, bitiş ve sıklığını tanımlamak için aşağıdaki XML 'i ekleyin:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Bu değerler, başlangıç saatini 10 Mayıs 2018 ve bitiş saati olarak 12 Mayıs 2018 ' de 12:00 PM olarak ayarlar. Bu işi çalıştırmaya yönelik Aralık günlük olarak ayarlanır. Sıklık dakika, bu nedenle 24 saat x 60 dakika = 1440 dakika. Son olarak, saat dilimi UTC olarak ayarlanır.

5. Dosyayı kaydetmek için **CTRL + X**' i seçin, **Y**girin ve ardından **ENTER**' u seçin.

6. İşi göndermek ve başlatmak için aşağıdaki komutu kullanın:

    ```bash
    oozie job -config job.xml -run
    ```

7. Oozie Web Kullanıcı arabirimine giderseniz ve **Düzenleyici işleri** sekmesini seçerseniz, aşağıdaki görüntüde olduğu gibi bilgileri görürsünüz:

    ![Oozie Web Konsolu Düzenleyicisi işler sekmesi](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    **Sonraki materialization** girişi, işin bir sonraki çalıştırılışında bulunur.

8. Önceki iş akışı işi gibi, Web Kullanıcı arabiriminde iş girişini seçerseniz iş üzerinde bilgi görüntülenir:

    ![Apache Oozie Düzenleyicisi iş bilgileri](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Bu görüntü, zamanlanan iş akışındaki bireysel eylemleri değil yalnızca işin başarılı çalıştırmalarını gösterir. Tek tek eylemleri görmek için **eylem** girişlerinden birini seçin.

    ![OOzie Web konsolu iş bilgileri sekmesi](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Sorun giderme

Oozie Kullanıcı arabirimi ile Oozie günlüklerini görüntüleyebilirsiniz. Oozie Kullanıcı arabirimi, iş akışı tarafından başlatılan MapReduce görevlerine yönelik JobTracker günlüklerine bağlantılar da içerir. Sorun giderme deseninin olması gerekir:

   1. İşi Oozie Web Kullanıcı arabiriminde görüntüleyin.

   2. Belirli bir eylem için hata veya hata oluşursa, **hata iletisi** alanının hata hakkında daha fazla bilgi sağladığını görmek için eylemi seçin.

   3. Varsa, eylem için JobTracker günlükleri gibi daha fazla ayrıntı görüntülemek için eylemden URL 'YI kullanın.

Aşağıdakiler, karşılaşabileceğiniz ve bunları nasıl çözeolabileceğiniz özel hatalardır.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: küme başlatılamıyor

**Belirtiler**: Iş durumu **askıya alındı**olarak değişir. İşin ayrıntıları, `RunHiveScript` durumunu **START_MANUAL**olarak gösterir. Eylemi seçmek şu hata iletisini görüntüler:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Neden**: **iş. xml** dosyasında kullanılan Azure Blob depolama adresleri depolama kapsayıcısı veya depolama hesabı adı içermiyor. BLOB depolama adresi biçiminin `wasbs://containername@storageaccountname.blob.core.windows.net`olması gerekir.

**Çözüm**: Işin kullandığı BLOB depolama adreslerini değiştirin.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie &lt;kullanıcının kimliğine bürünmesine izin verilmiyor&gt;

**Belirtiler**: Iş durumu **askıya alındı**olarak değişir. İşin ayrıntıları, `RunHiveScript` durumunu **START_MANUAL**olarak gösterir. Eylemi seçerseniz, aşağıdaki hata iletisini gösterir:

    JA002: User: oozie is not allowed to impersonate <USER>

**Neden**: geçerli Izin ayarları Oozie 'nin belirtilen kullanıcı hesabını taklit etmesine izin vermez.

**Çözüm**: Oozie **Kullanıcılar grubundaki kullanıcıları** taklit edebilir. Kullanıcı hesabının üyesi olduğu grupları görmek için `groups USERNAME` kullanın. Kullanıcı, **Kullanıcılar** grubunun üyesi değilse, kullanıcıyı gruba eklemek için aşağıdaki komutu kullanın:

    sudo adduser USERNAME users

> [!NOTE]  
> HDInsight 'ın Kullanıcı gruba eklendiğini tanıması birkaç dakika sürebilir.

### <a name="launcher-error-sqoop"></a>Başlatıcı hatası (Sqoop)

**Belirtiler**: Iş durumu **sonlandırıldı**olarak değişir. İşin ayrıntıları `RunSqoopExport` durumunu **hata**olarak gösterir. Eylemi seçerseniz, aşağıdaki hata iletisini gösterir:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Neden**: Sqoop, veritabanına erişmek için gereken veritabanı sürücüsünü yükleyemiyor.

**Çözüm**: bir Oozie Işinden Sqoop kullandığınızda, iş akışı. xml gibi diğer kaynaklarla veritabanı sürücüsünü dahil etmeniz gerekir. Ayrıca, Workflow. xml ' in `<sqoop>...</sqoop>` bölümünden veritabanı sürücüsünü içeren arşive başvurun.

Örneğin, bu belgedeki iş için aşağıdaki adımları kullanacaksınız:

1. `mssql-jdbc-7.0.0.jre8.jar` dosyasını **/Tutorials/useoozie** dizinine kopyalayın:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Aşağıdaki XML 'i `</sqoop>`yukarıdaki yeni bir satıra eklemek için `workflow.xml` değiştirin:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir Oozie iş akışını tanımlamanızı ve Oozie işinin nasıl çalıştırılacağını öğrendiniz. HDInsight ile çalışma hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [HDInsight 'ta Apache Hadoop işleri için veri yükleme][hdinsight-upload-data]
* [HDInsight 'ta Apache Hadoop Apache Sqoop kullanma][hdinsight-use-sqoop]
* [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma][hdinsight-use-hive]
* [HDInsight için Java MapReduce programları geliştirme](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
