---
title: Apache Kafka ile akış Apache Spark-Azure HDInsight
description: DStreams kullanarak Apache Kafka veri akışını veya bu verileri dışarı aktarmak için Apache Spark nasıl kullanacağınızı öğrenin. Bu örnekte, HDInsight üzerinde Spark 'tan bir Jupyter Notebook kullanarak veri akışı oluşturursunuz.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 6ef11e9c7907f57b3b8de0a042e1035bce638cf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863284"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>HDInsight üzerinde Apache Kafka Apache Spark streaming (DStream) örneği

[DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html)kullanarak hdınsight 'ta [Apache Kafka](https://kafka.apache.org/) veri akışını veya bu verileri dışarı aktarmak için [Apache Spark](https://spark.apache.org/) nasıl kullanacağınızı öğrenin. Bu örnek, Spark kümesinde çalışan bir [Jupyter Notebook](https://jupyter.org/) kullanır.

> [!NOTE]  
> Bu belgede yer alan adımlar hem HDInsight üzerinde Spark hem de HDInsight kümesinde Kafka içeren bir Azure kaynak grubu oluşturur. Bu kümelerin her ikisi de Spark kümesinin Kafka kümesiyle doğrudan iletişim kurmasına olanak tanıyan bir Azure Sanal Ağı içinde bulunur.
>
> Bu belgedeki adımları tamamladığınızda, aşırı ücretlerden kaçınmak için kümeleri silmeyi unutmayın.

> [!IMPORTANT]  
> Bu örnek, daha eski bir Spark akış teknolojisi olan DStreams kullanır. Daha yeni Spark akış özelliklerini kullanan bir örnek için, Apache Kafka belgeyle [Spark yapısal akışı](hdinsight-apache-kafka-spark-structured-streaming.md) bölümüne bakın.

## <a name="create-the-clusters"></a>Kümeleri oluşturma

HDInsight üzerinde Apache Kafka, genel İnternet üzerinden Kafka aracıları için erişim sağlamaz. Kafka ile iletişim kuran her şey, Kafka kümesindeki düğümlerle aynı Azure sanal ağında olmalıdır. Bu örnekte, hem Kafka hem de Spark kümeleri bir Azure sanal ağında bulunur. Aşağıdaki diyagramda, kümeler arasında iletişimin nasıl akagösterdiği gösterilmektedir:

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png" alt-text="Bir Azure sanal ağında Spark ve Kafka kümeleri diyagramı" border="false":::

> [!NOTE]  
> Kafka kendisi, sanal ağ içindeki iletişim ile sınırlı olsa da, kümedeki SSH ve ambarı gibi diğer hizmetlere internet üzerinden erişilebilir. HDInsight üzerinde kullanılabilir olan genel bağlantı noktaları hakkında daha fazla bilgi için bkz. [HDInsight Tarafından Kullanılan Bağlantı Noktaları ve URI’ler](hdinsight-hadoop-port-settings-for-services.md).

Azure sanal ağını, Kafka ve Spark kümelerini el ile oluşturabileceğiniz gibi, Azure Resource Manager şablonu kullanmak daha kolay olur. Azure aboneliğinize Azure sanal ağını, Kafka ve Spark kümelerini dağıtmak için aşağıdaki adımları kullanın.

1. Aşağıdaki düğmeyi kullanarak Azure'da oturum açın ve şablonu Azure portalında açın.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

   Azure Resource Manager şablonu konumunda bulunur **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

   > [!WARNING]
   > HDInsight üzerinde Kafka'yı kullanabilmeniz için kümenizin en az üç çalışan düğümü içermesi gerekir. Bu şablon, üç çalışan düğümü içeren bir Kafka kümesi oluşturur.

   Bu şablon hem Kafka hem de Spark için bir HDInsight 3,6 kümesi oluşturur.

1. **Özel dağıtım** bölümündeki girişleri doldurmak için aşağıdaki bilgileri kullanın:

   |Özellik |Değer |
   |---|---|
   |Kaynak grubu|Bir grup oluşturun veya var olan bir grubu seçin.|
   |Konum|Coğrafi olarak size yakın bir konum seçin.|
   |Taban küme adı|Bu değer Spark ve Kafka kümelerinin temel adı olarak kullanılır. Örneğin, **hdistreaKafka** , __Spark-Hdistreate__ adlı bir Spark kümesi ve **Kafka-Hdisantadlı** bir kümesi oluşturur.|
   |Küme Oturum Açma Kullanıcı Adı|Spark ve Kafka kümeleri için Yönetici Kullanıcı adı.|
   |Küme Oturum Açma Parolası|Spark ve Kafka kümeleri için Yönetici Kullanıcı parolası.|
   |SSH Kullanıcı Adı|Spark ve Kafka kümeleri için oluşturulacak SSH kullanıcısı.|
   |SSH Parolası|Spark ve Kafka kümeleri için SSH kullanıcısının parolası.|

   :::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png" alt-text="HDInsight özel dağıtım parametreleri":::

1. **Hüküm ve Koşullar**’ı okuyun ve ardından **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**’u seçin.

1. Son olarak, **satın al**' ı seçin. Kümelerin oluşturulması yaklaşık 20 dakika sürer.

Kaynaklar oluşturulduktan sonra bir Özet sayfası görünür.

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png" alt-text="VNET ve kümeler için kaynak grubu Özeti":::

> [!IMPORTANT]  
> HDInsight kümelerinin adlarının **Spark-BASENAME** ve **Kafka-BASENAME** olduğunu ve burada BASENAME, şablona verdiğiniz addır dikkat edin. Bu adları, kümelere bağlanırken sonraki adımlarda kullanırsınız.

## <a name="use-the-notebooks"></a>Not defterlerini kullanma

Bu belgede açıklanan örnek için kodu adresinde bulabilirsiniz [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) .

## <a name="delete-the-cluster"></a>Küme silme

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Bu belgedeki adımlar aynı Azure Kaynak grubunda her iki küme de oluşturduğundan, Azure portal kaynak grubunu silebilirsiniz. Grup silindiğinde, bu belge, Azure sanal ağı ve kümeler tarafından kullanılan depolama hesabı tarafından oluşturulan tüm kaynaklar kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekte, Kafka 'e okumak ve yazmak için Spark 'ı nasıl kullanacağınızı öğrendiniz. Kafka ile çalışmanın diğer yollarını saptamak için aşağıdaki bağlantıları kullanın:

* [HDInsight üzerinde Apache Kafka kullanmaya başlama](kafka/apache-kafka-get-started.md)
* [HDInsight üzerinde Apache Kafka çoğaltmasını oluşturmak için MirrorMaker 'ı kullanma](kafka/apache-kafka-mirroring.md)
* [HDInsight üzerinde Apache Kafka ile Apache Storm kullanma](hdinsight-apache-storm-with-kafka.md)
