---
title: "Hızlı başlangıç: Azure portal kullanarak HDInsight 'ta Apache Kafka ayarlama"
description: Bu hızlı başlangıçta, Azure portalını kullanarak Azure HDInsight’ta bir Apache Kafka kümesi oluşturmayı öğrenirsiniz. Kafka konuları, aboneleri ve tüketicileri hakkında da bilgi edinirsiniz.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: acb497b2f0111d36650ec415c8f1f580d116b55e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863301"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak Azure HDInsight 'ta Apache Kafka kümesi oluşturma

[Apache Kafka](./apache-kafka-introduction.md) , açık kaynaklı, dağıtılmış bir akış platformudur. Yayımla-abone ol ileti kuyruğuna benzer işlevler sağladığı için genellikle ileti aracısı olarak kullanılır.

Bu hızlı başlangıçta, Azure portal kullanarak Apache Kafka kümesi oluşturmayı öğreneceksiniz. Ayrıca Apache Kafka kullanarak ileti göndermek ve almak için verilen yardımcı programları kullanmayı da öğrenirsiniz. Kullanılabilir yapılandırmaların derinlemesine açıklamaları için bkz. [HDInsight 'ta kümeleri ayarlama](../hdinsight-hadoop-provision-linux-clusters.md). Küme oluşturmak üzere portalın kullanımıyla ilgili ek bilgiler için, bkz. [portalda kümeler oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Apache Kafka API'sine yalnızca aynı sanal ağ içindeki kaynaklar tarafından erişilebilir. Bu hızlı başlangıçta, SSH kullanarak kümeye doğrudan erişirsiniz. Diğer hizmetleri, ağları veya sanal makineleri Apache Kafka'ya bağlamak için önce bir sanal ağ oluşturmanız e sonra ağ içinde kaynakları oluşturmanız gerekir. Daha fazla bilgi için [Sanal ağ kullanarak Apache Kafka'ya bağlanma](apache-kafka-connect-vpn-gateway.md) belgesine bakın. HDInsight için sanal ağları planlama hakkında daha fazla genel bilgi için bkz. [Azure HDInsight için sanal ağ](../hdinsight-plan-virtual-network-deployment.md)planlama.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka kümesi oluşturma

HDInsight üzerinde Apache Kafka kümesi oluşturmak için aşağıdaki adımları kullanın:

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Üstteki menüden **+ kaynak oluştur**' u seçin.

    :::image type="content" source="./media/apache-kafka-get-started/azure-portal-create-resource.png" alt-text="Azure portal kaynak HDInsight oluşturma" border="true":::

1. **Analiz**  >  **Azure HDInsight** ' ı seçerek **HDInsight kümesi oluşturma** sayfasına gidin.

1. **Temel bilgiler** sekmesinde, aşağıdaki bilgileri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |Abonelik    |  Aşağı açılan listeden, küme için kullanılan Azure aboneliğini seçin. |
    |Kaynak grubu     | Bir kaynak grubu oluşturun veya mevcut bir kaynak grubunu seçin.  Kaynak grubu, Azure bileşenleri için bir kapsayıcıdır.  Bu durumda, kaynak grubu HDInsight kümesini ve bağımlı Azure Depolama hesabını içermektedir. |
    |Küme adı   | Genel olarak benzersiz bir ad girin. Ad, harf, sayı ve kısa çizgi gibi en fazla 59 karakter içerebilir. Adın ilk ve son karakterleri, kısa çizgi olamaz. |
    |Region    | Aşağı açılan listeden, kümenin oluşturulduğu bir bölge seçin.  Daha iyi performans için size daha yakın bir bölge seçin. |
    |Küme türü| Liste açmak için **küme türünü seç** ' i seçin. Listeden küme türü olarak **Kafka** ' yi seçin.|
    |Sürüm|Küme türü için varsayılan sürüm belirtilecektir. Farklı bir sürüm belirtmek istiyorsanız, açılan listeden seçim yapın.|
    |Küme oturum açma kullanıcı adı ve parolası    | Varsayılan oturum açma adı **admin**' dir. Parola en az 10 karakter uzunluğunda olmalıdır ve en az bir rakam, bir büyük harf ve bir küçük harf, bir alfasayısal olmayan karakter (' "' karakterleri dışında) içermelidir \) . "Pass@word1" gibi genel parolalar **sağlamadığınızdan** emin olun.|
    |Secure Shell (SSH) kullanıcı adı | Varsayılan kullanıcı adı **sshuser** şeklindedir.  SSH kullanıcı adı için başka bir ad sağlayabilirsiniz. |
    |SSH için küme oturum açma parolasını kullanma| SSH kullanıcısı için, küme oturum açma kullanıcısı için sağladınız aynı parolayı kullanmak üzere bu onay kutusunu işaretleyin.|

   :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-basics.png" alt-text="Azure portal küme temelleri oluşturma" border="true":::

    Her Azure bölgesi (konum) _hata etki alanları_ sağlar. Hata etki alanı, bir Azure veri merkezinde temel donanımlardan oluşan mantıksal bir gruplandırmadır. Her hata etki alanı ortak bir güç kaynağı ve ağ anahtarına sahiptir. Bir HDInsight kümesi içindeki düğümleri uygulayan sanal makineler ve yönetilen diskler, bu hata etki alanlarına dağıtılır. Bu mimari, fiziksel donanım hatalarının olası etkisini sınırlar.

    Verilerin yüksek kullanılabilirliği için, __üç hata etki alanı__ içeren bir bölge (konum) seçin. Bir bölgedeki hata etki alanlarının sayısı hakkında bilgi almak için [Linux sanal makinelerinin kullanılabilirliği](../../virtual-machines/availability.md) belgesine bakın.

    Depolama ayarlarına ilerlemek için **Sonraki: depolama >>** sekmesini seçin.

