---
title: 'Hızlı başlangıç: Apache HBase & Apache Phoenix-Azure HDInsight'
description: Bu hızlı başlangıçta, HDInsight 'ta Apache Phoenix kullanmayı öğreneceksiniz. Ayrıca, HDInsight 'ta bir HBase kümesine bağlanmak için bilgisayarınıza SQLLine yüklemeyi ve ayarlamayı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: a6896eaad86c5e361c50cd81257131ee75a8d6a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467046"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Hızlı başlangıç: Apache Phoenix ile Azure HDInsight 'ta Apache HBase 'i sorgulama

Bu hızlı başlangıçta, Azure HDInsight 'ta HBase sorgularını çalıştırmak için Apache Phoenix nasıl kullanacağınızı öğreneceksiniz. Apache Phoenix, Apache HBase için bir SQL sorgu altyapısıdır. Buna JDBC sürücüsü olarak erişilir ve bu SQL kullanarak HBase tablolarını sorgulamayı ve yönetmeyi sağlar. [Sqlline](http://sqlline.sourceforge.net/) , SQL yürütmek için bir komut satırı yardımcı programıdır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Apache HBase kümesi. HDInsight kümesi oluşturmak için bkz. [küme oluşturma](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) .  **HBase** küme türünü seçtiğinizden emin olun.

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>ZooKeeper düğümünü tanımla

Bir HBase kümesine bağlandığınızda Apache ZooKeeper düğümlerinden birine bağlanmanız gerekir. Her HDInsight kümesinde üç ZooKeeper düğümü vardır. Kıvrımlı, bir ZooKeeper düğümünü hızlı bir şekilde tanımlamak için kullanılabilir. `PASSWORD` ve `CLUSTERNAME` ilgili değerlerle değiştirerek aşağıdaki kıvrımlı komutunu düzenleyin ve komut istemine komutu girin:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Çıktının bir kısmı şuna benzer olacaktır:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

`host_name` değerini daha sonra kullanmak üzere bir yere göz atın.

## <a name="create-a-table-and-manipulate-data"></a>Tablo oluşturma ve verileri işleme

HBase kümelerine bağlanmak için SSH kullanabilir ve sonra HBase tabloları oluşturmak, veri eklemek ve verileri sorgulamak için Apache Phoenix kullanabilirsiniz.

1. HBase kümenize bağlanmak için `ssh` komutunu kullanın. `CLUSTERNAME` kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Dizini Phoenix istemcisine değiştirin. Aşağıdaki komutu kullanın:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. [Sqlline](http://sqlline.sourceforge.net/)'ı başlatın. Daha önce tanımlanan ZooKeeper düğümle `ZOOKEEPER` değiştirerek aşağıdaki komutu düzenleyin, sonra şu komutu girin:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Bir HBase tablosu oluşturun. Aşağıdaki komutu kullanın:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. HBase 'deki tüm tabloları listelemek için SQLLine `!tables` komutunu kullanın. Aşağıdaki komutu kullanın:

    ```sqlline
    !tables
    ```

6. Tablodaki değerleri ekleyin. Aşağıdaki komutu kullanın:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Tabloyu sorgulayın. Aşağıdaki komutu kullanın:

    ```sql
    SELECT * FROM Company;
    ```

8. Bir kaydı silin. Aşağıdaki komutu kullanın:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Tabloyu bırakın. Aşağıdaki komutu kullanın:

    ```hbase
    DROP TABLE Company;
    ```

10. SQLLine 'dan çıkmak için SQLLine `!quit` komutunu kullanın. Aşağıdaki komutu kullanın:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure HDInsight 'ta HBase sorgularını çalıştırmak için Apache Phoenix nasıl kullanacağınızı öğrendiniz. Apache Phoenix hakkında daha fazla bilgi edinmek için sonraki makalede daha derin bir inceleme sunulacaktır.

> [!div class="nextstepaction"]
> [HDInsight 'ta Apache Phoenix](../hdinsight-phoenix-in-hdinsight.md)
