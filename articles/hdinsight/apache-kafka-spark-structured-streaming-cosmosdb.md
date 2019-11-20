---
title: Cosmos DB Apache Spark & Apache Kafka-Azure HDInsight
description: Apache Kafka verileri okumak için Apache Spark yapısal akışı kullanmayı ve sonra Azure Cosmos DB nasıl depolayacağınızı öğrenin. Bu örnekte, HDInsight üzerinde Spark’tan bir Jupyter not defterini kullanarak verilerinizi akışla aktaracaksınız.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: faae65c6664123bd673711674a36edc928c74278
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044903"
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

HDInsight üzerinde Apache Kafka, genel internet üzerinden Kafka aracılarına erişim sağlamaz. Kafka ile iletişim kuran her şey, Kafka kümesindeki düğümlerle aynı Azure sanal ağında olmalıdır. Bu örnekte, hem Kafka hem de Spark kümeleri bir Azure sanal ağında bulunur. Aşağıdaki diyagramda, kümeler arasında iletişimin nasıl akagösterdiği gösterilmektedir:

![Bir Azure sanal ağında Spark ve Kafka kümeleri diyagramı](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka hizmeti, sanal ağ içindeki iletişimle sınırlıdır. SSH ve Ambari gibi küme üzerindeki diğer hizmetlere internet üzerinden erişilebilir. HDInsight üzerinde kullanılabilir olan genel bağlantı noktaları hakkında daha fazla bilgi için bkz. [HDInsight Tarafından Kullanılan Bağlantı Noktaları ve URI’ler](hdinsight-hadoop-port-settings-for-services.md).

Azure sanal ağını, Kafka ve Spark kümelerini el ile oluşturabileceğiniz gibi, Azure Resource Manager şablonu kullanmak daha kolay olur. Azure aboneliğinize Azure sanal ağını, Kafka ve Spark kümelerini dağıtmak için aşağıdaki adımları kullanın.

1. Aşağıdaki düğmeyi kullanarak Azure'da oturum açın ve şablonu Azure portalında açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Azure Resource Manager şablonu bu proje için GitHub deposunda ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)) bulunur.

    Bu şablon aşağıdaki kaynakları oluşturur:

   * HDInsight 3.6 kümesi üzerinde bir Kafka.

   * HDInsight 3,6 kümesinde spark.

   * HDInsight kümeleri içeren bir Azure Sanal Ağı.

       > [!NOTE]  
       > Şablon tarafından oluşturulan sanal ağ, 10.0.0.0/16 adres alanını kullanır.

   * Azure Cosmos DB bir SQL API veritabanı.

     > [!IMPORTANT]  
     > Bu örnekte kullanılan yapılandırılmış akış not defteri, HDInsight 3.6 üzerinde Spark gerektirir. HDInsight üzerinde Spark’ın daha önceki bir sürümünü kullanıyorsanız, not defterini kullanırken hatalarla karşılaşırsınız.

2. **Özel dağıtım** bölümündeki girişleri doldurmak için aşağıdaki bilgileri kullanın:

    ![HDInsight özel dağıtım değerleri](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

    * **Abonelik**: Azure aboneliğinizi seçin.

    * **Kaynak grubu**: bir grup oluşturun veya var olan bir grubu seçin. Bu grup HDInsight kümesini içerir.

    * **Konum**: coğrafi olarak size yakın bir konum seçin.

    * **Cosmos DB hesap adı**: Bu değer, Cosmos DB hesabının adı olarak kullanılır.

    * **Temel küme adı**: Bu değer Spark ve Kafka kümelerinin temel adı olarak kullanılır. Örneğin, **myhdı** girildiğinde __Spark-Myhdi__ adlı bir Spark kümesi ve **Kafka-Myhdi**adlı bir Kafka kümesi oluşturulur.

    * **Küme sürümü**: HDInsight kümesi sürümü.

        > [!IMPORTANT]  
        > Bu örnek HDInsight 3,6 ile test edilmiştir ve diğer küme türleriyle çalışmayabilir.

    * **Küme oturum açma Kullanıcı adı**: Spark ve Kafka kümeleri için Yönetici Kullanıcı adı.

    * **Küme oturum açma parolası**: Spark ve Kafka kümeleri için Yönetici Kullanıcı parolası.

    * **SSH Kullanıcı adı**: Spark ve Kafka kümeleri IÇIN oluşturulacak SSH kullanıcısı.

    * **SSH parolası**: Spark ve Kafka KÜMELERI için SSH kullanıcısının parolası.

3. **Hüküm ve Koşullar**’ı okuyun ve ardından **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**’u seçin.

4. Son olarak, **satın al**' ı seçin. Kümelerin oluşturulması yaklaşık 20 dakika sürer.

> [!IMPORTANT]  
> Kümelerin, sanal ağın ve Cosmos DB hesabının oluşturulması 45 dakika sürebilir.

## <a name="create-the-cosmos-db-database-and-collection"></a>Cosmos DB veritabanı ve koleksiyonu oluşturma

Bu belgede kullanılan proje verileri Cosmos DB depolar. Kodu çalıştırmadan önce, önce Cosmos DB Örneğinizde bir _veritabanı_ ve _koleksiyon_ oluşturmanız gerekir. Ayrıca, Cosmos DB yönelik isteklerin kimliğini doğrulamak için kullanılan belge uç noktasını ve _anahtarı_ da almalısınız. 

Bunu yapmanın bir yolu, [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)'yi kullanmaktır. Aşağıdaki betik, `kafkadata` adlı bir veritabanı ve `kafkacollection`adlı bir koleksiyon oluşturur. Ardından birincil anahtarı döndürür.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
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

## <a name="get-the-apache-kafka-brokers"></a>Apache Kafka aracılarını al

Bu örnekteki kod, Kafka kümesindeki Kafka Broker konaklarına bağlanır. İki Kafka Broker ana bilgisayarlarının adreslerini bulmak için aşağıdaki PowerShell veya bash örneğini kullanın:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]  
> Bash örneği, `$CLUSTERNAME` Kafka kümesinin adını içermesini bekler.
>
> Bu örnek, JSON belgesinden verileri ayrıştırmak için [JQ](https://stedolan.github.io/jq/) yardımcı programını kullanır.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

İstendiğinde, küme oturum açma (yönetici) hesabının parolasını girin

Çıktı aşağıdaki metne benzer:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Bu belgenin aşağıdaki bölümlerinde kullanıldığı için bu bilgileri kaydedin.

## <a name="get-the-notebooks"></a>Not defterlerini al

Bu belgede açıklanan örneğin kodu [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) sayfasından edinilebilir.

## <a name="upload-the-notebooks"></a>Not defterlerini karşıya yükleme

Not defterlerini projeden HDInsight kümesine yüklemek için aşağıdaki adımları kullanın:

1. Web tarayıcınızdan Spark kümeniz üzerindeki Jupyter not defterine bağlanın. Aşağıdaki URL’de `CLUSTERNAME` değerini __Spark__ kümenizin adıyla değiştirin:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

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
* [Azure Cosmos DB hoş geldiniz](../cosmos-db/introduction.md)
