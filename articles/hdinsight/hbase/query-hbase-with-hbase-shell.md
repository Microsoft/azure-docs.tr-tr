---
title: "Hızlı başlangıç: Azure HDInsight 'ta Apache HBase 'i sorgulama-HBase kabuğu"
description: Bu hızlı başlangıçta Apache HBase kabuğunu kullanarak Apache HBase sorgularını nasıl çalıştıracağınızı öğreneceksiniz.
keywords: HDInsight, Hadoop, HBase
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 98844a7dab673fb98dcdb639fbc48c0f6035fbba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865120"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Hızlı başlangıç: Azure HDInsight 'ta HBase kabuğu ile Apache HBase 'i sorgulama

Bu hızlı başlangıçta, bir HBase tablosu oluşturmak, veri eklemek ve ardından tabloyu sorgulamak için Apache HBase kabuğu 'nu nasıl kullanacağınızı öğreneceksiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Apache HBase kümesi. HDInsight kümesi oluşturmak için bkz. [küme oluşturma](../hadoop/apache-hadoop-linux-tutorial-get-started.md) .  **HBase** küme türünü seçtiğinizden emin olun.

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Tablo oluşturma ve verileri işleme

Çoğu kişi için veriler tablo biçiminde görünür:

:::image type="content" source="./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png" alt-text="HDInsight Apache HBase tablo verileri" border="true":::

HBase 'de ( [Cloud BigTable](https://cloud.google.com/bigtable/)'ın bir uygulamasında), aynı veriler şöyle görünür:

:::image type="content" source="./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png" alt-text="HDInsight Apache HBase BigTable verileri" border="true":::

HBase kümelerine bağlanmak için SSH kullanabilirsiniz ve ardından, HBase tabloları oluşturmak, veri eklemek ve verileri sorgulamak için Apache HBase kabuğu ' nu kullanabilirsiniz.

1. `ssh`HBase kümenize bağlanmak için komutunu kullanın. Aşağıdaki komutu, `CLUSTERNAME` kümenizin adıyla değiştirerek düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. `hbase shell`HBase etkileşimli kabuğunu başlatmak için komutunu kullanın. SSH bağlantınıza aşağıdaki komutu girin:

    ```bash
    hbase shell
    ```

3. `create`İki sütunlu ailelerle bir HBase tablosu oluşturmak için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. `list`HBase 'deki tüm tabloları listelemek için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    list
    ```

5. Belirli bir tablodaki belirtilen bir `put` sütundaki değerleri eklemek için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. `scan`Tablo verilerini taramak ve döndürmek için komutunu kullanın `Contacts` . Aşağıdaki komutu girin:

    ```hbase
    scan 'Contacts'
    ```

7. `get`Bir satırın içeriğini getirmek için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    get 'Contacts', '1000'
    ```

    `scan`Yalnızca bir satır olduğundan, komutunu kullanarak benzer sonuçlar görürsünüz.

8. `delete`Tablodaki bir hücre değerini silmek için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. `disable`Tabloyu devre dışı bırakmak için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    disable 'Contacts'
    ```

10. `drop`Bir tabloyu HBase 'den bırakmak için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    drop 'Contacts'
    ```

11. `exit`HBase etkileşimli kabuğunu durdurmak için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    exit
    ```

HBase tablo şeması hakkında daha fazla bilgi için bkz. [Apache HBase şema tasarımına giriş](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). HBase komutları hakkında daha fazla bilgi için bkz. [Apache HBase başvuru kılavuzu](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir HBase tablosu oluşturmak, veri eklemek ve ardından tabloyu sorgulamak için Apache HBase kabuğu 'nu nasıl kullanacağınızı öğrendiniz. HBase 'de depolanan veriler hakkında daha fazla bilgi edinmek için, bir sonraki makalede Apache Spark sorguları yürütme gösterilmektedir.

> [!div class="nextstepaction"]
> [Apache HBase verilerini okuyup yazmak için Apache Spark kullanma](../hdinsight-using-spark-query-hbase.md)