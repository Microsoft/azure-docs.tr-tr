---
title: Hive ambarı Bağlayıcısı-Livy-Azure HDInsight kullanan Apache Zeppelin
description: Hive ambarı bağlayıcısını Azure HDInsight 'ta Apache Zeppelin ile tümleştirmeyi öğrenin.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 7957041c9d445f4239b8f0100128a531d982b451
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871563"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Zeppelin 'i Hive ambarı Bağlayıcısı ile tümleştirme

HDInsight Spark kümeleri, farklı yorumlayıcılara sahip Apache Zeppelin not defterlerini içerir. Bu makalede, Hive ambar Bağlayıcısı 'nı kullanarak Spark 'tan Hive tablolarına erişmek için yalnızca Livy Yorumlayıcısına odaklanacağız.

> [!NOTE]
> Bu makalede, Microsoft tarafından kullanılmayan bir terim olan *beyaz liste* teriminin başvuruları yer almaktadır. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.

## <a name="prerequisite"></a>Önkoşul

[Hive ambarı Bağlayıcısı kurulum](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) adımlarını doldurun.

## <a name="getting-started"></a>Kullanmaya başlama

1. Apache Spark kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ve sürümlerinin ve sürümlerini dikkate almak için SSH oturumınızdan aşağıdaki komutu yürütün `hive-warehouse-connector-assembly` `pyspark_hwc` :

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Daha sonra Apache Zeppelin yapılandırılırken kullanmak için çıktıyı kaydedin.

## <a name="configure-livy"></a>Livy 'ı yapılandırma

Zeppelin 'daki Hive tablolarına Livy yorumlayıcısıyla erişmek için aşağıdaki konfigürasyonlar gereklidir.

### <a name="interactive-query-cluster"></a>Etkileşimli sorgu kümesi

1. Bir Web tarayıcısından, `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` LLAPCLUSTERNAME ' ın etkileşimli sorgu Kümenizin adı olduğu yere gidin.

1. **Gelişmiş**  >  **özel çekirdek-site**' a gidin. Aşağıdaki konfigürasyonları eklemek için **Özellik Ekle...** öğesini seçin:

    | Yapılandırma                 | Değer |
    | ----------------------------- |-------|
    | Hadoop. proxyuser. Livy. Groups  | *     |
    | Hadoop. proxyuser. Livy. Konakları   | *     |

1. Değişiklikleri kaydedin ve etkilenen tüm bileşenleri yeniden başlatın.

### <a name="spark-cluster"></a>Spark kümesi

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` CLUSTERNAME Apache Spark Kümenizin adı ' na gidin.

1. **Custom livy2-conf** öğesini genişletin. Aşağıdaki yapılandırmayı eklemek için **Özellik Ekle...** öğesini seçin:

    | Yapılandırma                 | Değer                                      |
    | ----------------------------- |------------------------------------------  |
    | Livy. File. Local-dir-Whitelist | /usr/HDP/Current/hive_warehouse_connector/ |

1. Değişiklikleri kaydedin ve etkilenen tüm bileşenleri yeniden başlatın.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Zeppelin Kullanıcı arabiriminde (Spark kümesi) Livy yorumlayıcı yapılandırma

1. Bir Web tarayıcısından, ' a gidin `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` , burada `CLUSTERNAME` Apache Spark kümenizin adıdır.

1. **Livy2** adresine gidin.

1. Aşağıdaki konfigürasyonları ekleyin:

    | Yapılandırma                 | Değer                                      |
    | ----------------------------- |:------------------------------------------:|
    | Livy. spark. Hadoop. Hive. LLAP. Daemon. Service. Konakları | @llap0 |
    | Livy. spark. Security. Credentials. hiveserver2. Enabled | true |
    | Livy. spark. Sql. Hive. LLAP | true |
    | Livy. spark. yarn. Security. Credentials. hiveserver2. Enabled | true |
    | Livy. superusers | Livy, Zeppelin |
    | Livy. spark. jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>SÜRÜMÜ, daha önce [kullanmaya](#getting-started)Başlarken, edindiğiniz değerle değiştirin. |
    | Livy. spark. gönder. pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>SÜRÜMÜ, daha önce [kullanmaya](#getting-started)Başlarken, edindiğiniz değerle değiştirin. |
    | Livy. spark. Sql. Hive. hiveserver2. JDBC. URL | Etkileşimli sorgu kümesinin HiveServer2 Interactive JDBC URL 'sine ayarlayın. |
    | spark. Security. Credentials. hiveserver2. Enabled | true |

1. Yalnızca ESP kümeleri için aşağıdaki yapılandırmayı ekleyin:

    | Yapılandırma| Değer|
    |---|---|
    | Livy. spark. Sql. Hive. hiveserver2. JDBC. URL. Principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * Bir Web tarayıcısından, CLUSTERNAME öğesine gidin ve `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` etkileşimli sorgu kümenizin adıdır. **HiveServer2 Interactive** seçeneğine tıklayın. Ekran görüntüsünde gösterildiği gibi LLAP 'nin çalıştığı baş düğümün tam etki alanı adını (FQDN) görürsünüz. `<llap-headnode>`Bu değerle değiştirin.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Hive ambar Bağlayıcısı baş düğümü" border="true":::

    * Etkileşimli sorgu kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. `default_realm`Dosyadaki parametreyi bulun `/etc/krb5.conf` . `<AAD-DOMAIN>`Bu değerle değiştirin, aksi takdirde kimlik bilgisi bulunamadı.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Hive ambar Bağlayıcısı AAD etki alanı" border="true":::

    * Örneğin, `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .

1. Değişiklikleri kaydedin ve Livy yorumlayıcısını yeniden başlatın.

Eğer Livy yorumlayıcı erişilebilir değilse, `shiro.ini` ambarı 'Nda Zeppelin bileşeni içinde mevcut olan dosyayı değiştirin. Daha fazla bilgi için bkz. [Apache Zeppelin güvenliğini yapılandırma](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Zeppelin içinde sorguları çalıştırma 

Livy yorumlayıcı kullanarak bir Zeppelin Not defteri başlatın ve aşağıdakileri yürütün

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Sonraki adımlar

* [HWC ve Apache Spark işlemleri](./apache-hive-warehouse-connector-operations.md)
* [Apache Spark ve Apache Hive ile HWC tümleştirmesi](./apache-hive-warehouse-connector.md)
* [Etkileşimli Sorguyu HDInsight ile Kullanma](./apache-interactive-query-get-started.md)
