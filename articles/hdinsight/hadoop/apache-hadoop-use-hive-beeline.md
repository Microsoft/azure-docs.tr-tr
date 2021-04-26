---
title: Apache Hive-Azure HDInsight ile Apache Beeline kullanma
description: HDInsight 'ta Hadoop ile Hive sorguları çalıştırmak için Beeline istemcisini nasıl kullanacağınızı öğrenin. Beeline, JDBC üzerinden HiveServer2 ile çalışmaya yönelik bir yardımcı programdır.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: e8b7478ba64da0f99a9b7a710222ff2953795adf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943207"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Apache Beeline istemcisini Apache Hive ile kullanma

Bu makalede, bir SSH bağlantısı üzerinden Apache Hive sorguları oluşturmak ve yürütmek için komut satırı [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) istemcisinin nasıl kullanılacağı açıklanır.

## <a name="background"></a>Arka Plan

Beeline, HDInsight kümenizin baş düğümlerine eklenen bir Hive istemcanıdır. HDInsight kümenize yüklenmiş Beeline istemcisine bağlanmak veya Beeline yerel olarak yüklemek için bkz. [Apache Beeline bağlanma veya yükleme](connect-install-beeline.md). Beeline, HDInsight kümenizde barındırılan bir hizmet olan HiveServer2 'e bağlanmak için JDBC kullanır. Ayrıca, HDInsight 'ta Internet üzerinden uzaktan erişim sağlamak için Beeline da kullanabilirsiniz. Aşağıdaki örneklerde, Beeline 'dan HDInsight 'a bağlanmak için kullanılan en yaygın bağlantı dizeleri sağlanmaktadır.

## <a name="prerequisites-for-examples"></a>Örnek önkoşulları

* HDInsight üzerinde bir Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](./apache-hadoop-linux-tutorial-get-started.md).

* Kümenizin birincil depolama alanı için URI düzenine dikkat edin. Örneğin,  `wasb://` Azure depolama için, `abfs://` Azure Data Lake Storage 2. veya `adl://` Azure Data Lake Storage 1. için. Azure depolama için güvenli aktarım etkinse URI olur `wasbs://` . Daha fazla bilgi için bkz. [Güvenli aktarım](../../storage/common/storage-require-secure-transfer.md).

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md). Bu belgedeki adımların çoğu, kümeye bir SSH oturumundan Beeline kullandığınızı varsayar. Yerel bir Beeline istemcisini de kullanabilirsiniz, ancak bu adımlar bu makalede ele alınmıştır.

## <a name="run-a-hive-query"></a>Hive sorgusu çalıştırma

Bu örnek, bir SSH bağlantısından Beeline istemcisinin kullanılmasına dayalıdır.

1. Aşağıdaki kodla kümeye bir SSH bağlantısı açın. `sshuser` değerini, kümenizin SSH kullanıcısı ile, `CLUSTERNAME` değerini kümenizin adıyla değiştirin. İstendiğinde, SSH kullanıcı hesabının parolasını girin.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Aşağıdaki komutu girerek, açık SSH oturumunuzla Beeline istemcinizden HiveServer2 'e bağlanın:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline Commands bir karakterle başlar `!` , örneğin `!help` Yardımı görüntüler. Ancak `!` bazı komutlar için atlanabilir. Örneğin, `help` Ayrıca işe yarar.

    `!sql`HiveQL deyimlerini yürütmek için kullanılan. Ancak, HiveQL, önceki bir sürümünü atlamanızı sağlamak için yaygın olarak kullanılır `!sql` . Aşağıdaki iki deyim eşdeğerdir:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Yeni bir kümede yalnızca bir tablo listelenir: **hivesampletable**.

4. Hivesampletable için şemayı göstermek üzere aşağıdaki komutu kullanın:

    ```hiveql
    describe hivesampletable;
    ```

    Bu komut aşağıdaki bilgileri döndürür:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    Bu bilgiler tablodaki sütunları açıklar.

