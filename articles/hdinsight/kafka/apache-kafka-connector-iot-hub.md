---
title: Azure IOT Hub ile HDInsight üzerinde Apache kafka'yı kullanma
description: Azure IOT Hub ile HDInsight üzerinde Apache kafka'yı kullanma konusunda bilgi edinin. Kafka bağlanmak Azure IOT Hub project, Kafka için kaynak ve havuz bağlayıcı sağlar. Kaynak bağlayıcısını, IOT Hub'ından verileri okuyabilir ve IOT Hub'ına havuz bağlayıcı yazar.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 5559d243573ea04400007cdce0e71009dc91e27a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446433"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Azure IOT Hub ile HDInsight üzerinde Apache kafka'yı kullanma

Nasıl kullanacağınızı öğrenin [Apache Kafka bağlanmak Azure IOT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) HDInsight üzerinde Apache Kafka ile Azure IOT hub'ı arasında veri taşımak için bağlayıcı. Bu belgede, IOT hub'ı bağlayıcı bir küme kenar düğümüne çalışmayı öğrenin.

Kafka bağlanmak API sürekli olarak veri çekme Kafka veya başka bir sisteme Kafka'dan veri gönderme bağlayıcıları olanak tanır. [Apache Kafka bağlanmak Azure IOT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) Kafka ile Azure IOT Hub'ından veri çeker bir Bağlayıcıdır. Bu ayrıca verileri Kafka'dan IOT Hub'ına gönderebilirsiniz. 

IOT Hub'ından çekme sırasında kullandığınız bir __kaynak__ bağlayıcı. IOT Hub'ına gönderirken kullandığınız bir __havuz__ bağlayıcı. IOT hub'ı bağlayıcı hem kaynak hem de havuz bağlayıcılar sağlar.

Aşağıdaki diyagramda HDInsight üzerinde bağlayıcıyı kullanarak Azure IOT Hub ve Kafka arasındaki veri akışını gösterir.

