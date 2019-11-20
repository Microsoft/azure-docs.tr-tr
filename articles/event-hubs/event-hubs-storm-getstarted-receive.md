---
title: 'Hızlı başlangıç: Apache Storm Azure Event Hubs kullanarak olay alma'
description: 'Hızlı başlangıç: Bu makalede, Azure Event Hubs Apache Storm kullanarak nasıl olay alınacağı hakkında bilgi sağlanır.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 90293da07d3a7ef1c32e5f82d35198d4ffa536b1
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717617"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Hızlı başlangıç: Event Hubs Apache Storm kullanarak olay alma

[Apache Storm](https://storm.incubator.apache.org) , sınırsız miktarda veri akışının güvenilir işlemesini kolaylaştıran dağıtılmış bir gerçek zamanlı hesaplama sistemidir. Bu bölümde, Event Hubs olayları almak için Azure Event Hubs fırtınası Spout kullanımı gösterilmektedir. Apache Storm kullanarak, olayları farklı düğümlerde barındırılan birden çok işlem arasında ayırabilirsiniz. Event Hubs tümleştirme, fırtınası Zookeeper yüklemesi kullanarak ilerleme durumunu saydam bir şekilde işaretleyerek, kalıcı denetim noktaları ve Event Hubs paralel alma işlemlerini yönetebilir.

Event Hubs alma desenleri hakkında daha fazla bilgi için bkz. [Event Hubs genel bakış][Event Hubs overview].

## <a name="prerequisites"></a>Ön koşullar
Hızlı başlangıç ile başlamadan önce **bir Event Hubs ad alanı ve bir olay hub 'ı oluşturun**. Event Hubs türünde bir ad alanı oluşturmak ve uygulamanızın Olay Hub 'ı ile iletişim kurması için gereken yönetim kimlik bilgilerini almak için [Azure Portal](https://portal.azure.com) kullanın. Bir ad alanı ve Olay Hub 'ı oluşturmak için [Bu makaledeki](event-hubs-create.md)yordamı izleyin. 

## <a name="create-project-and-add-code"></a>Proje oluşturma ve kod ekleme

Bu öğretici, zaten kullanılabilir Event Hubs Spout ile birlikte gelen bir [HDInsight fırtınası][HDInsight Storm] yüklemesi kullanır.

1. Yeni bir HDInsight kümesi oluşturmak ve Uzak Masaüstü aracılığıyla buna bağlanmak için [HDInsight fırtınası-başlangıç](../hdinsight/storm/apache-storm-overview.md) yordamını izleyin.
2. `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` dosyasını yerel geliştirme ortamınıza kopyalayın. Bu,-fırtınası-Spout olaylarını içerir.
3. Paketi yerel Maven deposuna yüklemek için aşağıdaki komutu kullanın. Bu, daha sonraki bir adımda bunu, fırtınası projesine bir başvuru olarak eklemenize olanak sağlar.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Çakışan Küreler, yeni bir Maven projesi oluşturun ( **Dosya**, sonra **Yeni**ve **Proje**).
   
    ![Dosya-> Yeni > projesi][12]
5. **Varsayılan çalışma alanı konumunu kullan**öğesini seçin ve ardından **İleri** ' ye tıklayın
6. **Maven-arşiv ETYPE-hızlı başlangıç** arşiv ETYPE ' ı seçin ve **İleri** ' ye tıklayın.
7. Bir **GroupID** ve **ArtifactId**yerleştirip **son** ' a tıklayın.
8. **Pod. xml**dosyasında, `<dependency>` düğümüne aşağıdaki bağımlılıkları ekleyin.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. **Src** klasöründe, **config. Properties** adlı bir dosya oluşturun ve `receive rule key` ve `event hub name` değerlerini değiştirerek aşağıdaki içeriği kopyalayın:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    **Eventhub. alıcının. kredilerin** değeri, bu nesneleri bir fırtınası işlem hattına serbest bırakmadan önce toplu olarak kaç olay için toplu olarak belirler. Basitlik sağlamak için bu örnek, bu değeri 10 olarak ayarlar. Üretimde, genellikle daha yüksek değerlere ayarlanmalıdır; Örneğin, 1024.
10. Aşağıdaki kodla **Loggerrulname** adlı yeni bir sınıf oluşturun:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Bu fırtınası, alınan olayların içeriğini günlüğe kaydeder. Bu, tanımlama gruplarını bir depolama hizmetinde depolamak için kolayca genişletilebilir. [Olay Hub 'ı ile HDInsight fırtınası örneği] , verileri Azure depolama 'ya ve Power BI depolamak için aynı yaklaşımı kullanır.
11. Aşağıdaki kodla **Logtopology** adlı bir sınıf oluşturun:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Bu sınıf, yeni bir Event Hubs Spout oluşturur ve bu, yapılandırma dosyasındaki özellikleri kullanarak başlatır. Bu örnek, bu olay hub 'ında izin verilen en büyük paralelliği kullanmak için, Olay Hub 'ındaki bölüm sayısı olarak çok sayıda Spout görevi oluşturduğunu unutmayın.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs’a genel bakış][Event Hubs overview]
* [Olay Hub’ı oluşturma](event-hubs-create.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Olay Hub 'ı ile HDInsight fırtınası örneği]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
