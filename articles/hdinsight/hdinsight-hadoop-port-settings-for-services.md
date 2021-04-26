---
title: HDInsight 'ta Hadoop Hizmetleri tarafından kullanılan bağlantı noktaları-Azure
description: Bu makale, Azure HDInsight 'ta çalışan Apache Hadoop Hizmetleri tarafından kullanılan bağlantı noktalarının bir listesini sağlar
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b5dadb886078f701bb01447efd606c862ee73073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945528"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>HDInsight üzerinde Apache Hadoop Hizmetleri tarafından kullanılan bağlantı noktaları

Bu belge, HDInsight kümelerinde çalışan Apache Hadoop Hizmetleri tarafından kullanılan bağlantı noktalarının bir listesini sağlar. Ayrıca, SSH kullanarak kümeye bağlanmak için kullanılan bağlantı noktaları hakkında bilgi sağlar.

## <a name="public-ports-vs-non-public-ports"></a>Genel bağlantı noktaları ile genel olmayan bağlantı noktaları

Linux tabanlı HDInsight kümeleri Internet üzerinde yalnızca üç bağlantı noktasını genel kullanıma sunar: 22, 23 ve 443. Bu bağlantı noktaları, güvenli HTTPS protokolü üzerinden kullanıma sunulan SSH ve hizmetler kullanılarak güvenli küme erişimi.

HDInsight, Azure sanal ağı üzerinde çalışan çeşitli Azure sanal makineleri (küme düğümleri) tarafından uygulanır. Sanal ağ içinden Internet üzerinden gösterilmeyen bağlantı noktalarına erişebilirsiniz. Baş düğüme SSH aracılığıyla bağlanıyorsanız, küme düğümlerinde çalışan hizmetlere doğrudan erişebilirsiniz.

> [!IMPORTANT]  
> HDInsight için bir Azure sanal ağı yapılandırma seçeneği olarak belirtmezseniz, biri otomatik olarak oluşturulur. Ancak, diğer makinelere (diğer Azure sanal makineleri veya istemci geliştirme makineniz gibi) bu sanal ağa katılamaz.

