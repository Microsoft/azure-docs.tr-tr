---
title: Öğretici-Apache Kafka & Kurumsal Güvenlik-Azure HDInsight
description: Öğretici-Azure HDInsight 'ta Kurumsal Güvenlik Paketi ile Kafka için Apache Ranger ilkelerini yapılandırmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: cb99b747cb5de01c616c4cab0ac6c14823f7d4db
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044623"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Öğretici: HDInsight 'ta Kurumsal Güvenlik Paketi ile Apache Kafka ilkeleri yapılandırma (Önizleme)

Kurumsal Güvenlik Paketi (ESP) Apache Kafka kümeleri için Apache Ranger ilkelerini nasıl yapılandıracağınızı öğrenin. ESP kümeleri bir etki alanına bağlıdır ve kullanıcıların etki alanı kimlik bilgileriyle kimlik doğrulaması yapmasına olanak sağlar. Bu öğreticide, `sales` ve `marketingspend` konularına erişimi kısıtlamak için iki Ranger ilkesi oluşturacaksınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Etki alanı kullanıcılarını oluşturma
> * Ranger ilkelerini oluşturma
> * Kafka kümesinde konu oluşturma
> * Ranger ilkelerini test etme

## <a name="prerequisite"></a>Önkoşul

[Kurumsal güvenlik paketi olan bir HDInsight Kafka kümesi](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger Yönetici Arabirimine bağlanma

1. Bir tarayıcıdan, `https://ClusterName.azurehdinsight.net/Ranger/` URL’sini kullanarak Ranger Yönetici kullanıcı arabirimine bağlanın. `ClusterName` öğesini Kafka kümenizin adıyla değiştirmeyi unutmayın. Ranger kimlik bilgileri Hadoop kümesi kimlik bilgileriyle aynı değildir. Tarayıcıların ön belleğe alınmış Hadoop kimlik bilgilerini kullanmasını önlemek için Ranger Yönetici Arabirimine yeni bir InPrivate tarayıcı penceresinden bağlanın.

2. Azure Active Directory (AD) yönetici kimlik bilgilerinizi kullanarak oturum açın. Azure AD yönetici kimlik bilgileri HDInsight küme kimlik bilgileri veya Linux HDInsight düğümü SSH kimlik bilgileriyle aynı değildir.

   ![HDInsight Apache Ranger yönetici kullanıcı arabirimi](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Etki alanı kullanıcılarını oluşturma

**sales_user** ve **marketing_user** etki alanı kullanıcılarını nasıl oluşturacağınızı öğrenmek için [Kurumsal Güvenlik Paketi ile HDInsight kümesi oluşturma](./apache-domain-joined-configure-using-azure-adds.md) bölümünü ziyaret edin. Bir üretim senaryosunda, etki alanı kullanıcıları Active Directory kiracınızdan gelir.

## <a name="create-ranger-policy"></a>Ranger ilkesini oluşturma

**sales_user** ve **marketing_user** için Ranger ilkesi oluşturun.

1. **Ranger Yönetici Arabirimini** açın.

2. **Kafka**altında **\<clustername > _kafka** öğesini seçin. Bir önceden yapılandırılmış ilke listelenebilir.

3. **Yeni Ilke Ekle** ' yi seçin ve aşağıdaki değerleri girin:

   |Ayar  |Önerilen değer  |
   |---------|---------|
   |İlke Adı  |  hdi satış* ilkesi   |
   |Konu   |  satış* |
   |Kullanıcı Seçin  |  sales_user1 |
   |İzinler  | yayımlama, kullanma, oluşturma |

   Konu adında şu joker karakterler bulunabilir:

   * ’*’ karakterlerin sıfır veya daha fazla kez geçtiğini gösterir.
   * ’?‘ tek karakteri gösterir.

   ![Apache Ranger yönetici kullanıcı arabirimi Policy1 oluşturma](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   **Select User** için bir etki alanı kullanıcısı otomatik olarak doldurulmazsa, Ranger’ın Azure AD ile eşitlenmesi için birkaç dakika bekleyin.

4. İlkeyi kaydetmek için **Ekle** ' yi seçin.

5. **Yeni Ilke Ekle** ' yi seçin ve ardından aşağıdaki değerleri girin:

   |Ayar  |Önerilen değer  |
   |---------|---------|
   |İlke Adı  |  hdi pazarlama ilkesi   |
   |Konu   |  marketingspend |
   |Kullanıcı Seçin  |  marketing_user1 |
   |İzinler  | yayımlama, kullanma, oluşturma |

   ![Apache Ranger yönetici kullanıcı arabirimi Policy2 oluşturma](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. İlkeyi kaydetmek için **Ekle** ' yi seçin.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>ESP ile Kafka kümesinde konu oluşturma

`salesevents` ve `marketingspend`iki konu oluşturmak için:

1. Kümeye SSH bağlantısı açmak için aşağıdaki komutu kullanın:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   `DOMAINADMIN`, küme [oluşturma](./apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)sırasında yapılandırılmış kümenizin Yönetici kullanıcısı ile değiştirin ve `CLUSTERNAME` değerini kümenizin adıyla değiştirin. İstenirse, yönetici kullanıcı hesabının parolasını girin. HDInsight ile `SSH` kullanma hakkında daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Küme adını bir değişkene kaydedip JSON ayrıştırma yardımcı programını (`jq`) yüklemek için aşağıdaki komutları kullanın. İstendiğinde, Kafka kümesi adını girin.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Kafka aracı konaklarına ulaşmak için aşağıdaki komutları kullanın. İstendiğinde, küme yöneticisi hesabı için parolayı girin.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Devam etmeden önce, henüz yapmadıysanız geliştirme ortamınızı ayarlamanız gerekebilir. Java JDK, Apache Maven ve SCP ile SSH istemcisi gibi bileşenlere ihtiyacınız olacaktır. Daha fazla bilgi için bkz. [Kurulum yönergeleri](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. [Apache Kafka etki alanına katılmış üretici tüketici örneklerini](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer) indirin.

1. [Öğretici: Apache Kafka Üretici ve Tüketici API’lerini kullanma](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) sayfasının **Örneği derleme ve dağıtma** bölümündeki 2 ve 3 numaralı adımları izleyin.

1. Aşağıdaki komutları çalıştırın:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Ranger ilkelerini test etme

**Sales_user** , yapılandırılan Ranger ilkelerine bağlı olarak, konu `salesevents` oluşturabilir/kullanabilir, ancak konu `marketingspend`. Tersine, **marketing_user** konu `marketingspend` oluşturabilir/kullanabilir, ancak konu `salesevents`.

1. Kümeye yeni bir SSH bağlantısı açın. **sales_user1** olarak oturum açmak için aşağıdaki komutu kullanın:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Aşağıdaki komutu yürütün:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Aşağıdaki ortam değişkenini ayarlamak için önceki bölümdeki aracı adlarını kullanın:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Örnek: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Derleme bölümünde 3. adımı izleyin **ve örneği** öğreticide dağıtın: `kafka-producer-consumer.jar` **sales_user**için kullanılabilir olduğundan emin olmak Için [Apache Kafka Producer ve Consumer API 'lerini kullanın](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) .

5. Aşağıdaki komutu yürüterek **sales_user1** 'in konu `salesevents` üretebildiğini doğrulayın:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. `salesevents`konu başlığı altında kullanmak için aşağıdaki komutu yürütün:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   İletileri okuyabildiğinizi doğrulayın.

7. **Sales_user1** 'in aynı SSH penceresinde aşağıdakileri yürüterek konu `marketingspend` üretemiyor olduğunu doğrulayın:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Bir yetkilendirme hatası oluşur ve bu yok sayılabilir.

8. **Marketing_user1** konusunun `salesevents`tüketediğine dikkat edin.

   Yukarıdaki 1-4, bu kez **marketing_user1**olarak adımları yineleyin.

   `salesevents`konu başlığı altında kullanmak için aşağıdaki komutu yürütün:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Önceki iletiler görülemez.

9. Ranger kullanıcı arabiriminden denetim erişimi olaylarını görüntüleyin.

   ![Ranger UI ilkesi denetim erişim olayları ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımlarla oluşturduğunuz Kafka kümesini silin:

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.
1. Üstteki **arama** kutusuna **HDInsight**yazın.
1. **Hizmetler**altında **HDInsight kümeleri** ' ni seçin.
1. Görüntülenen HDInsight kümeleri listesinde, bu öğretici için oluşturduğunuz kümenin yanındaki **...** öğesine tıklayın. 
1. **Sil**'e tıklayın. **Evet**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Apache Kafka için kendi anahtarınızı getirin](../kafka/apache-kafka-byok.md)