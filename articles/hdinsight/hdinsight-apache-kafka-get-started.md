---
title: "Azure HDInsight’ta Apache Kafka ile çalışmaya başlama | Microsoft Docs"
description: "HDInsight’ta Kafka oluşturma ve birlikte çalışma işleminin temel bilgilerini alın."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1c7a95354358d91859ce9d2019cef93b6662811f
ms.lasthandoff: 03/25/2017

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Azure HDInsight’ta Apache Kafka (önizleme) ile çalışmaya başlama

[Apache Kafka](https://kafka.apache.org), HDInsight ile birlikte kullanılabilen, açık kaynaklı bir dağıtılmış akış platformudur. Yayımla-abone ol ileti kuyruğuna benzer işlevler sağladığı için genellikle ileti aracısı olarak kullanılır. Bu belgede, HDInsight kümesinde Kafka oluşturma ve sonra bir Java uygulamasından veri gönderip alma işlemi hakkında bilgi verilmektedir.

> [!NOTE]
> Kafka’nın şu anda HDInsight ile kullanılabilen iki sürümü vardır: 0.9.0 (HDInsight 3.4) ve 0.10.0 (HDInsight 3.5). Bu belgedeki adımlarda HDInsight 3.5 üzerinde Kafka kullandığınız kabul edilmiştir.

## <a name="prerequisite"></a>Önkoşul

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Bu Apache Kafka öğreticisini başarıyla tamamlamak için şunlara sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **SSH ve SCP hakkında bilgi**. Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) veya OpenJDK gibi eşdeğeri.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>Erişim denetimi gereksinimleri

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Kafka kümesi oluşturma

HDInsight kümesinde Kafka oluşturmak için aşağıdaki adımları kullanın:

