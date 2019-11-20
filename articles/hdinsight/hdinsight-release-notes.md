---
title: Azure HDInsight için sürüm notları
description: Azure HDInsight için en son sürüm notları. Hadoop, Spark, R Server, Hive ve daha fazlası için geliştirme ipuçları ve ayrıntıları alın.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 7c31520efd881e8e0b5ed309f62d273bac59c0e3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945024"
---
# <a name="release-notes"></a>Sürüm notları

Bu makalede **en son** Azure HDInsight sürüm güncelleştirmeleri hakkında bilgi sağlanır. Önceki sürümler hakkında daha fazla bilgi için bkz. [HDInsight sürüm notları Arşivi](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux, HDInsight sürüm 3.4 ve üzerinde kullanılan tek işletim sistemidir. Daha fazla bilgi için bkz. [HDInsight sürüm oluşturma makalesi](hdinsight-component-versioning.md).

## <a name="summary"></a>Özet

Azure HDInsight, Azure üzerinde açık kaynaklı Apache Hadoop ve Apache Spark analizlere yönelik kurumsal müşteriler arasındaki en popüler hizmetlerden biridir.

## <a name="new-features"></a>Yeni Özellikler

HDInsight 4,0 ile ilgili önemli değişiklikler hakkında daha fazla bilgi için bkz. [HDI 4,0 ' deki](../hdinsight/hdinsight-version-release.md)yenilikler.

## <a name="component-versions"></a>Bileşen sürümleri

Tüm HDInsight 4,0 bileşenlerinin resmi Apache sürümleri aşağıda verilmiştir. Listelenen bileşenler, kullanılabilir en son kararlı sürümlerin yayınlardır.

- Apache ambarı 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0 +
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Kaydırıcı 0.92.0
- Apache Spark 2.3.1, 2.4.0
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Apache bileşenlerinin sonraki sürümleri, bazen yukarıda listelenen sürümlere ek olarak HDP dağıtımında paketlenmiştir. Bu durumda, bu sonraki sürümler teknik önizlemeler tablosunda listelenir ve bir üretim ortamında yukarıdaki listenin Apache bileşen sürümleri yerine kullanılmamalıdır.

## <a name="apache-patch-information"></a>Apache Patch bilgileri

HDInsight 4,0 ' de kullanılabilen düzeltme ekleri hakkında daha fazla bilgi için, aşağıdaki tabloda yer alan her ürün için düzeltme eki listesine bakın.

| Ürün adı | Düzeltme eki bilgileri |
|---|---|
| Ambari | [Ambarı düzeltme eki bilgileri](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Hadoop Patch bilgileri](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [HBase düzeltme eki bilgileri](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Kovan  | Bu sürüm, ek Apache düzeltme ekleri olmadan Hive 3.1.0 sağlar.  |
| Kafka | Bu sürüm, ek Apache düzeltme ekleri olmadan Kafka 1.1.1 sağlar. |
| Oozie | [Oozie düzeltme eki bilgileri](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix düzeltme eki bilgileri](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig Patch bilgileri](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Ranger düzeltme eki bilgileri](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark Patch bilgileri](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Bu sürüm, ek Apache düzeltme ekleri olmadan Sqoop 1.4.7 sağlar. |
| Tez | Bu sürüm, ek Apache düzeltme ekleri olmadan tez 0.9.1 sağlar. |
| Zeppelin | Bu sürüm, ek Apache düzeltme ekleri olmadan Zeppelin 0.8.0 sağlar. |
| Zookeeper | [Zookeeper Patch bilgileri](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Düzeltilen Ortak Güvenlik Açıkları ve Etkilenmeler

Bu sürümde çözümlenen güvenlik sorunları hakkında daha fazla bilgi için bkz. Hortonçalışmalar ' [için HDP 3.0.1 Için sabit ortak güvenlik açıkları ve Etkilenmeler](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Çoğaltma, varsayılan yükleme ile güvenli HBase için kopuk

HDInsight 4,0 için aşağıdaki adımları uygulayın:

1. Küme içi iletişimi etkinleştirin.
1. Etkin yayın düğümünde oturum açın.
1. Aşağıdaki komutla çoğaltmayı etkinleştirmek için bir komut dosyası indirin:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Komutu `sudo kinit <domainuser>`yazın.
1. Betiği çalıştırmak için aşağıdaki komutu yazın:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
HDInsight 3,6 için aşağıdakileri yapın:

1. Etkin HMaster ZK 'de oturum açın.
1. Aşağıdaki komutla çoğaltmayı etkinleştirmek için bir komut dosyası indirin:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Komutu `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`yazın.
1. Aşağıdaki komutu yazın:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline, HBase kümesi HDInsight 4,0 ' e geçirildikten sonra çalışmayı durduruyor

Aşağıdaki adımları uygulayın:

1. Aşağıdaki Phoenix tablolarını bırakın:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Tablolardan hiçbirini silemseniz, tablolara yönelik bağlantıları temizlemek için HBase 'i yeniden başlatın.
1. `sqlline.py` komutunu yeniden çalıştırın. Phoenix, 1. adımda silinen tüm tabloları yeniden oluşturur.
1. HBase verileriniz için Phoenix tablolarını ve görünümlerini yeniden oluşturun.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline, HDInsight 3,6 ' den 4,0 ' ye HBase Phoenix meta verileri çoğaltıldıktan sonra çalışmayı durduruyor

Aşağıdaki adımları uygulayın:

1. Çoğaltmayı yapmadan önce, hedef 4,0 kümesine gidin ve yürütün `sqlline.py`. Bu komut, gibi `SYSTEM.MUTEX` Phoenix tabloları oluşturur ve `SYSTEM.LOG` yalnızca 4,0 içinde bulunur.
1. Aşağıdaki tabloları bırakın:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. HBase çoğaltmasını başlatma

## <a name="deprecation"></a>Kullanımdan kaldırma

Apache Storm ve ML Hizmetleri HDInsight 4,0 ' de kullanılamaz.
