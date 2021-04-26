---
title: Cosmos DB Apache Spark & Apache Kafka-Azure HDInsight
description: Apache Kafka verileri okumak için Apache Spark yapısal akışı kullanmayı ve sonra Azure Cosmos DB nasıl depolayacağınızı öğrenin. Bu örnekte, HDInsight üzerinde Spark 'tan bir Jupyter Notebook kullanarak veri akışı oluşturursunuz.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/18/2019
ms.openlocfilehash: d78b629e90903c58b98de86f425f0c1225d90997
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867058"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Apache Kafka ve Azure Cosmos DB ile yapılandırılmış Apache Spark akışı kullanın

Azure HDInsight 'ta [Apache Kafka](https://kafka.apache.org/) verileri okumak için [Apache Spark](https://spark.apache.org/) [yapısal akışı](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) kullanmayı ve sonra verileri Azure Cosmos DB ' ye depolamayı öğrenin.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) , genel olarak dağıtılmış, çok modelli bir veritabanıdır. Bu örnek, bir SQL API veritabanı modeli kullanır. Daha fazla bilgi için bkz. [Azure Cosmos DB belgeye hoş geldiniz](../cosmos-db/introduction.md) .

Spark yapılandırılmış akışı, Spark SQL üzerinde yerleşik bir akış işleme altyapısıdır. Bu altyapıyı kullanarak, statik veriler üzerinde toplu hesaplamayla aynı şekilde akış hesaplamalarını ifade edebilirsiniz. Yapılandırılmış akış hakkında daha fazla bilgi için Apache.org adresindeki [yapılandırılmış akış programlama kılavuzuna](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) bakın.

> [!IMPORTANT]  
> Bu örnek, HDInsight 3,6 üzerinde Spark 2,2 kullandı.
>
> Bu belgede yer alan adımlar hem HDInsight üzerinde Spark hem de HDInsight kümesinde Kafka içeren bir Azure kaynak grubu oluşturur. Bu kümelerin her ikisi de Spark kümesinin Kafka kümesiyle doğrudan iletişim kurmasına olanak tanıyan bir Azure Sanal Ağı içinde bulunur.
>
> Bu belgedeki adımları tamamladığınızda, aşırı ücretlerden kaçınmak için kümeleri silmeyi unutmayın.

## <a name="create-the-clusters"></a>Kümeleri oluşturma

HDInsight üzerinde Apache Kafka, genel İnternet üzerinden Kafka aracıları için erişim sağlamaz. Kafka ile iletişim kuran her şey, Kafka kümesindeki düğümlerle aynı Azure sanal ağında olmalıdır. Bu örnekte, hem Kafka hem de Spark kümeleri bir Azure sanal ağında bulunur. Aşağıdaki diyagramda, kümeler arasında iletişimin nasıl akagösterdiği gösterilmektedir:

:::image type="content" source="./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png" alt-text="Bir Azure sanal ağında Spark ve Kafka kümeleri diyagramı" border="false":::

> [!NOTE]  
> Kafka hizmeti, sanal ağ içindeki iletişimle sınırlıdır. SSH ve Ambari gibi küme üzerindeki diğer hizmetlere internet üzerinden erişilebilir. HDInsight üzerinde kullanılabilir olan genel bağlantı noktaları hakkında daha fazla bilgi için bkz. [HDInsight Tarafından Kullanılan Bağlantı Noktaları ve URI’ler](hdinsight-hadoop-port-settings-for-services.md).

Azure sanal ağını, Kafka ve Spark kümelerini el ile oluşturabileceğiniz gibi, Azure Resource Manager şablonu kullanmak daha kolay olur. Azure aboneliğinize Azure sanal ağını, Kafka ve Spark kümelerini dağıtmak için aşağıdaki adımları kullanın.

1. Aşağıdaki düğmeyi kullanarak Azure'da oturum açın ve şablonu Azure portalında açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Azure Resource Manager şablonu bu proje () için GitHub deposunda bulunur [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) .

    Bu şablon aşağıdaki kaynakları oluşturur:

   * HDInsight 3.6 kümesi üzerinde bir Kafka.

   * HDInsight 3,6 kümesinde spark.

   * HDInsight kümeleri içeren bir Azure Sanal Ağı. Şablon tarafından oluşturulan sanal ağ, 10.0.0.0/16 adres alanını kullanır.

   * Azure Cosmos DB bir SQL API veritabanı.

    > [!IMPORTANT]  
    > Bu örnekte kullanılan yapılandırılmış akış not defteri, HDInsight 3.6 üzerinde Spark gerektirir. HDInsight üzerinde Spark’ın daha önceki bir sürümünü kullanıyorsanız, not defterini kullanırken hatalarla karşılaşırsınız.