1. [Azure portalı](https://portal.azure.com)’ndan **+YENİ**, **Akıllı Özellikler ve Analiz** ve ardından **HDInsight**’ı seçin.
   
    ![HDInsight kümesi oluşturma](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. **Temel bilgiler** dikey penceresinde aşağıdaki bilgileri girin:

    * **Küme Adı**: HDInsight kümesinin adı.
    * **Abonelik**: Kullanılacak abonelik.
    * **Küme oturumu kullanıcı adı** ve **Küme oturumu parolası**: HTTPS üzerinden kümeye erişirken kullanılan oturum açma bilgileri. Ambari Web kullanıcı arabirimi veya REST API gibi hizmetlere erişmek için bu kimlik bilgilerini kullanın.
    * **Güvenli Kabuk (SSH) kullanıcı adı**: SSH üzerinden kümeye erişirken kullanılan oturum açma bilgileri. Varsayılan olarak parola, küme oturum açma parolası ile aynıdır.
    * **Kaynak Grubu**: Kümenin oluşturulduğu kaynak grubu.
    * **Konum**: Kümenin oluşturulacağı Azure bölgesi.
   
    ![Abonelik seçme](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. **Küme türü**’nü seçin, ardından **Küme yapılandırması** dikey penceresinde aşağıdaki değerleri ayarlayın:
   
    * **Küme Türü**: Kafka

    * **Sürüm**: Kafka 0.10.0 (HDI 3.5)

    * **Küme Katmanı**: Standart
     
    Son olarak, **Seç** düğmesini kullanarak ayarları kaydedin.
     
    ![Küme türü seçme](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

    > [!NOTE]
    > Azure aboneliğinizin Kafka önizlemesine erişimi yoksa, önizlemeye erişim kazanma yönergeleri gösterilir. Gösterilen yönergeler aşağıdaki görüntüye benzer:
    >
    > ![önizleme iletisi: HDInsight üzerinde yönetilen bir Apache Kafka kümesi dağıtmak isterseniz, önizleme erişimi için bize e-posta gönderin](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. Küme türünü seçtikten sonra __Seç__ düğmesini kullanarak küme türünü ayarlayın. Ardından, __İleri__ düğmesini kullanarak temel yapılandırmayı tamamlayın.

5. **Depolama** dikey penceresinden bir depolama hesabı seçin veya oluşturun. Bu belgedeki adımlar için bu dikey penceredeki diğer alanları varsayılan değerlerinde bırakın. __İleri__ düğmesini kullanarak depolama yapılandırmasını kaydedin.

    ![HDInsight depolama hesabı ayarlarını belirleme](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. **Özet** dikey penceresinden kümenin yapılandırmasını gözden geçirin. Yanlış olan ayarları değiştirmek için __Düzenle__ bağlantılarını kullanın. Son olarak, __Oluştur__ düğmesini kullanarak kümeyi oluşturun.
   
    ![Küme yapılandırma özeti](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Kümenin oluşturulması 20 dakika sürebilir.

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Kümeye bağlanmak için istemcinizden SSH kullanın. Linux, Unix, MacOS veya Windows 10 üzerine Bash işletim sistemini kullanıyorsanız aşağıdaki komutu yürütün:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

**SSHUSER** ifadesini küme oluşturma sırasında sağladığınız SSH kullanıcı adıyla değiştirin. **CLUSTERNAME** değerini kümenin adıyla değiştirin.

İstendiğinde, SSH hesabı için kullanılan parolayı girin.

Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

##<a id="getkafkainfo"></a>Zookeeper ve Aracı konak bilgilerini alma

Kafka ile çalışırken konak değerlerini bilmeniz gerekir; *Zookeeper* konakları ve *Aracı* konakları. Bu konaklar Kafka API’si ve Kafka ile gönderilen yardımcı programların birçoğu ile birlikte kullanılır.

Konak bilgilerini içeren ortam değişkenlerini oluşturmak için aşağıdaki adımları kullanın. Bu ortam değişkenleri bu belgedeki adımlarda kullanılır.

1. Küme ile SSH bağlantısından aşağıdaki komutu kullanarak `jq` yardımcı programını yükleyin. Bu yardımcı program JSON belgelerini ayrıştırmak için kullanılır ve aracı konak bilgilerini almak için yararlıdır:
   
    ```bash
    sudo apt -y install jq
    ```

2. Ambari’den alınan bilgilerle ortam değişkenlerini ayarlamak için aşağıdaki komutları kullanın. __KAFKANAME__ değerini Kafka kümesinin adıyla değiştirin. __PASSWORD__ değerini kümeyi oluştururken kullandığınız oturum açma (yönetici) parolası ile değiştirin.

    ```bash
    export KAFKAZKHOSTS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

    export KAFKABROKERS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Aşağıdaki metin `$KAFKAZKHOSTS` içeriğinin bir örneğidir:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    Aşağıdaki metin `$KAFKABROKERS` içeriğinin bir örneğidir:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
    > [!WARNING]
    > Bu oturumdan döndürülen bilgileri her zaman doğru olarak kabul etmeyin. Kümeyi ölçeklendirirseniz yeni aracılar eklenir veya kaldırılır. Bir hata oluşur ve bir düğüm değiştirilirse, düğümün konak adı değişebilir. 
    > 
    > Geçerli bilgilere sahip olduğunuzdan emin olmak için, kullanmadan hemen önce Zookeeper ve aracı konakların bilgilerini almanız gerekir.

## <a name="create-a-topic"></a>Konu başlığı oluşturma

Kafka, veri akışlarını *topics* (konu başlıkları) adlı kategorilerde depolar. Kafka ile birlikte verilen betiği kullanarak, bir küme baş düğümüne yönelik SSH bağlantısından konu başlığı oluşturun:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Bu komut, `$KAFKAZKHOSTS` içinde depolanan konak bilgilerini kullanarak Zookeeper’a bağlanır ve ardından **test** adlı Kafka konu başlığını oluşturur. Konu başlıklarını listelemek üzere aşağıdaki betiği kullanarak, konu başlığının oluşturulduğunu doğrulayabilirsiniz:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

Bu komut, **test** konu başlıklarını içeren Kafka konu başlıklarını listeler.

## <a name="produce-and-consume-records"></a>Kayıt oluşturma ve kullanma

Kafka, konu başlıklarında *records* (kayıtlar) depolar. Kayıtlar, *Üreticiler* tarafından oluşturulur ve *tüketiciler* tarafından kullanılır. Üreticiler, kayıtları Kafka *aracılarından* alır. HDInsight kümenizdeki her çalışan düğümü bir Kafka aracısıdır.

Daha önce oluşturduğunuz test konu başlığında kayıt depolamak ve ardından bir tüketici kullanarak bunları okumak için aşağıdaki adımları kullanın:

1. SSH oturumundan, Kafka ile birlikte sağlanan bir betik kullanarak kayıtları konu başlığına yazın:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Bu komuttan sonra isteme geri döndürülmezsiniz. Bunun yerine, birkaç metin iletisi yazın ve ardından **Ctrl + C** tuşlarını kullanarak, konu başlığına göndermeyi durdurun. Her satır ayrı bir kayıt olarak gönderilir.

2. Konu başlığından kayıt okumak için, Kafka ile birlikte sağlanan bir betik kullanın:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
    ```
   
    Bu işlem, kayıtları konu başlığından alır ve görüntüler. `--from-beginning` kullanılması, tüketiciye akışın başından başlamasını söyler, böylece tüm kayıtlar alınır.

3. Tüketiciyi durdurmak için __Ctrl + C__ tuşlarını kullanın.

## <a name="producer-and-consumer-api"></a>Üretici ve tüketici API’si

[Kafka API’lerin,](http://kafka.apache.org/documentation#api) kullanarak, kayıtları programlama yoluyla da üretebilir ve kullanabilirsiniz. Java tabanlı üretici ve tüketici indirip derlemek için aşağıdaki adımları kullanın:

1. Örnekleri [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) sayfasından indirebilirsiniz. Üretici/tüketici örneği için `Producer-Consumer` dizinindeki projeyi kullanın. Bu örnek aşağıdaki sınıfları içerir:
   
    * **Çalıştır** - tüketiciyi veya üreticiyi başlatır.

    * **Producer** (Üretici)- konu başlığında 1.000.000 kayıt depolar.

    * **Consumer** (Tüketici) - konu başlığındaki kayıtları okur.

2. Geliştirme ortamınızdaki komut satırından, dizinleri örnekteki `Producer-Consumer` dizininin konumuna geçirin ve ardından aşağıdaki komutu kullanarak bir jar paketi oluşturun:
   
    ```
    mvn clean package
    ```
   
    Bu komut, `kafka-producer-consumer-1.0-SNAPSHOT.jar` adlı dosyayı içeren `target` adlı bir dizin oluşturur.

3. `kafka-producer-consumer-1.0-SNAPSHOT.jar` dosyasını HDInsight kümenize kopyalamak için aşağıdaki komutları kullanın:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    **SSHUSER** değerini kümenizin SSH kullanıcısı ile, **CLUSTERNAME** değerini kümenizin adıyla değiştirin. İstendiğinde, SSH kullanıcısının parolasını girin.

4. `scp` komutu dosya kopyalamayı tamamladıktan sonra, SSH kullanarak kümeye bağlanın ve ardından, daha önce oluşturduğunuz test konu başlığına kayıtları yazmak için aşağıdaki komutu kullanın.
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS
    ```
   
    Bu komut, üretici ve yazma kayıtlarını başlatır. Kaç tane kaydın yazıldığını görebilmeniz için bir sayaç görüntülenir.

    > [!NOTE]
    > İzin reddedildi hatası alırsanız, dosyayı yürütülebilir hale getirmek için şu komutu kullanın: ```chmod +x kafka-producer-consumer.jar```

5. İşlem tamamlandıktan sonra, konu başlığından okumak için aşağıdaki komutu kullanın:
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Okunan kayıtlar, kayıt sayısıyla birlikte gösterilir. Daha önceki bir adımda, betik kullanarak konu başlığına çok sayıda kayıt gönderdiğiniz için 1.000.000’dan fazla kaydın günlüğe kaydedildiğini görebilirsiniz.

6. Tüketiciden çıkış yapmak için __Ctrl + C__ tuşlarını kullanın.

### <a name="multiple-consumers"></a>Birden çok tüketici

Kafka ile ilgili önemli bir kavram, tüketicilerin kayıtları okurken bir tüketici grubu (grup kimliği ile tanımlanır) kullanmasıdır. Birden çok tüketiciyle aynı grubun kullanılması, konu başlığından yük dengeli okuma yapılmasına neden olur. Gruptaki her bir tüketici, kayıtların bir kısmını alır. Bu işlemi uygulamada görmek için aşağıdaki adımları kullanın:

1. Kümede yeni bir SSH oturumu açın, böylece ikisine birden sahip olursunuz. Her oturumda aynı tüketici grubu kimliğine sahip bir tüketici başlatmak için aşağıdakileri kullanın:
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    > [!NOTE]
    > Bu yeni bir SSH oturumu olduğundan, `$KAFKABROKERS` ayarını yapmak için [Zookeeper ve Aracı konak bilgilerini alma](#getkafkainfo) bölümündeki komutları kullanmanız gerekir.

2. Her oturumun konu başlığından aldığı kayıtları saymasını izleyebilirsiniz. Her iki oturumun toplamı, daha önce bir tüketiciden aldığınız sayıyla aynı olmalıdır.

Aynı gruptaki istemcilerin tüketimi, konu başlığının bölümleri aracılığıyla işlenir. Daha önce oluşturulan `test` konu başlığında sekiz bölüm vardır. Sekiz SSH oturumu açıp tüm oturumlarda bir tüketici başlatırsanız her tüketici, konu başlığının tek bir bölümündeki kayıtları okur.

> [!IMPORTANT]
> Bir tüketici grubunda bölümden daha fazla tüketici örneği olamaz. Bu örnekte, konu başlığındaki bölüm sayısı 8 olduğu için bir tüketici grubu en fazla bu sayıda tüketiciyi içerebilir. Ya da her biri en fazla 8 tüketici içeren birden fazla tüketici grubunuz olabilir.

Kafka’ya depolanan kayıtlar bir bölümde alındıkları sırayla depolanır. *Bir bölüm* içindeki kayıtlar için sıralı teslim sağlamak üzere, tüketici örneklerinin bölüm sayısıyla eşleştiği bir tüketici grubu oluşturun. *Konu başlığı içindeki* kayıtların sıralı teslim edilmesini sağlayabilmek için, yalnızca bir tüketici örneği içeren bir tüketici grubu oluşturun.

## <a name="streaming-api"></a>Akış API’si

Akış API’si Kafka’ya sürüm 0.10.0’da eklenmiştir; önceki sürümler, akış işleme için Apache Spark veya Storm kullanır.

1. Henüz yapmadıysanız, örnekleri [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) sayfasından geliştirme ortamınıza indirin. Akış örneği için `streaming` dizinindeki projeyi kullanın.
   
    Bu proje yalnızca, daha önce `test` konu başlığından kayıtları okuyan `Stream` adlı sınıfı içerir. Okunan sözcükleri sayar ve her sözcük ile sayıyı `wordcounts` adlı konu başlığına iletir. `wordcounts` konu başlığı, bu bölümün sonraki bir adımında oluşturulur.

2. Geliştirme ortamınızdaki komut satırından, dizinleri `Streaming` dizininin konumuna geçirin ve ardından aşağıdaki komutu kullanarak bir jar paketi oluşturun:
   
    ```
    mvn clean package
    ```
   
    Bu komut, `kafka-streaming-1.0-SNAPSHOT.jar` adlı dosyayı içeren `target` adlı bir dizin oluşturur.

3. `kafka-streaming-1.0-SNAPSHOT.jar` dosyasını HDInsight kümenize kopyalamak için aşağıdaki komutları kullanın:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    **SSHUSER** değerini kümenizin SSH kullanıcısı ile, **CLUSTERNAME** değerini kümenizin adıyla değiştirin. İstendiğinde, SSH kullanıcısının parolasını girin.

4. `scp` komutu dosya kopyalamayı tamamladıktan sonra, SSH kullanarak kümeye bağlanın ve ardından aşağıdaki komutu kullanarak `wordcounts` konu başlığını oluşturun:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. Ardından, aşağıdaki komutu kullanarak akış işlemini başlatın:
   
    ```bash
    ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    Bu komut, arka planda akış işlemini başlatır.

6. `test` konu başlığına ileti göndermek için aşağıdaki komutu kullanın. Bu iletiler akış örneği tarafından işlenir:
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. Akış işlemi tarafından `wordcounts` konu başlığına yazılan çıktıyı görüntülemek için aşağıdaki komutu kullanın:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > Verileri görüntülemek için, tüketiciye anahtar ve değer için kullanılacak anahtarı ve seri durumdan çıkarma aracını yazdırmasını söylemeniz gerekir. Anahtar adı sözcüktür ve anahtar değeri sayıyı içerir.
   
    Çıktı aşağıdaki metne benzer:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
        a       13805
        snow    13637
   
    > [!NOTE]
    > Bir sözcükle karşılaşılan her durumda sayı artar.

7. Tüketiciden çıkmak için __Ctrl + C__ tuşlarını kullanın, ardından `fg` komutunu kullanarak akış arka plan görevini ön plana geri getirin. Çıkış yapmak için de __Ctrl + C__ tuşlarını kullanın.

## <a name="delete-the-cluster"></a>Küme silme

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, HDInsight üzerinde Apache Kafka ile çalışmanın temel bilgilerini öğrendiniz. Kafka ile çalışma hakkında daha fazla bilgi için aşağıdakileri kullanın:

* kafka.apache.org adresindeki [Apache Kafka belgeleri](http://kafka.apache.org/documentation.html).
* [MirrorMaker kullanarak HDInsight üzerinde Kafka kopyası oluşturma](hdinsight-apache-kafka-mirroring.md)
* [Apache Storm’u HDInsight üzerinde Kafka ile kullanma](hdinsight-apache-storm-with-kafka.md)
* [Apache Spark’ı HDInsight üzerinde Kafka ile kullanma](hdinsight-apache-spark-with-kafka.md)


