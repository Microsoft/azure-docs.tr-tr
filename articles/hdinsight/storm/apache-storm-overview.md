---
title: Apache Storm nedir? - Azure HDInsight
description: Apache Storm, veri akışlarını gerçek zamanlı olarak işlemenize olanak tanır. Azure HDInsight, Azure bulutu üzerinde Storm kümelerini kolayca oluşturmanıza olanak tanır. Visual Studio ile C# kullanarak Storm çözümleri oluşturabilir ve sonra HDInsight Storm kümelerinize dağıtabilirsiniz.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 867f042332457ebc5fdd6b1f10ce7fb636309ba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865341"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Azure HDInsight’ta Apache Storm nedir?

[Apache Storm](https://storm.apache.org/); dağıtılmış, hataya dayanıklı, açık kaynaklı bir hesaplama sistemidir. [Apache Hadoop](../hadoop/apache-hadoop-introduction.md)ile gerçek zamanlı olarak veri akışlarını Işlemek için fırtınası kullanabilirsiniz. Storm çözümleri ayrıca veriler için işleme garantisi sunabilir ve ilk seferinde başarıyla işlenmemiş olan veriler yeniden kullanılabilir.

## <a name="why-use-apache-storm-on-hdinsight"></a>HDInsight üzerinde neden Apache Storm kullanmalıyım?

HDInsight üzerinde Storm aşağıdaki özellikleri sunar:

* __%99 hizmet düzeyi sözleşmesi (SLA) fırtınası çalışma süresi__: HDInsight üzerinde fırtınası, tam sürekli destek ile birlikte gelir. HDInsight üzerinde Storm ayrıca yüzde 99,9 SLA’ya sahiptir. Diğer bir deyişle Microsoft, Storm kümesinin, sürenin en az yüzde 99,9’unda dış bağlantıya sahip olacağını garanti eder. Daha fazla bilgi için bkz. [Azure desteği](https://azure.microsoft.com/support/options/). Ayrıca bkz. [HDInsight Için SLA bilgileri](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) belgesi.

* Oluşturma sırasında veya sonrasında Storm kümesinde betik çalıştırarak kolay özelleştirmeyi destekler. Daha fazla bilgi için bkz. [HDInsight kümelerini betik eylemi kullanarak özelleştirme](../hdinsight-hadoop-customize-cluster-linux.md).

* **Birden fazla dilde çözümler oluşturma**: Storm bileşenlerini Java, C# ve Python gibi dilediğiniz bir dilde yazabilirsiniz.

    * C# topolojisi geliştirme, yönetme ve izleme için HDInsight ile Visual Studio’yu tümleştirir. Daha fazla bilgi için bkz. [Visual Studio için HDInsight Araçlarıyla C# Storm topolojileri geliştirme](apache-storm-develop-csharp-visual-studio-topology.md).

    * Trident Java arabirimini destekler. İletilerin tam olarak bir kez işlenmesini, işlemsel veri deposu kalıcılığını ve sık kullanılan Stream Analytics işlemlerini destekleyen Storm topolojileri oluşturabilirsiniz.

* **Dinamik ölçeklendirme**: Çalışan Storm topolojilerini etkilemeden çalışan düğümleri ekleyebilir veya kaldırabilirsiniz. Ölçeklendirme işlemleri aracılığıyla eklenen yeni düğümlerden yararlanmak için çalışan topolojileri devre dışı bırakıp yeniden etkinleştirin.

* **Birden çok Azure hizmetini kullanarak akış işlem hatları oluşturma**: HDInsight 'ta fırtınası diğer Azure hizmetleriyle tümleştirilir. Event Hubs, SQL veritabanı, Azure depolama ve Azure Data Lake Storage gibi. Azure hizmetleriyle tümleşen örnek bir çözüm için bkz. [HDInsight 'ta Apache Storm Event Hubs olayları işleme](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

Gerçek zamanlı analiz çözümleri için Apache Storm kullanan şirketlerin listesi için, bkz. [Apache Storm kullanan şirketler](https://storm.apache.org/Powered-By.html).

Fırtınası kullanmaya başlamak için bkz. [Azure HDInsight 'ta Apache Storm topolojisi oluşturma ve izleme](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Apache Storm nasıl çalışır?

Fırtınası, tanıdık olabileceğiniz [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)  işleri yerine topolojiler çalıştırır. Storm topolojileri döngüsel olmayan yönlü grafikte (DAG) düzenlenmiş birden fazla bileşenden oluşur. Veriler grafikteki bileşenler arasında akar. Her bileşen bir veya daha fazla veri akışı kullanır ve isteğe bağlı olarak bir veya daha fazla akış yayar. Aşağıdaki diyagram, temel bir sözcük sayısı topolojisindeki bileşenler arasında verilerin nasıl aktığını gösterir:

:::image type="content" source="./media/apache-storm-overview/example-apache-storm-topology-diagram.png" alt-text="Bileşenlerin bir Storm topolojisinde nasıl düzenlendiğini gösteren örnek" border="false":::

* Spout bileşenleri, verileri bir topolojiye getirir. Bu bileşenler topolojiye bir veya daha fazla akış yayar.

* Bolt bileşenleri, spout veya diğer boltlardan yayılan akışları kullanır. Boltlar topolojiye isteğe bağlı olarak akışlar yayabilir. Boltlar ayrıca HDFS, Kafka veya HBase gibi dış hizmetlere veya depolama alanlarına veri yazmaktan sorumludur.

## <a name="reliability"></a>Güvenilirlik

Apache Storm, veri analizi yüzlerce düğüme dağıldığında bile tüm gelen iletilerin her zaman tamamen işleneceğini garanti eder.

Nimbus düğümü Apache Hadoop JobTracker şuna benzer işlevsellik sağlar. Nimbus, Apache ZooKeeper aracılığıyla bir kümedeki diğer düğümlere görevler atar. Zookeeper düğümleri bir küme için koordinasyon sağlar ve çalışan düğümlerinde Nimbus ile gözetmen işlemi arasındaki iletişime yardımcı olur. Bir işleme düğümü devre dışı kalırsa Nimbus düğümü bilgilendirilir ve görevi ve ilişkili verileri başka bir düğüme atar.

Apache Storm kümeleri için varsayılan yapılandırma yalnızca bir Nimbus düğümü içerir. HDInsight üzerindeki Storm iki Nimbus düğümü sağlar. Birincil düğüm başarısız olursa birincil düğüm kurtarılırken Storm kümesi ikincil düğüme geçiş yapar. Aşağıdaki diyagramda HDInsight üzerinde Storm için görev akışı yapılandırması gösterilmektedir:

:::image type="content" source="./media/apache-storm-overview/storm-diagram-nimbus.png" alt-text="Nimbus, zookeeper ve supervisor diyagramı" border="false":::

## <a name="ease-of-use"></a>Kullanım kolaylığı

|Kullanın |Description |
|---|---|
|Secure Shell (SSH) bağlantısı|SSH kullanarak, Internet üzerinden fırtınası kümenizin baş düğümlerine erişebilirsiniz. SSH kullanarak komutları doğrudan kümeniz üzerinde çalıştırabilirsiniz. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).|
|Web bağlantısı|Tüm HDInsight kümeleri, ambarı Web Kullanıcı arabirimi sağlar. Ambari web kullanıcı arabirimini kullanarak kümenizdeki hizmetleri kolayca izleyebilir, yapılandırabilir ve yönetebilirsiniz. Storm kümeleri ayrıca Storm Kullanıcı Arabirimini sağlar. Storm kullanıcı arabirimini kullanarak, çalışan Storm topolojilerini tarayıcınızdan izleyip yönetebilirsiniz. Daha fazla bilgi için bkz. [Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight 'ı yönetme](../hdinsight-hadoop-manage-ambari.md) ve [Apache Storm UI belgelerini kullanarak izleme ve yönetme](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) .|
|Azure PowerShell ve Azure CLı|Hem PowerShell hem de Azure CLı, HDInsight ve diğer Azure hizmetleriyle çalışmak için istemci sisteminizden kullanabileceğiniz komut satırı yardımcı programları sağlar.|
|Visual Studio tümleştirmesi|Visual Studio için Azure Data Lake Araçları, SCP.NET çerçevesini kullanarak C# fırtınası topolojileri oluşturmaya yönelik proje şablonları içerir. Data Lake Araçları ayrıca HDInsight üzerinde Storm ile çözümleri dağıtma, izleme ve yönetmeye yönelik araçlar sağlar. Daha fazla bilgi için bkz. [Visual Studio için HDInsight Araçlarıyla C# Storm topolojileri geliştirme](apache-storm-develop-csharp-visual-studio-topology.md).|

## <a name="integration-with-other-azure-services"></a>Diğer Azure hizmetleriyle tümleştirme

* __Azure Data Lake Storage__: bkz. [hdınsight üzerinde Apache Storm Azure Data Lake Storage kullanma](apache-storm-write-data-lake-store.md).

* __Event Hubs__: Event Hubs’ı bir Storm kümesi ile kullanma örneği için aşağıdaki örneklere bakın:

    * [HDInsight üzerinde Apache Storm ile Azure Event Hubs olayları işleme (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [HDInsight üzerinde Apache Storm ile Azure Event Hubs olayları işleme (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Veritabanı__, __Cosmos DB__, __Event Hubs__ ve __HBase__: Visual Studio için Data Lake Araçlarına şablon örnekleri eklenmiştir. Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Storm Için C# topolojisi geliştirme](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="apache-storm-use-cases"></a>Apache Storm kullanım örnekleri

HDInsight üzerinde Storm kullanabileceğiniz bazı yaygın senaryolar aşağıda verilmiştir:

* Nesnelerin İnterneti (IoT)
* Sahtekarlık algılama
* Sosyal analiz
* Ayıklama, dönüşüm ve yükleme (ETL)
* Ağ izleme
* Arayın
* Mobil katılım

Gerçek zamanlı senaryolar hakkında daha fazla bilgi için bkz. [şirketler Apache Storm belge kullanma](https://storm.apache.org/Powered-By.html) .

## <a name="development"></a>Geliştirme

.NET geliştiricileri, Visual Studio için Data Lake Araçları kullanarak C# dilinde topolojiler tasarlayıp uygulayabilir. Ayrıca Java ve C# bileşenlerini kullanan karma topolojiler de oluşturabilirsiniz. Daha fazla bilgi için bkz. [Visual Studio kullanarak HDInsight üzerinde Apache Storm için C# topolojileri geliştirme](apache-storm-develop-csharp-visual-studio-topology.md).

Ayrıca seçtiğiniz IDE’yi kullanarak Java çözümleri geliştirebilirsiniz. Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Storm Için Java topolojileri geliştirme](apache-storm-develop-java-topology.md).

Storm bileşenleri geliştirmek için Python da kullanılabilir. Daha fazla bilgi için bkz. [HDInsight 'Ta Python kullanarak Apache Storm topolojileri geliştirme](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Yaygın geliştirme desenleri

### <a name="guaranteed-message-processing"></a>Garantili ileti işleme

Apache Storm farklı düzeylerde garantili ileti işleme sağlayabilir. Örneğin, temel bir fırtınası uygulaması en az bir kez işlemeyi garanti eder ve Trident tam bir kez işlemeyi garanti edebilir. Bkz. apache.org adresindeki [veri Işleme garantisi](https://storm.apache.org/about/guarantees-data-processing.html) .

### <a name="ibasicbolt"></a>IBasicBolt

Bir giriş tanımlama grubunu okuma, sıfır veya daha fazla tanımlama grubu yayma ve ardından yürütme yönteminin sonunda giriş tanımlama alanının hemen onaylanmasının düzeni yaygındır. Storm bu deseni otomatik hale getirmek için [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) arabirimini kullanır.

### <a name="joins"></a>Birleştirmeler

Uygulamalar arasına veri akışları nasıl katılır? Örneğin, birden çok akıştan her bir tanımlama grubunu yeni bir akışta birleştirebilirsiniz veya yalnızca belirli bir pencere için tanımlama grubu toplu işlerini birleştirebilirsiniz. Her iki durumda da katılım, [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) kullanılarak gerçekleştirilebilir. Alan gruplandırma, tanımlama gruplarının boltlara nasıl yönlendirileceğini tanımlamanın bir yoludur.

Aşağıdaki Java örneğinde fieldsGrouping, "1", "2" ve "3" bileşenlerinden kaynaklanan tanımlama gruplarını MyJoiner boltuna yönlendirmek için kullanılır:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Toplu işler

Apache Storm "değer çizgisi tanımlama grubu" olarak bilinen bir iç zamanlama mekanizmasını sağlar. Değer çizgisi tanımlama grubunun topolojiniz içinde ne sıklıkta yayıldığını ayarlayabilirsiniz.

Bir C# bileşeninden değer çizgisi tanımlama grubu kullanımı örneği için bkz. [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Önbellekler

Bellek içi önbelleğe alma, sık kullanılan varlıkları bellekte tuttuğu için çoğunlukla işlemeyi hızlandırmaya yönelik bir mekanizma olarak kullanılır. Bir topoloji birden fazla düğüme ve her bir düğüm içindeki birden fazla işleme dağıtıldığı için, [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) kullanmayı düşünmeniz gerekir. Önbellek araması için kullanılan alanları içeren tanımlama gruplarının her zaman aynı işleme yönlendirildiğinden emin olmak için `fieldsGrouping` kullanın. Bu gruplandırma işlevi, süreçler arasında önbellek girişlerinin yinelenmesini önler.

### <a name="stream-top-n"></a>"İlk N" akış

Topolojiniz bir "ilk N" değeri hesaplamaya bağlıysa, ilk N değeri paralel olarak hesaplayın. Ardından bu hesaplamaların çıktısını genel bir değerde birleştirmeniz gerekir. Bu işlem, alanı paralel işleme için yönlendirmek üzere [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) kullanılarak yapılabilir. Ardından, ilk n değerini genel olarak belirleyen bir bolta yönlendirebilirsiniz.

İlk N değeri hesaplama örneği için [Rollingtopwords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) örneğine bakın.

## <a name="logging"></a>Günlüğe Kaydetme

Fırtınası, bilgileri günlüğe kaydetmek için Apache Log4J 2 kullanır. Varsayılan olarak, büyük miktarlarda veriler günlüğe kaydedilir ve bilgilerin sıralanması zor olabilir. Günlüğe kaydetme davranışını denetlemek üzere Storm topolojinizin bir parçası olarak günlük yapılandırma dosyası ekleyebilirsiniz.

Günlüğün nasıl yapılandırılacağını gösteren örnek bir topoloji için HDInsight üzerinde Storm için [Java tabanlı WordCount](apache-storm-develop-java-topology.md) örneğine bakın.

## <a name="next-steps"></a>Sonraki adımlar

HDInsight üzerinde Apache Storm ile gerçek zamanlı analiz çözümleri hakkında daha fazla bilgi edinin:

* [Azure HDInsight 'ta Apache Storm topolojisi oluşturma ve izleme](apache-storm-quickstart.md)
* [HDInsight üzerinde Apache Storm için örnek topolojiler](apache-storm-example-topology.md)
