---
title: Apache Kafka SSL şifreleme & kimlik doğrulama-Azure HDInsight
description: Kafka istemcileri ile Kafka aracıları ve Kafka aracıları arasında iletişim için SSL şifrelemesi ayarlayın. İstemcilerin SSL kimlik doğrulamasını ayarlayın.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5dd698b28a01ed251492cf34e9da2dda4d0c2580
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241988"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Kafka için Güvenli Yuva Katmanı (SSL) şifrelemesini ve kimlik doğrulamasını ayarlama

Bu makalede, Apache Kafka istemcileri ve Apache Kafka aracıları arasında SSL şifrelemesini ayarlama konusu gösterilmektedir. Ayrıca, istemcilerin kimlik doğrulamasının nasıl ayarlanacağını gösterir (bazen iki yönlü SSL olarak adlandırılır).

> [!Important]
> Kafka uygulamaları için kullanabileceğiniz iki istemci vardır: bir Java istemcisi ve bir konsol istemcisi. Yalnızca `ProducerConsumer.java` Java istemcisi hem üretme hem de kullanma için SSL kullanabilir. Konsol üreticisi istemci `console-producer.sh` SSL ile çalışmıyor.

## <a name="apache-kafka-broker-setup"></a>Apache Kafka Aracısı kurulumu

Kafka SSL Aracısı kurulumu aşağıdaki şekilde dört HDInsight kümesi VM kullanır:

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
> Aşağıdaki kod parçacıkları wnX, üç çalışan düğümünden birine yönelik bir kısaltmadır ve uygun şekilde `wn0`, `wn1` veya `wn2` ile değiştirilmelidir. `WorkerNode0_Name` ve `HeadNode0_Name`, `wn0-abcxyz` veya `hn0-abcxyz`gibi ilgili makinelerin adlarıyla değiştirilmelidir.

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

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>SSL kullanmak için Kafka yapılandırmasını güncelleştirin ve aracıları yeniden başlatın

Artık her bir Kafka Aracısı 'nı bir anahtar deposu ve truststore ile ayarlamış ve doğru sertifikaları aktardınız. Sonra, ambarı kullanarak ilgili Kafka yapılandırma özelliklerini değiştirin ve ardından Kafka aracılarını yeniden başlatın.

Yapılandırma değişikliğini gerçekleştirmek için aşağıdaki adımları uygulayın:

1. Azure portal oturum açın ve Azure HDInsight Apache Kafka kümenizi seçin.
1. **Küme panoları**altında, **ambarı giriş** ' e tıklayarak ambarı Kullanıcı arabirimine gidin.
1. **Kafka Aracısı** altında **dinleyicileri** özelliğini `PLAINTEXT://localhost:9092,SSL://localhost:9093` olarak ayarlayın
1. **Advanced Kafka-Broker** altında **Security. Inter. Broker. Protocol** özelliğini `SSL` olarak ayarlayın

    ![Kafka SSL yapılandırma özelliklerini ambarı 'nda Düzenle](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. **Özel Kafka-Broker** altında **SSL. Client. auth** özelliğini `required` olarak ayarlayın. Bu adım yalnızca kimlik doğrulaması ve şifrelemeyi ayarlıyorsanız gereklidir.

    ![Kafka SSL yapılandırma özelliklerini ambarı 'nda Düzenle](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. **Gelişmiş Kafka-env** altında, **Kafka-env Template** özelliğinin sonuna aşağıdaki satırları ekleyin.

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![Kafka-env Template özelliğini, ambarı 'nda Düzenle](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Tüm Kafka aracılarını yeniden başlatın.
1. Üreticileri ve tüketicilerin 9093 numaralı bağlantı noktasında çalıştığını doğrulamak için yönetici istemcisini üretici ve tüketici seçenekleriyle başlatın.

## <a name="client-setup-with-authentication"></a>İstemci kurulumu (kimlik doğrulamasıyla)

> [!Note]
> Aşağıdaki adımlar yalnızca SSL şifrelemesini **ve** kimlik doğrulamasını ayarlıyorsanız gereklidir. Yalnızca şifrelemeyi ayarlıyorsanız, lütfen [kimlik doğrulaması olmadan istemci kurulumuna](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication) ilerleyin

İstemci kurulumunu gerçekleştirmek için aşağıdaki adımları izleyin:

1. İstemci makinesinde (hn1) oturum açın.
1. Bir Java anahtar deposu oluşturun ve aracı için imzalı bir sertifika alın. Ardından sertifikayı CA 'nın çalıştığı VM 'ye kopyalayın.
1. İstemci sertifikasını imzalamak için CA makinesine (hn0) geçiş yapın.
1. İstemci makinesine (hn1) gidin ve `~/ssl` klasörüne gidin. İmzalı sertifikayı istemci makinesine kopyalayın.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Son olarak, `client-ssl-auth.properties` dosyasını `cat client-ssl-auth.properties` komutuyla görüntüleyin. Aşağıdaki satırlara sahip olmalıdır:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>İstemci kurulumu (kimlik doğrulaması olmadan)

Kimlik doğrulamasına ihtiyacınız yoksa yalnızca SSL şifrelemesini ayarlama adımları şunlardır:

1. İstemci makinesinde (hn1) oturum açın ve `~/ssl` klasörüne gidin
1. İmzalı sertifikayı, CA makinesinden (wn0) istemci makinesine kopyalayın.
1. CA sertifikasını truststore 'a aktarma
1. CA sertifikasını anahtar deposu 'a aktarma

Bu adımlar aşağıdaki kod parçacığında gösterilmiştir.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Son olarak, `client-ssl-auth.properties` dosyasını `cat client-ssl-auth.properties` komutuyla görüntüleyin. Aşağıdaki satırlara sahip olmalıdır:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache Kafka nedir?](apache-kafka-introduction.md)