1. **Depolama** sekmesinden aşağıdaki değerleri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |Birincil depolama türü|Varsayılan **Azure Storage** değerini kullanın.|
    |Seçim yöntemi|Varsayılan değer **listesinden Seç ' i** kullanın.|
    |Birincil depolama hesabı|Açılan listeyi kullanarak mevcut bir depolama hesabını seçin veya **Yeni oluştur**' u seçin. Yeni bir hesap oluşturursanız, ad 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir|
    |Kapsayıcı|Otomatik doldurulmuş değeri kullanın.|

    :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-storage.png " alt-text="HDInsight Linux kullanmaya başlama küme depolama değerlerini sağlama" border="true":::

    **Güvenlik + ağ** sekmesini seçin.

1. Bu hızlı başlangıç için varsayılan güvenlik ayarlarını bırakın. Kurumsal Güvenlik paketi hakkında daha fazla bilgi edinmek için [Azure Active Directory Domain Services'i kullanarak bir HDInsight kümesi ile Kurumsal Güvenlik Paketi yapılandırma](../domain-joined/apache-domain-joined-configure-using-azure-adds.md) sayfasını ziyaret edin. Apache Kafka disk şifrelemesi için kendi anahtarınızı nasıl kullanacağınızı öğrenmek için, [müşteri tarafından yönetilen anahtar disk şifrelemesini](../disk-encryption.md) ziyaret edin

   Kümenizi bir sanal ağa bağlamak istiyorsanız, aşağı açılan **Sanal ağ** listesinden bir sanal ağ seçin.

   :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png" alt-text="Sanal ağa küme ekleme" border="true":::

    **Yapılandırma + fiyatlandırma** sekmesini seçin.

1. HDInsight üzerinde Apache Kafka kullanılabilirliğini güvence altına almak için, **çalışan düğümü** için __düğüm sayısı__ girişinin 3 veya daha büyük olarak ayarlanması gerekir. Varsayılan değer 4'tür.

    **Çalışan düğümü başına standart disk** girdisi, hdınsight üzerinde Apache Kafka ölçeklenebilirliğini yapılandırır. HDInsight üzerinde Apache Kafka, veri depolamak için kümedeki sanal makinelerin yerel diskini kullanır. Apache Kafka yoğun G/Ç kullandığından yüksek aktarım hızı ve düğüm başına daha fazla depolama alanı sağlamak için [Azure Yönetilen Diskler](../../virtual-machines/managed-disks-overview.md) kullanılır. Yönetilen diskin türü __Standart__ (HDD) veya __Premium__ (SSD) olabilir. Disk türü, çalışan düğümler (Apache Kafka aracıları) tarafından kullanılan sanal makine boyutuna bağlıdır. Premium diskler otomatik olarak DS ve GS serisi sanal makinelerle kullanılır. Diğer tüm VM türleri standart disk kullanır.

   :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png" alt-text="Apache Kafka küme boyutunu ayarlama" border="true":::

    **Gözden geçir + oluştur** sekmesini seçin.

1. Kümenin yapılandırmasını gözden geçirin. Yanlış olan ayarları değiştirin. Son olarak, kümeyi oluşturmak için **Oluştur** ' u seçin.

    :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png" alt-text="Kafka kümesi yapılandırma özeti" border="true":::

    Kümenin oluşturulması 20 dakika sürebilir.

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

1. Kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. İstendiğinde, SSH kullanıcısının parolasını girin.

    Bağlandığında, aşağıdaki metne benzer bilgiler görürsünüz:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Apache Zookeeper ve aracı ana bilgisayar bilgilerini al

Kafka ile çalışırken *Apache Zookeeper* ve *Aracı* konaklarınızı bilmeniz gerekir. Bu konaklar Apache Kafka API'si ve Kafka ile gönderilen yardımcı programların birçoğu ile birlikte kullanılır.

Bu bölümde, küme üzerindeki Apache ambarı REST API ana bilgisayar bilgilerini alırsınız.

1. Komut satırı JSON işlemcisi olan [JQ](https://stedolan.github.io/jq/)'yi yükler. Bu yardımcı program JSON belgelerini ayrıştırmak için kullanılır ve ana bilgisayar bilgilerini Ayrıştırmada faydalıdır. Açık SSH bağlantısından yüklemek için aşağıdaki komutu girin `jq` :

    ```bash
    sudo apt -y install jq
    ```

1. Parola değişkenini ayarlayın. `PASSWORD`Küme oturum açma parolasıyla değiştirin, ardından şu komutu girin:

    ```bash
    export password='PASSWORD'
    ```

1. Doğru şekilde oluşturulmuş küme adını ayıklayın. Kümenin nasıl oluşturulduğuna bağlı olarak, küme adının gerçek büyük küçük harfleri beklediğinizden farklı olabilir. Bu komut gerçek büyük küçük harf elde eder ve bir değişkende depolar. Aşağıdaki komutu girin:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Bu işlemi küme dışından yapıyorsanız, küme adını depolamak için farklı bir yordam vardır. Azure portal küme adını küçük harf olarak alın. Ardından, aşağıdaki komutta için küme adını değiştirin `<clustername>` ve yürütün: `export clusterName='<clustername>'` .


1. Bir ortam değişkenini Zookeeper ana bilgisayar bilgileriyle ayarlamak için aşağıdaki komutu kullanın. Komut tüm Zookeeper Konakları alır, ardından yalnızca ilk iki girişi döndürür. Bunun nedeni, bir ana bilgisayarın ulaşılamaz olması durumunda yedeklilik istemenizdir.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Bu komut, ambarı erişimi gerektirir. Kümeniz bir NSG 'nin arkasındaysa, bu komutu ambarı erişebilen bir makineden çalıştırın.

1. Ortam değişkeninin düzgün şekilde ayarlandığını doğrulamak için aşağıdaki komutu kullanın:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Bu komutun aşağıdaki metne benzer bilgiler döndürmesi gerekir:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Bir ortam değişkenini Apache Kafka aracı konak bilgileriyle ayarlamak için aşağıdaki komutu kullanın:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Bu komut, ambarı erişimi gerektirir. Kümeniz bir NSG 'nin arkasındaysa, bu komutu ambarı erişebilen bir makineden çalıştırın.

1. Ortam değişkeninin düzgün şekilde ayarlandığını doğrulamak için aşağıdaki komutu kullanın:

    ```bash
    echo $KAFKABROKERS
    ```

    Bu komutun aşağıdaki metne benzer bilgiler döndürmesi gerekir:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Apache Kafka konularını yönetme

Kafka, veri akışlarını *konular* içinde depolar. Konuları yönetmek için `kafka-topics.sh` yardımcı programını kullanabilirsiniz.

* **Bir konu oluşturmak için**, SSH bağlantısında aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Bu komut, `$KAFKAZKHOSTS` içinde depolanan konak bilgilerini kullanarak Zookeeper’a bağlanır. Daha sonra **test** adlı bir Apache Kafka konusu oluşturur.

    * Bu konuda depolanan veriler, sekiz bölüm halinde bölümlenir.

    * Her bölüm, kümedeki üç çalışan düğümü arasında çoğaltılır.

        * Üç hata etki alanı sağlayan bir Azure bölgesinde kümeyi oluşturduysanız, 3 çoğaltma katsayısını kullanın. Aksi takdirde 4 çoğaltma katsayısını kullanın.
        
        * Üç hata etki alanı içeren bölgelerde 3 çoğaltma katsayısı, çoğaltmaların hata etki alanları arasında yayılmasına olanak sağlar. İki hata etki alanı içeren bölgelerde dört çoğaltma katsayısı, çoğaltmaların etki alanları arasında eşit şekilde yayılmasına olanak sağlar.
        
        * Bir bölgedeki hata etki alanlarının sayısı hakkında bilgi almak için [Linux sanal makinelerinin kullanılabilirliği](../../virtual-machines/availability.md) belgesine bakın.

        * Apache Kafka, Azure hata etki alanları ile uyumlu değildir. Konular için bölüm çoğaltmaları oluşturulurken, çoğaltmalar yüksek kullanılabilirlik için düzgün şekilde dağıtılmayabilir.

        * Yüksek kullanılabilirlik sağlamak için [Apache Kafka bölüm yeniden dengeleme aracını](https://github.com/hdinsight/hdinsight-kafka-tools)kullanın. Bu araç, Apache Kafka kümenizin baş düğümüyle kurulan bir SSH bağlantısından çalıştırılmalıdır.

        * Apache Kafka verilerinizin en yüksek kullanılabilirliğe sahip olması için aşağıdaki durumlarda konunuz için bölüm çoğaltmalarını yeniden dengelemeniz gerekir:

            * Yeni bir konu veya bölüm oluşturduğunuzda

            * Bir kümenin ölçeğini artırdığınızda

* **Konuları listelemek için** aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Bu komut, Apache Kafka kümesinde bulunan konuları listeler.

* **Bir konuyu silmek için** aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Bu komut, `topicname` adlı konuyu siler.

    > [!WARNING]  
    > Daha önce oluşturulan `test` konusunu silerseniz, yeniden oluşturmanız gerekir. Bu belgenin ilerleyen kısmındaki adımlarda kullanılacaktır.

`kafka-topics.sh` yardımcı programı ile kullanılabilen komutlar hakkında daha fazla bilgi için aşağıdaki komutu kullanın:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Kayıt oluşturma ve kullanma

Kafka, konu başlıklarında *records* (kayıtlar) depolar. Kayıtlar, *Üreticiler* tarafından oluşturulur ve *tüketiciler* tarafından kullanılır. Üreticiler ve tüketiciler, *Kafka aracısı* hizmetiyle iletişim kurar. HDInsight kümenizdeki her çalışan düğümü bir Apache Kafka aracı konağıdır.

Daha önce oluşturduğunuz test konu başlığında kayıtları depolamak ve ardından bir tüketici kullanarak bunları okumak için aşağıdaki adımları kullanın:

1. Konuya kayıtlar yazmak için SSH bağlantısından `kafka-console-producer.sh` yardımcı programını kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Bu komuttan sonra boş bir satıra ulaşırsınız.

2. Boş satıra bir metin iletisi yazın ve Enter tuşuna basın. Bu şekilde birkaç ileti girin ve sonra normal isteme geri dönmek için **Ctrl + C** tuşlarını kullanın. Her satır, Apache Kafka konusuna ayrı bir kayıt olarak gönderilir.

3. Konudan kayıtları okumak için SSH bağlantısından `kafka-console-consumer.sh` yardımcı programını kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Bu komutla, kayıtlar konu başlığından alınır ve görüntülenir. `--from-beginning` kullanılması, tüketiciye akışın başından başlamasını söyler, böylece tüm kayıtlar alınır.

    Kafka’nın eski bir sürümünü kullanıyorsanız `--bootstrap-server $KAFKABROKERS` değerini `--zookeeper $KAFKAZKHOSTS` ile değiştirin.

4. Tüketiciyi durdurmak için __Ctrl + C__ tuşlarını kullanın.

Ayrıca programlı olarak üretici ve tüketici de oluşturabilirsiniz. Bu API 'nin kullanımıyla ilgili bir örnek için bkz. [HDInsight Ile tüketici API 'si Apache Kafka üreticisi](apache-kafka-producer-consumer-api.md) belgesi.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç tarafından oluşturulan kaynakları temizlemek için kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, ilişkili HDInsight kümesini ve kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

Azure portalını kullanarak kaynak grubunu kaldırmak için:

1. Azure portalında sol taraftaki menüyü genişleterek hizmet menüsünü açın ve sonra __Kaynak Grupları__'nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynak grubunu bulun ve sonra listenin sağ tarafındaki __Daha fazla__ düğmesine (...) sağ tıklayın.
3. __Kaynak grubunu sil__'i seçip onaylayın.

> [!WARNING]  
> HDInsight üzerinde Apache Kafka kümesi silindiğinde, Kafka içinde depolanan tüm veriler silinir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Apache Kafka ile Apache Spark kullanma](../hdinsight-apache-kafka-spark-structured-streaming.md)