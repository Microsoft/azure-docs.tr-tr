---
title: Azure ile HDInsight üzerinde Apache Kafka kullanın IoT Hub
description: Azure IoT Hub ile HDInsight üzerinde Apache Kafka nasıl kullanacağınızı öğrenin. Kafka Connect Azure IoT Hub projesi, Kafka için bir kaynak ve havuz Bağlayıcısı sağlar. Kaynak bağlayıcı IoT Hub verileri okuyabilir ve havuz Bağlayıcısı IoT Hub yazar.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: feada70c7a461bb4a9cd621c76b5606a7f0e19d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865290"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Azure ile HDInsight üzerinde Apache Kafka kullanın IoT Hub

HDInsight ve Azure IoT Hub Apache Kafka arasında veri taşımak için [Azure IoT Hub bağlayıcısını bağlama Apache Kafka](https://github.com/Azure/toketi-kafka-connect-iothub) nasıl kullanacağınızı öğrenin. Bu belgede, IoT Hub bağlayıcısını kümedeki bir kenar düğümünden çalıştırmayı öğreneceksiniz.

Kafka Connect API 'SI, verileri sürekli olarak Kafka 'e çekmek veya Kafka 'den başka bir sisteme veri göndermek için bağlayıcı uygulamanıza olanak tanır. [Azure IoT Hub Connect Apache Kafka](https://github.com/Azure/toketi-kafka-connect-iothub) , Azure IoT Hub 'tan veri çeken bir bağlayıcıdır. Ayrıca, Kafka 'den IoT Hub veri gönderebilir.

IoT Hub çekiliyor bir __kaynak__ Bağlayıcısı kullanırsınız. IoT Hub gönderilirken bir __Havuz__ Bağlayıcısı kullanırsınız. IoT Hub Bağlayıcısı hem kaynak hem de havuz bağlayıcıları sağlar.

Aşağıdaki diyagramda, bağlayıcıyı kullanırken HDInsight üzerinde Azure IoT Hub ve Kafka arasındaki veri akışı gösterilmektedir.

:::image type="content" source="./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png" alt-text="Bağlayıcı aracılığıyla IoT Hub 'den Kafka 'e akan verileri gösteren resim" border="false":::

Connect API 'SI hakkında daha fazla bilgi için bkz [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) ..

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Kafka kümesi. Daha fazla bilgi için [HDInsight üzerinde Kafka hızlı başlangıcı](apache-kafka-get-started.md) belgesine bakın.

* Kafka kümesindeki bir kenar düğümü. Daha fazla bilgi için bkz. [HDInsight ile Edge düğümlerini kullanma](../hdinsight-apps-use-edge-node.md) belgesi.

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Azure IoT Hub ve cihaz. Bu makalede, [Connect Raspberry PI online simülatörü IoT Hub Azure 'a](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md)kullanmayı düşünün.

* [Scala derleme aracı](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Bağlayıcıyı oluşturma

1. Bağlayıcısının kaynağını [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) yerel ortamınıza indirin.

2. Bir komut isteminden `toketi-kafka-connect-iothub-master` dizinine gidin. Ardından projeyi derlemek ve paketlemek için şu komutu kullanın:

    ```cmd
    sbt assembly
    ```

    Oluşturma işleminin tamamlanması birkaç dakika sürer. Komut, `kafka-connect-iothub-assembly_2.11-0.7.0.jar` proje için dizininde adlı bir dosya oluşturur `toketi-kafka-connect-iothub-master\target\scala-2.11` .

## <a name="install-the-connector"></a>Bağlayıcıyı yükler

1. . Jar dosyasını Kafka on HDInsight kümenizin Edge düğümüne yükleyin. Aşağıdaki komutu, `CLUSTERNAME` kümenizin gerçek adıyla değiştirerek düzenleyin. SSH kullanıcı hesabı ve [kenar düğümünün](../hdinsight-apps-use-edge-node.md#access-an-edge-node) adı için varsayılan değerler aşağıda kullanılır, gerektiğinde değiştirin.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Dosya kopyalama işlemi tamamlandıktan sonra, SSH kullanarak kenar düğümüne bağlanın:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Bağlayıcıyı Kafka dizinine yüklemek için `libs` Şu komutu kullanın:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Diğer adımlar için SSH bağlantınızı etkin tutun.

## <a name="configure-apache-kafka"></a>Apache Kafka Yapılandır

SSH bağlantınızdan kenar düğümüne, bağlayıcıyı tek başına modda çalıştırmak üzere Kafka yapılandırmak için aşağıdaki adımları kullanın:

1. Parola değişkenini ayarlayın. PAROLAYı küme oturum açma parolasıyla değiştirin, ardından şu komutu girin:

    ```bash
    export password='PASSWORD'
    ```

1. [JQ](https://stedolan.github.io/jq/) yardımcı programını yükler. JQ, ambarı sorgularından döndürülen JSON belgelerinin işlemesini kolaylaştırır. Aşağıdaki komutu girin:

    ```bash
    sudo apt -y install jq
    ```

1. Kafka aracıları adresini alın. Kümenizde birçok aracıda bulunabilir, ancak yalnızca bir veya iki başvuru yapmanız yeterlidir. İki aracı konağın adresini almak için aşağıdaki komutu kullanın:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Daha sonra kullanmak üzere değerleri kopyalayın. Döndürülen değer aşağıdaki metne benzer:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Apache Zookeeper düğümlerinin adresini alın. Kümede birkaç Zookeeper düğümü vardır, ancak yalnızca bir veya iki başvuru yapmanız yeterlidir. Şu komutu, değişkende bulunan adresleri depolamak için kullanın `KAFKAZKHOSTS` :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Bağlayıcıyı tek başına modunda çalıştırırken, `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` Dosya Kafka aracıları ile iletişim kurmak için kullanılır. Dosyayı düzenlemek için `connect-standalone.properties` aşağıdaki komutu kullanın:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Aşağıdaki düzenlemeleri yapın:

    |Geçerli değer |Yeni değer | Yorum |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Değeri, `localhost:9092` önceki adımdaki aracı konaklarıyla değiştirin|Kafka aracılarını bulmak için kenar düğümü için tek başına yapılandırmayı yapılandırır.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Bu değişiklik, Kafka ile birlikte sunulan konsol üreticisi 'ni kullanarak test etmenizi sağlar. Diğer üreticileri ve tüketiciler için farklı dönüştürücülerin olması gerekebilir. Diğer dönüştürücü değerlerini kullanma hakkında daha fazla bilgi için bkz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) ..|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Yukarıdakiyle aynıdır.|
    |Yok|`consumer.max.poll.records=10`|Dosyanın sonuna ekleyin. Bu değişiklik, havuz bağlayıcısında aynı anda 10 kayıt arasında sınırlama yaparak zaman aşımlarını önlemektir. Daha fazla bilgi için bkz. [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Dosyayı kaydetmek için __CTRL + X__, __Y__ kullanın ve ardından __girin__.

1. Bağlayıcı tarafından kullanılan konuları oluşturmak için aşağıdaki komutları kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    `iotin`Ve konuların mevcut olduğunu doğrulamak için `iotout` aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin`Konusu IoT Hub iletileri almak için kullanılır. `iotout`Konusu IoT Hub ileti göndermek için kullanılır.

## <a name="get-iot-hub-connection-information"></a>IoT Hub bağlantı bilgilerini al

Bağlayıcı tarafından kullanılan IoT Hub bilgilerini almak için aşağıdaki adımları kullanın:

1. IoT Hub 'ınız için Event hub ile uyumlu uç nokta ve Olay Hub 'ı ile uyumlu uç nokta adını alın. Bu bilgileri almak için aşağıdaki yöntemlerden birini kullanın:

   * __[Azure Portal](https://portal.azure.com/)__, aşağıdaki adımları kullanın:

     1. IoT Hub gidin ve __uç noktalar__' ı seçin.
     2. __Yerleşik uç noktalarda__ __Olaylar__' ı seçin.
     3. __Özelliklerden__ aşağıdaki alanların değerini kopyalayın:

         * __Olay Hub 'ı ile uyumlu ad__
         * __Olay Hub 'ı ile uyumlu uç nokta__
         * __Bölüme__

        > [!IMPORTANT]  
        > Portaldaki uç nokta değeri, bu örnekte gerekli olmayan ek metin içerebilir. Bu düzenle eşleşen metni ayıklayın `sb://<randomnamespace>.servicebus.windows.net/` .

   * __[Azure CLI](/cli/azure/get-started-with-azure-cli)'dan__ aşağıdaki komutu kullanın:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       `myhubname`IoT Hub 'ınızın adıyla değiştirin. Yanıt aşağıdaki metne benzer:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. __Paylaşılan erişim ilkesini__ ve __anahtarı__ alın. Bu örnekte, __hizmet__ anahtarını kullanın. Bu bilgileri almak için aşağıdaki yöntemlerden birini kullanın:

    * __[Azure Portal](https://portal.azure.com/)__, aşağıdaki adımları kullanın:

        1. __Paylaşılan erişim ilkeleri__' ni seçin ve ardından __hizmet__' i seçin.
        2. __Birincil anahtar__ değerini kopyalayın.
        3. __Bağlantı dizesini kopyalayın--birincil anahtar__ değeri.

    * __[Azure CLI](/cli/azure/get-started-with-azure-cli)'dan__ aşağıdaki komutu kullanın:

        1. Birincil anahtar değerini almak için aşağıdaki komutu kullanın:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            `myhubname`IoT Hub 'ınızın adıyla değiştirin. Yanıt, `service` Bu hub için ilkenin birincil anahtarıdır.

        2. İlke için bağlantı dizesini almak için `service` aşağıdaki komutu kullanın:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            `myhubname`IoT Hub 'ınızın adıyla değiştirin. Yanıt, ilke için bağlantı dizesidir `service` .

## <a name="configure-the-source-connection"></a>Kaynak bağlantıyı yapılandırma

Kaynağı IoT Hub çalışacak şekilde yapılandırmak için, Edge düğümüne bir SSH bağlantısından aşağıdaki eylemleri gerçekleştirin:

1. Dizindeki dosyanın bir kopyasını oluşturun `connect-iot-source.properties` `/usr/hdp/current/kafka-broker/config/` . Dosyayı toketi-Kafka-Connect-ıothub projesinden indirmek için aşağıdaki komutu kullanın:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. `connect-iot-source.properties`Dosyayı düzenlemek ve IoT Hub bilgilerini eklemek için aşağıdaki komutu kullanın:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. Düzenleyicide aşağıdaki girişleri bulun ve değiştirin:

    |Geçerli değer |Düzenle|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|`PLACEHOLDER` yerine `iotin` yazın. IoT Hub 'dan alınan iletiler `iotin` konuya yerleştirilir.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|`PLACEHOLDER`Olay Hub 'ı ile uyumlu adıyla değiştirin.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|`PLACEHOLDER`Olay Hub 'ı ile uyumlu uç nokta ile değiştirin.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|`PLACEHOLDER` yerine `service` yazın.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|`PLACEHOLDER`İlkenin birincil anahtarıyla değiştirin `service` .|
    |`IotHub.Partitions=PLACEHOLDER`|`PLACEHOLDER`Önceki adımlardan bölüm sayısıyla değiştirin.|
    |`IotHub.StartTime=PLACEHOLDER`|`PLACEHOLDER`BIR UTC tarihi ile değiştirin. Bu tarih, bağlayıcının iletileri denetlemeye başladığı tarihtir. Tarih biçimi `yyyy-mm-ddThh:mm:ssZ` .|
    |`BatchSize=100`|`100` yerine `5` yazın. Bu değişiklik, IoT Hub 'da beş yeni ileti olduğunda bağlayıcının iletileri Kafka 'e okumasına neden olur.|

    Örnek bir yapılandırma için bkz. [Azure Için Kafka Connect Source Connector IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Değişiklikleri kaydetmek için __CTRL + X__, __Y__ ve __ENTER__ tuşlarını kullanın.

Bağlayıcı kaynağını yapılandırma hakkında daha fazla bilgi için bkz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) ..

## <a name="configure-the-sink-connection"></a>Havuz bağlantısını yapılandırma

Havuz bağlantısını IoT Hub ile çalışacak şekilde yapılandırmak için, Edge düğümüne bir SSH bağlantısından aşağıdaki eylemleri gerçekleştirin:

1. Dizindeki dosyanın bir kopyasını oluşturun `connect-iothub-sink.properties` `/usr/hdp/current/kafka-broker/config/` . Dosyayı toketi-Kafka-Connect-ıothub projesinden indirmek için aşağıdaki komutu kullanın:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. `connect-iothub-sink.properties`Dosyayı düzenlemek ve IoT Hub bilgilerini eklemek için aşağıdaki komutu kullanın:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. Düzenleyicide aşağıdaki girişleri bulun ve değiştirin:

    |Geçerli değer |Düzenle|
    |---|---|
    |`topics=PLACEHOLDER`|`PLACEHOLDER` yerine `iotout` yazın. Konuya yazılan iletiler `iotout` IoT Hub 'ına iletilir.|
    |`IotHub.ConnectionString=PLACEHOLDER`|`PLACEHOLDER`İlke için bağlantı dizesiyle değiştirin `service` .|

    Örnek bir yapılandırma için bkz. [Azure IoT Hub Için Kafka Connect Sink Connector](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Değişiklikleri kaydetmek için __CTRL + X__, __Y__ ve __ENTER__ tuşlarını kullanın.

Bağlayıcı havuzunu yapılandırma hakkında daha fazla bilgi için bkz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) ..

## <a name="start-the-source-connector"></a>Kaynak bağlayıcısını başlatma

1. Kaynak bağlayıcısını başlatmak için Edge düğümüne bir SSH bağlantısından aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Bağlayıcı başladıktan sonra cihazınızdan IoT Hub 'ına ileti gönderin. Bağlayıcı IoT Hub 'ından iletileri okur ve Kafka konusunda bunları depoladığında, bilgileri konsola kaydeder:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Bağlayıcı başladığında birkaç uyarı görebilirsiniz. Bu uyarılar IoT Hub 'ından ileti alma ile ilgili sorunlara neden olmaz.

1. Birkaç dakika sonra **CTRL + C** tuşlarına iki kez kullanarak bağlayıcıyı durdurun. Bağlayıcının durdurulması birkaç dakika sürer.

## <a name="start-the-sink-connector"></a>Havuz bağlayıcısını başlatma

Bir SSH bağlantısından kenar düğümüne, havuz bağlayıcısını tek başına modunda başlatmak için aşağıdaki komutu kullanın:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Bağlayıcı çalışırken, aşağıdaki metinle benzer bilgiler görüntülenir:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Bağlayıcı başladığında birkaç uyarı fark edebilirsiniz. Bunları güvenle yok sayabilirsiniz.

## <a name="send-messages"></a>İleti gönderme

Bağlayıcı aracılığıyla ileti göndermek için aşağıdaki adımları kullanın:

1. Kafka kümesinde *ikinci bir* SSH oturumu açın:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Yeni SSH oturumu için Kafka aracıların adresini alın. PAROLAYı küme oturum açma parolasıyla değiştirin, ardından şu komutu girin:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Konuya ileti göndermek için `iotout` aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Bu komut sizi normal Bash istemine döndürmez. Bunun yerine, konuya klavye girişi gönderir `iotout` .

1. Cihazınıza bir ileti göndermek için, için SSH oturumuna bir JSON belgesi yapıştırın `kafka-console-producer` .

    > [!IMPORTANT]  
    > `"deviceId"`Girişin değerini CIHAZıNıZıN kimliğine ayarlamanız gerekir. Aşağıdaki örnekte, cihaz şu şekilde adlandırılır `myDeviceId` :

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Bu JSON belgesi için şema, daha ayrıntılı olarak açıklanmıştır [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

    Benzetimli Raspberry PI cihazını kullanıyorsanız ve çalışıyorsa, cihaz tarafından aşağıdaki ileti günlüğe kaydedilir:

    ```output
    Receive message: Turn On
    ```

    JSON belgesini yeniden gönderin, ancak girdinin değerini değiştirin `"message"` . Yeni değer cihaz tarafından günlüğe kaydedilir.

Havuz bağlayıcısını kullanma hakkında daha fazla bilgi için bkz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) ..

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, HDInsight üzerinde IoT Kafka bağlayıcısını başlatmak için Apache Kafka Connect API 'sini nasıl kullanacağınızı öğrendiniz. Kafka ile çalışmanın diğer yollarını saptamak için aşağıdaki bağlantıları kullanın:

* [HDInsight üzerinde Apache Kafka ile Apache Spark kullanma](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight üzerinde Apache Kafka ile Apache Storm kullanma](../hdinsight-apache-storm-with-kafka.md)