1. **Özel dağıtım** bölümündeki girişleri doldurmak için aşağıdaki bilgileri kullanın:

    |Özellik |Değer |
    |---|---|
    |Abonelik|Azure aboneliğinizi seçin.|
    |Kaynak grubu|Bir grup oluşturun veya var olan bir grubu seçin. Bu grup HDInsight kümesini içerir.|
    |Cosmos DB hesap adı|Bu değer, Cosmos DB hesabının adı olarak kullanılır. Ad yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. Ad, 3-31 karakter arası uzunlukta olmalıdır.|
    |Taban küme adı|Bu değer Spark ve Kafka kümelerinin temel adı olarak kullanılır. Örneğin, **myhdı** girildiğinde __Spark-Myhdi__ adlı bir Spark kümesi ve **Kafka-Myhdi** adlı bir Kafka kümesi oluşturulur.|
    |Küme sürümü|HDInsight kümesi sürümü. Bu örnek HDInsight 3,6 ile test edilmiştir ve diğer küme türleriyle çalışmayabilir.|
    |Küme Oturum Açma Kullanıcı Adı|Spark ve Kafka kümeleri için Yönetici Kullanıcı adı.|
    |Küme Oturum Açma Parolası|Spark ve Kafka kümeleri için Yönetici Kullanıcı parolası.|
    |SSH Kullanıcı adı|Spark ve Kafka kümeleri için oluşturulacak SSH kullanıcısı.|
    |SSH parolası|Spark ve Kafka kümeleri için SSH kullanıcısının parolası.|

    :::image type="content" source="./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png" alt-text="HDInsight özel dağıtım değerleri":::

1. **Hüküm ve Koşullar**’ı okuyun ve ardından **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**’u seçin.

1. Son olarak, **satın al**' ı seçin. Kümelerin, sanal ağın ve Cosmos DB hesabının oluşturulması 45 dakika sürebilir.

## <a name="create-the-cosmos-db-database-and-collection"></a>Cosmos DB veritabanı ve koleksiyonu oluşturma

Bu belgede kullanılan proje verileri Cosmos DB depolar. Kodu çalıştırmadan önce, önce Cosmos DB Örneğinizde bir _veritabanı_ ve _koleksiyon_ oluşturmanız gerekir. Ayrıca, Cosmos DB yönelik isteklerin kimliğini doğrulamak için kullanılan belge uç noktasını ve _anahtarı_ da almalısınız.

Bunu yapmanın bir yolu, [Azure CLI](/cli/azure/)'yi kullanmaktır. Aşağıdaki betik adlı bir veritabanı `kafkadata` ve adlı bir koleksiyonu oluşturacaktır `kafkacollection` . Ardından birincil anahtarı döndürür.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter Notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

Belge uç noktası ve birincil anahtar bilgileri aşağıdaki metne benzer:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Uç nokta ve anahtar değerlerini Jupyıter not defterlerinde gerektiği gibi kaydedin.

## <a name="get-the-notebooks"></a>Not defterlerini al

Bu belgede açıklanan örnek için kodu adresinde bulabilirsiniz [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) .

## <a name="upload-the-notebooks"></a>Not defterlerini karşıya yükleme

Not defterlerini projeden HDInsight kümesine yüklemek için aşağıdaki adımları kullanın:

1. Web tarayıcınızda, Spark kümenizdeki Jupyter Notebook bağlanın. Aşağıdaki URL’de `CLUSTERNAME` değerini __Spark__ kümenizin adıyla değiştirin:

    ```http
    https://CLUSTERNAME.azurehdinsight.net/jupyter
    ```

    Sorulduğunda, kümeyi oluştururken kullanılan küme kullanıcı adı (yönetici) ve parolasını girin.

2. Sayfanın sağ üst kısmından __karşıya yükle__ düğmesini kullanarak __Stream-Taxi-Data-to-Kafka. ipynb__ dosyasını kümeye yükleyin. Karşıya yüklemeyi başlatmak için __Aç__’ı seçin.

3. Not defterleri listesinde __Stream-Taxi-Data-to-Kafka. ipynb__ girişini bulun ve yanındaki __karşıya yükle__ düğmesini seçin.

4. Stream-Data-from- __Cosmos-DB. ipynb__ Not defteri 'ni yüklemek için 1-3 arasındaki adımları yineleyin.

## <a name="load-taxi-data-into-kafka"></a>Kafka 'e TAXI verileri yükleme

Dosyalar karşıya yüklendikten sonra, Not defterini açmak için __Stream-Taxi-Data-to-Kafka. ipynb__ girişini seçin. Kafka 'e veri yüklemek için not defterindeki adımları izleyin.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Spark yapılandırılmış akışını kullanarak TAXI verilerini işleme

[Jupyter Notebook](https://jupyter.org/) giriş sayfasından __Stream-Data-from-Cosmos-DB. ipynb__ girişini seçin. Kafka 'ten veri akışı sağlamak ve Spark yapılandırılmış akışı kullanarak Azure Cosmos DB için not defterindeki adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Apache Spark yapısal akışı kullanmayı öğrendiğinize göre, Apache Spark, Apache Kafka ve Azure Cosmos DB çalışma hakkında daha fazla bilgi edinmek için aşağıdaki belgelere bakın:

* [Apache Kafka ile Apache Spark akışını (DStream) kullanma](hdinsight-apache-spark-with-kafka.md).
* [HDInsight üzerinde Jupyter Notebook ve Apache Spark ile başlayın](spark/apache-spark-jupyter-spark-sql.md)
* [Azure Cosmos DB'ye hoş geldiniz](../cosmos-db/introduction.md)
