---
title: Apache Kafka TLS şifreleme & kimlik doğrulama-Azure HDInsight
description: Kafka istemcileri ile Kafka aracıları arasındaki iletişim için TLS şifrelemesini ve Kafka aracıları arasında iletişim kurmak için ayarlayın. İstemcilerin SSL kimlik doğrulamasını ayarlayın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: d061832022b983e4d5fd55e72c1d4789b82f6633
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863233"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Kafka için TLS şifrelemesini ve kimlik doğrulamasını ayarlama

Bu makalede, daha önce Güvenli Yuva Katmanı (SSL) şifrelemesi olarak bilinen ve Apache Kafka istemcileri ile Apache Kafka aracıları arasında Aktarım Katmanı Güvenliği (TLS) şifrelemesini ayarlama konusu gösterilmektedir. Ayrıca, istemcilerin kimlik doğrulamasının nasıl ayarlanacağını gösterir (bazen iki yönlü TLS olarak adlandırılır).

> [!Important]
> Kafka uygulamaları için kullanabileceğiniz iki istemci vardır: bir Java istemcisi ve bir konsol istemcisi. Yalnızca Java istemcisi `ProducerConsumer.java` hem üretim hem de kullanma IÇIN TLS kullanabilir. Konsol üreticisi istemcisi `console-producer.sh` TLS ile çalışmıyor.

> [!Note]
> HDInsight Kafka konsol üreticisi sürüm 1,1 ile SSL 'yi desteklemez.

## <a name="apache-kafka-broker-setup"></a>Apache Kafka Aracısı kurulumu

Kafka TLS aracı kurulumu aşağıdaki şekilde dört HDInsight kümesi VM kullanır:

* baş düğümüne 0-sertifika yetkilisi (CA)
* çalışan düğümü 0, 1 ve 2-aracılar

> [!Note] 
> Bu kılavuz otomatik olarak imzalanan sertifikalar kullanacaktır, ancak en güvenli çözüm, güvenilen CA 'Lar tarafından verilen sertifikaları kullanmaktır.

Aracı Kurulum işleminin Özeti aşağıdaki gibidir:

1. Aşağıdaki adımlar üç çalışan düğümünün her birinde yinelenir:

    1. Bir sertifika oluşturun.
    1. Bir sertifika imzalama isteği oluşturun.
    1. Sertifika imzalama isteğini sertifika yetkilisine (CA) gönderin.
    1. CA 'da oturum açın ve isteği imzalayın.
    1. SCP imzalı sertifikayı çalışan düğümüne geri dönün.
    1. SCP CA 'nın ortak sertifikasını çalışan düğümüne.

1. Tüm sertifikalara sahip olduktan sonra sertifikaları sertifika deposuna koyun.
1. Ambarı 'na gidin ve konfigürasyonları değiştirin.

Aracı kurulumunu gerçekleştirmek için aşağıdaki ayrıntılı yönergeleri kullanın:

> [!Important]
> Aşağıdaki kod parçacıklarında wnX, üç çalışan düğümünden birine yönelik bir kısaltmadır ve yerine `wn0` `wn1` veya `wn2` uygun şekilde olmalıdır. `WorkerNode0_Name` ve `HeadNode0_Name` ilgili makinelerin adlarıyla yerine gelmelidir.

