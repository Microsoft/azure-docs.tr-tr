---
title: Apache Beeline bağlanma veya yüklemesi-Azure HDInsight
description: HDInsight 'ta Hadoop ile Hive sorguları çalıştırmak için Apache Beeline istemcisine nasıl bağlanacağınızı öğrenin. Beeline, JDBC üzerinden HiveServer2 ile çalışmaya yönelik bir yardımcı programdır.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 04/07/2021
ms.openlocfilehash: 5dcb6168a263be11410126ff08bd8b015da5af46
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103445"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>HDInsight 'ta Apache Beeline bağlanma veya yerel olarak yüklemeyi

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) , HDInsight kümenizin baş düğümlerine eklenen bir Hive istemcanıdır. Bu makalede, farklı türlerde bağlantılar arasında HDInsight kümenize yüklenen Beeline istemcisine nasıl bağlanabileceğinizi açıklamaktadır. Ayrıca [, Beeline istemcisinin yerel olarak nasıl yükleneceğini](#install-beeline-client)de açıklar. 

## <a name="types-of-connections"></a>Bağlantı türleri

### <a name="from-an-ssh-session"></a>Bir SSH oturumundan

Bir SSH oturumundan bir küme headnode 'a bağlanırken, `headnodehost` bağlantı noktasındaki adrese bağlanabilirsiniz `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Bir Azure sanal ağı üzerinden

Bir istemciden HDInsight 'a bir Azure sanal ağı üzerinden bağlanılırken, bir küme baş düğümünün tam etki alanı adını (FQDN) sağlamanız gerekir. Bu bağlantı doğrudan küme düğümlerine yapıldığından, bağlantı şu bağlantı noktasını kullanır `10001` :

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

`<headnode-FQDN>`Bir küme headnode 'un tam etki alanı adıyla değiştirin. Bir headnode 'un tam etki alanı adını bulmak için [Apache ambarı REST API belgesini kullanarak HDInsight 'ı yönetme](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) bölümündeki bilgileri kullanın.

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Kerberos kullanarak HDInsight Kurumsal Güvenlik Paketi (ESP) kümesine

Bir istemciden, kümenin aynı bölgesindeki bir makinede Azure Active Directory (AAD)-DS 'ye katılmış bir Kurumsal Güvenlik Paketi (ESP) kümesine bağlandığında, `<AAD-Domain>` kümeye erişim izinleri olan etki alanı kullanıcı hesabının etki alanı adını ve adını da belirtmeniz gerekir `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

`<username>`Kümeye erişim izinleri olan etki alanındaki hesabın adıyla değiştirin. `<AAD-DOMAIN>`Kümenin katıldığı Azure Active Directory (AAD) adıyla değiştirin. Değer için bir büyük dize kullanın `<AAD-DOMAIN>` , aksi takdirde kimlik bilgisi bulunamadı. `/etc/krb5.conf`Gerekirse bölge adlarını kontrol edin.

Ambarı 'ndan JDBC URL 'sini bulmak için:

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` , `CLUSTERNAME` Kümenizin adı olan ' a gidin. HiveServer2 'ın çalıştığından emin olun.

1. HiveServer2 JDBC URL 'sini kopyalamak için Pano 'yu kullanın.

### <a name="over-public-or-private-endpoints"></a>Herkese açık veya özel uç noktalar

Ortak veya özel uç noktaları kullanarak bir kümeye bağlanırken, küme oturum açma hesabı adını (varsayılan `admin` ) ve parolayı sağlamanız gerekir. Örneğin, adrese bağlanmak için bir istemci sisteminden Beeline kullanma `clustername.azurehdinsight.net` . Bu bağlantı bağlantı noktası üzerinden yapılır `443` ve TLS/SSL kullanılarak şifrelenir.

`clustername` değerini HDInsight kümenizin adıyla değiştirin. `admin`Kümenizin küme oturum açma hesabı ile değiştirin. ESP kümeleri için tam UPN 'yi kullanın (örneğin, user@domain.com ). `password`Küme oturum açma hesabının parolasıyla değiştirin.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

veya özel uç nokta için:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Özel uç noktalar, yalnızca aynı bölgedeki VNET 'lerden erişilebilen temel bir yük dengeleyiciye işaret ediyor. Daha fazla bilgi için bkz. [genel VNET eşlemesi ve yük dengeleyiciler üzerindeki kısıtlamalar](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . `curl` `-v` Beeline kullanmadan önce ortak veya özel uç noktalarla bağlantı sorunlarını gidermek için komutunu kullanın.

### <a name="use-beeline-with-apache-spark"></a>Apache Spark ile Beeline kullanma

Apache Spark, bazı durumlarda Spark Thrift sunucusu olarak da adlandırılan kendi HiveServer2 uygulamasını sağlar. Bu hizmet, Hive yerine sorguları çözümlemek için Spark SQL 'i kullanır. Ve sorgunuza göre daha iyi performans sağlayabilir.

#### <a name="through-public-or-private-endpoints"></a>Ortak veya özel uç noktalar aracılığıyla

Kullanılan bağlantı dizesi biraz farklı. Yerine `httpPath=/hive2` kullanır `httpPath/sparkhive2` . `clustername` değerini HDInsight kümenizin adıyla değiştirin. `admin`Kümenizin küme oturum açma hesabı ile değiştirin. `password`Küme oturum açma hesabının parolasıyla değiştirin.
> [!NOTE]
> ESP kümeleri için, `admin` tam UPN ile değiştirin (örneğin, user@domain.com ). 

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

veya özel uç nokta için:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Özel uç noktalar, yalnızca aynı bölgedeki VNET 'lerden erişilebilen temel bir yük dengeleyiciye işaret ediyor. Daha fazla bilgi için bkz. [genel VNET eşlemesi ve yük dengeleyiciler üzerindeki kısıtlamalar](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . `curl` `-v` Beeline kullanmadan önce ortak veya özel uç noktalarla bağlantı sorunlarını gidermek için komutunu kullanın.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Apache Spark ile küme kafası veya Azure sanal ağı içinden

Doğrudan küme baş düğümünden veya HDInsight kümesiyle aynı Azure sanal ağı içindeki bir kaynaktan bağlantı kurulurken, `10002` yerine Spark Thrift sunucusu için kullanılmalıdır `10001` . Aşağıdaki örnek, baş düğüme doğrudan nasıl bağlanılacağını gösterir:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Beeline istemcisini yükler

Baş düğümlere Beeline dahil edilse de, yerel olarak yüklemek isteyebilirsiniz.  Yerel bir makineye yönelik Install adımları [Linux Için Windows alt sistemine](/windows/wsl/install-win10)dayanır.

1. Paket listelerini güncelleştirme. Bash kabuğunuzun aşağıdaki komutunu girin:

    ```bash
    sudo apt-get update
    ```

1. Yüklü değilse Java 'Yı yükler. Komutuyla kontrol edebilirsiniz `which java` .

    1. Java paketi yüklü değilse, aşağıdaki komutu girin:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Bashrc dosyasını açın (genellikle ~/,bashrc içinde bulunur): `nano ~/.bashrc` .

    1. Bashrc dosyasını düzeltme. Dosyanın sonuna aşağıdaki satırı ekleyin:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Sonra **CTRL + X** ve **Y** tuşlarına basın ve ardından girin.

1. Hadoop ve Beeline arşivlerini indirin, aşağıdaki komutları girin:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Arşivlerin paketini açın, aşağıdaki komutları girin:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Bashrc dosyasını daha fazla düzeltme. Arşivlerin açıldığı yolu belirlemeniz gerekir. [Linux Için Windows alt sistemi](/windows/wsl/install-win10)kullanıyorsanız ve adımları tam olarak takip ediyorsanız, yolunuzda `/mnt/c/Users/user/` `user` Kullanıcı adınız olur.

    1. Dosyayı açın: `nano ~/.bashrc`

    1. Aşağıdaki komutları uygun yol ile değiştirin ve ardından bashrc dosyasının sonuna girin:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Sonra **CTRL + X** ve **Y** tuşlarına basın ve ardından girin.

1. Bash oturumunuzu kapatıp yeniden açın.

1. Bağlantınızı test edin. Yukarıdaki [ortak veya özel uç noktalardan](#over-public-or-private-endpoints)bağlantı biçimini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Apache Hive Beeline istemcisini kullanan örnekler için bkz. [Apache Hive Ile Apache Beeline kullanma](apache-hadoop-use-hive-beeline.md)
* HDInsight 'ta Hive hakkında daha fazla genel bilgi için bkz. [HDInsight 'ta Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)