Sanal ağa ek makineler katmak için önce sanal ağı oluşturmanız ve ardından HDInsight kümenizi oluştururken belirtmeniz gerekir. Daha fazla bilgi için bkz. [HDInsight için bir sanal ağ planlayın](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Ortak bağlantı noktaları

HDInsight kümesindeki tüm düğümler bir Azure sanal ağında bulunur. Düğümlere doğrudan internet 'ten erişilemez. Ortak ağ geçidi, tüm HDInsight küme türlerinde ortak olan aşağıdaki bağlantı noktalarına internet erişimi sağlar.

| Hizmet | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- |
| SSHD |22 |SSH |İstemcileri birincil headnode üzerinde SSHD 'ye bağlar. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md). |
| SSHD |22 |SSH |İstemcileri kenar düğümündeki SSHD 'ye bağlar. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md). |
| SSHD |23 |SSH |İstemcileri ikincil headnode üzerinde SSHD 'ye bağlar. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Ambarı Web Kullanıcı arabirimi. Bkz [. Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight 'ı yönetme](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambarı REST API. Bkz [. Apache ambarı 'nı kullanarak HDInsight 'ı yönetme REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Bkz.  [kıvrımlı Ile MapReduce kullanma](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |ODBC kullanarak Hive 'e bağlanır. Bkz. [MICROSOFT ODBC sürücüsü Ile HDInsight 'A Excel 'ı bağlama](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |JDBC kullanarak ApacheHive 'e bağlanır. Bkz [. HIVE JDBC sürücüsünü kullanarak HDInsight 'ta Apache Hive bağlama](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Aşağıdakiler, belirli küme türleri için kullanılabilir:

| Hizmet | Bağlantı noktası | Protokol | Küme türü | Description |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |HBase |HBase REST API. Bkz. [Apache HBase kullanmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API. Bkz. [Apache Spark Işlerini Apache Livy kullanarak uzaktan gönderme](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift sunucusu |443 |HTTPS |Spark |Hive sorguları göndermek için kullanılan Spark Thrift sunucusu. Bkz. [HDInsight üzerinde Apache Hive Beeline kullanma](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Fırtınası Web Kullanıcı arabirimi. Bkz. [HDInsight 'ta Apache Storm topolojilerini dağıtma ve yönetme](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Kafka Rest proxy |443 |HTTPS |Kafka |Kafka REST API. Bkz. [rest proxy kullanarak Azure HDInsight 'ta Apache Kafka kümeleriyle etkileşim kurma](kafka/rest-proxy.md) |

### <a name="authentication"></a>Kimlik Doğrulaması

İnternet 'te genel olarak kullanıma sunulan tüm hizmetlerin kimliği doğrulanmalıdır:

| Bağlantı noktası | Kimlik bilgileri |
| --- | --- |
| 22 veya 23 |Küme oluşturma sırasında belirtilen SSH kullanıcı kimlik bilgileri |
| 443 |Küme oluşturma sırasında ayarlanan oturum açma adı (varsayılan: yönetici) ve parola |

## <a name="non-public-ports"></a>Genel olmayan bağlantı noktaları

> [!NOTE]  
> Bazı hizmetler yalnızca belirli küme türlerinde kullanılabilir. Örneğin, HBase yalnızca HBase küme türlerinde kullanılabilir.

> [!IMPORTANT]  
> Bazı hizmetler tek seferde yalnızca bir baş düğümüne üzerinde çalışır. Birincil baş düğümüne üzerinde hizmete bağlanmaya ve bir hata almaya çalışırsanız, ikincil yayın düğümünü kullanmayı deneyin.

### <a name="ambari"></a>Ambari

| Hizmet | Düğümler | Bağlantı noktası | URL yolu | Protokol |
| --- | --- | --- | --- | --- |
| Ambarı Web Kullanıcı arabirimi | Baş düğümler | 8080 | / | HTTP |
| Ambarı REST API | Baş düğümler | 8080 | /api/v1 | HTTP |

Örnekler:

* Ambarı REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Bağlantı noktaları

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| Süs Code Web Kullanıcı arabirimi |Baş düğümler |30070 |HTTPS |Durumu görüntülemek için Web Kullanıcı arabirimi |
| Süs Code meta veri hizmeti |baş düğümler |8020 |'YI |Dosya sistemi meta verileri |
| Batanode |Tüm çalışan düğümleri |30075 |HTTPS |Durumu, günlükleri vb. görüntülemek için Web Kullanıcı arabirimi. |
| Batanode |Tüm çalışan düğümleri |30010 |&nbsp; |Veri aktarımı |
| Batanode |Tüm çalışan düğümleri |30020 |'YI |Meta veri işlemleri |
| İkincil süs Yot |Baş düğümler |50090 |HTTP |Süs Code meta verileri için kontrol noktası |

### <a name="yarn-ports"></a>YARN bağlantı noktaları

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| Kaynak Yöneticisi Web Kullanıcı arabirimi |Baş düğümler |8088 |HTTP |Kaynak Yöneticisi için Web Kullanıcı arabirimi |
| Kaynak Yöneticisi Web Kullanıcı arabirimi |Baş düğümler |8090 |HTTPS |Kaynak Yöneticisi için Web Kullanıcı arabirimi |
| Yönetici arabirimi Kaynak Yöneticisi |baş düğümler |8141 |'YI |Uygulama gönderimleri için (Hive, Hive sunucusu, Pig vb.) |
| Kaynak Yöneticisi Zamanlayıcı |baş düğümler |8030 |HTTP |Yönetim arabirimi |
| Kaynak Yöneticisi uygulama arabirimi |baş düğümler |8050 |HTTP |Uygulama Yöneticisi arabiriminin adresi |
| NodeManager |Tüm çalışan düğümleri |30050 |&nbsp; |Kapsayıcı yöneticisinin adresi |
| NodeManager Web Kullanıcı arabirimi |Tüm çalışan düğümleri |30060 |HTTP |Kaynak Yöneticisi arabirimi |
| Zaman çizelgesi adresi |Baş düğümler |10200 |RPC |Zaman çizelgesi hizmeti RPC hizmeti. |
| Zaman çizelgesi Web Kullanıcı arabirimi |Baş düğümler |8188 |HTTP |Zaman çizelgesi hizmeti Web Kullanıcı arabirimi |

### <a name="hive-ports"></a>Hive bağlantı noktaları

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| HiveServer2 |Baş düğümler |10001 |Thrift |Hive 'e bağlanma hizmeti (Thrift/JDBC) |
| Hive Meta Veri Deposu |Baş düğümler |9083 |Thrift |Hive meta verilerine bağlanma hizmeti (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat bağlantı noktaları

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| WebHCat sunucusu |Baş düğümler |30111 |HTTP |HCatalog ve diğer Hadoop hizmetlerinin üstünde Web API 'SI |

### <a name="mapreduce-ports"></a>MapReduce bağlantı noktaları

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| JobHistory |Baş düğümler |19888 |HTTP |MapReduce JobHistory Web Kullanıcı arabirimi |
| JobHistory |Baş düğümler |10020 |&nbsp; |MapReduce JobHistory sunucusu |
| Karıştırılmış Lehandler |&nbsp; |13562 |&nbsp; |Ara eşleme çıkışlarını istek azaltıcının 'e aktarır |

### <a name="oozie"></a>Oozie

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| Oozie sunucusu |Baş düğümler |11000 |HTTP |Oozie hizmeti URL 'SI |
| Oozie sunucusu |Baş düğümler |11001 |HTTP |Oozie Yöneticisi için bağlantı noktası |

### <a name="ambari-metrics"></a>Ambari Ölçümleri

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| Zaman çizelgesi (uygulama geçmişi) |Baş düğümler |6188 |HTTP |Zaman çizelgesi hizmeti Web Kullanıcı arabirimi |
| Zaman çizelgesi (uygulama geçmişi) |Baş düğümler |30200 |RPC |Zaman çizelgesi hizmeti Web Kullanıcı arabirimi |

### <a name="hbase-ports"></a>HBase bağlantı noktaları

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| HMaster |Baş düğümler |16000 |&nbsp; |&nbsp; |
| HMaster Info Web Kullanıcı arabirimi |Baş düğümler |16010 |HTTP |HBase Master Web Kullanıcı arabirimi için bağlantı noktası |
| Bölge sunucusu |Tüm çalışan düğümleri |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |İstemcilerin ZooKeeper 'e bağlanmak için kullandığı bağlantı noktası |

### <a name="kafka-ports"></a>Kafka bağlantı noktaları

| Hizmet | Düğümler | Bağlantı noktası | Protokol | Description |
| --- | --- | --- | --- | --- |
| 'Ndan |Çalışan düğümleri |9092 |Kafka tel Protokolü |İstemci iletişimi için kullanılır |
| &nbsp; |Zookeeper düğümleri |2181 |&nbsp; |İstemcilerin Zookeeper 'e bağlanmak için kullandığı bağlantı noktası |
| REST proxy | Kafka yönetim düğümleri |9400 |HTTPS |[Kafka REST belirtimi](/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Spark bağlantı noktaları

| Hizmet | Düğümler | Bağlantı noktası | Protokol | URL yolu | Description |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift sunucuları |Baş düğümler |10002 |Thrift | &nbsp; | Spark SQL 'e bağlanma hizmeti (Thrift/JDBC) |
| Livy sunucusu | Baş düğümler | 8998 | HTTP | &nbsp; | Deyimleri, işleri ve uygulamaları çalıştırmak için hizmet |
| Jupyter Notebook | Baş düğümler | 8001 | HTTP | &nbsp; | Jupyter Notebook Web sitesi |

Örnekler:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"` . Bu örnekte, `10.0.0.11` Livy hizmetini barındıran baş düğümüne 'un IP adresidir.