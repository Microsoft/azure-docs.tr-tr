---
title: Azure HDInsight 3,6 Hive iş yüklerini HDInsight 'a geçirme 4,0
description: HDInsight 3,6 ' de Apache Hive iş yüklerini HDInsight 4,0 ' ye geçirmeyi öğrenin.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 43d616bc82c608918f5e7ee51481a393dd55a284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566079"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3,6 Hive iş yüklerini HDInsight 'a geçirme 4,0

HDInsight 4,0, HDInsight 3,6 ' den fazla avantaj sunar. [Hdınsight 4,0 ' deki yenilikler hakkında genel bir bakış](../hdinsight-version-release.md)aşağıda verilmiştir.

Bu makalede, Hive iş yüklerini HDInsight 3,6 ' den 4,0 ' e geçirme adımları ele alınmaktadır.

* Hive meta veri deposu kopyalama ve şema yükseltmesi
* ACID uyumluluğu için güvenli geçiş
* Hive güvenlik ilkelerinin korunması

Yeni ve eski HDInsight kümelerinin aynı depolama hesaplarına erişimi olmalıdır.

Hive tablolarının yeni bir depolama hesabına geçirilmesi ayrı bir adım olarak yapılmalıdır. Bkz. [depolama hesapları arasında Hive geçişi](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>Yükseltme adımları

### <a name="1-prepare-the-data"></a>1. verileri hazırlama

* HDInsight 3,6 varsayılan olarak ACID tablolarını desteklemez. Ancak ACID tabloları mevcutsa, bunlara ' Ana ' sıkıştırmayı çalıştırın. Düzenleme hakkındaki ayrıntılar için [Hive dilinde el ile](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) bakın.

* [Azure Data Lake Storage 1.](../overview-data-lake-storage-gen1.md)kullanıyorsanız, Hive tablo konumları büyük olasılıkla kümenin ıseconfigurations yapılandırmasına bağımlıdır. Bu konumları diğer kümelere taşınabilir hale getirmek için aşağıdaki betik eylemini çalıştırın. [Çalışan bir küme Için betik eylemi](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)' ne bakın.

    |Özellik | Değer |
    |---|---|
    |Bash betiği URI 'SI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Düğüm türleri|Head|
    |Parametreler||

### <a name="2-copy-the-sql-database"></a>2. SQL veritabanını kopyalayın

* Küme varsayılan bir Hive meta veri deposu kullanıyorsa, meta verileri dış bir meta yüklemeye aktarmak için bu [Kılavuzu](./hive-default-metastore-export-import.md) izleyin. Ardından, yükseltme için dış meta veri deposu 'nın bir kopyasını oluşturun.

* Küme bir dış Hive meta veri deposu kullanıyorsa, bir kopyasını oluşturun. Seçenekler [içeri/dışarı aktarma](../../azure-sql/database/database-export.md) ve [zaman içindeki bir noktaya geri yükleme](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore)içerir.

### <a name="3-upgrade-the-metastore-schema"></a>3. meta veri deposu şemasını yükseltin

Bu adım, [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) meta veri deposu şemasını yükseltmek Için hdınsight 4,0 ' den yararlanır.

> [!Warning]
> Bu adım geri alınamaz. Bunu yalnızca meta veri deposu kopyasında çalıştırın.

1. 4,0 Hive 'e erişmek için geçici bir HDInsight 4,0 kümesi oluşturun `schematool` . Bu adım için [varsayılan Hive meta veri deposu](../hdinsight-use-external-metadata-stores.md#default-metastore) kullanabilirsiniz.

1. HDInsight 4,0 kümesinden, `schematool` hedef hdınsight 3,6 meta öğesini yükseltmek için yürütün:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Bu yardımcı program `beeline` IÇINDEKI SQL betiklerini yürütmek için istemcisini kullanır `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > Bu betiklerdeki SQL sözdizimi diğer istemci araçlarıyla uyumlu değildir. Örneğin, [Azure portalında](../../azure-sql/database/connect-query-portal.md) [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) ve sorgu Düzenleyicisi `GO` her komuttan sonra anahtar sözcük gerektirir.
    >
    > Kaynak kapasitesi veya işlem zaman aşımları nedeniyle herhangi bir betik başarısız olursa, SQL veritabanını ölçeklendirin.

1. Son sürümü Query ile doğrulayın `select schema_version from dbo.version` .

    Çıktı, HDInsight 4,0 kümesinden aşağıdaki Bash komutuyla eşleşmelidir.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Geçici HDInsight 4,0 kümesini silin.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. yeni bir HDInsight 4,0 kümesi dağıtın

[Yükseltilen Hive meta veri deposu](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) ve aynı depolama hesaplarını seçerek yeni bir HDInsight 4,0 kümesi oluşturun.

* Yeni küme, aynı varsayılan dosya sistemine sahip olmasını gerektirmez.

* Meta veri deposu birden çok depolama hesabında bulunan tablolar içeriyorsa, bu tablolara erişmek için bu depolama hesaplarını yeni kümeye eklemeniz gerekir. Bkz. [HDInsight 'a ek depolama hesapları ekleme](../hdinsight-hadoop-add-storage.md).

* Depolama alanı erişilebilirliği nedeniyle Hive işleri başarısız olursa, tablo konumunun kümeye eklenmiş bir depolama hesabında olduğunu doğrulayın.

    Tablo konumunu tanımlamak için aşağıdaki Hive komutunu kullanın:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. ACID uyumluluğu için tabloları dönüştürün

Yönetilen tablolar HDInsight 4,0 ' de ACID uyumlu olmalıdır. `strictmanagedmigration`Tüm ACID olmayan yönetilen tabloları özelliği olan dış tablolara dönüştürmek Için hdınsight 4,0 üzerinde çalıştırın `'external.table.purge'='true'` . Headnode 'dan yürütün:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight sürümleri arasında güvenli Hive

HDInsight, isteğe bağlı olarak HDInsight Kurumsal Güvenlik Paketi (ESP) kullanarak Azure Active Directory tümleştirilir. ESP, küme içindeki belirli kaynakların izinlerini yönetmek için Kerberos ve Apache Ranger kullanır. HDInsight 3,6 ' de Hive 'e karşı dağıtılan Ranger ilkeleri, aşağıdaki adımlarla HDInsight 4,0 ' e geçirilebilir:

1. HDInsight 3,6 kümenizdeki Ranger Service Manager paneline gidin.
2. **HIVE** adlı ilkeye gidin ve ilkeyi bir JSON dosyasına dışarı aktarın.
3. Yeni kümede, verilen ilke JSON öğesinde başvurulan tüm kullanıcıların var olduğundan emin olun. Bir kullanıcıya, ilke JSON ' de başvurulmazsa, ancak yeni kümede yoksa, kullanıcıyı yeni kümeye ekleyin veya ilkeden başvuruyu kaldırın.
4. HDInsight 4,0 kümenizdeki **Ranger Service Manager** paneline gidin.
5. **HIVE** adlı ilkeye gidin ve adım 2 ' den Ranger ilkesi JSON 'sini içeri aktarın.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>HDInsight 4,0 ' de uygulama değişiklikleri gerektirebilecek Hive değişiklikleri

* ACID tabloları için Spark ve Hive arasında meta tablo paylaşmak üzere [Hive ambarı bağlayıcısını kullanarak ek yapılandırma](./apache-hive-warehouse-connector.md) konusuna bakın.

* HDInsight 4,0, [depolama tabanlı yetkilendirme](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server)kullanır. Dosya izinlerini değiştirir veya, Hive 'den farklı bir kullanıcı olarak klasör oluşturursanız, büyük olasılıkla depolama izinlerine göre Hive hatalarına ulaşırsınız. Onarmak için `rw-` kullanıcıya erişim izni verin. Bkz. [Gupermissions Kılavuzu](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` ile değiştirilmiştir `Beeline` .

Ek değişiklikler için [hdınsight 4,0 duyurusuna](../hdinsight-version-release.md) bakın.

## <a name="further-reading"></a>Daha fazla bilgi

* [HDInsight 4,0 duyurusu](../hdinsight-version-release.md)
* [HDInsight 4,0 derin bakış](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID tabloları](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)