---
title: Yansıtma Apache Kafka konuları-Azure HDInsight
description: Bir ikincil kümeye konuları yansıtarak HDInsight kümesinde bir Kafka çoğaltmasını sürdürmek için Apache Kafka yansıtma özelliğini kullanmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: 270bc5401e58f4e5c99cae3c5ab06b4f03ae9543
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123235"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>HDInsight üzerinde Kafka ile Apache Kafka konuları çoğaltmak için MirrorMaker kullanın

Apache Kafka yansıtma özelliğini kullanarak konuları ikincil bir kümeye nasıl çoğaltacağınızı öğrenin. Yansıtma sürekli bir işlem olarak çalıştırılabilir veya bir kümeden diğerine veri geçirme yöntemi olarak zaman zaman kullanılabilir.

Bu örnekte, iki HDInsight kümesi arasındaki konuları çoğaltmak için yansıtma kullanılır. Her iki küme de farklı veri merkezlerinde farklı sanal ağlarda bulunur.

> [!WARNING]  
> Yansıtma, hata toleransı elde etmek için bir yol olarak değerlendirilmemelidir. Bir konu içindeki öğelerin farkı, birincil ve ikincil kümeler arasında farklıdır, bu nedenle istemciler iki birbirlerinin yerine kullanamaz.
>
> Hataya dayanıklılık konusunda endişeleriniz varsa, kümenizin içindeki konular için çoğaltma ayarlamanız gerekir. Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka yansıtma nasıl kullanılır

Yansıtma, birincil kümedeki konulardaki kayıtları tüketmek ve sonra ikincil kümede yerel bir kopya oluşturmak için, [Mirroroluşturucu](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) aracı (Apache Kafka bir parçası) kullanılarak işe yarar. MirrorMaker birincil kümeden okuyan bir (veya daha fazla) *Tüketici* ve yerel (ikincil) kümeye yazan bir *üretici* kullanır.

Olağanüstü durum kurtarma için en yararlı yansıtma kurulumu, farklı Azure bölgelerinde Kafka kümelerinden yararlanır. Bunu başarmak için kümelerin bulunduğu sanal ağlar birlikte işlenir.

Aşağıdaki diyagramda, yansıtma işlemi ve iletişimin kümeler arasında nasıl akabileceği gösterilmektedir:

![Yansıtma işleminin diyagramı](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Birincil ve ikincil kümeler düğüm ve bölüm sayısında farklı olabilir ve konuların içindeki uzaklıklar da farklı olabilir. Yansıtma, bölümleme için kullanılan anahtar değerini korur, bu nedenle kayıt sırası anahtar başına temelinde korunur.

### <a name="mirroring-across-network-boundaries"></a>Ağ sınırları genelinde yansıtma

Farklı ağlardaki Kafka kümeleri arasında yansıtma yapmanız gerekiyorsa, aşağıdaki ek konular vardır:

* **Ağ geçitleri**: Ağların TCP/IP düzeyinde iletişim kurabilmesi gerekir.

* **Sunucu adresleme**: Küme düğümlerinizin IP adreslerini veya tam etki alanı adlarını kullanarak adreslemek üzere seçim yapabilirsiniz.

    * **IP adresleri**: Kafka kümelerinizi IP adresi tanıtımı kullanacak şekilde yapılandırırsanız, aracı düğümlerinin ve Zookeeper düğümlerinin IP adreslerini kullanarak yansıtma kurulumuna devam edebilirsiniz.
    
    * **Etki alanı adları**: IP adresi tanıtımı için Kafka kümelerinizi yapılandırmazsanız kümelerin, tam etki alanı adlarını (FQDN) kullanarak birbirlerine bağlanabilmesi gerekir. Bu, istekleri diğer ağlara iletmek üzere yapılandırılmış her bir ağda bir etki alanı adı sistemi (DNS) sunucusu gerektirir. Bir Azure sanal ağı oluştururken, ağla birlikte sunulan otomatik DNS kullanmak yerine, sunucu için özel bir DNS sunucusu ve IP adresi belirtmeniz gerekir. Sanal ağ oluşturulduktan sonra, bu IP adresini kullanan bir Azure sanal makinesi oluşturmanız ve ardından DNS yazılımını bu IP ile yapılandırmanız gerekir.

    > [!WARNING]  
    > HDInsight 'ı sanal ağa yüklemeden önce özel DNS sunucusu oluşturun ve yapılandırın. HDInsight 'ın sanal ağ için yapılandırılmış DNS sunucusunu kullanması için ek bir yapılandırma gerekmez.

İki Azure sanal ağını bağlama hakkında daha fazla bilgi için bkz. [VNET-VNET bağlantısını yapılandırma](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Yansıtma mimarisi

Bu mimari, farklı kaynak gruplarındaki ve sanal ağlardaki iki kümeyi sunar: **birincil** ve **İkincil**.

### <a name="creation-steps"></a>Oluşturma adımları

1. İki yeni kaynak grubu oluşturun:

    |Kaynak Grubu | Location |
    |---|---|
    | Kafka-birincil-RG | Orta ABD |
    | Kafka-ikincil-RG | Orta Kuzey ABD |

1. **Kafka-Primary-RG**içinde yeni bir sanal ağ **Kafka-Primary-VNET** oluşturun. Varsayılan ayarları bırakın.
1. Aynı zamanda varsayılan ayarlarla, **Kafka-Secondary-RG**içinde yeni bir sanal ağ **Kafka-Secondary-VNET** oluşturun.

1. İki yeni Kafka kümesi oluşturun:

    | Küme adı | Kaynak Grubu | Sanal Ağ | Depolama Hesabı |
    |---|---|---|---|
    | Kafka-birincil-küme | Kafka-birincil-RG | Kafka-birincil-VNET | kafkaprimarystorage |
    | Kafka-ikincil küme | Kafka-ikincil-RG | Kafka-ikincil-VNET | kafkasecondarystorage |

1. Sanal ağ eşlemesi oluşturun. Bu adım iki eşleme oluşturur: biri **Kafka-Primary-VNET** 'ten **Kafka-ikincil-VNET** 'e, diğeri **Kafka-ikincil VNET** 'ten **Kafka-Primary-VNET**'e kadar bir geri.
    1. **Kafka-Primary-VNET** sanal ağını seçin.
    1. Ayarlar **altında eşlemeler** 'e tıklayın.
    1. **Ekle**'yi tıklatın.
    1. **Eşleme Ekle** ekranında, aşağıdaki ekran görüntüsünde gösterildiği gibi ayrıntıları girin.

        ![HDInsight Kafka VNET eşlemesi Ekle](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

1. IP tanıtımı yapılandırın:
    1. Birincil kümenin ambarı panosuna gidin: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. **Hizmetler** > **Kafka**' e tıklayın. **Configs** sekmesine tıklayın.
    1. Aşağıdaki yapılandırma satırlarını alt **Kafka-env şablonu** bölümüne ekleyin. **Kaydet**’e tıklayın.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. **Yapılandırma kaydet** ekranına bir Note girin ve **Kaydet**' e tıklayın.
    1. Yapılandırma Uyarısı istenirse, **yine de devam et**' e tıklayın.
    1. **Yapılandırma değişikliklerini kaydet**sayfasında **Tamam** ' a tıklayın.
    1. Yeniden başlatma gerekli bildiriminde**etkilenen tüm** **Yeniden Başlat ' a**tıklayın.  >  **Tümünü yeniden başlatmayı Onayla**' ya tıklayın.

        ![Apache ambarı yeniden başlatma etkilendi](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Tüm ağ arabirimlerini dinlemek için Kafka yapılandırın.
    1. **Hizmetler** Kafka'ninaltındakiconfigs > sekmesinde kalın. **Kafka Broker** bölümünde **Listeners** özelliğini olarak `PLAINTEXT://0.0.0.0:9092`ayarlayın.
    1. **Kaydet**’e tıklayın.
    1. **Yeniden Başlat**' a tıklayıp **Tümünü Yeniden Başlat**' ı onaylayın.

1. Birincil küme için Kayıt Aracısı IP adreslerini ve Zookeeper adreslerini kaydeder.
    1. Ambarı panosundaki **konaklar** ' a tıklayın.
    1. Aracılar ve zookeepers için IP adreslerini bir yere göz önünde yapın. Aracı düğümlerinin, ana bilgisayar adının ilk iki harfi ve Zookeeper düğümleri ana bilgisayar adının ilk iki harfi olarak **ZK** **'a sahiptir.**

        ![Apache ambarı görünümü düğüm IP adresleri](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. İkinci küme için önceki üç adımı tekrarlayın **Kafka-ikincil-küme**: IP reklacılarını yapılandırın, dinleyicileri ayarlayın ve Broker ve Zookeeper IP adreslerini bir yere göz önünde yapın.

## <a name="create-topics"></a>Konu başlıkları oluştur

1. SSH kullanarak **birincil** kümeye bağlanma:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    **Sshuser** öğesini kümeyi oluştururken kullanılan SSH kullanıcı adıyla değiştirin. **BASENAME** değerini, kümeyi oluştururken kullanılan temel adla değiştirin.

    Bilgi için bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Birincil küme için Apache Zookeeper konaklarıyla bir değişken oluşturmak için aşağıdaki komutu kullanın. Benzer `ZOOKEEPER_IP_ADDRESS1` dizeler, `10.23.0.11` ve `10.23.0.7`gibi daha önce kaydedilen gerçek IP adresleriyle değiştirilmelidir. Özel bir DNS sunucusu ile FQDN çözümlemesi kullanıyorsanız, aracı ve Zookeeper adlarını almak için [aşağıdaki adımları](apache-kafka-get-started.md#getkafkainfo) izleyin.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Adlı `testtopic`bir konu oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. Konunun oluşturulduğunu doğrulamak için aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Yanıt içerir `testtopic`.

4. Bu ( **birincil**) küme için Zookeeper ana bilgisayar bilgilerini görüntülemek için aşağıdakileri kullanın:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Bu, aşağıdaki metne benzer bilgiler döndürür:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Bu bilgileri kaydedin. Sonraki bölümde kullanılır.

## <a name="configure-mirroring"></a>Yansıtmayı yapılandırma

1. Farklı bir SSH oturumu kullanarak **İkincil** kümeye bağlanın:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    **Sshuser** öğesini kümeyi oluştururken kullanılan SSH kullanıcı adıyla değiştirin. **Secondarycluster** öğesini kümeyi oluştururken kullanılan adla değiştirin.

    Bilgi için bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Birincil `consumer.properties` kümeyle iletişimi yapılandırmak için bir dosya kullanılır. Dosyayı oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    nano consumer.properties
    ```

    `consumer.properties` Dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    **PRIMARY_ZKHOSTS** değerini **BIRINCIL** kümeden Zookeeper IP adresleriyle değiştirin.

    Bu dosya, birincil Kafka kümesinden okurken kullanılacak tüketici bilgilerini açıklamaktadır. Daha fazla bilgi için tüketici yapılandırması, kafka.apache.org adresindeki [Tüketici yapılandırmaları](https://kafka.apache.org/documentation#consumerconfigs) bölümüne bakın.

    Dosyayı kaydetmek için **CTRL + X**, **Y**kullanın ve ardından **girin**.

3. İkincil kümeyle iletişim kuran üreticisi yapılandırmadan önce, **İkincil** KÜMENIN aracı IP adresleri için bir değişken ayarlayın. Bu değişkeni oluşturmak için aşağıdaki komutları kullanın:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Komut `echo $SECONDARY_BROKERHOSTS` aşağıdaki metne benzer bilgiler döndürmelidir:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. İkincil `producer.properties` kümeyi iletmek için bir dosya kullanılır . Dosyayı oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    nano producer.properties
    ```

    `producer.properties` Dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    **SECONDARY_BROKERHOSTS** değerini, önceki adımda kullanılan aracı IP adresleriyle değiştirin.

    Daha fazla bilgi üreticisi yapılandırması için kafka.apache.org adresindeki [Producer configs](https://kafka.apache.org/documentation#producerconfigs) bölümüne bakın.

5. İkincil küme için Zookeeper konaklarının IP adreslerine sahip bir ortam değişkeni oluşturmak için aşağıdaki komutları kullanın:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. HDInsight üzerinde Kafka için varsayılan yapılandırma, konuların otomatik olarak oluşturulmasına izin vermez. Yansıtma işlemini başlatmadan önce aşağıdaki seçeneklerden birini kullanmanız gerekir:

    * **İkincil kümede konuları oluşturun**: Bu seçenek ayrıca bölüm sayısını ve çoğaltma faktörünü ayarlamanıza olanak sağlar.

        Aşağıdaki komutu kullanarak zaman içinde daha fazla konu oluşturabilirsiniz:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Oluşturulacak `testtopic` konunun adıyla değiştirin.

    * **Kümeyi otomatik konu oluşturma Için yapılandırın**: Bu seçenek, MirrorMaker 'ın otomatik olarak konu oluşturmasını sağlar, ancak bunları, birincil konudan farklı sayıda bölüm veya çoğaltma faktörüyle oluşturabilir.

        İkincil kümeyi otomatik olarak konu başlıkları oluşturacak şekilde yapılandırmak için aşağıdaki adımları uygulayın:

        1. İkincil kümenin ambarı panosuna gidin: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. **Hizmetler** > **Kafka**' e tıklayın. **Configs** sekmesine tıklayın.
        5. __Filtre__ alanına bir değeri `auto.create`girin. Bu, Özellik listesini filtreler ve `auto.create.topics.enable` ayarı görüntüler.
        6. Değerini `auto.create.topics.enable` true olarak değiştirin ve ardından __Kaydet__' i seçin. Bir Note ekleyin ve sonra yeniden __Kaydet__ ' i seçin.
        7. __Kafka__ hizmetini seçin, __Yeniden Başlat__' ı seçin ve ardından __etkilenen tümünü yeniden Başlat__' ı seçin. İstendiğinde, __Tümünü Yeniden Başlat__' ı seçin.

        ![Kafka otomatik oluşturma konularını etkinleştir](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>MirrorMaker 'ı Başlat

1. SSH bağlantısından **İkincil** kümeye, mirrormaker işlemini başlatmak için aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Bu örnekte kullanılan parametreler şunlardır:

    * **--Consumer. config**: Tüketici özelliklerini içeren dosyayı belirtir. Bu özellikler, *birincil* Kafka kümesinden okuyan bir tüketici oluşturmak için kullanılır.

    * **--Producer. config**: Üretici özelliklerini içeren dosyayı belirtir. Bu özellikler, *İkincil* Kafka kümesine yazan bir üretici oluşturmak için kullanılır.

    * **--beyaz liste**: MirrorMaker 'ın birincil kümeden ikincil kümeye çoğaltıldığı konuların listesi.

    * **--say. akışlar**: Oluşturulacak tüketici iş parçacığı sayısı.

    İkincil düğümdeki tüketici artık ileti almayı bekliyor.

2. SSH bağlantısından **birincil** kümeye aşağıdaki komutu kullanarak bir üretici başlatın ve konuya ileti gönderin:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Bir imlece sahip boş bir satıra geldiğinizde birkaç metin mesajı yazın. İletiler, **birincil** kümedeki konuya gönderilir. İşiniz bittiğinde, Producer işlemini sonlandırmak için **CTRL + C** tuşlarını kullanın.

3. SSH bağlantısından **İkincil** kümeye, mirrormaker işlemini sonlandırmak için **CTRL + C** kullanın. İşlemin bitmesi birkaç saniye sürebilir. İletilerin ikinciye çoğaltıldığından emin olmak için aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Bu konu başlığı altında `testtopic`, mirrormaster konuyu ikincil kümeden ikinciye yansıtdığına göre oluşturulan konuların listesi. Konudan alınan iletiler, birincil kümede girdikleriyle aynıdır.

## <a name="delete-the-cluster"></a>Küme silme

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Bu belgedeki adımlar farklı Azure Kaynak gruplarında kümeler oluşturdu. Oluşturulan tüm kaynakları silmek için, oluşturulan iki kaynak grubunu silebilirsiniz: **Kafka-Primary-RG** ve **Kafka-secondary_rg**. Kaynak gruplarını silmek, kümeler, sanal ağlar ve depolama hesapları da dahil olmak üzere bu belgeyi izleyerek oluşturulan tüm kaynakları kaldırır.

## <a name="next-steps"></a>Sonraki Adımlar

Bu belgede, [Apache Kafka](https://kafka.apache.org/) kümesinin bir çoğaltmasını oluşturmak Için [mirrormaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 'ın nasıl kullanılacağını öğrendiniz. Kafka ile çalışmanın diğer yollarını saptamak için aşağıdaki bağlantıları kullanın:

* Cwiki.apache.org adresinde [MirrorMaker belgeleri Apache Kafka](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) .
* [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md)
* [HDInsight üzerinde Apache Kafka ile Apache Spark kullanma](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight üzerinde Apache Kafka ile Apache Storm kullanma](../hdinsight-apache-storm-with-kafka.md)
* [Azure sanal ağı üzerinden Apache Kafka bağlanma](apache-kafka-connect-vpn-gateway.md)
