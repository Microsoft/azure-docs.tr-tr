---
title: Azure HDInsight 'ta örnek Apache Storm topolojileri
description: HDInsight üzerinde temel C# ve Java topolojileri dahil Apache Storm oluşturulan ve test edilen bir örnek fırtınası topolojisi listesi ve Event Hubs çalışma.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 8dabfec18cb904fa72518428220991b817b53529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928916"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>HDInsight üzerinde Apache Storm için topolojiler ve bileşenler Apache Storm örnek

Aşağıda, HDInsight üzerinde [Apache Storm](https://storm.apache.org/) kullanılmak üzere Microsoft tarafından oluşturulan ve tutulan örneklerin bir listesi verilmiştir. Bu örnekler, Event Hubs, Cosmos DB, SQL veritabanı, HDInsight 'ta [Apache HBase](https://hbase.apache.org/) ve Azure depolama gibi Azure hizmetleriyle çalışmak üzere temel C# ve Java topolojileri oluşturmaktan çok çeşitli konuları kapsar. Bazı örnekler Ayrıca, Azure olmayan ve hatta SignalR ve Socket.IO gibi Microsoft dışı teknolojilerle nasıl çalışabileceğinizi gösterir.

| Description | Gösteriler | Dil/çerçeve |
|:--- |:--- |:--- |
| [Apache Storm Azure Data Lake Storage yazma](apache-storm-write-data-lake-store.md) |Azure Data Lake Storage yazma |Java |
| [Olay Hub 'ı ve Rulut kaynağı](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Olay Hub 'ı ve cıvata kaynağı |Java |
| [HDInsight üzerinde Apache Storm için Java tabanlı topolojiler geliştirme][5797064f] |Maven |Java |
| [Visual Studio kullanarak HDInsight üzerinde Apache Storm için C# topolojileri geliştirme][16fce2d1] |Visual Studio için HDInsight araçları |C#, Java |
| [HDInsight üzerinde Apache Storm ile Azure Event Hubs olayları işleme (C#)][844d1d81] |Event Hubs |C# ve Java |
| [HDInsight üzerinde Storm ile Azure Event Hubs’tan olay işleme (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) |Event Hubs |Java |
| [HDInsight üzerinde Apache Storm kullanarak Event Hubs araç algılayıcısı verilerini işleme][246ee964] |Event Hubs, Cosmos DB, Azure Depolama Blobu (ıLB) |C#, Java |
| [HDInsight üzerinde Apache Storm kullanarak Azure Event Hubs 'den Apache HBase 'e ayıklama, dönüştürme ve yükleme (ETL)][b4b68194] |Event Hubs, HBase |C# |
| [HDInsight üzerinde Apache Storm Azure hizmetleriyle çalışmak için şablon C# fırtınası topolojisi projesi][ce0c02a2] |Event Hubs, Cosmos DB, SQL veritabanı, HBase, SignalR |C#, Java |
| [HDInsight üzerinde Apache Storm kullanarak Azure Event Hubs okuma için ölçeklenebilirlik kıyaslamalar][d6c540e3] |İleti işleme, Event Hubs, SQL veritabanı |C#, Java |
| [HDInsight üzerinde Apache Storm ile Apache Kafka kullanma](../hdinsight-apache-storm-with-kafka.md) | Apache Kafka okuma ve yazma Apache Storm | Java |

> [!WARNING]  
> Bu listedeki C# örnekleri ilk olarak Windows tabanlı HDInsight ile oluşturulup test edilmiştir ve Linux tabanlı HDInsight kümeleriyle doğru çalışmayabilir. Linux tabanlı kümeler .NET kodu çalıştırmak için mono kullanır ve örnekte kullanılan çerçeveler ve paketlerle uyumluluk sorunlarına sahip olabilir.
>
> Linux, HDInsight sürüm 3,4 veya sonraki sürümlerde kullanılan tek işletim sistemidir.

## <a name="python-only"></a>Yalnızca Python

Flox topolojisi olan Python bileşenleri örneği için bkz. [HDInsight üzerinde Apache Storm Python kullanma](apache-storm-develop-python-topology.md) .

## <a name="next-steps"></a>Sonraki Adımlar

* [Azure HDInsight 'ta Apache Storm topolojisi oluşturma ve izleme](./apache-storm-quickstart.md)
* [HDInsight üzerinde Apache Storm ile Apache Storm topolojilerini dağıtmayı ve yönetmeyi öğrenin][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Web tabanlı Apache Storm panosu ve fırtınası Kullanıcı arabirimini veya Visual Studio için HDInsight araçlarını kullanarak topolojileri dağıtmayı ve yönetmeyi öğrenin."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Visual Studio için HDInsight araçlarını kullanarak C# fırtınası topolojileri oluşturmayı öğrenin."
[5797064f]:apache-storm-develop-java-topology.md "Temel bir WORDCOUNT topolojisi oluşturarak Maven kullanarak Java 'da bir fırtınası topolojisi oluşturmayı öğrenin."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "HDInsight 'ta fırtınası ile Azure Event Hubs 'dan veri okuma ve yazma hakkında bilgi edinin."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Azure Event Hubs iletileri okumak için bir fırtınası topolojisi kullanmayı öğrenin, Azure depolama 'ya veri başvurmak ve verileri kaydetmek için Azure Cosmos DB belgeleri okuyun."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Azure Event Hubs okurken ve HDInsight üzerinde Apache Storm kullanarak SQL veritabanı 'na depolarken üretilen iş verimini gösteren birkaç topoloji."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Azure Event Hubs 'den veri okumayı, verileri & dönüştürmeyi ve sonra HDInsight 'ta HBase 'ye depolamayı öğrenin."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Bu proje, Event Hubs, Cosmos DB ve SQL veritabanı gibi çeşitli Azure hizmetleriyle etkileşim kurmak için spomalar, cıvatları ve topolojilerle ilgili şablonlar içerir."