5. HDInsight kümesiyle birlikte sunulan örnek verileri kullanarak **log4jLogs** adlı bir tablo oluşturmak için aşağıdaki deyimlerini girin: (URI şemanız temelinde gerektiği şekilde gözden geçirin.)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Bu deyimler aşağıdaki işlemleri yapılır:

    |Deyim |Açıklama |
    |---|---|
    |DROP TABLE|Tablo varsa, silinir.|
    |DıŞ TABLO OLUŞTUR|Hive içinde bir **dış** tablo oluşturur. Dış tablolar yalnızca tablo tanımını Hive içinde depolar. Veriler özgün konumda bırakılır.|
    |SATıR BIÇIMI|Verilerin biçimlendirilmesi. Bu durumda, her günlükteki alanlar boşlukla ayrılır.|
    |TEXTFILE KONUMU OLARAK DEPOLANDı|Verilerin depolandığı yer ve dosya biçimi.|
    |SELECT|**T4** sütununun **[Error]** değerini içerdiği tüm satırların sayısını seçer. Bu sorgu, bu değeri içeren üç satır olduğu için **3** değerini döndürür.|
    |'%. Log ' gıbı INPUT__FILE__NAME|Hive, şemayı dizindeki tüm dosyalara uygulamayı dener. Bu durumda, Dizin şemayla eşleşmeyen dosyaları içerir. Sonuçlarda çöp verilerinin oluşmasını engellemek için, bu ifade Hive 'ye yalnızca. log ile biten dosyalardaki verileri döndürmeyeceğini söyler.|

   > [!NOTE]  
   > Dış tablolar, temel alınan verilerin bir dış kaynak tarafından güncelleştirilmesini beklediğinde kullanılmalıdır. Örneğin, otomatik bir veri yükleme işlemi veya MapReduce işlemi.
   >
   > Dış tablonun atılması, yalnızca tablo tanımı olan **verileri silmez.**

    Bu komutun çıktısı aşağıdaki metne benzer:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Beeline çıkış:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>HiveQL dosyası çalıştırma

Bu örnek, önceki örnekteki devamlılık örneğidir. Bir dosya oluşturmak için aşağıdaki adımları kullanın, ardından Beeline kullanarak çalıştırın.

1. **Query. HQL** adlı bir dosya oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    nano query.hql
    ```

1. Dosyanın içeriği olarak aşağıdaki metni kullanın. Bu sorgu **errorlogs** adlı yeni bir ' internal ' tablosu oluşturur:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Bu deyimler aşağıdaki işlemleri yapılır:

    |Deyim |Açıklama |
    |---|---|
    |MEVCUT DEĞILSE CREATE TABLE|Tablo henüz yoksa, oluşturulur. **Dış** anahtar sözcük kullanılmadığından, bu ifade bir iç tablo oluşturur. İç tablolar Hive veri ambarında depolanır ve tamamen Hive tarafından yönetilir.|
    |ORC OLARAK DEPOLANıR|Verileri Iyileştirilmiş satır sütunlu (ORC) biçimde depolar. ORC biçimi, Hive verilerinin depolanması için yüksek düzeyde iyileştirilmiş ve verimli bir biçimdir.|
    |ÜZERINE YAZMA EKLE... SEÇIN|**Log4jLogs** tablosundan **[Error]** içeren satırları seçer, ardından verileri **errorlogs** tablosuna ekler.|

    > [!NOTE]  
    > Dış tablolardan farklı olarak, iç tablo bırakılırken temel alınan veriler de silinir.

1. Dosyayı kaydetmek için **CTRL** + **X** kullanın, **Y** girin ve son olarak **girin**.

1. Dosyayı Beeline kullanarak çalıştırmak için aşağıdakileri kullanın:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i`Parametresi, Beeline başlar ve dosyadaki deyimleri çalıştırır `query.hql` . Sorgu tamamlandıktan sonra, `jdbc:hive2://headnodehost:10001/>` istemde bir uyarı alırsınız. Ayrıca `-f` , sorgu tamamlandıktan sonra Beeline çıkış eden parametresini kullanarak bir dosya çalıştırabilirsiniz.

1. **Errorlogs** tablosunun oluşturulduğunu doğrulamak Için, **hata günlüklerinden** tüm satırları döndürmek için aşağıdaki ifadeyi kullanın:

    ```hiveql
    SELECT * from errorLogs;
    ```

    T4 sütununda **[Error]** içeren, üç satırlık verilerin döndürülmesi gerekir:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Sonraki adımlar

* HDInsight 'ta Hive hakkında daha fazla genel bilgi için bkz. [HDInsight 'ta Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)

* HDInsight 'ta Hadoop ile çalışmanın diğer yolları hakkında daha fazla bilgi için bkz. [HDInsight 'Ta MapReduce 'yi Apache Hadoop kullanma](hdinsight-use-mapreduce.md)