1. HDInsight için, sertifika yetkilisinin (CA) rolünü dolduracağı baş düğüm 0 ' da ilk kurulumu gerçekleştirin.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Aracıların her birinde aynı ilk kurulumu gerçekleştirin (çalışan düğümleri 0, 1 ve 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Çalışan düğümlerinin her birinde aşağıdaki kod parçacığını kullanarak aşağıdaki adımları yürütün.
    1. Bir anahtar deposu oluşturun ve bunu yeni bir özel sertifikayla doldurun.
    1. Sertifika imzalama isteği oluşturun.
    1. SCP CA 'ya sertifika imzalama isteği (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. CA makinesinde, CA-sertifika ve CA-anahtar dosyaları oluşturmak için aşağıdaki komutu çalıştırın:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. CA makinesine geçin ve alınan tüm sertifika imzalama isteklerini imzalayın:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. İmzalı sertifikaları CA 'dan çalışan düğümlerine geri gönderin (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. CA 'nın genel sertifikasını her çalışan düğümüne gönderin.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Her çalışan düğümünde, CAs ortak sertifikasını truststore ve keystore 'a ekleyin. Ardından çalışan düğümünün kendi imzalı sertifikasını anahtar deposu 'a ekleyin

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Kafka yapılandırmasını TLS kullanacak şekilde güncelleştirin ve aracıları yeniden başlatın

Artık her bir Kafka Aracısı 'nı bir anahtar deposu ve truststore ile ayarlamış ve doğru sertifikaları aktardınız. Sonra, ambarı kullanarak ilgili Kafka yapılandırma özelliklerini değiştirin ve ardından Kafka aracılarını yeniden başlatın.

Yapılandırma değişikliğini gerçekleştirmek için aşağıdaki adımları uygulayın:

1. Azure portal oturum açın ve Azure HDInsight Apache Kafka kümenizi seçin.
1. **Küme panoları** altında, **ambarı giriş** ' e tıklayarak ambarı Kullanıcı arabirimine gidin.
1. **Kafka Aracısı** altında, **Listeners** özelliğini`PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. **Gelişmiş Kafka-aracı** altında **Security. Inter. Broker. Protocol** özelliğini olarak ayarlayın`SSL`

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png" alt-text="Kafka SSL yapılandırma özelliklerini ambarı 'nda Düzenle" border="true":::

1. **Özel Kafka-Broker** altında **SSL. Client. auth** özelliğini olarak ayarlayın `required` . Bu adım yalnızca kimlik doğrulaması ve şifrelemeyi ayarlıyorsanız gereklidir.

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png" alt-text="Kafka SSL yapılandırma özelliklerini ambarı 'nda Düzenle" border="true":::

1. HDI sürüm 3,6 için, ambarı Kullanıcı arabirimine gidin ve **Gelişmiş Kafka-env** ve **Kafka-env Template** özelliği altına aşağıdaki konfigürasyonları ekleyin.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Bu değişikliklerle birlikte, ambarı yapılandırma kullanıcı arabirimini gösteren ekran görüntüsü aşağıda verilmiştir.

    HDI sürüm 3,6 için:

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png" alt-text="Kafka-env Template özelliğini, ambarı 'nda Düzenle" border="true":::

    HDI sürüm 4,0 için:

     :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png" alt-text="Kafka-env Template özelliği, ambarı dört içinde düzenleniyor" border="true":::

1. Tüm Kafka aracılarını yeniden başlatın.

## <a name="client-setup-without-authentication"></a>İstemci kurulumu (kimlik doğrulaması olmadan)

Kimlik doğrulamasına gerek duymuyorsanız, yalnızca TLS şifrelemesini ayarlama adımlarının Özeti şunlardır:

1. CA 'da (etkin baş düğüm) oturum açın.
1. CA sertifikasını, CA makinesinden istemci makinesine (wn0) kopyalayın.
1. İstemci makinesinde (hn1) oturum açın ve `~/ssl` klasöre gidin.
1. CA sertifikasını truststore 'a aktarın.
1. CA sertifikasını keystore 'a aktarın.

Bu adımlar aşağıdaki kod parçacıkları içinde ayrıntılıdır.

1. CA düğümünde oturum açın.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. CA-CERT ' i istemci makinesine kopyalama

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. İstemci makinesinde (bekleyen baş düğüm) oturum açın.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. CA sertifikasını truststore 'a aktarın.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. CA sertifikasını keystore 'a aktarın.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Dosyayı `client-ssl-auth.properties` istemci makinesinde oluşturun (hn1). Aşağıdaki satırlara sahip olmalıdır:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Üreticileri ve tüketicilerin 9093 numaralı bağlantı noktasında çalıştığını doğrulamak için yönetici istemcisini üretici ve tüketici seçenekleriyle başlatın. Kurulumu, konsol üreticisi/tüketicisi kullanarak doğrulamak için gereken adımlar için lütfen aşağıdaki [doğrulama](apache-kafka-ssl-encryption-authentication.md#verification) bölümüne bakın.

## <a name="client-setup-with-authentication"></a>İstemci kurulumu (kimlik doğrulamasıyla)

> [!Note]
> Aşağıdaki adımlar yalnızca TLS şifrelemesini **ve** kimlik doğrulamasını ayarlıyorsanız gereklidir. Yalnızca şifrelemeyi ayarlıyorsanız, [kimlik doğrulaması olmadan istemci kurulumu](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)' na bakın.

Aşağıdaki dört adım, istemci kurulumunu tamamlaması için gereken görevleri özetler:

1. İstemci makinesinde (bekleyen baş düğüm) oturum açın.
1. Bir Java anahtar deposu oluşturun ve aracı için imzalı bir sertifika alın. Ardından sertifikayı CA 'nın çalıştığı VM 'ye kopyalayın.
1. İstemci sertifikasını imzalamak için CA makinesine (etkin baş düğüm) geçin.
1. İstemci makinesine (bekleyen baş düğüm) gidin ve `~/ssl` klasöre gidin. İmzalı sertifikayı istemci makinesine kopyalayın.

Her adımın ayrıntıları aşağıda verilmiştir.

1. İstemci makinesinde (bekleyen baş düğüm) oturum açın.

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Var olan tüm SSL dizinini kaldırın.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Bir Java anahtar deposu oluşturun ve bir sertifika imzalama isteği oluşturun. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Sertifika imzalama isteğini CA 'ya kopyalama

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. CA makinesine (etkin baş düğüm) geçin ve istemci sertifikasını imzalayın.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. İmzalı istemci sertifikasını CA 'dan (etkin baş düğüm) istemci makinesine kopyalayın.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. CA-CERT ' i istemci makinesine kopyalama

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. İstemci makinesinde (bekleyen baş düğüm) oturum açın ve SSL dizinine gidin.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. İmzalı CERT ile istemci deposu oluşturun ve istemci makinesindeki anahtar deposu ve truststore 'a CA sertifikası içeri aktarın (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. İstemci makinesinde bir dosya oluşturun `client-ssl-auth.properties` (hn1). Aşağıdaki satırlara sahip olmalıdır:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Doğrulama

Bu adımları istemci makinesinde çalıştırın.

> [!Note]
> HDInsight 4,0 ve Kafka 2,1 yüklüyse, kurulumunuzu doğrulamak için konsol üreticisi/tüketicilerini kullanabilirsiniz. Aksi takdirde, Kafka üreticisi bağlantı noktası 9092 üzerinde çalıştırın ve konuya ileti gönderin ve ardından, TLS kullanan 9093 numaralı bağlantı noktasında Kafka tüketicisini kullanın.

### <a name="kafka-21-or-above"></a>Kafka 2,1 veya üzeri

1. Zaten mevcut değilse bir konu oluşturun.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Konsol üreticisi ' ni başlatın ve `client-ssl-auth.properties` üretici için bir yapılandırma dosyası olarak yolunu sağlayın.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. İstemci makinesine başka bir SSH bağlantısı açın ve konsol tüketicisini başlatın ve `client-ssl-auth.properties` tüketicinin bir yapılandırma dosyası olarak yolunu sağlayın.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Zaten mevcut değilse bir konu oluşturun.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Konsol üreticisi ' ni başlatın ve istemci-SSL-Auth. Properties yolunu üretici için bir yapılandırma dosyası olarak sağlayın.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. İstemci makinesine başka bir SSH bağlantısı açın ve konsol tüketicisini başlatın ve `client-ssl-auth.properties` tüketicinin bir yapılandırma dosyası olarak yolunu sağlayın.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde Apache Kafka nedir?](apache-kafka-introduction.md)