![IOT Hub'ından Kafka'ya bağlayıcı üzerinden akan verileri gösteren resim](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Connect API hakkında daha fazla bilgi için bkz. [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* HDInsight kümesinde Kafka. Daha fazla bilgi için [HDInsight üzerinde Kafka hızlı başlangıcı](apache-kafka-get-started.md) belgesine bakın.

* Kafka kümesinin bir kenar düğümüne. Daha fazla bilgi için [HDInsight ile kenar düğümlerini kullanma](../hdinsight-apps-use-edge-node.md) belge.

* Azure IOT Hub. Bu makalede önerim [Azure IOT hub'a bağlanma Raspberry Pi çevrimiçi simülatör](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) belge.

* Bir SSH istemcisi. Bu belgede yer alan adımlarda, kümeye bağlanmak için SSH kullanılır. Daha fazla bilgi için [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md) belgesine bakın.

## <a name="install-the-connector"></a>Bağlayıcısını yükleme

1. Azure IOT Hub'ından için Kafka Connect en son sürümü indirmek [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Kenar düğümüne, kafka, HDInsight kümesinde .jar dosyasını karşıya yüklemek için aşağıdaki komutu kullanın:

    > [!NOTE]  
    > Değiştirin `sshuser` HDInsight kümenizin SSH kullanıcı hesabı ile. Değiştirin `new-edgenode` kenar düğümünün adıyla. Değiştirin `clustername` küme adı ile. Uç düğümünün SSH uç noktası hakkında daha fazla bilgi için bkz. [kenar düğümleri HDInsight ile kullanılan](../hdinsight-apps-use-edge-node.md#access-an-edge-node) belge.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Dosya kopyalama işlemi tamamlandıktan sonra SSH kullanarak kenar düğümüne bağlanın:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Daha fazla bilgi için [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md) belgesine bakın.

4. Kafka Bağlayıcısı'nı yüklemek için `libs` dizin, aşağıdaki komutu kullanın:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]  
> Bu belgedeki adımlarda geri kalanı ile ilgili sorunlar önceden oluşturulmuş .jar dosyasını kullanırken karşılaşırsanız, paket kaynağından oluşturmaya çalışın.
>
> Bağlayıcı oluşturmak için Scala geliştirme ortamıyla sahip [Scala derleme aracı](https://www.scala-sbt.org/).
>
> 1. Kaynak için bir bağlayıcı indirmek [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) geliştirme ortamınıza.
>
> 2. Bir komut istemi proje dizininde, derlemek ve projeyi paketlemek için aşağıdaki komutu kullanın:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Bu komut, adlı bir dosya oluşturur. `kafka-connect-iothub-assembly_2.11-0.6.jar` içinde `target/scala-2.11` projesi için dizin.

## <a name="configure-apache-kafka"></a>Configure Apache Kafka

Kenar düğümüne SSH bağlantısından, Kafka Bağlayıcısı'nı tek başına modunda çalışacak şekilde yapılandırmak için aşağıdaki adımları kullanın:

1. Küme adı, bir değişkene kaydedin. Bir değişken kullanmak bu bölümdeki diğer komutlar kopyala/yapıştır kolaylaştırır:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Yükleme `jq` yardımcı programı. Bu yardımcı program JSON belgelerini Ambari sorgulardan döndürülen işlem kolaylaştırır:

    ```bash
    sudo apt -y install jq
    ```

3. Kafka'nın Adres aracıları alın. Kümedeki birçok aracıları olabilir, ancak yalnızca bir veya iki başvuru gerekir. İki Aracısı ana bilgisayar adresini almak için aşağıdaki komutu kullanın:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    İstendiğinde, küme oturum açma (yönetici) hesabı için parolayı girin. Döndürülen değer aşağıdaki metne benzer:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Apache Zookeeper düğümleri adresini alın. Kümede birçok Zookeeper düğümleri vardır, ancak yalnızca bir veya iki başvuru gerekir. İki Zookeeper düğümleri adresini almak için aşağıdaki komutu kullanın:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Bağlayıcısı'nı tek başına modunda çalışırken `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` Kafka aracıları ile iletişim kurmak için kullanılır. Düzenlenecek `connect-standalone.properties` dosyasında, aşağıdaki komutu kullanın:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Kafka aracıları bulmak kenar düğümünü tek başına yapılandırma ile ile başlayan satırı bulun `bootstrap.servers=`. Değiştirin `localhost:9092` önceki adımdan Aracısı konaklarıyla değeri.

    * Değişiklik `key.converter=` ve `value.converter=` aşağıdaki değerlere satırlar:

        ```ini
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]  
        > Bu değişiklik, Kafka ile birlikte dahil edilen konsol üretici kullanarak test olanak tanır. Diğer üreticileri ve tüketicileri için farklı dönüştürücüleri gerekebilir. Diğer dönüştürücü değerleri kullanma hakkında daha fazla bilgi için bkz. [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Aşağıdaki metni içeren dosyanın sonunda bir satır ekleyin:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]  
        > Bu değişiklik, bir kerede 10 kayıtları sınırlayarak havuz bağlayıcı zaman aşımları engellemektir. Daha fazla bilgi için bkz. [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Dosyayı kaydetmek için kullanın __Ctrl + X__, __Y__, ardından __Enter__.

7. Bağlayıcı tarafından kullanılan Konular oluşturmak için aşağıdaki komutları kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Doğrulamak için `iotin` ve `iotout` konular var, aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` Konu, IOT Hub'ından ileti almak için kullanılır. `iotout` Konu, IOT Hub'ına ileti göndermek için kullanılır.

## <a name="get-iot-hub-connection-information"></a>IOT Hub bağlantı bilgilerini alma

Bağlayıcı tarafından kullanılan IOT hub'ı bilgileri almak için aşağıdaki adımları kullanın:

1. IOT hub'ınızın Event Hub ile uyumlu uç nokta adı ve Event Hub ile uyumlu uç noktasını alın. Bu bilgileri almak için aşağıdaki yöntemlerden birini kullanın:

   * __Gelen [Azure portalında](https://portal.azure.com/)__ , aşağıdaki adımları kullanın:

     1. IOT Hub'ınıza gidin ve seçin __uç noktaları__.
     2. Gelen __yerleşik uç noktaları__seçin __olayları__.
     3. Gelen __özellikleri__, aşağıdaki alanları değerini kopyalayın:

         * __Event Hub ile uyumlu adı__
         * __Event Hub ile uyumlu uç noktası__
         * __Bölümler__

        > [!IMPORTANT]  
        > Bu örnekte gerekmeyen fazladan metin portaldan uç nokta değerini içerebilir. Bu desenle eşleşen metni Ayıkla `sb://<randomnamespace>.servicebus.windows.net/`.

   * __Gelen [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ , aşağıdaki komutu kullanın:

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Değiştirin `myhubname` IOT hub'ınızın adıyla. Yanıt aşağıdaki metne benzer:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Alma __paylaşılan erişim ilkesi__ ve __anahtar__. Bu örneğin __hizmet__ anahtarı. Bu bilgileri almak için aşağıdaki yöntemlerden birini kullanın:

    * __Gelen [Azure portalında](https://portal.azure.com/)__ , aşağıdaki adımları kullanın:

        1. Seçin __paylaşılan erişim ilkeleri__ve ardından __hizmet__.
        2. Kopyalama __birincil anahtar__ değeri.
        3. Kopyalama __bağlantı dizesi – birincil anahtar__ değeri.

    * __Gelen [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ , aşağıdaki komutu kullanın:

        1. Birincil anahtar değerini almak için aşağıdaki komutu kullanın:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Değiştirin `myhubname` IOT hub'ınızın adıyla. Birincil anahtara yanıttır `service` bu hub ilkesi.

        2. Bağlantı dizesini almak için `service` İlkesi, aşağıdaki komutu kullanın:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Değiştirin `myhubname` IOT hub'ınızın adıyla. Bağlantı dizesini yanıttır `service` ilkesi.

## <a name="configure-the-source-connection"></a>Kaynak bağlantısı yapılandırma

IOT Hub ile çalışmaya kaynağını yapılandırmak için kenar düğümüne SSH bağlantısından aşağıdaki eylemleri gerçekleştirin:

1. Bir kopyasını oluşturmak `connect-iot-source.properties` dosyası `/usr/hdp/current/kafka-broker/config/` dizin. Toketi-kafka-connect-iothub projeden dosyasını indirmek için aşağıdaki komutu kullanın:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Düzenlenecek `connect-iot-source.properties` dosya ve IOT hub'ı bilgileri eklemek için aşağıdaki komutu kullanın:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Düzenleyicisi'ni bulun ve aşağıdaki girdileri değiştirin:

   * `Kafka.Topic=PLACEHOLDER`:  yerine `iotin` yazın. IOT hub'ından alınan iletileri yerleştirildiğinde `iotin` konu.
   * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Değiştirin `PLACEHOLDER` Event Hub ile uyumlu ada sahip.
   * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Değiştirin `PLACEHOLDER` Event Hub ile uyumlu uç nokta ile.
   * `IotHub.Partitions=PLACEHOLDER`: Değiştirin `PLACEHOLDER` ile önceki adımlarda geçen bölüm sayısı.
   * `IotHub.AccessKeyName=PLACEHOLDER`:  yerine `service` yazın.
   * `IotHub.AccessKeyValue=PLACEHOLDER`: Değiştirin `PLACEHOLDER` birincil anahtarı ile `service` ilkesi.
   * `IotHub.StartType=PLACEHOLDER`: Değiştirin `PLACEHOLDER` UTC tarihi. Bağlayıcı iletileri denetleme başladığında bu tarihtir. Tarih biçimi `yyyy-mm-ddThh:mm:ssZ`.
   * `BatchSize=100`:  yerine `5` yazın. Bu değişiklik, IOT hub'ında beş yeni iletileri sonra Kafka iletileri okumak bağlayıcıyı neden olur.

     Örnek bir yapılandırma için bkz: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Değişiklikleri kaydetmek için kullanın __Ctrl + X__, __Y__, ardından __Enter__.

Bağlayıcı kaynak yapılandırma hakkında daha fazla bilgi için bkz. [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Havuz bağlantı yapılandırma

IOT Hub'ınıza çalışmak için havuz bağlantı yapılandırmak için kenar düğümüne SSH bağlantısından aşağıdaki eylemleri gerçekleştirin:

1. Bir kopyasını oluşturmak `connect-iothub-sink.properties` dosyası `/usr/hdp/current/kafka-broker/config/` dizin. Toketi-kafka-connect-iothub projeden dosyasını indirmek için aşağıdaki komutu kullanın:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Düzenlenecek `connect-iothub-sink.properties` dosya ve IOT hub'ı bilgileri eklemek için aşağıdaki komutu kullanın:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    Düzenleyicisi'ni bulun ve aşağıdaki girdileri değiştirin:

   * `topics=PLACEHOLDER`:  yerine `iotout` yazın. Yazılan iletileri `iotout` konu, IOT hub'ına iletilir.
   * `IotHub.ConnectionString=PLACEHOLDER`: Değiştirin `PLACEHOLDER` bağlantı dizesiyle `service` ilkesi.

     Örnek bir yapılandırma için bkz: [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Değişiklikleri kaydetmek için kullanın __Ctrl + X__, __Y__, ardından __Enter__.

Bağlayıcı havuz yapılandırma hakkında daha fazla bilgi için bkz. [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Kaynak Bağlayıcısı'nı başlatın

Kaynak Bağlayıcısı'nı başlatmak için kenar düğümüne bir SSH bağlantısından aşağıdaki komutu kullanın:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Bağlayıcı başladıktan sonra iletileri, aygıttan IOT hub'ına gönderir. Bağlayıcı iletileri IOT hub'dan okur ve bunları Kafka konusuna depolar gibi konsola bilgilerini kaydeder:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]  
> Bağlayıcı başlatılırken bazı uyarılar görebilirsiniz. Bu uyarılar IOT hub'ından iletiler alan sorunlara neden olmaz.

Bağlayıcı durdurmak için kullanın __Ctrl + C__.

## <a name="start-the-sink-connector"></a>Havuz Bağlayıcısı'nı başlatın

Kenar düğümüne SSH bağlantısından, havuz Bağlayıcısı'nı tek başına modunda başlatmak için aşağıdaki komutu kullanın:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Bağlayıcı çalışırken aşağıdaki metne benzer bilgiler görüntülenir:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Bağlayıcı başlatılırken bazı uyarılar görebilirsiniz. Bunları güvenle yok sayabilirsiniz.

Bağlayıcı aracılığıyla göndermek için aşağıdaki adımları kullanın:

1. Kafka kümesinin başka bir SSH oturumu açın:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. İleti göndermek için `iotout` konu, aşağıdaki komutu kullanın:

    > [!WARNING]  
    > Bu yeni bir SSH bağlantısı olduğundan `$KAFKABROKERS` değişken, herhangi bir bilgi içermiyor. Bunu ayarlamak için aşağıdaki yöntemlerden birini kullanın:
    >
    > * İlk üç adımda kullanın [yapılandırma Apache Kafka](#configure-apache-kafka) bölümü.
    > * Kullanım `echo $KAFKABROKERS` değerlerini alma ve sonra değiştirmek için önceki SSH bağlantısından `$KAFKABROKERS` gerçek değerlerle aşağıdaki komutta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Bu komut için normal Bash isteminde döndürmez. Bunun yerine, klavye girdisini gönderdiği `iotout` konu.

3. Cihazınız için bir ileti göndermek için SSH oturumu için bir JSON belgesi yapıştırın `kafka-console-producer`.

    > [!IMPORTANT]  
    > Değerini ayarlamalısınız `"deviceId"` giriş cihazınızın kimliği. Aşağıdaki örnekte, cihazın adlı `fakepi`:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Bu JSON belgesini için şemayı daha ayrıntılı anlatılan [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Benzetimli bir Raspberry Pi cihaz kullandığınızı ve çalışmakta olan, aşağıdaki ileti cihaz tarafından kaydedilir:

    ```text
    Receive message: Turn On
    ```

    JSON belgesini yeniden ancak değiştirin `"message"` girişi. Yeni değer, cihaz tarafından günlüğe kaydedilir.

Havuz Bağlayıcısı'nı kullanma hakkında daha fazla bilgi için bkz. [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, HDInsight üzerinde Kafka IOT Bağlayıcısı'nı başlatmak için Apache Kafka API'si bağlanmak kullanmayı öğrendiniz. Kafka ile çalışmak için diğer yollarını bulmak için aşağıdaki bağlantıları kullanın:

* [HDInsight üzerinde Apache Kafka ile Apache Spark kullanma](../hdinsight-apache-spark-with-kafka.md)
* [Apache Storm'u HDInsight üzerinde Apache Kafka ile kullanma](../hdinsight-apache-storm-with-kafka.md)
