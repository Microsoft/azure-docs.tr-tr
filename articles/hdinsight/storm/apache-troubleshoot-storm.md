---
title: Azure HDInsight kullanarak fırtınası sorunlarını giderme
description: Azure HDInsight ile Apache Storm kullanma hakkında sık sorulan sorulara yanıtlar alın.
keywords: Azure HDInsight, fırtınası, SSS, sorun giderme kılavuzu, yaygın sorunlar
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: e2cc9dd81a0bbefa5cf37facb6067bda07117eaf
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903714"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Azure HDInsight 'ı kullanarak Apache Storm sorunlarını giderme

[Apache ambarı](https://ambari.apache.org/)'nda [Apache Storm](https://storm.apache.org/) yükleri ile çalışmaya yönelik en popüler sorunlar ve çözümleri hakkında bilgi edinin.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Bir kümedeki fırtınası Kullanıcı arabirimine erişmek Nasıl yaparım? mı?

Bir tarayıcıdan fırtınası Kullanıcı arabirimine erişmek için iki seçeneğiniz vardır:

### <a name="apache-ambari-ui"></a>Apache ambarı Kullanıcı arabirimi

1. Ambarı panosuna gidin.
2. Hizmetler listesinde, **fırtınası**' yi seçin.
3. **Hızlı bağlantılar** menüsünde, **fırtınası Kullanıcı arabirimi**' ni seçin.

### <a name="direct-link"></a>Doğrudan bağlantı

Aşağıdaki URL 'de, fırtınası Kullanıcı arabirimine erişebilirsiniz:

`https://<cluster DNS name>/stormui`

Örnek: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Nasıl yaparım?, bir topolojiden diğerine fırtınası olay merkezi Spout denetim noktası bilgilerini aktarmaya mi?

HDInsight fırtınası Olay Hub Spout. jar dosyasını kullanarak Azure Event Hubs okuyan topolojiler geliştirirken, yeni bir kümede aynı ada sahip bir topoloji dağıtmanız gerekir. Ancak, eski kümede [Apache ZooKeeper](https://zookeeper.apache.org/) için kaydedilmiş denetim noktası verilerini saklamanız gerekir.

### <a name="where-checkpoint-data-is-stored"></a>Denetim noktası verilerinin depolandığı yer

Uzaklıklara yönelik denetim noktası verileri, ZooKeeper 'de Olay Hub 'ı tarafından iki kök yolda saklanır:

- İşlem dışı Spout kontrol noktaları `/eventhubspout`depolanır.

- İşlem Spout denetim noktası verileri `/transactional`depolanır.

### <a name="how-to-restore"></a>Geri yükleme

ZooKeeper 'dan verileri dışarı aktarmak için kullandığınız betikleri ve kitaplıkları almak ve sonra verileri yeni bir adla ZooKeeper 'e geri aktarmak için bkz. [HDInsight fırtınası örnekleri](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

LIB klasörü, dışarı aktarma/içeri aktarma işlemi için uygulamayı içeren. jar dosyalarına sahiptir. Bash klasörü, Eski kümedeki ZooKeeper sunucusundan verileri nasıl dışarı aktarabileceğinizi gösteren örnek bir betiğe sahiptir ve sonra bunu yeni kümedeki ZooKeeper sunucusuna geri aktarabilirsiniz.

Dışarı aktarmak ve ardından verileri içeri aktarmak için ZooKeeper düğümlerinden [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) betiğini çalıştırın. Betiği doğru Hortonçalışmalar veri platformu (HDP) sürümüne güncelleştirin. (Bu betikleri HDInsight 'ta genel hale getirmek için çalışıyoruz. Genel betikler, Kullanıcı tarafından değişiklik yapılmadan kümedeki herhangi bir düğümden çalıştırılabilir.)

Dışarı aktar komutu, ayarladığınız bir konuma (Azure Blob Storage veya Azure Data Lake Storage) meta verileri bir Apache Hadoop Dağıtılmış Dosya Sistemi (bir) yoluna yazar.

### <a name="examples"></a>Örnekler

#### <a name="export-offset-metadata"></a>Dışarı aktarma boşluğu meta verileri

1. Denetim noktası kaydırması gereken kümedeki ZooKeeper kümesine gitmek için SSH kullanın.
2. ZooKeeper yol verilerini `/stormmetadta/zkdata` 00:00 yoluna dışarı aktarmak için aşağıdaki komutu çalıştırın (HDP sürüm dizesini güncelleştirdikten sonra):

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>İçeri aktarma meta verileri

1. Denetim noktası sapmasını içeri aktarılması gereken kümedeki ZooKeeper kümesine gitmek için SSH kullanın.
2. ZooKeeper `/stormmetadata/zkdata` Path 'den hedef kümedeki sunucusuna ZooKeeper sapmasını verileri aktarmak için aşağıdaki komutu çalıştırın (HDP sürüm dizesini güncelleştirdikten sonra):

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Topolojilerinin başlangıçtan itibaren veya kullanıcının seçtiği bir zaman damgasından veri işlemeye başlayabilmesi için konum meta verilerini silin

1. Denetim noktası kaydırması gereken kümedeki ZooKeeper kümesine gitmek için SSH kullanın.
2. Geçerli kümedeki tüm ZooKeeper sapmayı verilerini silmek için aşağıdaki komutu çalıştırın (HDP sürüm dizesini güncelleştirdikten sonra):

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Nasıl yaparım? bir kümedeki fırtınası ikililerini bulun mi?

Geçerli HDP yığınının fırtınası ikilileri `/usr/hdp/current/storm-client`. Konum, hem baş düğümleri hem de çalışan düğümleri için aynıdır.

/Usr/HDP içindeki belirli HDD sürümleri için birden çok ikili olabilir (örneğin, `/usr/hdp/2.5.0.1233/storm`). `/usr/hdp/current/storm-client` klasörü, kümede çalışan en son sürüme bağlı değildir.

Daha fazla bilgi için bkz. SSH ve [Apache Storm](https://storm.apache.org/) [kullanarak HDInsight kümesine bağlanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) .

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Bir fırtınası kümesinin dağıtım topolojisini belirleme Nasıl yaparım? mı?

İlk olarak, HDInsight fırtınası ile yüklenen tüm bileşenleri belirlersiniz. Bir fırtınası kümesi dört düğüm kategorisinden oluşur:

* Ağ Geçidi düğümleri
* Baş düğümler
* ZooKeeper düğümleri
* Çalışan düğümleri

### <a name="gateway-nodes"></a>Ağ Geçidi düğümleri

Bir ağ geçidi düğümü, etkin bir ambarı yönetim hizmetine genel erişim sağlayan bir ağ geçidi ve ters proxy hizmetidir. Ayrıca, ambarı öncü seçimi de işler.

### <a name="head-nodes"></a>Baş düğümler

Fırtınası başlığı düğümleri aşağıdaki hizmetleri çalıştırır:
* Nimbus
* Ambarı sunucusu
* Ambarı ölçüm sunucusu
* Ambarı ölçüm toplayıcısı
 
### <a name="zookeeper-nodes"></a>ZooKeeper düğümleri

HDInsight üç düğümlü bir ZooKeeper çekirdekle gelir. Çekirdek boyutu sabittir ve yeniden yapılandırılamaz.

Kümedeki fırtınası Hizmetleri, ZooKeeper çekirdeğini otomatik olarak kullanacak şekilde yapılandırılır.

### <a name="worker-nodes"></a>Çalışan düğümleri

Fırtınası çalışan düğümleri aşağıdaki hizmetleri çalıştırın:
* Gözetmen
* Çalışan topolojiler için çalışan Java sanal makineleri (JVM 'Ler)
* Ambarı Aracısı

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Geliştirme için fırtınası Event hub Spout ikililerini bulun Nasıl yaparım?.

Topolojiniz ile fırtınası Olay Hub Spout. jar dosyalarını kullanma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın.

### <a name="java-based-topology"></a>Java tabanlı topoloji

[HDInsight üzerinde Apache Storm ile Azure Event Hubs olayları işleme (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#tabanlı topoloji (tek başına HDInsight 3.4 + Linux fırtınası kümelerinde mono)

[HDInsight 'ta Apache Storm ile Azure Event Hubs olayları işleme (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>HDInsight 3.5 + Linux fırtınası kümeleri için en son Apache Storm Event hub Spout ikilileri

HDInsight 3.5 + Linux fırtınası kümeleriyle birlikte çalışarak en son fırtınası Olay Hub 'ı nasıl kullanacağınızı öğrenmek için bkz. [MVN-repo Benioku dosyası](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Kaynak kodu örnekleri

Azure HDInsight kümesinde Apache Storm topolojisi (Java 'da yazılmış) kullanarak Azure Event hub 'dan okuma ve yazma [örneklerine](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) bakın.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Nasıl yaparım?, Log4J 2 yapılandırma dosyalarını kümeler üzerinde bulamıyor musunuz?

Fırtınası Hizmetleri için [Apache Log4J 2](https://logging.apache.org/log4j/2.x/) yapılandırma dosyalarını belirlemek için.

### <a name="on-head-nodes"></a>Baş düğümlerde

Nimbus Log4J yapılandırması `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`okundu.

### <a name="on-worker-nodes"></a>Çalışan düğümlerinde

Gözetmen Log4J yapılandırması `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`' den okundu.

Çalışan Log4J yapılandırma dosyası `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`okundu.

Örnekler: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Öncü özel durum değil

Bir topoloji gönderirken Kullanıcı şuna benzer bir hata iletisi alabilir: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

Bu sorunu çözmek için, kullanıcıların düğümlerin yeniden başlatılması/yeniden başlatılması için bir bilet dosyası erişmesi gerekebilir. Daha fazla bilgi için bkz. [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

- [@AzureSupport](https://twitter.com/azuresupport) ile bağlanma-müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabı. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

- Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik Yönetimi’ne ve faturalandırma desteğine erişim Microsoft Azure aboneliğinize dahildir, Teknik Destek ise herhangi bir [Azure Destek Planı](https://azure.microsoft.com/support/plans/) üzerinden sağlanır.
